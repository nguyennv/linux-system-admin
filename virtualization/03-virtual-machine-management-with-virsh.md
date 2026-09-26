# Quản lý máy ảo bằng virsh: Hướng dẫn toàn tập
## Giới thiệu
Virsh (Virtualization Shell) là giao diện dòng lệnh dùng để quản lý các máy ảo KVM/QEMU thông qua libvirt. Là công cụ mạnh mẽ và linh hoạt nhất để quản trị máy ảo, virsh cung cấp khả năng kiểm soát chi tiết mọi khía cạnh trong vòng đời của máy ảo, từ khâu tạo và cấu hình cho đến giám sát và khắc phục sự cố.

Hướng dẫn toàn diện này sẽ đi sâu vào các tính năng phong phú của virsh, bao gồm mọi thứ từ các thao tác cơ bản với máy ảo đến các kỹ thuật quản lý nâng cao được áp dụng trong môi trường thực tế (production). Dù bạn đang quản lý một máy ảo duy nhất phục vụ phát triển hay điều phối hàng trăm máy ảo trên nhiều máy chủ vật lý (host), việc thành thạo virsh là yếu tố then chốt để quản lý ảo hóa hiệu quả.

Khác với các công cụ có giao diện đồ họa như virt-manager, virsh cung cấp các lệnh hỗ trợ viết kịch bản và tự động hóa, có khả năng tích hợp liền mạch vào quy trình làm việc DevOps, các đường ống CI/CD và các giải pháp "Infrastructure-as-Code" (cơ sở hạ tầng dưới dạng mã). Giao diện nhất quán cùng bộ tính năng toàn diện khiến virsh trở thành lựa chọn hàng đầu của các quản trị viên hệ thống và kỹ sư điện toán đám mây chuyên nghiệp.

Sau khi hoàn thành hướng dẫn này, bạn sẽ thành thạo việc sử dụng virsh để quản lý máy ảo, mạng, kho lưu trữ (storage pool) và bản sao lưu trạng thái (snapshot), qua đó có thể tự động hóa các quy trình ảo hóa phức tạp và tự tin xử lý các sự cố phát sinh.

## Tìm hiểu về Virsh và Libvirt
### Virsh là gì?
Virsh là một công cụ dòng lệnh giao tiếp với tiến trình nền libvirt (libvirtd) để quản lý các nền tảng ảo hóa, bao gồm KVM, QEMU, Xen, VMware và các nền tảng khác. Nó cung cấp:

* Quản lý vòng đời máy ảo (tạo, khởi động, dừng, xóa)
* Phân bổ và điều chỉnh tài nguyên
* Cấu hình mạng và lưu trữ
* Các thao tác tạo bản sao nhanh (snapshot) và sao chép (clone)
* Khả năng di chuyển máy ảo trực tiếp (live migration)
* Giám sát và thu thập số liệu thống kê

### Kiến trúc Libvirt
```
┌───────────────────────────────────────────────┐
│          virsh (Công cụ dòng lệnh)            │
├───────────────────────────────────────────────┤
│         libvirt API (libvirt-client)          │
├───────────────────────────────────────────────┤
│       libvirtd (Tiến trình nền ảo hóa)        │
├───────────────────────────────────────────────┤
│  Trình điều khiển Hypervisor (KVM, QEMU, Xen) │
├───────────────────────────────────────────────┤
│               Phần cứng vật lý                │
└───────────────────────────────────────────────┘
```

### URI kết nối
Virsh kết nối với các hypervisor sử dụng các lược đồ URI:

```console
# Kết nối hệ thống cục bộ (mặc định)
virsh --connect qemu:///system

# Kết nối từ xa qua SSH
virsh --connect qemu+ssh://user@remote-host/system

# Kết nối từ xa qua TLS
virsh --connect qemu+tls://remote-host/system

# Phiên người dùng (không phải root)
virsh --connect qemu:///session

# Kiểm tra kết nối hiện tại
virsh uri
```

## Các lệnh Virsh cơ bản
### Tìm kiếm sự trợ giúp
```console
# Trợ giúp chung
virsh help

# Trợ giúp cho lệnh cụ thể
virsh help list
virsh help start

# Liệt kê tất cả các lệnh theo danh mục
virsh help | less

# Tìm kiếm lệnh
virsh help | grep snapshot
```

### Liệt kê các máy ảo
```console
# Liệt kê các máy ảo đang chạy
virsh list

# Liệt kê tất cả các máy ảo (bao gồm cả những máy đã dừng)
virsh list --all

# Liệt kê các máy ảo kèm thông tin chi tiết bổ sung
virsh list --all --title

# Chỉ liệt kê các máy ảo không hoạt động
virsh list --inactive

# Danh sách kèm thông tin chi tiết
virsh list --all --name
virsh list --all --uuid
```

**Ví dụ kết quả đầu ra:**

```
 Id   Name        State
----------------------------
 1    my-vn       running
 2    centos-vm   running
 -    debian-vm   shut off
```

### Thông tin và chi tiết về máy ảo
```console
# Hiển thị thông tin máy ảo
virsh dominfo my-vn

# Hiển thị ID máy ảo
virsh domid my-vn

# Hiển thị UUID của máy ảo
virsh domuuid my-vn

# Hiển thị trạng thái máy ảo
virsh domstate my-vn

# Hiển thị tất cả siêu dữ liệu của máy ảo
virsh metadata my-vn --uri http://example.com/

# Hiển thị cấu hình máy ảo (XML)
virsh dumpxml my-vn

# Hiển thị cấu hình máy ảo cùng thông tin bảo mật
virsh dumpxml my-vn --security-info

# Lưu cấu hình vào tệp
virsh dumpxml my-vn > my-vn.xml
```

## Quản lý vòng đời máy ảo
### Khởi động máy ảo
```console
# Khởi động máy ảo
virsh start my-vn

# Khởi động máy ảo và mở bảng điều khiển
virsh start my-vn --console

# Bắt đầu từ trạng thái tạm dừng
virsh start my-vn --paused

# Buộc khởi động (bỏ qua lỗi)
virsh start my-vn --force-boot

# Bắt đầu với thiết bị khởi động cụ thể
virsh start my-vn --boot-device cdrom
```

### Dừng các máy ảo
```console
# Tắt máy an toàn (tín hiệu ACPI)
virsh shutdown my-vn

# Tắt máy với chế độ cụ thể
virsh shutdown my-vn --mode acpi

# Buộc tắt nguồn (ngay lập tức)
virsh destroy my-vn

# Khởi động lại máy ảo
virsh reboot my-vn

# Khởi động lại bằng ACPI
virsh reboot my-vn --mode acpi

# Khởi động lại cứng (giống như nút reset vật lý)
virsh reset my-vn
```

### Tạm dừng và Tiếp tục
```console
# Tạm dừng (tạm ngưng) máy ảo
virsh suspend my-vn

# Tiếp tục chạy máy ảo đang tạm dừng
virsh resume my-vn

# Lưu trạng thái máy ảo vào đĩa (ngủ đông)
virsh save my-vn /var/lib/libvirt/images/my-vn.save

# Khôi phục máy ảo từ trạng thái đã lưu
virsh restore /var/lib/libvirt/images/my-vn.save

# Lưu với XML tùy chỉnh để khôi phục
virsh save my-vn --xml custom.xml /tmp/my-vn.save
```

### Cấu hình Tự động khởi động
```console
# Bật tự động khởi động khi máy chủ khởi động
virsh autostart my-vn

# Tắt tự động khởi động
virsh autostart my-vn --disable

# Kiểm tra trạng thái tự động khởi động
virsh dominfo my-vn | grep Autostart
```

### Xóa máy ảo
```console
# Hủy định nghĩa VM (xóa cấu hình, giữ lại đĩa)
virsh undefine my-vn

# Hủy định nghĩa và xóa tất cả bộ nhớ
virsh undefine my-vn --remove-all-storage

# Hủy định nghĩa với các bản chụp nhanh
virsh undefine my-vn --snapshots-metadata

# Hủy xác định với tính năng lưu được quản lý
virsh undefine my-vn --managed-save

# Loại bỏ hoàn toàn
virsh undefine my-vn \
  --remove-all-storage \
  --snapshots-metadata \
  --managed-save
```

## Tạo và định nghĩa máy ảo
### Tạo máy ảo từ XML
```console
# Tạo tệp XML cấu hình máy ảo
cat > my-vn.xml << 'EOF'
<domain type='kvm'>
  <name>my-vn</name>
  <memory unit='GiB'>4</memory>
  <vcpu placement='static'>2</vcpu>
  <os>
    <type arch='x86_64' machine='pc'>hvm</type>
    <boot dev='hd'/>
  </os>
  <devices>
    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='/var/lib/libvirt/images/my-vn.qcow2'/>
      <target dev='vda' bus='virtio'/>
    </disk>
    <interface type='network'>
      <source network='default'/>
      <model type='virtio'/>
    </interface>
    <graphics type='vnc' port='-1' autoport='yes'/>
    <console type='pty'/>
  </devices>
</domain>
EOF

# Định nghĩa máy ảo từ XML
virsh define my-vn.xml

# Tạo và bắt đầu ngay lập tức
virsh create my-vn.xml

# Xác định nhưng đừng bắt đầu
virsh define my-vn.xml
```

### Sử dụng virt-install với virsh
```console
# Tạo máy ảo bằng virt-install
virt-install \
  --name my-vn \
  --memory 4096 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/my-vn.qcow2,size=20 \
  --os-variant ubuntu22.04 \
  --network network=default,model=virtio \
  --graphics vnc \
  --cdrom /var/lib/libvirt/images/Rocky-10.2-x86_64-minimal.iso \
  --print-xml > my-vn.xml

# Xem xét và chỉnh sửa XML trước khi định nghĩa.
vim my-vn.xml

# Xác định máy ảo
virsh define my-vn.xml
```

## Chỉnh sửa cấu hình máy ảo
### Chỉnh sửa XML tương tác
```console
# Chỉnh sửa cấu hình máy ảo
virsh edit my-vn
```

Thao tác này mở tệp XML của máy ảo trong trình soạn thảo mặc định của bạn. Các thay đổi sẽ được kiểm tra tính hợp lệ trước khi lưu.

### Thay đổi cài đặt máy ảo
```console
# Thay đổi mô tả máy ảo
virsh desc my-vn "Development web server"

# Xem mô tả
virsh desc my-vn

# Thay đổi tiêu đề máy ảo
virsh metadata my-vn \
  --uri http://libvirt.org/title \
  --key title \
  --set "Production Web Server"

# Thiết lập siêu dữ liệu tùy chỉnh
virsh metadata my-vn \
  --uri http://example.com/ \
  --key environment \
  --set "production"
```

### Sửa đổi tài nguyên
```console
# Thay đổi bộ nhớ tối đa (yêu cầu khởi động lại máy ảo)
virsh setmaxmem my-vn 8G --config

# Thay đổi bộ nhớ hiện tại (trực tiếp)
virsh setmem my-vn 4G --live

# Thay đổi bộ nhớ vĩnh viễn
virsh setmem my-vn 4G --config

# Xem cài đặt bộ nhớ
virsh dommemstat my-vn

# Thay đổi số lượng vCPU
virsh setvcpus my-vn 4 --config --maximum
virsh setvcpus my-vn 4 --config
virsh setvcpus my-vn 4 --live

# Xem thông tin CPU
virsh vcpucount my-vn
virsh vcpuinfo my-vn
```

## Quản lý tài nguyên máy ảo
### Quản lý CPU
```console
# Ghim vCPU vào CPU vật lý
virsh vcpupin my-vn 0 0
virsh vcpupin my-vn 1 1

# Xem ghim CPU
virsh vcpupin my-vn

# Hủy ghim vCPU (cho phép lập lịch trên bất kỳ CPU nào)
virsh vcpupin my-vn 0 0-7

# Thiết lập chia sẻ CPU (độ ưu tiên lập lịch)
virsh schedinfo my-vn --set cpu_shares=2048

# Xem thông tin bộ lập lịch
virsh schedinfo my-vn

# Hiển thị số liệu thống kê CPU
virsh cpu-stats my-vn --total
```

### Quản lý bộ nhớ
```console
# Thiết lập mục tiêu bong bóng bộ nhớ
virsh setmem my-vn 2G

# Xem số liệu thống kê bộ nhớ
virsh dommemstat my-vn

# Thiết lập các tham số bộ nhớ
virsh memtune my-vn --hard-limit 8388608 --soft-limit 4194304

# Xem thông số tinh chỉnh bộ nhớ
virsh memtune my-vn

# Lấy thông tin bong bóng bộ nhớ
virsh dominfo my-vn | grep memory
```

### Quản lý đĩa
```console
# Liệt kê các đĩa VM
virsh domblklist my-vn

# Hiển thị thống kê đĩa
virsh domblkstat my-vn vda

# Gắn đĩa mới vào máy ảo đang chạy
virsh attach-disk my-vn \
  /var/lib/libvirt/images/my-vn-disk2.qcow2 \
  vdb \
  --driver qemu \
  --subdriver qcow2 \
  --type disk \
  --live

# Gắn đĩa vĩnh viễn
virsh attach-disk my-vn \
  /var/lib/libvirt/images/my-vn-disk2.qcow2 \
  vdb \
  --driver qemu \
  --subdriver qcow2 \
  --config --persistent

# Ngắt kết nối đĩa
virsh detach-disk my-vn vdb --live
virsh detach-disk my-vn vdb --config

# Thay đổi kích thước đĩa
virsh blockresize my-vn vda 50G

# Lấy thông tin ổ đĩa
virsh domblkinfo my-vn vda
```

### Quản lý giao diện mạng
```console
# Liệt kê các giao diện mạng của máy ảo
virsh domiflist my-vn

# Hiển thị thống kê giao diện
virsh domifstat my-vn vnet0

# Gắn giao diện mạng
virsh attach-interface my-vn \
  --type network \
  --source default \
  --model virtio \
  --config --live

# Ngắt kết nối giao diện mạng
virsh detach-interface my-vn \
  --type network \
  --mac 52:54:00:xx:xx:xx \
  --config

# Lấy thông tin chi tiết về giao diện
virsh domif-getlink my-vn vnet0

# Thiết lập trạng thái liên kết
virsh domif-setlink my-vn vnet0 up
virsh domif-setlink my-vn vnet0 down
```

## Quản lý nhóm lưu trữ
### Liệt kê các nhóm lưu trữ
```console
# Liệt kê tất cả các nhóm lưu trữ
virsh pool-list --all

# Liệt kê các pool đang hoạt động
virsh pool-list

# Liệt kê các pool không hoạt động
virsh pool-list --inactive

# Danh sách kèm thông tin chi tiết
virsh pool-list --all --details
```

### Tạo các pool lưu trữ
```console
# Tạo pool lưu trữ dựa trên thư mục
virsh pool-define-as mypool dir \
  --target /var/lib/libvirt/pools/mypool

# Xây dựng pool
virsh pool-build mypool

# Khởi động pool
virsh pool-start mypool

# Thiết lập pool tự động khởi động
virsh pool-autostart mypool

# Tạo pool chỉ với một dòng lệnh
virsh pool-define-as mypool dir --target /var/lib/libvirt/pools/mypool && \
virsh pool-build mypool && \
virsh pool-start mypool && \
virsh pool-autostart mypool
```

### Các thao tác với pool lưu trữ
```console
# Hiển thị thông tin hồ bơi
virsh pool-info mypool

# Hiển thị XML của pool
virsh pool-dumpxml mypool

# Làm mới pool lưu trữ (quét các volume mới)
virsh pool-refresh mypool

# Xóa pool (hủy bỏ)
virsh pool-destroy mypool

# Hủy định nghĩa pool
virsh pool-undefine mypool

# Xóa bộ nhớ lưu trữ của pool
virsh pool-delete mypool
```

### Quản lý phân vùng lưu trữ
```console
# Liệt kê các ổ đĩa trong pool
virsh vol-list mypool

# Tạo ổ đĩa mới
virsh vol-create-as mypool \
  my-vn-disk.qcow2 \
  20G \
  --format qcow2

# Sao chép ổ đĩa
virsh vol-clone \
  --pool mypool \
  my-vn-disk.qcow2 \
  my-vn-disk-clone.qcow2

# Xóa ổ đĩa
virsh vol-delete --pool mypool my-vn-disk.qcow2

# Hiển thị thông tin ổ đĩa
virsh vol-info --pool mypool my-vn-disk.qcow2

# Lấy đường dẫn ổ đĩa
virsh vol-path --pool mypool my-vn-disk.qcow2

# Thay đổi kích thước ổ đĩa
virsh vol-resize --pool mypool my-vn-disk.qcow2 30G

# Tải lên ổ đĩa
virsh vol-upload --pool mypool my-vn-disk.qcow2 /path/to/image.qcow2

# Tải xuống từ ổ đĩa
virsh vol-download --pool mypool my-vn-disk.qcow2 /backup/image.qcow2
```

## Quản lý mạng
### Liệt kê các mạng
```console
# Liệt kê tất cả các mạng
virsh net-list --all

# Liệt kê các mạng đang hoạt động
virsh net-list

# Liệt kê các mạng không hoạt động
virsh net-list --inactive
```

### Tạo mạng ảo
```console
# Tạo mạng NAT
cat > nat-network.xml << 'EOF'
<network>
  <name>nat-network</name>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='virbr1' stp='on' delay='0'/>
  <ip address='192.168.100.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.100.2' end='192.168.100.254'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define nat-network.xml
virsh net-start nat-network
virsh net-autostart nat-network

# Tạo mạng cô lập
cat > isolated-network.xml << 'EOF'
<network>
  <name>isolated-network</name>
  <bridge name='virbr-iso' stp='on' delay='0'/>
  <ip address='10.0.0.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.0.0.2' end='10.0.0.254'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define isolated-network.xml
virsh net-start isolated-network
virsh net-autostart isolated-network

# Tạo mạng cầu nối (cho cầu nối hiện có)
cat > bridge-network.xml << 'EOF'
<network>
  <name>bridge-network</name>
  <forward mode='bridge'/>
  <bridge name='br0'/>
</network>
EOF

virsh net-define bridge-network.xml
virsh net-start bridge-network
```

### Vận hành mạng
```console
# Hiển thị thông tin mạng
virsh net-info default

# Hiển thị XML của mạng
virsh net-dumpxml default

# Chỉnh sửa mạng
virsh net-edit default

# Khởi động mạng
virsh net-start nat-network

# Dừng mạng
virsh net-destroy nat-network

# Đặt mạng ở chế độ tự khởi động
virsh net-autostart nat-network

# Tắt tự động khởi động
virsh net-autostart nat-network --disable

# Xóa mạng
virsh net-undefine nat-network

# Hiển thị các bản ghi cấp phát DHCP
virsh net-dhcp-leases default
```

### Quản lý DHCP mạng
```console
# Thêm mục DHCP tĩnh
virsh net-update default add ip-dhcp-host \
  "<host mac='52:54:00:xx:xx:xx' name='webserver' ip='192.168.122.10'/>" \
  --live --config

# Xóa mục DHCP
virsh net-update default delete ip-dhcp-host \
  "<host mac='52:54:00:xx:xx:xx'/>" \
  --live --config

# Thêm dải DHCP
virsh net-update default add ip-dhcp-range \
  "<range start='192.168.122.100' end='192.168.122.200'/>" \
  --live --config

# Xem tất cả các hợp đồng thuê DHCP
virsh net-dhcp-leases default --mac 52:54:00:xx:xx:xx
```

## Quản lý bản chụp nhanh
### Tạo bản chụp nhanh
```console
# Tạo bản chụp nhanh
virsh snapshot-create-as my-vn \
  snapshot1 \
  "Clean install state"

# Tạo bản chụp nhanh với XML tùy chỉnh
virsh snapshot-create my-vn

# Tạo bản chụp nhanh kèm trạng thái đĩa
virsh snapshot-create-as my-vn snapshot2 \
  "Before updates" \
  --disk-only

# Tạo bản chụp nhanh không bao gồm trạng thái bộ nhớ (nhanh hơn)
virsh snapshot-create-as my-vn snapshot3 \
  "Quick snapshot" \
  --no-metadata

# Tạo bản chụp nhanh với đĩa cụ thể
virsh snapshot-create-as my-vn snapshot4 \
  --diskspec vda,file=/var/lib/libvirt/images/snap4.qcow2
```

### Ảnh chụp nhanh về tin đăng
```console
# Liệt kê tất cả các bản chụp nhanh
virsh snapshot-list my-vn

# Danh sách dạng cây
virsh snapshot-list my-vn --tree

# Danh sách kèm thông tin chi tiết
virsh snapshot-list my-vn --details

# Hiển thị ảnh chụp nhanh hiện tại
virsh snapshot-current my-vn

# Chỉ hiển thị tên bản chụp nhanh hiện tại
virsh snapshot-current my-vn --name
```

### Các thao tác snapshot
```console
# Hiển thị thông tin ảnh chụp nhanh
virsh snapshot-info my-vn snapshot1

# Hiển thị XML ảnh chụp nhanh
virsh snapshot-dumpxml my-vn snapshot1

# Khôi phục về bản chụp nhanh
virsh snapshot-revert my-vn snapshot1

# Hoàn tác và khởi động máy ảo
virsh snapshot-revert my-vn snapshot1 --running

# Xóa bản chụp nhanh
virsh snapshot-delete my-vn snapshot1

# Xóa bản chụp nhanh và các bản chụp con
virsh snapshot-delete my-vn snapshot1 --children

# Chỉ xóa siêu dữ liệu bản chụp nhanh
virsh snapshot-delete my-vn snapshot1 --metadata
```

### Tổng quan về mối quan hệ cha mẹ - con cái
```console
# Hiển thị ảnh chụp nhanh của phần tử cha
virsh snapshot-parent my-vn snapshot2

# Hiển thị ảnh chụp nhanh các phần tử con
virsh snapshot-list my-vn --tree

# Tạo bản sao nhanh con
virsh snapshot-create-as my-vn child-snapshot \
  "Child of snapshot1" \
  --parent snapshot1
```

## Quản lý Bảng điều khiển và Màn hình hiển thị
### Truy cập Bảng điều khiển VM
```console
# Kết nối với bảng điều khiển nối tiếp
virsh console my-vn

# Thoát khỏi console: Ctrl+]

# Kết nối với bảng điều khiển an toàn
virsh console my-vn --safe

# Buộc kết nối lại bảng điều khiển
virsh console my-vn --force
```

### Quản lý hiển thị VNC
```console
# Hiển thị màn hình VNC
virsh vncdisplay my-vn

# Output: :0 (nghĩa là cổng 5900)

# Kết nối bằng VNC Viewer
virt-viewer my-vn

# Kết nối VNC từ xa
virt-viewer -c qemu+ssh://user@host/system my-vn

# Thay đổi mật khẩu VNC
virsh domdisplay my-vn
```

### Đồ họa và Màn hình
```console
# Lấy URI hiển thị
virsh domdisplay my-vn

# Thiết lập mật khẩu đồ họa
virsh edit my-vn
# Thêm vào phần đồ họa:
# <graphics type='vnc' passwd='yourpassword'/>

# Chụp ảnh màn hình
virsh screenshot my-vn /tmp/screenshot.ppm

# Chuyển đổi sang định dạng phổ biến
convert /tmp/screenshot.ppm /tmp/screenshot.png
```

## Giám sát và Thống kê
### Giám sát tài nguyên hệ thống
```console
# Số liệu thống kê máy ảo theo thời gian thực
virt-top

# Hiển thị số liệu thống kê tên miền
virsh domstats my-vn

# Số liệu thống kê CPU
virsh cpu-stats my-vn --total

# Số liệu thống kê theo từng CPU
virsh cpu-stats my-vn

# Số liệu thống kê bộ nhớ
virsh dommemstat my-vn

# Số liệu thống kê thiết bị khối
virsh domblkstat my-vn vda

# Thông tin thiết bị khối
virsh domblkinfo my-vn vda

# Số liệu thống kê giao diện mạng
virsh domifstat my-vn vnet0
```

### Giám sát hiệu năng
```console
# Get VM job info (for long-running operations)
virsh domjobinfo my-vn

# Monitor block I/O tuning
virsh blkiotune my-vn

# Set I/O weight
virsh blkiotune my-vn --weight 500

# Get VM time info
virsh domtime my-vn

# Set VM time
virsh domtime my-vn --now

# Show control info
virsh domcontrol my-vn

# Show state reason
virsh domstate my-vn --reason
```

### Giám sát sự kiện
```console
# Theo dõi tất cả các sự kiện
virsh event --all

# Theo dõi các sự kiện đối với tên miền cụ thể
virsh event --domain my-vn

# Theo dõi các sự kiện vòng đời
virsh event --event lifecycle

# Theo dõi các loại sự kiện cụ thể
virsh event --event reboot
virsh event --event shutdown

# Liệt kê các loại sự kiện
virsh event --list
```

## Các hoạt động nâng cao
### Chuẩn bị cho Di chuyển Trực tuyến
```console
# Kiểm tra khả năng di chuyển dữ liệu
virsh capabilities

# Lấy XML của máy ảo để lập kế hoạch di chuyển
virsh dumpxml my-vn > my-vn-migrate.xml

# Kiểm tra tính khả thi của việc chuyển đổi
virsh migrate --verbose --live --p2p my-vn \
  qemu+ssh://destination-host/system \
  --dry-run
```

### Nhân bản máy ảo
```console
# Sao chép máy ảo (Máy ảo phải được tắt)
virt-clone --original my-vn \
  --name my-vn-clone \
  --file /var/lib/libvirt/images/my-vn-clone.qcow2

# Sao chép với tên tự động
virt-clone --original my-vn --auto-clone

# Chỉ sao chép các ổ đĩa cụ thể
virt-clone --original my-vn \
  --name my-vn-clone \
  --file /var/lib/libvirt/images/disk1.qcow2 \
  --file /var/lib/libvirt/images/disk2.qcow2
```

### Sao lưu và Khôi phục
```console
# Sao lưu cấu hình máy ảo
virsh dumpxml my-vn > /backup/my-vn-config.xml

# Sao lưu bao gồm thông tin nhạy cảm
virsh dumpxml my-vn --security-info > /backup/my-vn-full.xml

# Đĩa sao lưu
virsh domblklist my-vn
cp /var/lib/libvirt/images/my-vn.qcow2 /backup/

# Khôi phục máy ảo
virsh define /backup/my-vn-config.xml
cp /backup/my-vn.qcow2 /var/lib/libvirt/images/
virsh start my-vn

# Sao chép theo khối để sao lưu trực tuyến
virsh blockcommit my-vn vda --active --pivot
```

### Quản lý các trạng thái đã lưu
```console
# Lưu trạng thái máy ảo vào tệp
virsh save my-vn /var/lib/libvirt/save/my-vn.save

# Lưu kèm mô tả
virsh save my-vn /var/lib/libvirt/save/my-vn.save \
  "Pre-upgrade state"

# Khôi phục máy ảo từ trạng thái đã lưu
virsh restore /var/lib/libvirt/save/my-vn.save

# Hiển thị thông tin trạng thái đã lưu
virsh save-image-dumpxml /var/lib/libvirt/save/my-vn.save

# Chỉnh sửa XML trạng thái đã lưu
virsh save-image-edit /var/lib/libvirt/save/my-vn.save

# Định nghĩa XML mới để khôi phục
virsh save-image-define /var/lib/libvirt/save/my-vn.save new-config.xml
```

## An ninh và Kiểm soát ra vào
### Danh sách kiểm soát truy cập
Thiết lập quyền cho máy ảo

```console
virsh edit my-vn
```

Thêm các nhãn bảo mật (ví dụ: SELinux)

```xml
<seclabel type='dynamic' model='selinux' relabel='yes'>
  <label>system_u:system_r:svirt_t:s0:c100,c200</label>
  <imagelabel>system_u:object_r:svirt_image_t:s0:c100,c200</imagelabel>
</seclabel>
```

### Quản lý thông tin bí mật
```console
# Xác định bí mật (cho các ổ đĩa được mã hóa, v.v.)
cat > secret.xml << 'EOF'
<secret ephemeral='no' private='yes'>
  <description>LUKS encryption key</description>
  <uuid>12345678-1234-1234-1234-123456789abc</uuid>
</secret>
EOF

virsh secret-define secret.xml

# Thiết lập giá trị bí mật
virsh secret-set-value 12345678-1234-1234-1234-123456789abc \
  $(echo -n "secretpassword" | base64)

# Liệt kê các bí mật
virsh secret-list

# Lấy giá trị bí mật
virsh secret-get-value 12345678-1234-1234-1234-123456789abc

# Xóa bí mật
virsh secret-undefine 12345678-1234-1234-1234-123456789abc
```

## Khắc phục sự cố với Virsh
### Gỡ lỗi các sự cố máy ảo
```console
# Nhận thông tin chi tiết về lỗi
virsh domstate my-vn --reason

# Kiểm tra trạng thái điều khiển VM
virsh domcontrol my-vn

# Xem thông tin công việc theo lĩnh vực
virsh domjobinfo my-vn

# Kết xuất core của máy ảo để phân tích
virsh dump my-vn /tmp/my-vn.dump --memory-only

# Đặt lại máy ảo (khởi động lại cưỡng bức)
virsh reset my-vn

# Gửi chuỗi phím tới máy ảo
virsh send-key my-vn KEY_LEFTCTRL KEY_LEFTALT KEY_DELETE
```

### Quản lý tiến trình nền Libvirt
```console
# Kiểm tra phiên bản libvirt
virsh version

# Lấy thông tin hệ thống
virsh sysinfo

# Hiển thị các khả năng của máy chủ
virsh capabilities

# Lấy thông tin máy chủ
virsh nodeinfo

# Hiển thị thống kê bộ nhớ
virsh nodememstats

# Hiển thị số liệu thống kê CPU
virsh nodecpustats

# Lấy bản đồ CPU của máy chủ
virsh nodecpumap

# Tạm dừng máy chủ
virsh nodesuspend mem 3600

# Lấy trạng thái của tiến trình nền libvirt
systemctl status libvirtd

# Khởi động lại tiến trình nền libvirt
systemctl restart libvirtd
```

### Các vấn đề thường gặp và giải pháp
**Sự cố: Máy ảo không khởi động được**

```console
# Kiểm tra trạng thái chi tiết
virsh domstate my-vn --reason

# Xác minh sự tồn tại của đĩa
virsh domblklist my-vn

# Kiểm tra quyền truy cập đĩa
ls -l /var/lib/libvirt/images/

# Xác thực XML
virsh define my-vn.xml

# Kiểm tra nhật ký
journalctl -u libvirtd -f
tail -f /var/log/libvirt/qemu/my-vn.log
```

**Sự cố: Không thể kết nối với bảng điều khiển**

```console
# Xác minh rằng bảng điều khiển đã được cấu hình.
virsh dumpxml my-vn | grep console

# Hãy thử loại bảng điều khiển khác.
virsh console my-vn --devname serial0

# Kiểm tra xem máy ảo có đang phản hồi hay không.
virsh domstate my-vn
```

**Vấn đề: Mạng không hoạt động**

```console
# Kiểm tra xem mạng có đang hoạt động không
virsh net-list

# Khởi động mạng nếu cần
virsh net-start default

# Xác minh giao diện mạng của máy ảo
virsh domiflist my-vn

# Kiểm tra các thuê cấp DHCP
virsh net-dhcp-leases default
```

## Tự động hóa và Viết kịch bản
### Các thao tác theo lô
```bash
#!/bin/bash
# Khởi động tất cả các máy ảo
for vm in $(virsh list --name --inactive); do
    echo "Starting $vm..."
    virsh start "$vm"
done

# Dừng tất cả các máy ảo đang chạy một cách an toàn
for vm in $(virsh list --name); do
    echo "Shutting down $vm..."
    virsh shutdown "$vm"
done

# Tạo bản sao nhanh (snapshot) cho tất cả các máy ảo.
for vm in $(virsh list --name --all); do
    snapshot_name="snapshot-$(date +%Y%m%d-%H%M%S)"
    echo "Creating snapshot $snapshot_name for $vm"
    virsh snapshot-create-as "$vm" "$snapshot_name" "Automated snapshot"
done
```

### Tập lệnh giám sát tài nguyên
```bash
#!/bin/bash
# Giám sát tài nguyên máy ảo

while true; do
    clear
    echo "=== VM Resource Usage ==="
    echo ""

    for vm in $(virsh list --name); do
        echo "VM: $vm"

        # Mức sử dụng CPU
        cpu_time=$(virsh cpu-stats "$vm" --total | grep "cpu_time" | awk '{print $3}')
        echo "  CPU Time: $cpu_time seconds"

        # Mức sử dụng bộ nhớ
        virsh dommemstat "$vm" | head -n 4

        # Mức sử dụng ổ đĩa
        virsh domblkstat "$vm" vda | head -n 2

        echo ""
    done

    sleep 5
done
```

### Tự động hóa sao lưu
```bash
#!/bin/bash
# Tập lệnh sao lưu máy ảo tự động

BACKUP_DIR="/backup/vms"
DATE=$(date +%Y%m%d)

mkdir -p "$BACKUP_DIR"

for vm in $(virsh list --name --all); do
    echo "Backing up $vm..."

    # Sao lưu cấu hình
    virsh dumpxml "$vm" > "$BACKUP_DIR/${vm}-${DATE}.xml"

    # Tạo bản chụp nhanh
    virsh snapshot-create-as "$vm" "backup-$DATE"

    # Sao lưu đĩa (nếu máy ảo đã tắt)
    if [ "$(virsh domstate $vm)" = "shut off" ]; then
        disk=$(virsh domblklist "$vm" | grep vda | awk '{print $2}')
        if [ -n "$disk" ]; then
            cp "$disk" "$BACKUP_DIR/${vm}-${DATE}.qcow2"
        fi
    fi
done

echo "Backup completed"
```

## Tối ưu hóa hiệu năng
### Tinh chỉnh hiệu năng máy ảo
Đặt chế độ CPU thành host-passthrough

```console
virsh edit my-vn
```

Bật tính năng chuyển tiếp bộ nhớ đệm CPU

```xml
<cpu mode='host-passthrough'>
  <cache mode='passthrough'/>
</cpu>
```

Thiết lập ghim luồng I/O

```xml
<iothreads>2</iothreads>
<iothreadids>
  <iothread id='1'/>
  <iothread id='2'/>
</iothreadids>
```

Ghim các luồng I/O

```console
virsh iothreadpin my-vn 1 0-3
virsh iothreadpin my-vn 2 4-7
```

Thiết lập bộ nhớ đệm

```xml
<memoryBacking>
  <hugepages/>
  <nosharepages/>
  <locked/>
</memoryBacking>
```

### Tinh chỉnh thiết bị khối
```console
# Thiết lập các tham số tinh chỉnh I/O
virsh blkiotune ubuntu-vm --device /dev/vda --total-bytes-sec 104857600
virsh blkiotune ubuntu-vm --device /dev/vda --read-bytes-sec 52428800
virsh blkiotune ubuntu-vm --device /dev/vda --write-bytes-sec 52428800

# Thiết lập giới hạn tốc độ I/O khối
virsh blockresize ubuntu-vm vda 50G
```

## Kết luận
Virsh là công cụ không thể thiếu để quản lý các máy ảo KVM/QEMU, mang lại khả năng kiểm soát toàn diện đối với mọi khía cạnh của cơ sở hạ tầng ảo hóa. Hướng dẫn này đã đề cập đến các lệnh và thao tác thiết yếu phục vụ công tác quản lý máy ảo hàng ngày, từ các thao tác cơ bản trong vòng đời máy ảo cho đến việc tinh chỉnh nâng cao và khắc phục sự cố.

Những điểm chính cần lưu ý:  
* Virsh cung cấp khả năng quản lý máy ảo (VM) hỗ trợ viết kịch bản và tự động hóa
* Nắm vững các thao tác cơ bản (khởi động, dừng, liệt kê) trước khi chuyển sang các tính năng nâng cao
* Hiểu rõ về các nhóm lưu trữ (storage pool) và mạng để quản lý tài nguyên hiệu quả
* Sử dụng snapshot một cách chiến lược cho quy trình sao lưu và kiểm thử
* Thường xuyên theo dõi hiệu năng máy ảo để phát hiện các điểm nghẽn
* Tận dụng virsh trong các kịch bản để tự động hóa và thực hiện các tác vụ hàng loạt

Khi đã thành thạo virsh, bạn sẽ nhận thấy đây là phương thức hiệu quả nhất để quản lý cơ sở hạ tầng ảo hóa, đặc biệt là khi kết hợp với các công cụ quản lý cấu hình như Ansible hoặc Terraform. Giao diện dòng lệnh mang lại khả năng tự động hóa mạnh mẽ mà các công cụ có giao diện đồ họa không thể sánh kịp.

Hãy tiếp tục khám phá các tính năng nâng cao của virsh như di chuyển máy ảo trực tiếp (live migration), tinh chỉnh NUMA và gán CPU (CPU pinning) để tối ưu hóa hiệu suất cho các khối lượng công việc trong môi trường thực tế (production). Với sự am hiểu sâu sắc về virsh, bạn sẽ có thể quản lý các môi trường ảo hóa quy mô lớn một cách tự tin và hiệu quả.
