# Cấu hình mạng ảo với Libvirt: Hướng dẫn toàn tập
## Giới thiệu
Mạng ảo là thành phần cốt lõi của công nghệ ảo hóa KVM/QEMU, cho phép các máy ảo giao tiếp với nhau, với hệ thống máy chủ (host) và với các mạng bên ngoài. Libvirt cung cấp một khung làm việc toàn diện để cấu hình và quản lý mạng ảo, mang lại sự linh hoạt từ các cấu hình NAT đơn giản cho đến những kiến trúc mạng đa tầng phức tạp.

Hướng dẫn chi tiết này sẽ đi sâu vào các khả năng về mạng ảo của libvirt, bao gồm các chế độ mạng, cấu hình bridge, các kịch bản định tuyến nâng cao và tối ưu hóa hiệu năng. Cho dù bạn đang xây dựng hệ thống thử nghiệm tại nhà (home lab), môi trường phát triển hay cơ sở hạ tầng thực tế (production), việc hiểu rõ về mạng trong libvirt là yếu tố then chốt để tạo ra các môi trường ảo hóa an toàn, hiệu quả và có khả năng mở rộng.

Các mạng ảo của libvirt tận dụng những thành phần mạng của Linux—như bridge, thiết bị TAP, iptables và dnsmasq—để cung cấp dịch vụ mạng cho máy ảo. Lớp trừu tượng mà libvirt cung cấp giúp đơn giản hóa các cấu hình mạng phức tạp, đồng thời vẫn duy trì sự linh hoạt để triển khai các kịch bản mạng nâng cao khi cần thiết.

Sau khi hoàn thành hướng dẫn này, bạn sẽ nắm vững kỹ năng cấu hình mạng libvirt, từ các thiết lập NAT cơ bản đến những kịch bản nâng cao liên quan đến VLAN, nhiều bridge, định tuyến tùy chỉnh và các chiến lược cô lập mạng thường được áp dụng trong môi trường thực tế.

## Tìm hiểu về mạng ảo Libvirt
### Kiến trúc mạng Libvirt
```
┌─────────────────────────────────────────────┐
│               Máy ảo (Khách)                │
│            eth0 (NIC máy ảo khách)          │
└──────────────────┬──────────────────────────┘
                   │
         ┌─────────▼────────────┐
         │  vnet0 (Thiết bị TAP)│
         │    52:54:00:xx:xx    │
         └─────────┬────────────┘
                   │
         ┌─────────▼─────────┐
         │  virbr0 (Bridge)  │
         │   192.168.122.1   │
         └─────────┬─────────┘
                   │
    ┌──────────────┴───────────────┐
    │                              │
┌───▼────┐                  ┌──────▼─────┐
│dnsmasq │                  │  iptables  │
│ (DHCP) │                  │    (NAT)   │
└────────┘                  └──────┬─────┘
                                   │
                           ┌───────▼──────────┐
                           │ Card mạng vật lý │
                           │  (Mạng máy chủ)  │
                           └──────────────────┘

```

### Các thành phần mạng
**Giao diện mạng ảo (vnet):**

* Thiết bị TAP được kết nối với máy ảo khách
* Hiển thị như một card mạng (NIC) thông thường bên trong máy ảo khách
* Được libvirt tự động quản lý

**Cầu nối ảo (virbr):**

* Bộ chuyển mạch phần mềm kết nối các máy ảo
* Kết nối các thiết bị TAP với nhau
* Cung cấp khả năng kết nối ở Lớp 2 (Layer 2)

**dnsmasq:**

* Cung cấp dịch vụ DHCP
* Chuyển tiếp và lưu đệm DNS
* Được quản lý bởi libvirt

**iptables:**

* NAT và các quy tắc chuyển tiếp
* Tường lửa và lọc dữ liệu
* Định tuyến giữa các mạng

## Các chế độ mạng trong Libvirt
### 1. Chế độ NAT (Mặc định)
Chế độ NAT (Network Address Translation) cung cấp khả năng kết nối Internet cho các máy ảo (VM) trong khi vẫn giữ chúng tách biệt khỏi mạng bên ngoài.

**Đặc trưng:**

* Các máy ảo được cấp địa chỉ IP riêng (mặc định là 192.168.122.0/24)
* Các máy ảo có thể truy cập mạng bên ngoài
* Mạng bên ngoài không thể truy cập trực tiếp vào các máy ảo
* Máy chủ đóng vai trò là bộ định tuyến (router) có sử dụng NAT

**Các trường hợp sử dụng:**

* Môi trường phát triển
* Máy ảo kiểm thử
* Máy ảo cần kết nối Internet nhưng không cần truy cập từ bên ngoài

#### Tạo mạng NAT:
Tạo cấu hình mạng NAT

```console
cat > nat-network.xml << 'EOF'
<network>
  <name>nat-network</name>
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='virbr1' stp='on' delay='0'/>
  <domain name='nat-network.local'/>
  <ip address='192.168.100.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.100.10' end='192.168.100.250'/>
      <host mac='52:54:00:11:22:33' name='webserver' ip='192.168.100.50'/>
    </dhcp>
  </ip>
</network>
EOF
```

Định nghĩa và khởi động mạng

```console
virsh net-define nat-network.xml
virsh net-start nat-network
virsh net-autostart nat-network
```

Xác minh mạng

```console
virsh net-list
virsh net-info nat-network
```

#### Xem các quy tắc iptables đã được tạo:
Kiểm tra các quy tắc NAT

```console
iptables -t nat -L -n -v | grep virbr1
```

Kiểm tra các quy tắc chuyển tiếp

```console
iptables -L FORWARD -n -v | grep virbr1

```

Xem tất cả các quy tắc cho mạng ảo

```console
siptables-save | grep virbr1
```

### 2. Chế độ định tuyến
Chế độ định tuyến (Routed mode) kết nối các máy ảo với mạng vật lý thông qua định tuyến thay vì bắc cầu (bridging), qua đó duy trì các mạng con riêng biệt.

**Đặc trưng**:

* Các máy ảo sử dụng địa chỉ IP có thể định tuyến
* Không thực hiện chuyển đổi NAT
* Yêu cầu cấu hình định tuyến tĩnh trên mạng vật lý
* Kết nối ở Lớp 3 (Layer 3)

**Tạo mạng định tuyến:**

```console
cat > routed-network.xml << 'EOF'
<network>
  <name>routed-network</name>
  <forward mode='route' dev='eth0'/>
  <bridge name='virbr-route' stp='on' delay='0'/>
  <ip address='10.10.10.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.10.10.10' end='10.10.10.250'/>
    </dhcp>
  </ip>
</network>
EOF
virsh net-define routed-network.xml
virsh net-start routed-network
virsh net-autostart routed-network
```

**Cấu hình định tuyến máy chủ:**
```console
# Bật chuyển tiếp IP
echo 1 > /proc/sys/net/ipv4/ip_forward

# Thiết lập vĩnh viễn
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -p

# Thêm quy tắc tường lửa
iptables -A FORWARD -i virbr-route -j ACCEPT
iptables -A FORWARD -o virbr-route -j ACCEPT
```

### 3. Chế độ Bridge
Chế độ Bridge kết nối trực tiếp các máy ảo với mạng vật lý, khiến chúng xuất hiện như các máy chủ vật lý trên cùng một mạng.

**Đặc trưng:**

* Các máy ảo nhận địa chỉ IP từ DHCP của mạng vật lý
* Kết nối trực tiếp ở lớp 2 (Layer 2)
* Các máy ảo hiển thị trên mạng vật lý
* Hiệu suất tối ưu

**Điều kiện tiên quyết:**  

```console
# Cài đặt các tiện ích cầu nối
apt install bridge-utils  # Debian/Ubuntu
dnf install bridge-utils  # RHEL/CentOS

# Kiểm tra các cầu nối hiện có
brctl show
ip link show type bridge
```

**Tạo Host Bridge (Netplan - Ubuntu/Debian):**  

```console
# Backup existing configuration
cp /etc/netplan/01-netcfg.yaml /etc/netplan/01-netcfg.yaml.bak

# Tạo cấu hình cầu nối
cat > /etc/netplan/01-netcfg.yaml << 'EOF'
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: no
      dhcp6: no
  bridges:
    br0:
      interfaces: [ens18]
      dhcp4: yes
      parameters:
        stp: true
        forward-delay: 4
EOF

# Áp dụng cấu hình
netplan apply

# Xác minh cầu nối
ip addr show br0
brctl show br0
```

**Tạo Host Bridge (NetworkManager - RHEL/CentOS):**

```console
# Tạo cầu nối
nmcli connection add type bridge ifname br0 con-name br0

# Thêm giao diện vật lý vào cầu nối
nmcli connection add type bridge-slave ifname ens18 master br0

# Cấu hình IP cho cầu nối (DHCP hoặc tĩnh)
nmcli connection modify br0 ipv4.method auto

# Kích hoạt cầu nối
nmcli connection up br0

# Xác minh
nmcli connection show
bridge link show
```

**Tạo mạng Bridge cho Libvirt:**

```console
cat > bridge-network.xml << 'EOF'
<network>
  <name>bridge-network</name>
  <forward mode='bridge'/>
  <bridge name='br0'/>
</network>
EOF

virsh net-define bridge-network.xml
virsh net-start bridge-network
virsh net-autostart bridge-network
```

**Gắn VM vào Bridge:**

```console
# Gắn vào máy ảo hiện có
virsh attach-interface ubuntu-vm bridge br0 --model virtio --config --live

# Hoặc tạo máy ảo với chế độ cầu nối.
virt-install \
  --name ubuntu-bridge \
  --memory 2048 \
  --vcpus 2 \
  --disk path=/var/lib/libvirt/images/ubuntu-bridge.qcow2,size=20 \
  --network bridge=br0,model=virtio \
  --os-variant ubuntu22.04 \
  --cdrom /path/to/ubuntu.iso
```

### 4. Chế độ Cô lập
Chế độ cô lập (Isolated mode) tạo ra một mạng lưới nơi các máy ảo có thể giao tiếp với nhau nhưng không có kết nối với máy chủ vật lý (host) hoặc các mạng bên ngoài.

**Đặc trưng:**

* Cách ly mạng hoàn toàn
* Các máy ảo chỉ có thể giao tiếp với nhau
* Không thể truy cập Internet hoặc máy chủ vật lý (host)
* Hữu ích cho việc kiểm thử bảo mật

**Tạo mạng cô lập:**

```console
cat > isolated-network.xml << 'EOF'
<network>
  <name>isolated</name>
  <bridge name='virbr-iso' stp='on' delay='0'/>
  <domain name='isolated.local'/>
  <ip address='10.0.0.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.0.0.10' end='10.0.0.250'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define isolated-network.xml
virsh net-start isolated
virsh net-autostart isolated

# Xác minh rằng không có tính năng chuyển tiếp nào được cấu hình.
virsh net-dumpxml isolated | grep forward
# Không có thành phần chuyển tiếp.
```

### 5. Chế độ Mở/Chuyển tiếp
Chế độ Mở chuyển tiếp toàn bộ lưu lượng mà không áp dụng các hạn chế về NAT hay định tuyến.

```console
cat > open-network.xml << 'EOF'
<network>
  <name>open-network</name>
  <forward mode='open'/>
  <bridge name='virbr-open' stp='on' delay='0'/>
  <ip address='172.16.0.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='172.16.0.10' end='172.16.0.250'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define open-network.xml
virsh net-start open-network
```

## Cấu hình mạng nâng cao
### Gán IP tĩnh qua DHCP
```console
# Lấy địa chỉ MAC của máy ảo
virsh dumpxml ubuntu-vm | grep "mac address"
# Output: <mac address='52:54:00:xx:xx:xx'/>

# Cập nhật mạng với mục nhập DHCP tĩnh
virsh net-update default add ip-dhcp-host \
  "<host mac='52:54:00:xx:xx:xx' name='ubuntu-vm' ip='192.168.122.100'/>" \
  --live --config

# Xác minh
virsh net-dhcp-leases default

# Thêm nhiều gán tĩnh
cat > dhcp-hosts.xml << 'EOF'
<host mac='52:54:00:11:11:11' name='web1' ip='192.168.122.11'/>
<host mac='52:54:00:22:22:22' name='web2' ip='192.168.122.12'/>
<host mac='52:54:00:33:33:33' name='db1' ip='192.168.122.20'/>
EOF

# Thêm từng máy chủ
while IFS= read -r line; do
    virsh net-update default add ip-dhcp-host "$line" --live --config
done < dhcp-hosts.xml

```

### Chuyển tiếp cổng trong mạng NAT
```console
# Chuyển tiếp cổng máy chủ sang máy ảo
virsh net-update default add-last ip-forward \
  "<forward proto='tcp' address='0.0.0.0' port='8080' dev='virbr0'> \
   <interface dev='eth0'/> \
   <backend dev='eth0'/> \
   </forward>" \
  --live --config

# Sử dụng trực tiếp iptables để kiểm soát tốt hơn
# Chuyển tiếp cổng 80 của máy chủ sang cổng 80 của máy ảo.
iptables -t nat -A PREROUTING -p tcp --dport 8080 \
  -j DNAT --to-destination 192.168.122.100:80

# Cho phép chuyển tiếp
iptables -A FORWARD -d 192.168.122.100 -p tcp --dport 80 -j ACCEPT

# Thiết lập vĩnh viễn (Ubuntu/Debian)
apt install iptables-persistent
iptables-save > /etc/iptables/rules.v4
```

### Nhiều dải IP trên cùng một mạng
```console
cat > multi-ip-network.xml << 'EOF'
<network>
  <name>multi-ip</name>
  <forward mode='nat'/>
  <bridge name='virbr-multi' stp='on' delay='0'/>
  <ip address='192.168.100.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.100.10' end='192.168.100.100'/>
    </dhcp>
  </ip>
  <ip address='10.10.10.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.10.10.10' end='10.10.10.100'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define multi-ip-network.xml
virsh net-start multi-ip
```

### Cấu hình IPv6
```console
cat > ipv6-network.xml << 'EOF'
<network>
  <name>ipv6-network</name>
  <forward mode='nat'/>
  <bridge name='virbr-v6' stp='on' delay='0'/>
  <ip address='192.168.150.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.150.10' end='192.168.150.250'/>
    </dhcp>
  </ip>
  <ip family='ipv6' address='fd00:cafe:cafe::1' prefix='64'>
    <dhcp>
      <range start='fd00:cafe:cafe::10' end='fd00:cafe:cafe::ff'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define ipv6-network.xml
virsh net-start ipv6-network

# Bật tính năng chuyển tiếp IPv6 trên máy chủ
echo 1 > /proc/sys/net/ipv6/conf/all/forwarding
echo "net.ipv6.conf.all.forwarding = 1" >> /etc/sysctl.conf
sysctl -p
```

### Cấu hình VLAN
```console
# Tạo mạng có gắn thẻ VLAN
cat > vlan-network.xml << 'EOF'
<network>
  <name>vlan100</name>
  <forward mode='bridge'/>
  <bridge name='br0'/>
  <vlan trunk='yes'>
    <tag id='100'/>
  </vlan>
</network>
EOF

virsh net-define vlan-network.xml
virsh net-start vlan100

# Cấu hình máy ảo với VLAN
virsh edit ubuntu-vm

# Thêm VLAN vào giao diện:
<interface type='network'>
  <source network='vlan100'/>
  <vlan>
    <tag id='100'/>
  </vlan>
</interface>
```

### Cấu hình SR-IOV
SR-IOV (Single Root I/O Virtualization) mang lại hiệu năng mạng gần như tương đương với hiệu năng gốc bằng cách cho phép các máy ảo truy cập trực tiếp vào các giao diện mạng vật lý.

```console
# Kiểm tra xem NIC có hỗ trợ SR-IOV hay không.
lspci | grep Ethernet
lspci -v -s <device-id> | grep SR-IOV

# Bật SR-IOV trên NIC
echo 4 > /sys/class/net/ens1f0/device/sriov_numvfs

# Thiết lập vĩnh viễn (thêm vào /etc/rc.local hoặc quy tắc udev)
cat > /etc/udev/rules.d/sriov.rules << 'EOF'
ACTION=="add", SUBSYSTEM=="net", ENV{ID_NET_NAME}=="ens1f0", \
RUN+="/bin/bash -c 'echo 4 > /sys/class/net/ens1f0/device/sriov_numvfs'"
EOF

# Liệt kê các hàm ảo
lspci | grep Virtual

# Tạo nhóm mạng cho các VF
cat > sriov-network.xml << 'EOF'
<network>
  <name>sriov-network</name>
  <forward mode='hostdev' managed='yes'>
    <pf dev='ens1f0'/>
  </forward>
</network>
EOF

virsh net-define sriov-network.xml
virsh net-start sriov-network

# Gắn giao diện SR-IOV vào máy ảo
virsh edit ubuntu-vm

<interface type='network'>
  <source network='sriov-network'/>
</interface>
```

## Tối ưu hóa hiệu năng mạng
### Multi-queue virtio-net

```console
# Bật tính năng đa hàng đợi trong cấu hình máy ảo
virsh edit ubuntu-vm

<interface type='network'>
  <source network='default'/>
  <model type='virtio'/>
  <driver name='vhost' queues='4'/>
</interface>

# Khởi động lại máy ảo
virsh shutdown ubuntu-vm
virsh start ubuntu-vm

# Bên trong máy ảo khách, hãy bật tính năng đa hàng đợi (multi-queue).
ethtool -L eth0 combined 4

# Xác minh
ethtool -l eth0
```

### Tối ưu hóa vhost-net

```console
# Đảm bảo mô-đun vhost-net đã được nạp.
lsmod | grep vhost
modprobe vhost_net

# Thiết lập vĩnh viễn
echo "vhost_net" >> /etc/modules

# Sử dụng vhost trong cấu hình mạng
virsh edit ubuntu-vm

<interface type='network'>
  <source network='default'/>
  <model type='virtio'/>
  <driver name='vhost' txmode='iothread' ioeventfd='on' event_idx='on'/>
</interface>
```

### Giảm tải mạng

```console
# Cấu hình tính năng giảm tải trong tệp XML của máy ảo
<interface type='network'>
  <source network='default'/>
  <model type='virtio'/>
  <driver name='vhost'>
    <host csum='on' gso='on' tso4='on' tso6='on' ecn='on' ufo='on'/>
    <guest csum='on' tso4='on' tso6='on' ecn='on' ufo='on'/>
  </driver>
</interface>

# Bên trong máy ảo, xác minh tính năng giảm tải.
ethtool -k eth0 | grep offload
```

### Giới hạn băng thông

```console
# Giới hạn băng thông cho giao diện máy ảo
virsh domiftune ubuntu-vm vnet0 \
  --inbound 100000,150000,200000 \
  --outbound 80000,120000,160000

# Các tham số: trung bình, đỉnh, bùng phát (tính bằng KiB/s)

# Xác minh
virsh domiftune ubuntu-vm vnet0

# Thiết lập vĩnh viễn
virsh edit ubuntu-vm

<interface type='network'>
  <source network='default'/>
  <bandwidth>
    <inbound average='100000' peak='150000' burst='200000'/>
    <outbound average='80000' peak='120000' burst='160000'/>
  </bandwidth>
</interface>
```

## Cấu hình DNS
### Các mục DNS tùy chỉnh

```console
# Thêm các mục DNS tùy chỉnh vào mạng
virsh net-update default add dns-host \
  "<host ip='192.168.122.100'><hostname>web.local</hostname></host>" \
  --live --config

# Thêm nhiều tên máy chủ vào cùng một địa chỉ IP
virsh net-update default add dns-host \
  "<host ip='192.168.122.100'><hostname>web.local</hostname><hostname>www.local</hostname></host>" \
  --live --config

# Thêm bộ chuyển tiếp DNS
virsh net-update default add dns-forwarder \
  "<forwarder addr='8.8.8.8'/>" \
  --live --config

virsh net-update default add dns-forwarder \
  "<forwarder addr='8.8.4.4'/>" \
  --live --config
```

### Cấu hình DNS trong XML

```console
cat > dns-network.xml << 'EOF'
<network>
  <name>dns-network</name>
  <forward mode='nat'/>
  <bridge name='virbr-dns'/>
  <domain name='example.local' localOnly='yes'/>
  <dns>
    <forwarder addr='8.8.8.8'/>
    <forwarder addr='1.1.1.1'/>
    <host ip='192.168.200.10'>
      <hostname>web1.example.local</hostname>
      <hostname>www.example.local</hostname>
    </host>
    <host ip='192.168.200.20'>
      <hostname>db1.example.local</hostname>
    </host>
    <txt name='example.local' value='v=spf1 a mx ~all'/>
    <srv service='ldap' protocol='tcp' domain='example.local'
         target='ldap.example.local' port='389' priority='10' weight='10'/>
  </dns>
  <ip address='192.168.200.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.200.10' end='192.168.200.250'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define dns-network.xml
virsh net-start dns-network
```

## Tường lửa và Bảo mật
### Các quy tắc tường lửa tùy chỉnh

```console
# Thêm các quy tắc tùy chỉnh vào mạng
cat > secure-network.xml << 'EOF'
<network>
  <name>secure-network</name>
  <forward mode='nat'/>
  <bridge name='virbr-sec'/>
  <ip address='192.168.180.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.180.10' end='192.168.180.250'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define secure-network.xml
virsh net-start secure-network

# Thêm các quy tắc iptables tùy chỉnh
iptables -I FORWARD -i virbr-sec -o virbr-sec -p tcp --dport 22 -j ACCEPT
iptables -I FORWARD -i virbr-sec -o virbr-sec -p tcp --dport 80 -j ACCEPT
iptables -I FORWARD -i virbr-sec -o virbr-sec -p tcp --dport 443 -j ACCEPT
iptables -I FORWARD -i virbr-sec -o virbr-sec -j DROP

# Lưu quy tắc
iptables-save > /etc/iptables/rules.v4
```

### Lọc mạng (nwfilter)

```console
# Liệt kê các bộ lọc khả dụng
virsh nwfilter-list

# Xem định nghĩa bộ lọc
virsh nwfilter-dumpxml clean-traffic

# Tạo bộ lọc tùy chỉnh
cat > custom-filter.xml << 'EOF'
<filter name='custom-filter' chain='root'>
  <uuid>12345678-1234-1234-1234-123456789abc</uuid>
  <!-- Allow ARP -->
  <rule action='accept' direction='inout' priority='100'>
    <mac protocolid='arp'/>
  </rule>
  <!-- Allow IPv4 -->
  <rule action='accept' direction='out' priority='100'>
    <ip/>
  </rule>
  <!-- Allow incoming HTTP/HTTPS -->
  <rule action='accept' direction='in' priority='100'>
    <tcp dstportstart='80' dstportend='80'/>
  </rule>
  <rule action='accept' direction='in' priority='100'>
    <tcp dstportstart='443' dstportend='443'/>
  </rule>
  <!-- Drop everything else -->
  <rule action='drop' direction='inout' priority='1000'>
    <all/>
  </rule>
</filter>
EOF

virsh nwfilter-define custom-filter.xml

# Áp dụng bộ lọc cho giao diện máy ảo
virsh edit ubuntu-vm

<interface type='network'>
  <source network='default'/>
  <filterref filter='custom-filter'/>
</interface>
```

### Giới hạn tốc độ và QoS

```console
# Tạo mạng hỗ trợ QoS
cat > qos-network.xml << 'EOF'
<network>
  <name>qos-network</name>
  <forward mode='nat'/>
  <bridge name='virbr-qos'/>
  <bandwidth>
    <inbound average='50000' peak='100000' burst='50000'/>
    <outbound average='50000' peak='100000' burst='50000'/>
  </bandwidth>
  <ip address='192.168.190.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.190.10' end='192.168.190.250'/>
    </dhcp>
  </ip>
</network>
EOF

virsh net-define qos-network.xml
virsh net-start qos-network
```

## Mạng đa máy chủ (Multi-Host Networking)
### Kết nối các máy ảo giữa các máy chủ vật lý
**Sử dụng VXLAN:**

```console
# Host 1
ip link add vxlan100 type vxlan id 100 \
  local 10.0.0.1 \
  remote 10.0.0.2 \
  dev eth0 \
  dstport 4789

ip link set vxlan100 up
brctl addbr br-vxlan
brctl addif br-vxlan vxlan100
ip link set br-vxlan up

# Tạo mạng libvirt
cat > vxlan-network.xml << 'EOF'
<network>
  <name>vxlan-network</name>
  <forward mode='bridge'/>
  <bridge name='br-vxlan'/>
</network>
EOF

virsh net-define vxlan-network.xml
virsh net-start vxlan-network

# Host 2 (đảo ngược địa chỉ IP cục bộ/từ xa)
ip link add vxlan100 type vxlan id 100 \
  local 10.0.0.2 \
  remote 10.0.0.1 \
  dev eth0 \
  dstport 4789
# ... lặp lại cấu hình cầu nối
```

**Sử dụng đường hầm GRE:**

```console
# Host 1
ip tunnel add gre1 mode gre remote 10.0.0.2 local 10.0.0.1 ttl 255
ip link set gre1 up
ip addr add 172.16.0.1/30 dev gre1

brctl addbr br-gre
brctl addif br-gre gre1
ip link set br-gre up

# Host 2
ip tunnel add gre1 mode gre remote 10.0.0.1 local 10.0.0.2 ttl 255
ip link set gre1 up
ip addr add 172.16.0.2/30 dev gre1
# ... lặp lại cấu hình cầu nối
```

## Khắc phục sự cố mạng
### Các lệnh chẩn đoán

```console
# Liệt kê tất cả các mạng
virsh net-list --all

# Kiểm tra trạng thái mạng
virsh net-info default

# Xem XML mạng
virsh net-dumpxml default

# Hiển thị các bản ghi cấp phát DHCP
virsh net-dhcp-leases default

# Kiểm tra trạng thái cầu nối
brctl show
bridge link show

# Xem cơ sở dữ liệu chuyển tiếp của cầu nối
bridge fdb show br virbr0

# Kiểm tra cấu hình dnsmasq
ps aux | grep dnsmasq
cat /var/lib/libvirt/dnsmasq/default.conf

# Xem các bản ghi cấp phát IP của dnsmasq
cat /var/lib/libvirt/dnsmasq/default.leases

# Kiểm tra các quy tắc iptables
iptables -t nat -L -n -v
iptables -L FORWARD -n -v

# Giám sát lưu lượng mạng
tcpdump -i virbr0 -n
```

### Các vấn đề thường gặp và giải pháp
**Vấn đề: Mạng không khởi động được**

```console
# Kiểm tra lỗi
virsh net-start default
# Xem chi tiết lỗi

# Kiểm tra xem cầu nối có tồn tại hay không
ip link show virbr0

# Xác minh rằng dnsmasq không đang chạy ở nơi khác.
ps aux | grep dnsmasq

# Kiểm tra xung đột cổng
ss -tulpn | grep :53

# Khởi động lại libvirtd
systemctl restart libvirtd
virsh net-start default
```

**Sự cố: Các máy ảo không nhận được địa chỉ IP.**

```console
# Kiểm tra dải DHCP
virsh net-dumpxml default | grep range

# Xác minh rằng dnsmasq đang chạy
ps aux | grep dnsmasq

# Kiểm tra xem tường lửa có cho phép DHCP hay không.
iptables -L INPUT -n | grep 67

# Khởi động lại mạng
virsh net-destroy default
virsh net-start default

# Bên trong máy ảo, gửi yêu cầu DHCP
dhclient -v eth0  # hoặc
dhcpcd eth0
```

### Vấn đề: Các máy ảo không thể truy cập Internet (NAT)

```console
# Kiểm tra chuyển tiếp IP
cat /proc/sys/net/ipv4/ip_forward
# Nên là 1

# Bật nếu cần
echo 1 > /proc/sys/net/ipv4/ip_forward

# Kiểm tra các quy tắc NAT
iptables -t nat -L POSTROUTING -n -v

# Kiểm tra các quy tắc chuyển tiếp
iptables -L FORWARD -n -v

# Kiểm tra định tuyến mặc định trên máy ảo
ip route show
```

**Vấn đề: Mạng cầu nối (bridged network) không hoạt động**

```console
# Kiểm tra xem giao diện vật lý có đang ở chế độ bridge hay không.
brctl show br0

# Xác minh giao diện vật lý không có địa chỉ IP.
ip addr show ens18
# Không được hiển thị địa chỉ mạng (inet address)

# Kiểm tra xem cầu nối có địa chỉ IP hay không.
ip addr show br0
# Cần có địa chỉ IP

# Kiểm tra kết nối
ping -c 4 <gateway-ip>

# Kiểm tra việc chuyển tiếp của cầu nối
cat /sys/class/net/br0/bridge/stp_state
```

## Giám sát và Thống kê Mạng
### Giám sát thời gian thực
Theo dõi số liệu thống kê giao diện

```console
watch -n 1 'virsh domifstat ubuntu-vm vnet0'
```

Giám sát tất cả các máy ảo

```bash
#!/bin/bash
while true; do
    clear
    echo "Network Statistics - $(date)"
    echo "================================"
    for vm in $(virsh list --name); do
        echo "VM: $vm"
        virsh domiflist "$vm"
        for iface in $(virsh domiflist "$vm" | awk 'NR>2 {print $1}'); do
            virsh domifstat "$vm" "$iface"
        done
        echo ""
    done
    sleep 5
done
```

Sử dụng iftop để giám sát băng thông.

```console
iftop -i virbr0
```

Sử dụng nethogs để giám sát theo từng tiến trình.

```console
nethogs virbr0
```

### Thu thập các chỉ số mạng
```bash
#!/bin/bash
# Network metrics collection script

LOG_FILE="/var/log/vm-network-stats.log"

collect_stats() {
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')

    for vm in $(virsh list --name); do
        for iface in $(virsh domiflist "$vm" | awk 'NR>2 {print $1}'); do
            stats=$(virsh domifstat "$vm" "$iface" | grep -E 'rx_bytes|tx_bytes')
            echo "$timestamp | $vm | $iface | $stats" >> "$LOG_FILE"
        done
    done
}

# Run every 60 seconds
while true; do
    collect_stats
    sleep 60
done
```

## Các phương pháp tốt nhất
### Các nguyên tắc thiết kế mạng
* Phân tách các mạng theo chức năng:

```console
# Management network
virsh net-define mgmt-network.xml

# Application network
virsh net-define app-network.xml

# Database network (isolated)
virsh net-define db-network.xml

# DMZ network
virsh net-define dmz-network.xml
```

* Sử dụng DHCP tĩnh cho các máy chủ:

```console
# Assign consistent IPs to server VMs
virsh net-update default add ip-dhcp-host \
  "<host mac='52:54:00:aa:bb:cc' name='web-server' ip='192.168.122.10'/>"
```

* Triển khai cách ly mạng:
    * Sử dụng các mạng cô lập để thử nghiệm
    * Sử dụng các mạng riêng biệt cho môi trường vận hành thực tế (production)
    * Thiết lập các quy tắc tường lửa giữa các mạng

* Ghi lại cấu trúc liên kết mạng:

```console
# Tạo tài liệu mạng
virsh net-list --all > /docs/networks.txt
for net in $(virsh net-list --name); do
    virsh net-dumpxml "$net" > "/docs/network-${net}.xml"
done
```

### Tăng cường bảo mật (Security Hardening)
```console
# Vô hiệu hóa các mạng không sử dụng
virsh net-destroy unused-network
virsh net-autostart unused-network --disable
```

* Thiết lập các quy tắc tường lửa nghiêm ngặt
*  Sử dụng nwfilter để lọc ở cấp độ máy ảo (VM)
* Bật STP trên các bridge
* Sử dụng VLAN để phân tách mạng
* Triển khai lọc địa chỉ MAC
* Bật tính năng bảo mật cổng (port security)

## Kết luận
Các khả năng về mạng ảo của Libvirt mang lại nền tảng mạnh mẽ và linh hoạt để xây dựng các cấu trúc liên kết mạng phức tạp trong môi trường ảo hóa. Từ các cấu hình NAT đơn giản đến những kịch bản đa máy chủ (multi-host) nâng cao sử dụng VXLAN và SR-IOV, Libvirt giúp trừu tượng hóa sự phức tạp của hệ thống mạng Linux nhưng vẫn đảm bảo khả năng kiểm soát toàn diện khi cần thiết.

Những điểm chính:

* Lựa chọn chế độ mạng phù hợp (NAT, bridge, routed, isolated) dựa trên các yêu cầu cụ thể
* Tận dụng DHCP tĩnh để đảm bảo việc cấp phát địa chỉ IP có thể dự đoán trước
* Sử dụng các bộ lọc mạng và iptables để đảm bảo tính bảo mật
* Tối ưu hóa hiệu năng bằng cách sử dụng virtio, multi-queue và vhost-net
* Triển khai các quy trình giám sát và khắc phục sự cố thích hợp
* Thiết kế mạng với trọng tâm là phân đoạn và cô lập

Hãy nắm vững kỹ năng cấu hình mạng libvirt để xây dựng cơ sở hạ tầng ảo hóa an toàn, hiệu quả và có khả năng mở rộng, đáp ứng các yêu cầu vận hành thực tế (production) đồng thời duy trì sự linh hoạt cho môi trường phát triển và thử nghiệm. Lớp mạng đóng vai trò then chốt đối với hiệu suất và tính bảo mật của máy ảo; vì vậy, hãy chú trọng việc cấu hình và giám sát đúng cách để đạt được kết quả tối ưu.
