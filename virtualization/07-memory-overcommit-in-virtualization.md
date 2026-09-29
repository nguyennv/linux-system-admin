# Cấp phát bộ nhớ vượt mức trong ảo hóa: Hướng dẫn toàn diện
## Giới thiệu
Cấp phát bộ nhớ vượt mức (memory overcommitment) là một kỹ thuật ảo hóa mạnh mẽ, cho phép cấp phát cho các máy ảo (VM) lượng bộ nhớ lớn hơn dung lượng vật lý thực tế có sẵn trên máy chủ (host). Khả năng này giúp tăng mật độ máy ảo, cải thiện hiệu suất sử dụng tài nguyên và tiết kiệm chi phí; tuy nhiên, nó đòi hỏi quy trình cấu hình và giám sát kỹ lưỡng để tránh tình trạng suy giảm hiệu năng.

Hướng dẫn toàn diện này sẽ đi sâu vào các chiến lược, công nghệ và các phương pháp tối ưu (best practices) liên quan đến việc cấp phát bộ nhớ vượt mức trong môi trường KVM/QEMU. Bạn sẽ học cách cấp phát bộ nhớ vượt mức một cách an toàn, triển khai kỹ thuật memory ballooning, cấu hình KSM (Kernel Same-page Merging) và giám sát áp lực bộ nhớ nhằm duy trì hiệu năng tối ưu đồng thời tối đa hóa hiệu quả sử dụng cơ sở hạ tầng.

Nếu không hiểu rõ cơ chế cấp phát bộ nhớ vượt mức, bạn có thể đối mặt với rủi ro lãng phí phần cứng đắt tiền do quá thận trọng, hoặc gây ra các vấn đề nghiêm trọng về hiệu năng do cấp phát quá mức dẫn đến tình trạng hoán đổi bộ nhớ (swapping) và cơ chế OOM kill (ngắt tiến trình do thiếu bộ nhớ). Chìa khóa nằm ở việc tìm ra sự cân bằng hợp lý dựa trên đặc thù khối lượng công việc và khả năng giám sát hệ thống.

Sau khi hoàn thành hướng dẫn này, bạn sẽ nắm vững các kỹ thuật cấp phát bộ nhớ vượt mức để vận hành nhiều máy ảo hơn trên mỗi máy chủ mà vẫn đảm bảo hiệu năng, hiểu rõ các yếu tố đánh đổi liên quan, cũng như biết cách khắc phục các sự cố về bộ nhớ trong môi trường ảo hóa.

## Hiểu về tình trạng cấp phát bộ nhớ quá mức
### Cấp phát bộ nhớ quá mức là gì?
Tình trạng cấp phát bộ nhớ vượt mức (memory overcommit) có nghĩa là tổng dung lượng bộ nhớ được cấp phát cho tất cả các máy ảo (VM) vượt quá dung lượng RAM vật lý hiện có trên máy chủ vật lý (host).

Ví dụ:

```
RAM vật lý của máy chủ: 64 GB
VM1: 16 GB
VM2: 16 GB
VM3: 16 GB
VM4: 16 GB
VM5: 16 GB
Tổng số đã phân bổ: 80 GB
Tỷ lệ phân bổ vượt mức: 1.25:1 (80/64)
```

### Tại sao lại thực hiện cấp phát bộ nhớ quá mức?
**Lợi ích:**

* Mật độ máy ảo (VM) cao hơn (nhiều VM hơn trên mỗi máy chủ vật lý)
* Hiệu suất sử dụng tài nguyên tốt hơn (các VM hiếm khi sử dụng 100% dung lượng RAM)
* Tiết kiệm chi phí (giảm số lượng máy chủ vật lý cần thiết)
* Linh hoạt trong việc xác định quy mô máy ảo

**Các rủi ro:**

* Suy giảm hiệu năng khi gặp áp lực về bộ nhớ
* Hoán đổi dữ liệu sang đĩa (tốc độ cực chậm)
* Bị chấm dứt tiến trình do lỗi OOM (Hết bộ nhớ)
* Hiệu năng không ổn định/khó dự đoán

### Các công nghệ vượt mức phân bổ bộ nhớ
1. Memory Ballooning
    * Máy ảo (guest) phối hợp với máy chủ (host)
    * Trả lại phần bộ nhớ không sử dụng cho máy chủ
    * Yêu cầu có trình điều khiển "balloon" trong máy ảo
2. KSM (Kernel Same-page Merging)
    * Khử trùng lặp các trang bộ nhớ giống hệt nhau
    * Giảm mức sử dụng bộ nhớ thực tế
    * Tốn tài nguyên CPU cho việc quét bộ nhớ
3. Transparent Huge Pages (THP)
    * Sử dụng các trang bộ nhớ 2MB thay vì 4KB
    * Giảm số lần trượt TLB (TLB misses)
    * Cải thiện hiệu năng
4. Memory Swapping (Hoán đổi bộ nhớ)
    * Cơ chế giải quyết cuối cùng
    * Tốc độ cực kỳ chậm
    * Nên tránh sử dụng
5. zswap/zram
    * Bộ đệm RAM đã nén
    * Hiệu quả hơn so với hoán đổi trên đĩa (disk swap)
    * Tốn tài nguyên CPU cho việc nén dữ liệu

## Kiểm tra trạng thái bộ nhớ máy chủ
### Xem bộ nhớ vật lý
* Tổng bộ nhớ hệ thống

```console
free -h
```

Kết quả đầu ra:

```
              total        used        free      shared  buff/cache   available
Mem:           62Gi       15Gi       30Gi       1.0Gi       16Gi        45Gi
Swap:          8.0Gi       0B         8.0Gi
```

* Thông tin chi tiết về bộ nhớ
```console
cat /proc/meminfo | head -20
```

* Bộ nhớ theo node NUMA

```console
numactl --hardware
```

* Bộ nhớ trống trên mỗi núnodet

```console
numastat -m
```

### Kiểm tra mức sử dụng bộ nhớ hiện tại của máy ảo
* Liệt kê tất cả các máy ảo cùng với thông tin cấp phát bộ nhớ.

```bash
virsh list --all
for vm in $(virsh list --name); do
    echo "VM: $vm"
    virsh dominfo $vm | grep memory
done
```

* Tổng bộ nhớ được cấp phát

```bash
virsh list --name | while read vm; do
    virsh dominfo $vm | grep "Max memory"
done | awk '{sum+=$3} END {print "Total allocated: " sum/1024/1024 " GB"}'
```

* Mức sử dụng bộ nhớ thực tế trên mỗi máy ảo

```bash
virsh list --name | while read vm; do
    echo -n "$vm: "
    virsh dommemstat $vm 2>/dev/null | grep actual
done
```

### Tính toán tỷ lệ vượt mức cam kết hiện tại
Tính toán tỷ lệ vượt mức cam kết bộ nhớ

```bash
#!/bin/bash

HOST_MEM=$(free -b | awk 'NR==2 {print $2}')
HOST_MEM_GB=$(echo "scale=2; $HOST_MEM/1024/1024/1024" | bc)

TOTAL_ALLOCATED=0
for vm in $(virsh list --all --name); do
  VM_MEM=$(virsh dominfo $vm 2>/dev/null | grep "Max memory" | awk '{print $3}')
  TOTAL_ALLOCATED=$((TOTAL_ALLOCATED + VM_MEM))
done

TOTAL_ALLOCATED_GB=$(echo "scale=2; $TOTAL_ALLOCATED/1024/1024" | bc)
OVERCOMMIT=$(echo "scale=2; $TOTAL_ALLOCATED_GB / $HOST_MEM_GB" | bc)

echo "Host Memory: ${HOST_MEM_GB} GB"
echo "Total Allocated: ${TOTAL_ALLOCATED_GB} GB"
echo "Overcommit Ratio: ${OVERCOMMIT}:1"

if (( $(echo "$OVERCOMMIT > 1.5" | bc -l) )); then
  echo "WARNING: High overcommit ratio!"
fi
```

## Cơ chế Ballooning bộ nhớ
### Tìm hiểu về Ballooning bộ nhớ
Cơ chế ballooning bộ nhớ cho phép máy chủ thu hồi bộ nhớ từ các máy ảo một cách linh hoạt:

```
┌─────────────────────────────────────┐
│         Máy ảo                      │
│                                     │
│  ┌────────────────────────────┐     │
│  │   Balloon Driver (virtio)  │     │
│  │   Inflates/Deflates        │     │
│  └────────────────────────────┘     │
│           │                         │
└───────────┼─────────────────────────┘
            │ Điều khiển Balloon
┌───────────▼─────────────────────────┐
│         Hệ thống chủ                │
│   Bộ nhớ đã được thu hồi/cấp phát   │
└─────────────────────────────────────┘
```

### Cấu hình Ballooning bộ nhớ
#### Bật thiết bị balloon trong máy ảo:
* Kiểm tra xem máy ảo có thiết bị balloon hay không.

```console
virsh dumpxml my-vm | grep balloon
```

* Nếu chưa có thì thêm vào.

```console
virsh edit my-vm
```

* Thêm vào trước `</devices>`:

```xml
<memballoon model='virtio'>
  <stats period='10'/>
  <address type='pci' domain='0x0000' bus='0x00' slot='0x08' function='0x0'/>
</memballoon>
```

* Khởi động lại máy ảo

```console
virsh shutdown my-vm
virsh start my-vm
```

* Xác minh thiết bị balloon

```console
virsh dumpxml my-vm | grep balloon
```

#### Bên trong máy ảo khách (xác minh trình điều khiển):
* Kiểm tra xem balloon driver đã được nạp chưa

```console
lsmod | grep virtio_balloon
```

* Nếu chưa được tải

```console
modprobe virtio_balloon
```

* Làm cho bền vững

```console
echo "virtio_balloon" >> /etc/modules
```

### Sử dụng Memory Ballooning
* Xem các cài đặt bộ nhớ hiện tại

```console
virsh dominfo my-vm | grep memory
```

> Kết quả đầu ra:
> - Max memory:     4194304 KiB (4 GB)
> - Used memory:    4194304 KiB (4 GB)

* Thiết lập bộ nhớ tối đa (yêu cầu khởi động lại máy ảo)

```console
virsh setmaxmem my-vm 4G --config
```

* Thiết lập bộ nhớ hiện tại (đang hoạt động, sử dụng cơ chế balloon)

```console
virsh setmem my-vm 2G --live
```

Máy ảo hiện thực tế có 2GB, máy chủ đã thu hồi lại 2GB.

* Xem số liệu thống kê bộ nhớ

```console
virsh dommemstat my-vm
```

Kết quả đầu ra:

```
actual 2097152  (current allocation)
swap_in 0
swap_out 0
major_fault 2234
minor_fault 89563
unused 1572864  (unused by guest)
available 2097152
usable 1835008  (guest can use)
rss 2359296     (host RSS)
```

### Tự động Ballooning
#### Sử dụng numad để quản lý NUMA và bộ nhớ tự động:
* Cài đặt numad

```console
apt install numad  # Debian/Ubuntu
dnf install numad  # RHEL/CentOS
```

* Khởi động numad

```console
systemctl start numad
systemctl enable numad
```

* Cấu hình máy ảo để quản lý tự động

```console
virsh edit my-vm
```

```xml
<vcpu placement='auto'>4</vcpu>
<numatune>
  <memory mode='strict' placement='auto'/>
</numatune>
```

* numad sẽ tự động:
    - Đặt máy ảo (VM) vào nút NUMA tối ưu
    - Điều chỉnh bộ nhớ dựa trên mức sử dụng
    - Cân bằng tải giữa các nút NUMA

### Kernel Same-page Merging (KSM)
### Tìm hiểu về KSM
KSM quét bộ nhớ để tìm các trang giống hệt nhau và hợp nhất chúng, chỉ giữ lại một bản sao duy nhất. Khi một trang bị sửa đổi, nó sẽ được sao chép (cơ chế COW - Copy-on-Write).

**Cách thức hoạt động của KSM:**

```
Trước KSM:
VM1: Trang A (nội dung: "xxxxx")  ─┐
VM2: Trang A (nội dung: "xxxxx")  ─┤ Các trang giống hệt nhau
VM3: Trang A (nội dung: "xxxxx")  ─┘
Tổng cộng: 3 trang

Sau KSM:
VM1: ─┐
VM2: ─┤──> Trang được chia sẻ A (nội dung: "xxxxx")
VM3: ─┘
Tổng cộng: 1 trang (giải phóng 2 trang)
```

### Bật KSM
* Kiểm tra trạng thái KSM

```console
cat /sys/kernel/mm/ksm/run
```

0 = disabled, 1 = enabled

* Bật KSM

```console
echo 1 | sudo tee /sys/kernel/mm/ksm/run
```

* Cấu hình các tham số KSM. Số trang cần quét mỗi lần chạy

```console
echo 100 | sudo tee /sys/kernel/mm/ksm/pages_to_scan
```

* Thời gian nghỉ giữa các lần quét (mili giây)

```console
echo 20 | sudo tee /sys/kernel/mm/ksm/sleep_millisecs
```

* Làm cho bền vững

```console
cat > /etc/tmpfiles.d/ksm.conf << 'EOF'
w /sys/kernel/mm/ksm/run - - - - 1
w /sys/kernel/mm/ksm/pages_to_scan - - - - 100
w /sys/kernel/mm/ksm/sleep_millisecs - - - - 20
EOF
```

* Hoặc tạo dịch vụ systemd

```console
cat > /etc/systemd/system/ksm.service << 'EOF'
[Unit]
Description=Enable Kernel Same-page Merging

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'echo 1 > /sys/kernel/mm/ksm/run'
ExecStart=/bin/bash -c 'echo 100 > /sys/kernel/mm/ksm/pages_to_scan'
ExecStart=/bin/bash -c 'echo 20 > /sys/kernel/mm/ksm/sleep_millisecs'

[Install]
WantedBy=multi-user.target
EOF
```

```console
systemctl enable ksm
systemctl start ksm
```

### Theo dõi hiệu suất KSM
* Số liệu thống kê KSM

```console
cat /sys/kernel/mm/ksm/pages_sharing
cat /sys/kernel/mm/ksm/pages_shared
cat /sys/kernel/mm/ksm/pages_unshared
cat /sys/kernel/mm/ksm/pages_volatile
```

* Tính toán dung lượng bộ nhớ tiết kiệm được

```console
SHARING=$(cat /sys/kernel/mm/ksm/pages_sharing)
SHARED=$(cat /sys/kernel/mm/ksm/pages_shared)
SAVED=$((SHARING - SHARED))
SAVED_MB=$((SAVED * 4 / 1024))
echo "Memory saved by KSM: ${SAVED_MB} MB"
```

* Thông tin chi tiết về KSM

```console
grep -H '' /sys/kernel/mm/ksm/*
```

* Theo dõi KSM theo thời gian

```console
watch -n 5 'echo "Pages sharing: $(cat /sys/kernel/mm/ksm/pages_sharing)"; \
            echo "Pages shared: $(cat /sys/kernel/mm/ksm/pages_shared)"; \
            echo "Saved: $(( ($(cat /sys/kernel/mm/ksm/pages_sharing) - \
                         $(cat /sys/kernel/mm/ksm/pages_shared)) * 4 / 1024 )) MB"'
```

### Cấu hình các máy ảo cho KSM
Bật tính năng gộp bộ nhớ trong máy ảo

```console
virsh edit my-vm
```

```xml
<memoryBacking>
  <nosharepages/>  <!-- Vô hiệu hóa KSM cho máy ảo này (nếu cần) -->
</memoryBacking>
```

Hoặc kích hoạt một cách tường minh (hành vi mặc định). Chỉ cần xóa `<nosharepages/>` hoặc không đưa nó vào.

* Để đạt hiệu quả tối đa từ KSM, các máy ảo (VM) nên:
    - Chạy các hệ điều hành tương đồng (giúp tăng số lượng trang bộ nhớ giống hệt nhau)
    - Sử dụng các ứng dụng tương đồng
    - Có cấu hình tương đồng

### Tinh chỉnh hiệu suất KSM
* Quét tích cực (tốn nhiều CPU hơn, hợp nhất tốt hơn)

```console
echo 500 | sudo tee /sys/kernel/mm/ksm/pages_to_scan
echo 10 | sudo tee /sys/kernel/mm/ksm/sleep_millisecs
```

* Quét thận trọng (ít sử dụng CPU hơn, ít thực hiện gộp hơn)

```console
echo 50 | sudo tee /sys/kernel/mm/ksm/pages_to_scan
echo 100 | sudo tee /sys/kernel/mm/ksm/sleep_millisecs
```

* Cân bằng (khuyên dùng)

```console
echo 100 | sudo tee /sys/kernel/mm/ksm/pages_to_scan
echo 20 | sudo tee /sys/kernel/mm/ksm/sleep_millisecs
```

* Theo dõi mức độ ảnh hưởng đến CPU

```console
top -d 1
```

Tìm kiếm tiến trình [ksmd]

## Transparent Huge Pages (THP)
### Tìm hiểu về THP
Trang thông thường: 4KB Trang dung lượng lớn: 2MB (lớn hơn 512 lần)

**Lợi ích:**

* Giảm số lần trượt TLB
* Giảm chi phí quản lý bảng trang
* Hiệu năng bộ nhớ tốt hơn

### Bật THP cho các máy ảo
* Kiểm tra trạng thái THP

```console
cat /sys/kernel/mm/transparent_hugepage/enabled
# [always] madvise never
```

* Bật THP

```console
echo always | sudo tee /sys/kernel/mm/transparent_hugepage/enabled
```

* Cấu hình chống phân mảnh (nén dữ liệu)

```console
echo defer | sudo tee /sys/kernel/mm/transparent_hugepage/defrag
# Tùy chọn: always defer defer+madvise madvise never
```

* Làm cho bền vững

```console
cat >> /etc/rc.local << 'EOF'
echo always > /sys/kernel/mm/transparent_hugepage/enabled
echo defer > /sys/kernel/mm/transparent_hugepage/defrag
EOF

chmod +x /etc/rc.local
```

### Cấu hình các máy ảo cho THP
Các máy ảo tự động sử dụng THP nếu tính năng này được bật trên máy chủ vật lý (host).

* Kiểm tra bên trong máy ảo khách

```console
cat /proc/meminfo | grep Huge
```

AnonHugePages: 2097152 kB (2GB dưới dạng huge page)

* Xác minh việc phân bổ THP

```console
cat /sys/kernel/mm/transparent_hugepage/khugepaged/pages_collapsed
```

* Theo dõi hiệu quả của THP

```console
watch -n 1 'cat /proc/meminfo | grep Huge'
```

### THP so với Huge Pages thông thường
* THP (Transparent Huge Pages)
    - Tự động
    - Không cấp phát trước
    - Động
    - Có thể gây ra các đợt tăng đột biến về độ trễ (do quá trình nén/gom vùng nhớ - compaction)
* Huge Pages thông thường
    - Cấu hình thủ công
    - Được cấp phát trước
    - Đảm bảo khả năng sẵn sàng
    - Không tốn chi phí xử lý cho việc nén/gom vùng nhớ

* Cấu hình huge page thông thường cho máy ảo
```console
virsh edit my-vm
```

```xml
<memoryBacking>
  <hugepages>
    <page size='2048' unit='KiB'/>
  </hugepages>
  <locked/>
</memoryBacking>
```

* Phân bổ trước các trang bộ nhớ lớn trên máy chủ.

```console
echo 4096 > /proc/sys/vm/nr_hugepages
```

4096 trang * 2MB = 8GB

## Hoán đổi bộ nhớ và zswap
### Tìm hiểu về VM Swapping
**Nên tránh việc hoán đổi:**

* Cực kỳ chậm (so sánh giữa ổ đĩa và RAM)
* Chậm hơn RAM từ 100 đến 1000 lần
* Gây suy giảm hiệu năng nghiêm trọng
* Cho thấy tình trạng thiếu hụt bộ nhớ (memory pressure)


### Cấu hình Swap cho Host
* Kiểm tra swap hiện tại

```console
free -h
swapon --show
```

* Tạo tệp hoán đổi nếu cần

```console
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

* Làm cho bền vững

```console
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

* Cấu hình swappiness (mức độ tích cực thực hiện swap). Mặc định: 60, Phạm vi: 0-100. Thấp hơn = ít hoán đổi hơn

```console
sudo sysctl vm.swappiness=10
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.conf
```

### Bật zswap (Bộ nhớ đệm RAM nén)
zswap nén các trang dữ liệu trước khi ghi vào vùng swap. Nhanh hơn nhiều so với hoán đổi dữ liệu trên đĩa.

* Bật zswap

```console
echo 1 | sudo tee /sys/module/zswap/parameters/enabled
```

* Cấu hình zswap

```console
echo 20 | sudo tee /sys/module/zswap/parameters/max_pool_percent
echo lz4 | sudo tee /sys/module/zswap/parameters/compressor
echo z3fold | sudo tee /sys/module/zswap/parameters/zpool
```

* Thiết lập vĩnh viễn (thêm vào tham số kernel)

```console
sudo vim /etc/default/grub
````

```
GRUB_CMDLINE_LINUX="zswap.enabled=1 zswap.compressor=lz4 zswap.max_pool_percent=20"
```

```console
sudo update-grub
sudo reboot
```

* Kiểm tra số liệu thống kê zswap
```console
cat /sys/kernel/debug/zswap/*
```

### Giải pháp thay thế: zram (Thiết bị khối nén)
zram tạo ra một thiết bị khối được nén trong RAM. Nó hoạt động hiệu quả hơn zswap đối với một số loại tác vụ.

* Cài đặt các công cụ zram

```console
apt install zram-config  # Debian/Ubuntu
dnf install zram  # RHEL/CentOS
```

* Cấu hình thủ công

```console
modprobe zram
echo lz4 > /sys/block/zram0/comp_algorithm
echo 4G > /sys/block/zram0/disksize
mkswap /dev/zram0
swapon /dev/zram0 -p 10  # độ ưu tiên 10
```

* Xác minh

```console
zramctl
swapon --show
```

* Theo dõi tỷ số nén

```console
cat /sys/block/zram0/mm_stat
```

## Theo dõi mức sử dụng bộ nhớ
### Giám sát bộ nhớ theo thời gian thực
* Giám sát bộ nhớ trên toàn hệ thống

```console
free -h -s 1  # Cập nhật mỗi giây
```

* Mức sử dụng bộ nhớ trên mỗi máy ảo

```bash
virsh list --name | while read vm; do
    echo "=== $vm ==="
    virsh dommemstat $vm 2>/dev/null
done
```

* Theo dõi áp lực bộ nhớ

```console
watch -n 1 'free -h; echo ""; virsh list --name | while read vm; do \
    echo "$vm:"; virsh dommemstat $vm 2>/dev/null | grep -E "actual|rss|usable"; done'
```

* Giám sát bằng virt-top

```console
virt-top
```

Nhấn phím 2 để xem bộ nhớ

### Giám sát bộ nhớ nâng cao
* memory-monitor.sh - Giám sát bộ nhớ toàn diện

```bash
#!/bin/bash

LOG="/var/log/vm-memory.log"

while true; do
  timestamp=$(date '+%Y-%m-%d %H:%M:%S')

  # Bộ nhớ máy chủ
  host_total=$(free -b | awk 'NR==2 {print $2}')
  host_used=$(free -b | awk 'NR==2 {print $3}')
  host_free=$(free -b | awk 'NR==2 {print $4}')
  host_available=$(free -b | awk 'NR==2 {print $7}')

  # Số liệu thống kê KSM
  ksm_sharing=$(cat /sys/kernel/mm/ksm/pages_sharing 2>/dev/null || echo 0)
  ksm_shared=$(cat /sys/kernel/mm/ksm/pages_shared 2>/dev/null || echo 0)
  ksm_saved=$(( (ksm_sharing - ksm_shared) * 4096 ))

  echo "$timestamp | Host: Used=$host_used Free=$host_free Available=$host_available | KSM_Saved=$ksm_saved" >> $LOG

  # Số liệu thống kê theo từng máy ảo
  for vm in $(virsh list --name); do
    vm_mem=$(virsh dommemstat $vm 2>/dev/null | grep "actual" | awk '{print $2}')
    vm_rss=$(virsh dommemstat $vm 2>/dev/null | grep "rss" | awk '{print $2}')
    vm_usable=$(virsh dommemstat $vm 2>/dev/null | grep "usable" | awk '{print $2}')

    echo "$timestamp | VM: $vm | Allocated=$vm_mem RSS=$vm_rss Usable=$vm_usable" >> $LOG
  done

  sleep 60
done
```

### Thiết lập cảnh báo
* memory-alert.sh - Cảnh báo về áp lực bộ nhớ

```bash
#!/bin/bash

THRESHOLD=90  # Cảnh báo nếu mức sử dụng bộ nhớ > 90%
EMAIL="admin@example.com"

check_memory() {
  used=$(free | awk 'NR==2 {print $3}')
  total=$(free | awk 'NR==2 {print $2}')
  percent=$(( used * 100 / total ))

  if [ $percent -gt $THRESHOLD ]; then
    message="WARNING: Memory usage at ${percent}%"
    echo "$message" | mail -s "Memory Alert" $EMAIL
    logger "$message"
  fi
}

# Chạy 5 phút một lần
while true; do
    check_memory
    sleep 300
done
```

## Các chiến lược phân bổ vượt mức an toàn
### Cam kết vượt mức thận trọng (1,2:1)
* An toàn cho môi trường sản xuất
* Máy chủ 64GB → Đã cấp phát 76GB
* Rủi ro thấp, đạt được một số cải thiện về hiệu quả

* Ví dụ về cấu hình:

```
- VM1: 16GB
- VM2: 16GB
- VM3: 16GB
- VM4: 16GB
- VM5: 12GB
- Tổng cộng: 76GB trên máy chủ 64GB
```

### Phân bổ vượt mức ở mức vừa phải (1,5:1)
* Phù hợp với hầu hết các khối lượng công việc
* Host 64GB → Cấp phát 96GB
* Yêu cầu giám sát, KSM, ballooning
* Kích hoạt các công nghệ:
    - KSM (dự kiến tiết kiệm 10-20%)
    - Memory ballooning
    - THP
    - Cảnh báo giám sát
* Ví dụ: 6 máy ảo * 16GB = 96GB trên máy chủ 64GB

### Phân bổ vượt mức mạnh tay (tỷ lệ 2:1 hoặc cao hơn)
* Rủi ro cao, đòi hỏi sự quản lý chặt chẽ
* Máy chủ 64GB → cấp phát hơn 128GB
* Chỉ dành cho các trường hợp cụ thể:
    - Môi trường phát triển/thử nghiệm
    - Các máy ảo (VM) có mức sử dụng bộ nhớ thấp
    - Khối lượng công việc đồng nhất
    - Giám sát chủ động
* Yêu cầu:
    - Đã kích hoạt và tinh chỉnh KSM
    - Đã bật tính năng Memory ballooning
    - Giám sát 24/7
    - Đã cấu hình hệ thống cảnh báo
    - Có quy trình được văn bản hóa để xử lý tình trạng thiếu hụt bộ nhớ (memory pressure)

### Các phương pháp tốt nhất
1. **Hiểu rõ khối lượng công việc của bạn:**
* Theo dõi mức sử dụng bộ nhớ của máy ảo theo thời gian

```bash
for vm in $(virsh list --name); do
  echo "$vm memory usage:"
  virsh dommemstat $vm | grep -E "actual|rss|usable"
done
```

Nhiều máy ảo sử dụng dưới 50% bộ nhớ được cấp phát. Đây là lúc việc cam kết vượt mức phát huy tác dụng.

2. **Bắt đầu một cách thận trọng:**
    * Bắt đầu với tỷ lệ vượt mức phân bổ (overcommit) là 1,2:1
    * Theo dõi trong 1-2 tuần
    * Tăng dần nếu đảm bảo an toàn
    * Tuyệt đối không vượt quá tỷ lệ 2:1 đối với môi trường sản xuất (production)

3. **Bật tất cả các công nghệ:**
* KSM

```console
echo 1 > /sys/kernel/mm/ksm/run
```

* THP

```console
echo always > /sys/kernel/mm/transparent_hugepage/enabled
```

* Ballooning (trong cấu hình máy ảo)

```console
virsh edit vm-name  # Thêm thiết bị balloon
```

* zswap
```console
echo 1 > /sys/module/zswap/parameters/enabled
```

4. Giám sát liên tục:

```
- Thiết lập giám sát tự động
- Cảnh báo khi:
  - Mức sử dụng bộ nhớ > 85%
  - Mức sử dụng Swap > 10%
  - Xảy ra OOM kill
  - Số lượng major fault cao
```

5. Lập kế hoạch tăng trưởng:

```
- Dự phòng tài nguyên cho:
  - Các đợt tăng đột biến nhu cầu bộ nhớ
  - Các máy ảo (VM) mới
  - Cập nhật/bảo trì
  - Các tình huống khẩn cấp

Quy tắc: Không bao giờ cấp phát vượt quá 90% dung lượng, ngay cả khi áp dụng cơ chế overcommit (cấp phát vượt mức).
```

## Khắc phục sự cố bộ nhớ
### Xác định áp lực bộ nhớ
* Kiểm tra các dấu hiệu áp lực bộ nhớ

```console
free -h  # Bộ nhớ khả dụng thấp
```

* Kiểm tra mức sử dụng swap

```console
swapon --show
vmstat 1 10  # Theo dõi các cột si/so (hoán đổi vào/ra)
```

* Các sự kiện OOM

```console
dmesg | grep -i oom
journalctl | grep -i "out of memory"
```

* Các lỗi trang nghiêm trọng trên mỗi máy ảo (cho biết có hiện tượng hoán đổi/swapping)

```console
virsh dommemstat my-vm | grep major_fault
```

* Áp suất hệ thống

```console
cat /proc/pressure/memory
```

### Giảm áp lực bộ nhớ
#### Các hành động khẩn cấp:
1. Tăng mức độ balloon (cấp ít bộ nhớ hơn cho các máy ảo)

```bash
for vm in $(virsh list --name); do
  current=$(virsh dominfo $vm | grep "Used memory" | awk '{print $3}')
  reduced=$(( current * 80 / 100 ))  # Giảm xuống còn 80%
  virsh setmem $vm ${reduced}K --live
done
```

* 2. Di trú các máy ảo sang các máy chủ khác

```console
virsh migrate --live vm-name qemu+ssh://other-host/system
```

3. Tắt các máy ảo không quan trọng

```console
virsh shutdown dev-vm
```

4. Xóa bộ nhớ đệm (giải pháp tạm thời)

```console
sync; echo 3 > /proc/sys/vm/drop_caches
```

**Các giải pháp dài hạn:**

1. Thêm RAM vật lý
2. Giảm tỷ lệ phân bổ vượt mức (overcommit ratio). Di chuyển các máy ảo sang các máy chủ bổ sung
3. Tối ưu hóa việc cấp phát bộ nhớ cho máy ảo. Điều chỉnh quy mô máy ảo phù hợp với mức sử dụng thực tế
4. Tinh chỉnh KSM mạnh mẽ hơn

```console
echo 500 > /sys/kernel/mm/ksm/pages_to_scan
echo 10 > /sys/kernel/mm/ksm/sleep_millisecs
```

5. Bật huge pages

```console
echo 4096 > /proc/sys/vm/nr_hugepages
```

### Xử lý các tình huống OOM (Hết bộ nhớ)
* Kiểm tra nhật ký OOM killer

```console
dmesg | grep -i "killed process"
journalctl -k | grep -i oom
```

* Xác định các tiến trình bị chấm dứt do OOM

```console
grep -i "killed process" /var/log/kern.log
```

* Cấu hình độ ưu tiên OOM (cho mỗi máy ảo). Điểm số càng thấp = nguy cơ bị giết càng thấp.

```console
ps aux | grep qemu | grep vm-name
# Lấy PID

echo -17 > /proc/<PID>/oom_score_adj
# Phạm vi: -1000 (không bao giờ giết) đến 1000 (giết trước)
```

# Thiết lập tính bền vững thông qua XML của máy ảo
```console
virsh edit my-vm
```

* Việc này đòi hỏi các tập lệnh khởi động tùy chỉnh. Tạo tệp /etc/libvirt/hooks/qemu:

```bash
#!/bin/bash
if [ "$1" = "my-vm" ] && [ "$2" = "started" ]; then
  pid=$(pgrep -f "qemu.*my-vm")
  echo -500 > /proc/$pid/oom_score_adj
fi
```

### Suy giảm hiệu năng
* Các triệu chứng của vấn đề phân bổ bộ nhớ vượt mức (memory overcommit):
    - Hiệu năng máy ảo (VM) chậm
    - Thời gian chờ I/O cao
    - Ứng dụng bị quá thời gian chờ (timeout)
    - Thời gian phản hồi không ổn định

#### Chẩn đoán:
1. Kiểm tra mức sử dụng swap

```console
free -h
vmstat 1 10
```

2. Kiểm tra các lỗi nghiêm trọng

```console
virsh dommemstat vm-name | grep major_fault
# Cao và đang tăng = hoán đổi
```

3. Kiểm tra tác động của KSM

```console
top | grep ksmd
# Mức sử dụng CPU cao có thể cho thấy KSM đang hoạt động quá mức.
```

4. Kiểm tra tình trạng tắc nghẽn do áp lực bộ nhớ

```console
cat /proc/pressure/memory
```

* Các giải pháp:
    - Giảm mức độ cấp phát vượt mức (overcommit)
    - Bổ sung RAM
    - Tối ưu hóa ứng dụng
    - Di chuyển máy ảo (VM)

## Các script giám sát
### Bảng điều khiển giám sát toàn diện
```bash
#!/bin/bash
# vm-memory-dashboard.sh

while true; do
  clear
  echo "================================"
  echo "   VM Memory Dashboard"
  echo "   $(date)"
  echo "================================"
  echo ""

  # Bộ nhớ máy chủ
  echo "HOST MEMORY:"
  free -h | grep -E "Mem|Swap"
  echo ""

  # Số liệu thống kê KSM
  if [ -f /sys/kernel/mm/ksm/pages_sharing ]; then
    sharing=$(cat /sys/kernel/mm/ksm/pages_sharing)
    shared=$(cat /sys/kernel/mm/ksm/pages_shared)
    saved=$(( (sharing - shared) * 4 / 1024 ))
    echo "KSM: Saved ${saved} MB"
    echo ""
  fi

  # Tỷ lệ vượt mức cam kết
  host_mem=$(free -b | awk 'NR==2 {print $2}')
  total_alloc=0
  echo "VMs:"
  echo "Name                  Allocated    RSS         Usage%"
  echo "--------------------------------------------------------"

  for vm in $(virsh list --name); do
    alloc=$(virsh dominfo $vm 2>/dev/null | grep "Max memory" | awk '{print $3}')
    rss=$(virsh dommemstat $vm 2>/dev/null | grep "rss" | awk '{print $2}')

    if [ -n "$alloc" ] && [ -n "$rss" ]; then
      alloc_mb=$(( alloc / 1024 ))
      rss_mb=$(( rss / 1024 ))
      usage=$(( rss * 100 / alloc ))
      printf "%-20s %7d MB   %7d MB   %3d%%\n" "$vm" "$alloc_mb" "$rss_mb" "$usage"
      total_alloc=$(( total_alloc + alloc ))
    fi
  done

  echo ""
  overcommit=$(echo "scale=2; $total_alloc / $host_mem" | bc)
  echo "Total Allocated: $(( total_alloc / 1024 / 1024 )) GB"
  echo "Overcommit Ratio: ${overcommit}:1"

  sleep 5
done
```

## Kết luận
Memory overcommit (phân bổ bộ nhớ vượt mức) là một kỹ thuật mạnh mẽ giúp tối đa hóa hiệu quả của cơ sở hạ tầng ảo hóa, nhưng nó đòi hỏi phải có sự lập kế hoạch, triển khai và giám sát kỹ lưỡng. Bằng cách tận dụng các công nghệ như memory ballooning, KSM và transparent huge pages, bạn có thể vận hành an toàn nhiều máy ảo (VM) hơn trên mỗi máy chủ vật lý (host) mà vẫn duy trì được hiệu năng ở mức chấp nhận được.

Những điểm chính cần lưu ý:

* Bắt đầu với tỷ lệ vượt mức phân bổ (overcommit ratio) ở mức thận trọng (1,2:1)
* Kích hoạt tất cả các công nghệ tối ưu hóa bộ nhớ (KSM, THP, ballooning)
* Liên tục theo dõi các dấu hiệu cho thấy bộ nhớ đang bị quá tải (memory pressure)
* Hiểu rõ đặc điểm khối lượng công việc của bạn
* Lập kế hoạch cho sự tăng trưởng và các thời điểm nhu cầu sử dụng đạt đỉnh
* Tuyệt đối không vượt quá tỷ lệ 2:1 đối với môi trường vận hành thực tế (production)
* Chuẩn bị sẵn quy trình xử lý cho các tình huống bộ nhớ bị quá tải

Các chiến lược phân bổ bộ nhớ vượt mức thành công cân bằng giữa hiệu quả và độ tin cậy, sử dụng giám sát và tự động hóa để đảm bảo rằng các hạn chế về tài nguyên không bao giờ ảnh hưởng đến khối lượng công việc sản xuất. Với cấu hình và sự giám sát phù hợp, phân bổ bộ nhớ vượt mức có thể giảm đáng kể chi phí cơ sở hạ tầng trong khi vẫn duy trì hiệu suất và tính khả dụng mà ứng dụng của bạn yêu cầu.

Hãy nhớ rằng phân bổ vượt mức là một sự đánh đổi: mật độ máy ảo cao hơn so với độ phức tạp và yêu cầu giám sát tăng lên. Chiến lược tối ưu phụ thuộc vào khối lượng công việc cụ thể, khả năng chấp nhận rủi ro và khả năng vận hành của bạn. Khi được thực hiện đúng cách, phân bổ bộ nhớ vượt mức là nền tảng của cơ sở hạ tầng ảo hóa hiệu quả và tiết kiệm chi phí.
