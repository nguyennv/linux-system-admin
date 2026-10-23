Cài đặt OpenShift
-----------------

# Cài đặt cụm tại chỗ (on-premise) bằng trình cài đặt dựa trên tác nhân (Agent-based Installer)
## I. Chuẩn bị cài đặt bằng trình cài đặt dựa trên tác nhân
Agent-based Installer (trình cài đặt dựa trên tác nhân) mang đến sự linh hoạt, cho phép bạn khởi động các máy chủ tại chỗ (on-premise) theo bất kỳ phương thức nào bạn chọn. Công cụ này kết hợp sự tiện lợi của dịch vụ Assisted Installation (cài đặt có hỗ trợ) với khả năng vận hành ngoại tuyến, bao gồm cả trong các môi trường bị cách ly hoàn toàn với mạng bên ngoài (air-gapped).

Agent-based Installer sử dụng một lệnh phụ (subcommand) của chương trình cài đặt OpenShift Container Platform. Nó tạo ra một tệp ảnh ISO có khả năng khởi động, chứa tất cả thông tin cần thiết để triển khai một cụm (cluster) OpenShift Container Platform, cùng với một tệp ảnh phát hành (release image) đi kèm.

Cấu hình được thiết lập theo cùng định dạng với các phương thức cài đặt trên hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure) hoặc hạ tầng do người dùng cung cấp (user-provisioned infrastructure). Ngoài ra, Agent-based Installer còn có tùy chọn tạo hoặc chấp nhận các tài nguyên tùy chỉnh (custom resources) cho quy trình Zero Touch Provisioning (ZTP). ZTP cho phép bạn thiết lập các địa điểm biên (edge site) mới thông qua các cấu hình mang tính khai báo (declarative configurations) cho các thiết bị phần cứng vật lý (bare-metal).

> **Lưu ý:** Để triển khai các cụm với mặt phẳng điều khiển (control plane) được ảo hóa chạy trên các máy ảo (VM) của OpenShift Virtualization, bạn có thể sử dụng KubeVirt Redfish để hiển thị các máy ảo này dưới dạng các điểm cuối (endpoint) tương thích với Redfish. Để biết thêm thông tin về việc sử dụng các mặt phẳng điều khiển được ảo hóa, hãy xem phần "Sử dụng các mặt phẳng điều khiển được ảo hóa".

**Bảng 1.1. Các kiến trúc được hỗ trợ bởi trình cài đặt dựa trên tác nhân**

| Kiến trúc CPU | Cài đặt kết nối | Cài đặt không kết nối |
|---------------|-----------------|-----------------------|
| 64-bit x86    | ✓               | ✓                     |
| 64-bit ARM    | ✓               | ✓                     |
| ppc64le       | ✓               | ✓                     |
| s390x         | ✓               | ✓                     |

### I.1. Tìm hiểu về Trình cài đặt dựa trên tác nhân
Là người dùng OpenShift Container Platform, bạn có thể tận dụng các ưu điểm của dịch vụ Assisted Installer (Trình cài đặt hỗ trợ) trong các môi trường không kết nối mạng (disconnected environments).

Agent-based Installer sử dụng một tệp ISO có khả năng khởi động (bootable ISO), chứa tác nhân khám phá (Assisted discovery agent) và dịch vụ hỗ trợ (Assisted Service). Cả hai thành phần này đều cần thiết để thực hiện cài đặt cụm (cluster), trong đó Assisted Service chỉ chạy trên một trong các máy chủ (host).

> **Lưu ý:** Hiện tại, việc hỗ trợ khởi động từ tệp ISO trên nền tảng IBM Z® (`s390x`) chỉ khả dụng đối với Red Hat Enterprise Linux (RHEL) KVM; cấu hình này cho phép linh hoạt lựa chọn phương thức cài đặt qua PXE hoặc qua tệp ISO. Đối với các hệ thống sử dụng z/VM và Phân vùng logic (Logical Partition - LPAR), chỉ hỗ trợ phương thức khởi động qua PXE.

Lệnh phụ `openshift-install agent create image` sẽ tạo ra một tệp ISO tạm thời dựa trên các thông tin đầu vào mà bạn cung cấp. Bạn có thể cung cấp các thông tin này thông qua các tệp cấu hình (manifest) sau:

Các tệp cấu hình bắt buộc:

* install-config.yaml
* agent-config.yaml

Các tệp cấu hình tùy chọn (dành cho ZTP - Zero Touch Provisioning):

* cluster-manifests/cluster-deployment.yaml
* cluster-manifests/agent-cluster-install.yaml
* cluster-manifests/pull-secret.yaml
* cluster-manifests/infraenv.yaml
* cluster-manifests/cluster-image-set.yaml
* cluster-manifests/nmstateconfig.yaml
* mirror/registries.conf
* mirror/ca-bundle.crt

#### I.1.1. Quy trình cài đặt dựa trên tác nhân
Một trong các máy chủ thuộc mặt phẳng điều khiển (control plane) sẽ chạy dịch vụ Assisted Service ngay khi bắt đầu quá trình khởi động và sau đó trở thành máy chủ khởi động ban đầu (bootstrap host). Nút này được gọi là máy chủ tập hợp (rendezvous host hay nút 0).

Dịch vụ Assisted Service đảm bảo rằng tất cả các máy chủ đều đáp ứng các yêu cầu cần thiết, đồng thời kích hoạt quá trình triển khai cụm OpenShift Container Platform. Hình ảnh hệ điều hành Red Hat Enterprise Linux (RHEL) sẽ được ghi vào ổ đĩa của tất cả các nút. Các nút không phải là nút khởi động ban đầu sẽ thực hiện khởi động lại và bắt đầu quá trình triển khai cụm.

Sau khi các nút khác đã khởi động lại, máy chủ tập hợp (rendezvous host) cũng sẽ khởi động lại và tham gia vào cụm. Đến đây, quá trình khởi động ban đầu (bootstrapping) hoàn tất và cụm đã được triển khai thành công.

**Hình 1.1. Quy trình cài đặt các nút**  
![Hình 1.1](img/agent-based-installer-workflow.png)

Bạn có thể cài đặt một cụm OpenShift Container Platform trong môi trường không kết nối mạng (disconnected) thông qua lệnh phụ `openshift-install agent create image`, áp dụng cho các cấu trúc liên kết (topology) sau:

* **Cụm OpenShift Container Platform một nút (single-node)**: Một nút đóng vai trò vừa là mặt phẳng điều khiển (control plane) vừa là nút tính toán (compute node). 
* **Cụm OpenShift Container Platform ba nút (three-node)**: Một cụm tinh gọn gồm ba nút đóng vai trò vừa là mặt phẳng điều khiển vừa là nút tính toán. 
* **Cụm OpenShift Container Platform có tính sẵn sàng cao (HA)**: Gồm ba nút mặt phẳng điều khiển cùng với số lượng nút tính toán tùy ý. 
* **Cụm OpenShift Container Platform hai nút với nút trọng tài (Arbiter)**: Gồm hai nút mặt phẳng điều khiển và một nút trọng tài cục bộ (local arbiter node). Để biết thêm thông tin, hãy xem phần "Giới thiệu về nút trọng tài cục bộ".

#### I.1.2. Các tài nguyên được khuyến nghị cho các cấu trúc liên kết
Các tài nguyên cụm (cluster resources) sau đây được khuyến nghị cho từng cấu trúc liên kết:

**Bảng 1.2. Các tài nguyên cụm được khuyến nghị**

| Topology                                      | Số mặt phẳng điều khiển                         | Số nút tính toán | vCPU    | Bộ nhớ    | Lưu trữ |
|-----------------------------------------------|-------------------------------------------------|------------------|---------|-----------|---------|
| Cụm đơn nút                                   | 1                                               | 0                | 8 vCPUs | 16 GB RAM | 120 GB  |
| Cụm OpenShift hai nút với Arbiter             | 2 (nút mặt phẳng điều khiển), 1 (nút trọng tài) | 0                | 4 vCPUs | 16 GB RAM | 120 GB  |
| Cụm OpenShift hai nút có cơ chế cách ly (TNF) | 2                                               | 0                | 4 vCPUs | 16 GB RAM | 120 GB  |
| Cụm nhỏ gọn                                   | 3                                               | 0 hoặc 1         | 8 vCPUs | 16 GB RAM | 120 GB  |
| Cụm HA (High Availability)                    | 3 đến 5                                         | từ 2 trở lên     | 8 vCPUs | 16 GB RAM | 120 GB  |

> **Lưu ý:** Bạn có thể sử dụng tối thiểu 4 vCPU cho một cụm OpenShift đơn nút (single-node).  
> Tuy nhiên, việc chạy OpenShift đơn nút trên 4 vCPU sẽ để lại rất ít "khoảng trống" (headroom) cho các ứng dụng người dùng, đồng thời tạo ra nguy cơ cao về tranh chấp tài nguyên và suy giảm hiệu năng.  
> Để đảm bảo tính ổn định của cụm ở mức cấu hình này, bạn cần thực hiện các biện pháp nhằm giảm thiểu tổng lượng tài nguyên tiêu thụ, chẳng hạn như giới hạn số lượng khối lượng công việc (workload) đang chạy trên cụm hoặc hạn chế các tính năng của cụm. Để biết thêm thông tin, hãy xem phần "Các tính năng của cụm" (Cluster capabilities).  
> Ngoài ra, bạn nên cân nhắc cung cấp thêm tài nguyên tính toán cho cụm.

#### I.1.3. Các nền tảng được hỗ trợ
Trong tệp `install-config.yaml`, hãy chỉ định nền tảng mà bạn muốn thực hiện cài đặt. Các nền tảng sau đây được hỗ trợ:

* baremetal
* vsphere
* nutanix
* external
* none

Đối với cụm OpenShift Container Platform gồm hai nút có cơ chế cách ly (fencing - TNF), chỉ các nền tảng sau đây được hỗ trợ:

* baremetal
* external
* none

Các nền tảng `vsphere` và `nutanix` không được hỗ trợ cho các cụm gồm hai nút có cơ chế cách ly.

> **Quan trọng:** Đối với tùy chọn nền tảng "none" (không xác định):
> 
> * Tùy chọn "none" yêu cầu bạn phải tự cung cấp cơ sở hạ tầng phân giải tên DNS và cân bằng tải trong cụm (cluster) của mình. Để biết thêm thông tin, hãy xem phần "Các yêu cầu đối với cụm sử dụng tùy chọn nền tảng 'none'" trong mục "Tài nguyên bổ sung". 
> * Trước khi cố gắng cài đặt cụm OpenShift Container Platform trong môi trường ảo hóa hoặc môi trường đám mây, hãy tham khảo tài liệu "Triển khai OpenShift 4.x trên các nền tảng chưa được kiểm thử bằng phương pháp cài đặt trên phần cứng vật lý (bare metal)".
> 
> Đối với nền tảng "vsphere", hoặc nền tảng "baremetal" chạy trên vSphere:
> 
> * Bạn phải thiết lập tham số `disk.EnableUUID` thành `TRUE` trên tất cả các nút (node) của OpenShift Container Platform. Nếu tham số này không được kích hoạt, quá trình kiểm tra xác thực của trình cài đặt dựa trên tác nhân sẽ thất bại.

### I.2. Về tuân thủ FIPS
Đối với nhiều khách hàng sử dụng OpenShift Container Platform, việc đáp ứng các quy định—hay còn gọi là tuân thủ—là yêu cầu bắt buộc ở một mức độ nhất định trước khi đưa bất kỳ hệ thống nào vào môi trường vận hành thực tế (production). Các yêu cầu tuân thủ này có thể xuất phát từ các tiêu chuẩn quốc gia, tiêu chuẩn ngành hoặc khung quản trị doanh nghiệp của chính tổ chức đó.

Tuân thủ các Tiêu chuẩn Xử lý Thông tin Liên bang (Federal Information Processing Standards - FIPS) là một trong những yếu tố quan trọng nhất trong các môi trường có yêu cầu bảo mật cao, nhằm đảm bảo rằng chỉ những công nghệ mật mã được hỗ trợ mới được phép sử dụng trên các nút (node).

> **Lưu ý quan trọng:** Để kích hoạt chế độ FIPS cho cụm (cluster) của mình, bạn phải chạy chương trình cài đặt từ một máy tính sử dụng hệ điều hành Red Hat Enterprise Linux (RHEL) đã được cấu hình để vận hành ở chế độ FIPS. Để biết thêm thông tin về cách cấu hình chế độ FIPS trên RHEL, hãy xem phần Chuyển đổi RHEL sang chế độ FIPS.  
> Khi chạy trên Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã của RHEL—những thư viện đã được gửi tới NIST để thực hiện quy trình xác thực FIPS 140-2/140-3—trên các kiến trúc hệ thống x86_64, ppc64le và s390x.

### I.3. Cấu hình FIPS thông qua trình cài đặt dựa trên tác nhân
Trong quá trình triển khai cụm (cluster), thay đổi liên quan đến Tiêu chuẩn Xử lý Thông tin Liên bang (FIPS) sẽ được áp dụng khi các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) được triển khai trong cụm của bạn.

Đối với các máy chạy hệ điều hành Red Hat Enterprise Linux (RHEL), bạn phải bật chế độ FIPS khi cài đặt hệ điều hành trên những máy mà bạn dự định sử dụng làm máy worker (worker machine).

> **Quan trọng:** OpenShift Container Platform yêu cầu sử dụng tệp nhị phân cài đặt có hỗ trợ FIPS để triển khai cụm ở chế độ FIPS.

Bạn có thể bật chế độ FIPS thông qua phương thức được khuyến nghị là sử dụng các tệp `install-config.yaml` và `agent-config.yaml`:

Bạn phải đặt giá trị của trường `fips` thành `true` trong tệp `install-config.yaml`:

Ví dụ về tệp `install-config.yaml`

```yaml
apiVersion: v1
baseDomain: test.example.com
metadata:
  name: sno-cluster
fips: true
```

> **Quan trọng:** Để bật chế độ FIPS trên các cụm IBM Z®, bạn cũng phải bật FIPS trong tệp `.parm` hoặc thông qua lệnh `virt-install`, theo các quy trình hướng dẫn cách thêm thủ công các tác nhân (agent) IBM Z®.

Nếu bạn đang sử dụng các tệp cấu hình (manifest) GitOps ZTP tùy chọn, bạn phải đặt giá trị fips thành true trong trường agent-install.openshift.io/install-config-overrides thuộc tệp a`gent-cluster-install.yaml`:

Ví dụ về tệp `agent-cluster-install.yaml`

```yaml
apiVersion: extensions.hive.openshift.io/v1beta1
kind: AgentClusterInstall
metadata:
  annotations:
    agent-install.openshift.io/install-config-overrides: '{"fips":true}'
  name: sno-cluster
  namespace: sno-cluster-test
```

### I.4. Cấu hình máy chủ (host)
Bạn có thể thực hiện các cấu hình bổ sung cho từng máy chủ trong cụm thông qua tệp `agent-config.yaml`, chẳng hạn như các cấu hình mạng và thông tin gợi ý về thiết bị gốc (root device hints).

> **Quan trọng:** Đối với mỗi máy chủ cần cấu hình, bạn phải xác định rõ máy chủ đó bằng cách cung cấp địa chỉ MAC của một giao diện mạng trên máy chủ đó.

#### I.4.1. Vai trò của các host
Mỗi host trong cụm được chỉ định một vai trò là master (chủ) hoặc worker (nút làm việc). Bạn có thể xác định vai trò cho từng host trong tệp `agent-config.yaml` bằng cách sử dụng tham số `role`. Nếu bạn không chỉ định vai trò cho các host, các vai trò sẽ được gán ngẫu nhiên trong quá trình cài đặt.

Bạn nên xác định rõ ràng vai trò cho các host của mình.

`rendezvousIP` phải được gán cho một host có vai trò là master. Việc này có thể được thực hiện theo cách thủ công hoặc để trình cài đặt tự động gán vai trò.

> **Quan trọng:** Bạn không bắt buộc phải xác định rõ ràng vai trò master cho host đóng vai trò `rendezvous`, tuy nhiên, bạn không được tạo các cấu hình gây xung đột với việc gán vai trò này.

Ví dụ: Nếu bạn có 4 host, trong đó 3 host được xác định rõ ràng là master, thì host còn lại (được tự động gán vai trò worker trong quá trình cài đặt) sẽ không thể được cấu hình làm host `rendezvous`.

Tệp `agent-config.yaml` mẫu

```yaml
apiVersion: v1beta1
kind: AgentConfig
metadata:
  name: example-cluster
rendezvousIP: 192.168.111.80
hosts:
  - hostname: master-1
    role: master
    interfaces:
      - name: eno1
        macAddress: 00:ef:44:21:e6:a5
  - hostname: master-2
    role: master
    interfaces:
      - name: eno1
        macAddress: 00:ef:44:21:e6:a6
  - hostname: master-3
    role: master
    interfaces:
      - name: eno1
        macAddress: 00:ef:44:21:e6:a7
  - hostname: worker-1
    role: worker
    interfaces:
      - name: eno1
        macAddress: 00:ef:44:21:e6:a8
```

#### I.4.2. Về các gợi ý thiết bị gốc (root device hints)
Tham số `rootDeviceHints` cho phép chương trình cài đặt triển khai ảnh hệ thống Red Hat Enterprise Linux CoreOS (RHCOS) vào một thiết bị cụ thể.

Chương trình cài đặt sẽ kiểm tra các thiết bị theo thứ tự phát hiện được, sau đó so sánh các giá trị thu thập từ thiết bị với các giá trị gợi ý (hint values). Chương trình sẽ chọn thiết bị đầu tiên khớp với giá trị gợi ý đó. Cấu hình có thể kết hợp nhiều gợi ý cùng lúc, nhưng một thiết bị phải thỏa mãn tất cả các gợi ý này thì chương trình cài đặt mới chọn nó.

**Bảng 1.3. Các trường con**

| Trường con       | Mô tả                                                                                                                                                                                                                                                                         |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| deviceName       | Một chuỗi chứa tên thiết bị Linux, chẳng hạn như `/dev/vda` hoặc `/dev/disk/by-path/`. Bạn nên sử dụng liên kết `/dev/disk/by-path/<device_path>` để trỏ đến vị trí lưu trữ. Giá trị gợi ý phải khớp chính xác với giá trị thực tế.                                           |
| hctl             | Một chuỗi chứa địa chỉ bus SCSI, chẳng hạn như `0:0:0:0`. Thông tin gợi ý này phải khớp chính xác với giá trị thực tế.                                                                                                                                                        |
| model            | Một chuỗi chứa định danh thiết bị dành riêng cho nhà cung cấp. Gợi ý này có thể là một chuỗi con của giá trị thực tế.                                                                                                                                                         |
| vendor           | Một chuỗi chứa tên nhà cung cấp hoặc nhà sản xuất thiết bị. Gợi ý này có thể là một chuỗi con của giá trị thực tế.                                                                                                                                                            |
| serialNumber     | Một chuỗi chứa số sê-ri của thiết bị. Gợi ý phải khớp chính xác với giá trị thực tế.                                                                                                                                                                                          |
| minSizeGigabytes | Một số nguyên biểu thị dung lượng tối thiểu của thiết bị, tính bằng gigabyte.                                                                                                                                                                                                 |
| wwn              | Một chuỗi chứa định danh lưu trữ duy nhất. Giá trị gợi ý phải khớp chính xác với giá trị thực tế. Nếu bạn sử dụng lệnh `udevadm` để truy xuất giá trị WWN và lệnh này trả về giá trị cho `ID_WWN_WITH_EXTENSION`, thì bạn phải sử dụng giá trị đó để chỉ định trường con WWN. |
| rotational       | Một giá trị kiểu boolean cho biết thiết bị có nên là đĩa quay (true) hay không (false).                                                                                                                                                                                       |

**Ví dụ sử dụng**

```yaml
     - name: master-0
       role: master
       rootDeviceHints:
         deviceName: "/dev/sda"
```

### I.5. Về mạng
Địa chỉ IP rendezvous (điểm tập kết) phải được xác định tại thời điểm tạo tệp ISO cho tác nhân (agent ISO), để trong quá trình khởi động ban đầu, tất cả các máy chủ (host) có thể kết nối với dịch vụ hỗ trợ (assisted service).

Nếu các địa chỉ IP được cấp phát thông qua máy chủ Giao thức Cấu hình Máy chủ Động (DHCP), thì trường `rendezvousIP` phải được đặt thành địa chỉ IP của một trong các máy chủ sẽ trở thành một phần của mặt phẳng điều khiển (control plane) được triển khai. Trong môi trường không có máy chủ DHCP, bạn có thể thiết lập địa chỉ IP theo cách tĩnh (static).

Ngoài các địa chỉ IP tĩnh, bạn có thể áp dụng bất kỳ cấu hình mạng nào ở định dạng NMState. Điều này bao gồm các VLAN và các kết nối gộp giao diện mạng (NIC bonds).

> **Lưu ý:** Theo mặc định, Podman sử dụng dải mạng con (subnet) 10.88.0.0/16 làm mạng cầu nối (bridge network). Không được đặt tham số `network.machineNetwork.cidr` bao gồm dải địa chỉ này; nếu không, xung đột sẽ xảy ra và khiến quá trình cài đặt cụm (cluster) bị thất bại.

#### I.5.1. Các yêu cầu về cổng (port) đối với máy chủ điều phối (rendezvous host)
Trong các giai đoạn khám phá (discovery) và khởi động (bootstrap) của quá trình cài đặt, tất cả các máy chủ đều kết nối với Dịch vụ Hỗ trợ (Assisted Service) đang chạy trên máy chủ điều phối. Hãy cấu hình tường lửa của bạn để cho phép lưu lượng truy cập sau đây từ mỗi máy chủ đến máy chủ điều phối:

**Bảng 1.4. Các cổng cần thiết để kết nối với Dịch vụ Hỗ trợ (Assisted Service) trên máy chủ điều phối**

| Cổng | Giao thức | Mô tả                                                                                                                                                                  |
|------|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 8090 | TCP       | API Dịch vụ Hỗ trợ (Assisted Service API). Các máy chủ sử dụng cổng này để đăng ký với Dịch vụ Hỗ trợ, báo cáo thông tin phần cứng và truy xuất các hướng dẫn cài đặt. |

> **Lưu ý:** Cổng 8090 chỉ cần thiết trong quá trình cài đặt. Sau khi quá trình cài đặt hoàn tất, dịch vụ hỗ trợ (Assisted Service) sẽ không còn hiển thị trên máy chủ điều phối (rendezvous host).

Yêu cầu về cổng cụ thể này nằm ngoài các yêu cầu mạng tiêu chuẩn của OpenShift Container Platform dành cho việc cài đặt.

#### I.5.2. DHCP
Khi sử dụng Giao thức Cấu hình Host Động (DHCP), bạn phải chỉ định giá trị cho trường `rendezvousIP` trong tệp `agent-config.yaml`, còn các trường `networkConfig` có thể để trống:

Ví dụ về tệp `agent-config.yaml`

```yaml
apiVersion: v1alpha1
kind: AgentConfig
metadata:
  name: sno-cluster
rendezvousIP: 192.168.111.80
```

Trong đó: `rendezvousIP` chỉ định địa chỉ IP cho máy chủ rendezvous (rendezvous host).

#### I.5.3. Cấu hình mạng tĩnh
Khi sử dụng cấu hình mạng tĩnh với các tệp `install-config.yaml` và `agent-config.yaml` theo quy định, bạn có thể chỉ định giá trị cho trường `rendezvousIP` trong tệp `agent-config.yaml`, hoặc để chương trình cài đặt tự chọn một địa chỉ IP tĩnh từ các trường `networkConfig`.

**Tệp `agent-config.yaml` mẫu**

```yaml
apiVersion: v1alpha1
kind: AgentConfig
metadata:
  name: sno-cluster
rendezvousIP: 192.168.111.80
hosts:
  - hostname: master-0
    interfaces:
      - name: eno1
        macAddress: 00:ef:44:21:e6:a5
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 00:ef:44:21:e6:a5
          ipv4:
            enabled: true
            address:
              - ip: 192.168.111.80
                prefix-length: 23
            dhcp: false
      dns-resolver:
        config:
          server:
            - 192.168.111.1
      routes:
        config:
          - destination: 0.0.0.0/0
            next-hop-address: 192.168.111.1
            next-hop-interface: eno1
            table-id: 254
```

Trong đó:

* `rendezvousIP`: Xác định địa chỉ IP cho máy chủ rendezvous (máy chủ điều phối). Nếu trường rendezvousIP không được chỉ định giá trị, một địa chỉ sẽ được chọn từ các địa chỉ IP tĩnh đã khai báo trong các trường networkConfig.
* `hosts.interfaces.macAddress`: Xác định địa chỉ MAC của một giao diện (interface) trên máy chủ; thông tin này được dùng để xác định máy chủ nào sẽ được áp dụng cấu hình.
* `ipv4.address.ip`: Xác định địa chỉ IP tĩnh của máy chủ vật lý (bare-metal host) mục tiêu.
* `ipv4.address.prefix-length`: Xác định độ dài tiền tố mạng (subnet prefix) cho địa chỉ IP tĩnh của máy chủ vật lý mục tiêu.
* `dns-resolver.config.server`: Xác định máy chủ DNS cho máy chủ vật lý mục tiêu.
* `routes.config.next-hop-address`: Xác định địa chỉ "next-hop" (chặng kế tiếp) cho lưu lượng truy cập của nút (node). Địa chỉ này phải nằm cùng mạng con (subnet) với địa chỉ IP đã thiết lập cho giao diện tương ứng.

Khi sử dụng cấu hình mạng tĩnh (static networking) với phương thức tùy chọn thông qua các tài nguyên tùy chỉnh (custom resources) của GitOps ZTP — bao gồm 6 tài nguyên tùy chỉnh — bạn có thể thiết lập các địa chỉ IP tĩnh trong tệp `nmstateconfig.yaml`. Địa chỉ IP rendezvous (điểm tập kết) sẽ được chọn từ các địa chỉ IP tĩnh đã được chỉ định trong các trường cấu hình.

Tệp `nmstateconfig.yaml` mẫu

```yaml
apiVersion: agent-install.openshift.io/v1beta1
kind: NMStateConfig
metadata:
  name: master-0
  namespace: openshift-machine-api
  labels:
    cluster0-nmstate-label-name: cluster0-nmstate-label-value
spec:
  config:
    interfaces:
      - name: eth0
        type: ethernet
        state: up
        mac-address: 52:54:01:aa:aa:a1
        ipv4:
          enabled: true
          address:
            - ip: 192.168.122.2
              prefix-length: 23
          dhcp: false
    dns-resolver:
      config:
        server:
          - 192.168.122.1
    routes:
      config:
        - destination: 0.0.0.0/0
          next-hop-address: 192.168.122.1
          next-hop-interface: eth0
          table-id: 254
  interfaces:
    - name: eth0
      macAddress: 52:54:01:aa:aa:a1
```

Trong đó:

* `ipv4.address.ip`: Chỉ định địa chỉ IP tĩnh của máy chủ vật lý (bare-metal host) mục tiêu.
* `ipv4.address.prefix-length`: Chỉ định độ dài tiền tố mạng con (subnet prefix) cho địa chỉ IP tĩnh của máy chủ vật lý mục tiêu.
* `dns-resolver.config.server`: Chỉ định máy chủ DNS cho máy chủ vật lý mục tiêu.
* `routes.config.next-hop-address`: Chỉ định địa chỉ chặng kế tiếp (next-hop address) cho lưu lượng truy cập của nút (node). Địa chỉ này phải nằm trong cùng mạng con với địa chỉ IP được thiết lập cho giao diện (interface) tương ứng.
* `spec.interfaces.macAddress`: Chỉ định địa chỉ MAC của một giao diện trên máy chủ, được sử dụng để xác định máy chủ nào sẽ được áp dụng cấu hình.

### I.6. Các yêu cầu đối với cụm sử dụng tùy chọn nền tảng "none"
Có những yêu cầu bổ sung khi cài đặt một cụm sử dụng tùy chọn nền tảng "none" (không xác định) với trình cài đặt dựa trên tác nhân.

> **Quan trọng:** Hãy tham khảo mục "Triển khai OpenShift 4.x trên các nền tảng chưa được kiểm thử bằng phương pháp cài đặt trên phần cứng vật lý (bare metal)" trước khi bạn thực hiện cài đặt cụm OpenShift Container Platform trong các môi trường ảo hóa hoặc môi trường đám mây.

#### I.6.1. Các yêu cầu về DNS cho nền tảng "none"
Trong các triển khai OpenShift Container Platform, việc phân giải tên DNS là cần thiết đối với một số thành phần.

Các thành phần sau đây yêu cầu phân giải tên DNS:

* Kubernetes API
* Wildcard (tên miền đại diện) cho ứng dụng trên OpenShift Container Platform
* Các máy thuộc mặt phẳng điều khiển (control plane) và các máy tính toán (compute machines)

Phân giải DNS ngược (reverse DNS resolution) cũng là yêu cầu bắt buộc đối với Kubernetes API, các máy thuộc mặt phẳng điều khiển và các máy tính toán.

Các bản ghi DNS loại A/AAAA hoặc CNAME được sử dụng để phân giải tên, trong khi các bản ghi PTR được sử dụng cho phân giải tên ngược. Các bản ghi ngược đóng vai trò quan trọng vì Red Hat Enterprise Linux CoreOS (RHCOS) sử dụng chúng để thiết lập tên máy chủ (hostname) cho tất cả các nút (node), trừ khi các tên máy chủ này đã được cung cấp thông qua DHCP. Ngoài ra, các bản ghi ngược còn được dùng để tạo các yêu cầu ký chứng chỉ (Certificate Signing Request - CSR) mà OpenShift Container Platform cần để vận hành.
Lưu ý

Bạn nên sử dụng máy chủ DHCP để cung cấp tên máy chủ cho từng nút trong cụm (cluster node).

Các bản ghi DNS dưới đây là bắt buộc đối với một cụm OpenShift Container Platform sử dụng tùy chọn nền tảng "none" (platform none) và phải được thiết lập trước khi tiến hành cài đặt. Trong mỗi bản ghi, `<cluster_name>` là tên cụm và `<base_domain>` là tên miền cơ sở mà bạn chỉ định trong tệp `install-config.yaml`. Một bản ghi DNS hoàn chỉnh sẽ có dạng: `<component>.<cluster_name>.<base_domain>.`

**Bảng 1.5. Các bản ghi DNS bắt buộc**

| Thành phần            | Bản ghi                                   | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-----------------------|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kubernetes API        | api.<cluster_name>.<base_domain>.         | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định bộ cân bằng tải API. Các bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm và tất cả các nút bên trong cụm đó.                                                                                                                                                                                                                                                                                                                                                                                                                           |
|                       | api-int.<cluster_name>.<base_domain>.     | Cần có bản ghi DNS A/AAAA hoặc CNAME, cùng với bản ghi DNS PTR, để xác định bộ cân bằng tải (load balancer) API trong nội bộ hệ thống. Các bản ghi này phải có thể được phân giải từ tất cả các nút (node) bên trong cụm (cluster). **Quan trọng**: Máy chủ API (API server) phải có khả năng phân giải các nút worker thông qua tên máy chủ (hostname) đã được ghi lại trong Kubernetes. Nếu máy chủ API không thể phân giải tên các nút, các lệnh gọi API thông qua proxy có thể bị lỗi và bạn sẽ không thể truy xuất nhật ký (log) từ các pod.                                                                                                  |
| Routes                | *.apps.<cluster_name>.<base_domain>.      | Một bản ghi DNS wildcard (dạng A/AAAA hoặc CNAME) trỏ đến bộ cân bằng tải ingress (ingress load balancer) của ứng dụng. Bộ cân bằng tải ingress này hướng lưu lượng truy cập đến các máy chủ đang chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller này hoạt động trên các máy chủ tính toán (compute machines). Các bản ghi này phải có thể được phân giải bởi cả các client bên ngoài cụm lẫn tất cả các node nằm bên trong cụm. Ví dụ: `console-openshift-console.apps.<cluster_name>.<base_domain>` được sử dụng làm tuyến đường (route) wildcard dẫn đến bảng điều khiển (console) của OpenShift Container Platform. |
| CMặt phẳng điều khiển | <master><n>.<cluster_name>.<base_domain>. | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút thuộc mặt phẳng điều khiển (control plane nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                              |
| Các nút tính toán     | <worker><n>.<cluster_name>.<base_domain>. | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút worker (worker nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                         |

> **Lưu ý:** Trong OpenShift Container Platform 4.4 trở về sau, bạn không cần chỉ định các bản ghi SRV và máy chủ etcd trong cấu hình DNS của mình.

##### I.6.1.1. Ví dụ về cấu hình DNS cho các cụm sử dụng nền tảng "none"
Phần này cung cấp các mẫu cấu hình bản ghi A và PTR đáp ứng các yêu cầu DNS để triển khai OpenShift Container Platform với tùy chọn nền tảng là "none". Các ví dụ này không nhằm mục đích đưa ra lời khuyên về việc lựa chọn giải pháp DNS này hay giải pháp DNS khác.

Trong các ví dụ, tên cụm là `ocp4` và tên miền cơ sở là `example.com`.

Ví dụ về cấu hình bản ghi DNS A cho cụm sử dụng nền tảng "none"
Ví dụ dưới đây là tệp vùng (zone file) BIND, hiển thị các bản ghi A mẫu phục vụ việc phân giải tên trong một cụm sử dụng tùy chọn nền tảng "none".

Mẫu cơ sở dữ liệu vùng DNS

> **Lưu ý:** Từ phiên bản OpenShift Container Platform 4.4 trở đi, bạn không cần phải chỉ định các bản ghi máy chủ (host) etcd và bản ghi SRV trong cấu hình DNS của mình.

```
$TTL 1W
@   IN  SOA ns1.example.com.    root (
            2019070700  ; serial
            3H      ; refresh (3 hours)
            30M     ; retry (30 minutes)
            2W      ; expiry (2 weeks)
            1W )        ; minimum (1 week)
    IN  NS  ns1.example.com.
    IN  MX 10   smtp.example.com.
;
;
ns1.example.com.        IN  A   192.168.1.5
smtp.example.com.       IN  A   192.168.1.5
;
helper.example.com.     IN  A   192.168.1.5
helper.ocp4.example.com.    IN  A   192.168.1.5
;
api.ocp4.example.com.       IN  A   192.168.1.5
api-int.ocp4.example.com.   IN  A   192.168.1.5
;
*.apps.ocp4.example.com.    IN  A   192.168.1.5
;
master0.ocp4.example.com.   IN  A   192.168.1.97
master1.ocp4.example.com.   IN  A   192.168.1.98
master2.ocp4.example.com.   IN  A   192.168.1.99
;
worker0.ocp4.example.com.   IN  A   192.168.1.11
worker1.ocp4.example.com.   IN  A   192.168.1.7
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho API và được sử dụng cho các hoạt động giao tiếp nội bộ trong cụm (cluster).
* `*.apps.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các tuyến đường (route) dạng ký tự đại diện (wildcard). Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho ứng dụng (application ingress load balancer). Bộ cân bằng tải này hướng lưu lượng truy cập đến các máy chủ đang chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller sẽ chạy trên các máy chủ tính toán (compute machine). 

> **Lưu ý:** Trong ví dụ này, cùng một bộ cân bằng tải được sử dụng cho cả Kubernetes API và lưu lượng truy cập ứng dụng (application ingress). Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt bộ cân bằng tải cho API và bộ cân bằng tải cho ứng dụng, nhờ đó có thể mở rộng hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

* `master0.ocp4.example.com.` – `master2.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy chủ thuộc mặt phẳng điều khiển (control plane).
* `worker0.ocp4.example.com.` – `worker1.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy chủ tính toán (compute machine).

**Ví dụ về cấu hình bản ghi DNS PTR cho cụm sử dụng tùy chọn nền tảng "none"**

Tệp vùng (zone file) BIND dưới đây trình bày các bản ghi PTR mẫu phục vụ cho việc phân giải tên ngược trong một cụm sử dụng tùy chọn nền tảng "none".

**Cơ sở dữ liệu vùng DNS mẫu cho các bản ghi phân giải ngược**

```
$TTL 1W
@   IN  SOA ns1.example.com.    root (
            2019070700  ; serial
            3H      ; refresh (3 hours)
            30M     ; retry (30 minutes)
            2W      ; expiry (2 weeks)
            1W )        ; minimum (1 week)
    IN  NS  ns1.example.com.
;
5.1.168.192.in-addr.arpa.   IN  PTR api.ocp4.example.com.
5.1.168.192.in-addr.arpa.   IN  PTR api-int.ocp4.example.com.
;
97.1.168.192.in-addr.arpa.  IN  PTR master0.ocp4.example.com.
98.1.168.192.in-addr.arpa.  IN  PTR master1.ocp4.example.com.
99.1.168.192.in-addr.arpa.  IN  PTR master2.ocp4.example.com.
;
11.1.168.192.in-addr.arpa.  IN  PTR worker0.ocp4.example.com.
7.1.168.192.in-addr.arpa.   IN  PTR worker1.ocp4.example.com.
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược (reverse DNS resolution) cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải (load balancer) API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải API và được sử dụng cho các hoạt động giao tiếp nội bộ trong cụm (cluster).
`master0.ocp4.example.com.` – `master2.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy thuộc mặt phẳng điều khiển (control plane).
`worker0.ocp4.example.com.` – `worker1.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy tính toán (compute machines).

> **Lưu ý:** Không bắt buộc phải có bản ghi PTR cho tên miền đại diện (wildcard) của ứng dụng trên OpenShift Container Platform.

#### I.6.2. Các yêu cầu về cân bằng tải cho nền tảng "none"
Trước khi cài đặt OpenShift Container Platform, bạn phải cung cấp cơ sở hạ tầng cân bằng tải cho API và Ingress ứng dụng. Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và Ingress ứng dụng, nhờ đó có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Các yêu cầu này không áp dụng cho các cụm OpenShift đơn nút (single-node) sử dụng tùy chọn nền tảng "none". 
Nếu bạn muốn triển khai các bộ cân bằng tải cho API và Ingress ứng dụng trên một instance Red Hat Enterprise Linux (RHEL), bạn phải mua riêng gói đăng ký (subscription) cho RHEL.

Cơ sở hạ tầng cân bằng tải phải đáp ứng các yêu cầu sau:

###### 1 - Bộ cân bằng tải API
Cung cấp một điểm cuối chung để người dùng (bao gồm cả con người và máy móc) tương tác với nền tảng và định cấu hình nền tảng đó. Hãy định cấu hình các điều kiện sau:

* Chỉ sử dụng cân bằng tải Lớp 4 (Layer 4). Chế độ này còn được gọi là Raw TCP, SSL Passthrough hoặc SSL Bridge. Nếu sử dụng chế độ SSL Bridge, bạn phải bật tính năng Chỉ báo Tên Máy chủ (Server Name Indication - SNI) cho các tuyến API (API routes). 
* Sử dụng thuật toán cân bằng tải không lưu trạng thái (stateless). Các tùy chọn có thể khác nhau tùy thuộc vào cơ chế triển khai bộ cân bằng tải.

> **Quan trọng:** Không định cấu hình tính năng duy trì phiên (session persistence) cho bộ cân bằng tải API.

Định cấu hình các cổng sau ở cả phía trước (front-end) và phía sau (back-end) của các bộ cân bằng tải:
**Bảng 1.6. Bộ cân bằng tải API**

| Cổng  | Các máy chủ phía sau                                                                                                                       | Nội bộ | Bên ngoài | Mô tả                  |
|-------|--------------------------------------------------------------------------------------------------------------------------------------------|--------|-----------|------------------------|
| 6443  | Mặt phẳng điều khiển. Bạn phải cấu hình endpoint /readyz để phục vụ cho quá trình kiểm tra tình trạng (health check probe) của API server. | X      | X         | Máy chủ API Kubernetes |
| 22623 | Mặt phẳng điều khiển                                                                                                                       | X      |           | Máy chủ cấu hình máy   |


> **Quan trọng:** Trình cài đặt dựa trên tác nhân yêu cầu mở cổng TCP 8090 giữa tất cả các máy chủ (host) và máy chủ điều phối (rendezvous host) để các máy chủ này có thể truy cập vào API của Dịch vụ Hỗ trợ (Assisted Service API). Cổng 8090 chỉ cần thiết trong các giai đoạn khám phá (discovery) và khởi động hệ thống (bootstrap). Để biết thêm thông tin, hãy xem phần "Các yêu cầu về cổng cho máy chủ điều phối".

> **Lưu ý:** Bộ cân bằng tải (load balancer) phải được cấu hình sao cho khoảng thời gian từ lúc máy chủ API tắt điểm cuối (endpoint) `/readyz` cho đến khi thực thể máy chủ API đó bị loại bỏ khỏi nhóm (pool) không được vượt quá 30 giây. Trong khoảng thời gian sau khi `/readyz` trả về lỗi hoặc chuyển sang trạng thái khỏe mạnh (healthy), điểm cuối đó phải được loại bỏ hoặc thêm vào tương ứng. Việc thực hiện kiểm tra (probe) mỗi 5 hoặc 10 giây, với quy định cần 2 phản hồi thành công liên tiếp để xác định trạng thái khỏe mạnh và 3 phản hồi không thành công liên tiếp để xác định trạng thái không khỏe mạnh, là các thông số đã được kiểm chứng hiệu quả.

###### 2 - Bộ cân bằng tải Ingress cho ứng dụng
Cung cấp điểm truy cập cho lưu lượng ứng dụng đi từ bên ngoài cụm vào. Cụm OpenShift Container Platform bắt buộc phải có cấu hình hoạt động cho bộ định tuyến Ingress (Ingress router).

Hãy cấu hình các điều kiện sau:

* Chỉ sử dụng cân bằng tải lớp 4 (Layer 4 load balancing). Cấu hình này có thể được gọi là chế độ Raw TCP, SSL Passthrough hoặc SSL Bridge. Nếu sử dụng chế độ SSL Bridge, bạn phải bật tính năng Server Name Indication (SNI) cho các tuyến đường (route) Ingress. 
* Khuyến nghị sử dụng cơ chế duy trì phiên (session persistence) dựa trên kết nối hoặc dựa trên phiên, tùy thuộc vào các tùy chọn khả dụng và loại ứng dụng sẽ được lưu trữ trên nền tảng.

> **Mẹo:** Nếu bộ cân bằng tải Ingress có thể nhận diện được địa chỉ IP thực của máy khách (client), thì việc bật tính năng duy trì phiên dựa trên địa chỉ IP nguồn (source IP-based session persistence) có thể giúp cải thiện hiệu suất cho các ứng dụng sử dụng mã hóa TLS đầu cuối (end-to-end TLS encryption).

Cấu hình các cổng (port) sau đây ở cả phía trước và phía sau của các bộ cân bằng tải:

**Bảng 1.7. Bộ cân bằng tải Ingress cho ứng dụng**

| Cổng | Máy chủ phía sau                                                                                                          | Nội bộ | Bên ngoài | Mô tả           |
|------|---------------------------------------------------------------------------------------------------------------------------|--------|-----------|-----------------|
| 443  | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng HTTPS |
| 80   | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng HTTP  |

> **Lưu ý:** Nếu bạn triển khai cụm gồm ba nút (node) mà không có nút tính toán (compute node) nào, các pod của Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển (control plane node). Đối với các cụm gồm ba nút, bạn phải cấu hình bộ cân bằng tải Ingress cho ứng dụng của mình để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển.

##### I.6.2.1. Ví dụ về cấu hình bộ cân bằng tải cho các cụm sử dụng nền tảng "none"
Phần này cung cấp ví dụ về cấu hình bộ cân bằng tải cho API và Ingress ứng dụng, đáp ứng các yêu cầu cân bằng tải đối với các cụm sử dụng tùy chọn nền tảng "none". Ví dụ này là tệp cấu hình `/etc/haproxy/haproxy.cfg` dành cho bộ cân bằng tải HAProxy. Ví dụ này không nhằm mục đích đưa ra lời khuyên về việc chọn giải pháp cân bằng tải này thay vì giải pháp khác.

Trong ví dụ này, cùng một bộ cân bằng tải được sử dụng cho cả API Kubernetes và lưu lượng truy cập Ingress ứng dụng. Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và Ingress ứng dụng để có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Nếu bạn sử dụng HAProxy làm bộ cân bằng tải và SELinux đang ở chế độ thực thi (enforcing), bạn phải đảm bảo rằng dịch vụ HAProxy có thể liên kết (bind) với cổng TCP đã cấu hình bằng cách chạy lệnh `setsebool -P haproxy_connect_any=1`.

Ví dụ về cấu hình bộ cân bằng tải `HaProxy` cho API và Ingress ứng dụng

```
global
  log         127.0.0.1 local2
  pidfile     /var/run/haproxy.pid
  maxconn     4000
  daemon
defaults
  mode                    http
  log                     global
  option                  dontlognull
  option http-server-close
  option                  redispatch
  retries                 3
  timeout http-request    10s
  timeout queue           1m
  timeout connect         10s
  timeout client          1m
  timeout server          1m
  timeout http-keep-alive 10s
  timeout check           10s
  maxconn                 3000
listen api-server-6443
  bind *:6443
  mode tcp
  server master0 master0.ocp4.example.com:6443 check inter 1s
  server master1 master1.ocp4.example.com:6443 check inter 1s
  server master2 master2.ocp4.example.com:6443 check inter 1s
listen machine-config-server-22623
  bind *:22623
  mode tcp
  server master0 master0.ocp4.example.com:22623 check inter 1s
  server master1 master1.ocp4.example.com:22623 check inter 1s
  server master2 master2.ocp4.example.com:22623 check inter 1s
listen ingress-router-443
  bind *:443
  mode tcp
  balance source
  server worker0 worker0.ocp4.example.com:443 check inter 1s
  server worker1 worker1.ocp4.example.com:443 check inter 1s
listen ingress-router-80
  bind *:80
  mode tcp
  balance source
  server worker0 worker0.ocp4.example.com:80 check inter 1s
  server worker1 worker1.ocp4.example.com:80 check inter 1s
```

* Cổng `6443` xử lý lưu lượng truy cập Kubernetes API và trỏ đến các máy thuộc mặt phẳng điều khiển (control plane). Bạn phải định cấu hình kiểm tra trạng thái (health check) trên cổng này để đảm bảo rằng máy chủ API khả dụng trước khi định tuyến lưu lượng truy cập.
* Cổng `22623` xử lý lưu lượng truy cập của máy chủ cấu hình máy (machine config server) và trỏ đến các máy thuộc mặt phẳng điều khiển.
* Cổng `443` xử lý lưu lượng truy cập HTTPS và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán (compute machines).
* Cổng `80` xử lý lưu lượng truy cập HTTP và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán.

> **Lưu ý:** Nếu bạn đang triển khai một cụm (cluster) ba nút gọn nhẹ không có nút tính toán (compute node) riêng biệt, các pod của Ingress Controller sẽ chạy trên các nút mặt phẳng điều khiển (control plane node). Đối với các cụm ba nút, bạn phải cấu hình bộ cân bằng tải Ingress cho ứng dụng của mình để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút mặt phẳng điều khiển.

> **Mẹo:** Nếu bạn đang sử dụng HAProxy làm bộ cân bằng tải, bạn có thể kiểm tra xem tiến trình HAProxy có đang lắng nghe trên các cổng 6443, 22623, 443 và 80 hay không bằng cách chạy lệnh `netstat -nltupe` trên nút chạy HAProxy.

### I.7. Giới thiệu về nút trọng tài (arbiter node)
Bạn có thể cấu hình cụm OpenShift Container Platform với hai nút mặt phẳng điều khiển (control plane node) và một nút trọng tài (arbiter node) để duy trì tính sẵn sàng cao (HA), đồng thời giảm chi phí cơ sở hạ tầng cho cụm của mình.

Nút trọng tài là một máy chủ có chi phí thấp hơn, được đặt cùng vị trí và tham gia vào các quyết định thiết lập hạn ngạch (quorum) của mặt phẳng điều khiển. Khác với nút mặt phẳng điều khiển tiêu chuẩn, nút trọng tài không chạy toàn bộ các dịch vụ của mặt phẳng điều khiển. Bạn có thể sử dụng cấu hình này để duy trì tính sẵn sàng cao trong cụm của mình chỉ với hai nút mặt phẳng điều khiển được thiết lập đầy đủ, thay vì ba nút.

Để triển khai một cụm gồm hai nút mặt phẳng điều khiển và một nút trọng tài, bạn phải xác định các nút sau trong tệp `install-config.yaml`:

* 2 nút mặt phẳng điều khiển
* 1 nút trọng tài

Nút trọng tài phải đáp ứng các yêu cầu hệ thống tối thiểu sau:

* 2 vCPU
* 8 GB RAM
* 50 GB ổ cứng SSD hoặc dung lượng lưu trữ tương đương

Nút trọng tài phải được đặt trong môi trường mạng có độ trễ đầu cuối (end-to-end latency) dưới 100 mili giây, bao gồm cả độ trễ I/O của ổ đĩa. Trong các môi trường có độ trễ cao, bạn có thể cần áp dụng cấu hình "etcd slow profile".

Các nút mặt phẳng điều khiển phải đáp ứng các yêu cầu hệ thống tối thiểu sau:

* 4 vCPU
* 16 GB RAM
* 120 GB ổ cứng SSD hoặc dung lượng lưu trữ tương đương

Ngoài ra, các nút mặt phẳng điều khiển cũng phải có đủ dung lượng lưu trữ cho khối lượng công việc (workload).

Ví dụ về cấu hình `install-config.yaml` để triển khai nút trọng tài

```yaml
apiVersion: v1
baseDomain: devcluster.openshift.com
compute:
  - architecture: amd64
    hyperthreading: Enabled
    name: worker
    platform: {}
    replicas: 0
arbiter:
  architecture: amd64
  hyperthreading: Enabled
  replicas: 1
  name: arbiter
  platform:
    baremetal: {}
controlPlane:
  architecture: amd64
  hyperthreading: Enabled
  name: master
  platform:
    baremetal: {}
  replicas: 2
platform:
  baremetal:
    hosts:
      - name: cluster-master-0
        role: master
# ...
      - name: cluster-master-1
        role: master
        ...
      - name: cluster-arbiter-0
        role: arbiter
# ...
```

Trong đó:

* `arbiter`: Xác định nhóm máy chủ đóng vai trò trọng tài (arbiter). Bạn cấu hình trường này để triển khai một cụm (cluster) có chứa một nút trọng tài.
* `arbiter.replicas`: Thiết lập tham số arbiter.replicas bằng 1 cho nhóm máy chủ trọng tài. Bạn không được đặt giá trị cho trường này lớn hơn 1.
* `arbiter.name`: Đặt tên cho nhóm máy chủ trọng tài.
* `controlPlane`: Xác định nhóm máy chủ điều khiển (control plane).
* `controlPlane.replicas`: Thiết lập tham số `controlPlane.replicas`. Khi một nhóm máy chủ trọng tài được xác định, số lượng bản sao (replica) hợp lệ cho nhóm máy chủ điều khiển là 2.

### I.8. Ví dụ: Cấu hình mạng cho các nút sử dụng giao diện Bond và VLAN
Hãy tham khảo các tệp cấu hình mẫu (manifest files) để hiểu rõ hơn về các tùy chọn cấu hình khi triển khai cụm (cluster) của bạn.

Tệp `agent-config.yaml` dưới đây là một ví dụ về tệp cấu hình dành cho các giao diện Bond và VLAN:

```yaml
  apiVersion: v1alpha1
  kind: AgentConfig
  rendezvousIP: 10.10.10.14
  hosts:
    - hostname: master0
      role: master
      interfaces:
       - name: enp0s4
         macAddress: 00:21:50:90:c0:10
       - name: enp0s5
         macAddress: 00:21:50:90:c0:20
      networkConfig:
        interfaces:
          - name: bond0.300
            type: vlan
            state: up
            vlan:
              base-iface: bond0
              id: 300
            ipv4:
              enabled: true
              address:
                - ip: 10.10.10.14
                  prefix-length: 24
              dhcp: false
          - name: bond0
            type: bond
            state: up
            mac-address: 00:21:50:90:c0:10
            ipv4:
              enabled: false
            ipv6:
              enabled: false
            link-aggregation:
              mode: active-backup
              options:
                miimon: "150"
              port:
               - enp0s4
               - enp0s5
        dns-resolver:
          config:
            server:
              - 10.10.10.11
              - 10.10.10.12
        routes:
          config:
            - destination: 0.0.0.0/0
              next-hop-address: 10.10.10.10
              next-hop-interface: bond0.300
              table-id: 254
```

Trong đó:

* `networkConfig.interfaces.name`: Xác định tên của giao diện (interface). Giá trị này không bắt buộc phải trùng với tên thiết bị.
* `networkConfig.interfaces.type`: Xác định loại giao diện. Chỉ định `vlan` sẽ tạo một VLAN, còn chỉ định `bond` sẽ tạo một liên kết gộp (bond).
* `link-aggregation.mode`: Xác định chế độ gộp liên kết (bonding mode).
* `link-aggregation.options.mode`: Xác định tần suất giám sát liên kết MII (tính bằng mili giây). Ví dụ này thực hiện kiểm tra liên kết gộp (bond link) sau mỗi 150 mili giây.
* `dns-resolver`: Xác định các thiết lập tìm kiếm (search) và máy chủ (server) cho máy chủ DNS. Cấu hình này là tùy chọn.
* `routes.config.next-hop-address`: Xác định địa chỉ "next-hop" (chặng kế tiếp) cho lưu lượng truy cập của nút (node). Địa chỉ này phải nằm cùng mạng con (subnet) với địa chỉ IP được thiết lập cho giao diện đã chỉ định.
* `routes.config.next-hop-interface`: Xác định giao diện "next-hop" (chặng kế tiếp) cho lưu lượng truy cập của nút (node).

### I.9. Ví dụ: Cấu hình mạng cho nút (node) sử dụng giao diện mạng kép (dual-NIC) với tính năng gộp cổng (bond) và SR-IOV
Hãy tham khảo các tệp cấu hình mẫu (manifest file) để hiểu rõ hơn về các tùy chọn cấu hình khi triển khai cụm (cluster) của bạn.

Tệp `agent-config.yaml` dưới đây là một ví dụ về tệp cấu hình cho bộ điều khiển giao diện mạng (NIC) hai cổng, bao gồm cả cấu hình gộp cổng (bond) và các giao diện SR-IOV:

```yaml
apiVersion: v1alpha1
kind: AgentConfig
rendezvousIP: 10.10.10.14
hosts:
  - hostname: worker-1
    interfaces:
      - name: eno1
        macAddress: 0c:42:a1:55:f3:06
      - name: eno2
        macAddress: 0c:42:a1:55:f3:07
    networkConfig:
      interfaces:
        - name: eno1
          type: ethernet
          state: up
          mac-address: 0c:42:a1:55:f3:06
          ipv4:
            enabled: true
            dhcp: false
          ethernet:
            sr-iov:
              total-vfs: 2
          ipv6:
            enabled: false
        - name: sriov:eno1:0
          type: ethernet
          state: up
          ipv4:
            enabled: false
          ipv6:
            enabled: false
            dhcp: false
        - name: sriov:eno1:1
          type: ethernet
          state: down
        - name: eno2
          type: ethernet
          state: up
          mac-address: 0c:42:a1:55:f3:07
          ipv4:
            enabled: true
          ethernet:
            sr-iov:
              total-vfs: 2
          ipv6:
            enabled: false
        - name: sriov:eno2:0
          type: ethernet
          state: up
          ipv4:
            enabled: false
          ipv6:
            enabled: false
        - name: sriov:eno2:1
          type: ethernet
          state: down
        - name: bond0
          type: bond
          state: up
          min-tx-rate: 100
          max-tx-rate: 200
          link-aggregation:
            mode: active-backup
            options:
              primary: sriov:eno1:0
            port:
              - sriov:eno1:0
              - sriov:eno2:0
          ipv4:
            address:
              - ip: 10.19.16.57
                prefix-length: 23
            dhcp: false
            enabled: true
          ipv6:
            enabled: false
          dns-resolver:
            config:
              server:
              - 10.11.5.160
              - 10.2.70.215
          routes:
            config:
            - destination: 0.0.0.0/0
              next-hop-address: 10.19.17.254
              next-hop-interface: bond0
              table-id: 254
```

Trong đó:

* `networkConfig`: Chỉ định thông tin về cấu hình mạng của máy chủ (host), bao gồm các trường con như interfaces (giao diện), dns-resolver (bộ phân giải DNS) và routes (các tuyến đường).
* `networkConfig.interfaces`: Chỉ định một mảng các giao diện mạng được định nghĩa cho máy chủ.
* `networkConfig.interfaces.name`: Chỉ định tên của giao diện. Giá trị này không bắt buộc phải trùng với tên thiết bị.
* `networkConfig.interfaces.type`: Chỉ định loại giao diện. Ví dụ này tạo ra một giao diện ethernet.
* `networkConfig.interfaces.ipv4.dhcp`: Chỉ định việc kích hoạt DHCP. Hãy đặt giá trị này là false để vô hiệu hóa DHCP cho chức năng vật lý (Physical Function - PF) nếu không thực sự cần thiết.
* `ethernet.sr-iov.total-vfs`: Chỉ định số lượng các chức năng ảo (Virtual Functions - VF) SR-IOV cần khởi tạo.
* `networkConfig.interfaces.state`: Chỉ định trạng thái của giao diện mạng (networkConfig.interfaces.state). Hãy đặt tham số này là up.
* `networkConfig.interfaces.ipv4.enabled`: Chỉ định việc kích hoạt địa chỉ IPv4 cho VF được gắn vào nhóm liên kết (bond). Hãy đặt giá trị này là false để vô hiệu hóa.
* `networkConfig.interfaces.min-tx-rate`: Chỉ định tốc độ truyền tối thiểu (tính bằng Mbps) cho VF. Giá trị mẫu này thiết lập tốc độ là 100 Mbps. Giá trị này phải nhỏ hơn hoặc bằng tốc độ truyền tối đa. **Lưu ý:** Các card mạng (NIC) của Intel không hỗ trợ tham số min-tx-rate
* `networkConfig.interfaces.max-tx-rate`: Chỉ định tốc độ truyền tối đa (tính bằng Mbps) cho VF. Giá trị mẫu này thiết lập tốc độ là 200 Mbps.
* `link-aggregation.mod`e: Chỉ định chế độ liên kết (bond mode) cần thiết.
* `link-aggregation.options.primary`: Chỉ định cổng ưu tiên của giao diện liên kết (bonding interface). Thiết bị chính (primary device) là thiết bị đầu tiên được sử dụng trong nhóm liên kết và sẽ không bị thay thế trừ khi nó gặp lỗi. Thiết lập này đặc biệt hữu ích khi một card mạng (NIC) trong nhóm liên kết có tốc độ nhanh hơn và do đó có khả năng xử lý tải công việc lớn hơn. Thiết lập này chỉ có hiệu lực khi giao diện liên kết (bonding interface) ở chế độ active-backup (chế độ 1).
* `ipv4.address.ip`: Chỉ định địa chỉ IP tĩnh cho giao diện liên kết (bond interface). Đây chính là địa chỉ IP của nút (node).
* `routes.config.next-hop-interface`: Chỉ định bond0 làm cổng kết nối (gateway) cho tuyến đường mặc định (default route).

### I.10. Tệp install-config.yaml mẫu cho môi trường bare metal
Bạn có thể tùy chỉnh tệp install-config.yaml để chỉ định thêm thông tin chi tiết về nền tảng của cụm OpenShift Container Platform hoặc sửa đổi các giá trị của các tham số bắt buộc.

Tệp install-config.yaml mẫu cho môi trường bare metal

```yaml
apiVersion: v1
baseDomain: example.com
compute:
- name: worker
  replicas: 0
  architecture: amd64
controlPlane:
  name: master
  replicas: 1
  architecture: amd64
metadata:
  name: sno-cluster
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 192.168.0.0/16
  networkType: OVNKubernetes
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
fips: false
pullSecret: '{"auths": ...}'
sshKey: 'ssh-ed25519 AAAA...'
```

Trong đó:

* `baseDomain`: Xác định tên miền cơ sở (base domain) của cụm. Tất cả các bản ghi DNS phải là tên miền phụ (sub-domain) của tên miền cơ sở này và phải bao gồm tên cụm.
* `compute`: Xác định một chuỗi các ánh xạ (mappings). Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `compute` phải bắt đầu bằng một dấu gạch ngang (-).
* `compute.replicas`: Xác định số lượng máy tính toán (compute machines) mà trình cài đặt dựa trên tác nhân cần phát hiện trước khi kích hoạt quy trình cài đặt. Đây là số lượng máy tính toán phải được khởi động bằng tệp ISO đã tạo. **Lưu ý:** Nếu bạn đang cài đặt một cụm gồm ba nút (three-node cluster), đừng triển khai bất kỳ máy tính toán nào khi cài đặt các máy Red Hat Enterprise Linux CoreOS (RHCOS).
* `controlPlane`: Xác định một ánh xạ duy nhất. Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `controlPlane` không được bắt đầu bằng dấu gạch ngang (-). Chỉ một nhóm (pool) control plane được sử dụng.
* `controlPlane.replicas`: Xác định số lượng máy control plane mà bạn thêm vào cụm. Vì cụm sử dụng các giá trị này để xác định số lượng điểm cuối (endpoints) etcd trong cụm, nên giá trị này phải khớp với số lượng máy control plane mà bạn triển khai.
* `metadata.name`: Xác định tên cụm mà bạn đã chỉ định trong các bản ghi DNS của mình.
* `networking.clusterNetwork.cidr`: Xác định một khối địa chỉ IP dùng để cấp phát địa chỉ IP cho các pod. Khối này không được trùng lặp với các mạng vật lý hiện có. Các địa chỉ IP này được sử dụng cho mạng pod. Nếu bạn cần truy cập các pod từ một mạng bên ngoài, bạn phải cấu hình bộ cân bằng tải (load balancers) và bộ định tuyến (routers) để quản lý lưu lượng truy cập. **Lưu ý:** Dải CIDR Lớp E (Class E) được dành riêng cho mục đích sử dụng trong tương lai. Để sử dụng dải CIDR Lớp E, bạn phải đảm bảo rằng môi trường mạng của mình chấp nhận các địa chỉ IP nằm trong dải này.
* `networking.clusterNetwork.hostPrefix`: Xác định độ dài tiền tố mạng con (subnet prefix length) được gán cho từng nút riêng lẻ. Ví dụ: nếu `hostPrefix` được đặt là 23, thì mỗi nút (node) sẽ được gán một mạng con (subnet) /23 từ dải CIDR đã cho, cho phép tạo ra 510 (2^(32 - 23) - 2) địa chỉ IP cho các pod. Nếu bạn cần cho phép truy cập vào các nút từ một mạng bên ngoài, hãy cấu hình bộ cân bằng tải (load balancer) và bộ định tuyến (router) để quản lý lưu lượng truy cập.
* `networking.networkType`: Chỉ định plugin mạng cụm (cluster network plugin) cần cài đặt. Giá trị mặc định `OVNKubernetes` là giá trị duy nhất được hỗ trợ.
* `networking.serviceNetwork`: Chỉ định nhóm địa chỉ IP (IP address pool) được sử dụng cho các địa chỉ IP của dịch vụ (service). Bạn chỉ có thể nhập một nhóm địa chỉ IP duy nhất. Khối địa chỉ này không được trùng lặp với các mạng vật lý hiện có. Nếu bạn cần truy cập các dịch vụ từ một mạng bên ngoài, hãy cấu hình bộ cân bằng tải và bộ định tuyến để quản lý lưu lượng truy cập.
* `platform.none`: Chỉ định nền tảng là `none`. Bạn bắt buộc phải đặt nền tảng là `none` đối với cụm chỉ có một nút (single-node cluster). Đối với các cụm có nhiều nút (multi-node cluster), bạn có thể đặt nền tảng là `vsphere`, `baremetal` hoặc `none`.
* `fips`: Xác định xem có bật hay tắt chế độ FIPS hay không. Theo mặc định, chế độ FIPS không được bật. Nếu chế độ FIPS được bật, các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) – nền tảng mà OpenShift Container Platform vận hành trên đó – sẽ bỏ qua bộ mật mã mặc định của Kubernetes và thay vào đó sử dụng các mô-đun mật mã được cung cấp kèm theo RHCOS. **Quan trọng:** Khi chạy Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã của RHEL – vốn đã được gửi tới NIST để thực hiện quy trình xác thực FIPS 140-2/140-3 – trên các kiến trúc x86_64, ppc64le và s390x.
* `pullSecret`: Xác định một "pull secret" (bí mật kéo ảnh) cho phép bạn xác thực với các dịch vụ được cung cấp bởi các đơn vị liên quan, bao gồm cả Quay.io – nơi lưu trữ các hình ảnh container (container images) cho các thành phần của OpenShift Container Platform.
* `sshKey`: Xác định khóa công khai SSH (SSH public key) dành cho người dùng cốt lõi (core user) trong Red Hat Enterprise Linux CoreOS (RHCOS). **Lưu ý:** Đối với các cụm OpenShift Container Platform trong môi trường sản xuất (production) mà bạn dự định thực hiện các thao tác gỡ lỗi cài đặt hoặc khôi phục sau thảm họa, hãy chỉ định một khóa SSH mà tiến trình `ssh-agent` của bạn đang sử dụng.

> **Lưu ý:** Nếu bạn thiết lập nền tảng là `vSphere` hoặc `bare metal` (máy chủ vật lý), bạn có thể cấu hình các điểm cuối (endpoint) địa chỉ IP cho các nút (node) trong cụm theo ba cách:
> 
> * IPv4
> * IPv6
> * IPv4 và IPv6 song song (dual-stack)
> 
> Ví dụ về mạng dual-stack
> 
> ```yaml
> networking:
>   clusterNetwork:
>     - cidr: 172.21.0.0/16
>       hostPrefix: 23
>     - cidr: fd02::/48
>       hostPrefix: 64
>   machineNetwork:
>     - cidr: 192.168.11.0/16
>     - cidr: 2001:DB8::/32
>   serviceNetwork:
>     - 172.22.0.0/16
>     - fd03::/112
>   networkType: OVNKubernetes
> platform:
>   baremetal:
>     apiVIPs:
>     - 192.168.11.3
>     - 2001:DB8::4
>     ingressVIPs:
>     - 192.168.11.4
>     - 2001:DB8::5
> ```

### I.11. Các bước kiểm tra xác thực trước khi tạo tệp ISO tác nhân (agent ISO)
Trình cài đặt dựa trên tác nhân sẽ thực hiện các bước kiểm tra xác thực đối với các tệp YAML do người dùng định nghĩa trước khi tệp ISO được tạo. Sau khi các bước kiểm tra này hoàn tất thành công, tệp ISO tác nhân sẽ được tạo ra.

* **`install-config.yaml`**
    * Hỗ trợ các nền tảng: bare metal (phần cứng vật lý), vSphere và none (không xác định nền tảng cụ thể). 
    * Đối với nền tảng "none", tham số networkType bắt buộc phải là OVNKubernetes. 
    * Đối với các nền tảng bare metal và vSphere, các tham số apiVIPs và ingressVIPs phải được thiết lập. 
    * Một số trường dành riêng cho máy chủ (host) trong cấu hình nền tảng bare metal — vốn đã có các trường tương đương trong tệp agent-config.yaml — sẽ bị bỏ qua. Một thông báo cảnh báo sẽ được ghi lại nếu các trường này được thiết lập.
* **`agent-config.yaml`**
    * Mỗi giao diện mạng (interface) phải có địa chỉ MAC được xác định. Ngoài ra, tất cả các giao diện mạng phải có địa chỉ MAC khác nhau. 
    * Mỗi máy chủ (host) phải có ít nhất một giao diện mạng được định nghĩa. 
    * Các phần mở rộng nhà cung cấp (vendor extensions) của World Wide Name (WWN) không được hỗ trợ trong các chỉ dẫn về thiết bị gốc (root device hints). 
    * Tham số "role" (vai trò) trong đối tượng máy chủ (host object) phải có giá trị là "master" hoặc "worker".
* Các bước kiểm tra xác thực bổ sung cho cấu hình hai nút với cơ chế cách ly (Two-Node with Fencing - TNF)
    * Khi tham số `controlPlane.replicas` được đặt thành 2, bạn phải cung cấp chính xác 2 thông tin xác thực cho cơ chế cách ly (fencing credentials). 
    * Mỗi thông tin xác thực phải bao gồm: `hostName` (tên máy chủ), `address` (địa chỉ), `username` (tên người dùng) và `password` (mật khẩu). 
    * Trường `address` phải chứa một URL Redfish (nghĩa là chuỗi ký tự phải bao gồm từ khóa "redfish"). Các địa chỉ IPMI sẽ bị từ chối. 
    * Tất cả các giá trị `hostName` phải là duy nhất. 
    * Nếu bạn chỉ định `certificateVerification`, giá trị phải là `Enabled` (đã bật) hoặc `Disabled` (đã tắt). 
    * Thông tin xác thực cho cơ chế cách ly chỉ hợp lệ đối với các nền tảng `baremetal` (phần cứng vật lý), `external` (bên ngoài) hoặc `none` (không có). Các nền tảng khác sẽ dẫn đến lỗi xác thực.

#### I.11.1. Các bước kiểm tra xác thực cho tệp cấu hình ZTP (ZTP manifests)
Các bước kiểm tra xác thực sau đây được thực hiện khi sử dụng các tệp cấu hình ZTP:

* `agent-cluster-install.yaml`
    * Đối với IPv6, giá trị duy nhất được hỗ trợ cho tham số networkType là OVNKubernetes. Giá trị OpenshiftSDN chỉ có thể được sử dụng cho IPv4.
* `cluster-image-set.yaml`
    * Tham số ReleaseImage phải khớp với phiên bản phát hành (release) được xác định trong trình cài đặt (installer).

> **Quan trọng:** Tính năng Cung cấp thiết bị không cần thao tác thủ công (Zero Touch Provisioning - ZTP) không được hỗ trợ cho các cụm gồm hai nút có cơ chế cách ly nút (fencing - TNF). Mặc dù bạn có thể sử dụng Red Hat Advanced Cluster Management (RHACM) để thực hiện cài đặt, nhưng các thành phần cơ sở hạ tầng bổ sung cần thiết cho ZTP chưa được kiểm chứng cho cấu trúc liên kết (topology) này.

## II. Hiểu về việc sao chép (mirroring) image cho quá trình cài đặt trong môi trường không kết nối mạng
Bạn có thể sử dụng một registry trung gian (mirror registry) cho các quá trình cài đặt trong môi trường không kết nối mạng (disconnected installation). Cách làm này cũng đảm bảo rằng các cụm (cluster) của bạn chỉ sử dụng những hình ảnh container (container image) đáp ứng các quy định của tổ chức về nội dung từ bên ngoài.

Trước khi cài đặt một cụm trên cơ sở hạ tầng trong môi trường không kết nối mạng, bạn phải sao chép các hình ảnh container cần thiết vào môi trường đó. Để thực hiện việc này, bạn cần có một registry đóng vai trò là nơi lưu trữ các bản sao (mirror registry).

Bạn có thể sử dụng một trong các quy trình sau để sao chép kho lưu trữ image của OpenShift Container Platform sang registry trung gian của mình:

* "Sao chép image cho quá trình cài đặt trong môi trường không kết nối mạng bằng cách sử dụng plugin oc-mirror v2"
* "Sao chép image cho quá trình cài đặt trong môi trường không kết nối mạng"

### II.1. Về việc sao chép (mirror) kho lưu trữ image OpenShift Container Platform cho registry trong môi trường không kết nối mạng (disconnected registry)

Để sử dụng các image đã được sao chép (mirror images) cho quá trình cài đặt trong môi trường không kết nối mạng thông qua Agent-based Installer, bạn cần sửa đổi tệp `install-config.yaml`.

Bạn có thể sao chép image phát hành (release image) bằng cách sử dụng kết quả đầu ra từ lệnh `oc mirror`.
Quan trọng

Lệnh `oc adm release mirror` đã lỗi thời kể từ phiên bản OpenShift Container Platform 4.22 và sẽ bị loại bỏ trong các phiên bản tương lai.

Thay vào đó, hãy sử dụng plugin `oc-mirror` phiên bản 2 (v2).

Ví dụ dưới đây hiển thị kết quả đầu ra của lệnh `oc adm release mirror`.

```console
oc adm release mirror
```

Ví dụ đầu ra

```
To use the new mirrored repository to install, add the following
section to the install-config.yaml:

imageContentSources:

mirrors:
virthost.ostest.test.metalkube.org:5000/localimages/local-release-image
source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
mirrors:
virthost.ostest.test.metalkube.org:5000/localimages/local-release-image
source: registry.ci.openshift.org/ocp/release
```

Ví dụ sau đây hiển thị một phần của tệp `imageContentSourcePolicy.yaml` được tạo bởi `plugin oc-mirror`. Tệp này nằm trong thư mục kết quả, chẳng hạn như `oc-mirror-workspace/results-1682697932/`.

**Ví dụ tệp `imageContentSourcePolicy.yaml`**

```yaml
spec:
  repositoryDigestMirrors:
  - mirrors:
    - virthost.ostest.test.metalkube.org:5000/openshift/release
    source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
  - mirrors:
    - virthost.ostest.test.metalkube.org:5000/openshift/release-images
    source: quay.io/openshift-release-dev/ocp-release
```

#### II.1.1. Cấu hình Agent-based Installer để sử dụng các image đã được sao chép (mirrored images)
Bạn phải sử dụng kết quả đầu ra từ lệnh `oc adm release mirror` hoặc plugin `oc-mirror` để cấu hình Agent-based Installer sử dụng các image đã được sao chép (mirrored images).

##### Quy trình

1. Nếu bạn đã sử dụng plugin `oc-mirror` để sao chép các image phát hành (release images):
    * a. Mở tệp `imageContentSourcePolicy.yaml` nằm trong thư mục kết quả (ví dụ: `oc-mirror-workspace/results-1682697932/`). 
    * b. Sao chép nội dung trong phần `repositoryDigestMirrors` của tệp YAML đó. 
2. Nếu bạn đã sử dụng lệnh `oc adm release mirror` để sao chép các image phát hành:
    * Sao chép nội dung trong phần `imageContentSources` từ kết quả đầu ra của lệnh này. 
3. Dán nội dung đã sao chép vào trường `imageContentSources` trong tệp `install-config.yaml`. 
4. Thêm tệp chứng chỉ (certificate file) được sử dụng cho registry sao chép (mirror registry) vào trường `additionalTrustBundle` trong tệp YAML. **Quan trọng:** Giá trị này phải là nội dung của tệp chứng chỉ mà bạn đã sử dụng cho registry sao chép của mình. Tệp chứng chỉ này có thể là chứng chỉ từ một cơ quan cấp chứng chỉ (CA) đáng tin cậy hiện có, hoặc là chứng chỉ tự ký (self-signed certificate) mà bạn đã tạo cho registry sao chép đó.

Ví dụ tệp install-config.yaml

```yaml
  additionalTrustBundle: |
    -----BEGIN CERTIFICATE-----
    ZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZ
    -----END CERTIFICATE-----
```

5. Nếu bạn đang sử dụng các tệp manifest GitOps ZTP: hãy thêm các tệp `registries.conf` và `ca-bundle.crt` vào đường dẫn mirror để bổ sung cấu hình mirror vào tệp ISO của agent. **Lưu ý:** Bạn có thể tạo tệp `registries.conf` từ kết quả đầu ra của lệnh `oc adm release mirror` hoặc từ plugin `oc mirror`. Định dạng của tệp `/etc/containers/registries.conf` đã thay đổi; hiện tại tệp này sử dụng phiên bản 2 và có định dạng TOML.

Ví dụ về tệp `registries.conf`

```conf
[[registry]]
location = "registry.ci.openshift.org/ocp/release" mirror-by-digest-only = true

[[registry.mirror]] location = "virthost.ostest.test.metalkube.org:5000/localimages/local-release-image"

[[registry]]
location = "quay.io/openshift-release-dev/ocp-v4.0-art-dev" mirror-by-digest-only = true

[[registry.mirror]] location = "virthost.ostest.test.metalkube.org:5000/localimages/local-release-image"
```

## III. Cài đặt cụm (cluster)
Bạn có thể cài đặt một cụm OpenShift Container Platform cơ bản bằng cách sử dụng trình cài đặt dựa trên tác nhân.

Các quy trình dưới đây hướng dẫn triển khai một cụm OpenShift Container Platform gồm một nút duy nhất (single-node) trong môi trường không kết nối mạng (disconnected environment). Bạn có thể sử dụng các quy trình này làm nền tảng và điều chỉnh tùy theo yêu cầu cụ thể của mình.

Để biết các quy trình bao gồm những tùy chỉnh tùy chọn mà bạn có thể thực hiện khi sử dụng Agent-based Installer, hãy xem phần "Cài đặt cụm với các tùy chỉnh".

### III.1. Các điều kiện tiên quyết để cài đặt cụm bằng trình cài đặt dựa trên tác nhân
Trước khi bắt đầu cài đặt cụm, bạn phải hoàn tất các tác vụ tiên quyết để chuẩn bị môi trường của mình. 

* Bạn đã xem xét các thông tin chi tiết về quy trình cài đặt và cập nhật OpenShift Container Platform. Để biết thêm thông tin, hãy xem phần "Cài đặt và cập nhật". 
* Bạn đã đọc nội dung "Chọn phương pháp cài đặt cụm và chuẩn bị cho người dùng". 
* Nếu sử dụng tường lửa hoặc proxy, bạn đã định cấu hình để cho phép truy cập vào các trang web mà cụm của bạn cần kết nối. Để biết thêm thông tin, hãy xem phần "Định cấu hình tường lửa". 
* Bạn đã định cấu hình tường lửa để cho phép lưu lượng TCP trên cổng `8090` từ tất cả các máy chủ (host) đến máy chủ điều phối (rendezvous host), nhờ đó các máy chủ có thể kết nối với Assisted Service API trong quá trình khám phá (discovery) và khởi động (bootstrap). Để biết thêm thông tin, hãy xem phần "Các yêu cầu về cổng cho máy chủ điều phối".

### III.2. Tải xuống trình cài đặt dựa trên tác nhân
Bắt đầu quy trình cài đặt bằng cách tải xuống trình cài đặt dựa trên tác nhân và giao diện dòng lệnh (CLI) cần thiết cho quá trình cài đặt của bạn.

**Quy trình**

1. Đăng nhập vào Red Hat Hybrid Cloud Console bằng thông tin đăng nhập của bạn. 
2. Điều hướng đến mục Datacenter. 
3. Nhấp vào Run Agent-based Installer locally (Chạy trình cài đặt dựa trên tác nhân cục bộ). 
4. Chọn hệ điều hành và kiến trúc phù hợp cho trình cài đặt OpenShift (OpenShift Installer) và giao diện dòng lệnh (CLI). 
5. Nhấp vào Download Installer (Tải xuống trình cài đặt) để tải xuống và giải nén chương trình cài đặt. 
6. Tải xuống hoặc sao chép "pull secret" (bí mật kéo ảnh container) bằng cách nhấp vào Download pull secret hoặc Copy pull secret. 
7. Nhấp vào Download command-line tools (Tải xuống các công cụ dòng lệnh) và đặt tệp nhị phân `openshift-install` vào một thư mục nằm trong biến môi trường PATH của bạn.

### III.3. Tạo các dữ liệu đầu vào cho cấu hình
Tạo các tệp cấu hình được chương trình cài đặt sử dụng để tạo ảnh (image) của tác nhân (agent).

**Quy trình**

1. Đặt tệp nhị phân `openshift-install` vào một thư mục nằm trong biến môi trường PATH của bạn. 
2. Tạo một thư mục để lưu trữ cấu hình cài đặt bằng cách chạy lệnh sau:

```console
mkdir ~/<directory_name>
```

3. Tạo tệp `install-config.yaml` bằng cách chạy lệnh sau với nội dung:
```console
vim ./my-cluster/install-config.yaml
```

```yaml
apiVersion: v1
baseDomain: test.example.com
compute:
- architecture: amd64
  hyperthreading: Enabled
  name: worker
  replicas: 0
controlPlane:
  architecture: amd64
  hyperthreading: Enabled
  name: master
  replicas: 1
metadata:
  name: sno-cluster
networking:
  clusterNetwork:
  - cidr: fd01::/48
    hostPrefix: 64
  machineNetwork:
  - cidr: fd2e:6f44:5dd8:c956::/120
  networkType: OVNKubernetes
  serviceNetwork:
  - fd02::/112
platform:
  none: {}
pullSecret: '<pull_secret>'
sshKey: '<ssh_pub_key>'
additionalTrustBundle: |
  -----BEGIN CERTIFICATE-----
  ZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZZ
  -----END CERTIFICATE-----
imageContentSources:
- mirrors:
  - <local_registry>/<local_repository_name>/release
  source: quay.io/openshift-release-dev/ocp-release
- mirrors:
  - <local_registry>/<local_repository_name>/release
  source: quay.io/openshift-release-dev/ocp-v4.0-art-dev
```

**Trong đó:**

* `compute.architecture`: Xác định kiến trúc hệ thống. Các giá trị hợp lệ bao gồm amd64, arm64, ppc64le và s390x.  
Nếu sử dụng image phát hành (release image) có chứa nhiều payload (multi-payload), bạn có thể cài đặt cụm (cluster) trên các kiến trúc khác nhau như arm64, amd64, s390x và ppc64le. Nếu không, bạn chỉ có thể cài đặt cụm trên kiến trúc được hiển thị trong kết quả của lệnh `openshift-install version`. Để biết thêm thông tin, hãy xem phần "Xác minh kiến trúc được hỗ trợ để cài đặt cụm bằng Agent-based Installer".
* `metadata.name`: Xác định tên cụm của bạn. Đây là giá trị bắt buộc.
* networking.networkingType: Xác định plugin mạng (network plugin) cho cụm cần cài đặt. Giá trị mặc định là `OVNKubernetes` – đây cũng là giá trị duy nhất được hỗ trợ.
* `platform`: Xác định nền tảng (platform) của bạn. **Lưu ý:** Đối với các nền tảng bare metal (phần cứng vật lý), các thiết lập máy chủ (host settings) được khai báo trong phần `platform` của tệp `install-config.yaml` sẽ được sử dụng theo mặc định, trừ khi chúng bị ghi đè bởi các cấu hình trong tệp `agent-config.yaml`.
* `pullSecret`: Xác định pull secret (bí mật dùng để tải image) của bạn.
* `sshKey`: Xác định khóa công khai (public key) SSH của bạn.
* `additionalTrustBundle`: Xác định nội dung của tệp chứng chỉ (certificate file) mà bạn đã sử dụng cho mirror registry (kho lưu trữ bản sao) của mình. Tệp chứng chỉ này có thể là chứng chỉ từ một cơ quan cấp chứng chỉ (CA) đáng tin cậy hoặc là chứng chỉ tự ký (self-signed certificate) mà bạn đã tạo cho mirror registry đó. Bạn bắt buộc phải chỉ định tham số này nếu đang sử dụng mirror registry trong môi trường không kết nối internet (disconnected environment).
* `imageContentSources`: Xác định phần `imageContentSources` dựa trên kết quả đầu ra của lệnh mà bạn đã sử dụng để sao chép (mirror) kho lưu trữ (repository). Bạn bắt buộc phải chỉ định tham số này nếu đang sử dụng mirror registry trong môi trường không kết nối internet.

**Quan trọng**

* Khi sử dụng lệnh `oc adm release mirror`, hãy dùng dữ liệu đầu ra từ phần `imageContentSources`. 
* Khi sử dụng lệnh `oc mirror`, hãy dùng phần `repositoryDigestMirrors` trong tệp `ImageContentSourcePolicy` được tạo ra sau khi chạy lệnh đó. 
* Tài nguyên `ImageContentSourcePolicy` hiện đã lỗi thời (deprecated).

4. Tạo tệp `agent-config.yaml` bằng cách chạy lệnh sau:

```console
cat > agent-config.yaml << EOF
apiVersion: v1beta1
kind: AgentConfig
metadata:
  name: sno-cluster
rendezvousIP: fd2e:6f44:5dd8:c956::50
EOF
```

Trong đó:

* `rendezvousIP`: Xác định địa chỉ IP được sử dụng để quyết định nút nào sẽ thực hiện quy trình khởi động (bootstrapping) cũng như chạy thành phần dịch vụ hỗ trợ (assisted-service). Bạn bắt buộc phải cung cấp địa chỉ IP rendezvous nếu không chỉ định ít nhất một địa chỉ IP máy chủ (host IP) trong tham số networkConfig. Nếu địa chỉ này không được cung cấp, một địa chỉ IP sẽ được chọn từ danh sách các địa chỉ IP máy chủ đã được khai báo trong tham số networkConfig.

### III.4. Tạo và khởi động ảnh agent (agent image)
Sau khi đã chuẩn bị các thông tin cấu hình đầu vào cho quá trình cài đặt, hãy tạo ảnh ISO và khởi động ảnh đó trên các máy của bạn.

**Điều kiện tiên quyết**

Nếu bạn dự định khởi động ảnh agent từ ổ đĩa USB, hãy đảm bảo rằng bạn đã cài đặt gói phần mềm syslinux.

**Quy trình**

1. Tạo ảnh agent bằng cách chạy lệnh sau:

```console
openshift-install --dir <install_directory> agent create image
```

> **Lưu ý:** Red Hat Enterprise Linux CoreOS (RHCOS) hỗ trợ cơ chế đa đường dẫn (multipathing) trên đĩa chính, giúp tăng cường khả năng chống chịu trước các lỗi phần cứng, từ đó đạt được độ sẵn sàng cao hơn cho máy chủ. Cơ chế đa đường dẫn được bật theo mặc định trong tệp ảnh ISO của tác nhân (agent ISO image), với cấu hình mặc định nằm tại /etc/multipath.conf.

2. Nếu bạn dự định khởi động tệp ảnh ISO từ ổ đĩa USB, hãy thêm bản ghi khởi động chính (master boot record) vào tệp ảnh đó bằng cách chạy lệnh sau:

```console
isohybrid --uefi <agent_iso_image>
```

Ví dụ về lệnh

```console
isohybrid --uefi agent.x86_64.iso
```

3. Khởi động tệp ảnh agent.x86_64.iso, agent.aarch64.iso hoặc agent.s390x.iso trên các máy vật lý (bare-metal).

### III.5. Xác minh khả năng tải xuống các ảnh phát hành (release images) của máy chủ cài đặt
Sau khi bạn khởi động ảnh tác nhân (agent image) và các dịch vụ mạng đã sẵn sàng trên máy chủ, ứng dụng bảng điều khiển tác nhân (agent console application) sẽ thực hiện kiểm tra việc tải xuống để xác minh rằng máy chủ hiện tại có thể truy xuất các ảnh phát hành hay không.

Nếu quá trình kiểm tra tải xuống chính thành công, bạn có thể thoát khỏi ứng dụng để tiếp tục quá trình cài đặt. Nếu quá trình kiểm tra này thất bại, ứng dụng sẽ thực hiện các bước kiểm tra bổ sung — hiển thị trong phần "Các bước kiểm tra bổ sung" (Additional checks) trên giao diện người dùng dạng văn bản (TUI) — nhằm hỗ trợ bạn khắc phục sự cố. Việc thất bại ở bất kỳ bước kiểm tra bổ sung nào không nhất thiết là vấn đề nghiêm trọng, miễn là quá trình kiểm tra tải xuống chính vẫn thành công.

Nếu có các vấn đề về cấu hình mạng trên máy chủ có thể dẫn đến lỗi cài đặt, bạn có thể sử dụng ứng dụng bảng điều khiển để điều chỉnh cấu hình mạng của mình.

> **Lưu ý quan trọng:** Nếu ứng dụng bảng điều khiển tác nhân phát hiện các vấn đề về cấu hình mạng trên máy chủ, quy trình cài đặt sẽ tạm dừng cho đến khi người dùng thực hiện thao tác dừng ứng dụng bảng điều khiển theo cách thủ công và xác nhận ý muốn tiếp tục quy trình.

**Quy trình**

1. Đợi ứng dụng bảng điều khiển tác nhân (agent console application) kiểm tra xem hình ảnh phát hành (release image) đã cấu hình có thể được tải về từ kho lưu trữ (registry) hay không. 
2. Nếu ứng dụng bảng điều khiển tác nhân thông báo rằng các bước kiểm tra kết nối trình cài đặt đã hoàn tất thành công, hãy đợi cho đến khi thông báo nhắc nhở tự động hết thời gian chờ (time out) để tiếp tục quá trình cài đặt.

> **Lưu ý:** Bạn vẫn có thể chọn xem hoặc thay đổi các thiết lập cấu hình mạng ngay cả khi các bước kiểm tra kết nối đã hoàn tất thành công. Tuy nhiên, nếu bạn chọn thao tác trên ứng dụng giao diện người dùng dạng văn bản (TUI) của tác nhân (agent) thay vì để ứng dụng tự động hết thời gian chờ (time out), bạn sẽ cần phải thoát thủ công khỏi TUI này để tiếp tục quá trình cài đặt.

3. Nếu các bước kiểm tra ứng dụng bảng điều khiển tác nhân (agent console application) không đạt yêu cầu – được biểu thị bằng biểu tượng màu đỏ bên cạnh mục kiểm tra lấy URL hình ảnh phát hành (Release image URL pull check) – hãy thực hiện các bước sau để định cấu hình lại cài đặt mạng của máy chủ:
    * Hãy xem phần `Check Errors` trong giao diện văn bản (TUI). Phần này hiển thị các thông báo lỗi cụ thể cho những mục kiểm tra không đạt.
    * Chọn **Configure network** để khởi chạy giao diện TUI của NetworkManager.
    * Chọn **Edit a connection** rồi chọn kết nối mà bạn muốn cấu hình lại.
    * Thay đổi cấu hình và chọn **OK** để lưu các thay đổi.
    * Chọn **Back** để trở về màn hình chính của giao diện TUI NetworkManager.
    * Chọn **Activate a Connection**.
    * Chọn mạng vừa cấu hình lại để hủy kích hoạt mạng đó.
    * Chọn lại mạng vừa cấu hình lại để kích hoạt lại mạng đó.
    * Chọn **Back**, sau đó chọn **Quit** để trở về ứng dụng bảng điều khiển tác nhân (agent console).
    * Đợi ít nhất năm giây để các quy trình kiểm tra mạng liên tục khởi động lại với cấu hình mạng mới.
    * Nếu bước kiểm tra tải URL hình ảnh phát hành (`Release image URL` pull check) thành công và hiển thị biểu tượng màu xanh lá cây bên cạnh URL, hãy chọn **Quit** để rời khỏi ứng dụng bảng điều khiển tác nhân và tiếp tục quá trình cài đặt.

![Hình 3.1](img/agent-tui-home.png)

### III.6. Theo dõi và xác minh tiến trình cài đặt
Sau khi quá trình cài đặt bắt đầu, bạn có thể theo dõi tiến trình cài đặt và xác minh xem quá trình cài đặt đã hoàn tất thành công hay chưa.

**Điều kiện tiên quyết**

Bạn đã định cấu hình bản ghi DNS cho máy chủ API Kubernetes.

**Quy trình**

1. Tùy chọn: Để biết khi nào máy chủ khởi động (máy chủ rendezvous) khởi động lại, hãy chạy lệnh sau:

```console
./openshift-install --dir <install_directory> agent wait-for bootstrap-complete \
    --log-level=info
```

Trong đó:

* `--dir`: chỉ định đường dẫn đến thư mục nơi tệp ISO của tác nhân (agent) được tạo ra.
* `--log-level`: chỉ định mức độ chi tiết của thông tin trong quá trình cài đặt. Các giá trị hợp lệ bao gồm: info, warn, debug và error.

Ví dụ đầu ra

```
...................................................................
...................................................................
INFO Bootstrap configMap status is complete
INFO cluster bootstrap is complete
```

Lệnh này hoàn tất thành công khi Kubernetes API server báo hiệu rằng nó đã được khởi tạo trên các máy thuộc control plane.

2. Theo dõi tiến trình và xác minh quá trình cài đặt đã hoàn tất thành công bằng cách chạy lệnh sau:

```console
openshift-install --dir <install_directory> agent wait-for install-complete
```

Hãy thay thế `<install_directory>` bằng đường dẫn đến thư mục nơi tệp ISO của tác nhân (agent) được tạo ra.

**Ví dụ đầu ra**

```
...................................................................
...................................................................
INFO Cluster is installed
INFO Install complete!
INFO To access the cluster as the system:admin user when using 'oc', run
INFO     export KUBECONFIG=/home/core/installer/auth/kubeconfig
INFO Access the OpenShift web-console here: https://console-openshift-console.apps.sno-cluster.test.example.com
```

### III.7. Thu thập dữ liệu nhật ký (log) từ quá trình cài đặt dựa trên Agent bị lỗi
Nếu bạn gặp lỗi trong quá trình cài đặt dựa trên Agent, bạn có thể thu thập dữ liệu nhật ký để cung cấp cho yêu cầu hỗ trợ (support case).

**Điều kiện tiên quyết**

Bạn đã định cấu hình bản ghi DNS cho máy chủ API Kubernetes.

**Quy trình**

1. Chạy lệnh sau và thu thập kết quả đầu ra:

```console
./openshift-install --dir <installation_directory> agent wait-for bootstrap-complete --log-level=debug
```

Thông báo lỗi ví dụ

```
...
ERROR Bootstrap failed to complete: : bootstrap process timed out: context deadline exceeded
```

2. Nếu kết quả từ lệnh trước đó cho thấy lỗi, hoặc nếu quá trình khởi động (bootstrap) không tiến triển, hãy chạy lệnh sau để kết nối với máy chủ điều phối (rendezvous host) và thu thập kết quả đầu ra:

```console
ssh core@<node-ip> agent-gather -O >agent-gather.tar.xz
```

> **Lưu ý:** Bộ phận Hỗ trợ của Red Hat có thể chẩn đoán hầu hết các vấn đề bằng cách sử dụng dữ liệu thu thập được từ máy chủ kết nối (rendezvous host); tuy nhiên, nếu một số máy chủ không thể đăng ký, thì việc thu thập dữ liệu này từ tất cả các máy chủ có thể sẽ hữu ích.

3. Nếu quá trình khởi động ban đầu (bootstrap) hoàn tất và các nút trong cụm (cluster nodes) khởi động lại, hãy chạy lệnh sau và thu thập kết quả đầu ra:

```console
./openshift-install --dir <install_directory> agent wait-for install-complete --log-level=debug
```

4. Nếu kết quả từ lệnh trước đó cho thấy lỗi, hãy thực hiện các bước sau:
    * Xuất tệp `kubeconfig` vào môi trường của bạn bằng cách chạy lệnh sau:

    ```console
    export KUBECONFIG=<install_directory>/auth/kubeconfig
    ```

    * Thu thập thông tin phục vụ việc gỡ lỗi bằng cách chạy lệnh sau:

    ```console
    oc adm must-gather
    ```

    * Hãy tạo một tệp nén từ thư mục `must-gather` vừa được tạo trong thư mục làm việc của bạn bằng cách chạy lệnh sau:

    ```console
    tar cvaf must-gather.tar.gz <must_gather_directory>
    ```

5. Hãy đính kèm thư mục cài đặt được sử dụng trong quá trình triển khai (ngoại trừ thư mục con /auth) vào yêu cầu hỗ trợ của bạn trên [Cổng thông tin khách hàng Red Hat](https://access.redhat.com/).
6. Đồng thời, hãy đính kèm tất cả dữ liệu khác thu thập được từ quy trình này vào yêu cầu hỗ trợ đó.

## 4. Cài đặt cụm với các tùy chỉnh
Bạn có thể cài đặt cụm OpenShift Container Platform bằng trình cài đặt dựa trên tác nhân (Agent-based Installer), với các tùy chỉnh để đáp ứng nhu cầu triển khai của mình.

Các quy trình dưới đây mô tả cách triển khai một cụm OpenShift Container Platform gồm một nút duy nhất trong môi trường không kết nối mạng (disconnected environment). Bạn có thể sử dụng các quy trình này làm nền tảng và điều chỉnh tùy theo yêu cầu cụ thể của mình.

### 4.1. Các điều kiện tiên quyết để cài đặt cụm bằng trình cài đặt dựa trên tác nhân (Agent-based Installer)
Trước khi bắt đầu cài đặt cụm, bạn phải hoàn tất các tác vụ tiên quyết để chuẩn bị môi trường của mình. 

* Bạn đã xem xét các thông tin chi tiết về quy trình cài đặt và cập nhật OpenShift Container Platform. Để biết thêm thông tin, hãy xem phần "Cài đặt và cập nhật". 
* Bạn đã đọc nội dung "Chọn phương pháp cài đặt cụm và chuẩn bị cho người dùng". 
* Nếu sử dụng tường lửa hoặc proxy, bạn đã định cấu hình để cho phép truy cập vào các trang web mà cụm của bạn cần kết nối. Để biết thêm thông tin, hãy xem phần "Định cấu hình tường lửa". 
* Bạn đã định cấu hình tường lửa để cho phép lưu lượng truy cập TCP trên cổng 8090 từ tất cả các máy chủ (host) đến máy chủ điều phối (rendezvous host), nhờ đó các máy chủ có thể kết nối với Assisted Service API trong quá trình khám phá (discovery) và khởi động (bootstrap). Để biết thêm thông tin, hãy xem phần "Yêu cầu về cổng cho máy chủ điều phối".

### 4.2. Tải xuống Trình cài đặt dựa trên tác nhân (Agent-based Installer)
Bắt đầu quy trình cài đặt bằng cách tải xuống Trình cài đặt dựa trên tác nhân (Agent-based Installer) và giao diện dòng lệnh (CLI) cần thiết cho quá trình cài đặt của bạn.

**Quy trình**

1. Đăng nhập vào Red Hat Hybrid Cloud Console bằng thông tin đăng nhập của bạn. 
2. Điều hướng đến mục [Datacenter](https://console.redhat.com/openshift/create/datacenter). 
3. Nhấp vào Run Agent-based Installer locally (Chạy Trình cài đặt dựa trên tác nhân cục bộ). 
4. Chọn hệ điều hành và kiến trúc phù hợp cho Trình cài đặt OpenShift (OpenShift Installer) và giao diện dòng lệnh (CLI). 
5. Nhấp vào Download Installer (Tải xuống trình cài đặt) để tải xuống và giải nén chương trình cài đặt. 
6. Tải xuống hoặc sao chép "pull secret" (bí mật kéo ảnh container) bằng cách nhấp vào Download pull secret hoặc Copy pull secret. 
7. Nhấp vào Download command-line tools (Tải xuống các công cụ dòng lệnh) và đặt tệp nhị phân `openshift-install` vào một thư mục nằm trong biến môi trường PATH của bạn.

### 4.3. Xác minh kiến trúc được hỗ trợ cho quá trình cài đặt dựa trên Agent (Agent-based installation)

Trước khi cài đặt cụm OpenShift Container Platform bằng trình cài đặt dựa trên Agent (Agent-based Installer), bạn có thể tùy chọn xác minh các kiến trúc được hỗ trợ để triển khai cụm.

**Điều kiện tiên quyết**

* Bạn đã cài đặt giao diện dòng lệnh OpenShift (OpenShift CLI - `oc`). 
* Bạn đã tải xuống chương trình cài đặt.

**Quy trình**

1. Đăng nhập vào giao diện dòng lệnh OpenShift (OpenShift CLI - `oc`). 
2. Kiểm tra gói phát hành (release payload) bằng cách chạy lệnh sau:

```console
./openshift-install version
```

Ví dụ đầu ra

```console
./openshift-install 4.22.0
built from commit abc123def456
release image quay.io/openshift-release-dev/ocp-release@sha256:123abc456def789ghi012jkl345mno678pqr901stu234vwx567yz0
release architecture amd64
```

Nếu bạn đang sử dụng ảnh phát hành (release image) với gói dữ liệu `multi`, thì `release architecture` hiển thị trong kết quả của lệnh này chính là kiến trúc mặc định.

3. Để kiểm tra kiến trúc của payload, hãy chạy lệnh sau:

```console
oc adm release info <release_image> -o jsonpath="{ .metadata.metadata}"
```

Thay thế `<release_image>` bằng ảnh phát hành. Ví dụ: quay.io/openshift-release-dev/ocp-release@sha256:123abc456def789ghi012jkl345mno678pqr901stu234vwx567yz0.

Ví dụ đầu ra khi hình ảnh phát hành sử dụng cơ chế đa tải trọng (multi-payload)

```json
{"release.openshift.io architecture":"multi"}
```

Nếu bạn đang sử dụng image phát hành (release image) với payload `multi`, bạn có thể cài đặt cụm (cluster) trên các kiến trúc khác nhau như arm64, amd64, s390x và ppc64le. Ngược lại, bạn chỉ có thể cài đặt cụm trên `release architecture` được hiển thị trong kết quả của lệnh `openshift-install version`.

## 4. Cài đặt cụm với các tùy chỉnh
Bạn có thể cài đặt cụm OpenShift Container Platform bằng trình cài đặt dựa trên tác nhân (Agent-based Installer), với các tùy chỉnh phù hợp với nhu cầu triển khai của mình.

Các quy trình dưới đây hướng dẫn triển khai một cụm OpenShift Container Platform gồm một nút duy nhất trong môi trường không kết nối mạng (disconnected environment). Bạn có thể sử dụng các quy trình này làm nền tảng và điều chỉnh tùy theo yêu cầu cụ thể của mình.

### 4.1. Các điều kiện tiên quyết để cài đặt cụm bằng trình cài đặt dựa trên tác nhân (Agent-based Installer)
Trước khi bắt đầu cài đặt cụm, bạn phải hoàn tất các tác vụ tiên quyết để chuẩn bị môi trường của mình. 

Bạn đã xem xét các thông tin chi tiết về quy trình cài đặt và cập nhật OpenShift Container Platform. Để biết thêm thông tin, hãy xem phần "Cài đặt và cập nhật". 
Bạn đã đọc nội dung "Chọn phương pháp cài đặt cụm và chuẩn bị cho người dùng". 
Nếu sử dụng tường lửa hoặc proxy, bạn đã định cấu hình để cho phép truy cập vào các trang web mà cụm của bạn cần kết nối. Để biết thêm thông tin, hãy xem phần "Định cấu hình tường lửa". 
Bạn đã định cấu hình tường lửa để cho phép lưu lượng truy cập TCP trên cổng 8090 từ tất cả các máy chủ (host) đến máy chủ điều phối (rendezvous host), nhờ đó các máy chủ có thể kết nối với Assisted Service API trong quá trình khám phá (discovery) và khởi động (bootstrap). Để biết thêm thông tin, hãy xem phần "Yêu cầu về cổng cho máy chủ điều phối".

### 4.2. Tải xuống Trình cài đặt dựa trên tác nhân (Agent-based Installer)
Bắt đầu quy trình cài đặt bằng cách tải xuống Trình cài đặt dựa trên tác nhân (Agent-based Installer) và giao diện dòng lệnh (CLI) cần thiết cho quá trình cài đặt của bạn.

**Quy trình**

1. Đăng nhập vào Red Hat Hybrid Cloud Console bằng thông tin đăng nhập của bạn. 
2. Điều hướng đến mục Datacenter. 
3. Nhấp vào Run Agent-based Installer locally (Chạy Trình cài đặt dựa trên tác nhân cục bộ). 
4. Chọn hệ điều hành và kiến trúc phù hợp cho Trình cài đặt OpenShift (OpenShift Installer) và giao diện dòng lệnh (CLI). 
5. Nhấp vào Download Installer (Tải xuống trình cài đặt) để tải xuống và giải nén chương trình cài đặt. 
6. Tải xuống hoặc sao chép "pull secret" (bí mật kéo ảnh container) bằng cách nhấp vào Download pull secret hoặc Copy pull secret. 
7. Nhấp vào Download command-line tools (Tải xuống các công cụ dòng lệnh) và đặt tệp nhị phân `openshift-install` vào một thư mục nằm trong biến môi trường PATH của bạn.

### 4.3. Xác minh kiến trúc được hỗ trợ cho quá trình cài đặt dựa trên Agent (Agent-based installation)

Trước khi cài đặt cụm OpenShift Container Platform bằng trình cài đặt dựa trên Agent (Agent-based Installer), bạn có thể tùy chọn xác minh các kiến trúc được hỗ trợ để triển khai cụm.

Điều kiện tiên quyết

Bạn đã cài đặt giao diện dòng lệnh OpenShift (OpenShift CLI - `oc`). 
Bạn đã tải xuống chương trình cài đặt.

Quy trình

Đăng nhập vào giao diện dòng lệnh OpenShift (OpenShift CLI - `oc`). 

Kiểm tra gói phát hành (release payload) bằng cách chạy lệnh sau:
