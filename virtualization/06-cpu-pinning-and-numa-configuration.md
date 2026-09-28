# Ghim CPU và Cấu hình NUMA: Hướng dẫn Toàn tập
## Giới thiệu
CPU pinning và cấu hình NUMA (Non-Uniform Memory Access - Truy cập bộ nhớ không đồng nhất) là các kỹ thuật ảo hóa nâng cao giúp cải thiện đáng kể hiệu suất máy ảo thông qua việc tối ưu hóa tính cục bộ của CPU và bộ nhớ. Các kỹ thuật này đóng vai trò thiết yếu đối với các khối lượng công việc đòi hỏi hiệu suất cao, ứng dụng thời gian thực và những môi trường mà sự ổn định cũng như tính dự báo được về hiệu suất là yếu tố then chốt.

Hướng dẫn toàn diện này sẽ đi sâu vào các nội dung: CPU pinning, cấu hình cấu trúc tô-pô (topology) NUMA và các chiến lược tối ưu hóa hiệu suất cho máy ảo KVM/QEMU. Cho dù bạn đang vận hành các ứng dụng nhạy cảm với độ trễ, cơ sở dữ liệu có lưu lượng xử lý lớn hay các tác vụ đòi hỏi năng lực tính toán cao, việc nắm vững các khái niệm này sẽ giúp bạn khai thác tối đa hiệu suất từ cơ sở hạ tầng ảo hóa của mình.

Nếu không được cấu hình CPU và NUMA đúng cách, máy ảo có thể gặp phải tình trạng hiệu suất thiếu ổn định, hiện tượng tranh chấp bộ nhớ đệm (cache thrashing) và các vấn đề về độ trễ khi truy cập bộ nhớ, dẫn đến sụt giảm hiệu suất từ 50% trở lên. Các hệ thống đa socket hiện đại sử dụng kiến trúc NUMA đòi hỏi quy trình cấu hình kỹ lưỡng để tránh tình trạng truy cập bộ nhớ chéo giữa các socket và đảm bảo tính cục bộ tối ưu cho tài nguyên.

Sau khi hoàn thành hướng dẫn này, bạn sẽ làm chủ được các chiến lược CPU pinning, cách cấu hình cấu trúc tô-pô NUMA cũng như các kỹ thuật tinh chỉnh hiệu suất, giúp máy ảo đạt được tốc độ xử lý gần như tương đương với hệ thống vật lý (native performance) ngay cả với những khối lượng công việc nặng nề trong môi trường thực tế.

## Tìm hiểu về Kiến trúc CPU và NUMA
### Các nguyên lý cơ bản về cấu trúc tô-pô CPU (CPU Topology)
Các máy chủ hiện đại sử dụng kiến trúc CPU phân cấp:

```
Server
  └─ Sockets (CPU vật lý)
      └─ Cores (Số nhân vật lý trên mỗi socket)
          └─ Threads (Các CPU logic thông qua Hyper-Threading/SMT)
```

Ví dụ về cấu trúc tô-pô (topology):

* 2 socket (nút NUMA)
* 8 nhân mỗi socket
* 2 luồng mỗi nhân (Hyper-Threading)
* Tổng cộng: 32 CPU logic (luồng)

### Kiểm tra cấu trúc tô-pô CPU của máy chủ
```console
# Xem cấu trúc tô-pô CPU
lscpu

# Kết quả đầu ra bao gồm:
# CPU(s):              32
# Thread(s) per core:  2
# Core(s) per socket:  8
# Socket(s):           2
# NUMA node(s):        2
# NUMA node0 CPU(s):   0-15
# NUMA node1 CPU(s):   16-31

# cấu trúc tô-pô chi tiết
lscpu -e

# CPU NODE SOCKET CORE L1d:L1i:L2:L3
#   0    0      0    0 0:0:0:0
#   1    0      0    0 0:0:0:0
#   2    0      0    1 1:1:1:0
# ...

# Cấu trúc tô-pô trực quan
lstopo
# Hoặc dành cho terminal
lstopo-no-graphics
```

### Tìm hiểu về NUMA
NUMA (Non-Uniform Memory Access) có nghĩa là thời gian truy cập bộ nhớ phụ thuộc vào vị trí của bộ nhớ so với bộ xử lý.

```
┌────────────────────────────────────────┐
│         NUMA Node 0                    │
│  ┌──────────────┐    ┌──────────────┐  │
│  │  CPU 0-15    │───>│   Memory     │  │
│  │  (Socket 0)  │    │   64GB       │  │
│  └──────────────┘    └──────────────┘  │
└────────────┬───────────────────────────┘
             │
             │ Interconnect (slower)
             │
┌────────────▼───────────────────────────┐
│         NUMA Node 1                    │
│  ┌──────────────┐    ┌──────────────┐  │
│  │  CPU 16-31   │───>│   Memory     │  │
│  │  (Socket 1)  │    │   64GB       │  │
│  └──────────────┘    └──────────────┘  │
└────────────────────────────────────────┘
```

**Truy cập bộ nhớ cục bộ và từ xa:**

* Cục bộ: CPU truy cập bộ nhớ trên cùng một nút NUMA (nhanh)
* Từ xa: CPU truy cập bộ nhớ trên một nút NUMA khác (chậm hơn, độ trễ gấp khoảng 2 lần)

### Kiểm tra cấu hình NUMA

```console
# Cài đặt numactl
apt install numactl  # Debian/Ubuntu
dnf install numactl  # RHEL/CentOS

# Xem cấu trúc tô-pô NUMA
numactl --hardware

# Kết quả đầu ra:
# available: 2 nodes (0-1)
# node 0 cpus: 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
# node 0 size: 65536 MB
# node 0 free: 32768 MB
# node 1 cpus: 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31
# node 1 size: 65536 MB
# node 1 free: 28672 MB
# node distances:
# node   0   1
#   0:  10  20
#   1:  20  10

# Giải thích khoảng cách:
# 10 = cục bộ (cùng nút)
# 20 = từ xa (nút khác)
# Số càng cao = càng nhiều bước nhảy/chậm hơn

# Kiểm tra số liệu thống kê NUMA
numastat

# Mức sử dụng bộ nhớ trên mỗi nút
numastat -m

# Thành công và thất bại của NUMA
numastat -c qemu-system-x86

# Theo dõi số liệu thống kê NUMA
watch -n 1 'numastat -c qemu-system-x86'
```

## Các khái niệm về ghim CPU (CPU Pinning)
### Tại sao lại ghim CPU?
#### Không sử dụng CPU pinning:
* Các máy ảo có thể chạy trên bất kỳ CPU vật lý nào
* Bộ lập lịch Linux di chuyển các luồng (thread) của máy ảo
* Xảy ra hiện tượng tranh chấp bộ nhớ đệm (cache thrashing)
* Hiệu năng không ổn định
* Hiệu suất sử dụng bộ nhớ đệm CPU kém

#### Với tính năng ghim CPU:
* Máy ảo được liên kết với các CPU vật lý cụ thể
* Không có sự di trú không mong muốn
* Tính cục bộ của bộ nhớ cache tốt hơn
* Hiệu suất có thể dự đoán được
* Giảm thiểu việc chuyển đổi ngữ cảnh

### Các chiến lược ghim CPU
1. Gán CPU chuyên dụng (Dedicated CPU Assignment):
    * Mỗi vCPU được gán cố định (pin) vào một CPU vật lý riêng biệt
    * VM có 4 vCPU → Gán vào các CPU 0, 1, 2, 3
    * Hiệu năng tối ưu, tài nguyên độc quyền
2. Gán CPU chia sẻ (Shared CPU Assignment):
    * Nhiều VM cùng chia sẻ các CPU vật lý
    * Phù hợp cho các tác vụ không yêu cầu khắt khe về hiệu năng
    * Mật độ VM cao hơn, chi phí thấp hơn
3. Gán cố định theo cấu trúc NUMA (NUMA-Aware Pinning):
    * Gán VM nằm trọn vẹn trong một node NUMA duy nhất
    * Tránh việc truy cập bộ nhớ giữa các node (cross-node)
    * Yếu tố then chốt đối với hiệu năng
4. Lưu ý về Hyper-threading:
    * Các luồng "anh em" (siblings) chia sẻ tài nguyên của cùng một nhân (core) CPU
    * CPU 0 và CPU 16 có thể là các luồng "anh em"
    * Kiểm tra bằng lệnh: cat /sys/devices/system/cpu/cpu0/topology/thread_siblings_list

## Triển khai ghim CPU (CPU Pinning)
### Kiểm tra cấu hình CPU của máy ảo
```console
# Xem thông tin CPU hiện tại của máy ảo
virsh vcpuinfo my-vm

# Kết quả đầu ra:
# VCPU:           0
# CPU:            3
# State:          running
# CPU time:       25.2s
# CPU Affinity:   yyyyyyyyyyyyyyyy

# "yyyyyyyy..." có nghĩa là có thể chạy trên bất kỳ CPU nào

# Xem số lượng vCPU
virsh vcpucount my-vm
```

### Ghim vCPU vào CPU vật lý
**Ghim CPU cơ bản:**

```console
# Ghim vCPU 0 vào CPU vật lý 0
virsh vcpupin my-vm 0 0

# Ghim vCPU 1 vào CPU vật lý 1
virsh vcpupin my-vm 1 1

# Ghim vCPU 2 vào CPU vật lý 2
virsh vcpupin my-vm 2 2

# Ghim vCPU 3 vào CPU vật lý 3
virsh vcpupin my-vm 3 3

# Xác minh việc ghim
virsh vcpupin my-vm

# Kết quả đầu ra:
# VCPU   CPU Affinity
# -----------------------
# 0      0
# 1      1
# 2      2
# 3      3
```

**Ghim Dải CPU:**

```console
# Cho phép vCPU chạy trên một dải các CPU.
virsh vcpupin my-vm 0 0-3

# Ghim vào các CPU cụ thể (không liên tục)
virsh vcpupin my-vm 0 0,2,4,6

# Ghim tất cả vCPU cùng lúc
virsh vcpupin my-vm --vcpu 0 --cpulist 0
virsh vcpupin my-vm --vcpu 1 --cpulist 1
virsh vcpupin my-vm --vcpu 2 --cpulist 2
virsh vcpupin my-vm --vcpu 3 --cpulist 3
```

### Thiết lập ghim CPU cố định
Việc gán CPU (CPU pinning) bằng virsh có hiệu lực ngay lập tức nhưng không được lưu lại lâu dài. Để thiết lập cố định:

```console
# Chỉnh sửa XML của máy ảo
virsh edit my-vm
```

```xml
<!-- Thêm cấu hình CPU pinning vào phần <cputune>: -->
<domain>
  <vcpu placement='static'>4</vcpu>
  <cputune>
    <vcpupin vcpu='0' cpuset='0'/>
    <vcpupin vcpu='1' cpuset='1'/>
    <vcpupin vcpu='2' cpuset='2'/>
    <vcpupin vcpu='3' cpuset='3'/>
  </cputune>
  ...
</domain>
```

```console
# Lưu và thoát. Khởi động lại máy ảo để các thay đổi có hiệu lực.
virsh shutdown my-vm
virsh start my-vm

# Xác minh
virsh vcpupin my-vm
```

### Tránh các luồng anh em (siblings) trong Hyper-threading
Tìm các luồng anh em (hyperthreading siblings)

```console
for cpu in /sys/devices/system/cpu/cpu[0-9]*; do
    echo -n "CPU $(basename $cpu | sed 's/cpu//'): "
    cat $cpu/topology/thread_siblings_list
done

# Ví dụ kết quả đầu ra:
# CPU 0: 0,16
# CPU 1: 1,17
# CPU 2: 2,18
# ...
```

Chỉ ghim vào các nhân vật lý (tránh các nhân "anh em" - siblings). Nếu bạn có 16 nhân vật lý hỗ trợ HT: Hãy sử dụng các CPU từ 0-15 HOẶC từ 16-31, không dùng lẫn lộn.

```console
virsh edit my-vm

<cputune>
  <vcpupin vcpu='0' cpuset='0'/>
  <vcpupin vcpu='1' cpuset='1'/>
  <vcpupin vcpu='2' cpuset='2'/>
  <vcpupin vcpu='3' cpuset='3'/>
</cputune>

# This avoids siblings if using lower half
```

### Ghim luồng trình giả lập
Các luồng trình giả lập QEMU xử lý I/O và việc giả lập thiết bị. Hãy gán cố định (pin) chúng một cách riêng biệt:
```console
virsh edit my-vm
```

```xml
<cputune>
  <!-- Ghim vCPU -->
  <vcpupin vcpu='0' cpuset='0'/>
  <vcpupin vcpu='1' cpuset='1'/>
  <vcpupin vcpu='2' cpuset='2'/>
  <vcpupin vcpu='3' cpuset='3'/>

  <!-- Ghim luồng trình giả lập -->
  <emulatorpin cpuset='4,5'/>

  <!-- Ghim luồng I/O -->
  <iothreadids>
    <iothread id='1'/>
    <iothread id='2'/>
  </iothreadids>
  <iothreadpin iothread='1' cpuset='6'/>
  <iothreadpin iothread='2' cpuset='7'/>
</cputune>
```

**Xác minh việc ghim trình giả lập:**

```console
# Tìm tiến trình QEMU
ps aux | grep qemu | grep my-vm

# Kiểm tra sự gắn kết luồng
ps -mo pid,tid,comm,psr -p <qemu-pid>
```

Cột PSR hiển thị luồng CPU nào đang thực thi.

## Cấu hình NUMA
### Gán node NUMA đơn
Giới hạn VM vào một node NUMA duy nhất (khuyến nghị):

```console
virsh edit my-vm
```

```xml
<domain>
  <vcpu placement='static'>8</vcpu>
  <numatune>
    <memory mode='strict' nodeset='0'/>
  </numatune>
  <cpu mode='host-passthrough'>
    <numa>
      <cell id='0' cpus='0-7' memory='16' unit='GiB' memAccess='shared'/>
    </numa>
  </cpu>
  <cputune>
    <vcpupin vcpu='0' cpuset='0'/>
    <vcpupin vcpu='1' cpuset='1'/>
    <vcpupin vcpu='2' cpuset='2'/>
    <vcpupin vcpu='3' cpuset='3'/>
    <vcpupin vcpu='4' cpuset='4'/>
    <vcpupin vcpu='5' cpuset='5'/>
    <vcpupin vcpu='6' cpuset='6'/>
    <vcpupin vcpu='7' cpuset='7'/>
  </cputune>
</domain>
```

* Điều này đảm bảo:
    - Tất cả vCPU từ nút NUMA 0
    - Tất cả bộ nhớ từ nút NUMA 0
    - Không có truy cập chéo giữa các nút

### Cấu trúc tô-pô NUMA nhiều node
Tạo cấu trúc tô-pô NUMA cho máy khách khớp với máy chủ:

```console
# Đối với máy ảo trải rộng trên nhiều node NUMA
virsh edit my-vm
```

```xml
<domain>
  <vcpu placement='static'>16</vcpu>
  <cpu mode='host-passthrough'>
    <numa>
      <cell id='0' cpus='0-7' memory='16' unit='GiB' memAccess='shared'/>
      <cell id='1' cpus='8-15' memory='16' unit='GiB' memAccess='shared'/>
    </numa>
  </cpu>
  <numatune>
    <memory mode='strict' nodeset='0-1'/>
    <memnode cellid='0' mode='strict' nodeset='0'/>
    <memnode cellid='1' mode='strict' nodeset='1'/>
  </numatune>
  <cputune>
    <!-- Node 0 vCPUs -->
    <vcpupin vcpu='0' cpuset='0'/>
    <vcpupin vcpu='1' cpuset='1'/>
    <vcpupin vcpu='2' cpuset='2'/>
    <vcpupin vcpu='3' cpuset='3'/>
    <vcpupin vcpu='4' cpuset='4'/>
    <vcpupin vcpu='5' cpuset='5'/>
    <vcpupin vcpu='6' cpuset='6'/>
    <vcpupin vcpu='7' cpuset='7'/>
    <!-- Node 1 vCPUs -->
    <vcpupin vcpu='8' cpuset='16'/>
    <vcpupin vcpu='9' cpuset='17'/>
    <vcpupin vcpu='10' cpuset='18'/>
    <vcpupin vcpu='11' cpuset='19'/>
    <vcpupin vcpu='12' cpuset='20'/>
    <vcpupin vcpu='13' cpuset='21'/>
    <vcpupin vcpu='14' cpuset='22'/>
    <vcpupin vcpu='15' cpuset='23'/>
  </cputune>
</domain>
```

### Các chính sách bộ nhớ NUMA
* strict: Việc cấp phát phải được thực hiện từ các node được chỉ định (thất bại nếu không có sẵn)
* ưu tiên: Thử các node được chỉ định trước, chuyển sang phương án dự phòng nếu cần.
* interleave: Phân bổ bộ nhớ trên các node
* restrictive: Giống như quy định nghiêm ngặt, nhưng việc thực thi còn nghiêm ngặt hơn nữa.

```xml
<memory mode='strict' nodeset='0'/>
<memory mode='preferred' nodeset='0'/>
<memory mode='interleave' nodeset='0-1'/>
<memory mode='restrictive' nodeset='0'/>
```

### Tự động định vị NUMA
Để libvirt tự động đặt máy ảo trên nút NUMA tối ưu.

```console
virsh edit my-vm
```

```xml
<vcpu placement='auto'>4</vcpu>
<numatune>
  <memory mode='strict' placement='auto'/>
</numatune>
```

* libvirt sẽ:
    - Phân tích cấu trúc tô-pô NUMA của máy chủ (host)
    - Tìm nút có đủ tài nguyên
    - Tự động gán (pin) máy ảo vào nút đó

Xem kết quả tự động sắp xếp

```console
virsh vcpuinfo my-vm
virsh numatune my-vm
```

## Cấu hình CPU nâng cao
### Định nghĩa cấu trúc tô-pô CPU
Xác định cấu trúc tô-pô CPU cụ thể bên trong máy ảo khách:

```console
virsh edit my-vm
```

```xml
<cpu mode='host-passthrough'>
  <topology sockets='2' cores='4' threads='2'/>
  <numa>
    <cell id='0' cpus='0-7' memory='16' unit='GiB'/>
    <cell id='1' cpus='8-15' memory='16' unit='GiB'/>
  </numa>
</cpu>
```

* Cấu hình này tạo ra bên trong máy khách (guest):
    - 2 socket
    - 4 nhân mỗi socket
    - 2 luồng mỗi nhân
    - Tổng cộng: 16 vCPU
    - 2 node NUMA hiển thị trong máy khách

### Lựa chọn model CPU
Host-passthrough (hiệu năng tốt nhất, hạn chế khả năng di chuyển máy ảo)

```xml
<cpu mode='host-passthrough'/>
```

Mô hình Host (hiệu năng tốt, khả năng di trú tốt hơn)

```xml
<cpu mode='host-model'/>
```

Tùy chỉnh (tương thích nhất, hạn chế tính năng)

```xml
<cpu mode='custom' match='exact'>
  <model>Broadwell</model>
</cpu>
```

Với các tính năng cụ thể

```xml
<cpu mode='custom' match='exact'>
  <model>Broadwell</model>
  <feature policy='require' name='pdpe1gb'/>
  <feature policy='require' name='pcid'/>
  <feature policy='disable' name='x2apic'/>
</cpu>
```

### Truyền trực tiếp bộ nhớ đệm CPU
Truyền trực tiếp qua cấu trúc bộ nhớ đệm CPU của máy chủ

```console
virsh edit my-vm
```

```xml
<cpu mode='host-passthrough'>
  <cache mode='passthrough'/>
  <topology sockets='1' cores='4' threads='2'/>
</cpu>
```

Cải thiện hiệu năng đối với các khối lượng công việc nhạy cảm với bộ nhớ đệm.

### Lập lịch CPU
```console
# Thiết lập các tham số bộ lập lịch CPU
virsh schedinfo my-vm --set cpu_shares=2048

# Chia sẻ CPU (trọng số tương đối)
# Mặc định: 1024
# Giá trị càng cao = càng tốn nhiều thời gian CPU

# Xem các cài đặt hiện tại
virsh schedinfo my-vm

# Lập lịch thời gian thực (yêu cầu nhân RT)
virsh edit my-vm
```

```xml
<cputune>
  <vcpusched vcpus='0-3' scheduler='fifo' priority='1'/>
</cputune>
```

## Tối ưu hóa hiệu năng
### Cấu hình điều tiết CPU

```console
# Thiết lập chế độ điều tiết CPU sang hiệu năng cao
echo performance | tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Xác minh
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# Làm cho bền vững
cat > /etc/systemd/system/cpu-performance.service << 'EOF'
[Unit]
Description=Set CPU governor to performance

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'echo performance | tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor'

[Install]
WantedBy=multi-user.target
EOF

systemctl enable cpu-performance
systemctl start cpu-performance
```

### Vô hiệu hóa tính năng tiết kiệm điện của CPU

```console
# Vô hiệu hóa C-state để duy trì độ trễ ổn định.
# Thêm vào các tham số kernel
vim /etc/default/grub

GRUB_CMDLINE_LINUX="intel_idle.max_cstate=0 processor.max_cstate=1 intel_pstate=disable idle=poll"

# Cập nhật GRUB
update-grub  # Debian/Ubuntu
grub2-mkconfig -o /boot/grub2/grub.cfg  # RHEL/CentOS

# Khởi động lại
reboot

# Xác minh
cat /sys/module/intel_idle/parameters/max_cstate
```

### Cô lập CPU
**Cô lập các CPU khỏi bộ lập lịch của máy chủ để chỉ dành riêng cho máy ảo sử dụng:**

```console
# Chỉnh sửa các tham số kernel
vim /etc/default/grub

# Cô lập các CPU từ 0 đến 7 cho các máy ảo.
GRUB_CMDLINE_LINUX="isolcpus=0-7 nohz_full=0-7 rcu_nocbs=0-7"

# Cập nhật GRUB và khởi động lại.
update-grub
reboot

# Xác minh sự cách ly
cat /sys/devices/system/cpu/isolated
```

Giờ đây, các máy ảo được ghim vào các CPU từ 0 đến 7 sẽ chịu rất ít sự can thiệp từ máy chủ vật lý.

### Cấu hình Huge Pages
Tính toán số lượng huge page cần thiết. Ví dụ: VM 16GB = 8192 huge page (mỗi trang 2MB).

```console
# Cấu hình huge page
echo 8192 > /proc/sys/vm/nr_hugepages

# Làm cho bền vững
echo "vm.nr_hugepages=8192" >> /etc/sysctl.conf
sysctl -p

# Xác minh
cat /proc/meminfo | grep Huge

# Cấu hình máy ảo sử dụng huge page
virsh edit my-vm

<memoryBacking>
  <hugepages/>
  <locked/>
</memoryBacking>

# Khởi động lại máy ảo
virsh shutdown my-vm
virsh start my-vm

# Xác minh rằng máy ảo đang sử dụng huge pages.
grep Huge /proc/meminfo
```

### Cân bằng NUMA
Vô hiệu hóa tính năng cân bằng NUMA tự động (có thể gây xung đột với việc ghim tiến trình/luồng)

```console
echo 0 > /proc/sys/kernel/numa_balancing

# Làm cho bền vững
echo "kernel.numa_balancing=0" >> /etc/sysctl.conf
sysctl -p

# Xác minh
cat /proc/sys/kernel/numa_balancing
```

## Giám sát và Xác minh
### Xác minh việc ghim CPU

```console
# Kiểm tra độ gắn kết vCPU
virsh vcpupin my-vm

# Kiểm tra mức sử dụng CPU thực tế
virsh vcpuinfo my-vm

# Tìm tiến trình QEMU
ps aux | grep qemu | grep my-vm

# Kiểm tra sự gắn kết luồng
taskset -cp <qemu-pid>

# Thông tin chi tiết theo từng luồng
ps -mo pid,tid,comm,psr -p <qemu-pid>
```

PSR = Số hiệu bộ xử lý (CPU nào)

### Theo dõi số liệu thống kê NUMA

```console
# Số liệu thống kê NUMA của máy ảo
virsh numastat my-vm

# Số liệu thống kê NUMA toàn hệ thống
numastat

# Mức sử dụng bộ nhớ trên mỗi node
numastat -m

# Xem theo thời gian thực
watch -n 1 'numastat -c qemu-system-x86'

# Kiểm tra các trường hợp lỗi NUMA (không tốt – cho thấy có sự truy cập chéo giữa các node).
numastat | grep numa_miss
```

### Giám sát hiệu năng CPU

```console
# Cài đặt perf
apt install linux-tools-generic  # Debian/Ubuntu
dnf install perf  # RHEL/CentOS

# Theo dõi hiệu năng CPU của máy ảo
perf stat -p <qemu-pid> -a sleep 10

# Kiểm tra lỗi bộ nhớ cache
perf stat -e cache-misses,cache-references -p <qemu-pid> sleep 10

# Theo dõi việc chuyển đổi ngữ cảnh
perf stat -e context-switches -p <qemu-pid> sleep 10

# Phân tích chi tiết
perf top -p <qemu-pid>
```

### Kiểm thử độ trễ

```console
# Bên trong VM: Kiểm tra độ trễ bằng cyclictest
apt install rt-tests  # Debian/Ubuntu
dnf install rt-tests  # RHEL/CentOS

# Chạy kiểm tra độ trễ
cyclictest -t4 -p80 -n -i1000 -l10000

# Kết quả đầu ra hiển thị độ trễ tối min/max/avg.
# Độ trễ tối đa thấp hơn = tốt hơn cho các tác vụ thời gian thực.
```

## Khắc phục sự cố
### Vấn đề: Hiệu suất kém dù đã ghim

```console
# Kiểm tra xem các CPU có thực sự bị cô lập hay không.
cat /sys/devices/system/cpu/isolated

# Kiểm tra trình quản lý CPU
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Kiểm tra các chuyển đổi ngữ cảnh cao
pidstat -w -p <qemu-pid> 1 10

# Theo dõi thời gian CPU bị chiếm dụng (steal time)
top -d 1
# Hãy tìm %st (thời gian ăn cắp) - giá trị phải là 0.
```

* Giải pháp:
    1. Cách ly các CPU khỏi bộ lập lịch của máy chủ (host scheduler)
    2. Thiết lập chế độ quản lý hiệu năng (performance governor)
    3. Tắt tính năng quản lý điện năng
    4. Sử dụng huge pages

### Vấn đề: Tỷ lệ trượt NUMA cao

```console
# Kiểm tra số liệu thống kê NUMA
numastat -c qemu-system-x86 | grep numa_miss

# Xác minh VM đang chạy trên một node duy nhất.
virsh numatune my-vm

# Kiểm tra xem bộ nhớ có bị phân chia giữa các node hay không.
numastat -p <qemu-pid>

# Giải pháp:
# 1. Ghim VM vào một node NUMA duy nhất
virsh numatune my-vm --mode strict --nodeset 0

# 2. Chỉnh sửa XML của máy ảo để áp dụng cấu hình NUMA nghiêm ngặt
virsh edit my-vm
<numatune>
  <memory mode='strict' nodeset='0'/>
</numatune>

# 3. Vô hiệu hóa tính năng tự động cân bằng NUMA
echo 0 > /proc/sys/kernel/numa_balancing
```

### Vấn đề: Tính năng ghim CPU (CPU Pinning) không hoạt động

```console
# Xác minh rằng tính năng ghim đã được thiết lập.
virsh vcpupin my-vm

# Kiểm tra xem dịch vụ libvirt có đang chạy hay không.
systemctl status libvirtd

# Xác minh cấu hình XML
virsh dumpxml my-vm | grep -A 10 cputune

# Kiểm tra xung đột
# - Đảm bảo CPU không bị phân bổ vượt mức.
# - Kiểm tra xem có sự trùng lặp ghim với các máy ảo khác hay không.

# Áp dụng lại tính năng ghim
virsh vcpupin my-vm 0 0 --live --config
virsh vcpupin my-vm 1 1 --live --config

# Khởi động lại máy ảo
virsh shutdown my-vm
virsh start my-vm
```

### Vấn đề: Di trú thất bại khi sử dụng tính năng ghim (pinning)
Tính năng ghim CPU (CPU pinning) có thể ngăn chặn quá trình di chuyển nếu đích đến không có cùng loại CPU.

Giải pháp 1: Sử dụng các dải cpuset để đảm bảo tính linh hoạt.

```xml 
<vcpupin vcpu='0' cpuset='0-3'/>

```

Giải pháp 2: Hủy ghim trước khi di chuyển

```console
virsh vcpupin ubuntu-vm 0 0-31  # Allow all CPUs
virsh migrate --live ubuntu-vm qemu+ssh://dest/system
# Ghim lại trên máy chủ đích
```

Giải pháp 3: Đảm bảo cấu trúc liên kết CPU giống hệt nhau trên cả hai máy chủ.

## Các ví dụ về cấu hình trong thực tế
### Máy chủ cơ sở dữ liệu hiệu năng cao

```xml
<domain type='kvm'>
  <name>db-server</name>
  <memory unit='GiB'>32</memory>
  <vcpu placement='static'>8</vcpu>
  <memoryBacking>
    <hugepages/>
    <locked/>
  </memoryBacking>
  <numatune>
    <memory mode='strict' nodeset='0'/>
  </numatune>
  <cpu mode='host-passthrough'>
    <cache mode='passthrough'/>
    <topology sockets='1' cores='8' threads='1'/>
    <numa>
      <cell id='0' cpus='0-7' memory='32' unit='GiB'/>
    </numa>
  </cpu>
  <cputune>
    <vcpupin vcpu='0' cpuset='0'/>
    <vcpupin vcpu='1' cpuset='1'/>
    <vcpupin vcpu='2' cpuset='2'/>
    <vcpupin vcpu='3' cpuset='3'/>
    <vcpupin vcpu='4' cpuset='4'/>
    <vcpupin vcpu='5' cpuset='5'/>
    <vcpupin vcpu='6' cpuset='6'/>
    <vcpupin vcpu='7' cpuset='7'/>
    <emulatorpin cpuset='8-9'/>
  </cputune>
</domain>
```

### Máy chủ ứng dụng thời gian thực

```xml
<domain type='kvm'>
  <name>rt-app</name>
  <memory unit='GiB'>16</memory>
  <vcpu placement='static'>4</vcpu>
  <memoryBacking>
    <hugepages/>
    <locked/>
    <nosharepages/>
  </memoryBacking>
  <numatune>
    <memory mode='strict' nodeset='0'/>
  </numatune>
  <cpu mode='host-passthrough'>
    <cache mode='passthrough'/>
    <topology sockets='1' cores='4' threads='1'/>
  </cpu>
  <cputune>
    <vcpupin vcpu='0' cpuset='0'/>
    <vcpupin vcpu='1' cpuset='1'/>
    <vcpupin vcpu='2' cpuset='2'/>
    <vcpupin vcpu='3' cpuset='3'/>
    <emulatorpin cpuset='4'/>
    <vcpusched vcpus='0-3' scheduler='fifo' priority='1'/>
  </cputune>
</domain>
```

### Máy ảo cỡ lớn đa NUMA

```xml
<domain type='kvm'>
  <name>large-vm</name>
  <memory unit='GiB'>128</memory>
  <vcpu placement='static'>32</vcpu>
  <memoryBacking>
    <hugepages/>
  </memoryBacking>
  <cpu mode='host-model'>
    <topology sockets='2' cores='8' threads='2'/>
    <numa>
      <cell id='0' cpus='0-15' memory='64' unit='GiB'/>
      <cell id='1' cpus='16-31' memory='64' unit='GiB'/>
    </numa>
  </cpu>
  <numatune>
    <memory mode='strict' nodeset='0-1'/>
    <memnode cellid='0' mode='strict' nodeset='0'/>
    <memnode cellid='1' mode='strict' nodeset='1'/>
  </numatune>
  <cputune>
    <!-- NUMA node 0 -->
    <vcpupin vcpu='0' cpuset='0'/>
    <vcpupin vcpu='1' cpuset='1'/>
    <!-- ... pins for vcpu 2-15 -->
    <vcpupin vcpu='15' cpuset='15'/>
    <!-- NUMA node 1 -->
    <vcpupin vcpu='16' cpuset='16'/>
    <vcpupin vcpu='17' cpuset='17'/>
    <!-- ... pins for vcpu 18-31 -->
    <vcpupin vcpu='31' cpuset='31'/>
  </cputune>
</domain>
```

## Các phương pháp tốt nhất
### Lập kế hoạch phân bổ CPU
1. **Lập sơ đồ cấu trúc tô-pô máy chủ trước.**

```console
lscpu
numactl --hardware
lstopo
```

2. Định cỡ máy ảo phù hợp với các nút NUMA
    * Ưu tiên đặt các máy ảo trong cùng một nút NUMA
    * Nếu cần sử dụng nhiều nút, hãy căn chỉnh theo cấu trúc tô-pô của máy chủ vật lý
3. Dành riêng CPU cho máy chủ
    * Đừng gán toàn bộ CPU cho các máy ảo.
    * Hãy dành lại 1-2 CPU cho các tiến trình của máy chủ vật lý (host).
4. Chiến lược ghim tài liệu: Tạo bản đồ phân bổ

```
# Node 0: CPUs 0-15
#   - VM1: CPUs 0-3
#   - VM2: CPUs 4-7
#   - VM3: CPUs 8-11
#   - Host: CPUs 12-15
```

### Danh mục kiểm tra tối ưu hóa hiệu năng

```console
# 1. Bật huge pages
echo 8192 > /proc/sys/vm/nr_hugepages

# 2. Thiết lập cơ chế điều tiết hiệu năng (performance governor)
echo performance | tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# 3. Vô hiệu hóa tính năng cân bằng NUMA
echo 0 > /proc/sys/kernel/numa_balancing

# 4. Cô lập các CPU (tùy chọn)
# Thêm vào kernel: isolcpus=0-15

# 5. Tắt chế độ tiết kiệm điện của CPU
# Thêm vào kernel: intel_idle.max_cstate=0

# 6. Ghim các máy ảo vào các node NUMA
virsh edit vm # Thêm numatune

# 7. Sử dụng chế độ CPU host-passthrough
virsh edit vm # <cpu mode='host-passthrough'/>

# 8. Bật tính năng chuyển trực tiếp bộ nhớ đệm (cache passthrough)
virsh edit vm # <cache mode='passthrough'/>
```

## Kết luận
CPU pinning và cấu hình NUMA là những kỹ thuật mạnh mẽ giúp tối ưu hóa hiệu năng máy ảo trong môi trường KVM/QEMU. Bằng cách đảm bảo thiết lập CPU affinity (gắn kết CPU) và tính cục bộ của bộ nhớ (memory locality) một cách hợp lý, cũng như giảm thiểu việc truy cập chéo giữa các nút NUMA, bạn có thể đạt được mức hiệu năng tiệm cận với máy chủ vật lý (bare-metal).

Các điểm chính:

* Luôn phân tích cấu trúc liên kết máy chủ trước khi cấu hình máy ảo
* Ưu tiên bố trí một nút NUMA duy nhất để đạt hiệu suất tốt nhất
* Sử dụng ghim CPU để có hiệu suất ổn định và dễ dự đoán
* Kích hoạt các trang bộ nhớ lớn cho các khối lượng công việc tiêu tốn nhiều bộ nhớ
* Theo dõi số liệu thống kê NUMA để xác định các tổn thất do phân bổ bộ nhớ giữa các nút
* Cách ly CPU cho các ứng dụng nhạy cảm với độ trễ
* Ghi lại các chiến lược phân bổ CPU để bảo trì

Các tối ưu hóa này đặc biệt quan trọng đối với:

* Các cơ sở dữ liệu hiệu năng cao
* Các ứng dụng thời gian thực
* Các khối lượng công việc HPC (Tính toán hiệu năng cao)
* Các dịch vụ nhạy cảm với độ trễ
* Các ứng dụng có lưu lượng xử lý lớn

Việc cấu hình đúng CPU và NUMA giúp chuyển đổi ảo hóa từ một giải pháp vốn phải đánh đổi hiệu năng thành một nền tảng có khả năng đáp ứng các khối lượng công việc sản xuất đòi hỏi khắt khe với mức hao hụt tài nguyên (overhead) tối thiểu. Khi kết hợp với các tối ưu hóa khác như mạng SR-IOV và lưu trữ NVMe, công nghệ ảo hóa hiện đại có thể mang lại hiệu năng ngang ngửa, thậm chí vượt trội so với các mô hình triển khai trên máy chủ vật lý truyền thống (bare-metal).
