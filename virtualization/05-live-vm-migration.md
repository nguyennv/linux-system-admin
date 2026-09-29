# Di trú máy ảo trực tiếp: Hướng dẫn toàn tập
## Giới thiệu
Di trú trực tiếp (live migration) là một trong những tính năng mạnh mẽ nhất của các nền tảng ảo hóa hiện đại, cho phép di trú máy ảo giữa các máy chủ vật lý mà không gây gián đoạn dịch vụ (zero downtime). Khả năng này đóng vai trò thiết yếu đối với việc cân bằng tải, bảo trì phần cứng, phục hồi sau sự cố và tối ưu hóa hiệu suất sử dụng tài nguyên trong môi trường vận hành thực tế (production).

Hướng dẫn toàn diện này sẽ đi sâu vào quy trình di trú trực tiếp KVM/QEMU sử dụng libvirt, bao quát mọi khía cạnh từ thiết lập cơ bản đến các kịch bản di trú nâng cao. Dù bạn đang quản lý một cụm máy chủ nhỏ hay điều phối việc di trú giữa các trung tâm dữ liệu, việc nắm vững các chi tiết kỹ thuật, yêu cầu hệ thống và các phương pháp thực hành tốt nhất là yếu tố then chốt để triển khai thành công.

Cơ chế di trú trực tiếp hoạt động bằng cách chuyển giao bộ nhớ, trạng thái CPU và trạng thái thiết bị của máy ảo đang chạy từ máy chủ này sang máy chủ khác trong khi máy ảo vẫn tiếp tục hoạt động. Quá trình này diễn ra một cách trong suốt đối với các ứng dụng và người dùng; chỉ có một tác động nhỏ đến hiệu năng trong giai đoạn chuyển đổi cuối cùng. Các giải pháp hiện đại có thể di trú máy ảo chỉ trong vài giây với thời gian gián đoạn gần như không thể nhận thấy.

Sau khi hoàn thành hướng dẫn này, bạn sẽ nắm vững cách cấu hình di trú trực tiếp, hiểu rõ các loại hình di trú khác nhau, biết cách tối ưu hóa hiệu suất di trú cũng như khắc phục các sự cố thường gặp trong môi trường vận hành thực tế.

## Tìm hiểu về di trú trực tiếp (Live Migration)
### di trú trực tiếp (Live Migration) là gì?
Di trú trực tiếp (hay còn gọi là di trú nóng) là quá trình chuyển một máy ảo đang hoạt động từ máy chủ vật lý này sang máy chủ vật lý khác mà không cần dừng máy ảo hay làm gián đoạn các dịch vụ đang chạy. Toàn bộ trạng thái của máy ảo—bao gồm nội dung bộ nhớ, các thanh ghi CPU và trạng thái thiết bị—đều được chuyển sang máy chủ đích.

### Các giai đoạn của quy trình di trú
```
┌──────────────────────────────────────────────┐
│   Giai đoạn 1: Thiết lập trước khi di trú    │
│   - Xác minh khả năng tương thích            │
│   - Kiểm tra các tài nguyên                  │
│   - Thiết lập kết nối                        │
└────────────────────┬─────────────────────────┘
                     │
┌────────────────────▼─────────────────────────┐
│   Giai đoạn 2: Sao chép trước lặp lại        │
│   - Sao chép các trang bộ nhớ                │
│   - Theo dõi các trang bẩn                   │
│   - Sao chép lại các trang đã sửa đổi        │
└────────────────────┬─────────────────────────┘
                     │
┌────────────────────▼─────────────────────────┐
│   Giai đoạn 3: Dừng và Sao chép              │
│   - Tạm dừng máy ảo trong chốc lát           │
│   - Chuyển trạng thái còn lại                │
│   - Thời gian gián đoạn khoảng 100-500ms     │
└────────────────────┬─────────────────────────┘
                     │
┌────────────────────▼─────────────────────────┐
│   Giai đoạn 4: Sau chuyển đổi                │
│   - Tiếp tục chạy VM tại đích                │
│   - Dọn dẹp nguồn                            │
│   - Cập nhật mạng/lưu trữ                    │
└──────────────────────────────────────────────┘
```

### Các loại hình di trú
1. Di trú trực tiếp (Di trú nóng)
    * Máy ảo vẫn đang chạy trong suốt quá trình
    * Bộ nhớ và trạng thái được sao chép trong khi máy ảo đang thực thi
    * Tạm dừng ngắn trong quá trình chuyển đổi cuối cùng
    * Không có thời gian ngừng hoạt động rõ rệt
2. Di trú ngoại tuyến (Di trú nguội)
    * Máy ảo đã dừng trước khi di trú
    * Truyền tải nhanh hơn (không theo dõi trang bẩn)
    * Có thời gian ngừng hoạt động trong toàn bộ quá trình
    * Đơn giản hơn, đáng tin cậy hơn
3. Di trú lưu trữ trực tiếp
    * Di trú ảnh đĩa máy ảo trong khi đang chạy
    * Có thể kết hợp với di trú trực tiếp
    * Hữu ích cho việc bảo trì lưu trữ
4. Di trú sau sao chép
    * Khởi động máy ảo trên đích ngay lập tức
    * Lấy các trang bộ nhớ theo yêu cầu
    * Giảm tổng thời gian di trú
    * Rủi ro nếu mạng gặp sự cố

## Các điều kiện tiên quyết và yêu cầu
### Yêu cầu cấu hình máy chủ
**Cả máy chủ nguồn và máy chủ đích đều phải có:**

```console
# 1. Cùng kiến trúc CPU
lscpu | grep "Model name"
lscpu | grep "Architecture"

# 2. Các tính năng CPU tương thích
virsh capabilities | grep -A 20 "<cpu>"

# 3. Cùng phiên bản libvirt (hoặc tương thích)
virsh version

# 4. Đã cài đặt KVM/QEMU
which qemu-system-x86_64
lsmod | grep kvm

# 5. Đã cấu hình lưu trữ chia sẻ hoặc di trú lưu trữ
# (dành cho các tệp ảnh đĩa)

# 6. Kết nối mạng
ping destination-host
```

### Yêu cầu về mạng

```console
# Mạng có độ trễ thấp (ưu tiên mạng chuyên dụng)
ping -c 100 destination-host | tail -n 1
# RTT nên dưới 1ms để đạt hiệu suất tốt nhất.

# Băng thông cao (tối thiểu 1 Gbps, khuyến nghị 10 Gbps)
iperf3 -s  # Trên máy chủ đích
iperf3 -c destination-host -t 30  # Trên máy chủ nguồn
```

**Mở các cổng cần thiết**

* libvirt mặc định: 16509 (TLS) hoặc 16514 (TCP)
* qemu+ssh: 22 (SSH)

### Yêu cầu lưu trữ
**Lựa chọn 1: Lưu trữ dùng chung (Khuyên dùng)**

```console
# Lưu trữ chia sẻ NFS
# Mount cùng một NFS share trên cả hai máy chủ.
mount -t nfs nfs-server:/exports/vms /var/lib/libvirt/images

# Xác minh rằng cả hai máy chủ đều nhìn thấy cùng một bộ lưu trữ.
ls -la /var/lib/libvirt/images/

# Thêm vào /etc/fstab để duy trì cấu hình sau khi khởi động lại.
echo "nfs-server:/exports/vms /var/lib/libvirt/images nfs defaults 0 0" >> /etc/fstab
```

**Tùy chọn 2: Di trú lưu trữ**

* Sao chép các ảnh đĩa trong quá trình di trú
* Yêu cầu băng thông đủ lớn
* Mất nhiều thời gian hơn so với di trú sử dụng bộ lưu trữ chia sẻ

### Khả năng tương thích với CPU

```console
# Kiểm tra các cờ CPU trên cả hai máy chủ.
virsh capabilities | grep features

# Hãy thận trọng khi sử dụng host-passthrough hoặc host-model.
# Tốt hơn: Sử dụng tên kiểu CPU để đảm bảo tính tương thích.

# Xem các mẫu CPU có sẵn
virsh domcapabilities | grep -A 50 cpu
qemu-system-x86_64 -cpu help

# Cấu hình máy ảo với CPU tương thích
virsh edit vm-name
```

```xml
<cpu mode='custom' match='exact'>
  <model>Broadwell</model>
</cpu>
```

Hoặc sử dụng mô hình host kết hợp kiểm tra tính năng.

```xml
<cpu mode='host-model'>
  <model fallback='forbid'/>
</cpu>
```

## Thiết lập bộ lưu trữ chia sẻ
### Cấu hình NFS
**Trên máy chủ NFS:**

```console
# Cài đặt máy chủ NFS
apt install nfs-kernel-server  # Debian/Ubuntu
dnf install nfs-utils  # RHEL/CentOS

# Tạo thư mục xuất
mkdir -p /exports/vms
chown -R qemu:qemu /exports/vms
chmod 755 /exports/vms

# Cấu hình xuất dữ liệu
cat >> /etc/exports << 'EOF'
/exports/vms 192.168.1.0/24(rw,sync,no_root_squash,no_subtree_check)
EOF

# Áp dụng các thay đổi
exportfs -arv

# Khởi động NFS
systemctl enable nfs-server
systemctl start nfs-server

# Xác minh dữ liệu xuất
showmount -e localhost
```

**Trên các máy chủ KVM (cả nguồn và đích):**

```console
# Cài đặt máy khách NFS
apt install nfs-common  # Debian/Ubuntu
dnf install nfs-utils  # RHEL/CentOS

# Tạo điểm gắn
mkdir -p /var/lib/libvirt/images

# Gắn kết thư mục chia sẻ NFS
mount -t nfs nfs-server:/exports/vms /var/lib/libvirt/images

# Kiểm tra quyền ghi
touch /var/lib/libvirt/images/test
rm /var/lib/libvirt/images/test

# Thêm vào fstab
echo "nfs-server:/exports/vms /var/lib/libvirt/images nfs defaults 0 0" >> /etc/fstab

# Xác minh việc gắn kết
df -h | grep vms
```

### Cấu hình Pool lưu trữ
* Tạo pool lưu trữ trên bộ lưu trữ chia sẻ

```console
vi nfs-pool.xml
```

```xml
<pool type='dir'>
  <name>nfs-pool</name>
  <target>
    <path>/var/lib/libvirt/images</path>
  </target>
</pool>
```

* Định nghĩa pool trên cả hai máy chủ.

```console
virsh pool-define nfs-pool.xml
virsh pool-start nfs-pool
virsh pool-autostart nfs-pool
```

* Xác minh

```console
virsh pool-list
virsh pool-info nfs-pool
```

## Cấu hình các máy chủ cho quá trình di trú
### Cấu hình mạng
**Sử dụng libvirtd qua TCP (Không mã hóa - Chỉ dùng để thử nghiệm):**

```console
# Chỉnh sửa tệp /etc/libvirt/libvirtd.conf trên cả hai máy chủ.
sudo vim /etc/libvirt/libvirtd.conf

# Bỏ chú thích và sửa đổi:
listen_tls = 0
listen_tcp = 1
tcp_port = "16509"
auth_tcp = "none"  # Chỉ dành cho mục đích thử nghiệm!

# Chỉnh sửa /etc/default/libvirtd (Debian/Ubuntu)
sudo vim /etc/default/libvirtd
libvirtd_opts="--listen"

# Hoặc /etc/sysconfig/libvirtd (RHEL/CentOS)
LIBVIRTD_ARGS="--listen"

# Khởi động lại libvirtd
sudo systemctl restart libvirtd

# Xác nhận lắng nghe
ss -tulpn | grep 16509
```

**Sử dụng TLS cho libvirtd (An toàn - Môi trường sản xuất):**

```console
# Tạo chứng chỉ TLS (trên cơ quan cấp chứng chỉ)
mkdir -p /etc/pki/CA
cd /etc/pki/CA

# Tạo CA
certtool --generate-privkey > cakey.pem
cat > ca.info << EOF
cn = CA
ca
cert_signing_key
EOF
certtool --generate-self-signed --load-privkey cakey.pem \
  --template ca.info --outfile cacert.pem

# Tạo chứng chỉ máy chủ cho từng máy chủ
certtool --generate-privkey > serverkey.pem
cat > server.info << EOF
organization = MyOrg
cn = host1.example.com
tls_www_server
encryption_key
signing_key
EOF
certtool --generate-certificate --load-privkey serverkey.pem \
  --load-ca-certificate cacert.pem --load-ca-privkey cakey.pem \
  --template server.info --outfile servercert.pem

# Cài đặt chứng chỉ trên cả hai máy chủ.
sudo mkdir -p /etc/pki/libvirt/private
sudo cp cacert.pem /etc/pki/CA/
sudo cp servercert.pem /etc/pki/libvirt/
sudo cp serverkey.pem /etc/pki/libvirt/private/

# Cấu hình libvirtd cho TLS
sudo vim /etc/libvirt/libvirtd.conf
listen_tls = 1
listen_tcp = 0

# Khởi động lại libvirtd
sudo systemctl restart libvirtd
```

**Sử dụng SSH (Đơn giản nhất - Khuyên dùng):**
Không cần cấu hình libvirtd đặc biệt nào. Chỉ cần thiết lập xác thực bằng khóa SSH.

```console
# Trên máy chủ nguồn
ssh-keygen -t rsa -b 4096

# Sao chép khóa đến máy chủ đích
ssh-copy-id root@destination-host

# Kiểm tra kết nối
ssh root@destination-host 'virsh version'
```

Đây là phương pháp được khuyến nghị!

### Cấu hình tường lửa

```console
# Cho phép các cổng libvirt
# TCP: 16509 (non-TLS), 16514 (TLS)
# SSH: 22

# Debian/Ubuntu (UFW)
ufw allow 16509/tcp
ufw allow 16514/tcp
ufw allow 22/tcp

# RHEL/CentOS (firewalld)
firewall-cmd --permanent --add-port=16509/tcp
firewall-cmd --permanent --add-port=16514/tcp
firewall-cmd --permanent --add-service=ssh
firewall-cmd --reload

# trực tiếp qua iptables
iptables -A INPUT -p tcp --dport 16509 -j ACCEPT
iptables -A INPUT -p tcp --dport 16514 -j ACCEPT
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

## Thực hiện di trú trực tiếp
### Di trú trực tiếp cơ bản
**Sử dụng virsh (phương thức SSH - được khuyến nghị):**
Cú pháp: `virsh migrate [options] domain desturi [migrateuri] [dname]`

```console
# Di trú đơn giản sử dụng SSH
virsh migrate --live my-vm qemu+ssh://destination-host/system

# Với đầu ra chi tiết
virsh migrate --live --verbose my-vm qemu+ssh://destination-host/system

# Di trú liên tục (giữ lại cấu hình)
virsh migrate --live --persistent my-vm qemu+ssh://destination-host/system

# Hủy định nghĩa nguồn sau khi di trú
virsh migrate --live --persistent --undefinesource my-vm \
  qemu+ssh://destination-host/system
```

**Sử dụng kết nối TCP:**

```console
# Di trú TCP trực tiếp
virsh migrate --live my-vm qemu+tcp://destination-host/system

# Với cổng tùy chỉnh
virsh migrate --live my-vm qemu+tcp://destination-host:16509/system
```

**Theo dõi tiến độ di trú:**

```console
# Theo dõi trong một cửa sổ terminal riêng biệt
watch -n 1 'virsh domjobinfo my-vm'

# Hiển thị:
# - Time elapsed
# - Data processed
# - Data remaining
# - Memory transfer rate
# - Migration status
```

### Di trú ngang hàng
```console
# Di trú P2P (do máy chủ đích khởi xướng)
virsh migrate --live --p2p my-vm qemu+ssh://destination-host/system

# P2P với di trú qua đường hầm (được mã hóa)
virsh migrate --live --p2p --tunnelled my-vm \
  qemu+ssh://destination-host/system
```

**Ưu điểm:**

- Cấu trúc mạng đơn giản hơn
- Chỉ cần kết nối từ nguồn đến đích
- Tự động chọn URI đích

### Di trú với các tùy chọn tùy chỉnh
```console
# Chỉ định URI di trú để truyền dữ liệu
virsh migrate --live --p2p --tunnelled \
  --migrateuri tcp://192.168.100.1:49152 \
  my-vm qemu+ssh://destination-host/system

# Thiết lập giới hạn băng thông (MB/s)
virsh migrate --live --verbose --bandwidth 100 \
  my-vm qemu+ssh://destination-host/system

# Tạm dừng máy ảo đích sau khi di trú (để kiểm thử)
virsh migrate --live --suspend my-vm qemu+ssh://destination-host/system

# Thay đổi tên máy ảo tại đích
virsh migrate --live my-vm qemu+ssh://destination-host/system \
  --dname my-vm-migrated

# Di trú dữ liệu không an toàn (bỏ qua các bước kiểm tra an toàn - hãy thận trọng khi sử dụng!)
virsh migrate --live --unsafe my-vm qemu+ssh://destination-host/system
```

### Di trú dữ liệu lưu trữ trực tuyến
**Di trú máy ảo sử dụng bộ lưu trữ không chia sẻ:**

```console
# Sao chép đĩa trong quá trình di trú
virsh migrate --live --copy-storage-all my-vm \
  qemu+ssh://destination-host/system

# Chỉ sao chép các thay đổi gia tăng (nếu đĩa đã tồn tại một phần)
virsh migrate --live --copy-storage-inc my-vm \
  qemu+ssh://destination-host/system

# Chỉ định các đường dẫn đĩa đích
virsh migrate --live --copy-storage-all \
  --migrate-disks vda \
  my-vm qemu+ssh://destination-host/system
```

Quá trình này chậm hơn nhiều do phải sao chép dữ liệu trên đĩa! Hãy theo dõi bằng lệnh: `virsh domjobinfo my-vm`

### Di trú ngoại tuyến (Cold Migration)
```console
# Hãy tắt máy ảo trước.
virsh shutdown my-vm

# Đợi tắt máy
while [ "$(virsh domstate my-vm)" != "shut off" ]; do
    sleep 1
done

# Di trú cấu hình và đĩa
virsh dumpxml my-vm > my-vm.xml
scp my-vm.xml root@destination-host:/tmp/
scp /var/lib/libvirt/images/my-vm.qcow2 \
    root@destination-host:/var/lib/libvirt/images/

# Trên máy chủ đích
virsh define /tmp/my-vm.xml
virsh start my-vm

# Loại bỏ khỏi máy chủ nguồn
virsh undefine my-vm
```

## Các kịch bản di trú nâng cao
### Di trú nén
```console
# Sử dụng nén để giảm băng thông (mức sử dụng CPU cao hơn)
virsh migrate --live --compressed my-vm \
  qemu+ssh://destination-host/system

# Điều chỉnh mức độ nén và số luồng
virsh migrate --live --compressed \
  --comp-methods mt \
  --comp-mt-level 9 \
  --comp-mt-threads 4 \
  my-vm qemu+ssh://destination-host/system
```

### Di trú với cơ chế Tự động hội tụ (Auto-Converge)
```console
# Tự động điều tiết VM nếu quá trình di trú không hội tụ.
virsh migrate --live --auto-converge my-vm \
  qemu+ssh://destination-host/system

# Đặt mức ga (throttle) ban đầu và tăng dần.
virsh migrate --live --auto-converge \
  --auto-converge-initial 20 \
  --auto-converge-increment 10 \
  my-vm qemu+ssh://destination-host/system
```

Hỗ trợ các khối lượng công việc đòi hỏi nhiều bộ nhớ.

### Di trú theo cơ chế Post-Copy
```console
# Khởi động máy ảo tại đích trước khi chuyển toàn bộ bộ nhớ.
virsh migrate --live --postcopy my-vm \
  qemu+ssh://destination-host/system

# Chuyển sang chế độ post-copy trong quá trình di trú.
virsh migrate-setmaxdowntime my-vm 1000
virsh migrate-postcopy my-vm
```

* Ưu điểm:
    - Đảm bảo hội tụ
    - Tổng thời gian di trú ngắn hơn
    - Giai đoạn tiền di trú ngắn hơn
* Nhược điểm:
    - Sự cố mạng có thể dẫn đến mất máy ảo (VM)
    - Có thể gặp vấn đề về hiệu năng cho đến khi hoàn tất quá trình chuyển giao

### Di trú với cấu hình bền vững
```console
# Giữ lại máy ảo tại đích, xóa khỏi nguồn.
virsh migrate --live --persistent --undefinesource my-vm \
  qemu+ssh://destination-host/system

# Duy trì định nghĩa máy ảo trên cả hai máy chủ (không độc quyền)
virsh migrate --live --persistent my-vm \
  qemu+ssh://destination-host/system

# Xác minh tại đích
ssh root@destination-host 'virsh list --all'
```

### Di trú nhiều máy ảo
```bash
#!/bin/bash
# Di trú tuần tự nhiều máy ảo

VMS=("web1" "web2" "web3")
DEST="qemu+ssh://destination-host/system"

for vm in "${VMS[@]}"; do
  echo "Migrating $vm..."
  virsh migrate --live --verbose --persistent --undefinesource \
       "$vm" "$DEST"

  if [ $? -eq 0 ]; then
    echo "$vm migrated successfully"
  else
    echo "ERROR: Failed to migrate $vm"
    exit 1
  fi
done

echo "All VMs migrated successfully"
```

### Di trú song song (Nhiều máy ảo)
```bash
#!/bin/bash
# Di trú các máy ảo song song (cần thận trọng khi sử dụng!)

VMS=("web1" "web2" "web3")
DEST="qemu+ssh://destination-host/system"

for vm in "${VMS[@]}"; do
  (
    virsh migrate --live --verbose "$vm" "$DEST" &
  ) &
done

# Chờ cho tất cả quá trình di trú hoàn tất.
wait

echo "All migrations initiated"
```

## Tối ưu hóa hiệu năng
### Quản lý băng thông
```console
# Thiết lập giới hạn băng thông di trú (MB/s)
virsh migrate-setspeed my-vm 100

# Kiểm tra giới hạn băng thông hiện tại
virsh migrate-getspeed my-vm

# Diễn ra trong thời gian di trú
virsh migrate --live --bandwidth 200 my-vm \
  qemu+ssh://destination-host/system

# Điều chỉnh linh hoạt trong quá trình di trú
virsh migrate-setspeed my-vm 150
```

### Quản lý thời gian ngừng hoạt động
```console
# Thiết lập thời gian ngừng hoạt động tối đa có thể chấp nhận được (mili giây)
virsh migrate-setmaxdowntime my-vm 500

# Mặc định thường là 300ms.
# Các giá trị thấp hơn có thể khiến quá trình di trú thất bại.
# Các giá trị cao hơn giúp giảm tổng thời gian di trú.

# Kiểm tra xem có thể đáp ứng ngưỡng thời gian ngừng hoạt động hay không.
virsh domjobinfo my-vm | grep downtime
```

### Tinh chỉnh mạng
```console
# Sử dụng mạng di ưu dữ liệu chuyên dụng
virsh migrate --live --migrateuri tcp://10.0.1.1:49152 \
  my-vm qemu+ssh://destination-host/system

# Cấu hình virtio-net đa hàng đợi để đạt hiệu năng tốt hơn
virsh edit my-vm

<interface type='network'>
  <source network='default'/>
  <model type='virtio'/>
  <driver name='vhost' queues='4'/>
</interface>

# Bật MTU lớn (jumbo frames) trên mạng di trú
ip link set dev eth1 mtu 9000
```

### Tối ưu hóa bộ nhớ
```console
# Bật tính năng memory balloon để di trú tốt hơn
virsh edit my-vm

<memballoon model='virtio'>
  <stats period='10'/>
</memballoon>

# Giảm bộ nhớ máy ảo trước khi di trú
virsh setmem my-vm 2G

# Bật huge page để di trú nhanh hơn
virsh edit my-vm

<memoryBacking>
  <hugepages/>
</memoryBacking>

# Xác minh huge page trên máy chủ
cat /proc/meminfo | grep Huge
```

### Cấu hình CPU
```console
# Sử dụng tính năng ghim CPU để đạt hiệu năng ổn định.
virsh vcpupin my-vm 0 0
virsh vcpupin my-vm 1 1

# Đảm bảo mẫu CPU tương thích
virsh edit my-vm

<cpu mode='custom' match='exact'>
  <model>Broadwell</model>
  <feature policy='require' name='pdpe1gb'/>
</cpu>

# Kiểm tra khả năng tương thích của CPU
virsh cpu-compare cpu.xml
```

## Giám sát và Khắc phục sự cố
### Theo dõi tiến độ di trú
```console
# Số liệu thống kê di trú dữ liệu theo thời gian thực
virsh domjobinfo my-vm

# Kết quả đầu ra bao gồm:
# Job type:         Unbounded
# Time elapsed:     42123 ms
# Data processed:   2.5 GiB
# Data remaining:   512 MiB
# Memory processed: 2.3 GiB
# Memory remaining: 256 MiB
# Memory bandwidth: 128 MiB/s

# Giám sát liên tục
watch -n 1 'virsh domjobinfo my-vm'
```

Script giám sát chi tiết

```bash
#!/bin/bash
while true; do
  clear
  virsh domjobinfo my-vm
  sleep 1
done
```

### Nhật ký di trú dữ liệu
```console
# Kiểm tra nhật ký libvirt
tail -f /var/log/libvirt/libvirtd.log

# Các bản ghi QEMU cho máy ảo cụ thể
tail -f /var/log/libvirt/qemu/my-vm.log

# Nhật ký hệ thống
journalctl -u libvirtd -f

# Lọc các sự kiện di trú
journalctl -u libvirtd | grep -i migrate
```

### Các vấn đề thường gặp và giải pháp
#### Vấn đề: Quá trình di trú bị đình trệ hoặc không bao giờ hoàn tất

```console
# Kiểm tra xem máy ảo có mức độ biến động bộ nhớ cao hay không.
virsh domjobinfo my-vm | grep "Memory bandwidth"
```

**Giải pháp:**

```console
# 1. Bật chế độ tự động hội tụ
virsh migrate --live --auto-converge my-vm qemu+ssh://dest/system

# 2. Tăng băng thông
virsh migrate-setspeed my-vm 500

# 3. Sử dụng tính năng nén
virsh migrate --live --compressed my-vm qemu+ssh://dest/system

# 4. Chuyển sang chế độ post-copy
virsh migrate-postcopy my-vm

# 5. Giảm tạm thời khối lượng công việc của máy ảo
```

#### Vấn đề: Lỗi tương thích CPU
Lỗi: "migration of domain failed: Unsafe migration..."

```console
# Kiểm tra khả năng tương thích của CPU
virsh capabilities | grep features

# Giải pháp: Sử dụng chế độ CPU tương thích
virsh edit my-vm

# Thay đổi từ:
<cpu mode='host-passthrough'/>

# Thành:
<cpu mode='host-model'>
  <model fallback='allow'/>
</cpu>

# Hoặc sử dụng model cụ thể:
<cpu mode='custom' match='exact'>
  <model>Westmere</model>
</cpu>

# Buộc di trú không an toàn (chỉ dành cho thử nghiệm)
virsh migrate --live --unsafe my-vm qemu+ssh://dest/system
```

#### Sự cố: Không thể kết nối mạng sau khi di trú
```console
# Xác minh cấu hình mạng khớp nhau
virsh net-list --all  # Trên cả hai máy chủ

# Kiểm tra cấu hình cầu nối
brctl show  # Trên cả hai máy chủ

# Xác minh giao diện mạng của máy ảo
virsh domiflist my-vm
```

* Giải pháp:
    1. Đảm bảo tên mạng giống nhau trên cả hai máy chủ
    2. Sử dụng chế độ bridge để đảm bảo tính nhất quán
    3. Cấu hình định tuyến phù hợp giữa các máy chủ

#### Sự cố: Không thể truy cập bộ nhớ
```console
# Xác minh bộ lưu trữ chia sẻ đã được gắn trên cả hai máy chủ.
df -h | grep libvirt

# Kiểm tra kết nối NFS
showmount -e nfs-server

# Xác minh quyền truy cập tệp
ls -la /var/lib/libvirt/images/
```

* Giải pháp:
    1. Gắn (mount) bộ lưu trữ dùng chung tại đích
    2. Sử dụng tùy chọn `--copy-storage-all` nếu không có bộ lưu trữ dùng chung
    3. Kiểm tra kết nối NFS/bộ lưu trữ

#### Vấn đề: Quyền bị từ chối
```console
# Kiểm tra quyền libvirt
ls -la /var/run/libvirt/

# Xác minh tư cách thành viên nhóm
groups $USER

# Kiểm tra SELinux/AppArmor
getenforce  # SELinux
aa-status   # AppArmor

# Giải pháp:
# 1. Thêm người dùng vào nhóm libvirt
usermod -aG libvirt $USER

# 2. Cấu hình SELinux
setsebool -P virt_use_nfs 1

# 3. Sử dụng quyền root để kiểm thử
```

#### Vấn đề: Hiệu suất di trú dữ liệu chậm
```console
# Kiểm tra băng thông mạng
iperf3 -c destination-host

# Kiểm tra mức sử dụng CPU
top
htop

# Giám sát I/O đĩa
iotop
```

**Giải pháp:**

1. Tăng băng thông di trú dữ liệu: `virsh migrate-setspeed my-vm 1000`
2. Sử dụng nén nếu có tài nguyên CPU: `virsh migrate --live --compressed my-vm qemu+ssh://dest/system`
3. Sử dụng mạng di trú chuyên dụng với khung dữ liệu lớn (jumbo frames)
4. Giảm bộ nhớ VM hoặc tạm dừng khối lượng công việc
5. Bật tự động hội tụ

## Script di trú và tự động
### Script kiểm tra trước khi di trú
```bash
#!/bin/bash
# pre-migration-check.sh

VM=$1
DEST_HOST=$2

if [ -z "$VM" ] || [ -z "$DEST_HOST" ]; then
  echo "Usage: $0 <vm-name> <destination-host>"
  exit 1
fi

echo "Pre-migration checks for $VM to $DEST_HOST"
echo "============================================"

# Check VM exists and is running
if ! virsh domstate "$VM" | grep -q "running"; then
  echo "ERROR: VM $VM is not running"
  exit 1
fi
echo "✓ VM is running"

# Check destination host reachable
if ! ping -c 1 "$DEST_HOST" &>/dev/null; then
  echo "ERROR: Cannot reach destination host"
  exit 1
fi
echo "✓ Destination host reachable"

# Check SSH connectivity
if ! ssh root@"$DEST_HOST" 'exit' &>/dev/null; then
  echo "ERROR: Cannot SSH to destination"
  exit 1
fi
echo "✓ SSH connectivity OK"

# Check destination has KVM
if ! ssh root@"$DEST_HOST" 'virsh version' &>/dev/null; then
  echo "ERROR: KVM not available on destination"
  exit 1
fi
echo "✓ KVM available on destination"

# Check CPU compatibility
echo "✓ CPU compatibility (manual verification recommended)"

# Check memory availability
VM_MEM=$(virsh dominfo "$VM" | grep "Max memory" | awk '{print $3}')
DEST_FREE=$(ssh root@"$DEST_HOST" "free | grep Mem | awk '{print \$4}'")

if [ "$DEST_FREE" -lt "$VM_MEM" ]; then
    echo "WARNING: Low memory on destination"
fi
echo "✓ Memory check complete"

# Check shared storage
VM_DISK=$(virsh domblklist "$VM" | awk 'NR>2 {print $2}' | head -n 1)
if ! ssh root@"$DEST_HOST" "ls $VM_DISK" &>/dev/null; then
  echo "ERROR: Disk not accessible on destination"
  echo "  Consider using --copy-storage-all"
  exit 1
fi
echo "✓ Shared storage accessible"

echo ""
echo "All checks passed! Ready to migrate."
echo ""
echo "Suggested command:"
echo "virsh migrate --live --verbose --persistent --undefinesource \\"
echo "  $VM qemu+ssh://$DEST_HOST/system"
```

### Script di trú tự động
```bash
#!/bin/bash
# migrate-vm.sh

VM=$1
DEST=$2
BANDWIDTH=500  # MB/s

if [ -z "$VM" ] || [ -z "$DEST" ]; then
  echo "Usage: $0 <vm-name> <destination-host>"
  exit 1
fi

DEST_URI="qemu+ssh://${DEST}/system"

echo "Starting migration of $VM to $DEST"
echo "===================================="

# Pre-migration checks
echo "Running pre-migration checks..."
if ! bash pre-migration-check.sh "$VM" "$DEST"; then
  echo "Pre-migration checks failed!"
  exit 1
fi

# Create snapshot before migration (safety)
echo "Creating safety snapshot..."
SNAPSHOT="pre-migrate-$(date +%Y%m%d-%H%M%S)"
virsh snapshot-create-as "$VM" "$SNAPSHOT" "Pre-migration snapshot"

# Perform migration
echo "Starting migration..."
virsh migrate --live --verbose --persistent --undefinesource \
    --bandwidth "$BANDWIDTH" \
    --auto-converge \
    "$VM" "$DEST_URI" 2>&1 | tee migration-${VM}.log

if [ ${PIPESTATUS[0]} -eq 0 ]; then
  echo "Migration completed successfully!"

  # Verify VM is running on destination
  if ssh root@"$DEST" "virsh domstate $VM" | grep -q "running"; then
    echo "VM verified running on destination"
    # Delete safety snapshot
    virsh snapshot-delete "$VM" "$SNAPSHOT" 2>/dev/null || true
  else
    echo "WARNING: VM may not be running on destination"
  fi
else
  echo "Migration failed!"
  echo "Check migration-${VM}.log for details"
  exit 1
fi
```

### Script di trú cân bằng tải
Di trú các máy ảo để cân bằng tải giữa các máy chủ vật lý.

```bash
#!/bin/bash
# load-balance.sh

HOSTS=("host1" "host2" "host3")
THRESHOLD=70  # CPU usage threshold

for host in "${HOSTS[@]}"; do
  CPU_USAGE=$(ssh root@"$host" "top -bn1 | grep 'Cpu(s)' | awk '{print \$2}' | cut -d'%' -f1")

  if (( $(echo "$CPU_USAGE > $THRESHOLD" | bc -l) )); then
    echo "Host $host overloaded (${CPU_USAGE}%)"

    # Find least loaded host
    MIN_HOST=""
    MIN_LOAD=100

    for target in "${HOSTS[@]}"; do
      if [ "$target" != "$host" ]; then
        LOAD=$(ssh root@"$target" "top -bn1 | grep 'Cpu(s)' | awk '{print \$2}' | cut -d'%' -f1")
        if (( $(echo "$LOAD < $MIN_LOAD" | bc -l) )); then
          MIN_LOAD=$LOAD
          MIN_HOST=$target
        fi
      fi
    done

    # Migrate least critical VM
    VM=$(ssh root@"$host" "virsh list --name | head -n 1")
    echo "Migrating $VM from $host to $MIN_HOST"
    ssh root@"$host" "virsh migrate --live $VM qemu+ssh://${MIN_HOST}/system"
  fi
done
```

## Các phương pháp tốt nhất
### Lập kế hoạch và chuẩn bị
1. Luôn kiểm tra quá trình di trú dữ liệu trên môi trường phát triển/thử nghiệm trước
2. Xác minh khả năng tương thích CPU trước khi di trú lên môi trường sản xuất
3. Sử dụng bộ nhớ dùng chung khi có thể
4. Cấu hình mạng di trú chuyên dụng cho môi trường sản xuất
5. Lập tài liệu về quy trình di trú và sổ tay hướng dẫn

### Các cân nhắc về hiệu năng
1. Sử dụng tính năng nén dữ liệu cho quá trình chuyển đổi mạng WAN: `virsh migrate --live --compressed`
2. Thiết lập giới hạn băng thông phù hợp: `virsh migrate --bandwidth 500`
3. Bật tính năng tự động hội tụ (auto-converge) cho các máy ảo tiêu tốn nhiều bộ nhớ: `virsh migrate --auto-converge`
4. Sử dụng post-copy để đảm bảo sự hội tụ: `virsh migrate --postcopy`
5. Lên lịch chuyển đổi dữ liệu vào các thời điểm có lưu lượng sử dụng thấp.

### Các biện pháp bảo mật tốt nhất
1. Luôn sử dụng SSH hoặc TLS trong môi trường vận hành thực tế (production): `virsh migrate --live ubuntu-vm qemu+ssh://dest/system`
2. Tuyệt đối không sử dụng `auth_tcp = "none"` trong môi trường vận hành thực tế
3. Triển khai quy trình quản lý chứng chỉ phù hợp cho TLS
4. Sử dụng mạng chuyên dụng và tách biệt dành riêng cho việc di trú máy ảo (migration)
5. Thiết lập quy tắc tường lửa chỉ cho phép các cổng di trú máy ảo hoạt động giữa các máy chủ tin cậy

### Giám sát và Xác nhận
1. Luôn giám sát quá trình di trú dữ liệu: `watch -n 1 'virsh domjobinfo vm-name'`
2. Xác thực máy ảo sau khi di trú: `ssh dest-host 'virsh domstate vm-name'`, `ssh dest-host 'virsh dominfo vm-name'`
3. Kiểm tra khả năng kết nối của ứng dụng: `curl http://vm-ip/health`
4. Kiểm tra nhật ký để tìm lỗi: `journalctl -u libvirtd | grep -i error`
5. Lưu giữ nhật ký di trú chi tiết

## Kết luận
Di trú trực tiếp (live migration) là khả năng thiết yếu đối với cơ sở hạ tầng ảo hóa hiện đại, cho phép bảo trì không gây gián đoạn dịch vụ, tối ưu hóa tài nguyên linh hoạt và tăng cường các chiến lược phục hồi sau thảm họa. Việc nắm vững kỹ thuật di trú trực tiếp trên KVM/QEMU sử dụng libvirt tạo nền tảng để xây dựng các nền tảng ảo hóa linh hoạt với tính sẵn sàng cao.

Các điểm chính:

* Lưu trữ dùng chung giúp đơn giản hóa đáng kể quá trình di trú trực tiếp (live migration)
* Di trú dựa trên SSH là phương pháp đơn giản và an toàn nhất
* Khả năng tương thích của CPU đóng vai trò then chốt để di trú thành công
* Tính năng tự động hội tụ (auto-converge) và nén dữ liệu hỗ trợ xử lý các khối lượng công việc phức tạp
* Luôn theo dõi tiến trình di trú và kiểm tra, xác thực kết quả
* Phương pháp di trú post-copy đảm bảo sự hội tụ nhưng tiềm ẩn rủi ro

Khi đã tích lũy được kinh nghiệm về di trú trực tiếp (live migration), bạn hãy tìm hiểu các kịch bản nâng cao như di trú giữa các trung tâm dữ liệu, các chiến lược di trú lưu trữ và tích hợp với các nền tảng điều phối (orchestration) như OpenStack hoặc oVirt. Sự linh hoạt và mạnh mẽ của tính năng di trú trực tiếp trên KVM biến nó thành công nghệ nền tảng cho cơ sở hạ tầng đám mây và các hệ thống ảo hóa cấp doanh nghiệp.

Hãy lưu ý rằng quá trình di trú thành công đòi hỏi phải có kế hoạch kỹ lưỡng, quy trình kiểm thử toàn diện và công tác giám sát hiệu quả. Với những kiến thức và kỹ thuật được đề cập trong hướng dẫn này, bạn đã sẵn sàng triển khai các quy trình di trú trực tiếp đáng tin cậy và hiệu quả trong môi trường vận hành thực tế (production).
