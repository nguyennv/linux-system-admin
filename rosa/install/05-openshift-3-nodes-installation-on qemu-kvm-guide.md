Cài đặt OpenShift
-----------------

# Hướng dẫn cài đặt OpenShift 3 node trên QEMU/KVM
* Môi trường học tập
* host CentOS Stream, AlmaLinux, RockyLinux phiên bản 9/10
* Cụm compact 3 node (Agent-based Installer)

## 1. Tổng quan và lưu ý quan trọng về topology
Tài liệu này hướng dẫn dựng một cụm OpenShift Container Platform (OCP) gồm **3 máy ảo** trên một host CentOS/AlmaLinux/RockyLinux 9/10 dùng QEMU/KVM, với cấu hình tối thiểu để học.

> **Lưu ý:":** Mô hình "2 control + 1 compute OpenShift" **không hỗ trợ** 2 control plane node. etcd cần số node lẻ để có quorum; với 2 node, mất 1 node là cụm mất quorum (còn tệ hơn chạy 1 node). Vì vậy, với 3 VM, mô hình đúng là **compact cluster**: **3 node đều là control plane và đồng thời chạy workload** (master + worker). Đây là topology 3 node được Red Hat hỗ trợ chính thức.

Nếu máy host yếu, có thể cân nhắc **Single Node OpenShift (SNO)**: 1 VM, 8 vCPU, 16–32 GB RAM, tiết kiệm tài nguyên hơn nhiều nhưng không có HA.

### Sơ đồ

| Thành phần       | Giá trị                                                      |
| ---------------- | ------------------------------------------------------------ |
| Tên cụm / domain | `ocp.lab.local` (cluster name `ocp`, baseDomain `lab.local`) |
| Mạng libvirt     | `ocp` – `192.168.100.0/24`, gateway `192.168.100.1` (NAT)    |
| master1          | `192.168.100.11` – MAC `52:54:00:00:01:01` (rendezvous node) |
| master2          | `192.168.100.12` – MAC `52:54:00:00:01:02`                   |
| master3          | `192.168.100.13` – MAC `52:54:00:00:01:03`                   |
| API VIP          | `192.168.100.5`                                              |
| Ingress VIP      | `192.168.100.6`                                              |

## 2. Yêu cầu tài nguyên

| Hạng mục      | Tối thiểu để chạy được | Khuyến nghị cho lab |
| ------------- | ---------------------- | ------------------- |
| Mỗi VM – vCPU | 4                      | 8                   |
| Mỗi VM – RAM  | 8 GB                   | 16–24 GB            |
| Mỗi VM – Disk | 50  GB                 | 120 GB (qcow2 thin) |
| Host – CPU    | 16 thread              | 24 thread trở lên   |
| Host – RAM    | 32 GB                  | 64 GB trở lên       |
| Host – Disk   | 400 GB trống           | SSD/NVMe            |

> **Ghi chú:**
> 
> - etcd rất nhạy với độ trễ đĩa
> - Disk nên đặt trên SSD/NVMe. Đĩa HDD thường làm etcd báo lỗi timeout và quá trình cài đặt thất bại.
> - Host cần CPU hỗ trợ ảo hóa (Intel VT-x / AMD-V) và đã bật trong BIOS.
> - Cần có tài khoản Red Hat (miễn phí) để lấy **pull secret** tại console.redhat.com.

## 3. Chuẩn bị host CentOS 9
### 3.1. Kiểm tra ảo hóa và cài gói

```console
egrep -c '(vmx|svm)' /proc/cpuinfo   # phải > 0

sudo dnf install -y qemu-kvm libvirt virt-install libvirt-client \
    virt-viewer bind-utils curl tar jq

sudo systemctl enable --now libvirtd
sudo usermod -aG libvirt $USER   # đăng xuất/đăng nhập lại sau lệnh này

virt-host-validate qemu
```

### 3.2. Mở firewall cho mạng libvirt (nếu bật firewalld)
libvirt thường tự xử lý. Nếu VM không ra được Internet, kiểm tra:

```console
sudo firewall-cmd --get-active-zones
sudo sysctl net.ipv4.ip_forward   # phải = 1
```

## 4. Tạo mạng libvirt có DNS và DHCP
OpenShift yêu cầu DNS chính xác: `api`, `api-int`, wildcard `*.apps` và tên các node. Ta dùng dnsmasq tích hợp trong libvirt.

Tạo file `ocp-net.xml`:

```xml
<network xmlns:dnsmasq='http://libvirt.org/schemas/network/dnsmasq/1.0'>
  <name>ocp</name>
  <forward mode='nat'/>
  <bridge name='virbr-ocp' stp='on' delay='0'/>
  <domain name='ocp.lab.local' localOnly='yes'/>
  <ip address='192.168.100.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.100.100' end='192.168.100.200'/>
      <host mac='52:54:00:00:01:01' name='master1.ocp.lab.local' ip='192.168.100.11'/>
      <host mac='52:54:00:00:01:02' name='master2.ocp.lab.local' ip='192.168.100.12'/>
      <host mac='52:54:00:00:01:03' name='master3.ocp.lab.local' ip='192.168.100.13'/>
    </dhcp>
  </ip>
  <dns>
    <host ip='192.168.100.5'>
      <hostname>api.ocp.lab.local</hostname>
      <hostname>api-int.ocp.lab.local</hostname>
    </host>
    <host ip='192.168.100.11'><hostname>master1.ocp.lab.local</hostname></host>
    <host ip='192.168.100.12'><hostname>master2.ocp.lab.local</hostname></host>
    <host ip='192.168.100.13'><hostname>master3.ocp.lab.local</hostname></host>
  </dns>
  <dnsmasq:options>
    <dnsmasq:option value='address=/apps.ocp.lab.local/192.168.100.6'/>
  </dnsmasq:options>
</network>
```

> Lưu ý: Ta có thể tạo các địa chỉ MAC bằng cách sử dụng lệnh:
> 
> ```console
> openssl rand -hex 6 | sed 's/\(..\)/\1:/g; s/:$//'
> ```

Kích hoạt mạng:

```console
sudo virsh net-define ocp-net.xml
sudo virsh net-start ocp
sudo virsh net-autostart ocp
sudo virsh net-list --all
```

Kiểm tra DNS từ host:

```console
dig +short @192.168.100.1 api.ocp.lab.local          # 192.168.100.5
dig +short @192.168.100.1 test.apps.ocp.lab.local    # 192.168.100.6
```

## 5. Tải công cụ và chuẩn bị file cấu hình
### 5.1. Tải oc và openshift-install
Chọn một phiên bản ổn định (stable) mới từ mirror chính thức. Ví dụ dùng kênh `stable`:

```console
mkdir -p ~/ocp && cd ~/ocp
BASE=https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable

curl -LO $BASE/openshift-install-linux.tar.gz
curl -LO $BASE/openshift-client-linux.tar.gz

sudo tar -xzf openshift-install-linux.tar.gz -C /usr/local/bin openshift-install
sudo tar -xzf openshift-client-linux.tar.gz -C /usr/local/bin oc kubectl

openshift-install version
oc version --client
```

### 5.2. Pull secret và SSH key
1. Đăng nhập `console.redhat.com`, vào mục OpenShift, chọn **Create cluster → Datacenter → Bare Metal (x86\_64) → Agent-based**, tải **pull secret** và lưu thành `~/pull-secret.txt`.
2. Tạo SSH key để truy cập node khi cần debug:

```console
ssh-keygen -t ed25519 -N '' -f ~/.ssh/ocp_key
```

### 5.3. Tạo thư mục cài đặt và install-config.yaml
Tạo thư mục `~/ocp/install` và file `~/ocp/install/install-config.yaml`:

```console
mkdir ~/ocp/install
vi ~/ocp/install/install-config.yaml
```

```yaml
apiVersion: v1
baseDomain: lab.local
metadata:
  name: ocp
compute:
- name: worker
  replicas: 0
controlPlane:
  name: master
  replicas: 3
networking:
  networkType: OVNKubernetes
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  serviceNetwork:
  - 172.30.0.0/16
  machineNetwork:
  - cidr: 192.168.100.0/24
platform:
  baremetal:
    apiVIPs:
    - 192.168.100.5
    ingressVIPs:
    - 192.168.100.6
pullSecret: '<DÁN NỘI DUNG pull-secret.txt VÀO ĐÂY, GIỮ NGUYÊN DẤU NHÁY ĐƠN>'
sshKey: '<DÁN NỘI DUNG ~/.ssh/ocp_key.pub>'
```

`compute.replicas: 0` cùng `controlPlane.replicas: 3` là cấu hình compact cluster: 3 control plane node tự động có thể chạy workload.

### 5.4. agent-config.yaml
Tạo file `~/ocp/install/agent-config.yaml`:

```console
vi ~/ocp/install/agent-config.yaml
```

```yaml
apiVersion: v1alpha1
kind: AgentConfig
metadata:
  name: ocp
rendezvousIP: 192.168.100.11
hosts:
- hostname: master1.ocp.lab.local
  role: master
  interfaces:
  - name: enp1s0
    macAddress: 52:54:00:00:01:01
- hostname: master2.ocp.lab.local
  role: master
  interfaces:
  - name: enp1s0
    macAddress: 52:54:00:00:01:02
- hostname: master3.ocp.lab.local
  role: master
  interfaces:
  - name: enp1s0
    macAddress: 52:54:00:00:01:03
```

`rendezvousIP` là IP của node điều phối quá trình cài đặt, phải trùng với IP của một trong các node (ở đây là master1). IP các node được cấp bằng DHCP reservation đã khai báo ở bước 4.

> Lưu ý: lệnh tạo ISO sẽ **xóa** hai file YAML trong thư mục. Hãy sao lưu trước:
> 
> ```console
> cp -r ~/ocp/install ~/ocp/install.bak
> ```

## 6. Tạo ISO cài đặt
```console
cd ~/ocp
openshift-install --dir install agent create image --log-level=info

ls -lh install/agent.x86_64.iso
sudo cp install/agent.x86_64.iso /var/lib/libvirt/images/ocp-agent.iso
```

Sau lệnh này, thư mục `install/` có thêm thư mục `auth/` chứa `kubeconfig` và `kubeadmin-password` (dùng ở bước 9).

## 7. Tạo 3 máy ảo
Dùng vòng lặp để tạo 3 VM với MAC cố định, boot từ đĩa trước, CD (ISO) sau. Do đĩa trống nên lần đầu sẽ boot từ ISO; sau khi cài xong, VM boot từ đĩa.

```console
for i in 1 2 3; do
  sudo virt-install \
    --name master$i \
    --vcpus 4 --memory 8192 \
    --cpu host-passthrough \
    --osinfo detect=on,require=off \
    --disk path=/var/lib/libvirt/images/master$i.qcow2,size=120,format=qcow2,bus=virtio \
    --disk path=/var/lib/libvirt/images/ocp-agent.iso,device=cdrom,readonly=on \
    --network network=ocp,model=virtio,mac=52:54:00:00:01:0$i \
    --boot hd,cdrom \
    --graphics vnc \
    --import \
    --noautoconsole
done

sudo virsh list --all
```

Nếu host yếu, giảm `--vcpus 8` xuống `4` (cài chậm hơn) – không nên giảm RAM dưới 16 GB.

Nếu một VM bị tắt thay vì khởi động lại sau lần boot đầu, chỉ cần bật lại:

```console
sudo virsh start master1
```

## 8. Theo dõi quá trình cài đặt
Chạy các lệnh sau trên host (có thể mất 40–90 phút tùy phần cứng):

```console
cd ~/ocp
openshift-install --dir install agent wait-for bootstrap-complete --log-level=info
openshift-install --dir install agent wait-for install-complete --log-level=info
```

Khi hoàn tất, lệnh in ra địa chỉ web console, tên người dùng `kubeadmin` và mật khẩu tương ứng.

Nếu cần xem trực tiếp trên node:

```console
ssh -i ~/.ssh/ocp_key core@192.168.100.11
journalctl -u agent -f          # giai đoạn agent
sudo crictl ps                  # sau khi hệ thống lên
```

## 9. Truy cập cụm

### 9.1. Dùng oc

```console
export KUBECONFIG=~/ocp/install/auth/kubeconfig
oc get nodes
oc get clusteroperators
oc get clusterversion
```

Kết quả mong đợi: 3 node ở trạng thái `Ready` với role `control-plane,master,worker`; tất cả cluster operator `Available=True`, `Degraded=False`.

### 9.2. Truy cập web console từ host
Cách đơn giản nhất là thêm vào `/etc/hosts` của máy dùng trình duyệt:

```text
192.168.100.5 api.ocp.lab.local
192.168.100.6 console-openshift-console.apps.ocp.lab.local oauth-openshift.apps.ocp.lab.local downloads-openshift-console.apps.ocp.lab.local canary-openshift-ingress-canary.apps.ocp.lab.local
```

Sau đó mở `https://console-openshift-console.apps.ocp.lab.local`, đăng nhập `kubeadmin` với mật khẩu trong `~/ocp/install/auth/kubeadmin-password`. Trình duyệt sẽ cảnh báo chứng chỉ tự ký – chấp nhận để tiếp tục.

Nếu muốn dùng wildcard DNS thay vì sửa `/etc/hosts`, trỏ resolver của host về `192.168.100.1` cho domain `ocp.lab.local` (ví dụ qua `dnsmasq` của NetworkManager).

Nếu truy cập từ máy khác ngoài host, cần thêm quy tắc forward hoặc reverse proxy từ host vào hai VIP `192.168.100.5` và `192.168.100.6`.

## 10. Sau khi cài đặt
1. **Gỡ ISO khỏi VM** (tùy chọn, tránh boot nhầm):

```console
for i in 1 2 3; do
  sudo virsh change-media master$i sda --eject --config 2>/dev/null || true
done
```

2. **Tạo user thường** (htpasswd) rồi xóa `kubeadmin` khi không cần nữa:

```console
htpasswd -c -B -b users.htpasswd admin 'MatKhauManh'
oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd -n openshift-config

cat <<'EOF' | oc apply -f -
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: local
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpass-secret
EOF

oc adm policy add-cluster-role-to-user cluster-admin admin
```

3. **Storage:** cụm mới cài chưa có StorageClass mặc định cho PVC. Với lab, có thể cài LVM Storage (LVMS) hoặc local-path / NFS provisioner để chạy ứng dụng có dữ liệu. Registry nội bộ sẽ ở trạng thái `Removed` cho tới khi có storage.
4. **Snapshot VM** sau khi cụm ổn định để có thể quay lại nhanh khi thử nghiệm (tắt VM rồi `virsh snapshot-create-as`), hoặc sao lưu file qcow2.

## 11. Xử lý sự cố thường gặp
| Triệu chứng                               | Nguyên nhân thường gặp                               | Cách xử lý                                                        |
| ----------------------------------------- | ---------------------------------------------------- | ----------------------------------------------------------------- |
| Agent không thấy đủ 3 host                | Sai MAC, VM chưa lấy IP, ISO cũ                      | Kiểm tra `virsh net-dhcp-leases ocp`; tạo lại ISO nếu đã sửa YAML |
| Lỗi validation "DNS resolution"           | Thiếu record `api`, `api-int` hoặc wildcard `*.apps` | Kiểm tra lại bước 4 bằng `dig`                                    |
| Bootstrap treo lâu, etcd lỗi              | Đĩa chậm, thiếu CPU/RAM                              | Dùng SSD/NVMe, tăng vCPU lên 8, RAM 16–24 GB                      |
| VIP không lên                             | VIP trùng IP đã dùng hoặc ngoài subnet               | VIP phải nằm trong `machineNetwork` và chưa bị cấp phát           |
| Console không vào được                    | Chưa khai báo hosts/DNS cho `*.apps`                 | Làm lại mục 9.2                                                   |
| Clock skew / certificate lỗi              | Đồng hồ host/VM lệch                                 | Đồng bộ thời gian host bằng `chronyd`                             |
| `wait-for` timeout nhưng cụm vẫn đang cài | Timeout của lệnh, không phải lỗi cụm                 | Chạy lại `wait-for install-complete`                              |

Lệnh hữu ích để kiểm tra:

```console
sudo virsh net-dhcp-leases ocp
sudo virsh console master1          # thoát bằng Ctrl+]
oc get co | grep -v 'True.*False.*False'
```

## 12. Dọn dẹp / cài lại

```console
for i in 1 2 3; do
  sudo virsh destroy master$i 2>/dev/null
  sudo virsh undefine master$i --remove-all-storage
done
sudo virsh net-destroy ocp && sudo virsh net-undefine ocp
rm -rf ~/ocp/install
```

Để cài lại, quay về bước 5 (khôi phục YAML từ `install.bak`), tạo ISO mới và tạo lại VM. **Không dùng lại ISO cũ** cho lần cài mới vì ISO chứa token và chứng chỉ riêng của lần cài đó.

## 13. Tài liệu tham khảo

- Red Hat Docs: *Installing an on-premise cluster with the Agent-based Installer*
- Red Hat Docs: *Installing a three-node cluster on bare metal*
- Red Hat Docs: *Single-node OpenShift* (nếu muốn thử bản 1 node)
- Tải công cụ: mirror.openshift.com và console.redhat.com/openshift
