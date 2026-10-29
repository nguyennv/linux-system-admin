Cài đặt OpenShift
-----------------

# Cài đặt cụm OpenShift gồm hai nút
## I. Cấu hình hai nút kèm nút trọng tài (Arbiter)
Cụm OpenShift Container Platform gồm hai nút mặt phẳng điều khiển (control plane node) và một nút trọng tài (arbiter node) cục bộ là một cấu trúc liên kết (topology) nhỏ gọn và tiết kiệm chi phí.

Nút trọng tài lưu trữ toàn bộ dữ liệu etcd, duy trì cơ chế đa số (quorum) cho etcd và ngăn chặn tình trạng phân tách hệ thống (split-brain). Nút trọng tài không chạy các thành phần bổ sung của mặt phẳng điều khiển như `kube-apiserver` hay `kube-controller-manager`, cũng không thực thi các khối lượng công việc (workload).

Để cài đặt một cụm gồm hai nút mặt phẳng điều khiển và một nút trọng tài cục bộ, bạn cần gán vai trò "arbiter" cho ít nhất một trong các nút và thiết lập số lượng nút mặt phẳng điều khiển cho cụm là 2. Mặc dù OpenShift Container Platform hiện không giới hạn số lượng nút trọng tài, nhưng thông thường chỉ cần một nút để tối thiểu hóa việc sử dụng tài nguyên phần cứng.

Sau khi cài đặt, bạn có thể thêm các nút worker vào cụm này, nhưng không thể chuyển đổi nó thành một cụm đa nút (multi-node cluster) tiêu chuẩn.

> **Lưu ý:** Không nên thêm quá hai nút worker vào cụm OpenShift Container Platform này. Đối với cụm có sử dụng nút trọng tài, các yêu cầu về mạng để đảm bảo kết nối giữa các máy vẫn tương tự như đối với một cụm thông thường.

Tham khảo tại **"I.7. Giới thiệu về nút trọng tài (arbiter node)"** tại tài liệu **Cài đặt cụm tại chỗ (on-premise) bằng trình cài đặt dựa trên tác nhân (Agent-based Installer)**

## II. Cấu hình hai nút với cơ chế cách ly (fencing)
### II.1. Chuẩn bị cài đặt cụm OpenShift hai nút với cơ chế cách ly (fencing)
Cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly (fencing) mang lại khả năng sẵn sàng cao (HA) trong khi vẫn tiết kiệm tài nguyên phần cứng. Cấu hình này được thiết kế cho các môi trường phân tán hoặc môi trường biên (edge), nơi việc triển khai cụm với mặt phẳng điều khiển (control plane) gồm ba nút là không khả thi.

Cụm hai nút không bao gồm các nút tính toán (compute node) riêng biệt. Hai máy chủ thuộc mặt phẳng điều khiển sẽ vừa thực hiện quản lý cụm, vừa chạy các khối lượng công việc (workload) của người dùng.

Cơ chế cách ly (fencing) được quản lý bởi Pacemaker; cơ chế này có thể cô lập một nút không phản hồi thông qua việc sử dụng Baseboard Management Console (BMC) của nút đó. Sau khi nút không phản hồi bị cách ly, nút còn lại có thể tiếp tục vận hành cụm một cách an toàn mà không gặp rủi ro gây hỏng dữ liệu hoặc tài nguyên.

> **Lưu ý:** Bạn có thể triển khai cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly bằng cách sử dụng phương pháp hạ tầng do người dùng cung cấp (user-provisioned infrastructure) hoặc phương pháp hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure).

Cụm OpenShift hai nút với cơ chế cách ly yêu cầu các máy chủ sau:

**Bảng 2.1. Các máy chủ tối thiểu cần thiết**

| Hosts                              | Mô tả                                                                                                                                                                                             |
|------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hai máy thuộc mặt phẳng điều khiển | Các máy thuộc mặt phẳng điều khiển (control plane) chạy các dịch vụ Kubernetes và OpenShift Container Platform, tạo nên mặt phẳng điều khiển đó.                                                  |
| Một máy khởi động tạm thời         | Bạn cần một máy bootstrap để triển khai cụm OpenShift Container Platform trên các máy thuộc mặt phẳng điều khiển (control plane). Sau khi cài đặt xong cụm, bạn có thể loại bỏ máy bootstrap này. |

Các máy bootstrap và control plane phải sử dụng Red Hat Enterprise Linux CoreOS (RHCOS) làm hệ điều hành. Để biết hướng dẫn về cách cài đặt RHCOS và bắt đầu quy trình bootstrap, hãy xem phần "Cài đặt RHCOS và bắt đầu quy trình bootstrap cho OpenShift Container Platform".

> **Lưu ý:** Yêu cầu sử dụng RHCOS chỉ áp dụng cho các mô hình triển khai trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure). Đối với các mô hình triển khai trên cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure), các máy bootstrap và control plane sẽ được trình cài đặt tự động thiết lập, vì vậy bạn không cần phải cài đặt RHCOS theo cách thủ công.

#### II.1.1. Các yêu cầu tối thiểu về tài nguyên để cài đặt cụm OpenShift hai nút có sử dụng cơ chế cách ly (fencing)
Mỗi cụm phải đáp ứng các yêu cầu tối thiểu để có thể vận hành đúng như mong đợi.

Mỗi máy trong cụm phải đáp ứng các yêu cầu tối thiểu sau đây:

**Bảng 2.2. Các yêu cầu tối thiểu về tài nguyên**

| Máy           | Hệ điều hành | CPU (1) | RAM   | Lưu trữ | IOPS (2) |
|---------------|--------------|---------|-------|---------|----------|
| Bootstrap     | RHCOS        | 4       | 16 GB | 120 GB  | 300      |
| Control plane | RHCOS        | 4       | 16 GB | 120 GB  | 300      |

> IOPS - Input/Output Per Second (Số lượng thao tác nhập/xuất mỗi giây)

1. Một CPU tương đương với một nhân vật lý khi tính năng đa luồng đồng thời (SMT) – hay còn gọi là Hyper-Threading – không được kích hoạt. Khi tính năng này được bật, hãy sử dụng công thức sau để tính toán tỷ lệ tương ứng: (số luồng trên mỗi nhân × số nhân) × số socket = số CPU.
2. OpenShift Container Platform và Kubernetes rất nhạy cảm với hiệu năng ổ đĩa; do đó, bạn nên sử dụng hệ thống lưu trữ có tốc độ cao, đặc biệt là đối với etcd trên các nút điều khiển (control plane nodes). Cần lưu ý rằng trên nhiều nền tảng đám mây, dung lượng lưu trữ và chỉ số IOPS thường tỷ lệ thuận với nhau; vì vậy, có thể bạn sẽ cần phân bổ dung lượng lưu trữ lớn hơn mức thực tế cần thiết để đạt được hiệu năng như mong muốn.

#### II.1.2. Các yêu cầu về DNS do người dùng cung cấp
Trong các triển khai OpenShift Container Platform, bạn phải đảm bảo rằng các thành phần của cụm (cluster) đáp ứng những tiêu chí nhất định về phân giải tên DNS, phục vụ cho các mục đích như giao tiếp nội bộ, xác thực chứng chỉ và tự động phát hiện nút (node).

Dưới đây là danh sách các thành phần cụm bắt buộc:

* Kubernetes API
* Wildcard (tên miền đại diện) cho ứng dụng trên OpenShift Container Platform
* Các máy bootstrap và các máy thuộc mặt phẳng điều khiển (control plane)

Phân giải DNS ngược (reverse DNS resolution) cũng là yêu cầu bắt buộc đối với Kubernetes API, máy bootstrap và các máy thuộc mặt phẳng điều khiển.

Các bản ghi DNS loại A/AAAA hoặc CNAME được sử dụng để phân giải tên, trong khi các bản ghi PTR được dùng cho phân giải tên ngược. Các bản ghi ngược đóng vai trò quan trọng vì Red Hat Enterprise Linux CoreOS (RHCOS) sử dụng chúng để thiết lập tên máy (hostname) cho tất cả các nút, trừ khi các tên máy này đã được cung cấp thông qua DHCP. Ngoài ra, các bản ghi ngược còn được dùng để tạo các yêu cầu ký chứng chỉ (Certificate Signing Request - CSR) cần thiết cho hoạt động của OpenShift Container Platform.

> **Lưu ý:** Bạn nên sử dụng máy chủ DHCP để cung cấp tên máy cho từng nút trong cụm. Hãy tham khảo phần các khuyến nghị về DHCP cho hạ tầng do người dùng cung cấp (user-provisioned infrastructure) để biết thêm thông tin.

Các bản ghi DNS dưới đây là bắt buộc đối với cụm OpenShift Container Platform do người dùng cung cấp và phải được thiết lập trước khi tiến hành cài đặt. Trong mỗi bản ghi, `<cluster_name>` là tên cụm và `<base_domain>` là tên miền cơ sở mà bạn chỉ định trong tệp `install-config.yaml`. Một bản ghi DNS hoàn chỉnh sẽ có dạng: `<component>.<cluster_name>.<base_domain>.`

**Bảng 2.3. Các bản ghi DNS bắt buộc**

| Thành phần     | Bản ghi                                            | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|----------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kubernetes API | `api.<cluster_name>.<base_domain>.`                | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định bộ cân bằng tải API. Các bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm và tất cả các nút bên trong cụm đó.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Kubernetes API | `api-int.<cluster_name>.<base_domain>.`            | Cần có bản ghi DNS A/AAAA hoặc CNAME, cùng với bản ghi DNS PTR, để xác định bộ cân bằng tải API (API load balancer) trong nội bộ hệ thống. Các bản ghi này phải có thể được phân giải từ tất cả các nút (node) bên trong cụm (cluster). **Quan trọng:** Máy chủ API (API server) phải có khả năng phân giải các nút worker thông qua tên máy chủ (hostname) đã được ghi lại trong Kubernetes. Nếu máy chủ API không thể phân giải tên các nút, các lệnh gọi API thông qua proxy có thể bị lỗi và bạn sẽ không thể truy xuất nhật ký (log) từ các pod.                                                                                                                                                                                                                                                                                                                                                      |
| Routes         | `*.apps.<cluster_name>.<base_domain>.`             | Một bản ghi DNS wildcard (dạng A/AAAA hoặc CNAME) trỏ đến bộ cân bằng tải ingress (ingress load balancer) của ứng dụng. Bộ cân bằng tải ingress này hướng lưu lượng đến các máy đang chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các nút tính toán (compute node). Trong các cấu trúc cụm không có nút tính toán chuyên biệt — chẳng hạn như cụm gồm hai hoặc ba nút — các nút thuộc mặt phẳng điều khiển (control plane node) cũng mang nhãn "worker", do đó các pod Ingress sẽ được lập lịch chạy trên chính các nút thuộc mặt phẳng điều khiển này. Các bản ghi này phải có thể phân giải được bởi cả các máy khách bên ngoài cụm lẫn tất cả các nút nằm bên trong cụm. Ví dụ: `bash-openshift-console.apps.<cluster_name>.<base_domain>` được sử dụng làm tuyến đường (route) wildcard dẫn đến bảng điều khiển (console) của OpenShift Container Platform. |
| Bootstrap      | `bootstrap.<cluster_name>.<base_domain>.`          | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định máy khởi động (bootstrap machine). Các bản ghi này phải có thể được phân giải bởi các nút (node) nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Control plane  | `<control_plane><n>.<cluster_name>.<base_domain>.` | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút thuộc mặt phẳng điều khiển (control plane nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

> **Lưu ý:** Trong OpenShift Container Platform 4.4 trở về sau, bạn không cần chỉ định các bản ghi SRV và máy chủ etcd trong cấu hình DNS của mình.

> **Mẹo:** Bạn có thể sử dụng lệnh `dig` để xác minh quá trình phân giải tên miền và phân giải ngược. Hãy xem phần "Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp" để biết các bước xác thực chi tiết.

##### II.1.2.1. Ví dụ về cấu hình DNS cho các cụm do người dùng cung cấp (user-provisioned clusters)
Hãy tham khảo các ví dụ về cấu hình DNS để hiểu cách thiết lập các bản ghi A và PTR nhằm đáp ứng các yêu cầu DNS khi triển khai OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp.

Các ví dụ về cấu hình DNS được cung cấp tại đây chỉ mang tính chất tham khảo và không nhằm mục đích đưa ra lời khuyên về việc lựa chọn giải pháp DNS này thay vì giải pháp khác.

Trong các ví dụ này, tên cụm (cluster name) là ocp4 và tên miền cơ sở (base domain) là example.com.

> **Lưu ý:** Trong một cụm gồm hai nút có sử dụng cơ chế cách ly (fencing), các máy thuộc mặt phẳng điều khiển (control plane) cũng đóng vai trò là các nút worker có thể lập lịch tác vụ. Do đó, cấu hình DNS chỉ được bao gồm hai nút thuộc mặt phẳng điều khiển này. Nếu sau đó bạn thêm các máy tính toán (compute machines), hãy cung cấp các bản ghi A và PTR tương ứng cho chúng, giống như trong quy trình cài đặt tiêu chuẩn do người dùng cung cấp.

Ví dụ dưới đây là tệp vùng (zone file) của BIND, minh họa các bản ghi DNS A mẫu dùng cho việc phân giải tên trong một cụm do người dùng cung cấp.

> **Lưu ý:** Trong ví dụ này, cùng một bộ cân bằng tải (load balancer) được sử dụng cho cả Kubernetes API và lưu lượng truy cập ứng dụng (application ingress). Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và cho lưu lượng truy cập ứng dụng, nhờ đó có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

```
$TTL 1W
@ IN  SOA ns1.example.com.  root (
      2019070700  ; serial
      3H    ; refresh (3 hours)
      30M   ; retry (30 minutes)
      2W    ; expiry (2 weeks)
      1W )    ; minimum (1 week)
  IN  NS  ns1.example.com.
  IN  MX 10 smtp.example.com.
;
;
ns1.example.com.    IN  A 192.168.1.5
smtp.example.com.   IN  A 192.168.1.5
;
helper.example.com.   IN  A 192.168.1.5
helper.ocp4.example.com.  IN  A 192.168.1.5
;
api.ocp4.example.com.   IN  A 192.168.1.5
api-int.ocp4.example.com. IN  A 192.168.1.5
;
*.apps.ocp4.example.com.  IN  A 192.168.1.5
;
bootstrap.ocp4.example.com. IN  A 192.168.1.96
;
control-plane0.ocp4.example.com.  IN  A 192.168.1.97
control-plane1.ocp4.example.com.  IN  A 192.168.1.98
;
;
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho API và được sử dụng cho các hoạt động giao tiếp nội bộ trong cụm (cluster).
* `*.apps.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các tuyến đường (route) dạng ký tự đại diện (wildcard). Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho ứng dụng (application ingress load balancer). Bộ cân bằng tải này hướng lưu lượng đến các máy chủ đang chạy các pod Ingress Controller.
* `bootstrap.ocp4.example.com`: Cung cấp khả năng phân giải tên cho máy chủ bootstrap.
* `control-plane0.ocp4.example.com`: Cung cấp khả năng phân giải tên cho các máy chủ thuộc mặt phẳng điều khiển (control plane).

Ví dụ về tệp vùng (zone file) BIND dưới đây hiển thị các bản ghi PTR mẫu dùng cho việc phân giải tên ngược (reverse name resolution) trong một cụm do người dùng tự cung cấp (user-provisioned cluster):

```
$TTL 1W
@ IN  SOA ns1.example.com.  root (
      2019070700  ; serial
      3H    ; refresh (3 hours)
      30M   ; retry (30 minutes)
      2W    ; expiry (2 weeks)
      1W )    ; minimum (1 week)
  IN  NS  ns1.example.com.
;
5.1.168.192.in-addr.arpa. IN  PTR api.ocp4.example.com.
5.1.168.192.in-addr.arpa. IN  PTR api-int.ocp4.example.com.
;
96.1.168.192.in-addr.arpa.  IN  PTR bootstrap.ocp4.example.com.
;
97.1.168.192.in-addr.arpa.  IN  PTR control-plane0.ocp4.example.com.
98.1.168.192.in-addr.arpa.  IN  PTR control-plane1.ocp4.example.com.
;
;
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược (reverse DNS resolution) cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải (load balancer) API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải API và được sử dụng cho các hoạt động giao tiếp nội bộ trong cụm (cluster).
* `bootstrap.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho máy bootstrap.
* `control-plane0.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy thuộc mặt phẳng điều khiển (control plane).

> **Lưu ý:** Không bắt buộc phải có bản ghi PTR cho tên miền đại diện (wildcard) của ứng dụng trên OpenShift Container Platform.

#### II.1.3. Các yêu cầu về DNS do trình cài đặt cung cấp
Trong các triển khai OpenShift Container Platform, bạn phải đảm bảo rằng các thành phần của cụm (cluster) đáp ứng những tiêu chí nhất định về phân giải tên DNS, phục vụ cho các mục đích như giao tiếp nội bộ, xác thực chứng chỉ và tự động phát hiện nút (node).

Các máy khách (client) truy cập vào các nút của cụm OpenShift Container Platform thông qua mạng bare-metal (mạng vật lý). Quản trị viên mạng cần cấu hình một tên miền phụ (subdomain) hoặc vùng phụ (subzone), trong đó phần mở rộng tên chuẩn (canonical name extension) chính là tên của cụm.

```
<cluster_name>.<base_domain>
```

Ví dụ: `test-cluster.example.com`

OpenShift Container Platform bao gồm chức năng sử dụng thông tin thành viên cụm (cluster membership) để tạo các bản ghi A/AAAA. Chức năng này giúp phân giải tên các nút (node) thành địa chỉ IP tương ứng. Sau khi các nút được đăng ký với API, cụm có thể phổ biến thông tin về các nút mà không cần sử dụng CoreDNS-mDNS. Điều này giúp loại bỏ lưu lượng mạng phát sinh từ giao thức multicast DNS.

Để hoạt động chính xác, CoreDNS yêu cầu các kết nối TCP và UDP tới máy chủ DNS thượng nguồn (upstream DNS server). Hãy đảm bảo rằng máy chủ DNS thượng nguồn có thể tiếp nhận cả kết nối TCP lẫn UDP từ các nút trong cụm OpenShift Container Platform.

Trong các triển khai OpenShift Container Platform, việc phân giải tên DNS là cần thiết cho các thành phần sau:

* Kubernetes API
* OpenShift Container Platform application wildcard ingress API

Các bản ghi A/AAAA được sử dụng để phân giải tên, trong khi các bản ghi PTR được sử dụng để phân giải tên ngược (reverse name resolution). Red Hat Enterprise Linux CoreOS (RHCOS) sử dụng các bản ghi ngược hoặc DHCP để thiết lập tên máy chủ (hostname) cho tất cả các nút.

Quy trình cài đặt do trình cài đặt tự động cung cấp (installer-provisioned installation) bao gồm chức năng sử dụng thông tin thành viên cụm để tạo các bản ghi A/AAAA. Chức năng này giúp phân giải tên các nút thành địa chỉ IP của chúng. Trong mỗi bản ghi, `<cluster_name>` là tên cụm và `<base_domain>` là tên miền cơ sở mà bạn chỉ định trong tệp install-config.yaml. Một bản ghi DNS hoàn chỉnh sẽ có dạng: `<component>.<cluster_name>.<base_domain>.`.

**Bảng 2.4. Các bản ghi DNS bắt buộc**

| Thành phần     | Bản ghi                                | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|----------------|----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kubernetes API | `api.<cluster_name>.<base_domain>.`    | Bản ghi A/AAAA và bản ghi PTR dùng để xác định bộ cân bằng tải API. Các bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm lẫn tất cả các nút bên trong cụm đó.                                                                                                                                                                                                                                                                                                                                                                                                             |
| Routes         | `*.apps.<cluster_name>.<base_domain>.` | Bản ghi A/AAAA dạng ký tự đại diện (wildcard) trỏ đến bộ cân bằng tải ingress của ứng dụng. Bộ cân bằng tải ingress này hướng lưu lượng truy cập đến các nút (node) đang chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller này chạy trên các nút worker. Các bản ghi này phải có thể được phân giải bởi cả các máy khách bên ngoài cụm lẫn tất cả các nút nằm bên trong cụm. Ví dụ: `bash-openshift-console.apps.<cluster_name>.<base_domain>` được sử dụng làm tuyến đường (route) dạng ký tự đại diện dẫn đến bảng điều khiển (console) của OpenShift Container Platform. |

> **Mẹo:** Bạn có thể sử dụng lệnh dig để kiểm tra quá trình phân giải DNS.

#### II.1.4. Tạo đối tượng manifest cho cầu nối br-ex tùy chỉnh
Bạn cần tạo một đối tượng manifest để sửa đổi cấu hình mạng của cụm (cluster) sau khi cài đặt. Đối tượng manifest này cấu hình cầu nối `br-ex`, vốn đảm nhiệm việc quản lý kết nối mạng bên ngoài cho cụm.

Để biết hướng dẫn về cách tạo manifest này, hãy xem phần "Tạo tệp manifest cho cầu nối br-ex tùy chỉnh" thuộc tài liệu **Cài đặt trên phần cứng vật lý**.

### II.2. Cài đặt cụm OpenShift hai nút với cơ chế cách ly (fencing)
Đối với nền tảng container có tính sẵn sàng cao và chiếm ít tài nguyên tại các vị trí biên (edge sites) hoặc các môi trường hạn chế về tài nguyên, bạn có thể triển khai cụm OpenShift hai nút với cơ chế cách ly (Two-Node Fencing - TNF). Cơ chế này bảo vệ các ứng dụng và dữ liệu của bạn khỏi tình trạng "split-brain" (xung đột trạng thái do mất kết nối) bằng cách cách ly an toàn một nút khi xảy ra lỗi giao tiếp. Để phù hợp với môi trường hiện có, bạn có thể triển khai cụm này thông qua các phương thức hạ tầng tự động, thủ công hoặc dựa trên tác nhân (agent-based).

**Hạ tầng tự động (do trình cài đặt cung cấp - installer-provisioned)**  
Chương trình cài đặt cụm sẽ kiểm soát mọi khía cạnh của quá trình triển khai, bao gồm việc cung cấp hạ tầng đám mây hoặc nền tảng ảo hóa, cấu hình tài nguyên mạng và khởi tạo các nút.

**Hạ tầng thủ công (do người dùng cung cấp - user-provisioned)**  
Bạn tự chuẩn bị và quản lý các image hệ điều hành, hệ thống mạng, lưu trữ và bộ cân bằng tải trước khi bắt đầu triển khai OpenShift. Phương thức này mang lại khả năng kiểm soát tối đa đối với các môi trường doanh nghiệp tùy chỉnh.

**Hạ tầng dựa trên tác nhân (agent-based)**  
Bạn sử dụng một image ISO có khả năng khởi động (bootable ISO), trong đó chứa một tác nhân (agent) giúp tự động hóa quá trình triển khai trên hạ tầng máy chủ vật lý (bare metal) hoặc hạ tầng đã được chuẩn bị sẵn. Cách tiếp cận này kết hợp sự linh hoạt của thiết lập thủ công với sự tiện lợi của quy trình tự động, rất phù hợp cho các môi trường không có kết nối mạng (disconnected environments).

> **Quan trọng:** Hãy cấu hình quyền truy cập vào các nút trong quá trình cài đặt, chẳng hạn như bằng cách thêm các khóa SSH vào tệp `install-config.yaml`. Các cụm sử dụng cơ chế cách ly (TNF) có thể đòi hỏi sự can thiệp thủ công trong một số tình huống lỗi cụ thể mà chỉ có thể giải quyết thông qua việc truy cập trực tiếp vào nút đó.

#### II.2.1. Tệp install-config.yaml mẫu cho cụm gồm hai nút với cơ chế cách ly (fencing) trên hạ tầng do trình cài đặt cung cấp
Bạn có thể sử dụng tệp cấu hình install-config.yaml dưới đây làm mẫu để triển khai cụm OpenShift gồm hai nút với cơ chế cách ly (fencing) thông qua phương thức hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure):

> **Lưu ý:** Hãy sao lưu etcd trước khi tiếp tục để đảm bảo rằng bạn có thể khôi phục cụm nếu xảy ra sự cố.

Cấu hình install-config.yaml mẫu

```yaml
apiVersion: v1
baseDomain: example.com
compute:
- name: worker
  replicas: 0
controlPlane:
  name: master
  replicas: 2
  fencing:
    credentials:
      - hostname: <control_0_hostname>
        address: https://<redfish-api-url>
        username: <username>
        password: <password>
        certificateVerification: Disabled
      - hostname: <control_1_hostname>
        address: https://<redfish-api-url>
        username: <username>
        password: <password>
        certificateVerification: Enabled
metadata:
  name: <cluster_name>
platform:
  baremetal:
    apiVIPs:
      - <api_ip>
    ingressVIPs:
      - <wildcard_ip>
    hosts:
      - name: <control_0_hostname>
        role: master
        bmc:
          address: <bmc_address>
          username: <bmc_username>
          password: <bmc_password>
        bootMACAddress: <boot_mac>
      - name: <control_1_hostname>
        role: master
        bmc:
          address: <bmc_address>
          username: <bmc_username>
          password: <bmc_password>
        bootMACAddress: <boot_mac>
pullSecret: '<pull_secret>'
sshKey: '<ssh_public_key>'
```

* `compute.replicas`: Đặt trường này thành 0 vì cụm OpenShift Container Platform gồm hai nút có sử dụng cơ chế cách ly nút (fencing) sẽ không bao gồm các nút worker.
* `controlPlane.replicas`: Đặt trường này thành 2 đối với mô hình triển khai cụm OpenShift Container Platform gồm hai nút có sử dụng cơ chế cách ly nút (fencing).
* `fencing.credentials.hostname`: Cung cấp thông tin xác thực cho Baseboard Management Console (BMC) của từng nút trong mặt phẳng điều khiển (control plane). Các thông tin xác thực này là bắt buộc để thực hiện cách ly nút (node fencing) và ngăn ngừa tình trạng "split-brain" (xung đột phân tách hệ thống).
* `fencing.credentials.certificateVerification`: Đặt trường này thành Disabled nếu URL Redfish của bạn sử dụng chứng chỉ tự ký (self-signed certificate) — trường hợp thường thấy đối với các điểm cuối (endpoint) được lưu trữ nội bộ. Đặt trường này thành Enabled nếu URL sử dụng chứng chỉ hợp lệ được ký bởi cơ quan cấp chứng chỉ (CA).
* `metadata.name`: Tên cụm, được sử dụng làm tiền tố cho tên máy chủ (hostname) và các bản ghi DNS.
* `platform.baremetal.apiVIPs` và `platform.baremetal.ingressVIPs`: Các địa chỉ IP ảo (Virtual IP) dành cho các điểm cuối API và Ingress. Hãy đảm bảo rằng tất cả các nút và các máy khách bên ngoài đều có thể truy cập được các địa chỉ IP này.
* `pullSecret`: Chứa thông tin xác thực cần thiết để tải (pull) các hình ảnh container (container image) cho các thành phần của cụm.
* `sshKey`: Khóa công khai SSH (SSH public key) dùng để truy cập vào các nút trong cụm sau khi hoàn tất quá trình cài đặt.

#### II.2.2. Tệp install-config.yaml mẫu cho cụm gồm hai nút trên cơ sở hạ tầng do người dùng cung cấp (UPI) với cơ chế cách ly nút (fencing)
Bạn có thể sử dụng tệp cấu hình install-config.yaml dưới đây làm mẫu để triển khai cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly nút (fencing), thông qua phương pháp sử dụng cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure):

> **Lưu ý:** Hãy sao lưu etcd trước khi tiếp tục để đảm bảo rằng bạn có thể khôi phục cụm nếu xảy ra sự cố.

Cấu hình install-config.yaml mẫu

```yaml
apiVersion: v1
baseDomain: example.com
compute:
- name: worker
  replicas: 0
controlPlane:
  name: master
  replicas: 2
  fencing:
    credentials:
      - hostname: <control_0_hostname>
        address: https://<redfish-api-url>
        username: <username>
        password: <password>
      - hostname: <control_1_hostname>
        address: https://<redfish-api-url>
        username: <username>
        password: <password>
metadata:
  name: <cluster_name>
platform:
  none: {}
pullSecret: '<pull_secret>'
sshKey: '<ssh_public_key>'
```

* `compute.replicas`: Đặt trường này thành 0 vì cụm OpenShift Container Platform gồm hai nút có sử dụng cơ chế cách ly (fencing) sẽ không bao gồm các nút worker.
* `controlPlane.replicas`: Đặt trường này thành 2 đối với mô hình triển khai hai nút có sử dụng cơ chế cách ly.
* `fencing.credentials.hostname`: Cung cấp thông tin xác thực BMC cho từng nút trong mặt phẳng điều khiển (control plane).
* `metadata.name`: Tên cụm được sử dụng làm tiền tố cho tên máy chủ (hostname) và các bản ghi DNS.
* `platform.none`: Đặt nền tảng (platform) thành "none" đối với các mô hình triển khai trên cơ sở hạ tầng do người dùng tự cung cấp (user-provisioned infrastructure). Các máy chủ vật lý (bare-metal) được chuẩn bị sẵn bên ngoài chương trình cài đặt.
* `pullSecret`: Chứa thông tin xác thực cần thiết để tải (pull) các hình ảnh container (container image) cho các thành phần của cụm.
* `sshKey`: Khóa công khai SSH dùng để truy cập vào các nút của cụm sau khi cài đặt.

#### II.2.3. Tệp install-config.yaml mẫu cho cụm hai nút có cơ chế cách ly (fencing) dành cho trình cài đặt dựa trên tác nhân (Agent-based Installer)
Bạn có thể sử dụng tệp cấu hình install-config.yaml dưới đây làm mẫu để triển khai cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly (fencing), thông qua phương thức cài đặt dựa trên tác nhân (Agent-based Installer).

Xem tệp cấu hình install-config.yaml mẫu dành cho môi trường bare-metal (phần cứng vật lý) dưới đây:

```yaml
apiVersion: v1
baseDomain: example.com
controlPlane:
  name: master
  replicas: 2
  fencing:
    credentials:
    - hostname: master-0
      address: redfish+https://<bmc_ip_0>:<bmc_port>/redfish/v1/Systems/<system_id_0>
      username: <bmc_username>
      password: <bmc_password>
      certificateVerification: Disabled
    - hostname: master-1
      address: redfish+https://<bmc_ip_1>:<bmc_port>/redfish/v1/Systems/<system_id_1>
      username: <bmc_username>
      password: <bmc_password>
      certificateVerification: Disabled
compute:
- name: worker
  replicas: 0
metadata:
  name: <cluster_name>
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  networkType: OVNKubernetes
  machineNetwork:
  - cidr: <machine_network_cidr>
  serviceNetwork:
  - 172.30.0.0/16
platform:
  baremetal:
    apiVIPs:
    - <api_vip>
    ingressVIPs:
    - <ingress_vip>
pullSecret: '<pull_secret>'
sshKey: '<ssh_public_key>'
```

* `platform.baremetal.apiVIPs và ingressVIPs`: Chỉ định các địa chỉ IP ảo (virtual IP) cho các điểm cuối (endpoint) API và Ingress. Thông số này là bắt buộc đối với nền tảng bare-metal; không áp dụng cho tùy chọn `none`.

Đối với các trường khác dành riêng cho môi trường bare metal (phần cứng vật lý), hãy xem phần "Các tham số cấu hình cài đặt cho trình cài đặt dựa trên tác nhân" (Installation configuration parameters for the Agent-based Installer).

Tệp cấu hình `install-config.yaml` mẫu dưới đây áp dụng cho thuộc tính `platform` với giá trị là `none`.

Bạn phải cung cấp cơ sở hạ tầng cho việc phân giải tên DNS và cân bằng tải.

```yaml
apiVersion: v1
baseDomain: example.com
controlPlane:
  name: master
  replicas: 2
  fencing:
    credentials:
    - hostname: master-0
      address: redfish+https://<bmc_ip_0>:<bmc_port>/redfish/v1/Systems/<system_id_0>
      username: <bmc_username>
      password: <bmc_password>
      certificateVerification: Disabled
    - hostname: master-1
      address: redfish+https://<bmc_ip_1>:<bmc_port>/redfish/v1/Systems/<system_id_1>
      username: <bmc_username>
      password: <bmc_password>
      certificateVerification: Disabled
compute:
- name: worker
  replicas: 0
metadata:
  name: <cluster_name>
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  networkType: OVNKubernetes
  machineNetwork:
  - cidr: <machine_network_cidr>
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
pullSecret: '<pull_secret>'
sshKey: '<ssh_public_key>'
```

* `controlPlane.replicas`: Phải là 2 đối với cụm OpenShift Container Platform gồm hai nút có sử dụng cơ chế cách ly (fencing - TNF).
* `compute[0].replicas`: Phải là 0. Cụm OpenShift Container Platform gồm hai nút có sử dụng cơ chế cách ly không hỗ trợ các nút tính toán (compute node).
* `controlPlane.fencing.credentials`: Yêu cầu chính xác 2 mục, mỗi mục tương ứng với một nút trong mặt phẳng điều khiển (control plane node).
* `fencing.credentials[].hostname`: Tên máy chủ (hostname) của nút trong mặt phẳng điều khiển. Phải là duy nhất trong số các thông tin xác thực (credentials).
* `fencing.credentials[].address`: URL của Redfish BMC. Phải sử dụng lược đồ (scheme) `redfish+https://` (ví dụ: `redfish+https://192.168.1.10:443/redfish/v1/Systems/1`). Các địa chỉ IPMI không được hỗ trợ. Các lược đồ Redfish dành riêng cho từng nhà cung cấp, chẳng hạn như `idrac-redfish+https://` và `ilo5-redfish+https://`, cũng được chấp nhận.
* `fencing.credentials[].username`: Tên người dùng BMC cho nút đó.
* `fencing.credentials[].password`: Mật khẩu BMC cho nút đó.
* `fencing.credentials[].certificateVerification`: Tùy chọn. Đặt là `Disabled` nếu BMC của bạn sử dụng chứng chỉ tự ký (thường gặp đối với các điểm cuối được lưu trữ nội bộ). Đặt là `Enabled` (mặc định) đối với các BMC sử dụng chứng chỉ hợp lệ được ký bởi cơ quan cấp chứng chỉ (CA).

#### II.2.4. Tệp agent-config.yaml mẫu cho cụm gồm hai nút có cơ chế cách ly (fencing) dành cho trình cài đặt dựa trên tác nhân (Agent-based Installer)
Bạn có thể sử dụng tệp cấu hình agent-config.yaml dưới đây làm mẫu để triển khai cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly (fencing) thông qua phương thức cài đặt dựa trên tác nhân (Agent-based Installer):

Xem tệp agent-config.yaml mẫu với cấu hình tối thiểu dưới đây:

```yaml
apiVersion: v1beta1
metadata:
  name: <cluster_name>
rendezvousIP: <rendezvous_ip>
```

* `rendezvousIP`: Chỉ định địa chỉ IP của nút đóng vai trò là máy chủ điều phối (rendezvous host) trong quá trình cài đặt. Nút này chạy dịch vụ hỗ trợ (Assisted Service) và điều phối việc cài đặt các nút còn lại.

Xem tệp agent-config.yaml mẫu dưới đây, bao gồm cấu hình máy chủ (host) và thiết lập mạng tĩnh (static networking):

> **Lưu ý:** Các giá trị `hostname` trong tệp `agent-config.yaml` phải khớp với các giá trị `hostname` trong phần thông tin xác thực cách ly (fencing credentials) của tệp `install-config.yaml`.

```yaml
apiVersion: v1beta1
metadata:
  name: <cluster_name>
rendezvousIP: <rendezvous_ip>
additionalNTPSources:
- 0.rhel.pool.ntp.org
- 1.rhel.pool.ntp.org
hosts:
- hostname: master-0
  role: master
  interfaces:
  - name: <nic_name>
    macAddress: <mac_address_0>
  networkConfig:
    interfaces:
    - name: <nic_name>
      type: ethernet
      state: up
      ipv4:
        enabled: true
        dhcp: false
        address:
        - ip: <master_0_ip>
          prefix-length: <prefix_length>
      ipv6:
        enabled: false
    dns-resolver:
      config:
        server:
        - <dns_server>
    routes:
      config:
      - destination: 0.0.0.0/0
        next-hop-address: <gateway>
        next-hop-interface: <nic_name>
        table-id: 254
- hostname: master-1
  role: master
  interfaces:
  - name: <nic_name>
    macAddress: <mac_address_1>
  networkConfig:
    interfaces:
    - name: <nic_name>
      type: ethernet
      state: up
      ipv4:
        enabled: true
        dhcp: false
        address:
        - ip: <master_1_ip>
          prefix-length: <prefix_length>
      ipv6:
        enabled: false
    dns-resolver:
      config:
        server:
        - <dns_server>
    routes:
      config:
      - destination: 0.0.0.0/0
        next-hop-address: <gateway>
        next-hop-interface: <nic_name>
        table-id: 254
```

### II.3. Vận hành cụm OpenShift hai nút ở trạng thái suy giảm (degraded) với cơ chế cách ly (fencing)
Một cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly (TNF) sẽ chuyển sang trạng thái suy giảm (degraded) khi một trong hai nút thuộc mặt phẳng điều khiển (control plane) không còn khả dụng. Nút còn lại sẽ tiếp tục duy trì mặt phẳng điều khiển đang hoạt động. Tuy nhiên, cụm này sẽ mất khả năng dự phòng theo cơ chế sẵn sàng cao (HA) cho đến khi nút bị lỗi được khôi phục.

Trạng thái suy giảm là một trạng thái được thiết kế có chủ đích chứ không phải là một lỗi hệ thống. Ở trạng thái này, cụm vẫn duy trì được chức năng và các dịch vụ cốt lõi vẫn tiếp tục hoạt động. Chỉ một số tính năng cụ thể yêu cầu bắt buộc phải có sự dự phòng giữa hai nút mới tạm thời không khả dụng.

> **Quan trọng:** Một cụm ở trạng thái suy giảm hoàn toàn không có khả năng chịu lỗi. Nếu nút còn lại cũng gặp sự cố, toàn bộ cụm sẽ ngừng hoạt động. Hãy khôi phục nút thứ hai càng sớm càng tốt. Trạng thái suy giảm chỉ là một khoảng thời gian tạm thời để phục hồi, không phải là trạng thái vận hành ổn định lâu dài.

#### II.3.1. Các nguyên nhân gây suy giảm trạng thái cụm TNF
Một cụm OpenShift gồm hai nút có sử dụng cơ chế cách ly (TNF - Two-Node Fencing) sẽ rơi vào trạng thái suy giảm (degraded) khi một nút gặp sự cố hoặc mất kết nối với cụm. Việc xác định nguyên nhân cụ thể gây ra tình trạng này là rất quan trọng, vì nó quyết định liệu cụm có thể tự động khôi phục hay cần sự can thiệp thủ công (như thực hiện cách ly nút hoặc sửa chữa phần cứng) để ngăn ngừa hỏng dữ liệu và duy trì tính khả dụng của dịch vụ.

Một số nguyên nhân phổ biến dẫn đến tình trạng suy giảm trạng thái cụm bao gồm:

**Tắt máy hoặc ngắt nguồn theo quy trình (Graceful shutdown)**  
Việc tắt máy theo quy trình xảy ra khi quản trị viên chủ động khởi tạo lệnh tắt hệ thống, cho phép hệ điều hành gửi tín hiệu dừng đến các tiến trình và ngắt kết nối (unmount) các hệ thống tệp một cách an toàn. Hành động có chủ đích này đảm bảo dữ liệu vẫn nhất quán và nút được đánh dấu là "Offline" (ngoại tuyến) trong cụm trước khi phần cứng ngừng hoạt động.

**Sự cố phần cứng hoặc mất nguồn đột ngột**  
Các lỗi phần cứng bất ngờ, chẳng hạn như hỏng bộ điều khiển ổ đĩa hoặc mất điện đột ngột, khiến dịch vụ ngừng hoạt động ngay lập tức mà không có cảnh báo. Khác với việc tắt máy theo quy trình, các sự kiện này không cho phép hệ thống kịp xử lý hoặc dọn dẹp các tiến trình đang chạy; do đó, khi có điện trở lại, hệ thống thường cần thực hiện kiểm tra tính nhất quán hoặc quy trình khôi phục tự động.

**Chia tách mạng (Network partition) hoặc mất kết nối**  
Tình trạng chia tách mạng xảy ra khi lỗi trong hạ tầng chuyển mạch (switching fabric) hoặc hệ thống cáp khiến các nút không thể giao tiếp với nhau, mặc dù bản thân các nút vẫn đang được cấp nguồn. Trong cụm gồm hai nút, kịch bản này (thường được gọi là "split-brain" - phân tách hệ thống) đặc biệt nguy hiểm, vì mỗi nút có thể hiểu lầm rằng nút kia đã gặp sự cố và cố gắng giành quyền kiểm soát độc quyền đối với các tài nguyên dùng chung.

**Lỗi nghiêm trọng của nhân hệ điều hành (Kernel panic)**  
Kernel panic xảy ra khi nhân hệ điều hành gặp lỗi nội bộ nghiêm trọng — chẳng hạn như hỏng bộ nhớ hoặc xung đột trình điều khiển (driver) không thể khắc phục — khiến hệ thống không thể tiếp tục hoạt động một cách an toàn. Để bảo vệ tính toàn vẹn của dữ liệu, nhân hệ điều hành sẽ ngay lập tức dừng mọi hoạt động, khiến nút đó rơi vào trạng thái "đóng băng" cho đến khi được khởi động lại cứng (hard reboot).

**Nút bị treo (Node hang)**  
Tình trạng nút bị treo xảy ra khi phần cứng vẫn được cấp nguồn nhưng hệ thống không còn phản hồi với bất kỳ yêu cầu bên ngoài nào, bao gồm cả các lệnh ping hoặc kết nối SSH. Trạng thái này thường xuất phát từ các lỗi khóa chết (deadlock) trong phần mềm hoặc một vòng lặp vô hạn trong tiến trình có mức ưu tiên cao, khiến các thành phần khác của hệ thống không thể truy cập được tài nguyên CPU.

**Lỗi Kubelet hoặc cạn kiệt tài nguyên**  
Một node sẽ trở nên không ổn định nếu Kubelet gặp sự cố hoặc nếu node đó rơi vào tình trạng cạn kiệt tài nguyên nghiêm trọng, chẳng hạn như hết bộ nhớ RAM (OOM) hoặc hết dung lượng ổ đĩa. Khi Kubelet không thể gửi tín hiệu "nhịp tim" (heartbeat) tới control plane, cụm (cluster) sẽ đánh dấu node đó là "NotReady" (không sẵn sàng) và cố gắng di chuyển các khối lượng công việc (workload) đang chạy trên node đó sang nơi khác.

#### II.3.2. Trình tự xử lý lỗi nút trong cụm TNF
Một cụm OpenShift gồm hai nút với cơ chế cách ly nút (TNF - Two-Node Fencing) sẽ chuyển sang trạng thái suy giảm hiệu năng (degraded) khi một trong hai nút thuộc mặt phẳng điều khiển (control plane) không còn khả dụng. Mặt phẳng điều khiển vẫn tiếp tục hoạt động trên nút còn lại, cho phép cụm duy trì chức năng trong các giới hạn nhất định.

Quy trình xử lý lỗi tự động diễn ra theo các bước sau:

* Phát hiện lỗi: Corosync phát hiện sự cố.

Hệ thống quản lý cụm ghi nhận rằng các tín hiệu "nhịp tim" (heartbeat) từ nút đối tác đã ngừng truyền. 

* Cách ly: Thực thi cơ chế STONITH (Shoot The Other Node In The Head).

Nút còn lại sử dụng API Redfish để kết nối với bộ điều khiển quản lý bo mạch chủ (BMC) của nút bị lỗi. Nó gửi lệnh tắt nguồn hoặc khởi động lại để đảm bảo nút bị lỗi được cách ly hoàn toàn. Điều này ngăn chặn tình trạng "split-brain" (phân mảnh hệ thống), nơi nút bị lỗi vẫn tiếp tục chạy các container cục bộ trong khi OpenShift Container Platform cố gắng lập lịch lại các khối lượng công việc (workload) đó sang các nút khỏe mạnh. Nhờ vậy, các pod có trạng thái (stateful pods), dịch vụ định tuyến và các volume lưu trữ luôn đảm bảo chỉ có một chủ sở hữu hợp lệ duy nhất. 

Nếu thao tác cách ly STONITH thất bại, nút còn lại sẽ không thể tiếp quản quyền kiểm soát các tài nguyên của cụm một cách an toàn. Trong trường hợp này, quản trị viên phải thực hiện tắt nguồn thủ công phần cứng bị lỗi thì cụm mới có thể khôi phục. 

* Điều chỉnh Quorum (số phiếu tối thiểu để đạt đồng thuận): etcd chuyển sang chế độ vận hành với một thành viên duy nhất. 

etcd Operator hạ cấp thành viên etcd trên nút bị lỗi thành một "learner" (thành viên học hỏi, không có quyền biểu quyết). Thành viên còn lại trở thành thực thể duy nhất có quyền biểu quyết, qua đó duy trì quorum. etcd tiếp tục vận hành với một thành viên biểu quyết duy nhất, đảm bảo quorum hợp lệ để Kubernetes API vẫn có thể truy cập được. 

* Lập lịch: Trạng thái nút chuyển sang "NotReady" (Không sẵn sàng). 

Khi một nút gặp sự cố, Kubernetes cập nhật trạng thái của nó, khiến các khối lượng công việc bị ảnh hưởng mất đi tính dự phòng. Trong một cụm đang hoạt động, các pod thuộc DaemonSet vẫn ở trạng thái "Running" (Đang chạy), trong khi các pod thuộc Deployment hiển thị trạng thái "Terminating" (Đang chấm dứt). Tuy nhiên, do kubelet trên nút bị lỗi không thể phản hồi để thực hiện xóa, các pod Deployment này về mặt kỹ thuật vẫn nằm trong giai đoạn "Running" trên API. Chúng chỉ được lập lịch lại sang các nút khác nếu các nút đó còn đủ tài nguyên và tuân thủ các quy tắc chống phân bổ (anti-affinity rules).

#### II.3.3. Hành vi của Pacemaker và cơ chế cách ly (fencing) trong quá trình vận hành ở trạng thái suy giảm
Trong quá trình vận hành ở trạng thái suy giảm (degraded operation), trình quản lý cụm Pacemaker chuyển đổi từ mô hình điều phối phân tán sang cấu trúc thực thi cục bộ trên nút (node) còn hoạt động.

Các hoạt động của cụm ở trạng thái suy giảm bao gồm những đặc điểm cấu trúc sau:

* Nút còn hoạt động vẫn ở trạng thái ONLINE và tiếp tục quản lý etcd, kubelet, cũng như các thiết bị cách ly STONITH (viết tắt của "Shoot The Other Node In The Head" – tạm dịch: "vô hiệu hóa nút kia"). 
* Nút bị lỗi được ghi nhận là OFFLINE hoặc UNCLEAN OFFLINE, tùy thuộc vào việc nút đó có được tắt đúng quy trình hay không. 
* Các thiết bị cách ly vẫn được kích hoạt. Thiết bị STONITH dành cho nút bị lỗi vẫn khả dụng trên nút còn hoạt động. Tuy nhiên, thiết bị STONITH dành cho chính nút còn hoạt động đó lại không thể sử dụng được, vì nút lẽ ra có nhiệm vụ kích hoạt nó hiện đang ở trạng thái ngoại tuyến (offline). 
* Pacemaker không cố gắng khởi động lại các tài nguyên trên nút bị lỗi, cũng như không di chuyển (migrate) các tài nguyên sang nút đó.

> **Lưu ý quan trọng:** Cơ chế bảo vệ bằng cách ly lẫn nhau (mutual fencing) sẽ không khả dụng khi hệ thống vận hành ở trạng thái suy giảm. Các thao tác cách ly không thể thực hiện đối với nút còn hoạt động, do các kênh giao tiếp và đường dẫn thực thi từ nút đối tác hiện đã ngừng hoạt động.

#### II.3.4. Tự động gắn "taint" (dấu hiệu) cho node trong cụm OpenShift hai node có cơ chế fencing
Trong cụm OpenShift Container Platform gồm hai node với cơ chế fencing (TNF), các ứng dụng của bạn có thể phục hồi nhanh chóng sau sự cố node mà không cần sự can thiệp thủ công, nhờ vào cơ chế tự động gắn "taint" cho node.

Khi một node gặp sự cố, cơ chế tự động gắn "taint" sẽ ngay lập tức di chuyển (evict) các khối lượng công việc (workload) sang node còn lại, giúp giảm đáng kể thời gian chuyển đổi dự phòng (failover).

Cơ chế này được tích hợp sẵn trong quá trình triển khai, không yêu cầu cấu hình, không thể vô hiệu hóa và chỉ áp dụng cho các node thuộc mặt phẳng điều khiển (control plane) trong cấu trúc liên kết hai node.

Bạn không cần thực hiện bất kỳ thao tác nào để cơ chế tự động này vận hành. Tuy nhiên, bạn có thể quan sát hoạt động của nó trong một sự kiện fencing. Bạn có thể xem các "taint" và chú thích (annotation) được áp dụng ngay sau khi một node bị đưa vào trạng thái fencing bằng cách chạy lệnh sau:

```bash
$ oc get node <node_name>
```

Các Persistent Volume (ổ đĩa bền vững) và các pod thuộc Deployment sẽ đủ điều kiện để được lập lịch lại (rescheduling) sớm hơn nhiều so với thông thường.

Bạn có thể kiểm tra nhật ký (log) cục bộ trên mỗi nút control plane bằng cách sử dụng công cụ `journalctl` và lọc theo các thẻ sau:

* `taint-fenced-node`
* `untaint-fenced-node`
* `tnf-taint-alert`
* `tnf-untaint-alert`

#### II.3.5. Độ ổn định của các Operator trong cụm (cluster Operator) khi hệ thống ở trạng thái suy giảm hiệu năng
Trong một cụm OpenShift gồm hai nút có cơ chế cách ly nút (fencing), các Operator trong cụm (cluster Operator) vẫn duy trì sự ổn định của mặt phẳng điều khiển (control plane) và khả năng truy cập API ngay cả khi hệ thống rơi vào trạng thái suy giảm hiệu năng (degraded state).

Khi hệ thống ở trạng thái suy giảm hiệu năng, các trạng thái (conditions) của các Operator trong cụm sẽ biểu hiện như sau:

Hầu hết các Operator vẫn duy trì trạng thái Available=True, đảm bảo các chức năng cốt lõi của API vẫn có thể truy cập được. 

Các Operator sau đây sẽ chuyển sang trạng thái Degraded=True do chỉ còn một trong hai thực thể thuộc mặt phẳng điều khiển (control plane) hoạt động:

* `etcd`
* `kube-apiserver`
* `kube-controller-manager`
* `kube-scheduler`
* `machine-config`

Một số Operator cụ thể có thể hiển thị trạng thái `Progressing=True` trong quá trình thực hiện các thao tác đồng bộ hóa (reconciliation) đòi hỏi dữ liệu từ cả hai nút hạ tầng. Trạng thái này sẽ tự giải quyết khi nút thứ hai quay trở lại trạng thái trực tuyến (online).

> **Lưu ý:** Không được phép xảy ra tình trạng các Operator gặp lỗi bất ngờ hoặc lỗi dây chuyền (cascading failures) khi hệ thống đang ở trạng thái suy giảm hiệu năng. Nếu điều này xảy ra, hãy xem xét vấn đề như một lỗi tiềm ẩn (bug) cần được điều tra.

#### II.3.6. Các khả năng trong quá trình vận hành cụm TNF ở trạng thái suy giảm
Khi một cụm OpenShift gồm hai nút có sử dụng cơ chế cách ly (TNF) vận hành ở trạng thái suy giảm, một số khả năng của cụm vẫn có thể được sử dụng.

**Bảng 2.5. Các khả năng của cụm trong quá trình vận hành ở trạng thái suy giảm hiệu năng**

| Năng lực                                                                   | Có sẵn |
|----------------------------------------------------------------------------|--------|
| Kubernetes API server (đọc và ghi)                                         | Có     |
| Các khối lượng công việc trên nút còn lại                                  | Có     |
| Lên lịch các khối lượng công việc mới cho nút còn hoạt động                | Có     |
| etcd (cơ chế đa số với một thành viên duy nhất)                            | Có     |
| Giám sát và cảnh báo cụm                                                   | Có     |
| Ingress (một điểm cuối duy nhất)                                           | Có     |
| Các chứng chỉ hiện có                                                      | Có     |
| Static pod khởi động lại bằng cách sử dụng cấu hình hiện có                | Có     |
| tính dự phòng của etcd                                                     | Không  |
| Cách ly nút còn lại                                                        | Không  |
| Nâng cấp cụm                                                               | Không  |
| Luân chuyển CA cho etcd                                                    | Không  |
| Các thay đổi đối với đối tượng `MachineConfig` yêu cầu khởi động lại nút   | Không  |
| Các khối lượng công việc hoặc dữ liệu lưu trữ gắn độc quyền với nút bị lỗi | Không  |

#### II.3.7. Các thao tác bị cấm khi cụm OpenShift hai nút với cơ chế cách ly (TNF) ở trạng thái suy giảm
Bạn không được thực hiện một số thao tác quản trị nhất định khi cụm OpenShift hai nút sử dụng cơ chế cách ly (TNF - Two-Node Fencing) đang ở trạng thái suy giảm (degraded). Việc cố gắng thực hiện các thao tác này có thể khiến cụm rơi vào trạng thái khó phục hồi hơn so với trạng thái suy giảm ban đầu.

Không thực hiện các thao tác sau đây khi cụm đang ở trạng thái suy giảm:

**Nâng cấp cụm**  
Không bắt đầu quá trình nâng cấp cụm khi cụm đang ở trạng thái suy giảm. Quá trình nâng cấp đòi hỏi phải triển khai các cấu hình mới cho cả hai nút thuộc mặt phẳng điều khiển (control plane). Khi một nút không khả dụng, việc triển khai cấu hình sẽ không thể hoàn tất. Cụm sẽ bị kẹt ở trạng thái nâng cấp dở dang, gây khó khăn cho việc phục hồi hơn so với trạng thái suy giảm ban đầu. 

Không bắt đầu bất kỳ quá trình nâng cấp nào trước khi khôi phục nút thứ hai. Nếu không thể khôi phục nút thứ hai, hãy thay thế nút đó.

**Thay đổi (xoay vòng) cơ quan cấp chứng chỉ (CA) cho etcd**  
Không bắt đầu quá trình xoay vòng CA cho etcd khi cụm đang ở trạng thái suy giảm. Quá trình này đòi hỏi phải phân phối các gói tin cậy (trust bundle) mới đến cả hai nút thuộc mặt phẳng điều khiển và cập nhật phiên bản static pod mới trên mỗi nút. Khi một nút ngừng hoạt động, việc phân phối các gói tin cậy không thể hoàn tất và phiên bản static pod cũng không thể cập nhật. 

Kết quả là, một CA ký (signer CA) mới có thể được tạo ra, nhưng các chứng chỉ phụ thuộc (như chứng chỉ peer, serving và client) lại không được tái tạo bằng CA mới này. Cụm sẽ rơi vào tình trạng xoay vòng dở dang, không hoàn chỉnh.

> **Cảnh báo:** Không xóa secret chứa CA ký (signer secret) của etcd khi cụm đang ở trạng thái suy giảm. Hành động này sẽ kích hoạt việc tạo mới CA, nhưng các chứng chỉ phụ thuộc sẽ không thể được tái tạo. Hệ quả là kube-apiserver sẽ mất sự tin tưởng vào etcd, dẫn đến lỗi nghiêm trọng tại mặt phẳng điều khiển mà không thể phục hồi được.

**Các thao tác liên quan đến hiệu lực chứng chỉ**  
Việc tái tạo các chứng chỉ riêng lẻ (leaf certificate) — chẳng hạn như chứng chỉ phục vụ (serving certificate) của etcd cho nút (node) còn hoạt động — vẫn có thể thực hiện được trong trạng thái vận hành suy giảm (degraded operation), vì quy trình này sử dụng cơ chế ký chứng chỉ hiện có thay vì tạo mới. 

Các chứng chỉ etcd hiện tại vẫn giữ nguyên hiệu lực trong trạng thái vận hành suy giảm. Thời hạn hiệu lực của các chứng chỉ (bao gồm chứng chỉ ngang hàng/peer, chứng chỉ phục vụ/serving và chứng chỉ CA ký) thường kéo dài khoảng năm năm.

**Cập nhật đối tượng `MachineConfig`**  
Các thay đổi đối với đối tượng `MachineConfig` đòi hỏi phải khởi động lại nút sẽ không được áp dụng khi hệ thống đang ở trạng thái vận hành suy giảm. Tài nguyên MachineConfigPool chính có thiết lập `maxUnavailable` (số lượng nút không khả dụng tối đa), với giá trị mặc định là `1`. Nút đang không khả dụng hiện tại đã được tính vào hạn mức này. Do hạn mức đã bị sử dụng hết, Machine Config Operator sẽ không tiến hành các cập nhật yêu cầu giải phóng (drain) và khởi động lại nút còn đang hoạt động. Các tài nguyên `MachineConfig` mới vẫn được chấp nhận, nhưng quá trình cập nhật tài nguyên MachineConfigPool sẽ không thể tiếp tục. 

Các thay đổi đối với đối tượng `MachineConfig` đang nằm trong hàng đợi sẽ được áp dụng sau khi nút thứ hai được khôi phục và cụm (cluster) thoát khỏi trạng thái vận hành suy giảm.

**Thực thi chính sách Pod Disruption Budget (PDB)**  
Việc thực thi `PodDisruptionBudget` (PDB) vẫn tiếp tục trong trạng thái vận hành suy giảm. Các yêu cầu loại bỏ pod (eviction) nếu vi phạm chính sách `minAvailable` hoặc `maxUnavailable` sẽ bị từ chối. Các thao tác quản trị liên quan đến việc loại bỏ pod — chẳng hạn như giải phóng nút (node drain) — có thể bị chặn lại. 

Không nên bỏ qua các PDB khi hệ thống đang ở trạng thái vận hành suy giảm. Việc ép buộc loại bỏ pod có thể dẫn đến tình trạng mất đi phiên bản (instance) cuối cùng đang chạy của các dịch vụ quan trọng.

#### II.3.8. Khôi phục nút TNF bị lỗi
Quá trình khôi phục diễn ra tự động khi nút bị lỗi được bật nguồn và kết nối lại với mạng. Trạng thái hoạt động suy giảm (degraded) chỉ là tạm thời. Hãy khôi phục nút bị lỗi càng sớm càng tốt để khôi phục khả năng chịu lỗi, cơ chế cách ly (fencing), cũng như khả năng thực hiện các tác vụ nâng cấp và bảo trì. Nếu không thể khôi phục nút ban đầu, hãy thay thế nút đó.

> **Quan trọng:** Một cụm OpenShift gồm hai nút sử dụng cơ chế cách ly (TNF) mà chỉ còn một nút hoạt động trong thời gian dài sẽ đối mặt với các rủi ro sau:
> 
> * Mất hoàn toàn cụm nếu nút còn lại cũng gặp sự cố
> * Không thể áp dụng các bản cập nhật bảo mật hoặc thực hiện nâng cấp
> * Chứng chỉ hết hạn nếu cụm duy trì ở trạng thái suy giảm vượt quá thời hạn hiệu lực của chứng chỉ

**Quy trình**

1. Bật nguồn nút (node) bị lỗi và xác minh khả năng kết nối mạng trên cả ba mặt phẳng mạng (network plane) trước khi nút đó có thể tham gia lại hoàn toàn vào cụm (cluster). 
  * Mạng BMC hoặc mạng quản lý: Đảm bảo rằng nút ngang hàng (peer node) có thể truy cập được địa chỉ BMC của nút bị lỗi. Nếu không có kết nối BMC, cơ chế cách ly (fencing) sẽ không thể bảo vệ cụm trong trường hợp xảy ra lỗi tiếp theo. 
  * Mạng cụm (Cluster network): Đảm bảo có kết nối hai chiều giữa các nút để phục vụ các tác vụ: thành viên Corosync (cổng 5405 đến 5407), quản lý Pacemaker (cổng 2224) và sao chép dữ liệu etcd (cổng 2379, 2380). 
  * Kubernetes API: Đảm bảo rằng nút đang khôi phục có thể truy cập được API server (cổng 6443). 
2. Đợi Pacemaker thiết lập lại kết nối. Corosync sẽ phát hiện nút quay trở lại và Pacemaker sẽ đánh dấu nút đó ở trạng thái ONLINE. Sau đó, Pacemaker sẽ khởi động các tài nguyên được quản lý — bao gồm kubelet và etcd — trên nút này. 
3. Đợi etcd tham gia lại cụm. Thành viên quay trở lại ban đầu sẽ tham gia với vai trò "learner" (không có quyền biểu quyết), nhận bản sao dữ liệu (snapshot) và đồng bộ hóa nhật ký (log) từ các thành viên đang hoạt động. Sau khi đã cập nhật đầy đủ dữ liệu, nó sẽ được chuyển thành thành viên có quyền biểu quyết, và cụm sẽ khôi phục trạng thái đạt hạn ngạch (quorum) với hai thành viên. 

> **Lưu ý:** Quá trình này thường mất khoảng 15 đến 25 phút. 

4. Xác minh rằng nút đã chuyển sang trạng thái Ready bằng cách chạy lệnh sau:

```bash
$ oc get nodes
```

**Xác minh**

1. Hãy xác minh rằng các toán tử cụm (cluster Operators) đã loại bỏ các trạng thái suy giảm (degraded conditions) bằng cách chạy lệnh sau:

```bash
$ oc get co
```
2. Sau khi khôi phục, hãy xác nhận các điều kiện sau:
  * Cả hai nút đều hiển thị trạng thái Ready (Sẵn sàng) bằng cách chạy lệnh sau:

  ```bash
  $ oc get nodes
  ```

  Kết quả đầu ra tương tự như ví dụ sau:

  ```bash
  $ oc get nodes
  NAME                                                   STATUS   ROLES                         AGE    VERSION
  e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com   Ready    control-plane,master,worker   5d1h   v1.35.4
  e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com   Ready    control-plane,master,worker   5d1h   v1.35.4
  ```

  * Hãy xác minh rằng etcd Operator không còn báo trạng thái Degraded=True nữa bằng cách chạy lệnh sau:

  ```bash
  $ oc get co etcd
  ```

  Kết quả đầu ra tương tự như ví dụ sau:

  ```
  NAME   VERSION       AVAILABLE   PROGRESSING   DEGRADED   SINCE   MESSAGE
  etcd   4.22.0-rc.3   True        False         False      5d1h
  ```

  * Xác minh rằng cả hai thiết bị cách ly (fencing device) đều đang hoạt động.
  * Các thay đổi MachineConfig, các bản nâng cấp và quy trình xoay vòng chứng chỉ đang chờ xử lý có thể được tiếp tục thực hiện.

#### II.3.9. Xác minh trạng thái cụm TNF
Bạn có thể chẩn đoán và giải quyết các vấn đề thường gặp trong quá trình vận hành ở trạng thái suy giảm (degraded operation) của cụm TNF (Two-Node with Fencing - cụm hai nút có cơ chế cách ly/fencing) bằng cách đánh giá tình trạng của Pacemaker, etcd và trạng thái các nút.

**Quy trình**

1. Kiểm tra trạng thái Pacemaker từ nút vẫn đang hoạt động bằng cách chạy lệnh sau:

```bash
$ oc debug node/<surviving-node> -- chroot /host pcs status
```

2. Kiểm tra tư cách thành viên etcd bằng cách chạy lệnh sau:

```bash
$ oc debug node/<surviving-node> -- chroot /host podman exec etcd etcdctl member list -w table
```

3. Kiểm tra trạng thái nút bằng cách chạy lệnh sau:

```bash
$ oc get nodes
```

4. Kiểm tra các toán tử cụm (cluster Operators) bằng cách chạy lệnh sau:

```bash
$ oc get co
```

#### II.3.10. Xử lý lỗi fencing trong TNF
Bạn cần can thiệp thủ công khi cụm máy chủ (cluster) không thể tự động thực hiện fencing (cô lập) một nút bị lỗi. Hãy thực hiện quy trình dưới đây để tắt nguồn phần cứng không phản hồi một cách an toàn, đồng thời xóa trạng thái UNCLEAN (ngoại tuyến), từ đó cho phép nút còn lại tiếp tục các hoạt động của cụm.

Nếu lệnh `pcs status` hiển thị nút bị lỗi ở trạng thái UNCLEAN (ngoại tuyến), nghĩa là quy trình fencing tự động đã không thành công và cần phải thực hiện khôi phục thủ công.

**Quy trình**

1. Xác minh rằng nút bị lỗi đã được tắt nguồn thông qua bảng điều khiển BMC hoặc kiểm tra trực tiếp tại chỗ. 
2. Xác nhận việc fencing theo cách thủ công bằng cách chạy lệnh sau:

```bash
$ oc debug node/<surviving-node> -- chroot /host pcs stonith confirm <failed_node_name> --force
```

#### II.3.11. Khắc phục tình trạng etcd không tự khôi phục trên nút còn lại
Nếu nút còn lại không tự động khởi động lại etcd sau khi thao tác cách ly (fencing) hoàn tất, hãy đặt lại trạng thái tài nguyên để khôi phục dịch vụ. .Điều kiện tiên quyết

Đảm bảo rằng bạn thực hiện các lệnh `oc debug` dưới đây trong cụm OpenShift hai nút có áp dụng cơ chế cách ly (fencing).

**Quy trình**

1. Dọn dẹp tài nguyên etcd bằng cách chạy lệnh sau:

```bash
$ oc debug node/<surviving_node> -- chroot /host bash -c '
  pcs resource cleanup etcd
'
```

Kết quả đầu ra tương tự như ví dụ sau:

Ví dụ

```
sudo  pcs resource cleanup etcd
+
Cleaned up etcd:0 on e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com
+
Cleaned up etcd:1 on e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com
+
Waiting for 1 reply from the controller
+
... got reply (done)
```

2. Kiểm tra trạng thái Pacemaker bằng cách chạy lệnh sau:

```bash
$ oc debug node/<surviving_node> -- chroot /host bash -c ' \
  pcs status
'
```

Kết quả đầu ra tương tự như ví dụ sau:

```
Cluster name: TNF
+
Cluster Summary:
+
Stack: corosync (Pacemaker is running)
+
Current DC: e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com (version 2.1.10-2.el9-5693eaeee) - partition with quorum
+
Last updated: Wed May 20 17:36:23 2026 on e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com
+
Last change:  Wed May 20 17:36:22 2026 by root via root on e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com
+
2 nodes configured
+
6 resource instances configured
+
Node List:
+
Online: [ e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com ]
+
Full List of Resources:
+
Clone Set: kubelet-clone [kubelet]:
+
Started: [ e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com ]
+
e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com_redfish  (stonith:fence_redfish):   Started e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com
+
e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com_redfish  (stonith:fence_redfish):   Started e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com
+
Clone Set: etcd-clone [etcd]:
+
Started: [ e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com ]
+
Failed Resource Actions:
+
e920t-01.cluster1.metal-platform.eng.rdu2.redhat.com_redfish 1m-interval monitor on e920t-02.cluster1.metal-platform.eng.rdu2.redhat.com could not be executed (Timed Out: Fence agent did not complete within 20s) at Sun May 17 12:50:32 2026 after 20.003s
+
Daemon Status:
+
corosync: active/enabled
+
pacemaker: active/enabled
+
pcsd: active/enabled
```

#### II.3.12. Khắc phục sự cố nút bị lỗi không tham gia lại cụm sau khi bật nguồn
Nếu nút bị lỗi không tham gia lại cụm sau khi được bật nguồn, hãy kiểm tra kết nối Corosync và trạng thái hoạt động của dịch vụ.

**Quy trình**

1. Kiểm tra trạng thái của Corosync và dịch vụ cụm trên nút vừa khởi động lại bằng cách chạy lệnh sau:

```bash
$ oc debug node/<returning-node> -- chroot /host bash -c '\
  corosync-cfgtool -s\
  systemctl status corosync pacemaker pcsd
'
```

2. Xác minh kết nối mạng giữa các nút. 
  * Đối với Mạng Cụm (Corosync), hãy thực hiện lệnh sau để ping một nút lân cận nhằm kiểm tra nút ngang hàng trên mạng cụm:

  ```bash
  $ ping -c 3 <peer_node_ip>
  ```

  * Đối với mạng Quản lý hoặc mạng BMC (đường dẫn cách ly - fencing path), hãy chạy lệnh sau:

  ```bash
  $ ping -c 3
  ```

  Kết quả đầu ra tương tự như ví dụ sau:

  ```
  HTTP_CODE: 401
  TIME_TOTAL: 0.224628s
  TIME_CONNECT: 0.000322s
  ```

  > **Lưu ý:** Mã `HTTP 401` là kết quả dự kiến vì lệnh curl không truyền thông tin xác thực (không có tham số --user Administrator:password). Điều này xác nhận rằng Redfish API đang hoạt động và từ chối các yêu cầu không được xác thực. Giá trị `TIME_CONNECT` là khoảng 0,322ms, cho thấy khả năng kết nối nhanh chóng.

  * Đối với Mạng ứng dụng (OpenShift/etcd), hãy thực hiện các tác vụ sau:

  Kiểm tra tình trạng của API server trên nút cục bộ bằng cách chạy lệnh sau:

  ```bash
  $ curl -sk https://localhost:6443/healthz
  ```

   Check API server health on the peer node by running the following command: 

   ```bash
   $ curl -sk https://<peer-node-ip>:6443/healthz
   ```

   Liệt kê các thành viên trong cụm etcd bằng cách chạy lệnh sau:

   ```bash
   $ podman exec etcd etcdctl member list --write-out=table
   ```

   Kiểm tra tình trạng các điểm cuối (endpoint) etcd trên toàn cụm bằng cách chạy lệnh sau:

   ```bash
   $ podman exec etcd etcdctl endpoint health --cluster
   ```

### II.4. Khắc phục sự cố và khôi phục sau khi cài đặt
Bạn có thể khắc phục sự cố và khôi phục cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly nút (fencing - TNF) sau khi xảy ra sự cố gián đoạn. Trong trường hợp không thể khôi phục tự động, bạn có thể thực hiện khôi phục thủ công các dịch vụ, thay thế các nút trong mặt phẳng điều khiển (control plane) bị suy giảm hiệu năng, đồng thời sử dụng tập lệnh `fencing_validator` để kiểm tra tình trạng của cụm.

#### II.4.1. Khôi phục thủ công sau sự cố khi không thể thực hiện khôi phục tự động
Bạn có thể cần thực hiện các bước khôi phục thủ công nếu một sự cố ngăn cản cơ chế cách ly (fencing) hoạt động bình thường. Trong trường hợp này, bạn có thể chạy các lệnh trực tiếp trên các nút thuộc mặt phẳng điều khiển (control plane nodes) để khôi phục cụm (cluster). Có năm kịch bản khôi phục chính, nên được thực hiện theo thứ tự sau:

1. Cập nhật thông tin xác thực cho cơ chế cách ly (fencing secrets): Làm mới thông tin đăng nhập của Baseboard Management Console (BMC) nếu chúng không chính xác hoặc đã lỗi thời. 
2. Khôi phục sau sự cố hỏng một nút: Khôi phục chức năng khi chỉ có một nút thuộc mặt phẳng điều khiển bị ngừng hoạt động. 
3. Khôi phục sau sự cố mất nguồn hai nút: Khôi phục chức năng khi cả hai nút thuộc mặt phẳng điều khiển đều bị mất nguồn nhưng có thể khởi động lại được. 
4. Khôi phục quorum (số phiếu tối thiểu) cho corosync sau sự cố mất nguồn hai nút: Khôi phục quorum cho corosync khi cả hai nút thuộc mặt phẳng điều khiển đều mất nguồn nhưng chỉ có một nút có thể khởi động lại được. 
5. Thay thế nút thuộc mặt phẳng điều khiển không thể khôi phục: Thay thế nút đó để khôi phục chức năng của cụm.

**Các điều kiện tiên quyết**

* Bạn có quyền truy cập quản trị vào các nút mặt phẳng điều khiển (control plane nodes). 
* Bạn có thể kết nối với các nút này thông qua SSH.

**Quy trình**

1. Cập nhật các bí mật (secret) dùng cho cơ chế cách ly (fencing):
  * a. Nếu Cluster API không khả dụng, hãy cập nhật bí mật dùng cho cơ chế cách ly bằng cách chạy lệnh sau trên một trong các nút (node) của cụm:

  ```bash
  $ sudo pcs stonith update <node_name>_redfish username=<user_name> password=<password>
  ```

  Sau khi Cluster API phục hồi, hoặc nếu Cluster API đã sẵn sàng, hãy cập nhật bí mật (secret) dùng cho cơ chế cách ly (fencing) trong cụm để đảm bảo dữ liệu được đồng bộ, theo hướng dẫn ở bước tiếp theo.

  * b. Chỉnh sửa tên người dùng và mật khẩu cho thông tin xác thực (secret) dùng trong cơ chế cách ly (fencing) hiện có dành cho nút mặt phẳng điều khiển (control plane node) bằng cách chạy các lệnh sau:

  ```bash
  $ oc project openshift-etcd
  $ oc edit secret <node_name>-fencing
  $ oc edit secret fencing-credentials-<node_name>
  ```

  Thông tin bảo mật này chứa các khóa dữ liệu sau:

  **Bảng 2.6. Các khóa dữ liệu**

  | Key                       | Mô tả                                                                               | Các thay đổi trong quá trình xoay vòng thông tin xác thực? |
  |---------------------------|-------------------------------------------------------------------------------------|------------------------------------------------------------|
  | `username`                | BMC username                                                                        | Có                                                         |
  | `password`                | Mật khẩu BMC                                                                        | Có                                                         |
  | `address`                 | URL Redfish đầy đủ (ví dụ: `redfish+https://192.168.1.10:443/redfish/v1/Systems/1`) | Chỉ khi địa chỉ BMC thay đổi                               |
  | `certificateVerification` | `Disabled` hoặc `Enabled`                                                           | Chỉ khi các thiết lập TLS thay đổi                         |

  > **Lưu ý:** Lệnh `oc edit secret` hiển thị các giá trị đã được mã hóa bằng base64. Nếu bạn sửa đổi bất kỳ giá trị nào, các giá trị mới cũng phải được mã hóa bằng base64.

  Ngoài ra, bạn có thể sử dụng lệnh sau để tạo hoặc cập nhật bí mật (secret) bằng các chuỗi ký tự cụ thể:

  ```bash
  $ oc create secret generic fencing-credentials-<node_name> \
    --from-literal=address='<redfish_address>' \
    --from-literal=username='<new_username>' \
    --from-literal=password='<new_password>' \
    --from-literal=certificateVerification='<Disabled_or_Enabled>' \
    --dry-run=client -o yaml | oc apply -f -
  ```

  Cần phải có đủ cả bốn khóa. Cluster etcd Operator sẽ từ chối các secret bị thiếu khóa.

  * c. Hãy xác minh rằng thông tin xác thực mới có thể kết nối với BMC bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs stonith config <node_name>_redfish
  ```

  * d. Hãy xác minh rằng không có tài nguyên STONITH nào bị chặn bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs status --full
  ```

  Cluster etcd Operator thực hiện quy trình xác thực này một cách tự động khi áp dụng thông tin xác thực từ đối tượng secret thông qua việc sử dụng lệnh sau:

  ```bash
  $ fence_redfish --action status
  ```

  Nếu cụm hệ thống phục hồi sau khi cập nhật các thông tin bảo mật về cơ chế cách ly (fencing secrets), thì không cần thực hiện thêm thao tác nào khác. Nếu sự cố vẫn tiếp diễn, hãy chuyển sang bước tiếp theo.

2. Khôi phục sau sự cố tại một nút đơn lẻ:
  * a. Thu thập thông tin chẩn đoán ban đầu bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs status --full
  ```

  Lệnh này cung cấp cái nhìn chi tiết về trạng thái hiện tại của cụm và các tài nguyên. Bạn có thể sử dụng kết quả đầu ra để xác định các vấn đề liên quan đến cơ chế cách ly (fencing) hoặc quá trình khởi động etcd.

  * b. Chạy các lệnh chẩn đoán bổ sung sau đây nếu cần:

  Đặt lại các tài nguyên trên cụm của bạn bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs resource cleanup
  ```

  * c. Xem lại tất cả hoạt động của Pacemaker trên nút bằng cách chạy lệnh sau:

  ```bash
  $ sudo journalctl -u pacemaker
  ```

  * d. Chẩn đoán các vấn đề khởi động tài nguyên etcd bằng cách chạy lệnh sau:

  ```bash
  $ sudo journalctl -u pacemaker | grep podman-etcd
  ```

  * e. Xem cấu hình hàng rào (fencing) cho nút bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs stonith config <node_name>_redfish
  ```

  Nếu yêu cầu cơ chế cách ly (fencing) nhưng cơ chế này không hoạt động, hãy đảm bảo rằng điểm cuối (endpoint) Redfish dành cho việc cách ly có thể truy cập được và kiểm tra xem thông tin xác thực đã chính xác hay chưa.

  Nếu bạn đã xác nhận rằng nút (node) bị lỗi hoàn toàn không thể truy cập được nhưng cơ chế cách ly tự động vẫn không hoạt động, hãy kiểm tra xem nút bị lỗi đó có đáp ứng TẤT CẢ các điều kiện sau đây hay không:

    * Nút đã được tắt nguồn và không thể khởi động lại. 
    * Nút không thể truy cập vào bất kỳ bộ lưu trữ chia sẻ hoặc tài nguyên cụm (cluster) nào. 
    * Nút hoàn toàn bị cô lập khỏi mạng của cụm.

  * f. Xác nhận rằng nút đã được cách ly bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs stonith confirm <failed_node_name>
  ```

  > **Cảnh báo:** Nếu nút bị lỗi vẫn có thể truy cập được hoặc có khả năng truy cập các tài nguyên dùng chung, thì việc xác nhận thực hiện cơ chế cách ly (fencing) có thể dẫn đến tình trạng hỏng dữ liệu và lỗi cụm hệ thống.

  * g. Nếu etcd không khởi động mặc dù cơ chế cách ly (fencing) vẫn đang hoạt động, hãy khôi phục etcd từ bản sao lưu bằng cách chạy các lệnh sau:

  ```bash
  $ sudo cp -r /var/lib/etcd-backup/* /var/lib/etcd/
  $ sudo chown -R etcd:etcd /var/lib/etcd
  ```

  Nếu quá trình khôi phục thành công, bạn không cần thực hiện thêm thao tác nào khác. Nếu sự cố vẫn tiếp diễn, hãy chuyển sang bước tiếp theo.

3. Khôi phục sau sự cố mất nguồn ở cả hai nút (node) trong trường hợp cả hai nút đều có thể khôi phục được:  
Quy trình này áp dụng khi cả hai nút thuộc mặt phẳng điều khiển (control plane) đều bị mất nguồn và cả hai nút này đều có thể khởi động lại. Nếu chỉ có một nút có thể khởi động lại, hãy chuyển sang bước 4.

  * a. Bật nguồn cả hai nút mặt phẳng điều khiển (control plane node).

  Pacemaker sẽ tự động khởi động và bắt đầu quy trình khôi phục khi phát hiện cả hai nút đều đã trực tuyến. Nếu quá trình khôi phục không diễn ra như mong đợi, hãy sử dụng các lệnh chẩn đoán đã nêu ở bước trước để kiểm tra vấn đề.

  * b. Đặt lại (reset) các tài nguyên trong cụm (cluster) của bạn và yêu cầu Pacemaker thử khởi động lại chúng từ đầu bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs resource cleanup
  ```

  * c. Kiểm tra thứ tự khởi động tài nguyên bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs status --full
  ```

  * d. Nếu kubelet gặp lỗi, hãy kiểm tra nhật ký dịch vụ pacemaker bằng cách chạy các lệnh sau:

  ```bash
  $ sudo journalctl -u pacemaker
  $ sudo journalctl -u kubelet
  ```

  * e. Xử lý tình trạng etcd không đồng bộ.

  Nếu một nút có dữ liệu etcd cập nhật hơn, Pacemaker sẽ cố gắng thực hiện thao tác cách ly (fence) đối với nút đang bị tụt hậu, sau đó khởi động lại nút này ở vai trò "người học" (learner). Nếu quá trình này bị đình trệ, hãy kiểm tra điểm cuối (endpoint) và thông tin xác thực dùng cho thao tác cách ly qua Redfish bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs stonith config
  ```

  Nếu quá trình khôi phục thành công, bạn không cần thực hiện thêm thao tác nào khác. Nếu sự cố vẫn tiếp diễn, hãy thực hiện khôi phục thủ công theo hướng dẫn ở bước tiếp theo.

4. Khôi phục cơ chế thiết lập đa số (quorum) của Corosync sau khi cả hai nút (node) đều mất nguồn (trong trường hợp chỉ có thể khởi động lại một nút):

Quy trình này áp dụng khi cả hai nút thuộc mặt phẳng điều khiển (control plane) đều bị mất nguồn và chỉ có thể khởi động lại một nút. Trong tình huống này, cụm máy chủ (cluster) đã mất cơ chế thiết lập đa số (quorum) của Corosync, do trạng thái được ghi nhận gần nhất cho thấy cả hai nút đều đang hoạt động trước khi xảy ra sự cố mất nguồn.

> **Quan trọng:** Chỉ thực hiện quy trình này khi đáp ứng cả hai điều kiện sau:
> 
> * Tất cả các nút mặt phẳng điều khiển (control plane node) đều bị mất nguồn
> * Chỉ có thể khởi động lại một nút mặt phẳng điều khiển duy nhất

Tình huống này thường xảy ra khi bạn cần thay thế một nút điều khiển (control plane node) — do một nút không thể khôi phục được — và nút còn lại thì bị mất nguồn trước khi quy trình thay thế diễn ra.

  * a. Hãy xác minh rằng chỉ còn một nút đang trực tuyến bằng cách chạy lệnh sau trên nút vẫn còn hoạt động:

  ```bash
  $ sudo pcs status --full
  ```

  Kết quả đầu ra cho thấy chỉ có một nút đang trực tuyến. Kết quả đầu ra mẫu như sau:

  ```
  Cluster name: TNF
  Cluster Summary:
    * Stack: corosync (Pacemaker is running)
    * Current DC: NONE
    * Last updated: Wed Apr 29 16:21:17 2026 on master-0.ostest.test.metalkube.org
    * Last change:  Wed Apr 29 16:19:25 2026 by root via root on master-1.ostest.test.metalkube.org
    * 2 nodes configured
    * 6 resource instances configured

  Node List:
    * Node master-0.ostest.test.metalkube.org (1): UNCLEAN (offline)
    * Node master-1.ostest.test.metalkube.org (2): UNCLEAN (offline)

  Full List of Resources:
    * Clone Set: kubelet-clone [kubelet]:
      * kubelet (systemd:kubelet):   Stopped
      * kubelet (systemd:kubelet):   Stopped
    * master-0.ostest.test.metalkube.org_redfish  (stonith:fence_redfish):   Stopped
    * master-1.ostest.test.metalkube.org_redfish  (stonith:fence_redfish):   Stopped
    * Clone Set: etcd-clone [etcd]:
      * etcd  (ocf:heartbeat:podman-etcd):   Stopped
      * etcd  (ocf:heartbeat:podman-etcd):   Stopped

  Tickets:

  PCSD Status:
    master-0.ostest.test.metalkube.org: Online
    master-1.ostest.test.metalkube.org: Offline

  Daemon Status:
    corosync: active/enabled
    pacemaker: active/enabled
    pcsd: active/enabled
  ```

  Trạng thái PCSD cho thấy nút master-0 đang ở trạng thái trực tuyến (Online), trong khi nút còn lại ở trạng thái ngoại tuyến (Offline). Cả hai nút trong danh sách nút đều ở trạng thái ngoại tuyến vì không nút nào đạt đủ số phiếu cần thiết (quorum).

  ```bash
  $ sudo pcs quorum status --debug
  Running: /usr/sbin/corosync-quorumtool -p
  Environment:
    LC_ALL=C

  Finished running: /usr/sbin/corosync-quorumtool -p
  Return value: 2
  --Debug Stdout Start--
  Quorum information
  ------------------
  Date:             Wed Apr 29 16:25:55 2026
  Quorum provider:  corosync_votequorum
  Nodes:            1
  Node ID:          1
  Ring ID:          1.e
  Quorate:          No

  Votequorum information
  ----------------------
  Expected votes:   2
  Highest expected: 2
  Total votes:      1
  Quorum:           1 Activity blocked
  Flags:            2Node WaitForAll

  Membership information
  ----------------------
      Nodeid      Votes    Qdevice Name
           1          1         NR master-0.ostest.test.metalkube.org (local)

  --Debug Stdout End--
  --Debug Stderr Start--

  --Debug Stderr End--

  Error: Unable to get quorum status:
  ```

  * b. Trước khi tiếp tục, hãy xác minh rằng nút bị lỗi hoàn toàn không thể truy cập được.

  Trước khi xác nhận với Pacemaker rằng nút bị lỗi đã được cách ly (fenced), bạn phải đảm bảo nút đó đáp ứng TẤT CẢ các điều kiện sau:

    * Nút đã được tắt nguồn và không thể khởi động lại.
    * Nút không thể truy cập vào bất kỳ bộ lưu trữ chia sẻ hoặc tài nguyên cụm (cluster) nào.
    * Nút hoàn toàn bị cô lập khỏi mạng của cụm.
    * Nếu nút bị lỗi vẫn có thể truy cập được hoặc có khả năng truy cập vào các tài nguyên chia sẻ, TUYỆT ĐỐI KHÔNG thực hiện bước này. Việc xác nhận cách ly đối với một nút vẫn đang hoạt động có thể dẫn đến tình trạng hỏng dữ liệu và gây lỗi cho toàn bộ cụm.

  * c. Xác nhận với Pacemaker rằng nút bị lỗi đã được cách ly bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs quorum unblock
  ```

  Lệnh này hiển thị kết quả mẫu như sau:

  ```
  WARNING: If node 'master-1' is not powered off or it does have access to shared resources, data corruption and/or cluster failure may occur
  Type 'yes' or 'y' to proceed, anything else to cancel:
  ```

  Hãy thay thế `<failed_node_name>` bằng tên của nút control plane bị lỗi (ví dụ: control-plane-1).


  * d. Xác minh rằng số lượng đại biểu (quorum) đã được khôi phục bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs quorum status
  ```

  Lệnh này hiển thị kết quả mẫu như sau:

  ```
  Quorum information
  ------------------
  Date:             Fri Oct  3 14:15:31 2025
  Quorum provider:  corosync_votequorum
  Nodes:            1
  Node ID:          1
  Ring ID:          1.16
  Quorate:          Yes

  Votequorum information
  ----------------------
  Expected votes:   2
  Highest expected: 2
  Total votes:      1
  Quorum:           1
  Flags:            2Node Quorate
  ```

  * e. Hãy đợi 30 giây để Pacemaker xử lý xác nhận cách ly (fencing) và bắt đầu quá trình khôi phục.
  * f. Xác minh rằng etcd đang chạy trên nút còn lại bằng cách thực thi lệnh sau:

  ```bash
  $ sudo pcs resource status etcd
  ```

  Nếu etcd không chạy, hãy khởi động lại nó bằng cách thực thi lệnh sau:

  ```bash
  $ sudo pcs resource cleanup etcd
  ```

  Hãy đợi tối đa 5 phút để etcd khởi động. Kiểm tra trạng thái định kỳ bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs resource status etcd
  ```

  Lệnh này cho thấy tài nguyên `podman-etcd` đã được khởi động. Nếu container được khởi động thành công, bạn có thể xem các bản ghi (logs) bằng cách chạy lệnh sau:

  ```bash
  sudo podman logs etcd
  ```

  Nếu container chưa được khởi chạy, bạn có thể xem các bản ghi (logs) bằng cách chạy lệnh sau:

  ```bash
  $ journalctl -u pacemaker | grep podman-etcd
  ```

  Các tệp nhật ký liên quan được lưu tại `/var/log/pacemaker/pacemaker.log`. Kết quả đầu ra phải cho thấy etcd đã được khởi động trên nút còn hoạt động.

  Sau khi khôi phục quorum cho corosync và xác nhận rằng etcd đang chạy, hãy chuyển sang bước 5 để thay thế nút mặt phẳng điều khiển (control plane node) đã bị lỗi.

5. Nếu bạn cần thực hiện khôi phục thủ công trong trường hợp một trong các nút (node) không thể phục hồi, hãy làm theo quy trình được mô tả trong phần "Thay thế các nút control plane trong cụm OpenShift gồm hai nút".

Khi một cụm mất một nút, nó sẽ chuyển sang trạng thái suy giảm (degraded mode). Ở trạng thái này, Pacemaker sẽ tự động bỏ chặn quorum (cơ chế đảm bảo sự đồng thuận) và cho phép cụm tạm thời hoạt động trên nút còn lại.

Nếu cả hai nút đều gặp sự cố nhưng đều có thể khởi động lại, Pacemaker sẽ tự động thiết lập lại quorum khi cả hai nút cùng trực tuyến (online).

Nếu chỉ có thể khởi động lại một nút, hãy chuyển sang bước 4 để khôi phục quorum của corosync theo cách thủ công.

Nếu vẫn cần thực hiện khôi phục thủ công nhưng không thành công, hãy thu thập dữ liệu thông qua `must-gather` và `sosreport`, sau đó gửi báo cáo lỗi (bug report).

**Xác minh**

Để biết thông tin về cách xác minh rằng cả các nút mặt phẳng điều khiển (control plane nodes) và etcd đều đang hoạt động bình thường, hãy xem phần "Xác minh tình trạng của etcd trong cụm OpenShift gồm hai nút với cơ chế cách ly (fencing)".

#### II.4.2. Thay thế các nút control plane trong cụm OpenShift Container Platform gồm hai nút bằng phương pháp cách ly (fencing)
Bạn có thể thay thế một nút control plane bị lỗi trong cụm OpenShift Container Platform gồm hai nút. Nút thay thế phải sử dụng cùng tên máy chủ (host name) và địa chỉ IP với nút đã bị lỗi.

##### Điều kiện tiên quyết
* Bạn có một nút control plane vẫn đang hoạt động bình thường. 
* Bạn đã xác minh rằng máy đó không chạy hoặc nút đó ở trạng thái không sẵn sàng (not ready). 
* Bạn có quyền truy cập vào cụm với tư cách là người dùng có vai trò `cluster-admin`. 
* Bạn biết tên máy chủ và địa chỉ IP của nút bị lỗi.

> **Lưu ý:** Hãy thực hiện sao lưu `etcd` trước khi tiến hành để đảm bảo rằng bạn có thể khôi phục cụm nếu xảy ra bất kỳ sự cố nào.

##### Quy trình
1. Kiểm tra trạng thái quorum (số phiếu bầu tối thiểu cần thiết) bằng cách chạy lệnh sau:

```bash
$ sudo pcs quorum status
```

Ví dụ đầu ra

```
Quorum information
------------------
Date:             Fri Oct  3 14:15:31 2025
Quorum provider:  corosync_votequorum
Nodes:            2
Node ID:          1
Ring ID:          1.16
Quorate:          Yes

Votequorum information
----------------------
Expected votes:   2
Highest expected: 2
Total votes:      2
Quorum:           1
Flags:            2Node Quorate WaitForAll

Membership information
----------------------
    Nodeid      Votes    Qdevice Name
         1          1         NR master-0 (local)
         2          1         NR master-1
```

  * a. Nếu mất số lượng nút tối thiểu (quorum) mà vẫn còn một nút mặt phẳng điều khiển (control plane node) đang hoạt động, hãy khôi phục quorum theo cách thủ công trên nút còn lại đó bằng cách chạy lệnh sau:

  ```bash
  $ sudo pcs quorum unblock
  ```

  * b. Nếu chỉ có một nút bị lỗi, hãy xác minh rằng etcd đang chạy trên nút còn lại bằng cách thực thi lệnh sau:

  ```bash
  $ sudo pcs resource status etcd
  ```

  * c. Nếu etcd không chạy, hãy khởi động lại etcd bằng cách thực thi lệnh sau:

  ```bash
  $ sudo pcs resource cleanup etcd
  ```

  Nếu etcd vẫn không khởi động, hãy buộc khởi động thủ công trên nút (node) còn hoạt động, đồng thời bỏ qua bước cách ly (fencing):

  > **Quan trọng:** Trước khi chạy các lệnh này, hãy đảm bảo rằng nút đang được thay thế không thể truy cập được. Nếu không, bạn có nguy cơ làm hỏng dữ liệu etcd.

  ```bash
  $ sudo pcs resource debug-stop etcd
  $ sudo OCF_RESKEY_CRM_meta_notify_start_resource='etcd' pcs resource debug-start etcd
  ```

  Sau khi khôi phục, etcd phải đang chạy thành công trên nút còn lại.

2. Xóa các bí mật etcd dành cho nút bị lỗi bằng cách chạy các lệnh sau:

```bash
$ oc project openshift-etcd
$ oc delete secret etcd-peer-<node_name>
$ oc delete secret etcd-serving-<node_name>
$ oc delete secret etcd-serving-metrics-<node_name>
```

> **Lưu ý:** Để thay thế nút bị lỗi, trước tiên bạn phải xóa các bí mật (secrets) etcd của nút đó. Khi etcd đang chạy, API server có thể mất một khoảng thời gian để phản hồi các lệnh này.

3. Xóa các tài nguyên dành cho nút bị lỗi:
* a. Nếu bạn có các đối tượng BareMetalHost (BMH), hãy liệt kê chúng để xác định nút mà bạn muốn thay thế bằng cách chạy lệnh sau:

```bash
$ oc get bmh -n openshift-machine-api
```

* b. Xóa đối tượng BMH cho nút bị lỗi bằng cách chạy lệnh sau:

```bash
$ oc delete bmh/<bmh_name> -n openshift-machine-api
```

* c. Hãy liệt kê các đối tượng Machine để xác định đối tượng tương ứng với nút mà bạn đang thay thế bằng cách chạy lệnh sau:

```bash
$ oc get machines.machine.openshift.io -n openshift-machine-api
```

d. Lấy nhãn chứa giá trị băm của máy từ đối tượng Machine bằng cách chạy lệnh sau:

```bash
oc get machines.machine.openshift.io/<machine_name> -n openshift-machine-api \
  -o jsonpath='Machine hash label: {.metadata.labels.machine\.openshift\.io/cluster-api-cluster}{"\n"}'
```

Hãy thay thế `<machine_name>` bằng tên của một đối tượng Machine trong cụm (cluster) của bạn. Ví dụ: `ostest-bfs7w-ctrlplane-0.`

Bạn cần nhãn này để cấp phát một đối tượng Machine mới.

e. Xóa đối tượng Machine của nút bị lỗi bằng cách chạy lệnh sau:

```bash
$ oc delete machines.machine.openshift.io/<machine_name>-<failed nodename> -n openshift-machine-api
```

> **Lưu ý:** Đối tượng node sẽ tự động bị xóa sau khi đối tượng `Machine` bị xóa.

4.  Tạo lại máy chủ bị lỗi bằng cách sử dụng cùng tên và địa chỉ IP:

> **Quan trọng:** Bạn chỉ được thực hiện bước này nếu đang sử dụng cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure) hoặc Machine API để tạo nút ban đầu. Để biết thông tin về việc thay thế nút mặt phẳng điều khiển (control plane node) trên phần cứng vật lý (bare-metal) bị lỗi, hãy xem phần "Thay thế thành viên etcd không ổn định trên phần cứng vật lý".

  * a. Xóa các đối tượng BMH và Machine. Bộ điều khiển máy (machine controller) sẽ tự động xóa đối tượng node.
  * b. Cung cấp (provision) một máy mới bằng cách sử dụng cấu hình mẫu sau đây:

    **Ví dụ về cấu hình đối tượng máy (Machine object)**

    ```yaml
    apiVersion: machine.openshift.io/v1beta1
    kind: Machine
    metadata:
      annotations:
        metal3.io/BareMetalHost: openshift-machine-api/{bmh_name}
      finalizers:
      - machine.machine.openshift.io
      labels:
        machine.openshift.io/cluster-api-cluster: {machine_hash_label}
        machine.openshift.io/cluster-api-machine-role: master
        machine.openshift.io/cluster-api-machine-type: master
      name: {machine_name}
      namespace: openshift-machine-api
    spec:
      authoritativeAPI: MachineAPI
      metadata: {}
      providerSpec:
        value:
          apiVersion: baremetal.cluster.k8s.io/v1alpha1
          customDeploy:
            method: install_coreos
          hostSelector: {}
          image:
            checksum: ""
            url: ""
          kind: BareMetalMachineProviderSpec
          metadata:
            creationTimestamp: null
          userData:
            name: master-user-data-managed
    ```

    * `metadata.annotations.metal3.io/BareMetalHost`: Thay thế {bmh_name} bằng tên của đối tượng BMH gắn với máy chủ mà bạn đang thay thế.
    * `labels.machine.openshift.io/cluster-api-cluster`: Thay thế {machine_hash_label} bằng nhãn mà bạn đã lấy từ máy (machine) mà bạn đã xóa.
    * `metadata.name`: Thay thế {machine_name} bằng tên của máy (machine) mà bạn đã xóa.

  * c. Tạo đối tượng BMH mới cùng với secret để lưu trữ thông tin xác thực BMC bằng cách chạy lệnh sau:

    ```bash
    $ cat <<EOF | oc apply -f -
    apiVersion: v1
    kind: Secret
    metadata:
      name: <secret_name>
      namespace: openshift-machine-api
    data:
      password: <password>
      username: <username>
    type: Opaque
    ---
    apiVersion: metal3.io/v1alpha1
    kind: BareMetalHost
    metadata:
      name: {bmh_name}
      namespace: openshift-machine-api
    spec:
      automatedCleaningMode: disabled
      bmc:
        address: <redfish_url>/{uuid}
        credentialsName: <name>
        disableCertificateVerification: true
      bootMACAddress: {boot_mac_address}
      bootMode: UEFI
      externallyProvisioned: false
      online: true
      rootDeviceHints:
        deviceName: /dev/disk/by-id/scsi-<serial_number>
      userData:
        name: master-user-data-managed
        namespace: openshift-machine-api
    EOF
    ```

    * `metadata.name`: Chỉ định tên của secret (bí mật).
    * `metadata.name`: Thay thế {bmh_name} bằng tên của đối tượng BMH mà bạn đã xóa.
    * `bmc.address`: Thay thế {uuid} bằng UUID của nút (node) mà bạn đã tạo.
    * `bmc.credentialsNam`e: Thay thế name bằng tên của secret mà bạn đã tạo.
    * `bootMACAddress`: Chỉ định địa chỉ MAC của giao diện mạng dùng cho việc cung cấp (provisioning). Đây là địa chỉ MAC mà nút sử dụng để tự nhận diện khi giao tiếp với Ironic trong quá trình cung cấp.

5. Hãy xác minh rằng nút mới đã đạt đến trạng thái Provisioned bằng cách chạy lệnh sau:

```bash
$ oc get bmh -o wide
```

Giá trị của cột `STATUS` trong kết quả đầu ra của lệnh này phải là `Provisioned`.

> **Lưu ý:** Quá trình cung cấp (provisioning) có thể mất từ 10 đến 20 phút để hoàn tất.

6. Hãy xác minh rằng cả hai nút mặt phẳng điều khiển (control plane node) đều ở trạng thái Sẵn sàng (Ready) bằng cách chạy lệnh sau:

```bash
$ oc get nodes
```

Giá trị của cột `STATUS` trong kết quả đầu ra của lệnh này phải là `Ready` đối với cả hai nút.

7. Áp dụng chú thích tách rời (detached annotation) cho đối tượng BMH để ngăn Machine API quản lý đối tượng này bằng cách chạy lệnh sau:

```bash
$ oc annotate bmh <bmh_name> -n openshift-machine-api baremetalhost.metal3.io/detached='' --overwrite
```

8. Kết nối lại nút thay thế vào cụm Pacemaker bằng cách chạy các lệnh sau:

> **Lưu ý:** Hãy chạy các lệnh sau trên nút mặt phẳng điều khiển (control plane node) vẫn còn hoạt động, không phải trên nút đang được thay thế.

```bash
$ sudo pcs cluster node remove <node_name>
$ sudo pcs cluster node add <node_name> addr=<node_ip> --start --enable
```

9. Hãy cập nhật secret (bí mật) dùng cho cơ chế fencing để STONITH có thể thực hiện thao tác fencing đối với node mới. Bạn bắt buộc phải cập nhật secret này nếu node thay thế có UUID hệ thống BMC khác với node đã bị lỗi.

> **Lưu ý:** Không được cập nhật trực tiếp tài nguyên STONITH bằng lệnh `pcs stonith update`. Thành phần "cluster etcd Operator" chịu trách nhiệm quản lý cấu hình STONITH và sẽ ghi đè lên bất kỳ thay đổi nào được thực hiện thủ công. 
Đối với các cụm sử dụng thông tin xác thực fencing dựa trên địa chỉ MAC, hãy chạy tập lệnh `update-fencing-credentials.sh` từ một node thuộc mặt phẳng điều khiển (control-plane) của TNF. Tập lệnh này sẽ xác định secret hiện có thông qua tên host (hostname), mã băm MAC hoặc địa chỉ STONITH hiện tại, sau đó tiến hành cập nhật secret cùng cấu hình STONITH.

Secret dùng cho cơ chế fencing chứa một URL Redfish trong trường địa chỉ (address field). Nếu node thay thế có UUID hệ thống BMC mới, thì URL đó vẫn sẽ trỏ đến UUID cũ, khiến STONITH không thể thực hiện thao tác fencing đối với node mới này.

* a. Xác minh UUID của hệ thống BMC mới bằng cách truy vấn API Redfish:

```bash
$ curl -k https://<bmc_address>/redfish/v1/Systems/ -u <username>:<password>
```

**Ví dụ đầu ra**

```json
{
  "Members": [
    {"@odata.id": "/redfish/v1/Systems/<new_uuid>"}
  ]
}
```

`UUID` là phân đoạn đường dẫn cuối cùng của giá trị `@odata.id` nằm trong mục `Members`.

* b. Cập nhật bí mật hàng rào (fencing secret) cho nút thay thế bằng cách chạy lệnh sau:

  ```bash
  $ oc create secret generic fencing-credentials-<node_name> \
    --namespace openshift-etcd \
    --from-literal=address='redfish+https://<bmc_address>:<port>/redfish/v1/Systems/<new_uuid>' \
    --from-literal=username='<username>' \
    --from-literal=password='<password>' \
    --from-literal=certificateVerification='<Disabled_or_Enabled>' \
    --dry-run=client -o yaml | oc apply -f -
  ```

  Trong đó:

  * Thay thế `<node_name>` bằng tên máy chủ (hostname) của nút thay thế, như hiển thị trong lệnh `oc get nodes`. 
  * Thay thế `<bmc_address>` và `<port>` bằng địa chỉ IP và cổng của BMC. 
  * Thay thế `<new_uuid>` bằng UUID hệ thống BMC mới. 
  * Thay thế `<username>` và `<password>` bằng thông tin đăng nhập BMC. 
  * Thay thế `<Disabled_or_Enabled>` bằng `Disabled` nếu muốn bỏ qua xác thực chứng chỉ (tương ứng với thiết lập `disableCertificateVerification: true` trên đối tượng BMH), hoặc bằng `Enabled` nếu BMC của bạn có chứng chỉ TLS hợp lệ.


* c. Xác minh rằng bí mật đã được cập nhật thành công bằng cách chạy lệnh sau:

```bash
oc get secret fencing-credentials-<node_name> -n openshift-etcd \
  -o jsonpath='{.data.address}' | base64 -d
```

Xác nhận rằng đầu ra chứa UUID hệ thống BMC mới.

> **Lưu ý:** Cần phải có đầy đủ cả bốn khóa, vì etcd Operator của cụm sẽ từ chối các bí mật (secret) bị thiếu khóa. Không sử dụng tập lệnh `update-fencing-credentials.sh` trong bước này. Tập lệnh đó yêu cầu một thiết bị STONITH đang hoạt động bình thường, nhưng thiết bị này chưa thể khả dụng cho đến khi quá trình thiết lập fencing hoàn tất. Chỉ sử dụng tập lệnh này cho các lần thay đổi thông tin xác thực (rotation) sau khi cụm đã đi vào hoạt động hoàn toàn.

10. Xóa các tác vụ cũ liên quan đến nút bị lỗi bằng cách chạy các lệnh sau:

```bash
$ oc project openshift-etcd
$ oc delete job tnf-auth-job-<node_name>
$ oc delete job tnf-after-setup-job-<node_name>
```

##### Xác minh
Để biết thông tin về cách xác minh rằng cả các nút mặt phẳng điều khiển (control plane nodes) và etcd đều đang hoạt động bình thường, hãy xem phần "Xác minh tình trạng của etcd trong cụm OpenShift gồm hai nút với cơ chế cách ly (fencing)".

#### II.4.3. Xác minh trạng thái hoạt động của etcd trong cụm OpenShift hai nút có sử dụng cơ chế cách ly (fencing)
Sau khi hoàn tất các quy trình khôi phục hoặc bảo trì nút, hãy xác minh rằng cả các nút mặt phẳng điều khiển (control plane node) và etcd đều đang hoạt động bình thường.

##### Điều kiện tiên quyết
Bạn có quyền truy cập vào cụm với tư cách là người dùng có đặc quyền `cluster-admin`. 
Bạn có thể truy cập ít nhất một nút mặt phẳng điều khiển thông qua SSH.

##### Quy trình
1. Kiểm tra trạng thái tổng thể của các nút bằng cách chạy lệnh sau:

```bash
$ oc get nodes
```

Lệnh này xác minh rằng cả hai nút mặt phẳng điều khiển (control plane node) đều ở trạng thái Sẵn sàng (Ready), cho thấy chúng có thể tiếp nhận các khối lượng công việc (workload) để lập lịch.

2. Kiểm tra trạng thái của cluster-etcd-operator bằng cách chạy lệnh sau:

```bash
$ oc describe co/etcd
```

`cluster-etcd-operator` thực hiện quản lý và báo cáo về tình trạng hoạt động của hệ thống etcd của bạn. Việc xem xét trạng thái của thành phần này giúp bạn xác định các vấn đề đang diễn ra hoặc các tình trạng suy giảm hiệu năng.

3. Hãy kiểm tra danh sách các thành viên etcd bằng cách chạy lệnh sau:

```bash
$ oc rsh -n openshift-etcd <etcd_pod> etcdctl member list -w table
```

Lệnh này hiển thị các thành viên etcd hiện tại cùng vai trò của chúng. Hãy tìm các nút được đánh dấu là "learner" (nút đang học); trạng thái này cho biết các nút đó đang trong quá trình trở thành thành viên có quyền biểu quyết.

4. Kiểm tra trạng thái tài nguyên Pacemaker bằng cách chạy lệnh sau trên một trong các nút control plane:

```bash
$ sudo pcs status --full
```

Lệnh này cung cấp cái nhìn tổng quan chi tiết về tất cả các tài nguyên do Pacemaker quản lý. Bạn cần đảm bảo đáp ứng các điều kiện sau:

  * Cả hai nút (node) đều đang trực tuyến (online). 
  * Các tài nguyên kubelet và etcd đang chạy. 
  * Cơ chế cách ly (fencing) đã được cấu hình chính xác cho cả hai nút.

#### II.4.4. Tổng quan về tập lệnh kiểm tra fencing (fencing validator)
Cụm OpenShift Container Platform gồm hai nút có sử dụng cơ chế fencing (TNF) dựa trên phương thức STONITH (Shoot The Other Node In The Head - tạm dịch: "vô hiệu hóa nút còn lại") để đảm bảo tính toàn vẹn của dữ liệu khi xảy ra lỗi nút. Nếu hệ thống con fencing được cấu hình sai, cụm có thể không phục hồi một cách an toàn, dẫn đến tình trạng hỏng dữ liệu hoặc kịch bản "split-brain" (phân tách hệ thống, nơi các nút không còn đồng bộ với nhau).

Trước khi có tiện ích này, các quản trị viên hoặc kỹ sư hỗ trợ phải thực hiện kiểm tra thủ công nhiều hệ thống con, bao gồm:

* Trạng thái của Pacemaker
* Cấu hình thiết bị STONITH
* Tình trạng hoạt động (health) của các daemon
* Quorum (số phiếu tối thiểu cần thiết) của etcd
* Các thông tin bảo mật (secrets) dùng cho fencing

Tập lệnh `fencing_validator` tự động hóa các bước kiểm tra thủ công này thành một lệnh duy nhất, cung cấp kết quả rõ ràng (đạt hoặc không đạt) cùng các thông báo lỗi chi tiết, giúp giảm thiểu thời gian khắc phục sự cố và hạn chế sai sót do con người.

`fencing_validator` là một tiện ích chẩn đoán dựa trên Bash, có sẵn trên mỗi nút control-plane (nút điều khiển) trong cụm OpenShift Container Platform sử dụng TNF. Với vai trò là công cụ kiểm tra tình trạng cho hệ thống con fencing, nó xác minh rằng ngăn xếp (stack) STONITH đã được cấu hình đúng cách và đang hoạt động bình thường.

Tập lệnh này nằm tại đường dẫn `/usr/local/bin/fencing_validator` trên cả hai nút control-plane. Nó được tự động cài đặt bởi Machine Config Operator (MCO) và không yêu cầu bất kỳ thao tác cài đặt thủ công nào.

Khi triển khai một cụm TNF, MCO sẽ tạo ra các tệp cấu hình `MachineConfig` dành riêng cho cấu trúc liên kết (topology) đó. Một trong số các tệp này là `fencing-validator.yaml`, nằm trong mã nguồn của MCO tại đường dẫn: `templates/master/00-master/two-node-with-fencing/files/fencing-validator.yaml`. Tệp `MachineConfig` này sẽ ghi tập lệnh vào vị trí `/usr/local/bin/fencing_validator` với quyền thực thi là `0755` trên mọi nút control-plane. Tập lệnh sẽ sẵn sàng sử dụng ngay khi nút hoàn tất việc áp dụng `MachineConfig` — tức là sau lần triển khai ban đầu hoặc sau bất kỳ lần khởi động lại nào do MCO thực hiện.

> **Quan trọng:** Tập lệnh này chỉ được triển khai trên các cụm TNF. Nó không xuất hiện trên các cụm HA (High Availability - độ sẵn sàng cao) tiêu chuẩn, cụm OpenShift một nút (Single-node OpenShift), hoặc các cụm hai nút có sử dụng cơ chế Arbiter (Two-Node with Arbiter).

Bạn có thể sử dụng tập lệnh này cho các mục đích sau:

* Xác thực sau triển khai: Sử dụng tiện ích này để kiểm tra xem cấu hình TNF đã chính xác và hoạt động đầy đủ sau khi triển khai hay chưa. 
* Khắc phục sự cố: Xác định các vấn đề cơ bản cụ thể khi hệ thống TNF không hoạt động như mong đợi. 
* Xác thực trước khi nâng cấp: Kiểm tra trạng thái của ngăn xếp cơ chế cách ly (fencing stack) để đảm bảo cụm (cluster) đủ ổn định cho việc thực hiện nâng cấp phiên bản. 
* Hỗ trợ kỹ thuật: Chạy tập lệnh và cung cấp kết quả đầu ra cho các kỹ sư hỗ trợ nhằm giúp giải quyết nhanh chóng các vấn đề kỹ thuật.

##### II.4.4.1. Các yêu cầu tiên quyết đối với tập lệnh kiểm tra fencing (fencing validator)
Hãy sử dụng tập lệnh `fencing_validator` để xác minh cấu hình fencing trên cụm OpenShift Container Platform gồm hai nút (node). Tập lệnh này, được triển khai tự động bởi Machine Config Operator, đảm bảo rằng các giao diện quản lý nguồn điện (power management interfaces) được cấu hình chính xác nhằm ngăn ngừa tình trạng hỏng dữ liệu khi một nút gặp sự cố. Để chạy tập lệnh, hãy đảm bảo rằng tiện ích `jq` đã được cài đặt, đồng thời bạn có quyền truy cập vào Kubernetes API (thông qua lệnh `oc`) và quyền truy cập SSH vào các nút điều khiển (control-plane nodes).

Bạn có thể xem các thao tác mà tập lệnh sẽ thực hiện — mà không cần thực sự tiến hành kiểm tra cho TNF — bằng cách chạy lệnh sau:

```bash
$ oc debug node/<node_name> --chroot /host /usr/local/bin/fencing_validator --dry-run
```

##### II.4.4.2. Các tùy chọn dòng lệnh cho tập lệnh kiểm chứng cơ chế cách ly (fencing validator)
Để nhanh chóng xác minh cấu hình tính sẵn sàng cao (high-availability) cho cụm OpenShift Container Platform gồm hai nút của bạn với cơ chế cách ly (fencing), bạn có thể xem qua các tùy chọn dòng lệnh và biến môi trường khả dụng cho tập lệnh `fencing_validator`. Tài liệu tham khảo này giúp bạn tùy chỉnh các phương thức kết nối, thiết lập thời gian chờ (timeout) khi thực thi và kiểm tra an toàn việc khởi động lại nút, từ đó đảm bảo cơ chế cách ly của bạn hoạt động tin cậy trước khi đưa hệ thống vào môi trường thực tế (production).

Bạn có thể xem các tùy chọn dòng lệnh khác nhau cho tập lệnh `fencing_validator` bằng cách chạy lệnh sau:

```bash
$ oc debug node/<node_name> --chroot /host /usr/local/bin/fencing_validator --help
```

Bảng dưới đây trình bày chi tiết các tùy chọn dòng lệnh cho lệnh oc debug node:

| Cờ           | Mô tả                                                                                                                                                                                                                                                                                                                      |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| --user       | Tên người dùng SSH để truy cập vào nút từ xa. Giá trị mặc định là `core`. Bạn có thể tùy chọn sử dụng biến môi trường `SSH_USER` để thiết lập giá trị này.                                                                                                                                                                 |
| --ssh-key    | Đường dẫn đến khóa riêng SSH. Bạn có thể tùy chọn sử dụng biến môi trường `SSH_KEY` để thiết lập giá trị này.                                                                                                                                                                                                              |
| --kubeconfig | Đường dẫn đến tệp `kubeconfig`. Bạn có thể tùy chọn sử dụng biến môi trường `KUBECONFIG` để thiết lập giá trị này.                                                                                                                                                                                                         |
| --transport  | Cách thức tập lệnh kết nối với nút khác. Các giá trị khả thi bao gồm `auto`, `ssh` và `ocdebug`. Giá trị mặc định là `auto`. Bạn cũng có thể sử dụng biến môi trường `TRANSPORT` để thiết lập giá trị này. Để biết thêm thông tin, hãy xem "Chế độ truyền tải cho tập lệnh kiểm chứng cách ly (fencing validator script)". |
| --timeout    | Thời gian chờ tối đa cho các vòng lặp khôi phục. Bạn có thể tùy chọn sử dụng biến môi trường `TIMEOUT` để thiết lập giá trị này. Theo mặc định, giá trị là 1200 giây, tương đương với 20 phút.                                                                                                                             |
| --hosts      | Cặp tên máy chủ hoặc địa chỉ IP của các nút, được phân tách bằng dấu phẩy.                                                                                                                                                                                                                                                 |
| --host-a     | Thiết lập nút đầu tiên một cách tường minh.                                                                                                                                                                                                                                                                                |
| --host-b     | Thiết lập nút thứ hai một cách tường minh.                                                                                                                                                                                                                                                                                 |
| --disruptive | Cho phép các kiểm thử cách ly (fencing) có tính phá hủy (gây khởi động lại các nút). Bạn có thể tùy chọn sử dụng biến môi trường `DISRUPTIVE` để thiết lập giá trị này.                                                                                                                                                    |
| --dry-run    | Hiển thị kết quả có thể xảy ra của tập lệnh `fencing_validator` mà không thực hiện các bước kiểm thực tế. Bạn có thể tùy chọn sử dụng biến môi trường `DRY_RUN` để thiết lập giá trị này.                                                                                                                                  |
| -h, --help   | Hiển thị thông tin sử dụng.                                                                                                                                                                                                                                                                                                |

Bạn có thể thiết lập tất cả các cờ (flag) bằng cách sử dụng các biến môi trường sau:

* `IP_A / IP_B`: thiết lập trực tiếp các địa chỉ máy chủ (host address)
* `OC_BIN`: đường dẫn tùy chỉnh đến tệp thực thi `oc`
* `OC_REQ_TIMEOUT`: thời gian chờ (timeout) cho mỗi lệnh gọi API. Giá trị mặc định là 10 giây. 
* `CMD_EXEC_TIMEOUT_SECS`: thời gian chờ (timeout) cho mỗi lệnh thực thi. Giá trị mặc định là 60 giây.

##### II.4.4.3. Tập lệnh kiểm chứng cơ chế cách ly (fencing validator) cho các kiểm tra không gây gián đoạn
Để đảm bảo cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly (fencing - TNF) duy trì tính sẵn sàng cao mà không gây nguy cơ ngừng hoạt động, bạn có thể chạy tập lệnh `fencing_validator` ở chế độ kiểm chứng (validation mode). Tập lệnh này thực hiện một loạt các kiểm tra trạng thái ở chế độ chỉ đọc (read-only) nhằm xác minh hạn ngạch cụm (quorum), trạng thái của các tiến trình nền (daemon) và trạng thái của thiết bị STONITH mà không làm gián đoạn các dịch vụ đang hoạt động.

Cách đơn giản nhất để chạy tập lệnh này là thông qua một phiên gỡ lỗi (debug session) trên một trong các nút thuộc mặt phẳng điều khiển (control plane node) bằng cách thực thi lệnh sau:

```bash
$ oc debug node/<node_name> --chroot /host /usr/local/bin/fencing_validator
```

Lệnh này không thực hiện khởi động lại hay cô lập (fence) bất kỳ nút (node) nào. Các bước kiểm tra này chỉ ở chế độ đọc (read-only) và an toàn để thực thi vào bất kỳ thời điểm nào. Lệnh sẽ tiến hành các bước kiểm tra không gây gián đoạn hệ thống như sau và báo cáo kết quả:

1. Kiểm tra phiên bản OpenShift - Xác nhận cụm (cluster) đang chạy OpenShift Container Platform phiên bản 4.20.0 trở lên. 
2. Kiểm tra số lượng nút - Xác nhận sự tồn tại của đúng 2 nút điều khiển (control-plane node). 
3. Kiểm tra kết nối truyền thông - Thiết lập kết nối tới cả hai nút (thông qua SSH hoặc `oc debug`). 
4. Kiểm tra thiết bị STONITH - Xác minh rằng các thiết bị STONITH đã có mặt và được kích hoạt trong Pacemaker. 
5. Trạng thái Pacemaker - Xác nhận cả hai nút đều hiển thị trạng thái ONLINE trong cụm Pacemaker. 
6. Trạng thái các dịch vụ (daemon) - Kiểm tra xem các dịch vụ `corosync`, `pacemaker` và `pcsd` có đang hoạt động trên cả hai nút hay không. 
7. Kiểm tra quorum của etcd - Xác minh rằng etcd có 2 thành viên bỏ phiếu ở trạng thái khỏe mạnh và cụm đạt đủ điều kiện quorum. 
8. Kiểm tra thông tin xác thực cho cơ chế cô lập (fencing secrets) - Xác nhận rằng các bí mật (secret) chứa thông tin xác thực cho cơ chế cô lập (được STONITH sử dụng để xác thực với BMC/IPMI) đã tồn tại và được liên kết chính xác với từng nút. 

Khi tất cả các bước kiểm tra không gây gián đoạn đều đạt yêu cầu, kết quả đầu ra sẽ có dạng như sau:

```
[INFO]
====
OpenShift version: 4.20.0 - OK [INFO]  Detected 2 control-plane nodes [INFO]  Transport: ssh [OK]    STONITH devices found and enabled [OK]    Both nodes ONLINE in Pacemaker [OK]    All daemons healthy on both nodes [OK]    etcd quorum healthy (2/2 voters) [OK]    Fencing secrets correctly bound [INFO]  All non-disruptive checks passed When something fails:
====
```

+ Khi các kiểm tra không gây gián đoạn thất bại, kết quả đầu ra sẽ có dạng như sau:

```
[INFO]
====
OpenShift version: 4.20.0 - OK [INFO]  Detected 2 control-plane nodes [INFO]  Transport: ssh [ERROR] No STONITH devices found - fencing is not configured
====
```

##### II.4.4.4. Tập lệnh kiểm chứng cơ chế cách ly (fencing validator) cho các kiểm tra gây gián đoạn
Bạn có thể kiểm chứng khả năng chống chịu của cụm (cluster) và thực hiện các kiểm tra gây gián đoạn từ một nút ngang hàng (peer node) bằng cách sử dụng tập lệnh `fencing_validator`. Thông qua việc mô phỏng các lỗi này, bạn có thể đảm bảo rằng môi trường có tính sẵn sàng cao (high-availability) của mình thực hiện đúng quy trình cách ly và phục hồi sau sự cố.

Bạn có thể kích hoạt cơ chế STONITH (viết tắt của "Shoot The Other Node In The Head" – tạm dịch: "vô hiệu hóa hoàn toàn nút khác") đối với nút gặp lỗi, nhằm cắt quyền truy cập của nút đó vào các tài nguyên dùng chung, từ đó ngăn ngừa tình trạng hỏng dữ liệu bằng cách chạy lệnh sau:

```bash
$ pcs stonith fence <node>
```

Bạn có thể kiểm tra xem thiết lập cụm OpenShift Container Platform gồm hai nút với cơ chế cách ly (fencing) có thực sự hoạt động hay không bằng cách chạy lệnh sau:

```bash
$ oc debug node/<node_name> --chroot /host /usr/local/bin/fencing_validator --disruptive
```

> **Cảnh báo:** Tham số `--disruptive` thực hiện cô lập (fence) từng nút trong mặt phẳng điều khiển (control plane node) một cách tuần tự, sau đó xác minh quá trình khôi phục. Tham số này kích hoạt các thao tác cô lập kiểu STONITH, chẳng hạn như tắt/bật nguồn (power cycle) hoặc khởi động lại máy ảo (VM reset). Quá trình này không thực hiện tắt máy theo trình tự an toàn (graceful shutdown) và sẽ gây gián đoạn tạm thời đối với các khối lượng công việc (workload) đang chạy.

Khi sử dụng tham số `--disruptive`, tập lệnh kiểm chứng cơ chế cô lập (fencing validator script) sẽ thực hiện các bước kiểm tra sau:

1. Cô lập Nút A (Fence Node A) – Kích hoạt STONITH để khởi động lại nút đầu tiên trong mặt phẳng điều khiển. 
2. Xác minh trạng thái NotReady – Chờ Kubernetes báo cáo Nút A ở trạng thái `NotReady`, xác nhận rằng việc khởi động lại đã diễn ra. 
3. Xác minh quá trình khôi phục – Chờ Nút A quay trở lại trạng thái `Ready`, tham gia lại cụm Pacemaker với trạng thái `ONLINE`, đồng thời chờ etcd thiết lập lại cơ chế đa số (quorum). 
4. Kiểm tra daemon sau khôi phục – Kiểm tra lại để đảm bảo tất cả các daemon đều hoạt động bình thường sau khi khôi phục. 
5. Cô lập Nút B (Fence Node B) – Kích hoạt STONITH để khởi động lại nút thứ hai. 
6. Xác minh trạng thái NotReady – Chờ Kubernetes báo cáo Nút B ở trạng thái `NotReady`, xác nhận rằng việc khởi động lại đã diễn ra. 
7. Xác minh quá trình khôi phục – Chờ Nút B quay trở lại trạng thái `Ready`, tham gia lại cụm Pacemaker với trạng thái `ONLINE`, đồng thời chờ etcd thiết lập lại cơ chế đa số (quorum). 
8. Kiểm tra daemon sau khôi phục – Kiểm tra lại để đảm bảo tất cả các daemon đều hoạt động bình thường sau khi khôi phục.

##### II.4.4.5. Các mã thoát (exit code) cho tập lệnh fencing-validator
Tập lệnh `fencing_validator` sử dụng các mã thoát cụ thể, cho phép các công cụ tự động hóa và hỗ trợ xác định vấn đề đã xảy ra thông qua lập trình.

Bảng dưới đây liệt kê các mã thoát cụ thể mà tập lệnh `fencing_validator` trả về, trong đó mỗi giá trị số tương ứng với một trạng thái chẩn đoán nhất định nhằm hỗ trợ quá trình khắc phục sự cố tự động.

| Mã thoát | Mô tả                                                                                                          |
|----------|----------------------------------------------------------------------------------------------------------------|
| 0        | Tất cả các bước kiểm tra đều đạt                                                                               |
| 1        | Lỗi chung hoặc lỗi không mong đợi                                                                              |
| 20       | Các thiết bị STONITH bị thiếu hoặc chưa được kích hoạt                                                         |
| 21       | Một hoặc cả hai nút không ở trạng thái ONLINE trong Pacemaker                                                  |
| 22       | Một hoặc nhiều tiến trình nền bắt buộc (corosync, pacemaker, pcsd) hiện không chạy                             |
| 23       | etcd không đạt đủ số phiếu cần thiết (quorum) hoặc không phải tất cả các thành viên đều ở trạng thái khỏe mạnh |
| 26       | Các bí mật về hàng rào (fencing secrets) bị thiếu hoặc không khớp với các nút (nodes) dự kiến.                 |

##### II.4.4.6. Chế độ truyền tải (transport mode) cho tập lệnh kiểm chứng hàng rào (fencing validator)
Tập lệnh `fencing_validator` kết nối với các nút trong mặt phẳng điều khiển (control-plane nodes) để thực thi các lệnh kiểm chứng. Hãy sử dụng cờ `--transport` hoặc biến môi trường `TRANSPORT` để xác định phương thức kết nối.

Cờ `--transport` hỗ trợ các tùy chọn sau:

* `auto`: Đây là tùy chọn mặc định. Tập lệnh trước tiên sẽ thử kết nối qua SSH tới cả hai nút. Nếu kết nối SSH thành công, nó sẽ sử dụng SSH cho phiên làm việc đó. Nếu SSH thất bại, tập lệnh sẽ chuyển sang sử dụng `oc debug`. Nếu cả hai phương thức đều không hoạt động trên các nút, tập lệnh sẽ thoát và báo lỗi. 
* `ssh`: Tùy chọn này sử dụng SSH để kết nối với tư cách là người dùng được chỉ định bởi tham số `--user`. Giá trị mặc định cho `--user` là `core`. 
  * Quyền hạn: Yêu cầu quyền truy cập `sudo` không cần mật khẩu (password-less sudo) trên tất cả các nút. 
  * Tự động hóa: Tập lệnh chạy ở chế độ `BatchMode`, vô hiệu hóa các lời nhắc tương tác (interactive prompts) và bỏ qua bước kiểm tra khóa máy chủ (host-key checking). 
  * Xác thực: Sử dụng cờ `--ssh-key` để cung cấp khóa SSH cụ thể cho tất cả các kết nối. 
* `oc debug`: Kết nối bằng cách thực thi lệnh sau trên mỗi nút:

```bash
$ oc debug node/<node> --chroot /host
```

Bạn không cần quyền truy cập SSH. Tập lệnh `fencing_validator` chỉ yêu cầu một tệp `KUBECONFIG` hợp lệ với các đặc quyền `cluster-admin`.

Đối với các kiểm tra không gây gián đoạn, cả hai phương thức truyền lệnh (transport) đều hoạt động theo cùng một cách. Tuy nhiên, chế độ truyền lệnh lại đóng vai trò quan trọng khi sử dụng tùy chọn `--disruptive`. Trong các bài kiểm tra này, tập lệnh sẽ gửi lệnh cách ly (fence command) theo cơ chế bất đồng bộ (asynchronously) thông qua `systemd-run`, hoặc sử dụng `nohup` làm phương án dự phòng. Phương thức "gửi rồi quên" (fire-and-forget) này đảm bảo rằng lệnh vẫn được thực thi hoàn tất ngay cả khi phiên làm việc `oc debug` bị chấm dứt trong quá trình nút (node) đó bị cách ly.
