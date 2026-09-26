# Hướng dẫn toàn tập về cài đặt KVM/QEMU trên Linux
## Giới thiệu
KVM (Kernel-based Virtual Machine) và QEMU (Quick Emulator) tạo nên nền tảng cho công nghệ ảo hóa Linux hiện đại, hỗ trợ mọi thứ từ môi trường phát triển cá nhân đến cơ sở hạ tầng đám mây doanh nghiệp. Sự kết hợp giữa chúng mang lại khả năng ảo hóa có hỗ trợ phần cứng, đảm bảo hiệu suất gần như tương đương với hệ thống chạy trực tiếp trên phần cứng (native) cùng mức tiêu tốn tài nguyên hệ thống tối thiểu.

Hướng dẫn toàn diện này sẽ đưa bạn qua quy trình cài đặt và cấu hình KVM/QEMU trên Linux từ A đến Z, bao gồm mọi khâu từ kiểm tra phần cứng đến các kỹ thuật tối ưu hóa nâng cao. Cho dù bạn đang thiết lập một phòng lab tại gia, xây dựng môi trường phát triển hay triển khai cơ sở hạ tầng ảo hóa thực tế (production), tài liệu này đều cung cấp các hướng dẫn chi tiết và các phương pháp thực tiễn tốt nhất mà bạn cần.

KVM là một module nhân (kernel module) của Linux giúp biến hệ thống Linux của bạn thành một hypervisor loại 1 (type-1 hypervisor), trong khi QEMU cung cấp các tính năng mô phỏng thiết bị và quản lý. Khi kết hợp với libvirt để quản lý và virt-manager để cung cấp giao diện đồ họa, bộ công cụ này tạo nên một giải pháp ảo hóa mã nguồn mở mạnh mẽ, có khả năng cạnh tranh sòng phẳng với các giải pháp thương mại khác.

Sau khi hoàn thành hướng dẫn này, bạn sẽ sở hữu một môi trường ảo hóa KVM/QEMU hoạt động hoàn chỉnh với hiệu suất được tối ưu hóa, cấu hình mạng và quản lý lưu trữ chuẩn xác, cùng đầy đủ các công cụ cần thiết để tạo và quản lý máy ảo một cách hiệu quả.

## Tìm hiểu về KVM và QEMU
### KVM là gì?
KVM (Kernel-based Virtual Machine) là một cơ sở hạ tầng ảo hóa được tích hợp sẵn trong nhân Linux. Nó biến Linux thành một hypervisor loại 1 (bare-metal) bằng cách tận dụng các phần mở rộng ảo hóa phần cứng có sẵn trên các bộ vi xử lý hiện đại (Intel VT-x hoặc AMD-V).

**Các đặc điểm chính của KVM:**  
* Được tích hợp vào nhân Linux từ phiên bản 2.6.20
* Yêu cầu CPU hỗ trợ ảo hóa (Intel VT-x hoặc AMD-V)
* Mang lại hiệu năng gần như tương đương với máy thật nhờ tăng tốc phần cứng
* Mỗi máy ảo (VM) chạy như một tiến trình Linux thông thường
* Cách ly hoàn toàn giữa các máy ảo
* Hỗ trợ ảo hóa bộ nhớ và I/O

### QEMU là gì?
QEMU (Quick Emulator) là một trình mô phỏng máy và công cụ ảo hóa đa năng, cung cấp:

* Mô phỏng thiết bị phần cứng (CPU, ổ đĩa, mạng, đồ họa)
* Thành phần chạy ở không gian người dùng (userspace) cho KVM
* Hỗ trợ nhiều kiến trúc
* Quản lý thiết bị và xử lý nhập/xuất (I/O)

**Cách KVM và QEMU phối hợp hoạt động:**  
```
VM khách → QEMU (mô phỏng thiết bị) → KVM (mô-đun nhân) → Phần cứng vật lý
```

QEMU cung cấp các công cụ ở không gian người dùng, trong khi KVM đảm nhiệm việc ảo hóa thực tế tại không gian nhân, mang lại hiệu năng tối ưu.

### Tổng quan về kiến trúc
```
┌─────────────────────────────────────────┐
│         Virtual Machine (Guest OS)      │
├─────────────────────────────────────────┤
│  Virtual CPU │ Virtual RAM │ Virtual I/O│
├─────────────────────────────────────────┤
│             QEMU Process                │
│    (Device Emulation & Management)      │
├─────────────────────────────────────────┤
│         KVM Kernel Module               │
│       (Hardware Virtualization)         │
├─────────────────────────────────────────┤
│            Linux Kernel                 │
├─────────────────────────────────────────┤
│       Physical Hardware (CPU/RAM/I/O)   │
└─────────────────────────────────────────┘
```

## Các điều kiện tiên quyết và yêu cầu về phần cứng
### Yêu cầu về CPU
CPU của bạn phải hỗ trợ các phần mở rộng ảo hóa phần cứng:

**Đối với bộ xử lý Intel:**  
* Công nghệ ảo hóa Intel VT-x (Intel Virtualization Technology)
* Có sẵn trong hầu hết các CPU Intel từ năm 2006 trở đi
* Tên model: Core i3/i5/i7/i9, Xeon

**Đối với bộ vi xử lý AMD:**  
* AMD-V (AMD Virtualization)
* Có sẵn trên hầu hết các CPU AMD từ năm 2006 trở đi
* Tên dòng sản phẩm: Ryzen, EPYC, Opteron (có hậu tố "V")

### Kiểm tra hỗ trợ ảo hóa của CPU
```console
# Kiểm tra xem CPU có hỗ trợ ảo hóa hay không.
egrep -c '(vmx|svm)' /proc/cpuinfo

# Diễn giải kết quả đầu ra:
# 0 = Không hỗ trợ ảo hóa (hoặc đã bị vô hiệu hóa trong BIOS)
# >0 = Số lượng lõi hỗ trợ ảo hóa

# Thông tin chi tiết
lscpu | grep Virtualization

# Thông số Intel: Virtualization: VT-x
# Thông số Intel: Virtualization: AMD-V
```

Nếu lệnh trả về giá trị 0, bạn cần:

1. Xác minh rằng CPU của bạn hỗ trợ ảo hóa (kiểm tra thông số kỹ thuật từ nhà sản xuất)
2. Bật tính năng ảo hóa trong cài đặt BIOS/UEFI (thường nằm trong mục "Advanced" hoặc "CPU Configuration")

### Bật tính năng ảo hóa trong BIOS/UEFI
Nếu tính năng ảo hóa bị vô hiệu hóa: Khởi động lại và truy cập BIOS/UEFI (thường là phím DEL, F2, F10 hoặc F12 trong quá trình khởi động).

Hãy tìm các cài đặt sau:  
* Intel: "Intel Virtualization Technology", "Intel VT-x", "Virtualization Extensions"
* AMD: "AMD-V", "SVM Mode", "Secure Virtual Machine"

Bật cài đặt và lưu các thay đổi.

### Yêu cầu hệ thống
**Yêu cầu tối thiểu:**  
* CPU: Bộ xử lý 64-bit có hỗ trợ ảo hóa
* RAM: 4GB (hệ điều hành máy chủ cần tối thiểu 2GB, phần còn lại dành cho các máy ảo)
* Lưu trữ: 20GB dung lượng trống
* Kernel Linux: 2.6.20 hoặc mới hơn

**Khuyến nghị cho môi trường sản xuất:**  
* CPU: Bộ xử lý đa nhân (từ 4 nhân trở lên)
* RAM: 16GB trở lên (cho phép chạy nhiều máy ảo)
* Lưu trữ: SSD với dung lượng trống từ 100GB trở lên
* Mạng: Gigabit Ethernet

### Kiểm tra hỗ trợ KVM của kernel
```console
# Kiểm tra xem các mô-đun KVM có sẵn hay không
ls -l /dev/kvm

# Kết quả mong đợi:
# crw-rw----+ 1 root kvm 10, 232 Jan 11 10:00 /dev/kvm

# Nếu /dev/kvm không tồn tại, hãy kiểm tra xem các mô-đun có sẵn hay không.
modinfo kvm
modinfo kvm_intel  # Cho Intel
modinfo kvm_amd    # Cho AMD
```

## Cài đặt trên rocky/Debian
### Bước 1: Cập nhật hệ thống
```console
# Cập nhật danh sách gói
sudo apt update

# Nâng cấp các gói hiện có
sudo apt upgrade -y
```

### Bước 2: Cài đặt KVM và các gói liên quan
```console
# Cài đặt KVM, QEMU và các công cụ cần thiết
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils

# Cài đặt các công cụ quản lý
sudo apt install -y virt-manager virt-viewer virtinst

# Cài đặt thêm các tiện ích
sudo apt install -y libguestfs-tools libosinfo-bin
```

Giải thích về gói dịch vụ:  
* `qemu-kvm`: Các tệp thực thi QEMU có hỗ trợ KVM
* `libvirt-daemon-system`: Tiến trình nền Libvirt để quản lý máy ảo (VM)
* `libvirt-clients`: Các công cụ dòng lệnh (virsh)
* `bridge-utils`: Các tiện ích cấu hình cầu nối mạng (network bridge)
* `virt-manager`: Giao diện đồ họa quản lý máy ảo
* `virt-viewer`: Công cụ xem giao diện điều khiển máy ảo (console viewer)
* `virtinst`: Các công cụ cài đặt máy ảo (virt-install)
* `libguestfs-tools`: Các công cụ thao tác hệ thống tập tin máy ảo khách (virt-customize, virt-sysprep)
* `libosinfo-bin`: Cơ sở dữ liệu thông tin hệ điều hành

### Bước 3: Xác minh việc cài đặt
```console
# Kiểm tra xem các mô-đun KVM đã được nạp chưa
lsmod | grep kvm

# Kết quả đầu ra dự kiến cho Intel:
# kvm_intel             311296  0
# kvm                   872448  1 kvm_intel

# Kết quả đầu ra dự kiến cho AMD:
# kvm_amd               131072  0
# kvm                   872448  1 kvm_amd

# Xác minh libvirt đang chạy
sudo systemctl status libvirtd

# Cho phép libvirt khởi động cùng hệ thống
sudo systemctl enable libvirtd
```

### Bước 4: Thêm người dùng vào các nhóm bắt buộc
```console
# Thêm người dùng của bạn vào các nhóm libvirt và kvm.
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER

# Xác minh tư cách thành viên nhóm
groups $USER

# Đăng xuất và đăng nhập lại để các thay đổi có hiệu lực.
# Hoặc sử dụng: newgrp libvirt
```

### Bước 5: Xác minh khả năng ảo hóa
```console
# Kiểm tra khả năng ảo hóa
virt-host-validate

# Kết quả đầu ra mong đợi phải hiển thị tất cả là PASS:
# QEMU: Checking for hardware virtualization                 : PASS
# QEMU: Checking if device /dev/kvm exists                   : PASS
# QEMU: Checking if device /dev/kvm is accessible            : PASS
# QEMU: Checking if device /dev/vhost-net exists             : PASS
# QEMU: Checking if device /dev/net/tun exists               : PASS
# LXC: Checking for Linux >= 2.6.26                          : PASS
```

## Cài đặt trên CentOS/RHEL/Rocky Linux
### Bước 1: Cập nhật hệ thống
```console
# Cập nhật các gói hệ thống
sudo dnf update -y
```

### Bước 2: Cài đặt KVM và các gói liên quan
```console
# Cài đặt nhóm ảo hóa (khuyên dùng)
sudo dnf groupinstall "Virtualization Host" -y

# Hoặc cài đặt các gói riêng lẻ
sudo dnf install -y qemu-kvm libvirt virt-install virt-manager virt-viewer

# Cài đặt thêm công cụ
sudo dnf install -y libguestfs-tools libvirt-client
```

### Bước 3: Khởi động và kích hoạt dịch vụ Libvirt
```console
# Khởi động dịch vụ libvirtd
sudo systemctl start libvirtd

# Cho phép libvirtd khởi động cùng hệ thống
sudo systemctl enable libvirtd

# Xác minh trạng thái dịch vụ
sudo systemctl status libvirtd
```

### Bước 4: Cấu hình tường lửa
```console
# Cho phép các dịch vụ libvirt đi qua tường lửa
sudo firewall-cmd --permanent --add-service=libvirt
sudo firewall-cmd --reload

# Để truy cập qua VNC (nếu cần)
sudo firewall-cmd --permanent --add-port=5900-5999/tcp
sudo firewall-cmd --reload
```

### Bước 5: Thêm người dùng vào các nhóm
```console
# Thêm người dùng vào nhóm libvirt
sudo usermod -aG libvirt $USER

# Xác minh
groups $USER

# Khởi động lại hoặc đăng xuất rồi đăng nhập lại để các thay đổi có hiệu lực.
```

### Bước 6: Xác minh việc cài đặt
```console
# Kiểm tra mô-đun KVM
lsmod | grep kvm

# Xác thực cấu hình ảo hóa
virt-host-validate
```

## Cài đặt trên Arch Linux
### Bước 1: Cập nhật hệ thống
```console
# Cập nhật hệ thống
sudo pacman -Syu
```

### Bước 2: Cài đặt các gói KVM
```console
# Cài đặt KVM và QEMU
sudo pacman -S qemu-full virt-manager virt-viewer libvirt bridge-utils dnsmasq

# Cài đặt thêm công cụ
sudo pacman -S ebtables iptables-nft
```

### Bước 3: Kích hoạt dịch vụ Libvirt
```console
# Khởi động và kích hoạt libvirtd
sudo systemctl enable libvirtd.service
sudo systemctl start libvirtd.service

# Bật mạng mặc định
sudo virsh net-autostart default
sudo virsh net-start default
```

### Bước 4: Cấu hình quyền người dùng
```console
# Thêm người dùng vào nhóm libvirt
sudo usermod -aG libvirt $USER

# Chỉnh sửa cấu hình libvirt (tùy chọn)
sudo nvim /etc/libvirt/libvirtd.conf

# Bỏ chú thích các dòng này:
# unix_sock_group = "libvirt"
# unix_sock_rw_perms = "0770"
```

### Bước 5: Khởi động lại Libvirt
```console
# Khởi động lại dịch vụ libvirt
sudo systemctl restart libvirtd.service

# Xác minh
sudo systemctl status libvirtd.service
```

## Cấu hình sau khi cài đặt
### Cấu hình mạng mặc định
```console
# Kiểm tra trạng thái mạng mặc định
sudo virsh net-list --all

# Nếu mạng mặc định không hoạt động, hãy khởi động nó.
sudo virsh net-start default

# Thiết lập để tự động khởi động.
sudo virsh net-autostart default

# Xem cấu hình mạng
sudo virsh net-dumpxml default
```

**Cấu hình XML mạng mặc định:**  
```xml
<network>
  <name>default</name>
  <bridge name='virbr0'/>
  <forward mode='nat'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>
```

### Cấu hình nhóm lưu trữ
```console
# Liệt kê các nhóm lưu trữ
sudo virsh pool-list --all

# Vị trí nhóm mặc định
ls -la /var/lib/libvirt/images/

# Tạo nhóm lưu trữ tùy chỉnh
sudo virsh pool-define-as mypool dir --target /data/vms

# Build and start the pool
sudo virsh pool-build mypool
sudo virsh pool-start mypool
sudo virsh pool-autostart mypool

# Verify
sudo virsh pool-info mypool
```

### Tối ưu hóa các thiết lập hiệu năng KVM
```console
# Bật ảo hóa lồng nhau (Intel)
sudo modprobe -r kvm_intel
sudo modprobe kvm_intel nested=1

# Làm cho bền vững
echo "options kvm_intel nested=1" | sudo tee /etc/modprobe.d/kvm-intel.conf

# Xác minh tính năng ảo hóa lồng nhau
cat /sys/module/kvm_intel/parameters/nested
# Kết quả đầu ra phải là: Y

# Dành cho AMD
echo "options kvm_amd nested=1" | sudo tee /etc/modprobe.d/kvm-amd.conf
cat /sys/module/kvm_amd/parameters/nested
```

### Cấu hình điều tiết CPU để tối ưu hiệu năng
```console
# Kiểm tra cơ chế điều tiết CPU hiện tại
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Chuyển sang chế độ hiệu năng cao
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Thiết lập vĩnh viễn (dịch vụ systemd)
sudo cat << 'EOF' > /etc/systemd/system/cpu-performance.service
[Unit]
Description=Set CPU governor to performance

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'echo performance | tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor'

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable cpu-performance.service
sudo systemctl start cpu-performance.service
```

### Bật Huge Pages để cải thiện hiệu năng bộ nhớ
```console
# Kiểm tra cấu hình huge pages hiện tại
cat /proc/meminfo | grep Huge

# Tính toán số lượng huge page cần thiết (2 GB cho các máy ảo với tổng dung lượng 8 GB)
# Mỗi trang lớn có kích thước 2MB, do đó 2GB = 1024 trang.

# Cấu hình huge page
echo 1024 | sudo tee /proc/sys/vm/nr_hugepages

# Thiết lập vĩnh viễn
echo "vm.nr_hugepages=1024" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

# Xác minh
cat /proc/meminfo | grep HugePages_Total
```

### Định cấu hình IOMMU để truyền qua PCI
```console
# Bật IOMMU trong GRUB (Intel)
sudo vim /etc/default/grub

# Thêm vào GRUB_CMDLINE_LINUX:
# Dành cho Intel: intel_iommu=on iommu=pt
# Dành cho AMD: amd_iommu=on iommu=pt

# Ví dụ:
# GRUB_CMDLINE_LINUX="intel_iommu=on iommu=pt"

# Cập nhật GRUB
sudo update-grub  # Debian/rocky
sudo grub2-mkconfig -o /boot/grub2/grub.cfg  # RHEL/CentOS

# Khởi động lại
sudo reboot

# Sau khi khởi động lại, xác minh IOMMU.
dmesg | grep -i iommu
```

## Tạo máy ảo đầu tiên của bạn
### Cách 1: Sử dụng virt-install (Dòng lệnh)
Tải xuống tệp ISO rocky (ví dụ)

```console
cd /var/lib/libvirt/images/
sudo wget https://download.rockylinux.org/pub/rocky/10/isos/x86_64/Rocky-10.2-x86_64-minimal.iso
```

Tạo máy ảo bằng virt-install

```console
sudo virt-install \
  --name my-vn \
  --ram 2048 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/my-vn.qcow2,size=20 \
  --os-variant rocky.10 \
  --network network=default \
  --graphics vnc,listen=0.0.0.0 \
  --console pty,target_type=serial \
  --cdrom /var/lib/libvirt/images/Rocky-10.2-x86_64-minimal.iso
```

Giải thích các tham số:  
* `--name`: Tên máy ảo (VM)
* `--ram`: Dung lượng bộ nhớ (tính bằng MB)
* `--vcpus`: Số lượng CPU ảo
* `--disk`: Cấu hình ổ đĩa (dung lượng tính bằng GB)
* `--os-variant`: Loại hệ điều hành (danh sách có sẵn: osinfo-query os)
* `--network`: Cấu hình mạng
* `--graphics`: Loại hiển thị
* `--cdrom`: Đường dẫn đến tệp ISO

### Cách 2: Sử dụng virt-manager (Giao diện đồ họa)
Khởi chạy virt-manager

```console
virt-manager
```

**Các bước thực hiện trên giao diện đồ họa (GUI):**  
1. Nhấn vào "Create a new virtual machine" (Tạo máy ảo mới)
2. Chọn "Local install media (ISO image or CDROM)" (Phương tiện cài đặt cục bộ - tệp ISO hoặc CDROM)
3. Duyệt và chọn tệp ISO của bạn
4. Thiết lập bộ nhớ và CPU
5. Tạo tệp ảnh đĩa (mặc định: định dạng qcow2)
6. Đặt tên cho máy ảo và hoàn tất
7. Máy ảo sẽ tự động khởi động

### Xác minh việc tạo máy ảo
```console
# Liệt kê tất cả các máy ảo
sudo virsh list --all

# Hiển thị thông tin máy ảo
sudo virsh dominfo my-vn

# Kiểm tra trạng thái máy ảo
sudo virsh domstate my-vn
```

## Cấu hình nâng cao
### Cấu hình trình điều khiển Virtio để đạt hiệu suất tốt hơn
Virtio cung cấp các trình điều khiển bán ảo hóa để đạt hiệu suất tốt hơn:

```console
# Tạo máy ảo với trình điều khiển virtio
sudo virt-install \
  --name rocky-virtio \
  --ram 4096 \
  --vcpus 4 \
  --disk path=/var/lib/libvirt/images/rocky-virtio.qcow2,size=30,bus=virtio \
  --network network=default,model=virtio \
  --os-variant rocky.10 \
  --graphics spice \
  --video qxl \
  --cdrom /var/lib/libvirt/images/Rocky-10.2-x86_64-minimal.iso
```

Các thay đổi chính:
* bus=virtio: Sử dụng virtio cho ổ đĩa (hiệu năng tốt hơn)
* model=virtio: Sử dụng virtio cho mạng (hiệu năng tốt hơn)
* graphics spice: Hiệu năng đồ họa tốt hơn
* video qxl: Trình điều khiển video QXL

### Cấu hình ghim CPU
```console
# Kiểm tra cấu trúc liên kết CPU của máy chủ
lscpu

# Ghim CPU của máy ảo vào các CPU vật lý cụ thể của máy chủ.
sudo virsh vcpupin my-vn 0 0
sudo virsh vcpupin my-vn 1 1

# Xác minh việc ghim
sudo virsh vcpupin my-vn --live

# Thiết lập chế độ bền vững (chỉnh sửa XML của máy ảo)
sudo virsh edit my-vn
```

Thêm vào phần `<vcpu>`:

```xml
<vcpu placement='static' cpuset='0-3'>4</vcpu>
<cputune>
  <vcpupin vcpu='0' cpuset='0'/>
  <vcpupin vcpu='1' cpuset='1'/>
  <vcpupin vcpu='2' cpuset='2'/>
  <vcpupin vcpu='3' cpuset='3'/>
</cputune>
```

### Cấu hình cấu trúc liên kết NUMA
Kiểm tra bố cục NUMA của máy chủ

```console
numactl --hardware
```

Tạo máy ảo có hỗ trợ NUMA

```console
sudo virsh edit my-vn
```

Thêm cấu hình NUMA:

```xml
<cpu mode='host-passthrough'>
  <topology sockets='1' cores='2' threads='2'/>
  <numa>
    <cell id='0' cpus='0-1' memory='2097152' unit='KiB'/>
    <cell id='1' cpus='2-3' memory='2097152' unit='KiB'/>
  </numa>
</cpu>
```

### Bật KSM (Kernel Same-page Merging)
KSM giảm mức sử dụng bộ nhớ bằng cách chia sẻ các trang giống hệt nhau.

```console
# Bật KSM
echo 1 | sudo tee /sys/kernel/mm/ksm/run

# Cấu hình các tham số quét
echo 100 | sudo tee /sys/kernel/mm/ksm/pages_to_scan
echo 20 | sudo tee /sys/kernel/mm/ksm/sleep_millisecs

# Làm cho bền vững
echo "w /sys/kernel/mm/ksm/run - - - - 1" | sudo tee /etc/tmpfiles.d/ksm.conf
echo "w /sys/kernel/mm/ksm/pages_to_scan - - - - 100" | sudo tee -a /etc/tmpfiles.d/ksm.conf

# Kiểm tra số liệu thống kê KSM
cat /sys/kernel/mm/ksm/pages_sharing
cat /sys/kernel/mm/ksm/pages_shared
```

## Cấu hình mạng
### Tạo mạng cầu nối
```console
# Cài đặt các tiện ích bridge (nếu chưa được cài đặt)
sudo apt install bridge-utils

# Tạo cấu hình cầu nối
sudo cat << 'EOF' > /etc/netplan/01-netcfg.yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: no
  bridges:
    br0:
      interfaces: [ens18]
      dhcp4: yes
EOF

# Áp dụng cấu hình
sudo netplan apply

# Xác minh cầu nối
ip addr show br0
brctl show
```

### Tạo mạng cầu nối cho Libvirt
```console
# Tạo XML cho mạng cầu nối
cat << 'EOF' > bridge-net.xml
<network>
  <name>br0</name>
  <forward mode='bridge'/>
  <bridge name='br0'/>
</network>
EOF

# Định nghĩa và khởi động mạng
sudo virsh net-define bridge-net.xml
sudo virsh net-start br0
sudo virsh net-autostart br0

# Xác minh
sudo virsh net-list --all
```

### Tạo mạng cô lập
```console
# Tạo mạng cô lập cho các máy ảo
cat << 'EOF' > isolated-net.xml
<network>
  <name>isolated</name>
  <ip address='10.10.10.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.10.10.2' end='10.10.10.254'/>
    </dhcp>
  </ip>
</network>
EOF

# Xác định và bắt đầu
sudo virsh net-define isolated-net.xml
sudo virsh net-start isolated
sudo virsh net-autostart isolated
```

## Tinh chỉnh hiệu năng
### Tối ưu hóa I/O đĩa
Sử dụng virtio-scsi để có hiệu năng tốt hơn.

```console
sudo virt-install \
  --disk path=/var/lib/libvirt/images/vm.qcow2,bus=scsi,cache=none,io=native
```


Hoặc chỉnh sửa máy ảo hiện có

```console
sudo virsh edit vm-name
```

Thay đổi cấu hình ổ đĩa:

```xml
<disk type='file' device='disk'>
  <driver name='qemu' type='qcow2' cache='none' io='native'/>
  <source file='/var/lib/libvirt/images/vm.qcow2'/>
  <target dev='sda' bus='scsi'/>
</disk>
```

### Tối ưu hóa bộ lập lịch I/O
```console
# Kiểm tra bộ lập lịch hiện tại
cat /sys/block/sda/queue/scheduler

# Đặt thành "none" cho NVMe (tốt nhất cho SSD)
echo none | sudo tee /sys/block/nvme0n1/queue/scheduler

# Thiết lập thành mq-deadline cho các ổ SSD SATA.
echo mq-deadline | sudo tee /sys/block/sda/queue/scheduler

# Làm cho bền vững
cat << 'EOF' | sudo tee /etc/udev/rules.d/60-scheduler.rules
# Set scheduler for NVMe
ACTION=="add|change", KERNEL=="nvme[0-9]n[0-9]", ATTR{queue/scheduler}="none"
# Set scheduler for SSDs
ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="mq-deadline"
EOF
```

### Tinh chỉnh hiệu năng mạng
Bật virtio-net đa hàng đợi

```console
sudo virsh edit vm-name
```

Chỉnh sửa giao diện mạng:

```xml
<interface type='network'>
  <source network='default'/>
  <model type='virtio'/>
  <driver name='vhost' queues='4'/>
</interface>
```

Bên trong máy ảo khách

```console
sudo ethtool -L eth0 combined 4
```

## Giám sát và Quản lý
### Theo dõi hiệu năng máy ảo
```console
# Mức sử dụng tài nguyên máy ảo
sudo virt-top

# Số liệu thống kê chi tiết về máy ảo
sudo virsh domstats my-vn

# Số liệu thống kê CPU
sudo virsh cpu-stats my-vn

# Số liệu thống kê bộ nhớ
sudo virsh dommemstat my-vn

# Số liệu thống kê thiết bị khối
sudo virsh domblkstat my-vn vda

# Số liệu thống kê mạng
sudo virsh domifstat my-vn vnet0
```

### Quản lý vòng đời máy ảo
```console
# Khởi động máy ảo
sudo virsh start my-vn

# Tắt máy ảo một cách an toàn
sudo virsh shutdown my-vn

# Buộc tắt nguồn
sudo virsh destroy my-vn

# Khởi động lại máy ảo
sudo virsh reboot my-vn

# Tạm dừng máy ảo
sudo virsh suspend my-vn

# Tiếp tục máy ảo
sudo virsh resume my-vn

# Tự động khởi động máy ảo khi máy chủ khởi động
sudo virsh autostart my-vn

# Tắt tự động khởi động
sudo virsh autostart --disable my-vn
```

### Quản lý bản chụp nhanh
```console
# Tạo bản chụp nhanh
sudo virsh snapshot-create-as my-vn \
  --name "snapshot1" \
  --description "Clean installation"

# Liệt kê các bản chụp nhanh
sudo virsh snapshot-list my-vn

# Khôi phục về bản chụp nhanh
sudo virsh snapshot-revert my-vn snapshot1

# Xóa bản chụp nhanh
sudo virsh snapshot-delete my-vn snapshot1
```

## Sao lưu và nhân bản
### Nhân bản máy ảo
```console
# Nhân bản máy ảo (khi đã tắt nguồn)
sudo virt-clone \
  --original my-vn \
  --name my-vn-clone \
  --file /var/lib/libvirt/images/my-vn-clone.qcow2

# Xác minh bản sao
sudo virsh list --all
```

### Sao lưu đĩa máy ảo
```console
# Sao lưu tệp ảnh đĩa máy ảo
sudo cp /var/lib/libvirt/images/my-vn.qcow2 \
       /backup/my-vn-$(date +%Y%m%d).qcow2

# Nén bản sao lưu
sudo qemu-img convert -O qcow2 -c \
  /var/lib/libvirt/images/my-vn.qcow2 \
  /backup/my-vn-$(date +%Y%m%d).qcow2

# Sao lưu cấu hình máy ảo
sudo virsh dumpxml my-vn > /backup/my-vn.xml
```

### Xuất và nhập máy ảo
```console
# Xuất máy ảo
sudo virsh dumpxml my-vn > my-vn.xml
sudo cp /var/lib/libvirt/images/my-vn.qcow2 /export/

# Nhập máy ảo trên máy chủ khác
sudo cp /export/my-vn.qcow2 /var/lib/libvirt/images/
sudo virsh define my-vn.xml
sudo virsh start my-vn
```

## Khắc phục sự cố
### Các vấn đề thường gặp và giải pháp
**Vấn đề: Mô-đun KVM không tải được**

```console
# Kiểm tra xem tính năng ảo hóa đã được bật hay chưa.
egrep -c '(vmx|svm)' /proc/cpuinfo

# Nếu là 0, hãy kích hoạt trong BIOS.

# Kiểm tra xung đột
dmesg | grep kvm
lsmod | grep kvm

# Tải lại mô-đun
sudo modprobe -r kvm_intel  # hoặc kvm_amd
sudo modprobe kvm_intel
```

**Lỗi: Không được phép truy cập vào /dev/kvm**

```console
# Kiểm tra quyền
ls -l /dev/kvm

# Thêm người dùng vào nhóm kvm
sudo usermod -aG kvm $USER

# Khởi động lại phiên
newgrp kvm
```

**Sự cố: Mạng mặc định không khởi động**

```console
# Kiểm tra trạng thái mạng
sudo virsh net-list --all

# Khởi động mạng
sudo virsh net-start default

# Kiểm tra lỗi
sudo journalctl -u libvirtd

# Tạo lại mạng mặc định
sudo virsh net-destroy default
sudo virsh net-undefine default

# Định nghĩa lại
cat << 'EOF' > default-net.xml
<network>
  <name>default</name>
  <bridge name='virbr0'/>
  <forward mode='nat'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>
EOF

sudo virsh net-define default-net.xml
sudo virsh net-start default
sudo virsh net-autostart default
```

**Vấn đề: Hiệu năng máy ảo kém**

```console
# Bật trình điều khiển virtio
sudo virsh edit vm-name
# Thay đổi bus='ide' thành bus='virtio'
# Thay đổi model='e1000' thành model='virtio'

# Cho phép truyền trực tiếp CPU máy chủ
# Thêm: <cpu mode='host-passthrough'/>

# Phân bổ thêm nguồn lực
sudo virsh setmem vm-name 4G --config
sudo virsh setvcpus vm-name 4 --config

# Kiểm tra bộ lập lịch I/O
cat /sys/block/sda/queue/scheduler
```

**Sự cố: Không thể kết nối với bảng điều khiển máy ảo (VM console)**

```console
# Kiểm tra xem máy ảo có đang chạy hay không
sudo virsh list

# Hãy thử dùng console nối tiếp (serial console).
sudo virsh console vm-name

# Kiểm tra cấu hình VNC
sudo virsh vncdisplay vm-name

# Kết nối bằng virt-viewer
virt-viewer vm-name
```

## Các biện pháp bảo mật tốt nhất
### Bảo mật quyền truy cập Libvirt
```console
# Cấu hình xác thực libvirt
sudo vim /etc/libvirt/libvirtd.conf

# Bỏ chú thích và thiết lập:
# unix_sock_group = "libvirt"
# unix_sock_ro_perms = "0770"
# unix_sock_rw_perms = "0770"
# auth_unix_ro = "none"
# auth_unix_rw = "none"

# Khởi động lại libvirtd
sudo systemctl restart libvirtd
```

### Bật SELinux/AppArmor cho các máy ảo
```console
# Ubuntu/Debian (AppArmor)
sudo apt install apparmor-utils
sudo aa-enforce /etc/apparmor.d/usr.sbin.libvirtd

# CentOS/RHEL (SELinux)
sudo setsebool -P virt_use_nfs 1
sudo setsebool -P virt_use_samba 1

# Kiểm tra ngữ cảnh SELinux
ls -Z /var/lib/libvirt/images/
```

### Cô lập mạng máy ảo
```console
# Tạo mạng cô lập không sử dụng NAT hoặc chuyển tiếp.
cat << 'EOF' > secure-net.xml
<network>
  <name>secure</name>
  <bridge name='virbr-secure'/>
  <ip address='172.16.0.1' netmask='255.255.255.0'/>
</network>
EOF

sudo virsh net-define secure-net.xml
sudo virsh net-start secure
```

## Kết luận
Giờ đây, bạn đã cài đặt và cấu hình thành công một môi trường ảo hóa KVM/QEMU hoàn chỉnh trên hệ thống Linux của mình. Bộ công cụ ảo hóa mã nguồn mở mạnh mẽ này mang lại các tính năng cấp doanh nghiệp cùng hiệu năng gần như tương đương với hệ thống chạy trực tiếp trên phần cứng (native), khiến nó trở thành lựa chọn phù hợp cho mọi nhu cầu, từ môi trường phát triển đến các hệ thống vận hành thực tế (production).

Những điểm chính cần lưu ý từ hướng dẫn này:  
* KVM tận dụng công nghệ ảo hóa phần cứng để đạt hiệu suất tối ưu
* Việc cấu hình đúng CPU, bộ nhớ và I/O đóng vai trò then chốt đối với hiệu suất
* Các trình điều khiển Virtio mang lại hiệu suất tốt nhất nhờ cơ chế ảo hóa bán phần (paravirtualization)
* Libvirt cung cấp khả năng quản lý mạnh mẽ thông qua virsh và virt-manager
* Các cầu nối mạng (network bridge) và nhóm lưu trữ (storage pool) mang lại các tùy chọn cơ sở hạ tầng linh hoạt

Khi tiếp tục làm việc với KVM/QEMU, hãy khám phá các chủ đề nâng cao như di chuyển máy ảo trực tiếp (live migration), chuyển tiếp GPU (GPU passthrough) và cụm máy chủ có tính sẵn sàng cao (high-availability clustering) để khai thác tối đa các tính năng của hệ thống. Công nghệ ảo hóa luôn không ngừng phát triển, vì vậy hãy thường xuyên cập nhật các phiên bản kernel và QEMU mới nhất để đảm bảo hiệu suất và tính bảo mật tối ưu.

Hãy nhớ thường xuyên sao lưu máy ảo, theo dõi mức sử dụng tài nguyên và tuân thủ các quy tắc bảo mật tốt nhất để duy trì một môi trường ảo hóa ổn định và an toàn. Với nền tảng đã được thiết lập trong hướng dẫn này, bạn hoàn toàn có đủ khả năng để xây dựng và quản lý cơ sở hạ tầng ảo hóa phức tạp bằng các công nghệ mã nguồn mở.
