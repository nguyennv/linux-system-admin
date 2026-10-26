Cài đặt OpenShift
-----------------

# Cài đặt trên một nút duy nhất
## I. Chuẩn bị cài đặt trên một nút duy nhất
Hãy xem xét các yêu cầu khác nhau đối với việc cài đặt OpenShift Container Platform trên một nút duy nhất (single node) và chuẩn bị môi trường của bạn cho quá trình cài đặt.

Trước khi chuẩn bị cho việc cài đặt trên một nút duy nhất, hãy đảm bảo rằng bạn đã xem qua các tài liệu sau:

* "Cài đặt và cập nhật OpenShift Container Platform"
* "Chọn phương thức cài đặt cụm (cluster) và chuẩn bị môi trường cho người dùng"

### I.1. OpenShift trên một nút (Single-node OpenShift)
Bạn có thể tạo một cụm (cluster) gồm một nút duy nhất bằng các phương thức cài đặt tiêu chuẩn. OpenShift Container Platform trên một nút là một dạng cài đặt chuyên biệt, đòi hỏi phải tạo một tệp cấu hình Ignition đặc thù.

> **Quan trọng:** Sau khi cài đặt cụm, không có tùy chọn cấu hình nào cho phép chuyển đổi cụm một nút thành cụm có tính sẵn sàng cao (High Availability - HA) hoặc cụm gồm hai nút. Cấu trúc liên kết (topology) mà bạn chọn tại thời điểm cài đặt sẽ được duy trì trong suốt vòng đời của cụm đó.

Trường hợp sử dụng chính là cho các khối lượng công việc (workload) trong mô hình điện toán biên (edge computing), bao gồm các tình huống kết nối không ổn định, các nền tảng đám mây di động, và mạng truy cập vô tuyến 5G (5G RAN) đặt gần trạm phát sóng. Hạn chế lớn nhất của việc cài đặt trên một nút duy nhất chính là thiếu tính sẵn sàng cao.

> **Quan trọng:** Việc sử dụng OpenShiftSDN với OpenShift trên một nút không được hỗ trợ. OVN-Kubernetes là plugin mạng mặc định cho các triển khai OpenShift trên một nút.

### I.2. Các yêu cầu để cài đặt OpenShift trên một nút duy nhất
Việc cài đặt OpenShift Container Platform trên một nút duy nhất giúp giảm bớt một số yêu cầu về tính sẵn sàng cao và các cụm quy mô lớn. Tuy nhiên, bạn vẫn cần đáp ứng một số yêu cầu nhất định.

Các yêu cầu sau đây phải được đáp ứng:

* Máy chủ quản trị (Administration host): Bạn cần có một máy tính để chuẩn bị tệp ISO, tạo ổ đĩa khởi động USB và giám sát quá trình cài đặt. 

> **Lưu ý:** Đối với nền tảng `ppc64le`, máy chủ quản trị được dùng để chuẩn bị tệp ISO nhưng không cần tạo ổ đĩa khởi động USB. Tệp ISO có thể được gắn trực tiếp vào PowerVM. 

> **Lưu ý:** Không yêu cầu tệp ISO đối với các cài đặt trên IBM Z®. 

* Kiến trúc CPU: Việc cài đặt OpenShift Container Platform trên một nút duy nhất hỗ trợ các kiến trúc CPU `x86_64`, `arm64`, `ppc64le` và `s390x`. 
* Các nền tảng được hỗ trợ: Việc cài đặt OpenShift Container Platform trên một nút duy nhất được hỗ trợ trên hạ tầng vật lý (bare metal) và các trình giám sát máy ảo (hypervisor) của bên thứ ba đã được chứng nhận. Xem phần "Các trình giám sát máy ảo và hệ điều hành khách đã được chứng nhận" (Certified Hypervisors and Guest Operating Systems) để biết thêm thông tin. Trong hầu hết các trường hợp, bạn phải chỉ định tham số `platform.none: {}` trong tệp cấu hình `install-config.yaml`. Danh sách dưới đây liệt kê các ngoại lệ duy nhất cùng với tham số tương ứng cần chỉ định trong tệp cấu hình `install-config.yaml`:
    * Amazon Web Services (AWS): sử dụng `platform=aws`
    * Google Cloud: sử dụng `platform=gcp`
    * Microsoft Azure: sử dụng `platform=azure`

* Máy chủ cấp sản xuất (Production-grade server): Việc cài đặt OpenShift Container Platform trên một nút duy nhất đòi hỏi một máy chủ có đủ tài nguyên để chạy các dịch vụ của OpenShift Container Platform cũng như các khối lượng công việc (workload) thực tế trong môi trường sản xuất.

**Bảng 1.1. Các yêu cầu tối thiểu về tài nguyên**

| Hồ sơ     | Tính toán | Bộ nhớ    | Lưu trữ |
|-----------|-----------|-----------|---------|
| Tối thiểu | 4 vCPUs   | 16 GB RAM | 120 GB  |

> **Quan trọng:** Việc chạy OpenShift trên một nút (single-node) với 4 vCPU sẽ để lại rất ít "khoảng trống" (headroom) cho các ứng dụng người dùng, đồng thời tạo ra nguy cơ cao về tranh chấp tài nguyên và suy giảm hiệu năng.  
> Để đảm bảo sự ổn định của cụm (cluster) ở ngưỡng này, bạn cần thực hiện các biện pháp nhằm giảm thiểu tổng lượng tài nguyên tiêu thụ, chẳng hạn như giới hạn số lượng khối lượng công việc (workload) đang chạy hoặc hạn chế các tính năng của cụm. Để biết thêm thông tin, hãy xem phần "Các tính năng của cụm" (Cluster capabilities).  
> Ngoài ra, bạn nên cung cấp thêm tài nguyên tính toán cho cụm.

> **Lưu ý:** Thông thường, một vCPU tương đương với một nhân vật lý. Tuy nhiên, nếu bạn bật tính năng đa luồng đồng thời (SMT) hoặc Hyper-Threading, mỗi luồng CPU sẽ được tính là một vCPU.  
> Việc thêm các Operator trong quá trình cài đặt có thể làm tăng yêu cầu tối thiểu về tài nguyên.

Máy chủ phải có Bộ điều khiển quản lý bo mạch chủ (Baseboard Management Controller - BMC) khi khởi động bằng phương tiện ảo.

> **Lưu ý:** BMC không được hỗ trợ trên các hệ thống IBM Z® và IBM Power®.

* Mạng: Máy chủ phải có quyền truy cập internet hoặc truy cập vào một registry cục bộ nếu không được kết nối với mạng có khả năng định tuyến. Máy chủ cần có địa chỉ IP tĩnh hoặc địa chỉ IP được dành riêng qua DHCP (DHCP reservation) cho các thành phần sau: Kubernetes API, tuyến đường ingress (ingress route) và tên miền của các nút trong cụm (cluster node). Bạn phải cấu hình DNS để phân giải địa chỉ IP cho từng tên miền đầy đủ (FQDN) dưới đây:

**Bảng 1.2. Các bản ghi DNS bắt buộc**

| Sử dụng        | FQDN                                   | Mô tả                                                                                                                                                                                             |
|----------------|----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kubernetes API | `api.<cluster_name>.<base_domain>`     | Thêm bản ghi DNS A/AAAA hoặc CNAME. Bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm lẫn các máy khách bên trong cụm.                                               |
| Internal API   | `api-int.<cluster_name>.<base_domain>` | Hãy thêm bản ghi DNS A/AAAA hoặc CNAME khi tạo tệp ISO theo cách thủ công. Bản ghi này phải có khả năng được phân giải bởi các nút (node) nằm trong cụm (cluster).                                |
| Ingress route  | `*.apps.<cluster_name>.<base_domain>`  | Thêm bản ghi DNS A/AAAA hoặc CNAME dạng ký tự đại diện (wildcard) trỏ tới nút đó. Bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm lẫn các máy khách bên trong cụm. |

> **Quan trọng:** Nếu không có các địa chỉ IP cố định, quá trình giao tiếp giữa apiserver và etcd có thể bị lỗi.

## II. Cài đặt OpenShift trên một nút duy nhất
Bạn có thể cài đặt OpenShift trên một nút duy nhất (single-node OpenShift) bằng cách sử dụng Assisted Installer (trình cài đặt hỗ trợ dựa trên nền tảng web) hoặc công cụ coreos-installer để tạo tệp ảnh ISO khám phá (discovery ISO image).

Tệp ảnh ISO khám phá này sẽ ghi cấu hình hệ thống Red Hat Enterprise Linux CoreOS (RHCOS) vào đĩa cài đặt đích, cho phép bạn vận hành một cụm gồm một nút duy nhất để đáp ứng các nhu cầu của mình.

Hãy cân nhắc sử dụng OpenShift trên một nút duy nhất khi bạn muốn vận hành một cụm trong môi trường có tài nguyên hạn chế hoặc môi trường biệt lập, phục vụ cho các mục đích như thử nghiệm, khắc phục sự cố, đào tạo hoặc triển khai các dự án quy mô nhỏ.

### II.1. Cài đặt OpenShift trên một nút duy nhất (single-node) bằng Assisted Installer
Để cài đặt OpenShift Container Platform trên một nút duy nhất, hãy sử dụng trình hướng dẫn Assisted Installer dựa trên nền tảng web để được dẫn dắt qua quy trình và quản lý việc cài đặt.

Xem phần "Cài đặt OpenShift Container Platform bằng Assisted Installer" để biết thông tin chi tiết và các tùy chọn cấu hình.

#### II.1.1. Tạo tệp ISO khám phá (discovery ISO) bằng Assisted Installer
Việc cài đặt OpenShift Container Platform trên một nút duy nhất (single node) đòi hỏi phải có tệp ISO khám phá; tệp này có thể được tạo thông qua Assisted Installer.

**Quy trình**

1. Trên máy chủ quản trị (administration host), hãy mở trình duyệt và truy cập vào [Red Hat OpenShift Cluster Manager](https://console.redhat.com/openshift/assisted-installer/clusters). 
2. Nhấp vào **Create New Cluster** để tạo một cụm (cluster) mới. 
3. Trong trường **Cluster name**, hãy nhập tên cho cụm. 
4. Trong trường **Base domain**, hãy nhập tên miền cơ sở (base domain). Ví dụ: `example.com`. Tất cả các bản ghi DNS phải là các tên miền phụ của tên miền cơ sở này và bao gồm tên cụm (cluster name), chẳng hạn như: `<cluster_name>.example.com`. **Lưu ý:** Bạn không thể thay đổi tên miền cơ sở hoặc tên cụm sau khi đã cài đặt cụm.
5. Chọn **Install single node OpenShift (SNO)** và hoàn tất các bước còn lại trong trình hướng dẫn. Tải xuống tệp ISO khám phá (discovery ISO).
6. Hoàn tất các bước còn lại trong trình hướng dẫn Assisted Installer.

> **Quan trọng:** Hãy lưu ý đường dẫn (URL) của tệp ISO khám phá (discovery ISO) để thực hiện cài đặt bằng phương tiện ảo (virtual media).  
> Nếu bạn kích hoạt OpenShift Virtualization trong quá trình này, bạn bắt buộc phải có thêm một thiết bị lưu trữ cục bộ thứ hai với dung lượng tối thiểu là 50GiB dành cho các máy ảo của mình.

#### II.1.2. Cài đặt OpenShift trên một nút duy nhất (single-node) bằng Assisted Installer
Sử dụng Assisted Installer để cài đặt cụm (cluster) gồm một nút duy nhất.

**Điều kiện tiên quyết**

Đảm bảo rằng thứ tự ổ đĩa khởi động trong cài đặt BIOS của máy chủ được thiết lập mặc định để khởi động từ ổ đĩa đích dùng cho việc cài đặt.

**Quy trình**

1. Gắn tệp ảnh ISO khám phá (discovery ISO image) vào máy chủ đích. 
2. Khởi động máy chủ từ tệp ảnh ISO khám phá này. Tệp ảnh ISO khám phá sẽ ghi cấu hình hệ thống vào ổ đĩa đích và tự động kích hoạt quá trình khởi động lại máy chủ. 
3. Trên máy chủ quản trị, quay lại trình duyệt web. Chờ cho đến khi máy chủ xuất hiện trong danh sách các máy chủ được phát hiện. Nếu cần, hãy tải lại trang "Assisted Clusters" và chọn tên cụm tương ứng. 
4. Hoàn tất các bước trong trình hướng dẫn cài đặt (install wizard). Cung cấp thông tin chi tiết về mạng, bao gồm cả mạng con (subnet) từ các mạng con khả dụng. Thêm khóa công khai SSH (SSH public key) nếu cần. 
5. Theo dõi tiến trình cài đặt. Quan sát các sự kiện của cụm. Sau khi quá trình cài đặt hoàn tất việc ghi tệp ảnh hệ điều hành vào ổ cứng của máy chủ, máy chủ sẽ tự động khởi động lại. 
6. Tùy chọn: Gỡ bỏ tệp ảnh ISO khám phá. 

Máy chủ sẽ tự động khởi động lại nhiều lần trong quá trình triển khai mặt phẳng điều khiển (control plane).

### II.2. Cài đặt OpenShift trên một nút duy nhất theo cách thủ công
Để cài đặt OpenShift Container Platform trên một nút duy nhất, trước tiên hãy tạo tệp ISO cài đặt, sau đó khởi động máy chủ từ tệp ISO đó. Bạn có thể theo dõi quá trình cài đặt thông qua chương trình cài đặt `openshift-install`.

#### II.2.1. Tạo tệp ISO cài đặt bằng coreos-installer
Bạn có thể cài đặt OpenShift Container Platform trên một nút duy nhất bằng cách tạo tệp ISO cài đặt.

**Điều kiện tiên quyết**

Cài đặt `podman`.

> **Lưu ý:** Xem phần "Các yêu cầu để cài đặt OpenShift trên một nút duy nhất" để biết các yêu cầu về mạng, bao gồm cả các bản ghi DNS.

**Quy trình**

1. Thiết lập phiên bản OpenShift Container Platform:

```bash
$ export OCP_VERSION=<ocp_version>
```

Thay thế `<ocp_version>` bằng phiên bản hiện tại, ví dụ: latest-4.22

2. Thiết lập kiến trúc cụm mục tiêu:

```bash
$ export ARCH=<architecture>
```

Hãy thay thế `<architecture>` bằng kiến trúc của máy chủ đích, chẳng hạn như `aarch64` hoặc `x86_64`.

3. Thiết lập kiến trúc máy chủ cài đặt:

```bash
$ export HOST_ARCH=$(uname -m)
```

Lệnh này phát hiện kiến trúc của máy chủ cài đặt. Nếu kiến trúc của máy chủ cài đặt khác với kiến trúc của cụm (cluster) đích, thì các tệp nhị phân được tải xuống phải tương ứng với máy chủ cài đặt đó. Ví dụ: nếu bạn đang cài đặt một cụm aarch64 từ một máy chủ trung gian (bastion host) có kiến trúc `x86_64`, thì HOST_ARCH sẽ là `x86_64`.

4. Tải xuống ứng dụng khách (`oc`) của OpenShift Container Platform và thiết lập để có thể sử dụng bằng cách nhập các lệnh sau:

```bash
$ curl -k https://mirror.openshift.com/pub/openshift-v4/$HOST_ARCH/clients/ocp/$OCP_VERSION/openshift-client-linux.tar.gz -o oc.tar.gz
$ tar zxf oc.tar.gz
$ chmod +x oc
```

5. Tải xuống trình cài đặt OpenShift Container Platform và chuẩn bị sẵn sàng để sử dụng bằng cách nhập các lệnh sau:

```bash
$ curl -k https://mirror.openshift.com/pub/openshift-v4/$HOST_ARCH/clients/ocp/$OCP_VERSION/openshift-install-linux.tar.gz -o openshift-install-linux.tar.gz
$ tar zxvf openshift-install-linux.tar.gz
$ chmod +x openshift-install
```

6. Lấy URL của tệp ISO RHCOS bằng cách chạy lệnh sau:

```bash
$ export ISO_URL=$(./openshift-install coreos print-stream-json | grep location | grep $ARCH | grep iso | cut -d\" -f4)
```

7. Tải xuống tệp ISO RHCOS:

```bash
$ curl -L $ISO_URL -o rhcos-live.iso
```

8. Chuẩn bị tệp `install-config.yaml`:

```yaml
apiVersion: v1
baseDomain: <domain>
compute:
- name: worker
  replicas: 0
controlPlane:
  name: master
  replicas: 1
metadata:
  name: <name>
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 10.0.0.0/16
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
bootstrapInPlace:
  installationDisk: /dev/disk/by-id/<disk_id>
pullSecret: '<pull_secret>'
sshKey: |
  <ssh_key>
```

Trong đó:

* `baseDomain`: Xác định tên miền của cụm (cluster domain name).
* `compute.replicas`: Thiết lập giá trị compute.replicas là 0. Cấu hình này cho phép lập lịch (scheduling) trên nút điều khiển (control plane node).
* `controlPlane.replicas`: Thiết lập giá trị controlPlane.replicas là 1. Kết hợp với thiết lập compute nêu trên, cấu hình này đảm bảo cụm chạy trên một nút duy nhất.
* `metadata.name`: Xác định tên của cụm (cluster name) trong phần metadata.
* `networking`: Xác định các thông tin chi tiết về mạng. OVN-Kubernetes là loại plugin mạng duy nhất được phép sử dụng cho các cụm chạy trên một nút duy nhất.
* `machineNetwork.cidr`: Xác định giá trị CIDR khớp với mạng con (subnet) của cụm.
* `installationDisk`: Xác định đường dẫn đến ổ đĩa cài đặt, ví dụ: `/dev/disk/by-id/wwn-0x64cd98f04fde100024684cf3034da5c2`.
* `pullSecret`: Xác định tham số pullSecret. Hãy sao chép pull secret từ Red Hat OpenShift Cluster Manager và thêm nội dung đó vào thiết lập cấu hình này.
* `sshKey`: Xác định tham số `sshKey`. Hãy thêm khóa công khai SSH (public SSH key) từ máy chủ quản trị để bạn có thể đăng nhập vào cụm sau khi quá trình cài đặt hoàn tất.

9. Tạo các tài nguyên OpenShift Container Platform bằng cách chạy các lệnh sau:

```bash
$ mkdir ocp
$ cp install-config.yaml ocp
$ ./openshift-install --dir=ocp create single-node-ignition-config
```

10. Nhúng dữ liệu khởi động (ignition data) vào tệp ISO của RHCOS bằng cách chạy các lệnh sau:

```bash
$ alias coreos-installer='podman run --privileged --pull always --rm \
        -v /dev:/dev -v /run/udev:/run/udev -v $PWD:/data \
        -w /data quay.io/coreos/coreos-installer:release'
$ coreos-installer iso ignition embed -fi ocp/bootstrap-in-place-for-live-iso.ign rhcos-live.iso
```

> **Quan trọng:** Các chứng chỉ SSL cho tệp ảnh cài đặt ISO của RHCOS chỉ có hiệu lực trong vòng 24 giờ. Nếu bạn sử dụng tệp ảnh ISO này để cài đặt một nút (node) sau thời điểm tạo tệp ảnh hơn 24 giờ, quá trình cài đặt có thể bị lỗi. Để tạo lại tệp ảnh sau 24 giờ, hãy xóa thư mục `ocp` rồi tạo lại các tài nguyên của OpenShift Container Platform.

#### II.2.2. Theo dõi quá trình cài đặt cụm bằng openshift-install
Sử dụng tệp nhị phân `openshift-install` để theo dõi tiến trình cài đặt cụm đơn nút (single-node cluster).

**Điều kiện tiên quyết**

Đảm bảo rằng thứ tự ổ đĩa khởi động trong cài đặt BIOS của máy chủ được thiết lập mặc định để khởi động từ ổ đĩa đích dùng cho việc cài đặt.

**Quy trình**

1. Gắn tệp ảnh ISO khám phá (discovery ISO image) vào máy chủ đích. 
2. Khởi động máy chủ từ tệp ảnh ISO khám phá này. Tệp ảnh ISO khám phá sẽ ghi cấu hình hệ thống vào ổ đĩa đích và tự động kích hoạt quá trình khởi động lại máy chủ. 
3. Trên máy chủ quản trị, hãy theo dõi quá trình cài đặt bằng cách chạy lệnh sau:

```bash
$ ./openshift-install --dir=ocp wait-for install-complete
```

4. Tùy chọn: Xóa tệp ảnh ISO khám phá (discovery ISO image).

Máy chủ sẽ khởi động lại nhiều lần trong quá trình triển khai mặt phẳng điều khiển (control plane).

**Xác minh**

Sau khi quá trình cài đặt hoàn tất, hãy kiểm tra môi trường bằng cách chạy lệnh sau:

```bash
$ export KUBECONFIG=ocp/auth/kubeconfig
$ oc get nodes
```

Ví dụ đầu ra

```
NAME                         STATUS   ROLES           AGE     VERSION
control-plane.example.com    Ready    master,worker   10m     v1.35.4
```

### II.3. Các yêu cầu bổ sung khi cài đặt OpenShift trên một nút duy nhất (single-node) trên nền tảng đám mây
So với việc cài đặt một cụm (cluster) có tính sẵn sàng cao (high-availability), việc cài đặt OpenShift trên một nút duy nhất có thêm một số yêu cầu khác.

Tài liệu hướng dẫn cài đặt do trình cài đặt tự động cung cấp (installer-provisioned installation) trên các nền tảng đám mây thường dựa trên mô hình cụm có tính sẵn sàng cao, bao gồm ba nút điều khiển (control plane nodes). Khi tham khảo tài liệu này, bạn cần lưu ý những điểm khác biệt giữa yêu cầu dành cho cụm OpenShift một nút duy nhất và cụm có tính sẵn sàng cao:

* Một cụm có tính sẵn sàng cao yêu cầu một máy khởi động (bootstrap machine) tạm thời, ba máy điều khiển (control plane machines) và ít nhất hai máy tính toán (compute machines). Đối với cụm OpenShift một nút duy nhất, bạn chỉ cần một máy khởi động tạm thời và một thực thể đám mây (cloud instance) đóng vai trò là nút điều khiển; không cần các nút tính toán riêng biệt.
* Yêu cầu tối thiểu về tài nguyên cho cụm có tính sẵn sàng cao bao gồm một nút điều khiển với 4 vCPU và 100 GB dung lượng lưu trữ. Đối với cụm OpenShift một nút duy nhất, bạn cần tối thiểu 4 vCPU và 120 GB dung lượng lưu trữ. 

> **Quan trọng:** Việc chạy OpenShift trên một nút duy nhất với 4 vCPU sẽ để lại rất ít "khoảng trống" (headroom) cho các ứng dụng người dùng, dẫn đến nguy cơ cao xảy ra tình trạng tranh chấp tài nguyên và suy giảm hiệu năng.  
> Để đảm bảo sự ổn định của cụm ở mức cấu hình này, bạn cần thực hiện các biện pháp nhằm giảm thiểu tổng lượng tài nguyên tiêu thụ, chẳng hạn như giới hạn số lượng khối lượng công việc (workload) chạy trên cụm hoặc hạn chế các tính năng của cụm. Để biết thêm thông tin, hãy xem phần "Các tính năng của cụm" (Cluster capabilities).  
> Ngoài ra, bạn nên cân nhắc cung cấp thêm tài nguyên tính toán cho cụm. 

* Thiết lập `controlPlane.replicas` trong tệp `install-config.yaml` cần được đặt là 1.
* Thiết lập `compute.replicas` trong tệp `install-config.yaml` cần được đặt là 0. Cấu hình này cho phép các ứng dụng được lập lịch chạy trên chính nút điều khiển (control plane node).

### II.4. Tạo tệp ảnh ISO có khả năng khởi động trên ổ đĩa USB
Bạn có thể cài đặt phần mềm bằng cách sử dụng ổ đĩa USB có khả năng khởi động, trong đó chứa tệp ảnh ISO. Việc khởi động máy chủ từ ổ đĩa USB này sẽ chuẩn bị cho quá trình cài đặt phần mềm trên máy chủ đó.

**Quy trình**

1. Trên máy chủ quản trị, hãy cắm ổ đĩa USB vào cổng USB. 
2. Tạo ổ đĩa USB có khả năng khởi động, ví dụ:

```bash
$ dd if=<path_to_iso> of=<path_to_usb> status=progress
```

Trong đó:

* `<path_to_iso>`: là đường dẫn tương đối đến tệp ISO đã tải xuống, ví dụ: rhcos-live.iso.
* `<path_to_usb>`: là vị trí của ổ đĩa USB đã kết nối, ví dụ: /dev/sdb.

Sau khi tệp ISO được sao chép vào ổ đĩa USB, bạn có thể sử dụng ổ đĩa USB đó để cài đặt phần mềm trên máy chủ.
