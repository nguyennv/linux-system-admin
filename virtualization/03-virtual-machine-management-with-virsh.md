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

* Kết nối hệ thống cục bộ (mặc định)

```console
virsh --connect qemu:///system
```

* Kết nối từ xa qua SSH

```console
virsh --connect qemu+ssh://user@remote-host/system
```

* Kết nối từ xa qua TLS

```console
virsh --connect qemu+tls://remote-host/system
```

* Phiên người dùng (không phải root)

```console
virsh --connect qemu:///session
```

* Kiểm tra kết nối hiện tại

```console
virsh uri
```

## Các lệnh Virsh cơ bản
### Tìm kiếm sự trợ giúp
* Trợ giúp chung

```console
virsh help
```

* Trợ giúp cho lệnh cụ thể

```console
virsh help list
virsh help start
```

* Liệt kê tất cả các lệnh theo danh mục

```console
virsh help | less
```

* Tìm kiếm lệnh

```console
virsh help | grep snapshot
```

### Liệt kê các máy ảo
* Liệt kê các máy ảo đang chạy

```console
virsh list
```

* Liệt kê tất cả các máy ảo (bao gồm cả những máy đã dừng)

```console
virsh list --all
```

* Liệt kê các máy ảo kèm thông tin chi tiết bổ sung

```console
virsh list --all --title
```

* Chỉ liệt kê các máy ảo không hoạt động

```console
virsh list --inactive
```

* Danh sách kèm thông tin chi tiết

```console
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
* Hiển thị thông tin máy ảo

```console
virsh dominfo my-vn
```

* Hiển thị ID máy ảo

```console
virsh domid my-vn
```

* Hiển thị UUID của máy ảo

```console
virsh domuuid my-vn
```

* Hiển thị trạng thái máy ảo

```console
virsh domstate my-vn
```

* Hiển thị tất cả siêu dữ liệu của máy ảo

```console
virsh metadata my-vn --uri http://example.com/
```

* Hiển thị cấu hình máy ảo (XML)

```console
virsh dumpxml my-vn
```

* Hiển thị cấu hình máy ảo cùng thông tin bảo mật

```console
virsh dumpxml my-vn --security-info
```

* Lưu cấu hình vào tệp

```console
virsh dumpxml my-vn > my-vn.xml
```

## Quản lý vòng đời máy ảo
### Khởi động máy ảo
* Khởi động máy ảo

```console
virsh start my-vn
```

* Khởi động máy ảo và mở bảng điều khiển

```console
virsh start my-vn --console
```

* Bắt đầu từ trạng thái tạm dừng

```console
virsh start my-vn --paused
```

* Buộc khởi động (bỏ qua lỗi)

```console
virsh start my-vn --force-boot
```

* Bắt đầu với thiết bị khởi động cụ thể

```console
virsh start my-vn --boot-device cdrom
```

### Dừng các máy ảo
* Tắt máy an toàn (tín hiệu ACPI)

```console
virsh shutdown my-vn
```

* Tắt máy với chế độ cụ thể

```console
virsh shutdown my-vn --mode acpi
```

* Buộc tắt nguồn (ngay lập tức)

```console
virsh destroy my-vn
```

* Khởi động lại máy ảo

```console
virsh reboot my-vn
```

* Khởi động lại bằng ACPI

```console
virsh reboot my-vn --mode acpi
```

* Khởi động lại cứng (giống như nút reset vật lý)

```console
virsh reset my-vn
```

### Tạm dừng và Tiếp tục
* Tạm dừng (tạm ngưng) máy ảo

```console
virsh suspend my-vn
```

* Tiếp tục chạy máy ảo đang tạm dừng

```console
virsh resume my-vn
```

* Lưu trạng thái máy ảo vào đĩa (ngủ đông)

```console
virsh save my-vn /var/lib/libvirt/images/my-vn.save
```

* Khôi phục máy ảo từ trạng thái đã lưu

```console
virsh restore /var/lib/libvirt/images/my-vn.save
```

* Lưu với XML tùy chỉnh để khôi phục

```console
virsh save my-vn --xml custom.xml /tmp/my-vn.save
```

### Cấu hình Tự động khởi động
* Bật tự động khởi động khi máy chủ khởi động

```console
virsh autostart my-vn
```

* Tắt tự động khởi động

```console
virsh autostart my-vn --disable
```

* Kiểm tra trạng thái tự động khởi động

```console
virsh dominfo my-vn | grep Autostart
```

### Xóa máy ảo
* Hủy định nghĩa VM (xóa cấu hình, giữ lại đĩa)

```console
virsh undefine my-vn
```

* Hủy định nghĩa và xóa tất cả bộ nhớ

```console
virsh undefine my-vn --remove-all-storage
```

* Hủy định nghĩa với các snapshot

```console
virsh undefine my-vn --snapshots-metadata
```

* Hủy xác định với tính năng lưu được quản lý

```console
virsh undefine my-vn --managed-save
```

* Loại bỏ hoàn toàn

```console
virsh undefine my-vn \
  --remove-all-storage \
  --snapshots-metadata \
  --managed-save
```

## Tạo và định nghĩa máy ảo
### Tạo máy ảo từ XML
* Tạo tệp XML cấu hình máy ảo

```console
vi my-vn.xml
```

```xml
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
```

* Định nghĩa máy ảo từ XML

```console
virsh define my-vn.xml
```

* Tạo và bắt đầu ngay lập tức

```console
virsh create my-vn.xml
```

* Xác định nhưng đừng bắt đầu

```console
virsh define my-vn.xml
```

### Sử dụng virt-install với virsh
* Tạo máy ảo bằng virt-install

```console
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
```

* Xem xét và chỉnh sửa XML trước khi định nghĩa.

```console
vim my-vn.xml
```

* Xác định máy ảo

```console
virsh define my-vn.xml
```

## Chỉnh sửa cấu hình máy ảo
### Chỉnh sửa XML tương tác
Chỉnh sửa cấu hình máy ảo

```console
virsh edit my-vn
```

Thao tác này mở tệp XML của máy ảo trong trình soạn thảo mặc định của bạn. Các thay đổi sẽ được kiểm tra tính hợp lệ trước khi lưu.

### Thay đổi cài đặt máy ảo
* Thay đổi mô tả máy ảo

```console
virsh desc my-vn "Development web server"
```

* Xem mô tả

```console
virsh desc my-vn
```

* Thay đổi tiêu đề máy ảo

```console
virsh metadata my-vn \
  --uri http://libvirt.org/title \
  --key title \
  --set "Production Web Server"
```

* Thiết lập siêu dữ liệu tùy chỉnh

```console
virsh metadata my-vn \
  --uri http://example.com/ \
  --key environment \
  --set "production"
```

### Sửa đổi tài nguyên
* Thay đổi bộ nhớ tối đa (yêu cầu khởi động lại máy ảo)

```console
virsh setmaxmem my-vn 8G --config
```

* Thay đổi bộ nhớ hiện tại (trực tiếp)

```console
virsh setmem my-vn 4G --live
```

* Thay đổi bộ nhớ vĩnh viễn

```console
virsh setmem my-vn 4G --config
```

* Xem cài đặt bộ nhớ

```console
virsh dommemstat my-vn
```

* Thay đổi số lượng vCPU

```console
virsh setvcpus my-vn 4 --config --maximum
virsh setvcpus my-vn 4 --config
virsh setvcpus my-vn 4 --live
```

* Xem thông tin CPU

```console
virsh vcpucount my-vn
virsh vcpuinfo my-vn
```

## Quản lý tài nguyên máy ảo
### Quản lý CPU
* Ghim vCPU vào CPU vật lý

```console
virsh vcpupin my-vn 0 0
virsh vcpupin my-vn 1 1
```

* Xem ghim CPU

```console
virsh vcpupin my-vn
```

* Hủy ghim vCPU (cho phép lập lịch trên bất kỳ CPU nào)

```console
virsh vcpupin my-vn 0 0-7
```

* Thiết lập chia sẻ CPU (độ ưu tiên lập lịch)

```console
virsh schedinfo my-vn --set cpu_shares=2048
```

* Xem thông tin bộ lập lịch

```console
virsh schedinfo my-vn
```

* Hiển thị số liệu thống kê CPU

```console
virsh cpu-stats my-vn --total
```

### Quản lý bộ nhớ
* Thiết lập mục tiêu bong bóng bộ nhớ

```console
virsh setmem my-vn 2G
```

* Xem số liệu thống kê bộ nhớ

```console
virsh dommemstat my-vn
```

* Thiết lập các tham số bộ nhớ

```console
virsh memtune my-vn --hard-limit 8388608 --soft-limit 4194304
```

* Xem thông số tinh chỉnh bộ nhớ

```console
virsh memtune my-vn
```

* Lấy thông tin bong bóng bộ nhớ

```console
virsh dominfo my-vn | grep memory
```

### Quản lý đĩa
* Liệt kê các đĩa VM

```console
virsh domblklist my-vn
```

* Hiển thị thống kê đĩa

```console
virsh domblkstat my-vn vda
```

* Gắn đĩa mới vào máy ảo đang chạy

```console
virsh attach-disk my-vn \
  /var/lib/libvirt/images/my-vn-disk2.qcow2 \
  vdb \
  --driver qemu \
  --subdriver qcow2 \
  --type disk \
  --live
```

* Gắn đĩa vĩnh viễn

```console
virsh attach-disk my-vn \
  /var/lib/libvirt/images/my-vn-disk2.qcow2 \
  vdb \
  --driver qemu \
  --subdriver qcow2 \
  --config --persistent
```

* Ngắt kết nối đĩa

```console
virsh detach-disk my-vn vdb --live
virsh detach-disk my-vn vdb --config
```

* Thay đổi kích thước đĩa

```console
virsh blockresize my-vn vda 50G
```

* Lấy thông tin ổ đĩa

```console
virsh domblkinfo my-vn vda
```

### Quản lý giao diện mạng
* Liệt kê các giao diện mạng của máy ảo

```console
virsh domiflist my-vn
```

* Hiển thị thống kê giao diện

```console
virsh domifstat my-vn vnet0
```

* Gắn giao diện mạng

```console
virsh attach-interface my-vn \
  --type network \
  --source default \
  --model virtio \
  --config --live
```

* Ngắt kết nối giao diện mạng

```console
virsh detach-interface my-vn \
  --type network \
  --mac 52:54:00:xx:xx:xx \
  --config
```

* Lấy thông tin chi tiết về giao diện

```console
virsh domif-getlink my-vn vnet0
```

* Thiết lập trạng thái liên kết

```console
virsh domif-setlink my-vn vnet0 up
virsh domif-setlink my-vn vnet0 down
```

## Quản lý pool lưu trữ
### Liệt kê các pool lưu trữ
* Liệt kê tất cả các pool lưu trữ

```console
virsh pool-list --all
```

* Liệt kê các pool đang hoạt động

```console
virsh pool-list
```

* Liệt kê các pool không hoạt động

```console
virsh pool-list --inactive
```

* Danh sách kèm thông tin chi tiết

```console
virsh pool-list --all --details
```

### Tạo các pool lưu trữ
* Tạo pool lưu trữ dựa trên thư mục

```console
virsh pool-define-as mypool dir \
  --target /var/lib/libvirt/pools/mypool
```

* Xây dựng pool

```console
virsh pool-build mypool
```

* Khởi động pool

```console
virsh pool-start mypool
```

* Thiết lập pool tự động khởi động

```console
virsh pool-autostart mypool
```

* Tạo pool chỉ với một dòng lệnh

```console
virsh pool-define-as mypool dir --target /var/lib/libvirt/pools/mypool && \
virsh pool-build mypool && \
virsh pool-start mypool && \
virsh pool-autostart mypool
```

### Các thao tác với pool lưu trữ
* Hiển thị thông tin pool

```console
virsh pool-info mypool
```

* Hiển thị XML của pool

```console
virsh pool-dumpxml mypool
```

* Làm mới pool lưu trữ (quét các volume mới)

```console
virsh pool-refresh mypool
```

* Xóa pool (hủy bỏ)

```console
virsh pool-destroy mypool
```

* Hủy định nghĩa pool

```console
virsh pool-undefine mypool
```

* Xóa bộ nhớ lưu trữ của pool

```console
virsh pool-delete mypool
```

### Quản volume
* Liệt kê các volume trong pool

```console
virsh vol-list mypool
```

* Tạo volume mới

```console
virsh vol-create-as mypool \
  my-vn-disk.qcow2 \
  20G \
  --format qcow2
```

* Sao chép volume

```console
virsh vol-clone \
  --pool mypool \
  my-vn-disk.qcow2 \
  my-vn-disk-clone.qcow2
```

* Xóa volume

```console
virsh vol-delete --pool mypool my-vn-disk.qcow2
```

* Hiển thị thông tin volume

```console
virsh vol-info --pool mypool my-vn-disk.qcow2
```

* Lấy đường dẫn volume

```console
virsh vol-path --pool mypool my-vn-disk.qcow2
```

* Thay đổi kích thước volume

```console
virsh vol-resize --pool mypool my-vn-disk.qcow2 30G
```

* Tải lên volume

```console
virsh vol-upload --pool mypool my-vn-disk.qcow2 /path/to/image.qcow2
```

* Tải xuống từ volume

```console
virsh vol-download --pool mypool my-vn-disk.qcow2 /backup/image.qcow2
```

## Quản lý mạng
### Liệt kê các mạng
* Liệt kê tất cả các mạng

```console
virsh net-list --all
```

* Liệt kê các mạng đang hoạt động

```console
virsh net-list
```

* Liệt kê các mạng không hoạt động

```console
virsh net-list --inactive
```

### Tạo mạng ảo
#### Tạo mạng NAT

```console
vi nat-network.xml
```

```xml
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
```

```console
virsh net-define nat-network.xml
virsh net-start nat-network
virsh net-autostart nat-network
```

#### Tạo mạng cô lập

```console
vi isolated-network.xml
```

```xml
<network>
  <name>isolated-network</name>
  <bridge name='virbr-iso' stp='on' delay='0'/>
  <ip address='10.0.0.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.0.0.2' end='10.0.0.254'/>
    </dhcp>
  </ip>
</network>
```

```console
virsh net-define isolated-network.xml
virsh net-start isolated-network
virsh net-autostart isolated-network
```

#### Tạo mạng cầu nối (cho cầu nối hiện có)

```console
vi bridge-network.xml
```

```xml
<network>
  <name>bridge-network</name>
  <forward mode='bridge'/>
  <bridge name='br0'/>
</network>
```

```console
virsh net-define bridge-network.xml
virsh net-start bridge-network
```

### Vận hành mạng
* Hiển thị thông tin mạng

```console
virsh net-info default
```

* Hiển thị XML của mạng

```console
virsh net-dumpxml default
```

* Chỉnh sửa mạng

```console
virsh net-edit default
```

* Khởi động mạng

```console
virsh net-start nat-network
```

* Dừng mạng

```console
virsh net-destroy nat-network
```

* Đặt mạng ở chế độ tự khởi động

```console
virsh net-autostart nat-network
```

* Tắt tự động khởi động

```console
virsh net-autostart nat-network --disable
```

* Xóa mạng

```console
virsh net-undefine nat-network
```

* Hiển thị các bản ghi cấp phát DHCP

```console
virsh net-dhcp-leases default
```

### Quản lý DHCP mạng
* Thêm mục DHCP tĩnh

```console
virsh net-update default add ip-dhcp-host \
  "<host mac='52:54:00:xx:xx:xx' name='webserver' ip='192.168.122.10'/>" \
  --live --config
```

* Xóa mục DHCP

```console
virsh net-update default delete ip-dhcp-host \
  "<host mac='52:54:00:xx:xx:xx'/>" \
  --live --config
```

* Thêm dải DHCP

```console
virsh net-update default add ip-dhcp-range \
  "<range start='192.168.122.100' end='192.168.122.200'/>" \
  --live --config
```

* Xem tất cả các hợp đồng thuê DHCP

```console
virsh net-dhcp-leases default --mac 52:54:00:xx:xx:xx
```

## Quản lý snapshot
### Tạo snapshot
* Tạo snapshot

```console
virsh snapshot-create-as my-vn \
  snapshot1 \
  "Clean install state"
```

* Tạo snapshot với XML tùy chỉnh

```console
virsh snapshot-create my-vn
```

* Tạo snapshot kèm trạng thái đĩa

```console
virsh snapshot-create-as my-vn snapshot2 \
  "Before updates" \
  --disk-only
```

* Tạo snapshot không bao gồm trạng thái bộ nhớ (nhanh hơn)

```console
virsh snapshot-create-as my-vn snapshot3 \
  "Quick snapshot" \
  --no-metadata
```

* Tạo snapshot với đĩa cụ thể

```console
virsh snapshot-create-as my-vn snapshot4 \
  --diskspec vda,file=/var/lib/libvirt/images/snap4.qcow2
```

### Liệt kê snapshot
* Liệt kê tất cả các snapshot

```console
virsh snapshot-list my-vn
```

* Danh sách dạng cây

```console
virsh snapshot-list my-vn --tree
```

* Danh sách kèm thông tin chi tiết

```console
virsh snapshot-list my-vn --details
```

* Hiển thị snapshot hiện tại

```console
virsh snapshot-current my-vn
```

* Chỉ hiển thị tên snapshot hiện tại

```console
virsh snapshot-current my-vn --name
```

### Các thao tác snapshot
* Hiển thị thông tin snapshot

```console
virsh snapshot-info my-vn snapshot1
```

* Hiển thị XML snapshot

```console
virsh snapshot-dumpxml my-vn snapshot1
```

* Khôi phục về snapshot

```console
virsh snapshot-revert my-vn snapshot1
```

* Hoàn tác và khởi động máy ảo

```console
virsh snapshot-revert my-vn snapshot1 --running
```

* Xóa snapshot

```console
virsh snapshot-delete my-vn snapshot1
```

* Xóa snapshot và các bản chụp con

```console
virsh snapshot-delete my-vn snapshot1 --children
```

* Chỉ xóa siêu dữ liệu snapshot

```console
virsh snapshot-delete my-vn snapshot1 --metadata
```

### Tổng quan về mối quan hệ cha - con
* Hiển thị snapshot của phần tử cha

```console
virsh snapshot-parent my-vn snapshot2
```

* Hiển thị snapshot các phần tử con

```console
virsh snapshot-list my-vn --tree
```

* Tạo bản sao nhanh con

```console
virsh snapshot-create-as my-vn child-snapshot \
  "Child of snapshot1" \
  --parent snapshot1
```

## Quản lý Bảng điều khiển và Màn hình hiển thị
### Truy cập Bảng điều khiển VM
* Kết nối với bảng điều khiển nối tiếp

```console
virsh console my-vn
# Thoát khỏi console: Ctrl+]
```

* Kết nối với bảng điều khiển an toàn

```console
virsh console my-vn --safe
```

* Buộc kết nối lại bảng điều khiển

```console
virsh console my-vn --force
```

### Quản lý hiển thị VNC
* Hiển thị màn hình VNC

```console
virsh vncdisplay my-vn
# Output: :0 (nghĩa là cổng 5900)
```

* Kết nối bằng VNC Viewer

```console
virt-viewer my-vn
```

* Kết nối VNC từ xa

```console
virt-viewer -c qemu+ssh://user@host/system my-vn
```

* Thay đổi mật khẩu VNC

```console
virsh domdisplay my-vn
```

### Đồ họa và Màn hình
* Lấy URI hiển thị

```console
virsh domdisplay my-vn
```

* Thiết lập mật khẩu đồ họa

```console
virsh edit my-vn
# Thêm vào phần đồ họa:
# <graphics type='vnc' passwd='yourpassword'/>
```

* Chụp ảnh màn hình

```console
virsh screenshot my-vn /tmp/screenshot.ppm
```

* Chuyển đổi sang định dạng phổ biến

```console
convert /tmp/screenshot.ppm /tmp/screenshot.png
```

## Giám sát và Thống kê
### Giám sát tài nguyên hệ thống
* Số liệu thống kê máy ảo theo thời gian thực

```console
virt-top
```

* Hiển thị số liệu thống kê tên miền

```console
virsh domstats my-vn
```

* Số liệu thống kê CPU

```console
virsh cpu-stats my-vn --total
```

* Số liệu thống kê theo từng CPU

```console
virsh cpu-stats my-vn
```

* Số liệu thống kê bộ nhớ

```console
virsh dommemstat my-vn
```

* Số liệu thống kê thiết bị khối

```console
virsh domblkstat my-vn vda
```

* Thông tin thiết bị khối

```console
virsh domblkinfo my-vn vda
```

* Số liệu thống kê giao diện mạng

```console
virsh domifstat my-vn vnet0
```

### Giám sát hiệu năng
* Get VM job info (for long-running operations)

```console
virsh domjobinfo my-vn
```

* Monitor block I/O tuning

```console
virsh blkiotune my-vn
```

* Set I/O weight

```console
virsh blkiotune my-vn --weight 500
```

* Get VM time info

```console
virsh domtime my-vn
```

* Set VM time

```console
virsh domtime my-vn --now
```

* Show control info

```console
virsh domcontrol my-vn
```

* Show state reason

```console
virsh domstate my-vn --reason
```

### Giám sát sự kiện
* Theo dõi tất cả các sự kiện

```console
virsh event --all
```

* Theo dõi các sự kiện đối với tên miền cụ thể

```console
virsh event --domain my-vn
```

* Theo dõi các sự kiện vòng đời

```console
virsh event --event lifecycle
```

* Theo dõi các loại sự kiện cụ thể

```console
virsh event --event reboot
virsh event --event shutdown
```

* Liệt kê các loại sự kiện

```console
virsh event --list
```

## Các hoạt động nâng cao
### Chuẩn bị cho Di trú Trực tuyến (Live Migrate)
* Kiểm tra khả năng di trú dữ liệu

```console
virsh capabilities
```

* Lấy XML của máy ảo để lập kế hoạch di trú

```console
virsh dumpxml my-vn > my-vn-migrate.xml
```

* Kiểm tra tính khả thi của việc di trú

```console
virsh migrate --verbose --live --p2p my-vn \
  qemu+ssh://destination-host/system \
  --dry-run
```

### Nhân bản máy ảo
* Nhân bản máy ảo (Máy ảo phải được tắt)

```console
virt-clone --original my-vn \
  --name my-vn-clone \
  --file /var/lib/libvirt/images/my-vn-clone.qcow2
```

* Nhân bản với tên tự động

```console
virt-clone --original my-vn --auto-clone
```

* Chỉ nhân bản các ổ đĩa cụ thể

```console
virt-clone --original my-vn \
  --name my-vn-clone \
  --file /var/lib/libvirt/images/disk1.qcow2 \
  --file /var/lib/libvirt/images/disk2.qcow2
```

### Sao lưu và Khôi phục
* Sao lưu cấu hình máy ảo

```console
virsh dumpxml my-vn > /backup/my-vn-config.xml
```

* Sao lưu bao gồm thông tin nhạy cảm

```console
virsh dumpxml my-vn --security-info > /backup/my-vn-full.xml
```

* Sao lưu đĩa

```console
virsh domblklist my-vn
cp /var/lib/libvirt/images/my-vn.qcow2 /backup/
```

* Khôi phục máy ảo

```console
virsh define /backup/my-vn-config.xml
cp /backup/my-vn.qcow2 /var/lib/libvirt/images/
virsh start my-vn
```

* Sao chép theo khối để sao lưu trực tuyến

```console
virsh blockcommit my-vn vda --active --pivot
```

### Quản lý các trạng thái đã lưu
* Lưu trạng thái máy ảo vào tệp

```console
virsh save my-vn /var/lib/libvirt/save/my-vn.save
```

* Lưu kèm mô tả

```console
virsh save my-vn /var/lib/libvirt/save/my-vn.save \
  "Pre-upgrade state"
```

* Khôi phục máy ảo từ trạng thái đã lưu

```console
virsh restore /var/lib/libvirt/save/my-vn.save
```

* Hiển thị thông tin trạng thái đã lưu

```console
virsh save-image-dumpxml /var/lib/libvirt/save/my-vn.save
```

* Chỉnh sửa XML trạng thái đã lưu

```console
virsh save-image-edit /var/lib/libvirt/save/my-vn.save
```

* Định nghĩa XML mới để khôi phục

```console
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
Xác định bí mật (cho các ổ đĩa được mã hóa, v.v.)

```console
vi secret.xml
```

```xml
<secret ephemeral='no' private='yes'>
  <description>LUKS encryption key</description>
  <uuid>12345678-1234-1234-1234-123456789abc</uuid>
</secret>
```

```console
virsh secret-define secret.xml
```

* Thiết lập giá trị bí mật

```console
virsh secret-set-value 12345678-1234-1234-1234-123456789abc \
  $(echo -n "secretpassword" | base64)
```

* Liệt kê các bí mật

```console
virsh secret-list
```

* Lấy giá trị bí mật

```console
virsh secret-get-value 12345678-1234-1234-1234-123456789abc
```

* Xóa bí mật

```console
virsh secret-undefine 12345678-1234-1234-1234-123456789abc
```

## Khắc phục sự cố với Virsh
### Gỡ lỗi các sự cố máy ảo
* Nhận thông tin chi tiết về lỗi

```console
virsh domstate my-vn --reason
```

* Kiểm tra trạng thái điều khiển VM

```console
virsh domcontrol my-vn
```

* Xem thông tin công việc theo lĩnh vực

```console
virsh domjobinfo my-vn
```

* Kết xuất core của máy ảo để phân tích

```console
virsh dump my-vn /tmp/my-vn.dump --memory-only
```

* Đặt lại máy ảo (khởi động lại cưỡng bức)

```console
virsh reset my-vn
```

* Gửi chuỗi phím tới máy ảo

```console
virsh send-key my-vn KEY_LEFTCTRL KEY_LEFTALT KEY_DELETE
```

### Quản lý tiến trình nền Libvirt
* Kiểm tra phiên bản libvirt

```console
virsh version
```

* Lấy thông tin hệ thống

```console
virsh sysinfo
```

* Hiển thị các khả năng của máy chủ

```console
virsh capabilities
```

* Lấy thông tin máy chủ

```console
virsh nodeinfo
```

* Hiển thị thống kê bộ nhớ

```console
virsh nodememstats
```

* Hiển thị số liệu thống kê CPU

```console
virsh nodecpustats
```

* Lấy bản đồ CPU của máy chủ

```console
virsh nodecpumap
```

* Tạm dừng máy chủ

```console
virsh nodesuspend mem 3600
```

* Lấy trạng thái của tiến trình nền libvirt

```console
systemctl status libvirtd
```

* Khởi động lại tiến trình nền libvirt

```console
systemctl restart libvirtd
```

### Các vấn đề thường gặp và giải pháp
#### Sự cố: Máy ảo không khởi động được
* Kiểm tra trạng thái chi tiết

```console
virsh domstate my-vn --reason
```

* Xác minh sự tồn tại của đĩa

```console
virsh domblklist my-vn
```

* Kiểm tra quyền truy cập đĩa

```console
ls -l /var/lib/libvirt/images/
```

* Xác thực XML

```console
virsh define my-vn.xml
```

* Kiểm tra nhật ký

```console
journalctl -u libvirtd -f
tail -f /var/log/libvirt/qemu/my-vn.log
```

#### Sự cố: Không thể kết nối với bảng điều khiển
* Xác minh rằng bảng điều khiển đã được cấu hình.

```console
virsh dumpxml my-vn | grep console
```

* Hãy thử loại bảng điều khiển khác.

```console
virsh console my-vn --devname serial0
```

* Kiểm tra xem máy ảo có đang phản hồi hay không.

```console
virsh domstate my-vn
```

#### Vấn đề: Mạng không hoạt động
* Kiểm tra xem mạng có đang hoạt động không

```console
virsh net-list
```

* Khởi động mạng nếu cần

```console
virsh net-start default
```

* Xác minh giao diện mạng của máy ảo

```console
virsh domiflist my-vn
```

* Kiểm tra các thuê cấp DHCP

```console
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
    time=$(virsh cpu-stats "$vm" --total | grep "cpu_time" | awk '{print $3}')
    echo "  CPU Time: $time seconds"

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

  # Tạo snapshot
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
* Đặt chế độ CPU thành host-passthrough

```console
virsh edit my-vn
```

* Bật tính năng chuyển tiếp bộ nhớ đệm CPU

```xml
<cpu mode='host-passthrough'>
  <cache mode='passthrough'/>
</cpu>
```

* Thiết lập ghim luồng I/O

```xml
<iothreads>2</iothreads>
<iothreadids>
  <iothread id='1'/>
  <iothread id='2'/>
</iothreadids>
```

* Ghim các luồng I/O

```console
virsh iothreadpin my-vn 1 0-3
virsh iothreadpin my-vn 2 4-7
```

* Thiết lập bộ nhớ đệm

```xml
<memoryBacking>
  <hugepages/>
  <nosharepages/>
  <locked/>
</memoryBacking>
```

### Tinh chỉnh thiết bị khối
* Thiết lập các tham số tinh chỉnh I/O

```console
virsh blkiotune ubuntu-vm --device /dev/vda --total-bytes-sec 104857600
virsh blkiotune ubuntu-vm --device /dev/vda --read-bytes-sec 52428800
virsh blkiotune ubuntu-vm --device /dev/vda --write-bytes-sec 52428800
```

* Thiết lập giới hạn tốc độ I/O khối

```console
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
