Cài đặt OpenShift - Tổng quan về cài đặt
----------------------------------------

# III. Các năng lực của cụm
Với tư cách là quản trị viên cụm, bạn có thể sử dụng các tính năng của cụm để bật hoặc tắt các thành phần tùy chọn trước khi cài đặt. Ngoài ra, bạn cũng có thể bật các tính năng của cụm vào bất kỳ thời điểm nào sau khi cài đặt.

> **Lưu ý:** Bạn không thể tắt một tính năng cụm sau khi tính năng đó đã được bật.

## III.1. Bật các tính năng cụm (cluster capabilities)
Nếu bạn sử dụng phương pháp cài đặt cho phép tùy chỉnh cụm thông qua việc tạo tệp `install-config.yaml`, bạn có thể chọn các tính năng cụm mà bạn muốn kích hoạt trên cụm đó.

> **Lưu ý:** Nếu bạn tùy chỉnh cụm bằng cách bật hoặc tắt các tính năng cụm cụ thể, bạn phải tự duy trì tệp `install-config.yaml` của mình. Các bản cập nhật mới của OpenShift Container Platform có thể bổ sung các định danh tính năng (capability handles) mới cho các thành phần hiện có, hoặc giới thiệu hoàn toàn các thành phần mới. Người dùng tùy chỉnh tệp `install-config.yaml` nên cân nhắc cập nhật tệp này định kỳ theo các bản cập nhật của OpenShift Container Platform.

Bạn có thể sử dụng các tham số cấu hình sau đây để chọn các tính năng cụm:

```yaml
capabilities:
  baselineCapabilitySet: v4.11
  additionalEnabledCapabilities:
  - CSISnapshot
  - Console
  - Storage
```

**capabilities.baselineCapabilitySet**  
Chỉ định tập hợp các khả năng (capabilities) cơ sở cần cài đặt. Các giá trị hợp lệ bao gồm None, vCurrent và v4.x. Nếu chọn None, tất cả các khả năng tùy chọn sẽ bị vô hiệu hóa. Giá trị mặc định là vCurrent, cho phép kích hoạt tất cả các khả năng tùy chọn.

> **Lưu ý:** v4.x đề cập đến bất kỳ phiên bản nào từ phiên bản đó trở xuống, bao gồm cả phiên bản cụm (cluster) hiện tại. Ví dụ, đối với cụm OpenShift Container Platform 4.12, các giá trị hợp lệ là v4.11 và v4.12.

**capabilities.additionalEnabledCapabilities**  
Chỉ định danh sách các khả năng cần được kích hoạt một cách rõ ràng. Những khả năng này được kích hoạt bổ sung bên cạnh các khả năng đã được xác định trong baselineCapabilitySet.

> **Lưu ý:** Trong ví dụ này, khả năng mặc định được đặt là v4.11. Trường additionalEnabledCapabilities cho phép kích hoạt thêm các khả năng khác ngoài tập hợp khả năng mặc định v4.11.

Bảng dưới đây mô tả các giá trị của baselineCapabilitySet.

**Bảng 3.1. Mô tả các giá trị trong tập hợp khả năng cơ sở (baselineCapabilitySet) của cụm**

| Giá trị  | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| vCurrent | Hãy chọn tùy chọn này khi bạn muốn tự động thêm các tính năng mặc định mới được giới thiệu trong các phiên bản mới.                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| v4.11    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.11. Khi chỉ định v4.11, các tính năng được giới thiệu trong những phiên bản OpenShift Container Platform mới hơn sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.11 bao gồm: baremetal, MachineAPI, marketplace và openshift-samples.                                                                                                                                                                                             |
| v4.12    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.12. Khi chỉ định v4.12, các tính năng được giới thiệu trong những phiên bản OpenShift Container Platform mới hơn sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.12 bao gồm: baremetal, MachineAPI, marketplace, openshift-samples, Console, Insights, Storage và CSISnapshot.                                                                                                                                                    |
| v4.13    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.13. Khi chỉ định phiên bản v4.13, các tính năng được giới thiệu trong những phiên bản OpenShift Container Platform mới hơn sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.13 bao gồm: baremetal, MachineAPI, marketplace, openshift-samples, Console, Insights, Storage, CSISnapshot và NodeTuning.                                                                                                                              |
| v4.14    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.14. Khi chỉ định phiên bản v4.14, các tính năng được giới thiệu trong những phiên bản mới hơn của OpenShift Container Platform sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.14 bao gồm: baremetal, MachineAPI, marketplace, openshift-samples, Console, Insights, Storage, CSISnapshot, NodeTuning, ImageRegistry, Build và DeploymentConfig.                                                                                  |
| v4.15    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.15. Khi chỉ định phiên bản v4.15, các tính năng được giới thiệu trong những phiên bản OpenShift Container Platform mới hơn sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.15 bao gồm: baremetal, MachineAPI, marketplace, OperatorLifecycleManager, openshift-samples, Console, Insights, Storage, CSISnapshot, NodeTuning, ImageRegistry, Build, CloudCredential và DeploymentConfig.                                           |
| v4.16    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.16. Khi chỉ định v4.16, các tính năng được giới thiệu trong những phiên bản OpenShift Container Platform mới hơn sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.16 bao gồm: baremetal, MachineAPI, marketplace, OperatorLifecycleManager, openshift-samples, Console, Insights, Storage, CSISnapshot, NodeTuning, ImageRegistry, Build, CloudCredential, DeploymentConfig và CloudControllerManager.                             |
| v4.17    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.17. Khi chỉ định v4.17, các tính năng được giới thiệu trong những phiên bản OpenShift Container Platform mới hơn sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.17 bao gồm: baremetal, MachineAPI, marketplace, OperatorLifecycleManager, openshift-samples, Console, Insights, Storage, CSISnapshot, NodeTuning, ImageRegistry, Build, CloudCredential, DeploymentConfig và CloudControllerManager.                             |
| v4.18    | Hãy chọn tùy chọn này khi bạn muốn kích hoạt các tính năng mặc định cho OpenShift Container Platform 4.18. Khi chỉ định v4.18, các tính năng được giới thiệu trong những phiên bản OpenShift Container Platform mới hơn sẽ không được kích hoạt. Các tính năng mặc định trong OpenShift Container Platform 4.18 bao gồm: baremetal, MachineAPI, marketplace, OperatorLifecycleManager, OperatorLifecycleManagerV1, openshift-samples, Console, Insights, Storage, CSISnapshot, NodeTuning, ImageRegistry, Build, CloudCredential, DeploymentConfig và CloudControllerManager. |
| None     | Hãy chỉ rõ các trường hợp mà những tập hợp khác trở nên quá lớn, đồng thời bạn không cần thêm bất kỳ khả năng (capabilities) nào hoặc muốn tinh chỉnh thông qua tham số `additionalEnabledCapabilities`.                                                                                                                                                                                                                                                                                                                                                                      |

## III.2. Các tính năng tùy chọn của cụm trong OpenShift Container Platform 4.22
Hiện tại, các Operator của cụm (cluster Operator) cung cấp những tính năng cho các khả năng tùy chọn này.

Các phần dưới đây tóm tắt những tính năng do từng khả năng cung cấp, cũng như các chức năng mà bạn sẽ mất đi nếu vô hiệu hóa một khả năng nào đó.

## III.2.1. Cluster Baremetal Operator
Cluster Baremetal Operator cung cấp các tính năng hỗ trợ cho khả năng làm việc với hạ tầng máy chủ vật lý (bare-metal).

Cluster Baremetal Operator (CBO) triển khai tất cả các thành phần cần thiết để chuyển đổi một máy chủ vật lý (bare-metal server) thành một worker node hoàn chỉnh, sẵn sàng chạy các compute node trong OpenShift Container Platform.

CBO đảm bảo rằng quá trình triển khai metal3 — bao gồm các container Bare Metal Operator (BMO) và Ironic — được thực thi trên một trong các control plane node thuộc cụm OpenShift Container Platform. CBO cũng theo dõi các cập nhật đối với những tài nguyên mà nó quản lý trong OpenShift Container Platform để thực hiện các thao tác phù hợp.

Khả năng làm việc với hạ tầng máy chủ vật lý là yêu cầu bắt buộc đối với các mô hình triển khai sử dụng hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure). Việc vô hiệu hóa tính năng này có thể dẫn đến các vấn đề không mong muốn trong quá trình triển khai.

> **Quan trọng:** Nếu khả năng làm việc với hạ tầng máy chủ vật lý bị vô hiệu hóa, cụm (cluster) sẽ không thể cung cấp (provision) hoặc quản lý các máy chủ vật lý (bare-metal node). Chỉ nên vô hiệu hóa tính năng này nếu không có bất kỳ tài nguyên BareMetalHost nào trong môi trường triển khai của bạn. Khả năng làm việc với hạ tầng máy chủ vật lý phụ thuộc vào tính năng MachineAPI; do đó, nếu bạn kích hoạt khả năng làm việc với hạ tầng máy chủ vật lý, bạn cũng phải kích hoạt MachineAPI.

> **Lưu ý:** Red Hat khuyến nghị các quản trị viên cụm chỉ nên vô hiệu hóa khả năng làm việc với hạ tầng máy chủ vật lý trong các trường hợp triển khai sử dụng hạ tầng do người dùng cung cấp (user-provisioned infrastructure) và không có bất kỳ tài nguyên BareMetalHost nào tồn tại trong cụm.

## III.2.2. Khả năng Build (Build capability)
Khả năng Build cho phép sử dụng Build API. Build API quản lý vòng đời của các đối tượng Build và BuildConfig.

> **Quan trọng:** Nếu bạn vô hiệu hóa khả năng Build, các tài nguyên sau đây sẽ không còn khả dụng trong cụm (cluster):
> 
> * Các tài nguyên Build và BuildConfig
> * Tài khoản dịch vụ (service account) dành cho trình xây dựng (builder)
> 
> Chỉ vô hiệu hóa khả năng Build nếu bạn không cần sử dụng các tài nguyên Build, BuildConfig hoặc tài khoản dịch vụ dành cho trình xây dựng trong cụm.

## III.2.3. Cloud Controller Manager Operator
Cloud Controller Manager Operator cung cấp các tính năng cho khả năng `CloudControllerManager`.

> **Lưu ý:** Hiện tại, việc vô hiệu hóa khả năng `CloudControllerManager` không được hỗ trợ trên tất cả các nền tảng.

Bạn có thể xác định xem cụm (cluster) của mình có hỗ trợ vô hiệu hóa khả năng `CloudControllerManager` hay không bằng cách kiểm tra các giá trị trong tệp cấu hình cài đặt (`install-config.yaml`) của cụm đó.

Trong tệp `install-config.yaml`, hãy tìm tham số `platform`. 

* Nếu giá trị của tham số `platform` là `Baremetal` hoặc `None`, bạn có thể vô hiệu hóa khả năng `CloudControllerManager` trên cụm của mình. 
* Nếu giá trị của tham số `platform` là `External`, hãy tìm tham số `platform.external.cloudControllerManager`. Nếu giá trị của tham số `platform.external.cloudControllerManager` là `None`, bạn có thể vô hiệu hóa khả năng `CloudControllerManager` trên cụm của mình.

> **Quan trọng:** Nếu các tham số này chứa bất kỳ giá trị nào khác ngoài những giá trị đã liệt kê, bạn không thể vô hiệu hóa khả năng `CloudControllerManager` trên cụm của mình.

> **Lưu ý:** Trạng thái của Operator này là "General Availability" (Sẵn sàng sử dụng rộng rãi) đối với Amazon Web Services (AWS), Google Cloud, IBM Cloud®, Microsoft Azure (toàn cầu), Microsoft Azure Stack Hub, Nutanix, Red Hat OpenStack Platform (RHOSP) và VMware vSphere.

Operator này hiện ở trạng thái "Technology Preview" (Xem trước công nghệ) đối với IBM Power® Virtual Server.

Cloud Controller Manager Operator quản lý và cập nhật các thành phần "cloud controller manager" được triển khai trên nền tảng OpenShift Container Platform. Operator này được xây dựng dựa trên framework Kubebuilder và các thư viện `controller-runtime`. Bạn có thể cài đặt Cloud Controller Manager Operator thông qua Cluster Version Operator (CVO).

Cloud Controller Manager Operator bao gồm các thành phần sau:

* Operator
* Cloud configuration observer (thành phần quan sát cấu hình đám mây)

Theo mặc định, Operator này cung cấp các chỉ số (metrics) Prometheus thông qua dịch vụ metrics.

## III.2.4. Cloud Credential Operator
Cloud Credential Operator cung cấp các tính năng cho khả năng `CloudCredential`.

> **Lưu ý:** Hiện tại, việc vô hiệu hóa khả năng `CloudCredential` chỉ được hỗ trợ đối với các cụm (cluster) chạy trên hạ tầng vật lý (bare-metal).

Cloud Credential Operator (CCO) quản lý thông tin xác thực (credential) của nhà cung cấp dịch vụ đám mây dưới dạng các định nghĩa tài nguyên tùy chỉnh (Custom Resource Definitions - CRD) trong Kubernetes.

CCO đồng bộ hóa dựa trên các tài nguyên tùy chỉnh (Custom Resource - CR) có tên là `CredentialsRequest`, cho phép các thành phần của OpenShift Container Platform yêu cầu thông tin xác thực từ nhà cung cấp dịch vụ đám mây với các quyền cụ thể cần thiết để cụm hoạt động.

Bằng cách thiết lập các giá trị khác nhau cho tham số `credentialsMode` trong tệp `install-config.yaml`, CCO có thể được cấu hình để vận hành theo nhiều chế độ khác nhau. Nếu không chỉ định chế độ nào, hoặc tham số `credentialsMode` được đặt thành một chuỗi rỗng (`""`), CCO sẽ hoạt động ở chế độ mặc định.

## III.2.5. Cluster Image Registry Operator
Cluster Image Registry Operator cung cấp các tính năng cho khả năng `ImageRegistry` (registry hình ảnh).

Cluster Image Registry Operator quản lý một thực thể duy nhất (singleton instance) của registry hình ảnh OpenShift. Nó quản lý toàn bộ cấu hình của registry, bao gồm cả việc thiết lập bộ lưu trữ.

Khi khởi chạy lần đầu, Operator sẽ tạo một thực thể tài nguyên `image-registry` mặc định dựa trên cấu hình được phát hiện trong cụm (cluster). Thực thể này xác định loại bộ lưu trữ đám mây cần sử dụng, tùy thuộc vào nhà cung cấp dịch vụ đám mây.

Nếu không có đủ thông tin để xác định một tài nguyên `image-registry` hoàn chỉnh, Operator sẽ tạo một tài nguyên chưa hoàn thiện và cập nhật trạng thái của tài nguyên đó với thông tin về những thành phần còn thiếu.

Cluster Image Registry Operator chạy trong namespace `openshift-image-registry` và quản lý thực thể registry tại vị trí đó. Tất cả các tài nguyên cấu hình và khối lượng công việc (workload) dành cho registry đều nằm trong namespace này.

Để tích hợp registry hình ảnh vào hệ thống xác thực và ủy quyền người dùng của cụm, một "image pull secret" (bí mật dùng để kéo hình ảnh) sẽ được tạo cho mỗi tài khoản dịch vụ (service account) trong cụm.

> **Quan trọng:** Nếu bạn vô hiệu hóa khả năng `ImageRegistry` hoặc vô hiệu hóa registry hình ảnh OpenShift tích hợp trong cấu hình của Cluster Image Registry Operator, thì "image pull secret" sẽ không được tạo cho các tài khoản dịch vụ.

Việc vô hiệu hóa khả năng `ImageRegistry` có thể giúp giảm mức tiêu thụ tài nguyên tổng thể của OpenShift Container Platform trong các môi trường viễn thông (Telco). Tùy thuộc vào mô hình triển khai, bạn có thể vô hiệu hóa thành phần này nếu không cần sử dụng đến nó.

## III.2.6. Cluster Storage Operator
Cluster Storage Operator cung cấp các tính năng cho khả năng lưu trữ (storage capability).

Cluster Storage Operator thiết lập các cấu hình lưu trữ mặc định trên toàn bộ cụm OpenShift Container Platform. Thành phần này đảm bảo luôn có một lớp lưu trữ (storageclass) mặc định cho các cụm OpenShift Container Platform. Ngoài ra, nó còn cài đặt các trình điều khiển Container Storage Interface (CSI), cho phép cụm của bạn sử dụng nhiều hệ thống lưu trữ (storage backend) khác nhau.

> **Quan trọng:** Nếu tính năng lưu trữ của cụm bị vô hiệu hóa, cụm sẽ không có lớp lưu trữ mặc định cũng như bất kỳ trình điều khiển CSI nào. Người dùng có đặc quyền quản trị viên vẫn có thể tạo lớp lưu trữ mặc định và tự cài đặt các trình điều khiển CSI theo cách thủ công ngay cả khi tính năng lưu trữ của cụm đã bị vô hiệu hóa.

**Lưu ý**  
Lớp lưu trữ do Operator tạo ra có thể được chuyển đổi thành lớp không mặc định bằng cách chỉnh sửa chú thích (annotation) của nó; tuy nhiên, lớp lưu trữ này không thể bị xóa khi Operator đang hoạt động.

## III.2.7. Console Operator
Console Operator cung cấp các tính năng cho chức năng Console (bảng điều khiển).

Console Operator thực hiện cài đặt và duy trì giao diện web (web console) của OpenShift Container Platform trên một cụm (cluster). Console Operator được cài đặt theo mặc định và tự động duy trì giao diện console này.

## III.2.8. Cluster CSI Snapshot Controller Operator
Cluster CSI Snapshot Controller Operator cung cấp các tính năng cho khả năng `CSISnapshot`.

Cluster CSI Snapshot Controller Operator thực hiện cài đặt và duy trì CSI Snapshot Controller. CSI Snapshot Controller chịu trách nhiệm theo dõi các đối tượng CRD `VolumeSnapshot`, đồng thời quản lý vòng đời tạo và xóa các bản chụp (snapshot) của volume.

## III.2.9. Tính năng DeploymentConfig
Tính năng `DeploymentConfig` cho phép sử dụng và quản lý API DeploymentConfig.

> **Quan trọng:** Nếu bạn tắt tính năng `DeploymentConfig`, các tài nguyên sau đây sẽ không còn khả dụng trong cụm (cluster):
> 
> * Các tài nguyên `DeploymentConfig`
> * Tài khoản dịch vụ (service account) dành cho trình triển khai (`deployer`)
> 
> Chỉ nên tắt tính năng `DeploymentConfig` nếu bạn không cần sử dụng các tài nguyên `DeploymentConfig` và tài khoản dịch vụ dành cho trình triển khai (`deployer`) trong cụm.

## III.2.10. Trình điều khiển Ingress
Trình điều khiển Ingress cung cấp các tính năng cho khả năng Ingress.

Trình điều khiển Ingress cấu hình và quản lý bộ định tuyến của OpenShift Container Platform.

**CRD**

* clusteringresses.ingress.openshift.io
    * Phạm vi: Không gian tên
    * CR: clusteringresses
    * Xác thực: Không

**Đối tượng cấu hình**

* Cấu hình cụm
    * Tên loại: clusteringresses.ingress.openshift.io
    * Tên phiên bản: mặc định
    * Lệnh xem:

```shell
oc get clusteringresses.ingress.openshift.io \
  -n openshift-ingress-operator default -o yaml
```

**Ghi chú**  
Ingress Operator thiết lập bộ định tuyến (router) trong dự án openshift-ingress và tạo deployment cho bộ định tuyến đó:

```shell
oc get deployment -n openshift-ingress
```

Ingress Operator sử dụng thông tin `clusterNetwork[].cidr` từ trạng thái `network/cluster` để xác định chế độ hoạt động (IPv4, IPv6 hoặc dual-stack) cho Ingress Controller (bộ định tuyến) mà nó quản lý. Chẳng hạn, nếu `clusterNetwork` chỉ chứa một CIDR phiên bản 6 (v6), thì Ingress Controller sẽ hoạt động ở chế độ chỉ hỗ trợ IPv6.

Trong ví dụ dưới đây, các Ingress Controller do Ingress Operator quản lý sẽ chạy ở chế độ chỉ hỗ trợ IPv4, vì chỉ có một mạng cụm (cluster network) tồn tại và mạng đó là một CIDR IPv4:

```shell
oc get network/cluster -o jsonpath='{.status.clusterNetwork[*]}'
```

Ví dụ đầu ra

```
map[cidr:10.128.0.0/14 hostPrefix:23]
```

## III.2.11. Insights Operator
Insights Operator cung cấp các tính năng cho khả năng Insights.

Insights Operator thu thập dữ liệu cấu hình của OpenShift Container Platform và gửi dữ liệu đó đến Red Hat.

Dữ liệu này được sử dụng để đưa ra các khuyến nghị chủ động về những vấn đề tiềm ẩn mà cụm (cluster) có thể gặp phải. Các khuyến nghị này được truyền đạt đến quản trị viên cụm thông qua dịch vụ tư vấn Red Hat Lightspeed trên [console.redhat.com](https://console.redhat.com/).

> **Lưu ý:** Insights Operator bổ sung cho tính năng Telemetry của OpenShift Container Platform.

## III.2.12. Khả năng Machine API
Các toán tử (operator) gồm `machine-api-operator`, `cluster-autoscaler-operator` và `cluster-control-plane-machine-set-operator` cung cấp các tính năng cho khả năng Machine API. Bạn chỉ có thể vô hiệu hóa khả năng này nếu cài đặt cụm (cluster) với cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure).

Khả năng Machine API chịu trách nhiệm cho toàn bộ quá trình cấu hình và quản lý máy (machine) trong cụm. Nếu bạn vô hiệu hóa khả năng Machine API trong quá trình cài đặt, bạn sẽ cần phải tự mình thực hiện thủ công mọi tác vụ liên quan đến các máy đó.

## III.2.13. Marketplace Operator
Marketplace Operator cung cấp các tính năng cho chức năng marketplace (thị trường ứng dụng).

Marketplace Operator đơn giản hóa quy trình đưa các Operator từ bên ngoài cụm (off-cluster) vào cụm của bạn bằng cách sử dụng một tập hợp các danh mục (catalog) mặc định của Operator Lifecycle Manager (OLM) trên cụm đó. Khi Marketplace Operator được cài đặt, nó sẽ tạo ra namespace `openshift-marketplace`. OLM đảm bảo rằng các nguồn danh mục (catalog source) được cài đặt trong namespace `openshift-marketplace` sẽ khả dụng cho tất cả các namespace khác trên cụm.

Nếu bạn vô hiệu hóa chức năng marketplace, Marketplace Operator sẽ không tạo namespace `openshift-marketplace`. Các nguồn danh mục vẫn có thể được cấu hình và quản lý thủ công trên cụm, nhưng OLM cần namespace `openshift-marketplace` để làm cho các danh mục đó khả dụng với tất cả các namespace trên cụm. Những người dùng có quyền hạn cao, chẳng hạn như quản trị viên hệ thống hoặc quản trị viên cụm (cluster administrator) — những người có quyền tạo các namespace bắt đầu bằng tiền tố `openshift-` — có thể tự tạo namespace `openshift-marketplace` theo cách thủ công.

Nếu bạn kích hoạt chức năng marketplace, bạn có thể bật hoặc tắt từng danh mục riêng lẻ bằng cách cấu hình Marketplace Operator.

## III.2.14. Node Tuning Operator
Node Tuning Operator cung cấp các tính năng cho khả năng NodeTuning (tinh chỉnh nút).

Node Tuning Operator hỗ trợ quản lý việc tinh chỉnh ở cấp độ nút thông qua việc điều phối tiến trình nền (daemon) TuneD, đồng thời đạt được hiệu năng có độ trễ thấp nhờ sử dụng bộ điều khiển Performance Profile (cấu hình hiệu năng).

Phần lớn các ứng dụng hiệu năng cao đều yêu cầu tinh chỉnh nhân hệ điều hành (kernel) ở một mức độ nhất định. Node Tuning Operator cung cấp cho người dùng một giao diện quản lý thống nhất đối với các tham số sysctl ở cấp độ nút, đồng thời mang lại sự linh hoạt cao hơn trong việc bổ sung các cấu hình tinh chỉnh tùy chỉnh theo nhu cầu cụ thể.

Nếu bạn vô hiệu hóa khả năng NodeTuning, một số thiết lập tinh chỉnh mặc định sẽ không được áp dụng cho các nút trong mặt phẳng điều khiển (control-plane nodes). Điều này có thể hạn chế khả năng mở rộng và hiệu năng của các cụm (cluster) quy mô lớn, với số lượng nút hoặc tuyến đường (route) vượt quá 900.

## III.2.15. Cluster Samples Operator
Cluster Samples Operator cung cấp các tính năng cho thành phần `openshift-samples`.

Cluster Samples Operator quản lý các image stream (luồng hình ảnh) mẫu và các template (mẫu cấu hình) được lưu trữ trong namespace `openshift`.

Khi khởi chạy lần đầu, Operator này sẽ tạo tài nguyên cấu hình mẫu mặc định để bắt đầu quá trình tạo các image stream và template. Đối tượng cấu hình này có phạm vi toàn cụm (cluster-scoped), với khóa (key) là `cluster` và loại (type) là `configs.samples`.

Các image stream này dựa trên Red Hat Enterprise Linux CoreOS (RHCOS) dành cho OpenShift Container Platform, trỏ đến các hình ảnh (image) trên registry.redhat.io. Tương tự, các template ở đây là những mẫu được phân loại dành riêng cho OpenShift Container Platform.

Nếu bạn vô hiệu hóa thành phần samples, người dùng sẽ không thể truy cập vào các image stream, mẫu (sample) và template mà thành phần này cung cấp. Tùy thuộc vào quá trình triển khai, bạn có thể cân nhắc vô hiệu hóa thành phần này nếu không cần sử dụng đến nó.

## III.2.16. Giới thiệu về Operator Lifecycle Manager (OLM) Classic
OLM (Classic) cung cấp các tính năng cho khả năng (capability) `OperatorLifecycleManager`.

Operator Lifecycle Manager (OLM) Classic hỗ trợ người dùng cài đặt, cập nhật và quản lý vòng đời của các ứng dụng gốc trên Kubernetes (được gọi là các Operator) cùng các dịch vụ liên quan, chạy trên các cụm OpenShift Container Platform. Operator Lifecycle Manager (OLM) Classic là một phần của Operator Framework – một bộ công cụ mã nguồn mở được thiết kế để quản lý các Operator theo cách hiệu quả, tự động và có khả năng mở rộng.

Nếu một Operator yêu cầu bất kỳ API nào dưới đây, bạn bắt buộc phải kích hoạt khả năng `OperatorLifecycleManager`:

* `ClusterServiceVersion`
* `CatalogSource`
* `Subscription`
* `InstallPlan`
* `OperatorGroup`

> **Quan trọng:** Khả năng `marketplace` phụ thuộc vào khả năng `OperatorLifecycleManager`. Bạn không thể vô hiệu hóa khả năng `OperatorLifecycleManager` trong khi vẫn kích hoạt khả năng `marketplace`.

## III.2.17. Operator Lifecycle Manager (OLM) v1 Operator
OLM v1 cung cấp các tính năng cho khả năng `OperatorLifecycleManagerV1`.

Bắt đầu từ OpenShift Container Platform 4.18, OLM v1 được kích hoạt mặc định cùng với OLM (phiên bản truyền thống - Classic). Phiên bản thế hệ mới này mang đến một khung làm việc (framework) được cập nhật, phát triển dựa trên nhiều khái niệm từ OLM (Classic), cho phép các quản trị viên cụm (cluster administrator) mở rộng các tính năng cho người dùng của họ.

OLM v1 quản lý vòng đời của đối tượng `ClusterExtension` mới — bao gồm các Operator thông qua định dạng gói `registry+v1` — đồng thời kiểm soát quá trình cài đặt, nâng cấp và kiểm soát truy cập dựa trên vai trò (RBAC) đối với các phần mở rộng (extension) bên trong một cụm.

Trong OpenShift Container Platform, OLM v1 được cung cấp bởi Operator cụm (cluster Operator) có tên là `olm`.

> **Lưu ý:** Operator cụm `olm` sẽ thông báo cho các quản trị viên cụm nếu có bất kỳ phần mở rộng nào đã cài đặt đang ngăn cản quá trình nâng cấp cụm, dựa trên các thuộc tính `olm.maxOpenShiftVersion` của chúng. Để biết thêm thông tin, hãy xem phần "Khả năng tương thích với các phiên bản OpenShift Container Platform".

Operator Lifecycle Manager (OLM) v1 bao gồm các dự án thành phần sau:

* Operator Controller: Thành phần trung tâm của OLM v1, giúp mở rộng Kubernetes thông qua một API cho phép người dùng cài đặt và quản lý vòng đời của các Operator cũng như các phần mở rộng. Thành phần này sử dụng thông tin từ `catalogd`. 
* Catalogd: Một phần mở rộng của Kubernetes có chức năng giải nén nội dung danh mục dựa trên tệp (file-based catalog - FBC) — vốn được đóng gói và phân phối trong các hình ảnh container (container image) — để các ứng dụng khách (client) bên trong cụm có thể sử dụng. Là một thành phần trong kiến trúc vi dịch vụ (microservices) của OLM v1, `catalogd` lưu trữ siêu dữ liệu (metadata) cho các phần mở rộng Kubernetes (do chính tác giả của các phần mở rộng đó đóng gói), từ đó giúp người dùng khám phá các nội dung có thể cài đặt.
* CRD:
    * clusterextension.olm.operatorframework.io
        * Phạm vi: Cụm
        * CR: ClusterExtension
    * clustercatalog.olm.operatorframework.io
        * Phạm vi: Cụm
        * CR: ClusterCatalog
* Xem các dự án sau trong phần Tài nguyên bổ sung:
    * operator-framework/operator-controller
    * operator-framework/catalogd

## III.3. Xem các khả năng của cụm (cluster capabilities)
Với tư cách là quản trị viên cụm, bạn có thể xem các khả năng của cụm thông qua trạng thái của tài nguyên `clusterversion`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (oc).

**Quy trình**  
Để xem trạng thái các khả năng của cụm, hãy chạy lệnh sau:

```shell
oc get clusterversion version -o \
  jsonpath='{.spec.capabilities}{"\n"}{.status.capabilities}{"\n"}'
```

Ví dụ đầu ra

```json
{"additionalEnabledCapabilities":["openshift-samples"],"baselineCapabilitySet":"None"}
{"enabledCapabilities":["openshift-samples"],"knownCapabilities":["CSISnapshot","Console","Insights","Storage","baremetal","marketplace","openshift-samples"]}
```

## 3.4. Kích hoạt các tính năng của cụm thông qua việc thiết lập tập hợp tính năng cơ sở (baseline capability set)
Với tư cách là quản trị viên cụm, bạn có thể kích hoạt các tính năng của cụm vào bất kỳ thời điểm nào sau khi cài đặt OpenShift Container Platform bằng cách thiết lập tham số cấu hình `baselineCapabilitySet`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (oc).

**Quy trình**  
Để thiết lập tham số cấu hình `baselineCapabilitySet`, hãy chạy lệnh sau:

```shell
oc patch clusterversion version --type merge -p \
  '{"spec":{"capabilities":{"baselineCapabilitySet":"vCurrent"}}}'
```

Đối với `baselineCapabilitySet`, bạn có thể chỉ định vCurrent, v4.22 hoặc None.

## III.5. Kích hoạt các tính năng cụm bằng cách thiết lập các tính năng bổ sung được kích hoạt
Với tư cách là quản trị viên cụm, bạn có thể kích hoạt các tính năng cụm vào bất kỳ thời điểm nào sau khi cài đặt OpenShift Container Platform bằng cách thiết lập tham số cấu hình `additionalEnabledCapabilities`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (CLI - `oc`).

**Quy trình**  
1. Xem các tính năng bổ sung đã được kích hoạt bằng cách chạy lệnh sau:

```shell
oc get clusterversion version -o jsonpath='{.spec.capabilities.additionalEnabledCapabilities}{"\n"}'
```

Ví dụ đầu ra

```json
["openshift-samples"]
```

2. Để thiết lập tham số cấu hình additionalEnabledCapabilities, hãy chạy lệnh sau:

```shell
oc patch clusterversion/version --type merge -p \
  '{"spec":{"capabilities":{"additionalEnabledCapabilities":["openshift-samples", "marketplace"]}}}'
```

> **Quan trọng:** Bạn không thể vô hiệu hóa một tính năng đã được kích hoạt trong cụm (cluster). Cluster Version Operator (CVO) sẽ tiếp tục duy trì trạng thái của tính năng đã được kích hoạt trong cụm đó.

Nếu bạn cố gắng vô hiệu hóa một tính năng, CVO sẽ hiển thị thông số kỹ thuật khác biệt (divergent spec):

```shell
oc get clusterversion version -o \
  jsonpath='{.status.conditions[?(@.type=="ImplicitlyEnabledCapabilities")]}{"\n"}'
```

Ví dụ đầu ra

```json
{"lastTransitionTime":"2022-07-22T03:14:35Z","message":"The following capabilities could not be disabled: openshift-samples","reason":"CapabilitiesImplicitlyEnabled","status":"True","type":"ImplicitlyEnabledCapabilities"}
```

> **Lưu ý:** Trong quá trình nâng cấp cụm (cluster), một tính năng nhất định có thể được tự động kích hoạt. Nếu một tài nguyên đã chạy trên cụm trước thời điểm nâng cấp, thì bất kỳ tính năng nào gắn liền với tài nguyên đó cũng sẽ được kích hoạt. Chẳng hạn, trong quá trình nâng cấp cụm, một tài nguyên đang chạy trên cụm có thể được hệ thống chuyển sang thuộc phạm vi của tính năng "marketplace" (thị trường). Ngay cả khi quản trị viên cụm không chủ động kích hoạt tính năng này, nó vẫn sẽ được hệ thống tự động kích hoạt.
