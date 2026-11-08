Cài đặt OpenShift
-----------------

# Cài đặt OpenShift Container Platform trên hạ tầng vật lý (bare metal)
## I. Chuẩn bị cho việc cài đặt cụm trên hạ tầng vật lý (bare-metal)
Xem xét các phương pháp khác nhau để cài đặt OpenShift Container Platform trên hạ tầng vật lý (bare metal) và chuẩn bị môi trường của bạn cho quá trình cài đặt.

### I.1. Các điều kiện tiên quyết

* Bạn đã đọc thông tin chi tiết về quy trình cài đặt và cập nhật OpenShift Container Platform. 
* Bạn đã đọc tài liệu hướng dẫn về cách chọn phương pháp cài đặt cụm (cluster) và chuẩn bị cụm đó cho người dùng. 
* Bạn đã đọc tài liệu về các trường hợp sử dụng được hỗ trợ và không được hỗ trợ đối với plugin mạng OVN-Kubernetes.

### I.2. Lựa chọn phương pháp cài đặt OpenShift Container Platform trên hạ tầng vật lý (bare metal)
Chương trình cài đặt OpenShift Container Platform cung cấp bốn phương pháp để triển khai một cụm (cluster):

* **Tương tác (Interactive)**: Bạn có thể triển khai cụm thông qua công cụ Assisted Installer dựa trên nền tảng web. Đây là phương pháp được khuyến nghị cho các cụm có kết nối mạng với internet. Assisted Installer là cách dễ nhất để cài đặt OpenShift Container Platform; công cụ này cung cấp các thiết lập mặc định thông minh và thực hiện kiểm tra điều kiện tiên quyết trước khi tiến hành cài đặt cụm. Ngoài ra, nó còn cung cấp giao diện lập trình ứng dụng (RESTful API) phục vụ cho các kịch bản tự động hóa và cấu hình nâng cao. 
* **Dựa trên tác nhân cục bộ (Local Agent-based)**: Bạn có thể triển khai cụm tại chỗ bằng trình cài đặt dựa trên tác nhân (agent-based installer), phù hợp cho các môi trường mạng bị cô lập (air-gapped) hoặc bị hạn chế kết nối. Phương pháp này mang lại nhiều lợi ích tương tự như Assisted Installer, nhưng yêu cầu bạn phải tải xuống và cấu hình trình cài đặt trước. Việc cấu hình được thực hiện thông qua giao diện dòng lệnh (CLI). Đây là lựa chọn lý tưởng cho các mạng bị cô lập hoặc hạn chế kết nối. 
* **Tự động hóa (Automated)**: Bạn có thể triển khai cụm trên hạ tầng do trình cài đặt cung cấp và duy trì. Chương trình cài đặt sử dụng bộ điều khiển quản lý bo mạch chủ (BMC) của từng máy chủ trong cụm để thực hiện việc cung cấp tài nguyên (provisioning). Phương pháp này áp dụng được cho cả môi trường có kết nối internet lẫn môi trường bị cô lập hoặc hạn chế kết nối. 
* **Toàn quyền kiểm soát (Full control)**: Bạn có thể triển khai cụm trên hạ tầng do chính mình chuẩn bị và duy trì, mang lại khả năng tùy chỉnh tối đa. Phương pháp này cũng áp dụng được cho cả môi trường có kết nối internet lẫn môi trường bị cô lập hoặc hạn chế kết nối.

Các cụm này có những đặc điểm sau:

* Hạ tầng có tính sẵn sàng cao, không tồn tại điểm lỗi duy nhất (single point of failure), được thiết lập mặc định. 
* Người quản trị duy trì quyền kiểm soát đối với việc cập nhật: quyết định những bản cập nhật nào sẽ được áp dụng và thời điểm áp dụng.

### I.3. Cài đặt cụm trên cơ sở hạ tầng do trình cài đặt cung cấp
Bạn có thể cài đặt một cụm trên cơ sở hạ tầng máy vật lý (bare-metal) được cung cấp bởi chương trình cài đặt OpenShift Container Platform, thông qua phương pháp sau:

Cài đặt cụm trên máy vật lý với cơ sở hạ tầng do trình cài đặt cung cấp
Bạn có thể cài đặt OpenShift Container Platform trên các máy vật lý bằng cách sử dụng cơ chế cung cấp hạ tầng từ trình cài đặt.

### I.4. Cài đặt cụm trên cơ sở hạ tầng do người dùng cung cấp
Bạn có thể cài đặt một cụm trên cơ sở hạ tầng vật lý (bare-metal) do chính bạn cung cấp, bằng cách sử dụng một trong các phương pháp sau:

* **Cài đặt cụm trên cơ sở hạ tầng vật lý do người dùng cung cấp**

  Bạn có thể cài đặt OpenShift Container Platform trên cơ sở hạ tầng vật lý do bạn cung cấp. Đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp, bạn phải triển khai tất cả các máy chủ cần thiết.

* **Cài đặt cụm trên cơ sở hạ tầng vật lý do người dùng cung cấp với các tùy chỉnh mạng**

  Bạn có thể cài đặt cụm trên cơ sở hạ tầng vật lý do người dùng cung cấp với các tùy chỉnh về mạng. Bằng cách tùy chỉnh cấu hình mạng, cụm của bạn có thể cùng tồn tại với các phân bổ địa chỉ IP hiện có trong môi trường của bạn, đồng thời tích hợp với các cấu hình MTU và VXLAN sẵn có. Hầu hết các tùy chỉnh mạng cần được thực hiện ngay trong giai đoạn cài đặt.

**Cài đặt cụm trên cơ sở hạ tầng vật lý do người dùng cung cấp trong mạng bị hạn chế**

  Bạn có thể cài đặt cụm trên cơ sở hạ tầng vật lý do người dùng cung cấp trong một mạng bị hạn chế hoặc mạng không kết nối với internet (disconnected network) bằng cách sử dụng registry phản chiếu (mirror registry). Bạn cũng có thể sử dụng phương pháp cài đặt này để đảm bảo rằng các cụm của mình chỉ sử dụng các hình ảnh container (container image) đáp ứng các quy định kiểm soát của tổ chức bạn đối với nội dung từ bên ngoài.

### I.5. Các yêu cầu cài đặt cụm bare-metal cho OpenShift Virtualization
Hãy định cấu hình cụm bare-metal của bạn một cách chính xác trong quá trình cài đặt để hỗ trợ OpenShift Virtualization, vì một số thiết lập bắt buộc sẽ không thể thay đổi sau khi đã hoàn tất cài đặt.

#### I.5.1. Các yêu cầu về tính sẵn sàng cao (High Availability - HA) đối với OpenShift Virtualization
Khi thảo luận về các tính năng sẵn sàng cao (HA) trong ngữ cảnh của OpenShift Virtualization, thuật ngữ này chỉ đề cập đến mô hình sao chép (replication model) của các thành phần cốt lõi trong cụm (cluster). Mô hình này được xác định bởi các trường `controlPlaneTopology` và `infrastructureTopology` trong tài nguyên tùy chỉnh (Custom Resource - CR) có tên là `Infrastructure`. Việc thiết lập các trường này thành `HighlyAvailable` sẽ cung cấp cơ chế dự phòng cho các thành phần; cơ chế này khác biệt với tính năng HA dành cho ứng dụng trên toàn bộ cụm. Ngược lại, nếu thiết lập các trường này thành `SingleReplica`, cơ chế dự phòng thành phần sẽ bị vô hiệu hóa, đồng nghĩa với việc các tính năng HA của OpenShift Virtualization cũng không khả dụng.

Nếu bạn dự định sử dụng các tính năng HA của OpenShift Virtualization, bạn bắt buộc phải có ba nút (node) thuộc mặt phẳng điều khiển (control plane) tại thời điểm cài đặt cụm. Trạng thái `controlPlaneTopology` trong tài nguyên `Infrastructure` của cụm phải được đặt là `HighlyAvailable`.

> **Lưu ý:** Bạn có thể cài đặt OpenShift Virtualization trên một cụm chỉ gồm một nút duy nhất (single-node cluster), nhưng cấu hình này không hỗ trợ các tính năng HA.

#### I.5.2. Các yêu cầu đối với tính năng di chuyển trực tiếp (live migration) trong OpenShift Virtualization
* Nếu dự định sử dụng tính năng di chuyển trực tiếp, bạn cần có nhiều worker node. Trạng thái `infrastructureTopology` trong tài nguyên tùy chỉnh (CR) `Infrastructure` của cụm (cluster) phải được thiết lập là `HighlyAvailable`. Khuyến nghị nên có tối thiểu ba worker node. 

  > **Lưu ý:** Bạn có thể cài đặt OpenShift Virtualization trên một cụm chỉ gồm một node (single-node cluster), nhưng cấu hình OpenShift một node này không hỗ trợ tính năng di chuyển trực tiếp. 

* Tính năng di chuyển trực tiếp yêu cầu phải có bộ lưu trữ dùng chung (shared storage). Bộ lưu trữ dành cho OpenShift Virtualization phải hỗ trợ và sử dụng chế độ truy cập `ReadWriteMany` (RWX).

#### I.5.3. Các yêu cầu đối với SR-IOV trong OpenShift Virtualization
Nếu bạn dự định sử dụng Single Root I/O Virtualization (SR-IOV), hãy đảm bảo rằng các bộ điều khiển giao diện mạng (NIC) của bạn được OpenShift Container Platform hỗ trợ.

### I.6. Phân chia NIC cho các thiết bị SR-IOV
Bạn có thể chia một NIC hai cổng tốc độ cao thành nhiều hàm ảo (Virtual Function - VF) và kích hoạt SR-IOV để hỗ trợ tính sẵn sàng cao thông qua cơ chế liên kết (bonding) sử dụng Giao thức điều khiển tập hợp liên kết (Link Aggregation Control Protocol - LACP).

Tính năng này hỗ trợ sử dụng các liên kết (bond) để đảm bảo tính sẵn sàng cao thông qua Giao thức điều khiển tập hợp liên kết (LACP).

> **Lưu ý:** Mỗi NIC vật lý chỉ có thể được cấu hình với một liên kết LACP duy nhất.

Một cụm OpenShift Container Platform có thể được triển khai trên một giao diện liên kết (bond interface) bao gồm 2 VF trên 2 hàm vật lý (Physical Function - PF) thông qua các phương thức sau:

* Trình cài đặt dựa trên tác nhân (Agent-based installer)
  > **Lưu ý:** Phiên bản tối thiểu yêu cầu của nmstate là:

* 1.4.2-4 đối với các phiên bản RHEL 8
* 2.2.7 đối với các phiên bản RHEL 9
* Cài đặt trên hạ tầng do trình cài đặt cung cấp (Installer-provisioned infrastructure installation)
* Cài đặt trên hạ tầng do người dùng cung cấp (User-provisioned infrastructure installation)

## 2. Cơ sở hạ tầng do người dùng cung cấp
### 2.1. Cài đặt cụm do người dùng cung cấp trên hạ tầng vật lý (bare metal)
Để tối ưu hóa hiệu suất và duy trì quyền kiểm soát tốt hơn đối với phần cứng trong OpenShift Container Platform 4.22, bạn có thể cài đặt một cụm trên hạ tầng vật lý (bare metal) do chính bạn cung cấp.

> **Quan trọng:** Mặc dù bạn có thể thực hiện theo quy trình này để triển khai cụm trên các môi trường ảo hóa hoặc môi trường đám mây, bạn cần lưu ý đến những yếu tố bổ sung đối với các nền tảng không phải hạ tầng vật lý. Hãy xem kỹ thông tin trong các hướng dẫn về việc triển khai OpenShift Container Platform trên những nền tảng chưa được kiểm thử trước khi tiến hành cài đặt cụm OpenShift Container Platform trong các môi trường như vậy.

#### 2.1.1. Các điều kiện tiên quyết
* Bạn đã xem qua các thông tin chi tiết về quy trình cài đặt và cập nhật OpenShift Container Platform. 
* Bạn đã đọc tài liệu hướng dẫn về cách chọn phương thức cài đặt cụm (cluster) và chuẩn bị cụm đó cho người dùng. 
* Nếu sử dụng tường lửa, bạn đã cấu hình để cho phép truy cập vào các trang web mà cụm của bạn cần kết nối tới. 

#### 2.1.2. Truy cập Internet cho OpenShift Container Platform
Trong OpenShift Container Platform 4.22, bạn cần có quyền truy cập Internet để cài đặt cụm (cluster) của mình.

Bạn bắt buộc phải có quyền truy cập Internet để thực hiện các thao tác sau:

* Truy cập Red Hat Hybrid Cloud Console để tải xuống chương trình cài đặt và quản lý đăng ký (subscription). Nếu cụm có quyền truy cập Internet và bạn không tắt tính năng Telemetry, dịch vụ này sẽ tự động cấp quyền (entitlement) cho cụm của bạn. 
* Truy cập Quay.io để lấy các gói cần thiết cho việc cài đặt cụm. 
* Lấy các gói cần thiết để thực hiện cập nhật cụm.

> **Quan trọng:** Nếu cụm của bạn không thể truy cập Internet trực tiếp, bạn có thể thực hiện cài đặt trong môi trường mạng bị hạn chế (restricted network installation) trên một số loại cơ sở hạ tầng mà bạn cung cấp. Trong quá trình này, bạn sẽ tải xuống các nội dung cần thiết và sử dụng chúng để nạp vào một registry phản chiếu (mirror registry) chứa các gói cài đặt. Với một số kiểu cài đặt, môi trường nơi bạn triển khai cụm sẽ không yêu cầu quyền truy cập Internet. Trước khi cập nhật cụm, bạn cần cập nhật nội dung của registry phản chiếu đó.

##### 2.1.2.1. Các máy cần thiết để cài đặt cụm (cluster)
Bạn phải xác định số lượng máy hoặc máy chủ tối thiểu cần thiết cho cụm của mình, nhằm đảm bảo cụm vẫn duy trì sự ổn định ngay cả khi một nút (node) gặp sự cố.

Các cụm OpenShift Container Platform với quy mô nhỏ nhất yêu cầu các máy chủ sau:

> **Quan trọng:** Đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải triển khai tất cả các máy cần thiết.

**Bảng 2.1. Số lượng máy chủ tối thiểu cần thiết**

| Máy chủ                                                                  | Mô tả                                                                                                                                                                                                  |
|--------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Một máy khởi động tạm thời                                               | Cụm yêu cầu phải có máy bootstrap để triển khai cụm OpenShift Container Platform trên ba máy thuộc mặt phẳng điều khiển (control plane). Bạn có thể loại bỏ máy bootstrap sau khi đã cài đặt xong cụm. |
| Ba máy thuộc mặt phẳng điều khiển                                        | Các máy thuộc mặt phẳng điều khiển (control plane) chạy các dịch vụ Kubernetes và OpenShift Container Platform, tạo nên mặt phẳng điều khiển đó.                                                       |
| Ít nhất hai máy tính, còn được gọi là các máy thực thi (worker machine). | Các khối lượng công việc do người dùng OpenShift Container Platform yêu cầu sẽ chạy trên các máy tính toán.                                                                                            |

> **Lưu ý:** Một ngoại lệ là bạn có thể vận hành cụm máy chủ bare metal (phần cứng vật lý) mà không cần máy chủ tính toán (compute machine) nào, chỉ bao gồm ba máy chủ điều khiển (control plane machine). Cấu hình này tạo ra các cụm nhỏ gọn, sử dụng tài nguyên hiệu quả hơn, phù hợp để các quản trị viên và nhà phát triển thực hiện kiểm thử, phát triển ứng dụng hoặc triển khai thực tế. Việc vận hành chỉ với một máy chủ tính toán là không được hỗ trợ.

**Quan trọng**: Để duy trì tính sẵn sàng cao cho cụm máy chủ, hãy sử dụng các máy chủ vật lý riêng biệt cho các thành phần trong cụm này.

Các máy chủ khởi động (bootstrap machine) và máy chủ điều khiển (control plane machine) bắt buộc phải sử dụng hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS). Trong khi đó, các máy chủ tính toán (compute machine) có thể sử dụng Red Hat Enterprise Linux CoreOS (RHCOS) hoặc Red Hat Enterprise Linux (RHEL) phiên bản 8.6 trở lên.

RHCOS được xây dựng dựa trên Red Hat Enterprise Linux (RHEL) 9.8 và kế thừa toàn bộ các chứng nhận cũng như yêu cầu về phần cứng của hệ điều hành này.

##### 2.1.2.2. Các yêu cầu tối thiểu về tài nguyên để cài đặt cụm (cluster)
Để đảm bảo cụm OpenShift Container Platform của bạn vận hành đúng như mong đợi, mỗi máy trong cụm phải đáp ứng các yêu cầu tối thiểu về CPU, bộ nhớ và lưu trữ.

**Bảng 2.2. Các yêu cầu tối thiểu về tài nguyên**

| Máy chủ              | Hệ điều hành | CPU | RAM   | Lưu trữ | IOPS |
|----------------------|--------------|-----|-------|---------|------|
| Bootstrap            | RHCOS        | 4   | 16 GB | 100 GB  | 300  |
| Mặt phẳng điều khiển | RHCOS        | 4   | 16 GB | 100 GB  | 300  |
| Tính toán            | RHCOS        | 2   | 8 GB  | 100 GB  | 300  |

* Một CPU tương đương với một nhân vật lý khi tính năng đa luồng đồng thời (SMT) – hay còn gọi là Hyper-Threading – không được kích hoạt. Khi tính năng này được kích hoạt, hãy sử dụng công thức sau để tính toán tỷ lệ tương ứng: (số luồng trên mỗi nhân × số nhân) × số socket = số CPU. 
* OpenShift Container Platform và Kubernetes rất nhạy cảm với hiệu năng ổ đĩa; do đó, Red Hat khuyến nghị sử dụng hệ thống lưu trữ có tốc độ cao, đặc biệt là đối với etcd trên các nút mặt phẳng điều khiển (control plane nodes) – nơi yêu cầu thời gian thực hiện fsync (p99) đạt mức 10 ms. Trên nhiều nền tảng đám mây, dung lượng lưu trữ và chỉ số IOPS thường tỷ lệ thuận với nhau; vì vậy, bạn có thể cần cấp phát thêm dung lượng lưu trữ để đạt được hiệu năng cần thiết. 
* Đối với tất cả các mô hình cài đặt do người dùng tự cung cấp (user-provisioned installations), nếu bạn chọn sử dụng các máy tính toán (compute machines) chạy hệ điều hành RHEL trong cụm (cluster) của mình, bạn sẽ chịu trách nhiệm quản lý vòng đời và bảo trì hệ điều hành đó. Điều này bao gồm việc thực hiện các bản cập nhật hệ thống, áp dụng các bản vá lỗi và hoàn tất mọi tác vụ cần thiết khác. OpenShift Container Platform phiên bản 4.10 trở về sau không hỗ trợ các máy tính toán chạy RHEL 7.

> **Lưu ý:** Trong OpenShift Container Platform phiên bản 4.22, RHCOS sử dụng RHEL phiên bản 9.8, với các yêu cầu cập nhật về kiến trúc vi xử lý (micro-architecture). Mỗi kiến trúc yêu cầu các tập lệnh (Instruction Set Architecture - ISA) tối thiểu như sau:
> 
> * Kiến trúc x86-64 yêu cầu ISA x86-64-v2
> * Kiến trúc ARM64 yêu cầu ISA ARMv8.0-A
> * Kiến trúc ppc64le yêu cầu ISA IBM® Power9
> * Kiến trúc s390x yêu cầu ISA IBM® z14

Nếu loại instance trên nền tảng của bạn đáp ứng các yêu cầu tối thiểu dành cho các máy trong cụm, thì loại instance đó được hỗ trợ để sử dụng trong OpenShift Container Platform.

##### 2.1.2.3. Quản lý các yêu cầu ký chứng chỉ (Certificate Signing Requests - CSR)
Trên cơ sở hạ tầng do người dùng cung cấp, nếu cụm (cluster) của bạn bị hạn chế quyền truy cập vào các cơ chế quản lý máy tự động, bạn phải triển khai một quy trình để phê duyệt các yêu cầu ký chứng chỉ (CSR) của cụm sau khi hoàn tất cài đặt.

Thành phần `kube-controller-manager` chỉ thực hiện phê duyệt các CSR dành cho máy khách (client) của `kubelet`. Cơ chế `machine-approver` không thể đảm bảo tính hợp lệ của chứng chỉ phục vụ (serving certificate) mà các thông tin xác thực (credentials) của `kubelet` yêu cầu, vì nó không thể xác nhận liệu yêu cầu đó có thực sự xuất phát từ đúng thiết bị (máy) hay không. Do đó, bạn cần tìm kiếm và triển khai một phương thức để xác minh tính hợp lệ của các yêu cầu cấp chứng chỉ phục vụ cho `kubelet`, đồng thời thực hiện phê duyệt các yêu cầu đó.

##### 2.1.2.4. Các yêu cầu về mạng đối với hạ tầng do người dùng cung cấp
Bạn phải định cấu hình mạng cho tất cả các máy chạy Red Hat Enterprise Linux CoreOS (RHCOS) trong giai đoạn initramfs khi khởi động, để các máy này có thể truy xuất được các tệp cấu hình Ignition của chúng.

Trong quá trình khởi động ban đầu, các máy cần được thiết lập địa chỉ IP, thông qua máy chủ DHCP hoặc bằng cách định cấu hình tĩnh thông qua các tùy chọn khởi động (boot options). Sau khi kết nối mạng được thiết lập, các máy sẽ tải xuống các tệp cấu hình Ignition từ một máy chủ HTTP hoặc HTTPS. Các tệp cấu hình Ignition này sau đó được sử dụng để thiết lập trạng thái cụ thể cho từng máy. Sau khi cài đặt, thành phần Machine Config Operator sẽ thực hiện thêm các thay đổi khác trên các máy, chẳng hạn như áp dụng các chứng chỉ hoặc khóa mới.

> **Lưu ý**
> 
> * Bạn nên cân nhắc sử dụng máy chủ DHCP để quản lý lâu dài các máy trong cụm (cluster). Hãy đảm bảo rằng máy chủ DHCP được định cấu hình để cung cấp các địa chỉ IP cố định, thông tin máy chủ DNS và tên máy chủ (hostname) cho các máy trong cụm. 
> * Nếu hạ tầng do bạn cung cấp không có dịch vụ DHCP, bạn có thể trực tiếp cung cấp cấu hình mạng IP và địa chỉ máy chủ DNS cho các nút (node) tại thời điểm cài đặt RHCOS. Các thông tin này có thể được truyền dưới dạng tham số khởi động (boot arguments) nếu bạn cài đặt từ tệp ảnh ISO. Hãy xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform" để biết thêm thông tin về việc cấp phát IP tĩnh và các tùy chọn mạng nâng cao.

Máy chủ API Kubernetes (Kubernetes API server) phải có khả năng phân giải tên của các nút trong cụm. Nếu các máy chủ API và các nút worker nằm trong các vùng (zone) khác nhau, bạn có thể định cấu hình vùng tìm kiếm DNS mặc định (default DNS search zone) để cho phép máy chủ API phân giải tên các nút. Một phương pháp khác được hỗ trợ là luôn tham chiếu đến các máy chủ (host) bằng tên miền đầy đủ (fully-qualified domain name - FQDN) trong cả các đối tượng nút (node objects) và tất cả các yêu cầu DNS.

###### 2.1.2.4.1. Thiết lập tên máy chủ (hostname) cho các nút trong cụm thông qua DHCP
Trên các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS), tên máy chủ (hostname) được thiết lập thông qua NetworkManager. Theo mặc định, các máy này nhận tên máy chủ thông qua DHCP. Nếu tên máy chủ không được cung cấp qua DHCP, không được thiết lập tĩnh thông qua các tham số nhân (kernel arguments), hoặc không được cấu hình bằng phương thức khác, thì hệ thống sẽ xác định tên máy chủ thông qua truy vấn DNS ngược (reverse DNS lookup). Quá trình truy vấn DNS ngược diễn ra sau khi mạng đã được khởi tạo trên một nút và có thể mất một khoảng thời gian để hoàn tất. Các dịch vụ hệ thống khác có thể khởi động trước thời điểm này và nhận diện tên máy chủ là "localhost" hoặc một giá trị tương tự. Bạn có thể tránh tình trạng này bằng cách sử dụng DHCP để cung cấp tên máy chủ cho từng nút trong cụm.

Ngoài ra, việc thiết lập tên máy chủ thông qua DHCP còn giúp ngăn ngừa các lỗi cấu hình thủ công đối với bản ghi DNS trong các môi trường triển khai kiến trúc DNS "split-horizon" (DNS phân tách theo phạm vi truy cập).

###### 2.1.2.4.2. Các yêu cầu về kết nối mạng
Bạn phải cấu hình kết nối mạng giữa các máy để cho phép các thành phần trong cụm OpenShift Container Platform có thể giao tiếp với nhau. Mỗi máy phải có khả năng phân giải tên máy chủ (hostname) của tất cả các máy khác trong cụm.

Phần này cung cấp thông tin chi tiết về các cổng (port) cần thiết.

> **Quan trọng:** Trong các môi trường OpenShift Container Platform có kết nối mạng, tất cả các nút (node) đều cần có quyền truy cập internet để tải xuống các image cho các container của nền tảng, đồng thời gửi dữ liệu đo từ xa (telemetry data) về cho Red Hat.

**Bảng 2.3. Các cổng được sử dụng cho hoạt động giao tiếp giữa tất cả các máy với nhau**

| Giao thức | Cổng            | Mô tả                                                                                                                                         |
|-----------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| ICMP      | N/A             | Kiểm tra khả năng kết nối mạng                                                                                                                |
| TCP       | `1936`          | Các chỉ số                                                                                                                                    |
| TCP       | `9000`-`9999`   | Các dịch vụ ở cấp độ máy chủ (host), bao gồm node exporter trên các cổng `9100`-`9101` và Cluster Version Operator trên cổng `9099`.          |
| TCP       | `10250`-`10259` | Các cổng mặc định mà Kubernetes dành riêng                                                                                                    |
| TCP       | `22623`         | Cổng này xử lý lưu lượng truy cập từ Machine Config Server và điều hướng lưu lượng đó đến các máy thuộc mặt phẳng điều khiển (control plane). |
| UDP       | `6081`          | Geneve                                                                                                                                        |
| UDP       | `9000`-`9999`   | Các dịch vụ ở cấp độ máy chủ, bao gồm node exporter trên các cổng `9100`-`9101`.                                                              |
| UDP       | `500`           | Các gói tin IPsec IKE                                                                                                                         |
| UDP       | `4500`          | Các gói tin IPsec NAT-T                                                                                                                       |
| UDP       | `123`           | Network Time Protocol (NTP) sử dụng cổng UDP `123`. Nếu máy chủ thời gian NTP bên ngoài được cấu hình, bạn phải mở cổng UDP `123`.            |
| TCP/UDP   | `30000`-`32767` | Cổng nút Kubernetes                                                                                                                           |
| ESP       | N/A             | Giao thức đóng gói bảo mật (ESP) trong IPsec                                                                                                  |

**Bảng 2.4. Các cổng được sử dụng cho hoạt động giao tiếp giữa tất cả các máy với mặt phẳng điều khiển (control plane)**

| Giao thức | Cổng   | Mô tả          |
|-----------|--------|----------------|
| TCP       | `6443` | Kubernetes API |

**Bảng 2.5. Các cổng được sử dụng cho hoạt động giao tiếp giữa các máy trong mặt phẳng điều khiển (control plane)**

| Giao thức | Cổng          | Mô tả                                                           |
|-----------|---------------|-----------------------------------------------------------------|
| TCP       | `2379`-`2380` | Các cổng dành cho máy chủ và các nút ngang hàng (peer) của etcd |

###### 2.1.2.4.3. Cấu hình NTP cho cơ sở hạ tầng do người dùng cung cấp
Theo mặc định, các cụm OpenShift Container Platform được cấu hình để sử dụng máy chủ Giao thức Thời gian Mạng (NTP) công cộng. Nếu bạn muốn sử dụng máy chủ NTP nội bộ của doanh nghiệp, hoặc nếu cụm của bạn được triển khai trong một mạng không kết nối với internet (disconnected network), bạn có thể cấu hình cụm để sử dụng một máy chủ thời gian cụ thể. Để biết thêm thông tin, hãy xem tài liệu về cách cấu hình dịch vụ thời gian chrony.

Nếu máy chủ DHCP cung cấp thông tin về máy chủ NTP, dịch vụ thời gian chrony trên các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) sẽ đọc thông tin đó và có thể đồng bộ hóa đồng hồ hệ thống với các máy chủ NTP này.

##### 2.1.2.5. Các yêu cầu về DNS do người dùng cung cấp
Trong các triển khai OpenShift Container Platform, bạn phải đảm bảo rằng các thành phần của cụm (cluster) đáp ứng những tiêu chí nhất định về phân giải tên DNS, phục vụ cho các mục đích như giao tiếp nội bộ, xác thực chứng chỉ và tự động phát hiện nút (node).

Dưới đây là danh sách các thành phần cụm bắt buộc:

* Kubernetes API
* Wildcard (tên miền đại diện) cho ứng dụng trong OpenShift Container Platform
* Các máy bootstrap và các máy thuộc mặt phẳng điều khiển (control plane)
* Các máy tính toán (compute machines)

Phân giải DNS ngược (reverse DNS resolution) cũng là yêu cầu bắt buộc đối với Kubernetes API, máy bootstrap, các máy thuộc mặt phẳng điều khiển và các máy tính toán.

Các bản ghi DNS loại A/AAAA hoặc CNAME được sử dụng để phân giải tên, trong khi các bản ghi PTR được sử dụng cho phân giải tên ngược. Các bản ghi ngược đóng vai trò quan trọng vì Red Hat Enterprise Linux CoreOS (RHCOS) sử dụng chúng để thiết lập tên máy (hostname) cho tất cả các nút, trừ khi các tên máy này đã được cung cấp thông qua DHCP. Ngoài ra, các bản ghi ngược còn được dùng để tạo các yêu cầu ký chứng chỉ (Certificate Signing Request - CSR) cần thiết cho hoạt động của OpenShift Container Platform.

> **Lưu ý:** Bạn nên sử dụng máy chủ DHCP để cung cấp tên máy cho từng nút trong cụm. Hãy tham khảo phần khuyến nghị về DHCP cho cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure) để biết thêm thông tin.

Các bản ghi DNS dưới đây là bắt buộc đối với cụm OpenShift Container Platform do người dùng cung cấp và phải được thiết lập trước khi tiến hành cài đặt. Trong mỗi bản ghi, `<cluster_name>` là tên cụm và `<base_domain>` là tên miền cơ sở mà bạn chỉ định trong tệp `install-config.yaml`. Một bản ghi DNS hoàn chỉnh sẽ có dạng: `<component>.<cluster_name>.<base_domain>.`.

**Bảng 2.6. Các bản ghi DNS bắt buộc**

| Thành phần                   | Bản ghi                                            | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|------------------------------|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kubernetes API               | `api.<cluster_name>.<base_domain>.`                | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định bộ cân bằng tải API. Các bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm và tất cả các nút bên trong cụm đó.                                                                                                                                                                                                                                                                                                                                                                                                              |
| Kubernetes API               | `api-int.<cluster_name>.<base_domain>.`            | Cần có bản ghi DNS A/AAAA hoặc CNAME, cùng với bản ghi DNS PTR, để xác định bộ cân bằng tải API (API load balancer) trong nội bộ hệ thống. Các bản ghi này phải có thể phân giải được từ tất cả các nút (node) bên trong cụm (cluster). **Quan trọng:** Máy chủ API (API server) phải có khả năng phân giải các nút worker thông qua tên máy chủ (hostname) đã được ghi lại trong Kubernetes. Nếu máy chủ API không thể phân giải được tên các nút, thì các lệnh gọi API thông qua proxy có thể bị lỗi, và bạn sẽ không thể truy xuất nhật ký (log) từ các pod.                                                                       |
| Routes                       | `*.apps.<cluster_name>.<base_domain>.`             | Một bản ghi DNS wildcard (dạng A/AAAA hoặc CNAME) trỏ đến bộ cân bằng tải ingress (ingress load balancer) của ứng dụng. Bộ cân bằng tải ingress này hướng lưu lượng truy cập đến các máy đang chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller này chạy trên các máy tính toán (compute machines). Các bản ghi này phải có thể được phân giải bởi cả các client bên ngoài cụm lẫn tất cả các node nằm bên trong cụm. Ví dụ: `console-openshift-console.apps.<cluster_name>.<base_domain>` được sử dụng làm tuyến đường (route) wildcard dẫn đến bảng điều khiển (console) của OpenShift Container Platform. |
| Máy khởi động                | `bootstrap.<cluster_name>.<base_domain>.`          | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định máy khởi động (bootstrap machine). Các bản ghi này phải có thể được phân giải bởi các nút (node) nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Máy chủ mặt phẳng điều khiển | `<control_plane><n>.<cluster_name>.<base_domain>.` | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút thuộc mặt phẳng điều khiển (control plane nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                 |
| Máy tính toán                | `<compute><n>.<cluster_name>.<base_domain>.`       | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút worker (worker nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                            |

> **Lưu ý:** Trong OpenShift Container Platform 4.4 trở về sau, bạn không cần chỉ định các bản ghi SRV và máy chủ etcd trong cấu hình DNS của mình.

> **Mẹo:** Bạn có thể sử dụng lệnh `dig` để xác minh quá trình phân giải tên miền và phân giải ngược. Hãy xem phần "Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp" để biết các bước xác thực chi tiết.

###### 2.1.2.5.1. Ví dụ về cấu hình DNS cho các cụm (cluster) do người dùng cung cấp
Hãy tham khảo các ví dụ về cấu hình DNS để hiểu cách thiết lập các bản ghi A và PTR nhằm đáp ứng các yêu cầu DNS khi triển khai OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp.

Các ví dụ về cấu hình DNS được cung cấp tại đây chỉ mang tính chất tham khảo và không nhằm mục đích đưa ra lời khuyên về việc lựa chọn giải pháp DNS này thay vì giải pháp DNS khác.

Trong các ví dụ này, tên cụm (cluster name) là `ocp4` và tên miền cơ sở (base domain) là `example.com`.

Ví dụ dưới đây là một tệp vùng (zone file) BIND, minh họa các bản ghi DNS loại A mẫu dùng cho việc phân giải tên trong một cụm do người dùng cung cấp.

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
control-plane2.ocp4.example.com.  IN  A 192.168.1.99
;
compute0.ocp4.example.com.  IN  A 192.168.1.11
compute1.ocp4.example.com.  IN  A 192.168.1.7
;
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho API và được sử dụng cho các giao tiếp nội bộ trong cụm (cluster).
* `*.apps.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các tuyến đường (route) dạng ký tự đại diện (wildcard). Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho ứng dụng (application ingress load balancer). Bộ cân bằng tải này hướng lưu lượng đến các máy đang chạy các pod Ingress Controller.
* `bootstrap.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho máy bootstrap.
* `control-plane0.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy thuộc mặt phẳng điều khiển (control plane).
* `compute0.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy tính toán (compute machine).

Ví dụ về tệp vùng (zone file) BIND dưới đây hiển thị các bản ghi PTR mẫu phục vụ cho việc phân giải tên ngược (reverse name resolution) trong một cụm do người dùng tự cung cấp (user-provisioned cluster):

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
99.1.168.192.in-addr.arpa.  IN  PTR control-plane2.ocp4.example.com.
;
11.1.168.192.in-addr.arpa.  IN  PTR compute0.ocp4.example.com.
7.1.168.192.in-addr.arpa. IN  PTR compute1.ocp4.example.com.
;
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược (reverse DNS resolution) cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải (load balancer) API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải API và được sử dụng cho các hoạt động giao tiếp nội bộ trong cụm (cluster).
* `bootstrap.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho máy bootstrap.
* `control-plane0.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy thuộc mặt phẳng điều khiển (control plane).
* `compute0.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy tính toán (compute machines).

> **Lưu ý:** Không bắt buộc phải có bản ghi PTR cho tên miền đại diện (wildcard) của ứng dụng trong OpenShift Container Platform.

##### 2.1.2.6. Cấu hình tham số dnsRecordsType
Trong quá trình cài đặt cụm (cluster), bạn có thể chỉ định tham số `dnsRecordsType` trong tệp `install-config.yaml` để xác định xem các bản ghi DNS cần thiết cho `api`, `api-int` và `ingress` sẽ do dịch vụ DNS nội bộ cung cấp hay do một nguồn bên ngoài cung cấp.

> **Quan trọng:** Việc cấu hình tham số `dnsRecordsType` chỉ là tính năng ở giai đoạn "Xem trước công nghệ" (Technology Preview). Các tính năng này không được hỗ trợ theo các thỏa thuận cấp độ dịch vụ (SLA) dành cho môi trường sản xuất của Red Hat và có thể chưa hoàn thiện về mặt chức năng. Red Hat khuyến cáo không nên sử dụng các tính năng này trong môi trường sản xuất. Những tính năng này cho phép khách hàng tiếp cận sớm các chức năng sắp ra mắt, từ đó có thể kiểm thử và cung cấp phản hồi trong suốt quá trình phát triển sản phẩm.

Tham số `dnsRecordsType` hỗ trợ các giá trị sau:

* `Internal`: Giá trị mặc định. Khi chọn giá trị này, cơ sở hạ tầng cụm (cluster infrastructure) sẽ tự động tạo và duy trì các bản ghi DNS cần thiết. 
* `External`: Bạn chỉ có thể sử dụng giá trị này nếu đặt tham số `loadBalancer.type` thành `UserManaged`. Cụm sẽ không quản lý các bản ghi DNS; bạn phải tự cấu hình các bản ghi DNS trên một máy chủ DNS bên ngoài.

**Các điều kiện tiên quyết**  
* Bạn đã tạo các bản ghi DNS, chẳng hạn như `api`, `api-int` hoặc `*.apps`. 
* Bạn đã định cấu hình bộ cân bằng tải do người dùng quản lý cho cụm (cluster) của mình. 
* Nếu bạn dự định đặt `dnsRecordsType.External` trong tài nguyên tùy chỉnh (CR) `infrastructure.config.openshift.io`, trước tiên bạn phải định cấu hình các nút (node) của cụm để sử dụng máy chủ bên ngoài cụ thể cho việc phân giải DNS.

**Quy trình**  
* Trong tệp `install-config.yaml` trong quá trình cài đặt cụm, hãy chỉ định `TechPreviewNoUpgrade` cho tham số `featureSet` và chỉ định `External` cho tham số `dnsRecordsType`:

```yaml
apiVersion: v1
baseDomain: example.com
metadata:
  name: dev-cluster
# ...
platform:
  baremetal:
# ...
    loadBalancer:
      type: UserManaged
    dnsRecordsType: External
# ...
featureSet: TechPreviewNoUpgrade
pullSecret: '{"auths":{"<local_registry>": {"auth": "<credentials>","email": "you@example.com"}}}'
sshKey: 'ssh-ed25519 AAAA...'
# ...
```

  Trong đó:

  * `type.UserManaged`: Chỉ định một bộ cân bằng tải (load balancer) bên ngoài cho cụm (cluster) của bạn.
  * `dnsRecordsType.External`: Chỉ định rằng cụm không tạo các bản ghi DNS nội bộ cho cơ sở hạ tầng cốt lõi.
  * `featureSet.TechPreviewNoUpgrade`: Chỉ định việc kích hoạt các tính năng không mặc định cho cụm của bạn.

##### 2.1.2.7. Các yêu cầu về cân bằng tải đối với cơ sở hạ tầng do người dùng cung cấp
Trước khi cài đặt OpenShift Container Platform, bạn phải cung cấp cơ sở hạ tầng cân bằng tải cho API và Ingress ứng dụng. Trong các môi trường thực tế (production), bạn có thể triển khai bộ cân bằng tải cho API và Ingress ứng dụng một cách riêng biệt, nhờ đó có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Nếu bạn muốn triển khai các bộ cân bằng tải cho API và Ingress ứng dụng trên một instance chạy hệ điều hành Red Hat Enterprise Linux (RHEL), bạn phải mua gói đăng ký (subscription) RHEL riêng.

Cơ sở hạ tầng cân bằng tải phải đáp ứng các yêu cầu sau:

* Bộ cân bằng tải API: Cung cấp một điểm cuối (endpoint) chung để người dùng (bao gồm cả con người và máy móc) tương tác với nền tảng và thực hiện cấu hình. Hãy thiết lập các điều kiện sau:
  * Chỉ sử dụng cân bằng tải ở Lớp 4 (Layer 4). Phương thức này còn được gọi là Raw TCP (TCP thuần) hoặc chế độ SSL Passthrough. 
  * Sử dụng thuật toán cân bằng tải không lưu trạng thái (stateless). Các tùy chọn cụ thể sẽ khác nhau tùy thuộc vào giải pháp cân bằng tải mà bạn triển khai.

> **Quan trọng:** Không cấu hình tính năng duy trì phiên (session persistence) cho bộ cân bằng tải API. Việc cấu hình duy trì phiên cho Kubernetes API server có thể gây ra các vấn đề về hiệu năng do lưu lượng truy cập ứng dụng quá mức đổ dồn vào cụm OpenShift Container Platform và Kubernetes API đang chạy bên trong cụm đó.

Cấu hình các cổng (port) sau đây ở cả phía trước (front-end) và phía sau (back-end) của các bộ cân bằng tải API:

| Cổng  | Máy chủ phụ trợ                                                                                                                                                                                                                                                                                                          | Nội bộ | Bên ngoài | Mô tả                     |
|-------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|-----------|---------------------------|
| 6443  | Bootstrap và mặt phẳng điều khiển (control plane). Sau khi máy bootstrap khởi tạo mặt phẳng điều khiển của cụm (cluster), bạn cần loại bỏ máy này khỏi bộ cân bằng tải (load balancer). Bạn phải cấu hình điểm cuối (endpoint) `/readyz` để phục vụ cho việc kiểm tra tình trạng hoạt động của máy chủ API (API server). | X      | X         | Máy chủ API Kubernetes    |
| 22623 | Bootstrap và mặt phẳng điều khiển (control plane). Bạn loại bỏ máy bootstrap khỏi bộ cân bằng tải sau khi máy này khởi tạo mặt phẳng điều khiển của cụm (cluster).                                                                                                                                                       | X      |           | Máy chủ cấu hình thiết bị |

> **Lưu ý:** Bộ cân bằng tải (load balancer) phải được cấu hình sao cho khoảng thời gian từ lúc máy chủ API tắt điểm cuối (endpoint) `/readyz` cho đến khi thực thể máy chủ API đó bị loại bỏ khỏi nhóm (pool) không vượt quá 30 giây. Trong khoảng thời gian sau khi `/readyz` trả về lỗi hoặc chuyển sang trạng thái khỏe mạnh (healthy), điểm cuối đó phải được loại bỏ hoặc thêm vào tương ứng. Việc thực hiện kiểm tra (probe) mỗi 5 hoặc 10 giây, với quy định cần 2 phản hồi thành công liên tiếp để xác định trạng thái khỏe mạnh và 3 phản hồi không thành công liên tiếp để xác định trạng thái không khỏe mạnh, là các thông số đã được kiểm chứng hiệu quả. 

* Bộ cân bằng tải Ingress cho ứng dụng: Cung cấp điểm truy cập (ingress point) cho lưu lượng ứng dụng đi từ bên ngoài cụm (cluster) vào. Cụm OpenShift Container Platform bắt buộc phải có cấu hình hoạt động cho bộ định tuyến Ingress. Hãy thiết lập các điều kiện sau:
  * Chỉ sử dụng cân bằng tải lớp 4 (Layer 4). Cấu hình này còn được gọi là chế độ TCP thô (Raw TCP) hoặc chuyển tiếp SSL (SSL Passthrough). 
  * Khuyến nghị sử dụng cơ chế duy trì phiên (session persistence) dựa trên kết nối hoặc dựa trên phiên, tùy thuộc vào các tùy chọn khả dụng và loại ứng dụng sẽ được lưu trữ trên nền tảng.

> **Mẹo:** Nếu bộ cân bằng tải Ingress cho ứng dụng có thể nhận diện được địa chỉ IP thực của máy khách (client), thì việc kích hoạt cơ chế duy trì phiên dựa trên IP nguồn (source IP-based session persistence) có thể giúp cải thiện hiệu suất cho các ứng dụng sử dụng mã hóa TLS đầu cuối (end-to-end TLS encryption).

Cấu hình các cổng (port) sau đây ở cả phía trước (front-end) và phía sau (back-end) của các bộ cân bằng tải:

**Bảng 2.7. Bộ cân bằng tải Ingress cho ứng dụng**

| Cổng | Máy chủ phụ trợ                                                                                                           | Nội bộ | Bên ngoài | Mô tả                    |
|------|---------------------------------------------------------------------------------------------------------------------------|--------|-----------|--------------------------|
| 443  | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng truy cập HTTPS |
| 80   | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng truy cập HTTP  |

> **Lưu ý:** Nếu bạn triển khai cụm gồm ba nút (node) mà không có nút tính toán (compute node) nào, các pod của Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển (control plane node). Đối với các cụm gồm ba nút, bạn phải cấu hình bộ cân bằng tải Ingress cho ứng dụng của mình để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển.

###### 2.1.2.7.1. Ví dụ về cấu hình bộ cân bằng tải cho các cụm do người dùng cung cấp
Hãy tham khảo ví dụ về cấu hình bộ cân bằng tải cho API và Ingress ứng dụng để hiểu cách đáp ứng các yêu cầu về cân bằng tải đối với các cụm do người dùng cung cấp.

Ví dụ này là tệp cấu hình `/etc/haproxy/haproxy.cfg` dành cho bộ cân bằng tải HAProxy. Ví dụ này không nhằm mục đích đưa ra lời khuyên về việc chọn giải pháp cân bằng tải này thay vì giải pháp khác.

> **Mẹo:** Nếu bạn sử dụng HAProxy làm bộ cân bằng tải, bạn có thể kiểm tra xem tiến trình HAProxy có đang lắng nghe trên các cổng 6443, 22623, 443 và 80 hay không bằng cách chạy lệnh `netstat -nltupe` trên nút (node) chạy HAProxy.

Trong ví dụ này, cùng một bộ cân bằng tải được sử dụng cho cả API Kubernetes và lưu lượng truy cập Ingress ứng dụng. Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và Ingress ứng dụng để có thể mở rộng quy mô hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Nếu bạn sử dụng HAProxy làm bộ cân bằng tải và SELinux đang ở chế độ thực thi (enforcing), bạn phải đảm bảo rằng dịch vụ HAProxy có thể liên kết (bind) với cổng TCP đã cấu hình bằng cách chạy lệnh `setsebool -P haproxy_connect_any=1`.

Ví dụ về cấu hình bộ cân bằng tải cho API và Ingress ứng dụng

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
  option  httpchk GET /readyz HTTP/1.0
  option  log-health-checks
  balance roundrobin
  server bootstrap bootstrap.ocp4.example.com:6443 verify none check check-ssl inter 10s fall 2 rise 3 backup
  server master0 master0.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server master1 master1.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server master2 master2.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
listen machine-config-server-22623
  bind *:22623
  mode tcp
  server bootstrap bootstrap.ocp4.example.com:22623 check inter 1s backup
  server master0 master0.ocp4.example.com:22623 check inter 1s
  server master1 master1.ocp4.example.com:22623 check inter 1s
  server master2 master2.ocp4.example.com:22623 check inter 1s
listen ingress-router-443
  bind *:443
  mode tcp
  balance source
  server compute0 compute0.ocp4.example.com:443 check inter 1s
  server compute1 compute1.ocp4.example.com:443 check inter 1s
listen ingress-router-80
  bind *:80
  mode tcp
  balance source
  server compute0 compute0.ocp4.example.com:80 check inter 1s
  server compute1 compute1.ocp4.example.com:80 check inter 1s
```

Trong đó:

* `listen api-server-6443`: Cổng 6443 xử lý lưu lượng truy cập Kubernetes API và trỏ đến các máy thuộc mặt phẳng điều khiển (control plane). Bạn phải định cấu hình kiểm tra trạng thái (health check) trên cổng này để đảm bảo rằng API server khả dụng trước khi định tuyến lưu lượng truy cập.
* `server bootstrap bootstrap.ocp4.example.com`: Các mục cấu hình bootstrap phải được thiết lập trước khi cài đặt cụm OpenShift Container Platform và phải được loại bỏ sau khi quá trình bootstrap hoàn tất.
* `listen machine-config-server`: Cổng 22623 xử lý lưu lượng truy cập của machine config server và trỏ đến các máy thuộc mặt phẳng điều khiển.
* `listen ingress-router-443`: Cổng 443 xử lý lưu lượng truy cập HTTPS và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán (compute nodes).
* `listen ingress-router-80`: Cổng 80 xử lý lưu lượng truy cập HTTP và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán. 

> **Lưu ý:** Nếu bạn đang triển khai một cụm gồm ba nút (compact three-node cluster) mà không có các nút tính toán riêng biệt, các pod Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển. Trong các triển khai cụm ba nút, bạn phải định cấu hình bộ cân bằng tải Ingress cho ứng dụng để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển.

#### 2.1.3. Tạo đối tượng manifest bao gồm cầu nối br-ex tùy chỉnh
Theo mặc định, OpenShift Container Platform tự động cấu hình cầu nối br-ex của Open vSwitch (OVS) trên các nút (node) chạy trên phần cứng vật lý (bare-metal). Đối với các yêu cầu mạng nâng cao, bạn có thể thay đổi hành vi mặc định này trên các nền tảng phần cứng vật lý. Để thực hiện việc này, hãy tạo một đối tượng MachineConfig bao gồm tệp cấu hình NMState.

> **Lưu ý:** Bạn có thể sử dụng Butane để tạo các cấu hình máy (machine config). Để biết thêm thông tin, hãy xem phần "Tạo đối tượng MachineConfig bằng cách sử dụng Butane".

Hãy cân nhắc sử dụng cấu hình cầu nối br-ex tùy chỉnh cho bất kỳ tác vụ nào sau đây:

* Bạn cần sửa đổi cầu nối br-ex sau khi đã cài đặt cụm (cluster). 
* Bạn cần sửa đổi đơn vị truyền tải tối đa (MTU) cho cụm của mình. 
* Bạn cần cập nhật các giá trị DNS. 
* Bạn cần sửa đổi các thuộc tính cho một giao diện liên kết (bond interface) khác. Các ví dụ bao gồm MIImon (Media Independent Interface Monitor), chế độ liên kết (bonding mode) hoặc Chất lượng dịch vụ (QoS). 
* Bạn cần bật Giao thức khám phá lớp liên kết (Link Layer Discovery Protocol - LLDP) để phát hiện và khắc phục sự cố kết nối với thiết bị chuyển mạch (switch).

> **Lưu ý:** Hãy sử dụng cầu nối OVS `br-ex` mặc định cho các môi trường tiêu chuẩn.  
> Hãy sử dụng cơ chế cầu nối OVS `br-ex` mặc định cho các môi trường chỉ có một bộ điều khiển giao diện mạng (NIC) với các thiết lập mạng mặc định.

Sau khi bạn cài đặt Red Hat Enterprise Linux CoreOS (RHCOS) và hệ thống khởi động lại, Machine Config Operator sẽ đưa các tệp cấu hình Ignition vào từng nút. Thao tác này đảm bảo rằng mỗi nút đều nhận được cấu hình mạng cho cầu nối br-ex. Để tránh xung đột cấu hình, cơ chế cầu nối OVS br-ex mặc định sẽ bị vô hiệu hóa.

> **Cảnh báo:** Danh sách các tên giao diện dưới đây đã được dành riêng; bạn không được phép sử dụng các tên này trong cấu hình NMstate:  
`br-ext`,  `br-int`, `br-local`, `br-nexthop`, `br0`, `ext-vxlan`, `ext`, `genev_sys_*`, `int`, `k8s-*`, `ovn-k8s-*`, `patch-br-*`, `tun0`, `vxlan_sys_*`

**Các điều kiện tiên quyết**

Tùy chọn: Bạn đã cài đặt công cụ dòng lệnh (CLI) `nmstatectl` để xác thực cấu hình NMState của mình.

**Quy trình**

1. Tạo một tệp cấu hình NMState và xác định cấu hình mạng cầu nối (bridge network) `br-ex` tùy chỉnh trong tệp đó:

Ví dụ về cấu hình NMState cho mạng cầu nối `br-ex` tùy chỉnh

```yaml
interfaces:
- name: enp2s0
  type: ethernet
  state: up
  mtu: 9000
  ipv4:
    enabled: false
  ipv6:
    enabled: false
- name: br-ex
  type: ovs-bridge
  state: up
  ipv4:
    enabled: false
    dhcp: false
  ipv6:
    enabled: false
    dhcp: false
  bridge:
    options:
      mcast-snooping-enable: true
    port:
    - name: enp2s0
    - name: br-ex
- name: br-ex
  type: ovs-interface
  state: up
  copy-mac-from: enp2s0
  mtu: 9000
  ipv4:
    enabled: true
    dhcp: true
    auto-route-metric: 48
  ipv6:
    enabled: true
    dhcp: true
    auto-route-metric: 48
# ...
```

Trong đó:

  * `interfaces.name`: Xác định tên của giao diện (interface).
  * `interfaces.type`: Xác định loại giao diện Ethernet.
  * `interfaces.state`: Xác định trạng thái mong muốn cho giao diện sau khi được tạo.
  * `mtu`: Để đảm bảo tính ổn định và hiệu suất mạng, bạn phải khai báo rõ ràng giá trị MTU trong tệp cấu hình (manifest) cho mọi giao diện. Không nên dựa vào cơ chế tự động cấu hình MTU. Giá trị MTU được thiết lập trên một cổng cầu nối (bridge port) hoặc giao diện có gắn thẻ VLAN (VLAN-tagged interface) không được vượt quá kích thước khung hình (frame size) tối đa mà phương tiện truyền dẫn vật lý hỗ trợ. Nếu các giá trị này không khớp, sẽ dẫn đến tình trạng phân mảnh gói tin hoặc mất kết nối.
  * `ipv4.enabled`: Trong ví dụ này, tùy chọn này dùng để vô hiệu hóa IPv4 và IPv6.
  * `port.name`: Xác định card mạng (NIC) của nút (node) mà cầu nối (bridge) sẽ kết nối vào.
  * `auto-route-metric`: Thiết lập tham số này thành 48 để đảm bảo tuyến đường mặc định (default route) của br-ex luôn có mức ưu tiên cao nhất (giá trị metric thấp nhất). Cấu hình này giúp ngăn ngừa xung đột định tuyến với các giao diện khác được dịch vụ NetworkManager tự động cấu hình.

2. Sử dụng lệnh cat để mã hóa nội dung cấu hình NMState sang định dạng base64:

```shell
$ cat <nmstate_configuration>.yml | base64
```

Trong đó:

  * `<nmstate_configuration>`: Hãy thay thế `<nmstate_configuration>` bằng tên tệp YAML tài nguyên NMState của bạn.


3. Hãy tạo một tệp manifest MachineConfig và xác định cấu hình mạng cầu nối (bridge network) `br-ex` tùy chỉnh, tương tự như ví dụ dưới đây. Chương trình cài đặt sẽ tự động áp dụng các cập nhật từ đối tượng MachineConfig này vào cụm (cluster) của bạn.

```yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 10-br-ex-worker
spec:
  config:
    ignition:
      version: 3.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/worker-0.yml
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/worker-1.yml
# ...
```

Trong đó:

* `metadata.name`: Xác định tên của chính sách (policy).
* `contents.source`: Ghi thông tin đã mã hóa base64 vào đường dẫn được chỉ định.
* `path`: Đối với mỗi nút (node) trong cụm (cluster) của bạn, hãy chỉ định đường dẫn dựa trên tên máy chủ (hostname) của nút đó cùng với dữ liệu tệp cấu hình Ignition (đã mã hóa base64) tương ứng với loại máy đó. Vai trò "worker" là vai trò mặc định cho các nút trong cụm của bạn. Bạn bắt buộc phải sử dụng phần mở rộng .yml cho các tệp cấu hình. Ví dụ: hãy sử dụng $(hostname -s).yml khi chỉ định đường dẫn theo tên máy chủ rút gọn cho từng nút hoặc tất cả các nút trong tệp manifest MachineConfig. 

Bạn có thể áp dụng một cấu hình chung duy nhất cho tất cả các nút trong cụm bằng cách sử dụng tệp cấu hình `/etc/nmstate/openshift/cluster.yml`. Trong trường hợp này, bạn không cần phải chỉ định đường dẫn theo tên máy chủ rút gọn cho từng nút (ví dụ: `/etc/nmstate/openshift/<node_hostname>.yml`). Ví dụ:

Ví dụ về tệp cấu hình `/etc/nmstate/openshift/cluster.yml`

```yaml
# ...
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/cluster.yml
# ...
```

**Các bước tiếp theo**

Mở rộng quy mô các nút tính toán (compute node) bằng cách áp dụng đối tượng manifest — bao gồm cấu hình cầu nối `br-ex` tùy chỉnh — cho từng nút tính toán hiện có trong cụm (cluster) của bạn. Để biết thêm thông tin, hãy xem phần "Mở rộng cụm".

##### 2.1.3.1. Mở rộng quy mô các tập hợp máy (machine set) thành các nút tính toán (compute node)
Để mở rộng quy mô các tập hợp máy thành các nút tính toán, bạn phải áp dụng cấu hình cầu nối `br-ex` tùy chỉnh cho tất cả các nút tính toán trong cụm OpenShift Container Platform của mình. Sau đó, bạn cần chỉnh sửa tài nguyên tùy chỉnh (CR) `MachineConfig` và sửa đổi các vai trò (roles) của nó.

Ngoài ra, bạn phải tạo một CR `BareMetalHost` để xác định thông tin cho máy vật lý (bare-metal) của mình, chẳng hạn như tên máy chủ (hostname), thông tin xác thực và các tham số cần thiết khác. Sau khi định cấu hình các tài nguyên này, bạn cần mở rộng quy mô các tập hợp máy để chúng có thể áp dụng cấu hình tài nguyên đó cho từng nút tính toán và khởi động lại các nút này.

**Điều kiện tiên quyết**

Bạn đã tạo một đối tượng manifest `MachineConfig` bao gồm cấu hình cầu nối `br-ex` tùy chỉnh.

**Quy trình**

1. Chỉnh sửa CR `MachineConfig` bằng cách nhập lệnh sau:

```shell
$ oc edit mc <machineconfig_custom_resource_name>
```

2. Hãy thêm cấu hình của từng nút tính toán (compute node) vào CR, để CR có thể quản lý vai trò cho mỗi nút tính toán đã xác định trong cụm của bạn.
3. Tạo một đối tượng Secret có tên là `extraworker-secret`, chứa cấu hình IP tĩnh tối thiểu.
4. Áp dụng Secret `extraworker-secret` cho từng nút trong cụm bằng cách nhập lệnh sau. Bước này cho phép mỗi nút tính toán truy cập vào tệp cấu hình Ignition.

```shell
$ oc apply -f ./extraworker-secret.yaml
```

5. Tạo tài nguyên BareMetalHost và chỉ định bí mật mạng (network secret) trong tham số preprovisioningNetworkDataName:

Ví dụ về tài nguyên BareMetalHost có đính kèm bí mật mạng

```yaml
apiVersion: metal3.io/v1alpha1
kind: BareMetalHost
spec:
# ...
  preprovisioningNetworkDataName: ostest-extraworker-0-network-config-secret
# ...
```

6. Để quản lý đối tượng BareMetalHost trong namespace openshift-machine-api của cụm, hãy chuyển sang namespace đó bằng cách nhập lệnh sau:

```shell
$ oc project openshift-machine-api
```

7. Lấy các tập hợp máy (machine set)

```shell
$ oc get machinesets
```

8. Điều chỉnh quy mô từng tập hợp máy bằng cách nhập lệnh sau. Bạn phải chạy lệnh này cho mỗi tập hợp máy.

```shell
$ oc scale machineset <machineset_name> --replicas=<n>
```

Trong đó:

  * `<machineset_name>`: Chỉ định tên của tập hợp máy (machine set).
  * `<n>`: Chỉ định số lượng nút tính toán (compute node).

#### 2.1.4. Kích hoạt chế độ OVS balance-slb cho cụm (cluster) của bạn
Bạn có thể kích hoạt chế độ balance-slb của Open vSwitch (OVS) để cho phép hai hoặc nhiều giao diện vật lý cùng chia sẻ lưu lượng mạng.

Giao diện ở chế độ balance-slb cung cấp khả năng cân bằng tải dựa trên nguồn (Source Load Balancing - SLB) cho một cụm chạy các khối lượng công việc ảo hóa (virtualization workloads), mà không yêu cầu phải thực hiện thương lượng cân bằng tải với thiết bị chuyển mạch (switch) mạng.

Hiện tại, cơ chế cân bằng tải dựa trên nguồn hoạt động trên một giao diện liên kết (bond interface), trong đó giao diện này kết nối với một cầu nối (bridge) phụ, chẳng hạn như br-phy. Cơ chế này chỉ thực hiện cân bằng tải dựa trên các tổ hợp địa chỉ MAC (Media Access Control) và VLAN (Virtual Local Area Network) khác nhau. Cần lưu ý rằng toàn bộ lưu lượng truy cập từ các pod trong OVN-Kubernetes đều sử dụng cùng một địa chỉ MAC và VLAN, do đó, loại lưu lượng này không thể được cân bằng tải trên nhiều giao diện vật lý khác nhau.

Sơ đồ dưới đây minh họa chế độ balance-slb trong một cấu trúc hạ tầng cụm đơn giản. Các máy ảo (VM) kết nối với các tài nguyên tùy chỉnh (Custom Resource Definition - CRD) NetworkAttachmentDefinition (NAD) loại localnet cụ thể, đó là NAD 0 hoặc NAD 1. Mỗi NAD cung cấp cho các máy ảo quyền truy cập vào mạng vật lý bên dưới, hỗ trợ cả lưu lượng có gắn thẻ VLAN (VLAN-tagged) và không gắn thẻ VLAN. Một cầu nối OVS có tên là br-ex sẽ tiếp nhận lưu lượng từ các máy ảo và chuyển tiếp đến cầu nối OVS tiếp theo là br-phy. Cầu nối br-phy đóng vai trò là bộ điều khiển cho liên kết SLB (SLB bond). Liên kết SLB này thực hiện cân bằng lưu lượng từ các cổng máy ảo khác nhau trên các đường kết nối vật lý, ví dụ như eno0 và eno1. Ngoài ra, lưu lượng đi vào (ingress traffic) từ bất kỳ giao diện vật lý nào cũng có thể đi qua các cầu nối OVS này để đến được các máy ảo.

**Hình 2.1. Chế độ OVS balance-slb hoạt động trên một mạng cục bộ (localnet) với hai NAD**
![Hình 2.1](img/552_OpenShift_slb_mode_0625.png)

Bạn có thể tích hợp giao diện ở chế độ `balance-slb` vào các loại mạng chính (primary) hoặc phụ (secondary) bằng cách sử dụng tính năng OVS bonding. Cần lưu ý một số điểm sau về OVS bonding:

* Hỗ trợ plugin CNI OVN-Kubernetes và có thể tích hợp dễ dàng với plugin này. 
* Hỗ trợ nguyên bản (natively) chế độ `balance-slb`.

**Các điều kiện tiên quyết**

* Bạn có nhiều hơn một giao diện vật lý được kết nối với mạng chính và đã định nghĩa các giao diện này trong tệp `MachineConfig`. 
* Bạn đã tạo một đối tượng manifest và định nghĩa một cầu nối (bridge) `br-ex` tùy chỉnh trong tệp cấu hình của đối tượng đó. 
* Bạn có nhiều hơn một giao diện vật lý được kết nối với mạng chính và đã định nghĩa các giao diện này trong tệp NAD CRD.

**Quy trình**

1. Đối với mỗi máy chủ vật lý (bare-metal host) trong cụm, hãy định nghĩa một phần `networkConfig` trong tệp `install-config.yaml` của cụm đó, tương tự như ví dụ dưới đây:

```yaml
# ...
networkConfig:
  interfaces:
    - name: enp1s0
      type: ethernet
      state: up
      ipv4:
        dhcp: true
        enabled: true
      ipv6:
        enabled: false
    - name: enp2s0
      type: ethernet
      state: up
      mtu: 1500
      ipv4:
        dhcp: true
        enabled: true
      ipv6:
        dhcp: true
        enabled: true
    - name: enp3s0
      type: ethernet
      state: up
      mtu: 1500
      ipv4:
        enabled: false
      ipv6:
        enabled: false
# ...
```

Trong đó:

  * `enp1s0`: Giao diện dành cho bộ điều khiển giao diện mạng (NIC) đã được cung cấp.
  * `enp2s0`: Giao diện liên kết (bonded interface) đầu tiên, thực hiện việc lấy tệp cấu hình Ignition cho giao diện liên kết này.
  * `mtu`: Thiết lập thủ công đơn vị truyền tải tối đa (MTU) cho br-ex trên các cổng liên kết (bond ports).
  * `enp3s0`: Giao diện liên kết thứ hai, là một phần của cấu hình tối giản, thực hiện việc lấy tệp Ignition trong quá trình cài đặt cụm (cluster).

2. Xác định từng giao diện mạng trong tệp cấu hình NMState:

Ví dụ về tệp cấu hình NMState định nghĩa nhiều giao diện mạng

```yaml
ovn:
  bridge-mappings:
    - localnet: localnet-network
      bridge: br-ex
      state: present
interfaces:
  - name: br-ex
    type: ovs-bridge
    state: up
    bridge:
      allow-extra-patch-ports: true
      port:
        - name: br-ex
        - name: patch-ex-to-phy
    ovs-db:
      external_ids:
        bridge-uplink: "patch-ex-to-phy"
  - name: br-ex
    type: ovs-interface
    state: up
    mtu: 1500
    ipv4:
      enabled: true
      dhcp: true
      auto-route-metric: 48
    ipv6:
      enabled: false
      dhcp: false
      auto-route-metric: 48
  - name: br-phy
    type: ovs-bridge
    state: up
    bridge:
      allow-extra-patch-ports: true
      port:
        - name: patch-phy-to-ex
        - name: ovs-bond
          link-aggregation:
            mode: balance-slb
            port:
              - name: enp2s0
              - name: enp3s0
  - name: patch-ex-to-phy
    type: ovs-interface
    state: up
    patch:
      peer: patch-phy-to-ex
  - name: patch-phy-to-ex
    type: ovs-interface
    state: up
    patch:
      peer: patch-ex-to-phy
  - name: enp1s0
    type: ethernet
    state: up
    ipv4:
      dhcp: true
      enabled: true
    ipv6:
      enabled: false
  - name: enp2s0
    type: ethernet
    state: up
    mtu: 1500
    ipv4:
      enabled: false
    ipv6:
      enabled: false
  - name: enp3s0
    type: ethernet
    state: up
    mtu: 1500
    ipv4:
      enabled: false
    ipv6:
      enabled: false
# ...
```

Trong đó:

  * `mtu`: Thiết lập thủ công giá trị MTU cho br-ex trên các cổng bond.

3. Sử dụng lệnh `base64` để mã hóa nội dung giao diện của tệp cấu hình NMState:

```shell
$ base64 -w0  <nmstate_configuration>.yml
```

  * `<nmstate_configuration>`: Trong đó, tùy chọn -w0 giúp ngăn chặn việc ngắt dòng trong quá trình mã hóa base64.

4. Hãy tạo các tệp manifest MachineConfig cho vai trò master (master role) và vai trò worker (worker role). Đảm bảo rằng bạn nhúng chuỗi đã được mã hóa base64 từ lệnh trước đó vào mỗi tệp manifest MachineConfig. Tệp manifest mẫu dưới đây cấu hình vai trò master cho tất cả các nút (node) hiện có trong cụm (cluster). Bạn cũng có thể tạo tệp manifest dành riêng cho vai trò master hoặc worker trên một nút cụ thể.

```yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: master
  name: 10-br-ex-master
spec:
  config:
    ignition:
      version: 3.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/cluster.yml
```

Trong đó:

  * `name`: Tên của chính sách.
  * `source`: Ghi thông tin đã mã hóa base64 vào đường dẫn được chỉ định.
  * `path`: Chỉ định đường dẫn đến tệp `cluster.yml`. Đối với mỗi nút (node) trong cụm (cluster) của bạn, bạn có thể chỉ định đường dẫn dựa trên tên máy chủ rút gọn (short hostname) của nút đó, chẳng hạn như `<node_short_hostname>.yml`.

5. Lưu từng tệp manifest MachineConfig vào thư mục `./<installation_directory>/manifests`, trong đó `<installation_directory>` là thư mục mà chương trình cài đặt sử dụng để tạo các tệp tin.

Machine Config Operator (MCO) sẽ lấy nội dung từ mỗi tệp manifest và áp dụng nội dung đó một cách nhất quán cho tất cả các nút (node) được chọn trong quá trình cập nhật theo phương thức cuốn chiếu (rolling update).


#### 2.1.5. Chuẩn bị cơ sở hạ tầng do người dùng cung cấp
Trước khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải chuẩn bị cơ sở hạ tầng nền tảng.

Phần này cung cấp thông tin chi tiết về các bước chính cần thiết để thiết lập cơ sở hạ tầng cụm (cluster) nhằm chuẩn bị cho việc cài đặt OpenShift Container Platform. Các bước này bao gồm cấu hình mạng IP và kết nối mạng cho các nút (node) trong cụm, mở các cổng (port) cần thiết trên tường lửa, cũng như thiết lập cơ sở hạ tầng DNS và cân bằng tải (load balancing).

Sau khi hoàn tất quá trình chuẩn bị, cơ sở hạ tầng cụm của bạn phải đáp ứng các yêu cầu được nêu trong phần "Các yêu cầu đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp".

**Điều kiện tiên quyết**

* Bạn đã xem qua trang [OpenShift Container Platform 4.x Tested Integrations](https://access.redhat.com/articles/4128421). 
* Bạn đã xem qua các yêu cầu về cơ sở hạ tầng được nêu chi tiết trong phần "Các yêu cầu đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp".

**Quy trình**

1. Nếu bạn sử dụng DHCP để cung cấp cấu hình mạng IP cho các nút trong cụm, hãy cấu hình dịch vụ DHCP của bạn. 
  * Thêm các địa chỉ IP cố định cho các nút vào cấu hình máy chủ DHCP. Trong cấu hình, hãy ánh xạ địa chỉ MAC của giao diện mạng tương ứng với địa chỉ IP dự định cho mỗi nút. 
  * Khi sử dụng DHCP để cấu hình địa chỉ IP cho các máy trong cụm, các máy này cũng sẽ nhận thông tin máy chủ DNS thông qua DHCP. Hãy xác định địa chỉ máy chủ DNS cố định mà các nút trong cụm sẽ sử dụng thông qua cấu hình máy chủ DHCP của bạn. 

  > **Lưu ý:** Nếu không sử dụng dịch vụ DHCP, bạn phải cung cấp cấu hình mạng IP và địa chỉ máy chủ DNS cho các nút tại thời điểm cài đặt RHCOS. Các thông tin này có thể được truyền dưới dạng tham số khởi động (boot arguments) nếu bạn cài đặt từ tệp ảnh ISO. Xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform" để biết thêm thông tin về việc cấp phát IP tĩnh và các tùy chọn mạng nâng cao. 

  * Xác định tên máy (hostname) cho các nút trong cụm thông qua cấu hình máy chủ DHCP. Xem phần "Thiết lập tên máy cho các nút trong cụm thông qua DHCP" để biết chi tiết về các lưu ý liên quan đến tên máy. 

  > Lưu ý: Nếu không sử dụng dịch vụ DHCP, các nút trong cụm sẽ nhận tên máy thông qua truy vấn DNS ngược (reverse DNS lookup). 

2. Đảm bảo rằng cơ sở hạ tầng mạng của bạn cung cấp khả năng kết nối cần thiết giữa các thành phần trong cụm. Xem phần "Các yêu cầu về mạng đối với cơ sở hạ tầng do người dùng cung cấp" để biết chi tiết về các yêu cầu này.
3. Cấu hình tường lửa để mở các cổng cần thiết cho việc giao tiếp giữa các thành phần trong cụm OpenShift Container Platform. Xem phần "Yêu cầu về mạng đối với hạ tầng do người dùng cung cấp" (Networking requirements for user-provisioned infrastructure) để biết chi tiết về các cổng cần thiết. 

> **Quan trọng:** Theo mặc định, cổng 1936 luôn mở trong cụm OpenShift Container Platform vì mỗi nút điều khiển (control plane node) đều cần truy cập vào cổng này.  
> Đối với các thăm dò kiểm tra trạng thái (health check probes) của Ingress, điểm cuối (endpoint) `/healthz/ready` khả dụng trên cổng này.  
> Tránh sử dụng bộ cân bằng tải Ingress (Ingress load balancer) để công khai cổng này, vì việc đó có thể làm lộ các thông tin nhạy cảm—chẳng hạn như số liệu thống kê và các chỉ số—liên quan đến các bộ điều khiển Ingress (Ingress Controllers). 

4. Thiết lập hạ tầng DNS cần thiết cho cụm của bạn. 
  * Cấu hình phân giải tên DNS cho Kubernetes API, tên miền đại diện (wildcard) cho ứng dụng, máy khởi động (bootstrap machine), các máy điều khiển (control plane machines) và các máy tính toán (compute machines). 
  * Cấu hình phân giải DNS ngược (reverse DNS resolution) cho Kubernetes API, máy khởi động, các máy điều khiển và các máy tính toán. Xem phần "Yêu cầu về DNS đối với hạ tầng do người dùng cung cấp" (User-provisioned DNS requirements) để biết thêm thông tin về các yêu cầu DNS của OpenShift Container Platform. 
5. Xác thực cấu hình DNS của bạn. 
  * Từ nút cài đặt (installation node), hãy thực hiện truy vấn DNS đối với các tên bản ghi của Kubernetes API, các tuyến đường đại diện (wildcard routes) và các nút trong cụm. Hãy đảm bảo rằng các địa chỉ IP nhận được phản hồi tương ứng với đúng các thành phần đó. 
  * Từ nút cài đặt, hãy thực hiện truy vấn DNS ngược đối với các địa chỉ IP của bộ cân bằng tải và các nút trong cụm. Hãy đảm bảo rằng các tên bản ghi nhận được phản hồi tương ứng với đúng các thành phần đó. Xem phần "Xác thực phân giải DNS đối với hạ tầng do người dùng cung cấp" (Validating DNS resolution for user-provisioned infrastructure) để biết các bước xác thực DNS chi tiết. 
6. Cung cấp hạ tầng cân bằng tải cần thiết cho API và Ingress ứng dụng. Xem phần "Yêu cầu về cân bằng tải đối với hạ tầng do người dùng cung cấp" (Load balancing requirements for user-provisioned infrastructure) để biết thêm thông tin về các yêu cầu này. 

> **Lưu ý:** Một số giải pháp cân bằng tải yêu cầu phải thiết lập xong việc phân giải tên DNS cho các nút trong cụm trước khi khởi tạo bộ cân bằng tải.

#### 2.1.6. Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp
Để ngăn ngừa các lỗi cài đặt liên quan đến mạng và đảm bảo khả năng kết nối giữa các nút (node) trong OpenShift Container Platform, bạn cần xác thực cấu hình DNS trước khi triển khai trên cơ sở hạ tầng do người dùng cung cấp.

> **Quan trọng:** Các bước xác thực được nêu chi tiết trong phần này phải được thực hiện thành công trước khi bạn tiến hành cài đặt cụm (cluster) của mình.

**Điều kiện tiên quyết**

* Bạn đã định cấu hình các bản ghi DNS cần thiết cho cơ sở hạ tầng do người dùng cung cấp.

**Quy trình**

1. Từ nút cài đặt (installation node), hãy thực hiện truy vấn DNS đối với tên các bản ghi của Kubernetes API, các tuyến đường đại diện (wildcard routes) và các nút trong cụm. Hãy xác thực rằng các địa chỉ IP nhận được trong phản hồi tương ứng với các thành phần chính xác. 
  * Thực hiện truy vấn đối với tên bản ghi Kubernetes API. Kiểm tra để đảm bảo kết quả trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API:

  ```shell
  $ dig +noall +answer @<nameserver_ip> api.<cluster_name>.<base_domain>
  ```

  Hãy thay thế `<nameserver_ip>` bằng địa chỉ IP của máy chủ tên miền (name server), `<cluster_name>` bằng tên cụm (cluster) của bạn và `<base_domain>` bằng tên miền cơ sở (base domain) của bạn.

  Ví dụ đầu ra

  ```
  api.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  * Thực hiện tra cứu dựa trên tên bản ghi API nội bộ của Kubernetes. Kiểm tra để đảm bảo rằng kết quả trỏ đến địa chỉ IP của bộ cân bằng tải API:

  ```shell
  $ dig +noall +answer @<nameserver_ip> api-int.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  api-int.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  * Kiểm tra một ví dụ về truy vấn DNS wildcard dạng `*.apps.<cluster_name>.<base_domain>`. Tất cả các truy vấn wildcard cho ứng dụng đều phải phân giải về địa chỉ IP của bộ cân bằng tải (load balancer) dành cho ingress của ứng dụng:

  ```shell
  $ dig +noall +answer @<nameserver_ip> random.apps.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  random.apps.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  > **Lưu ý:** Trong các kết quả đầu ra ví dụ, cùng một bộ cân bằng tải (load balancer) được sử dụng cho cả Kubernetes API và lưu lượng truy cập ứng dụng (application ingress). Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và cho lưu lượng truy cập ứng dụng, nhờ đó có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

  Bạn có thể thay thế `random` bằng một giá trị đại diện (wildcard) khác. Chẳng hạn, bạn có thể truy vấn đường dẫn (route) đến bảng điều khiển OpenShift Container Platform:

  ```shell
  $ dig +noall +answer @<nameserver_ip> console-openshift-console.apps.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  console-openshift-console.apps.ocp4.example.com. 604800 IN  A 192.168.1.5
  ```

  * Thực hiện tra cứu dựa trên tên bản ghi DNS bootstrap. Kiểm tra để đảm bảo kết quả trỏ đến địa chỉ IP của nút bootstrap:

  ```shell
  $ dig +noall +answer @<nameserver_ip> bootstrap.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  bootstrap.ocp4.example.com.   604800  IN  A 192.168.1.96
  ```

  * Sử dụng phương pháp này để thực hiện tra cứu dựa trên các tên bản ghi DNS của các nút mặt phẳng điều khiển (control plane) và các nút tính toán (compute nodes). Hãy kiểm tra để đảm bảo rằng các kết quả thu được tương ứng với địa chỉ IP của từng nút.
2. Từ nút cài đặt của bạn, hãy thực hiện truy vấn DNS ngược (reverse DNS lookup) đối với các địa chỉ IP của bộ cân bằng tải (load balancer) và các nút trong cụm (cluster nodes). Hãy xác minh rằng các tên bản ghi (record names) trong phản hồi tương ứng với các thành phần chính xác. 
  * Thực hiện truy vấn ngược đối với địa chỉ IP của bộ cân bằng tải API. Kiểm tra xem phản hồi có bao gồm các tên bản ghi dành cho Kubernetes API và Kubernetes internal API hay không:

  ```shell
  $ dig +noall +answer @<nameserver_ip> -x 192.168.1.5
  ```

  Ví dụ đầu ra

  ```
  5.1.168.192.in-addr.arpa. 604800  IN  PTR api-int.ocp4.example.com.
  5.1.168.192.in-addr.arpa. 604800  IN  PTR api.ocp4.example.com.
  ```

    Trong đó:
    * `api-int.ocp4.example.com`: Chỉ định tên bản ghi cho API nội bộ của Kubernetes.
    * `api.ocp4.example.com`: Chỉ định tên bản ghi cho API của Kubernetes.

    > **Lưu ý:** Bản ghi PTR là không bắt buộc đối với wildcard ứng dụng trên OpenShift Container Platform. Không cần thực hiện bước xác thực nào đối với quá trình phân giải DNS ngược (reverse DNS resolution) cho địa chỉ IP của bộ cân bằng tải ingress (ingress load balancer) của ứng dụng.
  * Thực hiện tra cứu ngược (reverse lookup) đối với địa chỉ IP của nút khởi động (bootstrap node). Kiểm tra xem kết quả có trỏ đến tên bản ghi DNS của nút khởi động đó hay không:

  ```shell
  $ dig +noall +answer @<nameserver_ip> -x 192.168.1.96
  ```

  Ví dụ đầu ra

  ```
  96.1.168.192.in-addr.arpa. 604800 IN  PTR bootstrap.ocp4.example.com.
  ```

  * Sử dụng phương pháp này để thực hiện tra cứu ngược đối với các địa chỉ IP của các nút điều khiển (control plane) và các nút tính toán (compute nodes). Hãy kiểm tra để đảm bảo rằng các kết quả thu được tương ứng với tên bản ghi DNS của từng nút.

#### 2.1.7. Tạo cặp khóa để truy cập SSH vào các nút cụm (cluster node)
Trong quá trình cài đặt OpenShift Container Platform, bạn có thể cung cấp khóa công khai (public key) SSH cho chương trình cài đặt. Khóa này được chuyển đến các nút Red Hat Enterprise Linux CoreOS (RHCOS) thông qua các tệp cấu hình Ignition và được thêm vào tệp `~/.ssh/authorized_keys` của người dùng `core` trên mỗi nút.

Khóa này được thêm vào danh sách `~/.ssh/authorized_keys` của người dùng `core` trên từng nút, cho phép xác thực mà không cần mật khẩu. Sau khi khóa được chuyển đến các nút, bạn có thể sử dụng cặp khóa này để truy cập SSH vào các nút RHCOS với tư cách là người dùng `core`. Để truy cập các nút thông qua SSH, khóa bí mật (private key) phải được quản lý bởi SSH trên máy cục bộ của bạn.

Nếu bạn muốn truy cập SSH vào các nút trong cụm để thực hiện gỡ lỗi cài đặt hoặc khôi phục sau sự cố, bạn bắt buộc phải cung cấp khóa công khai SSH trong quá trình cài đặt. Lệnh `./openshift-install gather` cũng yêu cầu khóa công khai SSH phải được thiết lập sẵn trên các nút của cụm.

> **Quan trọng:** Đừng bỏ qua bước này trong các môi trường thực tế (production), nơi việc khôi phục sau sự cố và gỡ lỗi là cần thiết.

> **Lưu ý:** Bạn phải sử dụng khóa cục bộ, không phải khóa được cấu hình theo các phương thức dành riêng cho từng nền tảng (platform-specific).

**Quy trình**

1. Nếu bạn chưa có cặp khóa SSH trên máy cục bộ để sử dụng cho việc xác thực vào các nút trong cụm, hãy tạo một cặp khóa mới. Ví dụ, trên máy tính sử dụng hệ điều hành Linux, hãy chạy lệnh sau:

```shell
$ ssh-keygen -t ed25519 -N '' -f <path>/<file_name>
```

Chỉ định đường dẫn và tên tệp (ví dụ: `~/.ssh/id_ed25519`) cho khóa SSH mới. Nếu bạn đã có sẵn một cặp khóa, hãy đảm bảo rằng khóa công khai (public key) của bạn nằm trong thư mục `~/.ssh`.

> **Lưu ý:** Nếu bạn dự định cài đặt cụm OpenShift Container Platform sử dụng các thư viện mật mã RHEL — vốn đã được gửi tới NIST để đánh giá theo tiêu chuẩn FIPS 140-2/140-3 trên các kiến trúc `x86_64`, `ppc64le` và `s390x` — thì không nên tạo khóa sử dụng thuật toán `ed25519`. Thay vào đó, hãy tạo khóa sử dụng thuật toán `rsa` hoặc `ecdsa`.

2. Xem khóa SSH công khai:

```shell
$ cat <path>/<file_name>.pub
```

Ví dụ, hãy chạy lệnh sau để xem khóa công khai `~/.ssh/id_ed25519.pub`:

```shell
$ cat ~/.ssh/id_ed25519.pub
```

3. Hãy thêm khóa cá nhân (private key) SSH vào SSH agent của người dùng cục bộ, nếu khóa đó chưa được thêm vào. Việc quản lý khóa thông qua SSH agent là yêu cầu bắt buộc để thực hiện xác thực SSH không cần mật khẩu vào các nút (node) trong cụm (cluster) của bạn, hoặc nếu bạn muốn sử dụng lệnh `./openshift-install gather`.

> **Lưu ý:** Trên một số bản phân phối, các khóa cá nhân SSH mặc định như `~/.ssh/id_rsa` và `~/.ssh/id_dsa` sẽ được tự động quản lý. 

  * Nếu tiến trình `ssh-agent` chưa chạy đối với người dùng cục bộ của bạn, hãy khởi chạy nó dưới dạng một tác vụ chạy ngầm (background task):

  ```shell
  $ eval "$(ssh-agent -s)"
  ```

  Ví dụ đầu ra

  ```
  Agent pid 31874
  ```

  > **Lưu ý:** Nếu cụm của bạn đang ở chế độ FIPS, chỉ sử dụng các thuật toán tuân thủ tiêu chuẩn FIPS để tạo khóa SSH. Khóa này phải thuộc loại RSA hoặc ECDSA.

4. Add your SSH private key to the `ssh-agent`: 

```shell
$ ssh-add <path>/<file_name>
```

Hãy chỉ định đường dẫn và tên tệp cho khóa riêng SSH của bạn, ví dụ như `~/.ssh/id_ed25519`.

Ví dụ về kết quả đầu ra

```
Identity added: /home/<you>/<path>/<file_name> (<computer_name>)
```

**Các bước tiếp theo**

Khi cài đặt OpenShift Container Platform, hãy cung cấp khóa công khai SSH cho chương trình cài đặt. Nếu bạn cài đặt cụm trên cơ sở hạ tầng do chính mình cung cấp, bạn bắt buộc phải cung cấp khóa này cho chương trình cài đặt.

#### 2.1.8. Tải xuống chương trình cài đặt
Trước khi cài đặt OpenShift Container Platform, hãy tải xuống tệp cài đặt về máy chủ mà bạn sẽ sử dụng cho quá trình cài đặt, để đảm bảo có sẵn các thành phần cần thiết cho việc triển khai trong môi trường của bạn.

**Điều kiện tiên quyết**

* Bạn cần có một máy tính chạy hệ điều hành Linux hoặc macOS, với 500 MB dung lượng ổ đĩa cục bộ.

**Quy trình**

1. Truy cập trang [Cluster Type](https://console.redhat.com/openshift/install) trên Red Hat Hybrid Cloud Console. Nếu đã có tài khoản Red Hat, hãy đăng nhập bằng thông tin xác thực của bạn. Nếu chưa có, hãy tạo một tài khoản mới. 

> **Mẹo:** Bạn cũng có thể [tải xuống](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/) các tệp nhị phân (binaries) cho một phiên bản OpenShift Container Platform cụ thể.

2. Chọn nhà cung cấp cơ sở hạ tầng của bạn từ mục **Run it yourself** trên trang này.
3. Chọn hệ điều hành máy chủ và kiến trúc hệ thống từ các menu thả xuống bên dưới mục **OpenShift Installer**, sau đó nhấp vào **Download Installer**.
4. Đặt tệp đã tải xuống vào thư mục mà bạn muốn dùng để lưu trữ các tệp cấu hình cài đặt.

> **Quan trọng**
> 
> * Chương trình cài đặt sẽ tạo ra một số tệp trên máy tính mà bạn sử dụng để cài đặt cụm (cluster) của mình. Bạn phải giữ lại chương trình cài đặt cùng các tệp do chương trình này tạo ra sau khi hoàn tất quá trình cài đặt cụm. Bạn sẽ cần đến các tệp này nếu muốn xóa cụm đó. 
> * Việc xóa các tệp do chương trình cài đặt tạo ra sẽ không loại bỏ cụm của bạn, ngay cả khi cụm đó gặp lỗi trong quá trình cài đặt. Để xóa cụm, hãy thực hiện các quy trình gỡ cài đặt OpenShift Container Platform dành riêng cho nhà cung cấp dịch vụ đám mây mà bạn đang sử dụng.

5. Giải nén chương trình cài đặt. Ví dụ, trên máy tính sử dụng hệ điều hành Linux, hãy chạy lệnh sau:

```shell
$ tar -xvf openshift-install-linux.tar.gz
```

7. Tải xuống [pull secret](https://console.redhat.com/openshift/install/pull-secret) dùng cho quá trình cài đặt từ Red Hat OpenShift Cluster Manager. "Pull secret" này cho phép bạn xác thực với các dịch vụ do những đơn vị được chỉ định cung cấp, bao gồm cả Quay.io – nơi lưu trữ các hình ảnh container (container images) cho các thành phần của OpenShift Container Platform.

> **Mẹo:** Ngoài ra, bạn có thể lấy chương trình cài đặt từ [Red Hat Customer Portal](https://access.redhat.com/downloads/content/290/); tại đây, bạn có thể chọn phiên bản chương trình cài đặt cụ thể để tải xuống. Tuy nhiên, bạn cần có gói đăng ký (subscription) còn hiệu lực để truy cập trang này.

#### 2.1.9. Cài đặt OpenShift CLI trên Linux
Để quản lý cụm (cluster) và triển khai các ứng dụng từ dòng lệnh trên hệ điều hành Linux, bạn cần cài đặt tệp nhị phân OpenShift CLI (oc). Bạn có thể tải xuống OpenShift CLI (oc) từ Cổng thông tin khách hàng Red Hat (Red Hat Customer Portal).

> **Quan trọng:** Nếu bạn đã cài đặt một phiên bản oc cũ hơn, bạn sẽ không thể sử dụng phiên bản đó để thực thi tất cả các lệnh trong OpenShift Container Platform.

Hãy tải xuống và cài đặt phiên bản oc mới nhất.

**Quy trình**

1. Truy cập trang Tải xuống OpenShift Container Platform (Download OpenShift Container Platform) trên Cổng thông tin khách hàng Red Hat. 
2. Chọn kiến trúc hệ thống từ danh sách Biến thể sản phẩm (Product Variant). 
3. Chọn phiên bản phù hợp từ danh sách Phiên bản (Version). 
4. Nhấp vào Tải xuống ngay (Download Now) bên cạnh mục OpenShift v4.22 Linux Clients và lưu tệp về máy. 
5. Giải nén tệp lưu trữ:

```shell
$ tar xvf <file>
```

6. Đặt tệp nhị phân `oc` vào một thư mục nằm trong biến môi trường `PATH` của bạn. Để kiểm tra biến `PATH`, hãy chạy lệnh sau:

```shell
$ echo $PATH
```

**Xác minh**

Sau khi cài đặt OpenShift CLI, bạn có thể sử dụng công cụ này thông qua lệnh `oc`:

```shell
$ oc <command>
```

#### 2.1.10. Cài đặt OpenShift CLI trên Windows
Để quản lý cụm (cluster) và triển khai các ứng dụng từ dòng lệnh trên Windows, bạn cần cài đặt tệp nhị phân OpenShift CLI (oc). Bạn có thể tải xuống OpenShift CLI (oc) từ Cổng thông tin khách hàng Red Hat (Red Hat Customer Portal).

> **Quan trọng:** Nếu bạn đã cài đặt phiên bản oc cũ hơn, bạn sẽ không thể sử dụng phiên bản đó để thực thi tất cả các lệnh trong OpenShift Container Platform. Tải xuống và cài đặt phiên bản oc mới nhất.

**Quy trình**

1. Truy cập trang [Download OpenShift Container Platform](https://access.redhat.com/downloads/content/290) trên Cổng thông tin khách hàng Red Hat. 
2. Chọn phiên bản phù hợp từ danh sách Version. 
3. Nhấp vào **Download Now** bên cạnh mục **OpenShift v4.22 Windows Client** và lưu tệp lại. 
4. Giải nén tệp lưu trữ bằng chương trình giải nén tệp ZIP. 
5. Di chuyển tệp nhị phân `oc` vào một thư mục nằm trong biến môi trường `PATH` của bạn. 

Để kiểm tra biến môi trường `PATH`, hãy mở Command Prompt và chạy lệnh sau:

```
C:\> path
```

**Xác minh**

Sau khi cài đặt OpenShift CLI, bạn có thể sử dụng công cụ này thông qua lệnh `oc`:

```
C:\> oc <command>
```

#### 2.1.11. Cài đặt OpenShift CLI trên macOS
Để quản lý cụm (cluster) và triển khai các ứng dụng từ dòng lệnh trên macOS, bạn cần cài đặt tệp nhị phân OpenShift CLI (oc). Bạn có thể tải xuống OpenShift CLI (oc) từ Cổng thông tin khách hàng Red Hat (Red Hat Customer Portal).

> **Quan trọng:** Nếu bạn đã cài đặt phiên bản oc cũ hơn, bạn sẽ không thể sử dụng phiên bản đó để thực hiện tất cả các lệnh trong OpenShift Container Platform. Hãy tải xuống và cài đặt phiên bản oc mới nhất.

**Quy trình**

1. Truy cập trang [Download OpenShift Container Platform](https://access.redhat.com/downloads/content/290) trên Cổng thông tin khách hàng Red Hat. 
2. Chọn kiến trúc hệ thống từ danh sách **Product Variant**. 
3. Chọn phiên bản phù hợp từ danh sách **Version**. 
4. Nhấp vào **Download Now** bên cạnh mục OpenShift v4.22 macOS Clients và lưu tệp lại. 

> **Lưu ý:** Đối với macOS arm64, hãy chọn mục **OpenShift v4.22 macOS arm64 Client**. 

5. Giải nén tệp lưu trữ.
6. Di chuyển tệp nhị phân oc vào một thư mục nằm trong biến môi trường `PATH` của bạn. Để kiểm tra biến môi trường `PATH`, hãy mở terminal và chạy lệnh sau:

```shell
$ echo $PATH
```

**Xác minh**

Hãy xác minh quá trình cài đặt của bạn bằng cách sử dụng lệnh oc:

```shell
$ oc <command>
```

#### 2.1.12. Tạo thủ công tệp cấu hình cài đặt
Việc cài đặt cụm (cluster) yêu cầu bạn phải tự tạo tệp cấu hình cài đặt.

**Điều kiện tiên quyết**

* Bạn đã có khóa công khai SSH (SSH public key) trên máy cục bộ để sử dụng với chương trình cài đặt. Bạn có thể dùng khóa này để xác thực SSH vào các nút (node) trong cụm nhằm mục đích gỡ lỗi và khôi phục sau sự cố. 
* Bạn đã tải xuống chương trình cài đặt OpenShift Container Platform và lấy "pull secret" (bí mật kéo hình ảnh) cho cụm của mình.

**Quy trình**

1. Tạo một thư mục cài đặt để lưu trữ các tài nguyên cần thiết cho quá trình cài đặt:

```shell
$ mkdir <installation_directory>
```

> **Quan trọng:** Bạn phải tạo một thư mục mới. Một số thành phần cài đặt, chẳng hạn như các chứng chỉ X.509 khởi tạo (bootstrap), có thời hạn hiệu lực ngắn; do đó, bạn không được sử dụng lại thư mục cài đặt cũ. Nếu muốn sử dụng lại các tệp tin riêng lẻ từ một quá trình cài đặt cụm khác, bạn có thể sao chép chúng vào thư mục của mình. Tuy nhiên, tên của các tệp tin cài đặt có thể thay đổi giữa các phiên bản phát hành. Hãy thận trọng khi sao chép các tệp tin cài đặt từ một phiên bản OpenShift Container Platform cũ hơn.

2. Hãy tùy chỉnh tệp mẫu `install-config.yaml` được cung cấp và lưu tệp đó vào `<installation_directory>`.

> **Lưu ý:** Bạn bắt buộc phải đặt tên cho tệp cấu hình này là `install-config.yaml`.

3. Hãy sao lưu tệp `install-config.yaml` để có thể sử dụng tệp này cho việc cài đặt nhiều cụm (cluster).

> **Quan trọng:** Hãy sao lưu tệp `install-config.yaml` ngay bây giờ, vì tệp này sẽ được sử dụng trong bước tiếp theo của quy trình cài đặt.

##### 2.1.12.1. Tệp install-config.yaml mẫu cho môi trường bare metal
Bạn có thể tùy chỉnh tệp install-config.yaml để chỉ định thêm thông tin chi tiết về nền tảng cụm OpenShift Container Platform của mình hoặc sửa đổi các giá trị của những tham số bắt buộc.

```yaml
apiVersion: v1
baseDomain: example.com
compute:
- hyperthreading: Enabled
  name: worker
  replicas: 0
controlPlane:
  hyperthreading: Enabled
  name: master
  replicas: 3
metadata:
  name: test
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  networkType: OVNKubernetes
  machineNetwork:
  - cidr: 192.168.0.0/16
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
fips: false
pullSecret: '{"auths": ...}'
sshKey: 'ssh-ed25519 AAAA...'
```

Trong đó:

* `baseDomain`: Xác định tên miền cơ sở (base domain) của cụm (cluster). Tất cả các bản ghi DNS phải là tên miền phụ (sub-domain) của tên miền cơ sở này và phải bao gồm tên của cụm.
* `compute`: Xác định cấu hình cho các nút tính toán (compute node), được biểu diễn dưới dạng một chuỗi các ánh xạ (mappings). Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `compute` phải bắt đầu bằng một dấu gạch ngang (-).
* `controlPlane`: Xác định cấu hình cho các nút điều khiển (control plane node), được biểu diễn dưới dạng một ánh xạ duy nhất. Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `controlPlane` không được bắt đầu bằng dấu gạch ngang. Chỉ một nhóm (pool) nút điều khiển được sử dụng.
* `hyperthreading`: Xác định việc bật hay tắt tính năng đa luồng đồng thời (Simultaneous Multithreading - SMT), hay còn gọi là `hyperthreading`. Theo mặc định, SMT được bật để tăng hiệu suất cho các lõi (core) trên máy chủ của bạn. Bạn có thể tắt tính năng này bằng cách đặt giá trị tham số thành `Disabled`. Nếu tắt SMT, bạn phải thực hiện tắt trên tất cả các máy chủ trong cụm, bao gồm cả các nút điều khiển (control plane) và các nút tính toán (compute).

  > **Lưu ý:** Tính năng đa luồng đồng thời (SMT) được bật theo mặc định. Nếu SMT chưa được bật trong cài đặt BIOS, thì tham số `hyperthreading` sẽ không có tác dụng.

  > **Quan trọng:** Nếu bạn tắt `hyperthreading` (dù là trong BIOS hay trong tệp `install-config.yaml`), hãy đảm bảo rằng kế hoạch phân bổ tài nguyên (capacity planning) của bạn đã tính đến việc hiệu suất máy chủ sẽ giảm đáng kể.

* `compute.replicas`: Xác định số lượng máy tính toán (compute machine) mà cụm (cluster) sẽ tạo và quản lý thay cho bạn trong các quy trình cài đặt do trình cài đặt tự động cung cấp (installer-provisioned installations). Bạn phải đặt giá trị này bằng 0 khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure). Ngoài ra, đối với các quy trình cài đặt do người dùng cung cấp, bạn phải tự triển khai các máy tính toán trước khi hoàn tất việc cài đặt cụm.

  > **Lưu ý:** Nếu bạn đang cài đặt một cụm gồm ba nút (three-node cluster), đừng triển khai thêm bất kỳ máy tính toán nào khi thực hiện cài đặt các máy Red Hat Enterprise Linux CoreOS (RHCOS).

* `controlPlane.replicas`: Xác định số lượng máy thuộc mặt phẳng điều khiển (control plane machine) mà bạn thêm vào cụm. Vì cụm sử dụng các giá trị này để xác định số lượng điểm cuối (endpoint) etcd trong cụm, nên giá trị này phải khớp với số lượng máy thuộc mặt phẳng điều khiển mà bạn triển khai.
* `metadata.name`: Xác định tên của cụm, tên này phải trùng với tên bạn đã chỉ định trong các bản ghi DNS của mình.
* `networking.clusterNetwork.cidr`: Xác định một khối địa chỉ IP dùng để cấp phát địa chỉ IP cho các pod. Khối địa chỉ này không được trùng lặp với các mạng vật lý hiện có. Các địa chỉ IP này được sử dụng cho mạng của pod. Nếu bạn cần truy cập vào các pod từ một mạng bên ngoài, bạn phải cấu hình bộ cân bằng tải (load balancer) và bộ định tuyến (router) để quản lý lưu lượng truy cập.

  > **Lưu ý:** Dải địa chỉ CIDR lớp E (Class E CIDR range) được dành riêng cho mục đích sử dụng trong tương lai. Để sử dụng dải địa chỉ CIDR lớp E, bạn phải đảm bảo rằng môi trường mạng của mình chấp nhận các địa chỉ IP nằm trong dải này.

* `networking.cidr.hostPrefix`: Xác định độ dài tiền tố mạng con (subnet prefix length) được gán cho mỗi nút (node). Ví dụ: nếu hostPrefix được đặt là 23, thì mỗi nút sẽ được gán một mạng con /23 từ dải CIDR đã cho, cho phép tạo ra 510 (2^(32 - 23) - 2) địa chỉ IP cho các pod. Nếu bạn cần cho phép truy cập vào các nút từ một mạng bên ngoài, hãy cấu hình bộ cân bằng tải (load balancer) và bộ định tuyến (router) để quản lý lưu lượng truy cập.
* `networking.networkType`: Xác định plugin mạng cụm (cluster network plugin) cần cài đặt. Giá trị mặc định là OVNKubernetes, đây cũng là giá trị duy nhất được hỗ trợ.
* `networking.serviceNetwork`: Xác định dải địa chỉ IP được sử dụng cho các địa chỉ IP dịch vụ (service IP). Bạn chỉ có thể nhập một dải địa chỉ IP duy nhất. Dải này không được trùng lặp với các mạng vật lý hiện có. Nếu bạn cần truy cập các dịch vụ từ một mạng bên ngoài, hãy cấu hình bộ cân bằng tải và bộ định tuyến để quản lý lưu lượng truy cập.
* `platform`: Xác định nền tảng (platform). Bạn bắt buộc phải đặt giá trị `platform` là "none". Bạn không thể cung cấp thêm các biến cấu hình nền tảng nào khác.

  > **Quan trọng:** Các cụm được cài đặt với loại nền tảng là "none" sẽ không thể sử dụng một số tính năng, chẳng hạn như quản lý các máy tính toán (compute machine) thông qua Machine API. Hạn chế này vẫn áp dụng ngay cả khi các máy tính toán được kết nối với cụm nằm trên một nền tảng vốn hỗ trợ tính năng đó. Tham số này không thể thay đổi sau khi đã hoàn tất cài đặt.

* `fips`: Xác định việc bật hoặc tắt chế độ FIPS. Theo mặc định, chế độ FIPS không được bật. Nếu chế độ FIPS được bật, các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) – nền tảng mà OpenShift Container Platform vận hành trên đó – sẽ bỏ qua bộ mật mã mặc định của Kubernetes và thay vào đó sử dụng các mô-đun mật mã được cung cấp kèm theo RHCOS.

> **Quan trọng:** Để bật chế độ FIPS cho cụm (cluster) của mình, bạn phải chạy chương trình cài đặt từ một máy tính sử dụng hệ điều hành Red Hat Enterprise Linux (RHEL) đã được cấu hình để hoạt động ở chế độ FIPS. Để biết thêm thông tin về cách cấu hình chế độ FIPS trên RHEL, hãy xem phần Chuyển đổi RHEL sang chế độ FIPS (Switching RHEL to FIPS mode).  
> Khi chạy Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã của RHEL – những thư viện đã được gửi tới NIST để thực hiện quy trình xác thực FIPS 140-2/140-3 (áp dụng cho các kiến trúc x86_64, ppc64le và s390x).

* `pullSecret`: Xác định "pull secret" (bí mật truy xuất) từ Red Hat OpenShift Cluster Manager. "Pull secret" này cho phép bạn xác thực với các dịch vụ được cung cấp bởi các đơn vị liên quan, bao gồm cả Quay.io – nơi lưu trữ các hình ảnh container (container images) cho các thành phần của OpenShift Container Platform.
* `sshKey`: Xác định khóa công khai SSH (SSH public key) dành cho người dùng cốt lõi (core user) trong hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS).

> **Lưu ý:** Đối với các cụm OpenShift Container Platform trong môi trường thực tế (production) mà bạn dự định thực hiện các thao tác gỡ lỗi cài đặt hoặc khôi phục sau sự cố, hãy chỉ định một khóa SSH mà tiến trình `ssh-agent` của bạn đang sử dụng.

##### 2.1.12.2. Cấu hình proxy cho toàn bộ cụm (cluster-wide proxy) trong quá trình cài đặt
Trong các môi trường thực tế (production), quyền truy cập trực tiếp vào internet có thể bị chặn; thay vào đó, hệ thống sẽ sử dụng proxy HTTP hoặc HTTPS. Bạn có thể cấu hình để cụm OpenShift Container Platform mới sử dụng proxy bằng cách thiết lập các thông số proxy trong tệp `install-config.yaml`.

> **Lưu ý:** Đối với các hệ thống cài đặt trên phần cứng vật lý (bare-metal), nếu bạn không gán địa chỉ IP cho các nút (node) từ dải IP được chỉ định trong trường `networking.machineNetwork[].cidr` tại tệp `install-config.yaml`, bạn bắt buộc phải thêm các địa chỉ đó vào trường `proxy.noProxy`.

**Điều kiện tiên quyết**

* Bạn đã có sẵn tệp `install-config.yaml`. 
* Bạn đã xem xét các trang web mà cụm của bạn cần truy cập và xác định xem có trang nào cần bỏ qua proxy hay không. Theo mặc định, proxy sẽ xử lý tất cả lưu lượng truy cập đi ra (egress traffic) từ cụm, bao gồm cả các yêu cầu gọi đến API của nhà cung cấp dịch vụ đám mây (cloud provider). Bạn cần thêm các trang web đó vào trường `spec.noProxy` của đối tượng Proxy (Proxy object) để bỏ qua proxy nếu cần thiết. 

> **Lưu ý:** Trường `status.noProxy` của đối tượng Proxy sẽ tự động bao gồm các giá trị từ các trường `networking.machineNetwork[].cidr`, `networking.clusterNetwork[].cidr` và `networking.serviceNetwork[]` trong cấu hình cài đặt của bạn.  
> Đối với các hệ thống cài đặt trên Amazon Web Services (AWS), Google Cloud, Microsoft Azure và Red Hat OpenStack Platform (RHOSP), trường `status.noProxy` của đối tượng Proxy cũng bao gồm cả điểm cuối siêu dữ liệu của thực thể (instance metadata endpoint - 169.254.169.254).

**Quy trình**

1. Chỉnh sửa tệp `install-config.yaml` của bạn và thêm các thiết lập proxy. Ví dụ:

```yaml
apiVersion: v1
baseDomain: my.domain.com
proxy:
  httpProxy: http://<username>:<pswd>@<ip>:<port>
  httpsProxy: https://<username>:<pswd>@<ip>:<port>
  noProxy: example.com
additionalTrustBundle: |
    -----BEGIN CERTIFICATE-----
    <MY_TRUSTED_CA_CERT>
    -----END CERTIFICATE-----
additionalTrustBundlePolicy: <policy_to_add_additionalTrustBundle>
# ...
```

  Trong đó:

  * `proxy.httpProxy`: Chỉ định URL proxy được sử dụng để thiết lập các kết nối HTTP bên ngoài cụm (cluster). Giao thức (scheme) của URL phải là `http`.
  * `proxy.httpsProxy`: Chỉ định URL proxy được sử dụng để thiết lập các kết nối HTTPS bên ngoài cụm.
  * `proxy.noProxy`: Chỉ định danh sách các tên miền đích, địa chỉ IP hoặc các dải mạng CIDR (được phân tách bằng dấu phẩy) không đi qua proxy. Thêm dấu . vào trước tên miền để chỉ khớp với các tên miền phụ (subdomain). Ví dụ: `.y.com` sẽ khớp với `x.y.com`, nhưng không khớp với `y.co`m. Sử dụng `*` để bỏ qua proxy cho tất cả các đích đến.
  * `additionalTrustBundle`: Nếu bạn chỉ định giá trị này, chương trình cài đặt sẽ tạo một config map có tên là user-ca-bundle trong namespace openshift-config để lưu trữ các chứng chỉ CA bổ sung. Nếu bạn chỉ định `additionalTrustBundle` cùng với ít nhất một thiết lập proxy, đối tượng `Proxy` sẽ tham chiếu đến config map user-ca-bundle trong trường `trustedCA`. Sau đó, Cluster Network Operator sẽ tạo một config map có tên `trusted-ca-bundle`, trong đó hợp nhất nội dung được chỉ định cho tham số `trustedCA` với bộ chứng chỉ tin cậy (trust bundle) của RHCOS. Bạn bắt buộc phải thiết lập trường `additionalTrustBundle`, trừ khi chứng chỉ định danh của proxy được ký bởi một cơ quan cấp chứng chỉ (CA) đã nằm trong bộ chứng chỉ tin cậy của RHCOS.
  * `additionalTrustBundlePolicy`: Chỉ định chính sách xác định cách cấu hình đối tượng `Proxy` để tham chiếu đến config map user-ca-bundle trong trường `trustedCA`. Các giá trị được phép là `Proxyonly` và `Always`. Sử dụng `Proxyonly` nếu muốn tham chiếu đến config map `user-ca-bundle` chỉ khi bạn cấu hình proxy `http/https`. Sử dụng `Always` nếu muốn luôn luôn tham chiếu đến config map `user-ca-bundle`. Giá trị mặc định là Proxyonly. Đây là tham số tùy chọn.

  > **Lưu ý:** Chương trình cài đặt không hỗ trợ trường `readinessEndpoints` của proxy.

  > **Lưu ý:** Nếu chương trình cài đặt bị hết thời gian chờ (timeout), hãy khởi động lại rồi hoàn tất quá trình triển khai bằng cách sử dụng lệnh `wait-for` của chương trình cài đặt. Ví dụ:
  > 
  > ```shell
  > ./openshift-install wait-for install-complete --log-level debug
  > ```

2. Hãy lưu tệp này lại và tham chiếu đến nó khi cài đặt OpenShift Container Platform.
Chương trình cài đặt sẽ tạo một proxy trên toàn cụm (cluster-wide proxy) có tên là `cluster`, sử dụng các thiết lập proxy từ tệp `install-config.yaml`. Nếu bạn không cung cấp các thiết lập proxy, chương trình cài đặt vẫn sẽ tạo một đối tượng Proxy cho cụm, nhưng với phần đặc tả (spec) để trống (giá trị `nil`).

> **Lưu ý:** Chỉ đối tượng Proxy có tên là `cluster` mới được hỗ trợ; bạn không thể tạo thêm các proxy khác.

##### 2.1.12.3. Cấu hình cụm ba nút (three-node cluster)
Để tạo các cụm có quy mô nhỏ hơn và tiết kiệm tài nguyên phục vụ cho mục đích thử nghiệm hoặc triển khai thực tế, bạn có thể thiết lập một cụm trên hạ tầng vật lý (bare-metal) mà không cần các máy tính toán (compute machine). Cấu hình tùy chọn này chỉ sử dụng ba máy thuộc mặt phẳng điều khiển (control plane), giúp tối ưu hóa tài nguyên hạ tầng cho các quản trị viên và nhà phát triển.

Trong các môi trường OpenShift Container Platform gồm ba nút, ba máy thuộc mặt phẳng điều khiển này cũng có thể đảm nhận vai trò lập lịch (schedulable); nghĩa là các khối lượng công việc (workload) ứng dụng của bạn sẽ chạy trực tiếp trên các máy này.

**Điều kiện tiên quyết**

* Bạn đã có sẵn tệp `install-config.yaml`.

**Quy trình**

* Đảm bảo rằng số lượng bản sao máy tính toán (compute replica) được đặt thành 0 trong tệp `install-config.yaml` của bạn, như được hiển thị trong đoạn cấu hình `compute` dưới đây:

```yaml
compute:
- name: worker
  platform: {}
  replicas: 0
# ...
```

> **Lưu ý:** Khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải đặt giá trị của tham số `replicas` cho các máy tính toán (compute machines) là 0, bất kể số lượng máy tính toán mà bạn triển khai là bao nhiêu. Đối với các quy trình cài đặt do trình cài đặt tự động thiết lập (installer-provisioned installations), tham số này kiểm soát số lượng máy tính toán mà cụm (cluster) sẽ tự tạo và quản lý cho bạn. Quy định này không áp dụng cho các quy trình cài đặt do người dùng tự cung cấp, nơi bạn phải tự triển khai các máy tính toán theo cách thủ công.

Đối với các cụm gồm ba nút (three-node cluster), hãy thực hiện các bước sau:

* Nếu bạn triển khai cụm gồm ba nút mà không có nút tính toán (compute node) nào, các pod của Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển (control plane nodes). Trong các triển khai cụm ba nút, bạn phải cấu hình bộ cân bằng tải (load balancer) cho lưu lượng truy cập ứng dụng (ingress load balancer) để định tuyến lưu lượng HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển. Xem thêm thông tin tại phần "Các yêu cầu về cân bằng tải cho cơ sở hạ tầng do người dùng cung cấp" (Load balancing requirements for user-provisioned infrastructure). 
* Khi tạo các tệp manifest của Kubernetes trong quy trình tiếp theo, hãy đảm bảo rằng tham số `mastersSchedulable` trong tệp `<installation_directory>/manifests/cluster-scheduler-02-config.yml` được đặt là `true`. Việc này cho phép các khối lượng công việc (workload) ứng dụng của bạn chạy trên các nút thuộc mặt phẳng điều khiển. 
* Không triển khai bất kỳ nút tính toán nào khi tạo các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS).

##### 2.1.12.4. Các điều kiện tiên quyết để cấu hình nút trọng tài cục bộ (local arbiter node)
Để duy trì tính sẵn sàng cao (HA) đồng thời giảm chi phí cơ sở hạ tầng cho cụm (cluster) của mình, bạn có thể cấu hình cụm OpenShift Container Platform với hai nút mặt phẳng điều khiển (control plane node) và một nút trọng tài cục bộ (local arbiter node).

Nút trọng tài cục bộ là một máy chủ có chi phí thấp hơn, được đặt cùng vị trí và tham gia vào các quyết định thiết lập hạn ngạch (quorum) của mặt phẳng điều khiển. Khác với nút mặt phẳng điều khiển tiêu chuẩn, nút trọng tài không chạy toàn bộ các dịch vụ của mặt phẳng điều khiển. Bạn có thể sử dụng cấu hình này để duy trì tính sẵn sàng cao trong cụm của mình chỉ với hai nút mặt phẳng điều khiển được cung cấp đầy đủ tài nguyên, thay vì ba nút.

> **Quan trọng:** Bạn chỉ có thể cấu hình nút trọng tài cục bộ. Các nút trọng tài từ xa (remote arbiter node) không được hỗ trợ.

Để triển khai một cụm gồm hai nút mặt phẳng điều khiển và một nút trọng tài cục bộ, bạn phải xác định các nút sau trong tệp `install-config.yaml`:

* 2 nút mặt phẳng điều khiển
* 1 nút trọng tài

Nút trọng tài phải đáp ứng các yêu cầu hệ thống tối thiểu sau:

* 2 luồng (threads)
* 8 GB RAM
* 50 GB ổ cứng SSD hoặc dung lượng lưu trữ tương đương

Nút trọng tài phải được đặt trong môi trường mạng có độ trễ đầu cuối (end-to-end latency) dưới 500 mili giây, bao gồm cả độ trễ I/O của ổ đĩa. Trong các môi trường có độ trễ cao, bạn có thể cần áp dụng cấu hình "etcd slow profile".

Các nút mặt phẳng điều khiển phải đáp ứng các yêu cầu hệ thống tối thiểu sau:

* 4 luồng (threads)
* 16 GB RAM
* 120 GB ổ cứng SSD hoặc dung lượng lưu trữ tương đương

Ngoài ra, các nút mặt phẳng điều khiển cũng phải có đủ dung lượng lưu trữ cho khối lượng công việc (workload).

##### 2.1.12.5. Cấu hình nút trọng tài (arbiter node) cục bộ bằng cơ sở hạ tầng do người dùng cung cấp
Để duy trì tính sẵn sàng cao (HA) đồng thời giảm chi phí cơ sở hạ tầng cho cụm (cluster) của mình, bạn có thể cấu hình cụm OpenShift Container Platform với hai nút mặt phẳng điều khiển (control plane node) và một nút trọng tài cục bộ.

**Điều kiện tiên quyết**

* Bạn đã tải xuống OpenShift CLI (oc) và chương trình cài đặt. 
* Bạn đã đăng nhập vào OpenShift CLI (oc).

**Quy trình**

1. Chỉnh sửa tệp `install-config.yaml` để xác định nút trọng tài cùng với các nút mặt phẳng điều khiển. 

Ví dụ về cấu hình `install-config.yaml` để triển khai một nút trọng tài

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
  name: <Specifies a name for the arbiter machine pool>
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
  none: {}
```

Đặt trường `arbiter.replicas` thành 1 cho nhóm arbiter (arbiter pool). Bạn không thể đặt trường này thành giá trị lớn hơn 1.

Đối với trường `controlPlane.replicas`, khi một nhóm arbiter được xác định, thì việc thiết lập hai bản sao (replica) cho control plane là hợp lệ.

2. Lưu tệp `install-config.yaml` đã sửa đổi.

#### 2.1.13. Tạo các tệp manifest Kubernetes và tệp cấu hình Ignition
Vì bạn tự thiết lập cơ sở hạ tầng theo cách thủ công, bạn cần tạo các tệp manifest Kubernetes và tệp cấu hình Ignition mà cụm (cluster) yêu cầu.

Chương trình cài đặt sẽ chuyển đổi cấu hình cài đặt thành các tệp manifest Kubernetes, sau đó đóng gói chúng vào các tệp cấu hình Ignition. Bạn sử dụng các tệp Ignition này để cấu hình các máy trong cụm.

> **Lưu ý quan trọng**  
> * Các tệp cấu hình Ignition do chương trình cài đặt OpenShift Container Platform tạo ra chứa các chứng chỉ có thời hạn 24 giờ; sau đó, hệ thống sẽ tự động gia hạn các chứng chỉ này. Nếu bạn tắt cụm trước khi hệ thống kịp gia hạn chứng chỉ và khởi động lại cụm sau khi đã quá 24 giờ, cụm sẽ tự động khôi phục các chứng chỉ đã hết hạn. Một ngoại lệ là bạn phải phê duyệt thủ công các yêu cầu ký chứng chỉ (CSR) của node-bootstrapper đang ở trạng thái chờ để khôi phục các chứng chỉ kubelet. Để biết thêm thông tin, hãy xem tài liệu về cách khôi phục khi các chứng chỉ control plane (mặt phẳng điều khiển) hết hạn. 
> * Sử dụng các tệp cấu hình Ignition trong vòng 12 giờ kể từ khi tạo, vì quá trình xoay vòng chứng chỉ 24 giờ thường diễn ra từ 16 đến 22 giờ sau khi cài đặt cụm. Việc sử dụng các tệp Ignition trong vòng 12 giờ giúp tránh lỗi cài đặt trong trường hợp quá trình cập nhật chứng chỉ diễn ra đúng lúc đang cài đặt.

**Điều kiện tiên quyết**

* Bạn đã có chương trình cài đặt OpenShift Container Platform. 
* Bạn đã tạo tệp cấu hình cài đặt `install-config.yaml`.

**Quy trình**

1. Di chuyển đến thư mục chứa chương trình cài đặt OpenShift Container Platform và tạo các tệp manifest Kubernetes cho cụm:

```shell
$ ./openshift-install create manifests --dir <installation_directory>
```

Trong đó: `<installation_directory>` chỉ định thư mục cài đặt chứa tệp `install-config.yaml` mà bạn đã tạo.

> **Cảnh báo:** Nếu bạn đang cài đặt một cụm gồm ba nút (three-node cluster), hãy bỏ qua bước tiếp theo để cho phép các nút mặt phẳng điều khiển (control plane nodes) có thể nhận tác vụ (schedulable).

> **Quan trọng:** Khi bạn cấu hình các nút mặt phẳng điều khiển từ trạng thái mặc định là không nhận tác vụ (unschedulable) sang trạng thái có thể nhận tác vụ (schedulable), bạn sẽ cần thêm các gói đăng ký (subscriptions) bổ sung, vì lúc này các nút mặt phẳng điều khiển sẽ trở thành các nút tính toán (compute nodes).

2. Hãy xác minh rằng tham số `mastersSchedulable` trong tệp manifest Kubernetes `<installation_directory>/manifests/cluster-scheduler-02-config.yml` được đặt thành `false`. Thiết lập này ngăn không cho các pod được lập lịch trên các máy thuộc mặt phẳng điều khiển (control plane):
  * Mở tệp `<installation_directory>/manifests/cluster-scheduler-02-config.yml`. 
  * Tìm tham số `mastersSchedulable` và xác minh rằng tham số này được đặt thành `false`. 
  * Lưu tệp và thoát.
3. Để tạo các tệp cấu hình Ignition, hãy chạy lệnh sau từ thư mục chứa chương trình cài đặt:

```shell
$ ./openshift-install create ignition-configs --dir <installation_directory>
```

Trong đó: `<installation_directory>` chỉ định thư mục cài đặt tương ứng. 

Chương trình cài đặt sẽ tạo các tệp cấu hình Ignition cho các nút bootstrap, control plane (mặt phẳng điều khiển) và compute (nút tính toán) trong thư mục cài đặt này. Chương trình cũng tạo các tệp `kubeadmin-password` và `kubeconfig` bên trong thư mục `./<installation_directory>/auth`:

```
.
├── auth
│   ├── kubeadmin-password
│   └── kubeconfig
├── bootstrap.ign
├── master.ign
├── metadata.json
└── worker.ign
```

#### 2.1.14. Cài đặt RHCOS và khởi động quy trình bootstrap của OpenShift Container Platform
Để cài đặt OpenShift Container Platform trên cơ sở hạ tầng phần cứng vật lý (bare-metal) do bạn cung cấp, hãy cài đặt Red Hat Enterprise Linux CoreOS (RHCOS) bằng cách sử dụng các tệp cấu hình Ignition đã được tạo sẵn. Việc cung cấp các tệp này đảm bảo rằng quy trình bootstrap sẽ tự động bắt đầu sau khi các máy khởi động lại.

Nếu bạn đã định cấu hình cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các máy chạy RHCOS khởi động lại.

Để cài đặt RHCOS trên các máy, hãy thực hiện theo các bước sử dụng tệp ảnh ISO hoặc khởi động qua mạng (PXE booting).

> **Lưu ý:** Các bước triển khai nút tính toán (compute node) có trong tài liệu cài đặt này dành riêng cho RHCOS. Nếu bạn chọn triển khai các nút tính toán dựa trên RHEL, bạn sẽ chịu trách nhiệm quản lý vòng đời và bảo trì hệ điều hành, bao gồm thực hiện cập nhật hệ thống, áp dụng các bản vá và hoàn thành tất cả các tác vụ cần thiết khác. Chỉ các máy tính toán chạy RHEL 8 mới được hỗ trợ.

Bạn có thể định cấu hình RHCOS trong quá trình cài đặt qua ISO hoặc PXE bằng các phương pháp sau:

* Các tham số nhân (Kernel arguments): Bạn có thể sử dụng các tham số nhân để cung cấp thông tin cụ thể cho quá trình cài đặt. Ví dụ: bạn có thể chỉ định vị trí các tệp cài đặt RHCOS đã tải lên máy chủ HTTP của mình, cũng như vị trí tệp cấu hình Ignition tương ứng với loại nút mà bạn đang cài đặt. Đối với cài đặt qua PXE, bạn có thể sử dụng tham số APPEND để truyền các tham số này vào nhân (kernel) của trình cài đặt trực tiếp (live installer). Đối với cài đặt qua ISO, bạn có thể tạm dừng quá trình khởi động của trình cài đặt trực tiếp để thêm các tham số nhân. Trong cả hai trường hợp, bạn đều có thể sử dụng các tham số đặc biệt dạng `coreos.inst.*` để điều khiển trình cài đặt trực tiếp, cũng như các tham số khởi động tiêu chuẩn để bật hoặc tắt các dịch vụ nhân thông thường. 
* Các tệp cấu hình Ignition: Các tệp cấu hình Ignition (`*.ign`) của OpenShift Container Platform được thiết kế riêng cho từng loại nút mà bạn cài đặt. Bạn cần cung cấp vị trí tệp cấu hình Ignition tương ứng với nút bootstrap, nút điều khiển (control plane) hoặc nút tính toán trong quá trình cài đặt RHCOS để tệp này có hiệu lực ngay từ lần khởi động đầu tiên. Trong một số trường hợp đặc biệt, bạn có thể tạo một tệp cấu hình Ignition riêng biệt, giới hạn phạm vi hơn để truyền vào hệ thống đang chạy. Cấu hình Ignition đó có thể thực hiện một số tác vụ nhất định, chẳng hạn như thông báo trạng thái thành công cho hệ thống cung cấp tài nguyên (provisioning system) sau khi hoàn tất quá trình cài đặt. Cấu hình Ignition đặc biệt này được `coreos-installer` sử dụng để áp dụng vào lần khởi động đầu tiên của hệ thống đã cài đặt. Tuyệt đối không cung cấp trực tiếp các cấu hình Ignition tiêu chuẩn dành cho control plane (mặt phẳng điều khiển) hoặc compute node (nút tính toán) vào tệp ISO chạy trực tiếp (live ISO). 
* `coreos-installer`: Bạn có thể khởi động tệp ISO chạy trực tiếp (live ISO) vào giao diện dòng lệnh (shell prompt), cho phép chuẩn bị hệ thống chính thức theo nhiều cách khác nhau trước lần khởi động đầu tiên. Cụ thể, bạn có thể chạy lệnh `coreos-installer` để xác định các thành phần (artifacts) cần đưa vào, thao tác với các phân vùng ổ đĩa và thiết lập mạng. Trong một số trường hợp, bạn có thể cấu hình các tính năng trên hệ thống chạy trực tiếp rồi sao chép chúng sang hệ thống đã cài đặt. 

> **Lưu ý:** Kể từ phiên bản 0.17.0-3, `coreos-installer` yêu cầu hệ điều hành RHEL 9 trở lên để chạy chương trình. Bạn vẫn có thể sử dụng các phiên bản `coreos-installer` cũ hơn để tùy chỉnh các thành phần RHCOS của các bản phát hành OpenShift Container Platform mới hơn, cũng như cài đặt các image dành cho phần cứng vật lý (metal images) vào ổ đĩa. Hãy tham khảo mục "coreos-installer image mirror" để tải xuống các phiên bản nhị phân cũ hơn của `coreos-installer`.

Việc chọn cài đặt qua ISO hay PXE tùy thuộc vào tình huống cụ thể của bạn. Cài đặt qua PXE đòi hỏi phải có sẵn dịch vụ DHCP và cần chuẩn bị kỹ lưỡng hơn, nhưng lại giúp quy trình cài đặt trở nên tự động hóa hơn. Ngược lại, cài đặt qua ISO là quy trình thủ công hơn và có thể gây bất tiện nếu bạn cần thiết lập số lượng máy lớn.

##### 2.1.14.1. Cài đặt RHCOS bằng tệp ảnh ISO
Để cung cấp tài nguyên cho các máy vật lý hoặc máy ảo, hãy cài đặt RHCOS bằng tệp ảnh ISO có khả năng khởi động (bootable ISO image).

**Các điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm (cluster) của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn có một máy chủ HTTP mà cả máy tính của bạn và các máy bạn tạo ra đều có thể truy cập được. 
* Bạn đã xem qua phần cấu hình cài đặt RHCOS nâng cao để biết các cách thiết lập những tính năng khác nhau, chẳng hạn như mạng và phân vùng đĩa.

**Quy trình**

1. Lấy mã băm (digest) SHA512 cho từng tệp cấu hình Ignition của bạn. Ví dụ: trên một hệ thống chạy Linux, bạn có thể sử dụng lệnh sau để lấy mã băm SHA512 cho tệp cấu hình Ignition `bootstrap.ign` của mình:

```shell
$ sha512sum <installation_directory>/bootstrap.ign
```

Các giá trị tóm tắt (digest) được cung cấp cho `coreos-installer` ở một bước sau đó để xác thực tính nguyên bản của các tệp cấu hình Ignition trên các nút của cụm (cluster).

2. Hãy tải các tệp cấu hình Ignition cho bootstrap, control plane và compute node — vốn được chương trình cài đặt tạo ra — lên máy chủ HTTP của bạn. Ghi lại các URL của những tệp này.

> **Quan trọng:** Bạn có thể thêm hoặc thay đổi các thiết lập cấu hình trong các tệp Ignition trước khi lưu chúng lên máy chủ HTTP. Nếu bạn dự định bổ sung thêm các máy tính toán (compute machine) vào cụm (cluster) của mình sau khi hoàn tất quá trình cài đặt, đừng xóa các tệp này.

3. Từ máy chủ cài đặt, hãy xác minh rằng các tệp cấu hình Ignition đã sẵn sàng tại các URL tương ứng. Ví dụ sau đây thực hiện truy xuất tệp cấu hình Ignition cho nút bootstrap:

```shell
$ curl -k http://<HTTP_server>/bootstrap.ign
```

  * `<HTTP_server>`: Thay thế bootstrap.ign bằng master.ign hoặc worker.ign trong lệnh để xác nhận rằng các tệp cấu hình Ignition cho mặt phẳng điều khiển và các nút tính toán cũng có sẵn.

  Ví dụ về kết quả đầu ra

  ```
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0{"ignition":{"version":"3.2.0"},"passwd":{"users":[{"name":"core","sshAuthorizedKeys":["ssh-rsa...
  ```

4. Mặc dù bạn có thể lấy các hình ảnh (image) RHCOS cần thiết cho phương thức cài đặt hệ điều hành mà mình chọn từ trang lưu trữ hình ảnh RHCOS, nhưng cách thức được khuyến nghị để có được phiên bản hình ảnh RHCOS chính xác là thông qua kết quả đầu ra của lệnh `openshift-install`:

```shell
$ openshift-install coreos print-stream-json | grep '\.iso[^.]'
```

Ví dụ đầu ra

```
"location": "<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live.aarch64.iso",
"location": "<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live.ppc64le.iso",
"location": "<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live.s390x.iso",
"location": "<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live.x86_64.iso",
```

> **Quan trọng:** Các hình ảnh (image) RHCOS có thể không thay đổi theo mỗi phiên bản phát hành của OpenShift Container Platform. Bạn phải tải xuống các hình ảnh có phiên bản cao nhất nhưng không vượt quá phiên bản OpenShift Container Platform mà bạn đang cài đặt. Nếu có sẵn các hình ảnh khớp với phiên bản OpenShift Container Platform của bạn, hãy sử dụng các phiên bản đó. Chỉ sử dụng các tệp hình ảnh định dạng ISO cho quy trình này. Các tệp hình ảnh RHCOS định dạng qcow2 không được hỗ trợ cho kiểu cài đặt này.

Tên tệp ISO có dạng như ví dụ sau: `rhcos-<phiên bản>-live.<architecture>.iso`

5. Sử dụng tệp ISO để bắt đầu quá trình cài đặt RHCOS. Bạn có thể chọn một trong các phương thức sau:
  * Ghi tệp ảnh ISO ra đĩa và khởi động trực tiếp từ đĩa đó. 
  * Sử dụng tính năng chuyển hướng ISO (ISO redirection) thông qua giao diện quản trị từ xa (lights-out management - LOM).
6. Khởi động tệp ảnh ISO của RHCOS mà không cần chỉ định thêm tùy chọn nào hoặc can thiệp vào trình tự khởi động trực tiếp (live boot). Hãy đợi cho đến khi trình cài đặt khởi động vào giao diện dòng lệnh (shell prompt) trong môi trường RHCOS live.

> **Lưu ý:** Bạn có thể can thiệp vào quá trình khởi động cài đặt RHCOS để thêm các tham số nhân (kernel arguments). Tuy nhiên, đối với quy trình sử dụng tệp ISO này, bạn nên dùng lệnh `coreos-installer` theo các bước hướng dẫn dưới đây thay vì thêm tham số nhân.

7. Thực thi lệnh `coreos-installer` cùng với các tùy chọn phù hợp với yêu cầu cài đặt của bạn. Tối thiểu, bạn cần chỉ định URL dẫn đến tệp cấu hình Ignition (Ignition config file) tương ứng với loại nút (node type) và thiết bị đích nơi bạn muốn thực hiện cài đặt:

```shell
$ sudo coreos-installer install --ignition-url=http://<HTTP_server>/<node_type>.ign <device> \
--ignition-hash=sha512-<digest>
```

  * `<device>`: Bạn phải chạy lệnh `coreos-installer` với quyền `sudo`, vì người dùng `core` không có đủ đặc quyền quản trị (root) cần thiết để thực hiện quá trình cài đặt.
  * `<digest>`: Tùy chọn `--ignition-hash` là bắt buộc khi tệp cấu hình Ignition được lấy thông qua URL HTTP, nhằm xác thực tính nguyên bản của tệp cấu hình đó trên nút (node) cụm. `<digest>` chính là mã băm SHA512 của tệp cấu hình Ignition, được lấy từ bước trước đó.

  > **Lưu ý:** Nếu bạn muốn cung cấp các tệp cấu hình Ignition thông qua máy chủ HTTPS sử dụng giao thức TLS, bạn có thể thêm cơ quan cấp chứng chỉ (CA) nội bộ vào kho lưu trữ chứng chỉ tin cậy của hệ thống trước khi chạy `coreos-installer`.

Ví dụ dưới đây thực hiện khởi tạo cài đặt cho một nút khởi động (bootstrap node) trên thiết bị `/dev/sda`. Tệp cấu hình Ignition cho nút khởi động này được lấy từ một máy chủ web HTTP có địa chỉ IP là 192.168.1.2:

```shell
$ sudo coreos-installer install --ignition-url=http://192.168.1.2:80/installation_directory/bootstrap.ign /dev/sda \
--ignition-hash=sha512-a5a2d43879223273c9b60af66b44202a1d1248fc01cf156c46d4a79f552b6bad47bc8cc78ddf0116e80c59d2ea9e32ba53bc807afbca581aa059311def2c3e3b
```

8. Theo dõi tiến trình cài đặt RHCOS trên bảng điều khiển (console) của máy.

> **Quan trọng:** Hãy đảm bảo rằng quá trình cài đặt đã hoàn tất thành công trên từng nút (node) trước khi bắt đầu cài đặt OpenShift Container Platform. Việc quan sát quá trình cài đặt cũng giúp xác định nguyên nhân nếu xảy ra sự cố trong lúc cài đặt RHCOS.

9. Sau khi RHCOS được cài đặt, bạn phải khởi động lại hệ thống. Trong quá trình khởi động lại, hệ thống sẽ áp dụng tệp cấu hình Ignition mà bạn đã chỉ định.
10. Kiểm tra đầu ra trên bảng điều khiển để xác nhận rằng Ignition đã chạy.

Ví dụ về lệnh

```
Ignition: ran on 2022/03/14 14:48:33 UTC (this boot)
Ignition: user-provided config was applied
```

11. Tiếp tục tạo các máy khác cho cụm (cluster) của bạn.

> **Quan trọng:** Tại thời điểm này, bạn phải tạo các máy bootstrap và các máy control plane (mặt phẳng điều khiển). Nếu các máy control plane không được thiết lập để có thể lập lịch (schedulable), bạn cũng cần tạo thêm ít nhất hai máy tính toán (compute machine) trước khi cài đặt OpenShift Container Platform.

Nếu các cơ sở hạ tầng cần thiết về mạng, DNS và bộ cân bằng tải (load balancer) đã sẵn sàng, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các nút (node) chạy hệ điều hành RHCOS khởi động lại.

> **Lưu ý:** Các nút RHCOS không có mật khẩu mặc định cho người dùng `core`. Bạn có thể truy cập các nút này bằng cách chạy lệnh `ssh core@<node>.<cluster_name>.<base_domain>` với tư cách là người dùng có quyền truy cập vào khóa cá nhân SSH (SSH private key) tương ứng với khóa công khai (public key) mà bạn đã chỉ định trong tệp `install_config.yaml`. Các nút trong cụm OpenShift Container Platform 4 chạy RHCOS là các thành phần không thể thay đổi (immutable) và dựa vào các Operator để thực hiện các thay đổi đối với cụm. Việc truy cập vào các nút của cụm thông qua SSH không được khuyến khích. Tuy nhiên, khi cần điều tra các sự cố cài đặt — chẳng hạn như khi API của OpenShift Container Platform không khả dụng hoặc `kubelet` trên một nút mục tiêu không hoạt động bình thường — bạn có thể cần truy cập qua SSH để gỡ lỗi hoặc khôi phục hệ thống trong trường hợp xảy ra sự cố nghiêm trọng.

##### 2.1.14.2. Cài đặt RHCOS bằng phương thức khởi động PXE hoặc iPXE
Bạn có thể sử dụng phương thức khởi động PXE hoặc iPXE để cài đặt RHCOS trên các máy chủ.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm (cluster) của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn đã thiết lập cơ sở hạ tầng PXE hoặc iPXE phù hợp. 
* Bạn có một máy chủ HTTP có thể truy cập được từ máy tính của bạn cũng như từ các máy chủ mà bạn tạo ra. 
* Bạn đã xem qua phần cấu hình cài đặt RHCOS nâng cao để biết các cách thiết lập những tính năng khác nhau, chẳng hạn như mạng và phân vùng đĩa.

**Quy trình**

1. Tải các tệp cấu hình Ignition dành cho nút bootstrap, nút điều khiển (control plane) và nút tính toán (compute node) — vốn được tạo bởi chương trình cài đặt — lên máy chủ HTTP của bạn. Hãy ghi lại các URL của những tệp này. 

> **Quan trọng:** Bạn có thể thêm hoặc thay đổi các thiết lập cấu hình trong các tệp Ignition trước khi lưu chúng lên máy chủ HTTP. Nếu bạn dự định bổ sung thêm các nút tính toán vào cụm sau khi hoàn tất cài đặt, đừng xóa các tệp này. 

2. Từ máy chủ thực hiện cài đặt (installation host), hãy xác thực rằng các tệp cấu hình Ignition đã sẵn sàng tại các URL tương ứng. Ví dụ dưới đây cho thấy cách truy xuất tệp cấu hình Ignition dành cho nút bootstrap:

```shell
$ curl -k http://<HTTP_server>/bootstrap.ign
```

  * `<HTTP_server>`: Thay thế bootstrap.ign bằng master.ign hoặc worker.ign trong lệnh để xác nhận rằng các tệp cấu hình Ignition cho mặt phẳng điều khiển và các nút tính toán cũng có sẵn.

  Ví dụ về kết quả đầu ra

  ```
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0{"ignition":{"version":"3.2.0"},"passwd":{"users":[{"name":"core","sshAuthorizedKeys":["ssh-rsa...
  ```

3. Mặc dù bạn có thể lấy các tệp RHCOS kernel, initramfs và rootfs — vốn cần thiết cho phương thức cài đặt hệ điều hành mà bạn chọn — từ trang lưu trữ (mirror) hình ảnh RHCOS, nhưng cách thức được khuyến nghị để có được phiên bản tệp RHCOS chính xác là thông qua kết quả đầu ra từ lệnh `openshift-install`:

```shell
$ openshift-install coreos print-stream-json | grep -Eo '"https.*(kernel-|initramfs.|rootfs.)\w+(\.img)?"'
```

Ví dụ đầu ra

```
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-kernel-aarch64"
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-initramfs.aarch64.img"
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-rootfs.aarch64.img"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/49.84.202110081256-0/ppc64le/rhcos-<release>-live-kernel-ppc64le"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live-initramfs.ppc64le.img"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live-rootfs.ppc64le.img"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-kernel-s390x"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-initramfs.s390x.img"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-rootfs.s390x.img"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-kernel-x86_64"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-initramfs.x86_64.img"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-rootfs.x86_64.img"
```

> **Quan trọng:** Các tệp (artifact) RHCOS có thể không thay đổi trong mỗi bản phát hành của OpenShift Container Platform. Bạn phải tải xuống các hình ảnh (image) có phiên bản cao nhất nhưng không vượt quá phiên bản OpenShift Container Platform mà bạn đang cài đặt. Chỉ sử dụng các tệp kernel, initramfs và rootfs phù hợp được mô tả dưới đây cho quy trình này. Các hình ảnh RHCOS định dạng QCOW2 không được hỗ trợ cho kiểu cài đặt này.

Tên tệp bao gồm số phiên bản của OpenShift Container Platform. Ví dụ như sau:

  * `kernel`: `rhcos-<version>-live-kernel-<architecture>`
  * initramfs: `rhcos-<version>-live-initramfs.<architecture>.img`
  * `rootfs`: `rhcos-<version>-live-rootfs.<architecture>.img`

4. Tải các tệp `rootfs`, `kernel` và `initramfs` lên máy chủ HTTP của bạn.

> **Quan trọng:** Nếu bạn dự định thêm các máy tính toán (compute machine) vào cụm (cluster) của mình sau khi hoàn tất cài đặt, đừng xóa các tệp này.

5. Cấu hình cơ sở hạ tầng khởi động qua mạng (network boot) sao cho các máy sẽ khởi động từ ổ đĩa cục bộ sau khi RHCOS đã được cài đặt trên đó.
6. Cấu hình cài đặt qua PXE hoặc iPXE cho các hình ảnh RHCOS và bắt đầu quá trình cài đặt.
7. Chỉnh sửa một trong các mục menu mẫu dưới đây cho phù hợp với môi trường của bạn, đồng thời xác minh rằng các tệp hình ảnh (image) và tệp Ignition đều có thể truy cập được:
  * Đối với PXE (x86_64):

    ```
    DEFAULT pxeboot
    TIMEOUT 20
    PROMPT 0
    LABEL pxeboot
        KERNEL http://<HTTP_server>/rhcos-<version>-live-kernel-<architecture>
        APPEND initrd=http://<HTTP_server>/rhcos-<version>-live-initramfs.<architecture>.img coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
    ```

    Trong đó:

    * `kernel`: Chỉ định vị trí của tệp kernel chạy trực tiếp (live kernel) mà bạn đã tải lên máy chủ HTTP của mình. URL phải thuộc loại HTTP, TFTP hoặc FTP; các giao thức HTTPS và NFS không được hỗ trợ.
    * `initrd=main`: Nếu sử dụng nhiều card mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên card mạng có tên là `eno1`, hãy đặt `ip=eno1:dhcp`. Hãy chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP của mình. Giá trị của tham số `initrd` là vị trí của tệp `initramfs`, giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp `rootfs`, và giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition khởi động (bootstrap Ignition config). Bạn cũng có thể thêm các đối số kernel khác vào dòng `APPEND` để cấu hình mạng hoặc các tùy chọn khởi động khác.

    > **Lưu ý:** Cấu hình này không kích hoạt quyền truy cập qua bảng điều khiển nối tiếp (serial console) trên các máy tính sử dụng bảng điều khiển đồ họa. Để thiết lập một bảng điều khiển khác, hãy thêm một hoặc nhiều tham số `console=` vào dòng `APPEND`. Ví dụ, hãy thêm `console=tty0 console=ttyS0` để đặt cổng nối tiếp đầu tiên trên máy tính làm bảng điều khiển chính và bảng điều khiển đồ họa làm bảng điều khiển phụ.

  * Đối với iPXE (x86_64 + aarch64):

  ```
  kernel http://<HTTP_server>/rhcos-<version>-live-kernel-<architecture> initrd=main coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
  initrd --name main http://<HTTP_server>/rhcos-<version>-live-initramfs.<architecture>.img
  boot
  ```

    * `kernel`: Chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP của mình. Giá trị của tham số `kernel` là vị trí của tệp kernel; đối số `initrd=main` là bắt buộc để khởi động trên các hệ thống UEFI; giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp rootfs; và giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition dùng để khởi động (bootstrap). Nếu sử dụng nhiều card mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên một card mạng có tên là `eno1`, hãy đặt `ip=eno1:dhcp`.
    * `initrd`: Chỉ định vị trí của tệp initramfs mà bạn đã tải lên máy chủ HTTP của mình.

    > **Lưu ý:** Cấu hình này không kích hoạt quyền truy cập qua giao diện điều khiển nối tiếp (serial console) trên các máy tính đã có giao diện điều khiển đồ họa. Để thiết lập một giao diện điều khiển khác, hãy thêm một hoặc nhiều tham số `console=` vào dòng lệnh khởi động nhân hệ điều hành (kernel). Ví dụ, hãy thêm `console=tty0 console=ttyS0` để đặt cổng nối tiếp đầu tiên trên máy tính làm giao diện điều khiển chính và giao diện điều khiển đồ họa làm giao diện điều khiển phụ.

    > **Lưu ý:** Để khởi động nhân (kernel) CoreOS qua mạng trên kiến trúc aarch64, bạn cần sử dụng phiên bản iPXE được biên dịch với tùy chọn IMAGE_GZIP đã được kích hoạt.

  * Đối với PXE (sử dụng UEFI và Grub làm giai đoạn thứ hai) trên kiến trúc aarch64:

  ```
  menuentry 'Install CoreOS' {
      linux rhcos-<version>-live-kernel-<architecture>  coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
      initrd rhcos-<version>-live-initramfs.<architecture>.img
  }
  ```

    Trong đó:

    * `coreos.live.rootfs_url`: Chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP/TFTP của mình.
    * `kernel`: Giá trị của tham số `kernel` là vị trí của `kernel` trên máy chủ TFTP của bạn. Giá trị của tham số coreos.live.rootfs_url là vị trí của tệp `rootfs`, còn giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition khởi động (bootstrap Ignition config) trên máy chủ HTTP của bạn. Nếu sử dụng nhiều giao diện mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên một NIC có tên là `eno1`, hãy đặt `ip=eno1:dhcp`.
    * `initrd rhcos`: Chỉ định vị trí của tệp initramfs mà bạn đã tải lên máy chủ TFTP của mình.

8. Theo dõi tiến trình cài đặt RHCOS trên bảng điều khiển (console) của máy.

> **Quan trọng:** Hãy đảm bảo rằng quá trình cài đặt đã hoàn tất thành công trên từng nút (node) trước khi bắt đầu cài đặt OpenShift Container Platform. Việc quan sát quá trình cài đặt cũng giúp xác định nguyên nhân nếu xảy ra sự cố trong lúc cài đặt RHCOS.
9. Sau khi RHCOS được cài đặt, hệ thống sẽ khởi động lại. Trong quá trình khởi động lại, hệ thống sẽ áp dụng tệp cấu hình Ignition mà bạn đã chỉ định.
10. Kiểm tra đầu ra trên bảng điều khiển để xác nhận rằng Ignition đã chạy.

Ví dụ về lệnh

```
Ignition: ran on 2022/03/14 14:48:33 UTC (this boot)
Ignition: user-provided config was applied
```

11. Tiếp tục tạo các máy (machine) cho cụm (cluster) của bạn.

> **Quan trọng:** Tại bước này, bạn phải tạo các máy bootstrap và các máy control plane. Nếu các máy control plane không được thiết lập để có thể lập lịch (schedulable), bạn cũng cần tạo thêm ít nhất hai máy tính toán (compute machine) trước khi tiến hành cài đặt cụm.

Nếu các cơ sở hạ tầng cần thiết về mạng, DNS và bộ cân bằng tải (load balancer) đã sẵn sàng, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các nút (node) chạy hệ điều hành RHCOS khởi động lại.

> **Lưu ý:** Các nút chạy RHCOS không có mật khẩu mặc định cho người dùng `core`. Bạn có thể truy cập vào các nút này bằng lệnh `ssh core@<node>.<cluster_name>.<base_domain>`, với điều kiện bạn là người dùng sở hữu khóa cá nhân SSH (SSH private key) tương ứng với khóa công khai (public key) mà bạn đã chỉ định trong tệp `install_config.yaml`. Các nút trong cụm OpenShift Container Platform 4 chạy RHCOS là các thành phần không thể thay đổi (immutable); mọi thay đổi đối với cụm đều được thực hiện thông qua các Operator. Việc truy cập vào các nút của cụm thông qua SSH không được khuyến khích. Tuy nhiên, trong trường hợp cần điều tra các sự cố cài đặt — chẳng hạn như khi API của OpenShift Container Platform không khả dụng hoặc `kubelet` trên một nút mục tiêu không hoạt động bình thường — bạn có thể cần truy cập qua SSH để phục vụ mục đích gỡ lỗi hoặc khôi phục sau sự cố.

##### 2.1.14.3. Cấu hình cài đặt RHCOS nâng cao
Để áp dụng các cấu hình nâng cao không khả dụng thông qua các phương thức cài đặt mặc định, bạn cần thực hiện cung cấp (provision) thủ công các nút Red Hat Enterprise Linux CoreOS (RHCOS) cho OpenShift Container Platform.

Phương pháp này cho phép kiểm soát chi tiết cơ sở hạ tầng nút để đáp ứng các yêu cầu triển khai cụ thể, bao gồm:

Truyền các tham số nhân (kernel arguments) vào trình cài đặt trực tiếp (live installer)
Chạy `coreos-installer` theo cách thủ công từ hệ thống trực tiếp (live system)
Tùy chỉnh tệp ISO trực tiếp hoặc hình ảnh khởi động qua PXE (PXE boot image)

Các chủ đề về cấu hình nâng cao cho việc cài đặt thủ công Red Hat Enterprise Linux CoreOS (RHCOS) được trình bày chi tiết trong phần này bao gồm các nội dung về phân vùng đĩa, mạng và các phương thức cấu hình Ignition khác nhau.

###### 2.1.14.3.1. Sử dụng các tùy chọn mạng nâng cao cho quá trình cài đặt qua PXE và ISO
Theo mặc định, hệ thống mạng cho các nút (node) trong OpenShift Container Platform sử dụng DHCP để thu thập tất cả các thiết lập cấu hình cần thiết. Bạn có thể thiết lập địa chỉ IP tĩnh hoặc định cấu hình các thiết lập đặc biệt khác.

Để thiết lập địa chỉ IP tĩnh hoặc định cấu hình các thiết lập đặc biệt (ví dụ: gộp cổng mạng - bonding), bạn có thể thực hiện một trong các cách sau:

Truyền các tham số nhân (kernel parameter) đặc biệt khi khởi động trình cài đặt trực tiếp (live installer). 
Sử dụng cấu hình máy (machine config) để sao chép các tệp tin mạng vào hệ thống được cài đặt. 
Định cấu hình mạng từ giao diện dòng lệnh (shell prompt) của trình cài đặt trực tiếp, sau đó sao chép các thiết lập đó vào hệ thống được cài đặt để chúng có hiệu lực ngay khi hệ thống này khởi động lần đầu tiên.

Để định cấu hình cho quá trình cài đặt qua PXE hoặc iPXE, hãy sử dụng một trong các tùy chọn sau:

Tham khảo các bảng "coreos-installer và các tùy chọn khởi động cho quá trình cài đặt qua ISO và PXE". 
Sử dụng cấu hình máy (machine config) để sao chép các tệp tin mạng vào hệ thống được cài đặt.

Để định cấu hình cho quá trình cài đặt qua ISO, hãy thực hiện theo quy trình dưới đây.

**Quy trình**

1. Khởi động trình cài đặt ISO. 
2. Từ giao diện dòng lệnh của hệ thống trực tiếp (live system), hãy định cấu hình mạng cho hệ thống này bằng cách sử dụng các công cụ có sẵn trên RHEL, chẳng hạn như `nmcli` hoặc `nmtui`. 
3. Chạy lệnh `coreos-installer` để cài đặt hệ thống, đồng thời thêm tùy chọn `--copy-network` để sao chép cấu hình mạng. Ví dụ:

```shell
$ sudo coreos-installer install --copy-network \
     --ignition-url=http://host/worker.ign /dev/disk/by-id/scsi-<serial_number>
```

> **Quan trọng:** Tùy chọn `--copy-network` chỉ sao chép cấu hình mạng nằm trong thư mục `/etc/NetworkManager/system-connections`. Cụ thể, tùy chọn này không sao chép tên máy (hostname) của hệ thống.

4. Hãy khởi động lại vào hệ thống đã cài đặt.

###### 2.1.14.3.2. Phân vùng đĩa
Các phân vùng đĩa được tạo trên các nút (node) của cụm OpenShift Container Platform trong quá trình cài đặt Red Hat Enterprise Linux CoreOS (RHCOS). Mỗi nút RHCOS thuộc một kiến trúc cụ thể sẽ sử dụng cùng một sơ đồ phân vùng, trừ khi bạn thay đổi cấu hình phân vùng mặc định.

Trong quá trình cài đặt RHCOS, kích thước của hệ thống tệp gốc (root file system) sẽ được mở rộng để tận dụng toàn bộ dung lượng còn trống trên thiết bị đích.

> **Quan trọng:** Việc sử dụng sơ đồ phân vùng tùy chỉnh trên nút có thể khiến OpenShift Container Platform không giám sát hoặc không đưa ra cảnh báo đối với một số phân vùng trên nút đó. Để biết thêm thông tin về việc giám sát hệ thống tệp của máy chủ (host) khi sử dụng phân vùng tùy chỉnh, hãy xem phần Tìm hiểu về Giám sát Hệ thống tệp trong OpenShift (các điều kiện loại bỏ/eviction conditions).

OpenShift Container Platform giám sát hai định danh hệ thống tệp sau:

* `nodefs`: hệ thống tệp chứa thư mục `/var/lib/kubelet`. 
* `imagefs`: hệ thống tệp chứa thư mục `/var/lib/containers`.

Với sơ đồ phân vùng mặc định, cả `nodefs` và `imagefs` đều giám sát cùng một hệ thống tệp gốc là `/`.

Để thay đổi sơ đồ phân vùng mặc định khi cài đặt RHCOS trên một nút của cụm OpenShift Container Platform, bạn cần tạo các phân vùng riêng biệt. Chẳng hạn, nếu bạn muốn thêm một phân vùng lưu trữ riêng cho các container và hình ảnh container (container images), bạn có thể gắn (mount) thư mục `/var/lib/containers` vào một phân vùng riêng. Khi đó, kubelet sẽ giám sát `/var/lib/containers` như là thư mục `imagefs`, đồng thời giám sát hệ thống tệp gốc như là thư mục `nodefs`.

> **Quan trọng:** Nếu bạn đã tăng kích thước đĩa để chứa hệ thống tệp lớn hơn, hãy cân nhắc việc tạo một phân vùng riêng cho `/var/lib/containers`. Ngoài ra, đối với các đĩa sử dụng định dạng `xfs`, hãy cân nhắc việc điều chỉnh kích thước đĩa để giảm thiểu các vấn đề về tiêu tốn tài nguyên CPU phát sinh do số lượng nhóm cấp phát (allocation groups) quá lớn.

OpenShift Container Platform hỗ trợ thêm một phân vùng riêng để gắn lưu trữ vào thư mục `/var` hoặc một thư mục con của `/var`. Ví dụ:

* `/var/lib/containers`: Chứa các nội dung liên quan đến container; dung lượng này có thể tăng lên khi có thêm nhiều image và container được thêm vào hệ thống. 
* `/var/lib/etcd`: Chứa dữ liệu mà bạn có thể muốn tách riêng, chẳng hạn như để tối ưu hóa hiệu năng lưu trữ cho etcd. 
* `/var`: Chứa dữ liệu mà bạn có thể muốn tách riêng, ví dụ như để phục vụ mục đích kiểm toán (auditing). 

> **Quan trọng:** Đối với các ổ đĩa có dung lượng lớn hơn 100GB, và đặc biệt là lớn hơn 1TB, hãy tạo một phân vùng `/var` riêng biệt.

Việc lưu trữ nội dung của thư mục `/var` trên một phân vùng riêng giúp dễ dàng mở rộng không gian lưu trữ khi cần thiết, đồng thời cho phép cài đặt lại OpenShift Container Platform trong tương lai mà vẫn giữ nguyên dữ liệu đó. Phương pháp này giúp loại bỏ nhu cầu tải lại các container hoặc sao chép các tệp nhật ký (log) có dung lượng lớn trong quá trình cập nhật hệ thống.

Việc sử dụng phân vùng riêng cho thư mục `/var` (hoặc thư mục con của `/var`) cũng ngăn chặn tình trạng dữ liệu trong phân vùng đó tăng lên đến mức làm đầy hệ thống tệp gốc (root file system).

Quy trình dưới đây thiết lập một phân vùng `/var` riêng biệt bằng cách thêm một tệp cấu hình máy (machine config manifest) vào tệp cấu hình Ignition dành cho một loại nút (node type) cụ thể trong giai đoạn chuẩn bị cài đặt.

**Quy trình**

1. Trên máy chủ cài đặt (installation host), hãy di chuyển đến thư mục chứa chương trình cài đặt OpenShift Container Platform và tạo các tệp cấu hình Kubernetes (Kubernetes manifests) cho cụm (cluster):

```shell
$ openshift-install create manifests --dir <installation_directory>
```

2. Hãy tạo một tệp cấu hình Butane để thiết lập phân vùng bổ sung. Chẳng hạn, bạn có thể đặt tên tệp là `$HOME/clusterconfig/98-var-partition.bu`, thay đổi tên thiết bị đĩa thành tên thiết bị lưu trữ trên các hệ thống worker, đồng thời thiết lập dung lượng lưu trữ cho phù hợp. Ví dụ này đặt thư mục `/var` trên một phân vùng riêng biệt:

```yaml
variant: openshift
version: 4.22.0
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 98-var-partition
storage:
  disks:
  - device: /dev/disk/by-id/<device_name>
    partitions:
    - label: var
      start_mib: <partition_start_offset>
      size_mib: <partition_size>
      number: 5
  filesystems:
    - device: /dev/disk/by-partlabel/var
      path: /var
      format: xfs
      mount_options: [defaults, prjquota]
      with_mount_unit: true
```

  Trong đó:

  * `<device_name>`: Chỉ định tên thiết bị lưu trữ của ổ đĩa mà bạn muốn phân vùng.
  * `<partition_start_offset>`: Chỉ định giá trị độ lệch (offset) tối thiểu cho ổ đĩa khởi động. Để đạt hiệu suất tối ưu, hãy đặt giá trị độ lệch tối thiểu là 25000 mebibyte. Hệ thống tệp gốc (root file system) sẽ tự động được thay đổi kích thước để lấp đầy toàn bộ không gian khả dụng cho đến vị trí độ lệch đã chỉ định. Nếu không chỉ định giá trị độ lệch, hoặc nếu giá trị được chỉ định nhỏ hơn mức tối thiểu khuyến nghị, hệ thống tệp gốc sẽ quá nhỏ; điều này có thể dẫn đến việc các lần cài đặt lại RHCOS sau này ghi đè lên phần đầu của phân vùng dữ liệu.
  * `<partition_size>`: Chỉ định kích thước của phân vùng dữ liệu (tính bằng mebibyte).
  * `mount_options`: Tùy chọn gắn (mount option) `prjquota` phải được bật đối với các hệ thống tệp được sử dụng cho lưu trữ container. 

  > Lưu ý: Khi tạo một phân vùng `/var` riêng biệt, bạn không thể sử dụng các loại instance (instance type) khác nhau cho các nút tính toán (compute node) nếu các loại instance đó không có cùng tên thiết bị.

3. Tạo tệp manifest từ cấu hình Butane và lưu tệp đó vào thư mục clusterconfig/openshift. Chẳng hạn, hãy chạy lệnh sau:

```shell
$ butane $HOME/clusterconfig/98-var-partition.bu -o $HOME/clusterconfig/openshift/98-var-partition.yaml
```

4. Tạo các tệp cấu hình Ignition bằng cách chạy lệnh sau:

```shell
$ openshift-install create ignition-configs --dir <installation_directory>
```

  Trong đó: `<installation_directory>` chỉ định tên của thư mục cài đặt. 

  Các tệp cấu hình Ignition cho các nút bootstrap, control plane (mặt phẳng điều khiển) và compute (nút tính toán) được tạo trong thư mục cài đặt này:

  ```.
  ├── auth
  │   ├── kubeadmin-password
  │   └── kubeconfig
  ├── bootstrap.ign
  ├── master.ign
  ├── metadata.json
  └── worker.ign
  ```

  Các tệp trong thư mục `<installation_directory>/manifest` và `<installation_directory>/openshift` được đưa vào các tệp cấu hình Ignition, bao gồm cả tệp chứa đối tượng `MachineConfig` tùy chỉnh có tên là `98-var-partition`.

5. Tùy chọn: Bạn có thể áp dụng cấu hình phân vùng đĩa tùy chỉnh bằng cách tham chiếu đến các tệp cấu hình Ignition trong quá trình cài đặt RHCOS.

###### 2.1.14.3.3. Ví dụ về việc giữ lại các phân vùng hiện có
Đối với quá trình cài đặt qua ISO, bạn có thể thêm các tùy chọn vào lệnh `coreos-installer` để yêu cầu chương trình cài đặt giữ lại một hoặc nhiều phân vùng hiện có. Đối với quá trình cài đặt qua PXE, bạn có thể thêm các tùy chọn `coreos.inst.*` vào tham số `APPEND` để bảo toàn các phân vùng đó.

Các phân vùng được giữ lại có thể là các phân vùng dữ liệu từ một hệ thống OpenShift Container Platform đã tồn tại. Bạn có thể xác định các phân vùng đĩa muốn giữ lại dựa trên nhãn phân vùng (label) hoặc số thứ tự phân vùng.

> **Lưu ý:** Nếu bạn giữ lại các phân vùng hiện có mà những phân vùng này không để lại đủ không gian trống cho RHCOS, quá trình cài đặt sẽ thất bại nhưng các phân vùng được giữ lại đó vẫn sẽ không bị hư hại.

Các ví dụ dưới đây minh họa việc bảo toàn bất kỳ phân vùng hiện có nào trong quá trình cài đặt qua ISO, với điều kiện nhãn của phân vùng đó bắt đầu bằng từ "data" (tức là `data*`):

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
--save-partlabel 'data*' \
/dev/disk/by-id/scsi-<serial_number>
```

Ví dụ sau đây chạy `coreos-installer` theo cách bảo toàn phân vùng thứ sáu (6) trên đĩa:

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
--save-partindex 6 /dev/disk/by-id/scsi-<serial_number>
```

Ví dụ sau đây giữ lại các phân vùng từ 5 trở lên:

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
--save-partindex 5- /dev/disk/by-id/scsi-<serial_number>
```

Trong các ví dụ trước đây có sử dụng tính năng bảo toàn phân vùng, `coreos-installer` sẽ tạo lại phân vùng đó ngay lập tức.

Các ví dụ dưới đây giúp giữ nguyên các phân vùng hiện có trong quá trình cài đặt qua PXE. Tùy chọn `APPEND` sau đây cho phép bảo toàn bất kỳ phân vùng nào có nhãn bắt đầu bằng chuỗi 'data' ('data*').

```
coreos.inst.save_partlabel=data*
```

Tùy chọn `APPEND` dưới đây bảo toàn các phân vùng từ 5 trở lên:

```
coreos.inst.save_partindex=5-
```

Tùy chọn `APPEND` sau đây bảo toàn phân vùng 6:

```
coreos.inst.save_partindex=6
```

###### 2.1.14.3.4. Xác định các cấu hình Ignition
Khi thực hiện cài đặt thủ công RHCOS, bạn có thể cung cấp hai loại cấu hình Ignition: cấu hình Ignition cho cài đặt cố định (permanent install) và cấu hình Ignition cho cài đặt trực tiếp (live install).

Khi cài đặt thủ công RHCOS, bạn có thể cung cấp hai loại cấu hình Ignition sau:

* **Cấu hình Ignition cho cài đặt cố định**: Mọi quá trình cài đặt thủ công RHCOS đều cần sử dụng một trong các tệp cấu hình Ignition do `openshift-installer` tạo ra — chẳng hạn như `bootstrap.ign`, `master.ign` hoặc `worker.ign` — để thực hiện việc cài đặt.

> **Quan trọng:** Không được sửa đổi trực tiếp các tệp cấu hình Ignition này. Thay vào đó, bạn có thể cập nhật các tệp manifest được đóng gói bên trong các tệp cấu hình Ignition đó, như đã nêu trong các ví dụ ở những phần trước.

Đối với cài đặt qua PXE, bạn có thể truyền các cấu hình Ignition thông qua dòng `APPEND` bằng cách sử dụng tùy chọn `coreos.inst.ignition_url=`. Đối với cài đặt qua ISO, sau khi ISO khởi động vào dấu nhắc lệnh (shell prompt), bạn phải chỉ định cấu hình Ignition trong dòng lệnh `coreos-installer` bằng tùy chọn `--ignition-url=`. Trong cả hai trường hợp, chỉ các giao thức HTTP và HTTPS mới được hỗ trợ. 

* **Cấu hình Ignition cho cài đặt trực tiếp (live install)**: Loại cấu hình này có thể được tạo bằng cách sử dụng lệnh con `customize` của `coreos-installer` cùng với các tùy chọn đi kèm. Với phương pháp này, cấu hình Ignition được truyền vào phương tiện cài đặt trực tiếp (live install medium), tự động chạy ngay khi khởi động và thực hiện các tác vụ thiết lập trước hoặc sau khi hệ thống RHCOS được cài đặt vào ổ đĩa. Phương pháp này chỉ nên được sử dụng cho các tác vụ cần thực hiện một lần duy nhất và không cần áp dụng lại sau đó; ví dụ như các thao tác phân vùng nâng cao mà không thể thực hiện thông qua `machine config`.

Đối với các trường hợp khởi động qua PXE hoặc ISO, bạn có thể tạo cấu hình Ignition và thêm tùy chọn `ignition.config.url=` vào dòng `APPEND` để xác định vị trí của tệp cấu hình đó. Bạn cũng cần bổ sung các tham số `ignition.firstboot` và `ignition.platform.id=metal`; nếu thiếu các tham số này, tùy chọn `ignition.config.url` sẽ bị bỏ qua.

###### 2.1.14.3.5. Cấu hình bảng điều khiển (console) mặc định
Các nút (node) Red Hat Enterprise Linux CoreOS (RHCOS) được cài đặt từ ảnh khởi động (boot image) của OpenShift Container Platform 4.22 sử dụng cấu hình bảng điều khiển mặc định, được thiết kế để phù hợp với hầu hết các thiết lập trên môi trường ảo hóa và máy chủ vật lý (bare metal).

Các nền tảng đám mây và ảo hóa khác nhau có thể sử dụng các thiết lập mặc định khác nhau tùy thuộc vào kiến trúc được chọn.

Đối với các hệ thống máy chủ vật lý, cấu hình mặc định của nhân hệ điều hành (kernel) thường được áp dụng; theo đó, bảng điều khiển đồ họa (graphical console) sẽ là bảng điều khiển chính, còn bảng điều khiển nối tiếp (serial console) sẽ bị vô hiệu hóa.

Cấu hình bảng điều khiển mặc định có thể không khớp với cấu hình phần cứng cụ thể của bạn, hoặc bạn có thể có những yêu cầu đặc thù đòi hỏi phải điều chỉnh lại cấu hình này. Ví dụ:

* Bạn cần truy cập vào trình bao khẩn cấp (emergency shell) thông qua bảng điều khiển để phục vụ mục đích gỡ lỗi. 
* Nền tảng đám mây của bạn không cung cấp quyền truy cập tương tác vào bảng điều khiển đồ họa, nhưng lại cung cấp bảng điều khiển nối tiếp. 
* Bạn muốn kích hoạt nhiều bảng điều khiển cùng lúc.

Cấu hình bảng điều khiển được kế thừa từ ảnh khởi động. Điều này có nghĩa là các nút mới trong các cụm (cluster) hiện có sẽ không bị ảnh hưởng bởi những thay đổi đối với cấu hình bảng điều khiển mặc định.

Bạn có thể cấu hình bảng điều khiển cho các hệ thống máy chủ vật lý theo những cách sau:

* Sử dụng công cụ `coreos-installer` theo cách thủ công trên dòng lệnh. 
* Sử dụng các lệnh con (subcommand) `coreos-installer iso customize` hoặc `coreos-installer pxe customize` cùng với tùy chọn `--dest-console` để tạo một ảnh tùy chỉnh giúp tự động hóa quy trình này.

> **Lưu ý:** Đối với các tùy chỉnh nâng cao, hãy thực hiện cấu hình bảng điều khiển thông qua các lệnh con `coreos-installer iso` hoặc `coreos-installer pxe`, thay vì sử dụng các tham số của nhân hệ điều hành (kernel arguments).

###### 2.1.14.3.6. Kích hoạt bảng điều khiển nối tiếp (serial console) cho các quá trình cài đặt qua PXE và ISO
Theo mặc định, bảng điều khiển nối tiếp trên Red Hat Enterprise Linux CoreOS (RHCOS) bị vô hiệu hóa và mọi thông tin đầu ra đều được hiển thị trên bảng điều khiển đồ họa. Bạn có thể kích hoạt bảng điều khiển nối tiếp cho các quá trình cài đặt qua PXE và ISO.

**Quy trình**

1. Khởi động trình cài đặt từ tệp ISO. 
2. Chạy lệnh `coreos-installer` để cài đặt hệ thống; trong đó, sử dụng tùy chọn `--console` hai lần: một lần để chỉ định bảng điều khiển đồ họa và lần thứ hai để chỉ định bảng điều khiển nối tiếp:

```shell
$ coreos-installer install \
--console=tty0 \
--console=ttyS0,<options> \
--ignition-url=http://host/worker.ign /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `--console=tty0`: Thiết bị console phụ mong muốn. Trong trường hợp này là console đồ họa. Nếu bỏ qua tùy chọn này, console đồ họa sẽ bị vô hiệu hóa.
  * `--console=ttyS0`: Thiết bị console chính mong muốn. Trong trường hợp này là console nối tiếp (serial console). Phần tùy chọn (options) xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho phần này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux (kernel) là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về console nối tiếp của nhân Linux.

3. Khởi động lại vào hệ thống đã cài đặt.

> **Lưu ý:** Bạn có thể đạt được kết quả tương tự bằng cách sử dụng tùy chọn `coreos-installer install --append-karg` và chỉ định bảng điều khiển (console) thông qua tham số `console=`. Tuy nhiên, cách này chỉ thiết lập bảng điều khiển cho nhân hệ điều hành (kernel) chứ không áp dụng cho bộ nạp khởi động (bootloader).

Để cấu hình cài đặt qua PXE, hãy đảm bảo rằng tùy chọn dòng lệnh nhân `coreos.inst.install_dev` không được đưa vào; sau đó, sử dụng dấu nhắc lệnh (shell prompt) để chạy thủ công `coreos-installer` theo quy trình cài đặt từ tệp ISO đã nêu ở trên.

###### 2.1.14.3.7. Tùy chỉnh cài đặt RHCOS qua live ISO hoặc PXE
Bạn có thể sử dụng tệp ảnh live ISO hoặc môi trường PXE để cài đặt RHCOS bằng cách đưa trực tiếp tệp cấu hình Ignition vào tệp ảnh đó. Thao tác này tạo ra một tệp ảnh tùy chỉnh mà bạn có thể dùng để triển khai hệ thống của mình.

Đối với tệp ảnh ISO, cơ chế thực hiện là sử dụng lệnh con `coreos-installer iso customize`; lệnh này sẽ sửa đổi tệp `.iso` dựa trên cấu hình của bạn. Tương tự, đối với môi trường PXE, bạn sử dụng lệnh con `coreos-installer pxe customize` để tạo tệp `initramfs` mới đã bao gồm các tùy chỉnh của bạn.

Lệnh phụ `customize` là một công cụ đa năng, cho phép tích hợp nhiều loại tùy chỉnh khác nhau. Dưới đây là một số ví dụ về các tùy chỉnh phổ biến:

* Thêm các chứng chỉ CA tùy chỉnh (trong trường hợp chính sách bảo mật của doanh nghiệp yêu cầu). 
* Cấu hình các thiết lập mạng mà không cần sử dụng các tham số nhân (kernel arguments). 
* Nhúng các tập lệnh (script) hoặc tệp nhị phân tùy ý để thực thi trước hoặc sau quá trình cài đặt.

###### 2.1.14.3.8. Tùy chỉnh tệp ISO RHCOS chạy trực tiếp (live ISO)
Bạn có thể tùy chỉnh trực tiếp tệp ISO RHCOS bằng lệnh con `iso customize` của `coreos-installer`. Khi khởi động từ tệp ISO này, các tùy chỉnh sẽ được tự động áp dụng. Bạn có thể sử dụng tính năng này để cấu hình tệp ISO sao cho tự động cài đặt RHCOS.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh của `coreos-installer`. 
2. Lấy tệp ISO RHCOS và tệp cấu hình Ignition từ trang lưu trữ hình ảnh RHCOS, sau đó chạy lệnh dưới đây để đưa cấu hình Ignition trực tiếp vào tệp ISO:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
    --dest-ignition bootstrap.ign \
    --dest-device /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `--dest-ignition`: Chỉ định tệp cấu hình Ignition được tạo ra từ chương trình cài đặt `openshift-installer`.
  * -`-dest-device`: Khi bạn chỉ định tùy chọn này, tệp ảnh ISO sẽ tự động thực thi quá trình cài đặt. Nếu không, tệp ảnh vẫn được cấu hình sẵn cho việc cài đặt nhưng sẽ không tự động thực thi, trừ khi bạn chỉ định tham số nhân (kernel argument) là `coreos.inst.install_dev`.

3. Tùy chọn: Để xóa các tùy chỉnh trên tệp ảnh ISO và đưa tệp ảnh trở về trạng thái ban đầu, hãy chạy lệnh sau:

```shell
$ coreos-installer iso reset rhcos-<version>-live.x86_64.iso
```

Giờ đây, bạn có thể tùy chỉnh lại tệp ảnh ISO trực tiếp (live ISO image) hoặc sử dụng nó ở trạng thái nguyên bản.

Việc áp dụng các tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.1.14.3.9. Chỉnh sửa tệp ảnh ISO cài đặt trực tiếp (live install ISO) để kích hoạt bảng điều khiển nối tiếp (serial console)

Để chuyển hướng đầu ra của hệ thống từ giao diện đồ họa mặc định, hãy kích hoạt bảng điều khiển nối tiếp bằng cách chỉnh sửa tệp ảnh ISO cài đặt trực tiếp. Cấu hình này đảm bảo khả năng truy cập vào các thông báo khởi động trên các cụm OpenShift Container Platform phiên bản 4.12 trở lên.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) tệp ảnh `coreos-installer`. 
2. Lấy tệp ảnh ISO RHCOS từ trang lưu trữ tệp ảnh RHCOS, sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO này, qua đó kích hoạt bảng điều khiển nối tiếp để nhận dữ liệu đầu ra:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
  --dest-ignition <path> \
  --dest-console tty0 \
  --dest-console ttyS0,<options> \
  --dest-device /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `<path>`: Vị trí của tệp cấu hình Ignition cần cài đặt.
  * tty0: Bảng điều khiển (console) phụ mong muốn. Trong trường hợp này là bảng điều khiển đồ họa. Nếu bỏ qua tùy chọn này, bảng điều khiển đồ họa sẽ bị vô hiệu hóa.
  * `<options>`: Bảng điều khiển chính mong muốn. Trong trường hợp này là bảng điều khiển nối tiếp (serial console). Trường tùy chọn này xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho trường này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux (kernel) là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về bảng điều khiển nối tiếp của nhân Linux.
  * `<serial_number>`: Ổ đĩa được chỉ định để thực hiện cài đặt. Nếu bạn bỏ qua tùy chọn này, tệp ảnh ISO sẽ tự động chạy chương trình cài đặt, nhưng quá trình này sẽ thất bại trừ khi bạn cũng chỉ định tham số nhân `coreos.inst.install_dev`. 

> **Lưu ý:** Tùy chọn `--dest-console` ảnh hưởng đến hệ thống sau khi cài đặt chứ không phải hệ thống chạy trực tiếp từ tệp ảnh ISO (live ISO). Để thay đổi bảng điều khiển cho hệ thống chạy từ tệp ảnh ISO, hãy sử dụng tùy chọn `--live-karg-append` và chỉ định bảng điều khiển thông qua tham số `console=`. 

Các tùy chỉnh của bạn sẽ được áp dụng và có hiệu lực trong mọi lần khởi động tiếp theo từ tệp ảnh ISO này.

3. Tùy chọn: Để xóa các tùy chỉnh trên tệp ảnh ISO và đưa tệp ảnh về trạng thái ban đầu, hãy chạy lệnh sau:

```shell
$ coreos-installer iso reset rhcos-<version>-live.x86_64.iso
```

Giờ đây, bạn có thể tùy chỉnh lại tệp ảnh ISO trực tiếp (live ISO image) hoặc sử dụng nó ở trạng thái nguyên bản.

###### 2.1.14.3.10. Sửa đổi tệp ISO cài đặt trực tiếp để sử dụng cơ quan cấp chứng chỉ (CA) tùy chỉnh
Bạn có thể cung cấp các chứng chỉ của cơ quan cấp chứng chỉ (CA) cho Ignition thông qua cờ `--ignition-ca` của lệnh con `customize`. Các chứng chỉ CA này có thể được sử dụng trong cả giai đoạn khởi động cài đặt lẫn giai đoạn cung cấp cấu hình (provisioning) cho hệ thống đã cài đặt.
Lưu ý

Các chứng chỉ CA tùy chỉnh ảnh hưởng đến cách Ignition truy xuất các tài nguyên từ xa, nhưng chúng không ảnh hưởng đến các chứng chỉ được cài đặt trên chính hệ thống đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh `coreos-installer`. 
2. Lấy tệp ISO RHCOS từ trang lưu trữ hình ảnh RHCOS, sau đó chạy lệnh dưới đây để tùy chỉnh tệp ISO này nhằm sử dụng với một CA tùy chỉnh:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso --ignition-ca cert.pem
```

> **Quan trọng:** Tham số nhân (kernel parameter) `coreos.inst.ignition_url` không hoạt động với cờ `--ignition-ca`. Bạn phải sử dụng cờ `--dest-ignition` để tạo một ảnh (image) tùy chỉnh cho mỗi cụm (cluster).

Việc áp dụng chứng chỉ CA tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.1.14.3.11. Chỉnh sửa tệp ảnh ISO cài đặt trực tiếp (live install ISO) với các thiết lập mạng tùy chỉnh
Bạn có thể nhúng một tệp khóa (keyfile) của NetworkManager vào tệp ảnh ISO cài đặt trực tiếp và chuyển nó sang hệ thống được cài đặt thông qua tham số `--network-keyfile` của lệnh con `customize`.

> **Cảnh báo:** Khi tạo hồ sơ kết nối (connection profile), bạn bắt buộc phải sử dụng phần mở rộng tệp là `.nmconnection`. Nếu không sử dụng phần mở rộng này, hệ thống sẽ áp dụng hồ sơ kết nối đó vào môi trường cài đặt trực tiếp (live environment), nhưng lại không áp dụng cấu hình này khi các nút (node) khởi động lần đầu, dẫn đến việc thiết lập không hoạt động.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) tệp ảnh `coreos-installer`. 
2. Tạo một hồ sơ kết nối cho giao diện mạng được gộp (bonded interface). Ví dụ: tạo tệp `bond0.nmconnection` trong thư mục cục bộ của bạn với nội dung như sau:

```
/[connection]
id=bond0
type=bond
interface-name=bond0
multi-connect=1

/[bond]
miimon=100
mode=active-backup

/[ipv4]
method=auto

/[ipv6]
method=auto
```

3. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em1.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em1
type=ethernet
interface-name=em1
master=bond0
multi-connect=1
slave-type=bond
```

4. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em2.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em2
type=ethernet
interface-name=em2
master=bond0
multi-connect=1
slave-type=bond
```

5. Hãy tải tệp ảnh ISO của RHCOS từ trang [lưu trữ (mirror) ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO này với các thiết lập mạng mà bạn đã cấu hình:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
    --network-keyfile bond0.nmconnection \
    --network-keyfile bond0-proxy-em1.nmconnection \
    --network-keyfile bond0-proxy-em2.nmconnection
```

Các thiết lập mạng được áp dụng cho hệ thống đang hoạt động và được chuyển sang hệ thống đích.

###### 2.1.14.3.12. Tùy chỉnh tệp ảnh ISO cài đặt trực tiếp (live install ISO) cho thiết bị khởi động iSCSI
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình bằng cách sử dụng một phiên bản tùy chỉnh của tệp ảnh RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) tệp ảnh `coreos-installer`. 
2. Lấy tệp ảnh ISO của RHCOS từ trang lưu trữ (mirror) tệp ảnh RHCOS, sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO đó với các thông tin sau:

```shell
$ coreos-installer iso customize \
    --pre-install mount-iscsi.sh \
    --post-install unmount-iscsi.sh \
    --dest-device /dev/disk/by-path/<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
    --dest-ignition config.ign \
    --dest-karg-append rd.iscsi.initiator=<initiator_iqn> \
    --dest-karg-append netroot=<target_iqn> \
    -o custom.iso rhcos-<version>-live.x86_64.iso
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * unmount-iscsi.sh: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
  * `<target_iqn>`: Xác định vị trí của hệ thống đích. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng (port number) tương ứng, nút iSCSI đích (target iSCSI node) ở định dạng IQN, và số đơn vị logic iSCSI (LUN).
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích. `<initiator_iqn>`::Xác định tên của iSCSI initiator (phía máy khách) ở định dạng IQN. Initiator này sẽ thiết lập một phiên kết nối tới iSCSI target. `<target_iqn>`::Xác định tên của iSCSI target (phía máy chủ) ở định dạng IQN.

Để biết thêm thông tin về các tùy chọn iSCSI được hỗ trợ bởi `dracut`, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.1.14.3.13. Tùy chỉnh tệp ảnh ISO cài đặt trực tiếp (live install ISO) cho thiết bị khởi động iSCSI sử dụng iBFT
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình hệ thống bằng cách sử dụng một phiên bản tùy chỉnh của tệp ảnh RHCOS dạng "live" (chạy trực tiếp).

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
* Tùy chọn: Bạn đã thiết lập đa đường dẫn (multipath) cho iSCSI target của mình.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) tệp ảnh `coreos-installer`. 
2. Lấy tệp ảnh ISO của RHCOS từ trang lưu trữ (mirror) tệp ảnh RHCOS, sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO đó với các thông tin sau:

```shell
$ coreos-installer iso customize \
    --pre-install mount-iscsi.sh \
    --post-install unmount-iscsi.sh \
    --dest-device /dev/mapper/mpatha \
    --dest-ignition config.ign \
    --dest-karg-append rd.iscsi.firmware=1 \
    --dest-karg-append rd.multipath=default \
    -o custom.iso rhcos-<version>-live.x86_64.iso
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này nên chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * unmount-iscsi.sh: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này nên chứa lệnh `iscsiadm --mode node --logout=all`.
  * `/dev/mapper/mpatha`: Chỉ định đường dẫn đến thiết bị. Nếu bạn đang sử dụng tính năng đa đường dẫn (multipath), thiết bị đó sẽ là `/dev/mapper/mpatha`. Trong trường hợp có nhiều thiết bị đa đường dẫn được kết nối, hoặc để xác định một cách rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN) có sẵn trong thư mục `/dev/disk/by-path`.
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích. `rd.iscsi.firmware=1` quy định rằng tham số iSCSI sẽ được đọc từ firmware của BIOS.
  * `rd.multipath=default`: Xác định xem bạn có muốn kích hoạt tính năng đa đường dẫn hay không. Đây là một tham số tùy chọn.

Để biết thêm thông tin, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) của `dracut.cmdline`.

###### 2.1.14.3.14. Tùy chỉnh môi trường RHCOS PXE trực tiếp (live)
Bạn có thể tùy chỉnh trực tiếp môi trường RHCOS PXE bằng lệnh con `coreos-installer pxe customize`. Khi khởi động môi trường PXE, các tùy chỉnh này sẽ được tự động áp dụng. Bạn có thể sử dụng tính năng này để cấu hình môi trường PXE nhằm tự động cài đặt RHCOS.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh `coreos-installer`. 
2. Lấy các tệp kernel, initramfs và rootfs của RHCOS từ trang lưu trữ hình ảnh RHCOS, cùng với tệp cấu hình Ignition; sau đó, chạy lệnh dưới đây để tạo một tệp initramfs mới chứa các tùy chỉnh từ tệp cấu hình Ignition của bạn:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
    --dest-ignition bootstrap.ign \
    --dest-device /dev/disk/by-id/scsi-<serial_number> \
    -o rhcos-<version>-custom-initramfs.x86_64.img
```

Trong đó:

* `--dest-ignition`: Chỉ định tệp cấu hình Ignition được tạo từ openshift-installer.
* `<serial_number>`: Khi bạn chỉ định tùy chọn này, môi trường PXE sẽ tự động thực thi quá trình cài đặt. Nếu không, hình ảnh (image) vẫn được cấu hình sẵn sàng cho việc cài đặt nhưng sẽ không tự động thực hiện trừ khi bạn chỉ định tham số nhân (kernel argument) `coreos.inst.install_dev`.
* `<version>`: Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn. 

Việc áp dụng các tùy chỉnh này sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.1.14.3.15. Chỉnh sửa môi trường PXE cài đặt trực tiếp (live install) để kích hoạt bảng điều khiển nối tiếp (serial console)
Để chuyển hướng đầu ra của hệ thống từ giao diện đồ họa mặc định, hãy kích hoạt bảng điều khiển nối tiếp bằng cách chỉnh sửa môi trường PXE cài đặt trực tiếp. Cấu hình này đảm bảo khả năng truy cập vào các thông báo khởi động trên các cụm OpenShift Container Platform phiên bản 4.12 trở lên.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh `coreos-installer`. 
2. Lấy các tệp nhân (kernel), `initramfs` và `rootfs` của RHCOS từ trang lưu trữ hình ảnh RHCOS, cùng với tệp cấu hình Ignition; sau đó, chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới, cho phép bảng điều khiển nối tiếp nhận dữ liệu đầu ra:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --dest-ignition <path> \
  --dest-console tty0 \
  --dest-console ttyS0,<options> \
  --dest-device /dev/disk/by-id/scsi-<serial_number> \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

Trong đó:

  * `<path>`: Vị trí của tệp cấu hình Ignition cần cài đặt.
  * `tty0`: Bảng điều khiển (console) phụ mong muốn. Trong trường hợp này là bảng điều khiển đồ họa. Nếu bỏ qua tùy chọn này, bảng điều khiển đồ họa sẽ bị vô hiệu hóa.
  * `<options>`: Bảng điều khiển chính mong muốn. Trong trường hợp này là bảng điều khiển nối tiếp (serial console). Trường tùy chọn này xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho trường này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux (kernel) là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về bảng điều khiển nối tiếp của nhân Linux.
  * `<serial_number>`: Ổ đĩa cụ thể được chọn để cài đặt. Nếu bỏ qua tùy chọn này, môi trường PXE sẽ tự động chạy chương trình cài đặt, nhưng quá trình này sẽ thất bại trừ khi bạn chỉ định thêm tham số nhân `coreos.inst.install_dev`.
  * `<version>`: Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn. 

Các tùy chỉnh của bạn sẽ được áp dụng và có hiệu lực trong mọi lần khởi động tiếp theo của môi trường PXE.

###### 2.1.14.3.16. Sửa đổi môi trường PXE cài đặt trực tiếp (live install) để sử dụng cơ quan cấp chứng chỉ (CA) tùy chỉnh
Bạn có thể cung cấp các chứng chỉ của cơ quan cấp chứng chỉ (CA) cho Ignition thông qua cờ `--ignition-ca` của lệnh con `customize`. Các chứng chỉ CA này có thể được sử dụng trong cả giai đoạn khởi động cài đặt lẫn giai đoạn cung cấp cấu hình (provisioning) cho hệ thống đã cài đặt.

> **Lưu ý:** Các chứng chỉ CA tùy chỉnh ảnh hưởng đến cách Ignition truy xuất các tài nguyên từ xa, nhưng chúng không ảnh hưởng đến các chứng chỉ được cài đặt trên chính hệ thống đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh `coreos-installer`. 
2. Lấy các tệp kernel, initramfs và rootfs của RHCOS từ trang lưu trữ hình ảnh RHCOS, sau đó chạy lệnh dưới đây để tạo một tệp initramfs tùy chỉnh mới nhằm sử dụng với CA tùy chỉnh:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
    --ignition-ca cert.pem \
    -o rhcos-<version>-custom-initramfs.x86_64.img
```

3. Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân (kernel arguments) `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn.

> **Quan trọng:** Tham số nhân `coreos.inst.ignition_url` không hoạt động cùng với cờ `--ignition-ca`. Bạn phải sử dụng cờ `--dest-ignition` để tạo một hình ảnh (image) tùy chỉnh cho mỗi cụm (cluster).

Việc áp dụng chứng chỉ CA tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động tiếp theo của RHCOS.

###### 2.1.14.3.17. Sửa đổi môi trường PXE cài đặt trực tiếp (live install) với các thiết lập mạng tùy chỉnh
Bạn có thể nhúng một tệp khóa (keyfile) của NetworkManager vào môi trường PXE cài đặt trực tiếp và chuyển nó sang hệ thống được cài đặt thông qua tham số `--network-keyfile` của lệnh con `customize`.

> **Cảnh báo:** Khi tạo hồ sơ kết nối (connection profile), bạn bắt buộc phải sử dụng phần mở rộng tệp là `.nmconnection`. Nếu không có phần mở rộng này, cụm (cluster) sẽ áp dụng hồ sơ kết nối vào môi trường cài đặt trực tiếp, nhưng cấu hình đó sẽ không được áp dụng khi cụm khởi động các nút (node) lần đầu tiên, dẫn đến hệ thống không hoạt động như mong đợi.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) của `coreos-installer`. 
2. Tạo một hồ sơ kết nối cho giao diện mạng được liên kết (bonded interface). Ví dụ: tạo tệp `bond0.nmconnection` trong thư mục cục bộ của bạn với nội dung như sau:

```
/[connection]
id=bond0
type=bond
interface-name=bond0
multi-connect=1

/[bond]
miimon=100
mode=active-backup

/[ipv4]
method=auto

/[ipv6]
method=auto
```

3. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em1.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em1
type=ethernet
interface-name=em1
master=bond0
multi-connect=1
slave-type=bond
```

4. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em2.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em2
type=ethernet
interface-name=em2
master=bond0
multi-connect=1
slave-type=bond
```

5. Tải các tệp kernel, initramfs và rootfs của RHCOS từ trang [lưu trữ (mirror) hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tạo một tệp initramfs tùy chỉnh mới, trong đó đã bao gồm các cấu hình mạng của bạn:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
    --network-keyfile bond0.nmconnection \
    --network-keyfile bond0-proxy-em1.nmconnection \
    --network-keyfile bond0-proxy-em2.nmconnection \
    -o rhcos-<version>-custom-initramfs.x86_64.img
```

6. Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân (kernel arguments) `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn.

Các thiết lập mạng sẽ được áp dụng cho hệ thống đang chạy (live system) và được chuyển sang hệ thống đích.

###### 2.1.14.3.18. Tùy chỉnh môi trường PXE cài đặt trực tiếp (live install) cho thiết bị khởi động iSCSI
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình bằng cách sử dụng một phiên bản tùy chỉnh của ảnh (image) RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy các tệp kernel, initramfs và rootfs của RHCOS từ [trang lưu trữ ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tạo một tệp initramfs tùy chỉnh mới với các thông tin sau:

```shell
$ coreos-installer pxe customize \
    --pre-install mount-iscsi.sh \
    --post-install unmount-iscsi.sh \
    --dest-device /dev/disk/by-path/<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
    --dest-ignition config.ign \
    --dest-karg-append rd.iscsi.initiator=<initiator_iqn> \
    --dest-karg-append netroot=<target_iqn> \
    -o custom.img rhcos-<version>-live-initramfs.x86_64.img
```

Trong đó:

* `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
* `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
* `<target_iqn>`: Xác định vị trí của hệ thống đích. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng tương ứng, nút iSCSI đích (target iSCSI node) ở định dạng IQN, và số đơn vị logic iSCSI (LUN).
* config.ign: Chỉ định cấu hình Ignition cho hệ thống đích.
* `<initiator_iqn>`: Xác định tên của iSCSI initiator (bên khởi tạo) hoặc máy khách (client) ở định dạng IQN. Initiator sẽ thiết lập một phiên kết nối tới iSCSI target.
* `<target_iqn>`: Xác định tên của iSCSI target (bên đích) hoặc máy chủ (server) ở định dạng IQN. 

Để biết thêm thông tin về các tùy chọn iSCSI được hỗ trợ bởi `dracut`, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.1.14.3.19. Tùy chỉnh môi trường PXE cài đặt trực tiếp (live install) cho thiết bị khởi động iSCSI sử dụng iBFT
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình hệ thống bằng cách sử dụng một phiên bản tùy chỉnh của ảnh (image) RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
* Tùy chọn: Bạn đã thiết lập đa đường dẫn (multipath) cho iSCSI target của mình.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) ảnh `coreos-installer`. 
2. Lấy các tệp kernel, initramfs và rootfs của RHCOS từ trang lưu trữ ảnh RHCOS, sau đó chạy lệnh dưới đây để tạo một tệp initramfs tùy chỉnh mới với các thông tin sau:

```shell
$ coreos-installer pxe customize \
    --pre-install mount-iscsi.sh \
    --post-install unmount-iscsi.sh \
    --dest-device /dev/mapper/mpatha \
    --dest-ignition config.ign \
    --dest-karg-append rd.iscsi.firmware=1 \
    --dest-karg-append rd.multipath=default \
    -o custom.img rhcos-<version>-live-initramfs.x86_64.img
```

Trong đó:

* `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target.
* `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
* `/dev/mapper/mpatha`: Chỉ định đường dẫn đến thiết bị. Nếu bạn đang sử dụng multipath (đa đường dẫn), thiết bị multipath sẽ là `/dev/mapper/mpatha`. Trong trường hợp có nhiều thiết bị multipath được kết nối, hoặc để xác định rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN) có sẵn trong thư mục `/dev/disk/by-path`.
* `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích.
* `rd.iscsi.firmware=1`: Chỉ định rằng tham số iSCSI sẽ được đọc từ firmware của BIOS.
* `rd.multipath=default`: Chỉ định việc có kích hoạt tính năng multipath hay không. Đây là tham số tùy chọn. 

Để biết thêm thông tin, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) của `dracut.cmdline`.

##### 2.1.14.4. Các tùy chọn về mạng và liên kết giao diện mạng (bonding) cho quá trình cài đặt qua tệp ISO
Bạn có thể định cấu hình các tùy chọn nâng cao để điều chỉnh quy trình cài đặt thủ công Red Hat Enterprise Linux CoreOS (RHCOS). Các phần tiếp theo sẽ trình bày các ví dụ về tùy chọn mạng cho quá trình cài đặt từ tệp ISO.

Nếu cài đặt RHCOS từ tệp ảnh ISO, bạn có thể thêm thủ công các tham số nhân (kernel arguments) trong quá trình khởi động tệp ảnh để định cấu hình mạng cho một nút (node). Nếu không có tham số mạng nào được chỉ định, DHCP sẽ tự động được kích hoạt trong initramfs khi RHCOS phát hiện thấy cần có kết nối mạng để truy xuất tệp cấu hình Ignition.
Quan trọng

Khi thêm thủ công các tham số mạng, bạn cũng phải thêm tham số nhân `rd.neednet=1` để khởi tạo mạng trong initramfs.

Thông tin dưới đây cung cấp các ví dụ về cách định cấu hình mạng và liên kết giao diện mạng (bonding) trên các nút RHCOS khi cài đặt qua tệp ISO. Các ví dụ này mô tả cách sử dụng các tham số nhân `ip=`, `nameserver=` và `bond=`.
Lưu ý

Thứ tự các tham số nhân khi thêm vào rất quan trọng: `ip=`, `nameserver=`, rồi mới đến `bond=`.

Các tùy chọn mạng này sẽ được truyền tới công cụ `dracut` trong quá trình khởi động hệ thống. Để biết thêm thông tin về các tùy chọn mạng được `dracut` hỗ trợ, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.1.14.4.1. Cấu hình DHCP hoặc địa chỉ IP tĩnh
Bạn có thể cấu hình địa chỉ IP bằng cách sử dụng DHCP hoặc thiết lập một địa chỉ IP tĩnh riêng biệt. Nếu chọn sử dụng địa chỉ IP tĩnh, bạn phải xác định địa chỉ IP của máy chủ DNS trên mỗi nút (node).

Các ví dụ cấu hình trong quy trình này cập nhật địa chỉ IP cho các thành phần sau:

* Địa chỉ IP của nút: 10.10.10.2
* Địa chỉ cổng mặc định (gateway): 10.10.10.254
* Mặt nạ mạng (netmask): 255.255.255.0
* Tên máy chủ (hostname): core0.example.com
* Địa chỉ máy chủ DNS: 4.4.4.41
* Giá trị tự động cấu hình (auto-configuration): none (không có). Không cần thực hiện tự động cấu hình khi mạng IP được thiết lập theo phương thức tĩnh.

**Quy trình**

1. Nhập một lệnh tương tự như lệnh dưới đây để cấu hình địa chỉ IP tĩnh:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
nameserver=4.4.4.41
```

2. Hãy nhập một lệnh như lệnh dưới đây để cấu hình địa chỉ IP DHCP:

```
ip=enp1s0:dhcp
```

> **Lưu ý:** Khi bạn sử dụng DHCP để cấu hình địa chỉ IP cho các máy RHCOS, các máy này cũng sẽ nhận thông tin máy chủ DNS thông qua DHCP. Đối với các triển khai dựa trên DHCP, bạn có thể xác định địa chỉ máy chủ DNS mà các nút RHCOS sẽ sử dụng thông qua cấu hình máy chủ DHCP của mình.

3. Nếu có từ hai giao diện mạng trở lên, hãy tắt DHCP trên các giao diện không cần thiết. Trong ví dụ này, giao diện enp1s0 được thiết lập cấu hình mạng tĩnh, còn DHCP đã bị tắt đối với giao diện enp2s0 (giao diện không được sử dụng):

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
ip=::::core0.example.com:enp2s0:none
```

4. Nếu bạn cần kết hợp các cấu hình DHCP và IP tĩnh trên các hệ thống có nhiều giao diện mạng, hãy chạy lệnh ví dụ sau đây:

```
ip=enp1s0:dhcp
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp2s0:none
```

###### 2.1.14.4.2. Cấu hình địa chỉ IP mà không cần tên máy chủ (hostname) tĩnh
Bạn có thể cấu hình địa chỉ IP mà không cần chỉ định tên máy chủ tĩnh. Nếu người dùng không thiết lập tên máy chủ tĩnh, hệ thống sẽ tự động xác định và thiết lập tên máy chủ này thông qua truy vấn DNS ngược (reverse DNS lookup).

Các ví dụ cấu hình trong quy trình này thực hiện cập nhật địa chỉ IP cho các thành phần sau:

* Địa chỉ IP của nút (node): 10.10.10.2
* Địa chỉ cổng mặc định (gateway): 10.10.10.254
* Mặt nạ mạng (netmask): 255.255.255.0
* Địa chỉ máy chủ DNS: 4.4.4.41
* Giá trị tự động cấu hình (auto-configuration): none (không có). Khi mạng IP được cấu hình theo cách tĩnh (static), không cần thực hiện tự động cấu hình.

**Quy trình**

* Để cấu hình địa chỉ IP mà không cần tên máy chủ tĩnh, hãy nhập một lệnh tương tự như lệnh sau:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0::enp1s0:none
nameserver=4.4.4.41
```

###### 2.1.14.4.3. Chỉ định nhiều giao diện mạng và máy chủ DNS
Bạn có thể chỉ định nhiều giao diện mạng bằng cách thiết lập nhiều mục `ip=`. Bạn có thể cung cấp nhiều máy chủ DNS bằng cách thêm một mục `nameserver=` cho mỗi máy chủ.

**Quy trình**

* Để chỉ định nhiều giao diện mạng, bạn có thể nhập một lệnh tương tự như lệnh sau:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
ip=10.10.10.3::10.10.10.254:255.255.255.0:core0.example.com:enp2s0:none
```

* Để cung cấp nhiều máy chủ DNS bằng cách thêm một mục `nameserver=` cho mỗi máy chủ, hãy nhập một lệnh như lệnh sau đây:

```
nameserver=1.1.1.1
nameserver=8.8.8.8
```

###### 2.1.14.4.4. Cấu hình cổng mặc định (default gateway) và tuyến đường (route)
Là một tác vụ tùy chọn, bạn có thể cấu hình các tuyến đường dẫn đến các mạng bổ sung bằng cách thiết lập giá trị `rd.route=`.

> **Lưu ý:** Khi cấu hình một hoặc nhiều mạng, bắt buộc phải có một cổng mặc định (default gateway). Nếu cổng của mạng bổ sung khác với cổng của mạng chính, thì cổng mặc định phải là cổng của mạng chính.

**Quy trình**

* Để cấu hình cổng mặc định, hãy nhập lệnh sau:

```
ip=::10.10.10.254::::
```

* Để cấu hình tuyến đường cho một mạng bổ sung, hãy nhập lệnh sau:

```
rd.route=20.20.20.0/24:20.20.20.254:enp2s0
```

###### 2.1.14.4.5. Cấu hình VLAN trên các giao diện riêng lẻ
Là một tác vụ tùy chọn, bạn có thể cấu hình VLAN trên các giao diện riêng lẻ bằng cách sử dụng tham số `vlan=`.

**Quy trình**

* Để cấu hình VLAN trên một giao diện mạng và sử dụng địa chỉ IP tĩnh, hãy chạy lệnh sau:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp2s0.100:none
vlan=enp2s0.100:enp2s0
```

* Để cấu hình VLAN trên một giao diện mạng và sử dụng DHCP, hãy chạy lệnh sau:

```
ip=enp2s0.100:dhcp
vlan=enp2s0.100:enp2s0
```

###### 2.1.14.4.6. Gộp nhiều giao diện mạng thành một giao diện duy nhất
Là một tác vụ tùy chọn, bạn có thể gộp nhiều giao diện mạng thành một giao diện duy nhất bằng cách sử dụng tùy chọn `bond=`.

Ví dụ dưới đây minh họa việc chỉnh sửa tệp `/etc/config/network` và chỉ định cú pháp sau để gộp nhiều giao diện mạng thành một giao diện duy nhất:

```
bond=<name>[:<network_interfaces>][:<options>]
```

* `<name>`: Chỉ định tên của thiết bị liên kết (bonding device), ví dụ: bond0. 
* `<network_interfaces>`: Chỉ định danh sách các giao diện vật lý (ethernet) được phân tách bằng dấu phẩy, chẳng hạn như em1, em2. 
* `<options>`: Chỉ định danh sách các tùy chọn liên kết (bonding options) được phân tách bằng dấu phẩy. Hãy chạy lệnh `modinfo bonding` để xem các tùy chọn khả dụng.

Khi tạo một giao diện liên kết (bonded interface) bằng lệnh `bond=`, bạn phải chỉ định cách thức gán địa chỉ IP cùng các thông tin khác cho giao diện đó.

**Quy trình**

* Để cấu hình giao diện được liên kết (bonded interface) sử dụng DHCP, hãy chỉnh sửa tệp `/etc/config/network` bằng cách thiết lập địa chỉ IP cho liên kết đó thành `dhcp`. Ví dụ:

```
ip=bond0:dhcp
```

* Để cấu hình giao diện được liên kết (bonded interface) sử dụng địa chỉ IP tĩnh, hãy chỉnh sửa tệp /etc/config/network rồi nhập địa chỉ IP cụ thể mà bạn muốn cùng các thông tin liên quan. Ví dụ:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:bond0:none
```

###### 2.1.14.4.7. Gộp (bond) nhiều giao diện mạng SR-IOV vào một giao diện NIC hai cổng
Bạn có thể gộp nhiều giao diện mạng SR-IOV vào một giao diện NIC hai cổng bằng cách sử dụng tùy chọn `bond=`. Hãy đảm bảo thực hiện các bước quy trình trên từng nút (node).

**Quy trình**

1. Tạo các hàm ảo (Virtual Function - VF) SR-IOV theo hướng dẫn trong phần Quản lý thiết bị SR-IOV. Thực hiện theo quy trình trong mục "Gắn các thiết bị mạng SR-IOV vào máy ảo". 
2. Tạo liên kết (bond), gắn các VF mong muốn vào liên kết đó, rồi thiết lập trạng thái kết nối (link state) cho liên kết theo hướng dẫn trong phần Cấu hình gộp mạng (network bonding). Bạn có thể chọn bất kỳ quy trình nào được mô tả để thực hiện việc tạo liên kết này.

Các ví dụ sau đây minh họa cú pháp mà bạn phải sử dụng:
  * Cú pháp để cấu hình một giao diện liên kết (bonded interface) là `bond=<name>[:<network_interfaces>][:options]`. Trong đó, `<name>` là tên của thiết bị liên kết (ví dụ: `bond0`); `<network_interfaces>` đại diện cho các hàm ảo (Virtual Functions - VF) thông qua tên gọi của chúng trong nhân hệ thống (kernel) — tên này cũng xuất hiện trong kết quả của lệnh `ip link` (ví dụ: `eno1f0`, `eno2f0`); còn options là danh sách các tùy chọn liên kết được phân tách bằng dấu phẩy. Bạn có thể chạy lệnh modinfo bonding để xem các tùy chọn khả dụng.
  * Khi tạo một giao diện liên kết (bonded interface) bằng cách sử dụng tham số `bond=`, bạn phải chỉ rõ cách thức gán địa chỉ IP cũng như các thông tin khác cho giao diện đó.
    * Để cấu hình giao diện được liên kết (bonded interface) sử dụng DHCP, hãy đặt địa chỉ IP của liên kết đó thành dhcp. Ví dụ:

    ```
    bond=bond0:eno1f0,eno2f0:mode=active-backup
    ip=bond0:dhcp::AA:BB:CC:DD:EE:FF
    ip=eno1f0:none::AA:BB:CC:DD:EE:FF
    ip=eno2f0:none::AA:BB:CC:DD:EE:FF
    ```

    * Để cấu hình giao diện được liên kết (bonded interface) sử dụng địa chỉ IP tĩnh, hãy nhập địa chỉ IP cụ thể mà bạn muốn cùng các thông tin liên quan. Ví dụ:

    ```
    bond=bond0:eno1f0,eno2f0:mode=active-backup
    ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:bond0:none
    ```

3. Tùy chọn: Bạn có thể sử dụng tính năng network teaming thay cho bonding bằng cách dùng tham số `team=`.
  * Cú pháp để cấu hình giao diện nhóm (team interface) là: `team=name[:network_interfaces]`. Trong đó, name là tên của thiết bị nhóm (ví dụ: `team0`), còn `network_interfaces` là danh sách các giao diện vật lý (ethernet) được phân tách bằng dấu phẩy (ví dụ: `em1`, `em2`).

  Sử dụng ví dụ sau để cấu hình một nhóm mạng (network team):

  ```
  team=team0:em1,em2
  ip=team0:dhcp
  ```

###### 2.1.14.4.8. coreos-installer và các tùy chọn khởi động cho quá trình cài đặt qua ISO và PXE
Bạn có thể cài đặt RHCOS bằng cách chạy lệnh `coreos-installer install <các tùy chọn> <thiết bị>` tại dấu nhắc lệnh, sau khi khởi động vào môi trường chạy trực tiếp (live environment) của RHCOS từ một tệp ảnh ISO.

Bảng dưới đây liệt kê các lệnh con, tùy chọn và đối số mà bạn có thể truyền vào lệnh `coreos-installer`.

**Bảng 2.8. Các lệnh con, tùy chọn dòng lệnh và đối số của `coreos-installer`**

* Lệnh con install của coreos-installer

| Lệnh con                                        | Mô tả                                        |
|-------------------------------------------------|----------------------------------------------|
| `$ coreos-installer install <options> <device>` | Nhúng cấu hình Ignition vào một tệp ảnh ISO. |


* Các tùy chọn cho lệnh con install của coreos-installer

| Tùy chọn                       | Mô tả                                                                                                                                                                                                                                                   |
|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-u`, `--image-url <url>`      | Nhập thủ công URL của hình ảnh.                                                                                                                                                                                                                         |
| `-f`, `--image-file <path>`    | Chỉ định thủ công một tệp hình ảnh cục bộ. Được sử dụng cho mục đích gỡ lỗi.                                                                                                                                                                            |
| `-i`, `--ignition-file <path>` | Nhúng cấu hình Ignition từ một tệp tin.                                                                                                                                                                                                                 |
| `-I`, `--ignition-url <URL>`   | Nhúng cấu hình Ignition từ một URL.                                                                                                                                                                                                                     |
| `--ignition-hash <digest>`     | Digest `type-value` của cấu hình Ignition.                                                                                                                                                                                                              |
| `-p`, `--platform <name>`      | Ghi đè ID nền tảng Ignition cho hệ thống đã cài đặt.                                                                                                                                                                                                    |
| `--console <spec>`             | Thiết lập bảng điều khiển (console) cho nhân hệ điều hành (kernel) và bộ nạp khởi động (boot loader) của hệ thống đã cài đặt. Để biết thêm thông tin về định dạng của `<spec>`                                                                          |
| `--append-karg <arg>…`         | Thêm một tham số nhân (kernel argument) mặc định vào hệ thống đã cài đặt.                                                                                                                                                                               |
| `--delete-karg <arg>…`         | Xóa một tham số nhân mặc định khỏi hệ thống đã cài đặt.                                                                                                                                                                                                 |
| `-n`, `--copy-network`         | Sao chép cấu hình mạng từ môi trường cài đặt. **Quan trọng**: Tùy chọn `--copy-network` chỉ sao chép cấu hình mạng nằm trong thư mục `/etc/NetworkManager/system-connections`. Cụ thể, tùy chọn này không sao chép tên máy chủ (hostname) của hệ thống. |
| `--network-dir <path>`         | Dùng với tùy chọn `-n`. Giá trị mặc định là `/etc/NetworkManager/system-connections/`.                                                                                                                                                                  |
| `--save-partlabel <lx>..`      | Lưu các phân vùng khớp với mẫu nhãn này.                                                                                                                                                                                                                |
| `--save-partindex <id>…`       | Lưu các phân vùng có số hoặc phạm vi này.                                                                                                                                                                                                               |
| `--insecure`                   | Bỏ qua việc xác minh chữ ký ảnh RHCOS.                                                                                                                                                                                                                  |
| `--insecure-ignition`          | Cho phép URL Ignition không có HTTPS hoặc dấu thăng (#).                                                                                                                                                                                                |
|  `--architecture <name>`       | Kiến trúc CPU đích. Các giá trị hợp lệ là `x86_64` và `aarch64`.                                                                                                                                                                                        |
| `--preserve-on-error`          | Không xóa bảng phân vùng khi xảy ra lỗi.                                                                                                                                                                                                                |
| `-h`, `--help`                 | In thông tin trợ giúp.                                                                                                                                                                                                                                  |

* Đối số của lệnh con install trong `coreos-installer`

| Đối số     | Mô tả          |
|------------|----------------|
| `<device>` | Thiết bị đích. |


* Các lệnh con ISO của `coreos-installer`

| Lệnh con                                                     | Mô tả                                                                                 |
|--------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `coreos-installer iso customize <options> <ISO_image>`       | Tùy chỉnh một tệp ISO live của RHCOS.                                                 |
| `coreos-installer iso reset <options> <ISO_image>`           | Khôi phục tệp ảnh ISO trực tiếp (live ISO image) của RHCOS về các thiết lập mặc định. |
| `coreos-installer iso ignition remove <options> <ISO_image>` | Xóa cấu hình Ignition được nhúng khỏi tệp ảnh ISO.                                    |

* Các tùy chọn của lệnh con tùy chỉnh ISO trong `coreos-installer`

| Tùy chọn                      | Mô tả                                                                                                                                                           |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--dest-ignition <path>`      | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho hệ thống đích.                                                                  |
| `--dest-console <spec>`       | Hãy chỉ định nhân hệ điều hành và bảng điều khiển bộ nạp khởi động cho hệ thống đích.                                                                           |
| `--dest-device <path>`        | Cài đặt và ghi đè lên thiết bị đích đã chỉ định.                                                                                                                |
| `--dest-karg-append <arg>`    | Thêm một tham số nhân (kernel argument) vào mỗi lần khởi động của hệ thống đích.                                                                                |
| `--dest-karg-delete <arg>`    | Xóa một tham số nhân (kernel argument) khỏi mỗi lần khởi động của hệ thống đích.                                                                                |
| `--network-keyfile <path>`    | Cấu hình mạng bằng cách sử dụng tệp khóa (keyfile) NetworkManager được chỉ định cho các hệ thống trực tiếp (live system) và hệ thống đích (destination system). |
| `--ignition-ca <path>`        | Chỉ định một cơ quan cấp chứng chỉ TLS bổ sung để Ignition tin tưởng.                                                                                           |
| `--pre-install <path>`        | Chạy tập lệnh được chỉ định trước khi cài đặt.                                                                                                                  |
| `--post-install <path>`       | Chạy tập lệnh được chỉ định sau khi cài đặt.                                                                                                                    |
| `--installer-config <path>`   | Áp dụng tệp cấu hình trình cài đặt đã chỉ định.                                                                                                                 |
| `--live-ignition <path>`      | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho môi trường thực tế (live environment).                                          |
| `--live-karg-append <arg>`    | Thêm một tham số nhân (kernel argument) vào mỗi lần khởi động môi trường chạy trực tiếp (live environment).                                                     |
| `--live-karg-delete <arg>`    | Xóa một tham số nhân (kernel argument) khỏi mỗi lần khởi động môi trường chạy trực tiếp (live environment).                                                     |
| `--live-karg-replace <k=o=n>` | Thay thế một tham số nhân (kernel argument) trong mỗi lần khởi động môi trường chạy trực tiếp (live environment), theo định dạng `key=old=new`.                 |
| `-f`, `--force`               | Ghi đè lên cấu hình Ignition hiện có.                                                                                                                           |
| `-o`, `--output <path>`       | Ghi tệp ISO vào một tệp đầu ra mới.                                                                                                                             |
| `-h`, `--help`                | In thông tin trợ giúp.                                                                                                                                          |

* Các lệnh con PXE của coreos-installer

| Lệnh con                                                      | Mô tả                                                                 |
|---------------------------------------------------------------|-----------------------------------------------------------------------|
| `coreos-installer pxe customize <options> <path>`             | Tùy chỉnh cấu hình khởi động PXE trực tiếp (live PXE boot) cho RHCOS. |
| `coreos-installer pxe ignition wrap <options>`                | Đóng gói cấu hình Ignition vào một ảnh (image).                       |
| `coreos-installer pxe ignition unwrap <options> <image_name>` | Hiển thị cấu hình Ignition đã được bao gói trong một hình ảnh.        |

Lưu ý rằng không phải tất cả các tùy chọn này đều được chấp nhận bởi mọi lệnh con.

* Các tùy chọn cho lệnh con tùy chỉnh (customize) PXE trong `coreos-installer`

| Tùy chọn                    | Mô tả                                                                                                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--dest-ignition <path>`    | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho hệ thống đích.                                                                  |
| `--dest-console <spec>`     | Hãy chỉ định nhân hệ điều hành và bảng điều khiển bộ nạp khởi động cho hệ thống đích.                                                                           |
| `--dest-device <path>`      | Cài đặt và ghi đè lên thiết bị đích đã chỉ định.                                                                                                                |
| `--network-keyfile <path>`  | Cấu hình mạng bằng cách sử dụng tệp khóa (keyfile) NetworkManager được chỉ định cho các hệ thống trực tiếp (live system) và hệ thống đích (destination system). |
| `--ignition-ca <path>`      | Chỉ định một cơ quan cấp chứng chỉ TLS bổ sung để Ignition tin tưởng.                                                                                           |
| `--pre-install <path>`      | Chạy tập lệnh được chỉ định trước khi cài đặt.                                                                                                                  |
| `post-install <path>`       | Chạy tập lệnh được chỉ định sau khi cài đặt.                                                                                                                    |
| `--installer-config <path>` | Áp dụng tệp cấu hình trình cài đặt đã chỉ định.                                                                                                                 |
| `--live-ignition <path>`    | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho môi trường thực tế (live environment).                                          |
| `-o`, `--output <path>`     | Ghi initramfs vào một tệp đầu ra mới. **Lưu ý**: Tùy chọn này là bắt buộc đối với các môi trường PXE.                                                           |
| `-h`, `--help`              | In thông tin trợ giúp.                                                                                                                                          |

Lưu ý rằng không phải tất cả các tùy chọn này đều được chấp nhận bởi mọi lệnh con.

Bạn có thể tự động áp dụng các tùy chọn của `coreos-installer` ngay khi khởi động bằng cách truyền các tham số khởi động `coreos.inst` vào trình cài đặt trực tiếp (live installer) của RHCOS. Các tham số này được bổ sung bên cạnh những tham số khởi động tiêu chuẩn. 

* Đối với các bản cài đặt từ tệp ISO, bạn có thể thêm các tùy chọn `coreos.inst` bằng cách ngắt quá trình khởi động tự động tại menu của bộ nạp khởi động (boot loader). Bạn có thể thực hiện việc này bằng cách nhấn phím TAB khi tùy chọn menu "RHEL CoreOS (Live)" đang được chọn. 
* Đối với các bản cài đặt qua PXE hoặc iPXE, các tùy chọn `coreos.inst` cần được thêm vào dòng `APPEND` trước khi trình cài đặt trực tiếp RHCOS khởi chạy.

Bảng dưới đây liệt kê các tùy chọn khởi động `coreos.inst` dành cho trình cài đặt trực tiếp RHCOS trong các trường hợp cài đặt từ tệp ISO hoặc qua PXE.

**Bảng 2.9. Các tùy chọn khởi động coreos.inst**

| Đối số                       | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `coreos.inst.install_dev`    | Bắt buộc. Thiết bị khối trên hệ thống dùng để cài đặt. Lưu ý: Bạn nên sử dụng đường dẫn đầy đủ, chẳng hạn như `/dev/sda`, mặc dù chỉ dùng `sda` cũng được chấp nhận.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `coreos.inst.ignition_url`   | Tùy chọn: URL của cấu hình Ignition cần nhúng vào hệ thống được cài đặt. Nếu không chỉ định URL, sẽ không có cấu hình Ignition nào được nhúng vào. Chỉ hỗ trợ các giao thức HTTP và HTTPS.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `coreos.inst.save_partlabel` | Tùy chọn: Danh sách các phân vùng cần giữ lại trong quá trình cài đặt, được phân tách bằng dấu phẩy. Có thể sử dụng các ký tự đại diện (wildcard) kiểu glob. Các phân vùng được chỉ định không nhất thiết phải tồn tại từ trước.                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `coreos.inst.save_partindex` | Tùy chọn: Các chỉ số phân vùng cần giữ lại trong quá trình cài đặt, được phân tách bằng dấu phẩy. Có thể sử dụng các dải giá trị dạng `m-n` (trong đó `m` hoặc `n` có thể được lược bỏ). Các phân vùng được chỉ định không nhất thiết phải tồn tại từ trước.                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `coreos.inst.insecure`       | Tùy chọn: Cho phép hình ảnh hệ điều hành (OS image) được chỉ định bởi coreos.inst.image_url không cần phải có chữ ký.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `coreos.inst.image_url`      | Tùy chọn: Tải xuống và cài đặt hình ảnh (image) RHCOS được chỉ định. Tham số này không nên được sử dụng trong các môi trường thực tế (production) mà chỉ dành cho mục đích gỡ lỗi (debugging). Mặc dù tham số này có thể được dùng để cài đặt một phiên bản RHCOS không khớp với phương tiện cài đặt (live media) hiện có, bạn vẫn nên sử dụng phương tiện cài đặt tương ứng với phiên bản mà bạn muốn cài đặt. Nếu sử dụng `coreos.inst.image_url`, bạn bắt buộc phải dùng kèm với `coreos.inst.insecure`. Lý do là vì các phương tiện cài đặt trên phần cứng vật lý (bare-metal) không được ký bằng GPG cho OpenShift Container Platform. Chỉ hỗ trợ các giao thức HTTP và HTTPS. |
| `coreos.inst.skip_reboot`    | Tùy chọn: Hệ thống sẽ không khởi động lại sau khi cài đặt. Khi quá trình cài đặt hoàn tất, bạn sẽ nhận được thông báo cho phép kiểm tra các diễn biến trong suốt quá trình cài đặt. Tham số này không nên được sử dụng trong môi trường thực tế (production) mà chỉ dành cho mục đích gỡ lỗi.                                                                                                                                                                                                                                                                                                                                                                                       |
| `coreos.inst.platform_id`    | Tùy chọn: ID nền tảng Ignition của nền tảng mà ảnh RHCOS đang được cài đặt. Mặc định là `metal`. Tùy chọn này xác định xem có yêu cầu cấu hình Ignition từ nhà cung cấp dịch vụ đám mây, chẳng hạn như VMware hay không. Ví dụ: `coreos.inst.platform_id=vmware`.                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `ignition.config.url`        | Tùy chọn: URL của cấu hình Ignition dùng cho quá trình khởi động trực tiếp (live boot). Ví dụ, thông số này có thể được sử dụng để tùy chỉnh cách thức gọi `coreos-installer`, hoặc để thực thi mã lệnh trước hay sau quá trình cài đặt. Thông số này khác với `coreos.inst.ignition_url` — vốn là cấu hình Ignition dành cho hệ thống sau khi đã được cài đặt.                                                                                                                                                                                                                                                                                                                     |

##### 2.1.14.5. Kích hoạt multipathing (đa đường dẫn) bằng các tham số kernel trên RHCOS
RHCOS hỗ trợ multipathing trên đĩa chính, giúp tăng cường khả năng chống chịu trước các lỗi phần cứng, từ đó nâng cao tính sẵn sàng của host (máy chủ).

Bạn có thể kích hoạt multipathing ngay tại thời điểm cài đặt đối với các node được cung cấp (provision) trong OpenShift Container Platform phiên bản 4.8 trở lên. Mặc dù vẫn có thể kích hoạt multipathing sau khi cài đặt thông qua cấu hình máy (machine config), Red Hat khuyến nghị nên thực hiện việc này ngay trong quá trình cài đặt.

Trong các thiết lập mà mọi thao tác I/O (nhập/xuất dữ liệu) trên các đường dẫn không được tối ưu hóa (non-optimized paths) đều dẫn đến lỗi hệ thống I/O, bạn bắt buộc phải kích hoạt multipathing ngay từ lúc cài đặt.

> **Quan trọng:** Trên các hệ thống IBM Z® và IBM® LinuxONE, bạn chỉ có thể kích hoạt multipathing nếu đã cấu hình cho cụm (cluster) ngay trong quá trình cài đặt. Để biết thêm thông tin, hãy xem phần "Cài đặt RHCOS và bắt đầu quy trình bootstrap của OpenShift Container Platform" trong tài liệu hướng dẫn cài đặt cụm trên z/VM dành cho IBM Z® và IBM® LinuxONE.

Quy trình dưới đây hướng dẫn cách kích hoạt multipath tại thời điểm cài đặt bằng cách thêm các tham số kernel vào lệnh `coreos-installer install`, đảm bảo hệ thống sau khi cài đặt sẽ sử dụng multipath ngay từ lần khởi động đầu tiên.

> **Lưu ý:** OpenShift Container Platform không hỗ trợ kích hoạt multipathing như một thao tác vận hành sau cài đặt (day-2 activity) đối với các node đã được nâng cấp từ phiên bản 4.6 trở về trước.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã xem qua nội dung về việc cài đặt RHCOS và khởi động quy trình bootstrap của OpenShift Container Platform.

**Quy trình**

1. Để kích hoạt multipath và khởi chạy tiến trình (daemon) `multipathd`, hãy thực thi lệnh sau trên host cài đặt:

```shell
$ mpathconf --enable && systemctl start multipathd.service
```

  * Tùy chọn: Nếu khởi động qua PXE hoặc ISO, bạn có thể kích hoạt tính năng đa đường dẫn (multipath) bằng cách thêm tham số `rd.multipath=default` vào dòng lệnh kernel.

2. Thêm các tham số kernel bằng cách gọi chương trình coreos-installer:

  * Nếu chỉ có một thiết bị đa đường dẫn (multipath device) được kết nối với máy, thiết bị đó sẽ khả dụng tại đường dẫn /dev/mapper/mpatha. Ví dụ:

  ```shell
  $ coreos-installer install /dev/mapper/mpatha \
    --ignition-url=http://host/worker.ign \
    --append-karg rd.multipath=default \
    --append-karg root=/dev/disk/by-label/dm-mpath-root \
    --append-karg rw
  ```

    * `/dev/mapper/mpatha`: Cho biết đường dẫn của thiết bị đa đường dẫn (multipathed device) duy nhất.

  * Nếu có nhiều thiết bị đa đường dẫn (multipath) được kết nối với máy, thay vì sử dụng `/dev/mapper/mpatha`, Red Hat khuyến nghị sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN). Liên kết tượng trưng này có sẵn tại `/dev/disk/by-id`. Ví dụ:

  ```shell
  $ coreos-installer install /dev/disk/by-id/wwn-<wwn_ID> \
    --ignition-url=http://host/worker.ign \
    --append-karg rd.multipath=default \
    --append-karg root=/dev/disk/by-label/dm-mpath-root \
    --append-karg rw
  ```

  Trong đó: `<wwn_ID>` cho biết WWN ID của thiết bị đa đường dẫn (multipathed device) đích. Ví dụ: 0xx194e957fcedb4841. 

  Liên kết tượng trưng (symlink) này cũng có thể được sử dụng làm tham số nhân (kernel argument) `coreos.inst.install_dev` khi dùng các tham số `coreos.inst.*` đặc biệt để điều hướng trình cài đặt trực tiếp (live installer). Để biết thêm thông tin, hãy xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) của OpenShift Container Platform".

3. Khởi động lại vào hệ thống đã cài đặt.
4. Kiểm tra xem các tham số nhân (kernel arguments) đã có hiệu lực hay chưa bằng cách truy cập vào một trong các nút worker và liệt kê các tham số dòng lệnh của nhân (nằm trong tệp /proc/cmdline trên máy chủ):

```shell
$ oc debug node/ip-10-0-141-105.ec2.internal
```

Ví dụ đầu ra

```shell
Starting pod/ip-10-0-141-105ec2internal-debug ...
To use host binaries, run `chroot /host`

sh-4.2# cat /host/proc/cmdline
...
rd.multipath=default root=/dev/disk/by-label/dm-mpath-root
...

sh-4.2# exit
```

Bạn sẽ thấy các tham số nhân (kernel arguments) đã được thêm vào.

###### 2.1.14.5.1. Kích hoạt tính năng đa đường dẫn (multipathing) trên các ổ đĩa phụ
RHCOS cũng hỗ trợ tính năng đa đường dẫn (multipathing) trên các ổ đĩa phụ. Thay vì sử dụng các tham số nhân (kernel arguments), bạn có thể dùng Ignition để kích hoạt tính năng này cho ổ đĩa phụ ngay tại thời điểm cài đặt.

**Điều kiện tiên quyết**

* Bạn đã đọc phần Phân vùng ổ đĩa (Disk partitioning). 
* Bạn đã đọc phần Kích hoạt tính năng đa đường dẫn bằng các tham số nhân trên RHCOS. 
* Bạn đã cài đặt tiện ích Butane.

**Quy trình**

1. Tạo một tệp cấu hình Butane với thông tin tương tự như sau:

Ví dụ về `multipath-config.bu`

```yaml
variant: openshift
version: 4.22.0
systemd:
  units:
    - name: mpath-configure.service
      enabled: true
      contents: |
        [Unit]
        Description=Configure Multipath on Secondary Disk
        ConditionFirstBoot=true
        ConditionPathExists=!/etc/multipath.conf
        Before=multipathd.service
        DefaultDependencies=no

        [Service]
        Type=oneshot
        ExecStart=/usr/sbin/mpathconf --enable

        [Install]
        WantedBy=multi-user.target
    - name: mpath-var-lib-container.service
      enabled: true
      contents: |
        [Unit]
        Description=Set Up Multipath On /var/lib/containers
        ConditionFirstBoot=true
        Requires=dev-mapper-mpatha.device
        After=dev-mapper-mpatha.device
        After=ostree-remount.service
        Before=kubelet.service
        DefaultDependencies=no

        [Service]
        Type=oneshot
        ExecStart=/usr/sbin/mkfs.xfs -L containers -m reflink=1 /dev/mapper/mpatha
        ExecStart=/usr/bin/mkdir -p /var/lib/containers

        [Install]
        WantedBy=multi-user.target
    - name: var-lib-containers.mount
      enabled: true
      contents: |
        [Unit]
        Description=Mount /var/lib/containers
        After=mpath-var-lib-containers.service
        Before=kubelet.service

        [Mount]
        What=/dev/disk/by-label/dm-mpath-containers
        Where=/var/lib/containers
        Type=xfs

        [Install]
        WantedBy=multi-user.target
```

Trong đó:

  * `Before=multipathd.service`: Quy định rằng cấu hình phải được thiết lập trước khi khởi chạy tiến trình nền (daemon) multipath.
  * `ExecStart=/usr/sbin/mpathconf`: Chỉ định việc khởi chạy tiện ích mpathconf.
  * `ConditionFirstBoot=true`: Được đặt thành giá trị true.
  * `[Service]`: Quy định việc tạo hệ thống tệp và thư mục `/var/lib/containers`.
  * `Before=kubelet.service`: Quy định rằng thiết bị phải được gắn (mount) trước khi khởi động bất kỳ nút (node) nào.
  * `[Mount]`: Chỉ định việc gắn thiết bị vào điểm gắn `/var/lib/containers`. Vị trí này không được phép là một liên kết tượng trưng (symlink).

2. Tạo cấu hình Ignition bằng cách chạy lệnh sau:

```shell
$ butane --pretty --strict multipath-config.bu > multipath-config.ign
```

3. Tiếp tục thực hiện các bước còn lại trong quy trình cài đặt RHCOS ở lần khởi động đầu tiên.

> **Quan trọng:** Không thêm các tham số nhân (kernel arguments) `rd.multipath` hoặc `root` vào dòng lệnh (CLI) trong quá trình cài đặt, trừ khi ổ đĩa chính cũng được cấu hình đa đường dẫn (multipath).

##### 2.1.14.6. Cài đặt thủ công RHCOS trên thiết bị khởi động iSCSI
Bạn có thể cài đặt thủ công RHCOS trên một đích iSCSI (iSCSI target).

**Điều kiện tiên quyết**

* Bạn đang ở trong môi trường chạy trực tiếp (live environment) của RHCOS. 
* Bạn có một đích iSCSI mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Gắn (mount) đích iSCSI từ môi trường chạy trực tiếp bằng cách thực thi lệnh sau:

```shell
$ iscsiadm \
  --mode discovery \
  --type sendtargets \
  --portal <IP_address> \
  --login
```

Trong đó: `<IP_address>` chỉ định địa chỉ IP của cổng đích (target portal)

2. Cài đặt RHCOS vào đích iSCSI bằng cách chạy lệnh sau và sử dụng các tham số nhân (kernel arguments) cần thiết, chẳng hạn như:

```shell
$ coreos-installer install \
  /dev/disk/by-path/ip-<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
  --append-karg rd.iscsi.initiator=<initiator_iqn> \
  --append.karg netroot=<target_iqn> \
  --console ttyS0,115200n8 \
  --ignition-file <path_to_file>
```

Trong đó:

  * `/dev/disk/by-path/ip`: Xác định vị trí cài đặt. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng (port number) tương ứng, nút iSCSI đích (target iSCSI node) ở định dạng IQN, và số đơn vị logic iSCSI (LUN).
  * `<initiator_iqn>`: Xác định tên của iSCSI initiator (bên khởi tạo) – hay còn gọi là client – ở định dạng IQN. Initiator này sẽ thiết lập một phiên kết nối tới iSCSI target (đích đến).
  * `<target_iqn>`: Xác định tên của iSCSI target (đích đến) – hay còn gọi là server – ở định dạng IQN.

3. Ngắt kết nối đĩa iSCSI bằng lệnh sau:

```shell
$ iscsiadm --mode node --logoutall=all
```

Quy trình này cũng có thể được thực hiện bằng cách sử dụng các lệnh phụ `coreos-installer iso customize` hoặc `coreos-installer pxe customize`.

##### 2.1.14.7. Cài đặt RHCOS trên thiết bị khởi động iSCSI sử dụng iBFT
Trên một máy hoàn toàn không có ổ đĩa cục bộ, các giá trị iSCSI target (đích) và initiator (bên khởi tạo) có thể được truyền qua iBFT. Tính năng đa đường dẫn (multipathing) cho iSCSI cũng được hỗ trợ.

**Các điều kiện tiên quyết**

1. Bạn đang ở trong môi trường live của RHCOS. 
2. Bạn có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
3. Tùy chọn: Bạn đã cấu hình tính năng đa đường dẫn (multipathing) cho iSCSI target của mình.

**Quy trình**

1. Gắn (mount) iSCSI target từ môi trường live bằng cách chạy lệnh sau:

```shell
$ iscsiadm \
  --mode discovery \
  --type sendtargets \
  --portal <IP_address> \
  --login
```

Trong đó: `<IP_address>` chỉ định địa chỉ IP của cổng đích (target portal).

2. Tùy chọn: bật tính năng đa đường dẫn (multipathing) và khởi chạy tiến trình nền (daemon) bằng lệnh sau:

```shell
$ mpathconf --enable && systemctl start multipathd.service
```

3. Cài đặt RHCOS vào đích iSCSI bằng cách chạy lệnh sau và sử dụng các tham số nhân (kernel arguments) cần thiết, chẳng hạn như:

```shell
$ coreos-installer install \
  /dev/mapper/mpatha \
  --append-karg rd.iscsi.firmware=1 \
  --append-karg rd.multipath=default \
  --console ttyS0 \
  --ignition-file <path_to_file>
```

Trong đó:

  * `/dev/mapper/mpatha`: Chỉ định đường dẫn của một thiết bị đa đường dẫn (multipathed device). Nếu có nhiều thiết bị đa đường dẫn được kết nối, hoặc để xác định rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN) có sẵn trong thư mục `/dev/disk/by-path`.
  * `rd.iscsi.firmware=1`: Chỉ định rằng tham số iSCSI sẽ được đọc từ firmware của BIOS.
  * `rd.multipath=default`: Chỉ định kích hoạt tính năng đa đường dẫn (multipathing). Đây là một tham số tùy chọn. 

Để biết thêm thông tin về các tùy chọn iSCSI được dracut hỗ trợ, hãy tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

4. Ngắt kết nối đĩa iSCSI:

```shell
$ iscsiadm --mode node --logout=all
```

Bạn cũng có thể thực hiện quy trình này bằng cách sử dụng các lệnh phụ `coreos-installer iso customize` hoặc `coreos-installer pxe customize`.

#### 2.1.15. Chờ quá trình khởi động ban đầu (bootstrap) hoàn tất
Sau khi các nút (node) trong cụm (cluster) của bạn khởi động lần đầu vào môi trường RHCOS cố định đã được cài đặt trên ổ đĩa, hãy chờ quá trình khởi động ban đầu (bootstrap) của OpenShift Container Platform hoàn tất.

Thông tin cấu hình được cung cấp thông qua các tệp cấu hình Ignition sẽ được sử dụng để khởi tạo quy trình bootstrap và cài đặt OpenShift Container Platform trên các máy chủ.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn đã tải xuống chương trình cài đặt và tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã cài đặt RHCOS trên các máy chủ trong cụm và cung cấp các tệp cấu hình Ignition do chương trình cài đặt OpenShift Container Platform tạo ra. 
* Các máy chủ của bạn có quyền truy cập trực tiếp vào internet hoặc có sẵn proxy HTTP hoặc HTTPS.

**Quy trình**

1. Theo dõi quá trình khởi động ban đầu (bootstrap):

```shell
$ ./openshift-install --dir <installation_directory> wait-for bootstrap-complete \
  --log-level=info
```

Trong đó:

  * `<installation_directory`>: Chỉ định đường dẫn đến thư mục lưu trữ các tệp cài đặt.
  * `--log-level=info`: Có thể thay thế `info` bằng `warn`, `debug` hoặc `error` để xem các chi tiết cài đặt khác nhau.

Ví dụ đầu ra

```
INFO Waiting up to 20m0s for the Kubernetes API at https://api.test.example.com:6443...
INFO API v1.35.4 up
INFO Waiting up to 1h0m0s for bootstrapping to complete...
INFO It is now safe to remove the bootstrap resources
```

Thời gian chờ hoàn tất quá trình khởi động ban đầu (bootstrapping) khác nhau tùy theo từng nền tảng.

Lệnh này sẽ thực thi thành công khi Kubernetes API server phát tín hiệu xác nhận rằng quá trình khởi động ban đầu đã hoàn tất trên các máy thuộc mặt phẳng điều khiển (control plane).

2. Sau khi quá trình khởi động ban đầu hoàn tất, hãy loại bỏ máy khởi động (bootstrap machine) khỏi bộ cân bằng tải (load balancer).

> **Quan trọng:** Tại thời điểm này, bạn bắt buộc phải loại bỏ máy khởi động khỏi bộ cân bằng tải. Bạn cũng có thể xóa hoặc định dạng lại chính máy khởi động đó.

#### 2.1.16. Đăng nhập vào cụm bằng CLI
Để đăng nhập vào cụm của bạn với tư cách là người dùng hệ thống mặc định, hãy xuất tệp kubeconfig. Cấu hình này cho phép CLI xác thực và kết nối với máy chủ API cụ thể đã được tạo trong quá trình cài đặt OpenShift Container Platform.

Tệp kubeconfig gắn liền với một cụm cụ thể và được OpenShift Container Platform tạo ra trong quá trình cài đặt.

**Điều kiện tiên quyết**

* Bạn đã triển khai một cụm OpenShift Container Platform. 
* Bạn đã cài đặt OpenShift CLI (oc).

**Quy trình**

1. Xuất thông tin xác thực kubeadmin bằng cách chạy lệnh sau:

```shell
$ export KUBECONFIG=<installation_directory>/auth/kubeconfig
```

trong đó: `<installation_directory>` chỉ định đường dẫn đến thư mục lưu trữ các tệp cài đặt.

2. Hãy xác minh rằng bạn có thể chạy thành công các lệnh `oc` bằng cách sử dụng cấu hình đã xuất, thông qua việc thực thi lệnh sau:

```shell
$ oc whoami
```

Ví dụ đầu ra

```
system:admin
```

**Các bước tiếp theo**

* "Tùy chỉnh cụm của bạn"
* "Báo cáo tình trạng từ xa"

#### 2.1.17. Phê duyệt các yêu cầu ký chứng chỉ cho các máy của bạn
Để cho phép các máy mới được thêm vào tham gia cụm OpenShift Container Platform của bạn, hãy đảm bảo rằng cụm tự động phê duyệt các yêu cầu ký chứng chỉ (CSR) đang chờ xử lý, hoặc tự bạn thực hiện phê duyệt các yêu cầu đó. Hãy phê duyệt các yêu cầu từ phía máy khách (client) trước, sau đó mới đến các yêu cầu từ phía máy chủ (server).

**Điều kiện tiên quyết**

* Bạn đã thêm các máy vào cụm của mình.

**Quy trình**

1. Xác nhận rằng cụm đã nhận diện được các máy đó:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME      STATUS    ROLES   AGE  VERSION
master-0  Ready     master  63m  v1.35.4
master-1  Ready     master  63m  v1.35.4
master-2  Ready     master  64m  v1.35.4
```

Kết quả đầu ra liệt kê tất cả các máy mà bạn đã tạo.

> **Lưu ý:** Kết quả đầu ra nêu trên có thể chưa bao gồm các nút tính toán (compute node) cho đến khi bạn phê duyệt một số yêu cầu ký chứng chỉ (CSR).

2. Hãy xem lại các yêu cầu CSR đang chờ xử lý và đảm bảo rằng bạn thấy các yêu cầu từ máy khách với trạng thái "Đang chờ xử lý" (Pending) hoặc "Đã phê duyệt" (Approved) đối với từng máy mà bạn đã thêm vào cụm:

```shell
$ oc get csr
```

Ví dụ đầu ra

```
NAME        AGE     REQUESTOR                                                                   CONDITION
csr-8b2br   15m     system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
csr-8vnps   15m     system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
...
```

Trong ví dụ này, có hai máy đang tham gia vào cụm (cluster). Bạn có thể thấy nhiều yêu cầu ký chứng chỉ (CSR) đã được phê duyệt hơn trong danh sách.

3. Nếu các CSR chưa được phê duyệt, sau khi tất cả các CSR dành cho các máy bạn vừa thêm vào đều ở trạng thái "Đang chờ xử lý" (Pending), hãy tiến hành phê duyệt các CSR đó cho các máy trong cụm của bạn:

> **Lưu ý:** Bạn phải phê duyệt các CSR trong vòng một giờ kể từ khi thêm máy vào cụm. Nếu không thực hiện phê duyệt trong khoảng thời gian này, các chứng chỉ sẽ tự động xoay vòng (rotate), dẫn đến việc xuất hiện nhiều hơn hai chứng chỉ cho mỗi nút (node). Khi đó, bạn sẽ phải phê duyệt tất cả các chứng chỉ này. Sau khi bạn phê duyệt CSR của máy khách (client CSR), kubelet sẽ tạo thêm một CSR thứ hai cho chứng chỉ phục vụ (serving certificate); yêu cầu này cũng cần được phê duyệt thủ công. Sau đó, thành phần `machine-approver` sẽ tự động phê duyệt các yêu cầu gia hạn chứng chỉ phục vụ tiếp theo, miễn là kubelet yêu cầu chứng chỉ mới với các tham số tương tự.

> **Lưu ý:** Đối với các cụm chạy trên những nền tảng không hỗ trợ Machine API — chẳng hạn như trên phần cứng vật lý (bare metal) hoặc cơ sở hạ tầng do người dùng tự cung cấp (user-provisioned infrastructure) — bạn cần triển khai một phương thức để tự động phê duyệt các yêu cầu ký chứng chỉ phục vụ (CSR) từ kubelet. Nếu bạn không phê duyệt yêu cầu này, các lệnh như `oc exec`, `oc rsh` và `oc logs` sẽ không thể thực thi thành công, vì API server yêu cầu phải có chứng chỉ phục vụ khi kết nối với kubelet. Mọi thao tác liên lạc với điểm cuối (endpoint) của kubelet đều đòi hỏi chứng chỉ này phải được phê duyệt. Phương thức triển khai cần theo dõi các CSR mới, xác nhận rằng CSR đó được gửi bởi tài khoản dịch vụ (service account) `node-bootstrapper` (thuộc nhóm `system:node` hoặc `system:admin`), đồng thời xác thực danh tính của nút đó.

  * Để phê duyệt từng yêu cầu một, hãy chạy lệnh sau cho mỗi CSR hợp lệ:

  ```shell
  $ oc adm certificate approve <csr_name>
  ```

  trong đó: `<csr_name>` chỉ định tên của một CSR từ danh sách các CSR hiện có.

  * Để phê duyệt tất cả các CSR đang chờ xử lý, hãy chạy lệnh sau:

  ```shell
  $ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs --no-run-if-empty oc adm certificate approve
  ```

  > **Lưu ý:** Một số Operator có thể chưa khả dụng cho đến khi bạn phê duyệt các CSR. Mỗi node gửi hai CSR, vì vậy bạn có thể cần thực hiện lệnh phê duyệt CSR nhiều lần.

4. Sau khi phê duyệt các yêu cầu từ phía máy khách (client), hãy xem xét các yêu cầu từ phía máy chủ (server) đối với từng máy mà bạn đã thêm vào cụm (cluster):

```shell
$ oc get csr
```

Ví dụ đầu ra

```
NAME        AGE     REQUESTOR                                                                   CONDITION
csr-bfd72   5m26s   system:node:ip-10-0-50-126.us-east-2.compute.internal                       Pending
csr-c57lv   5m26s   system:node:ip-10-0-95-157.us-east-2.compute.internal                       Pending
...
```

5. Nếu các yêu cầu ký chứng chỉ (CSR) còn lại chưa được phê duyệt và đang ở trạng thái "Đang chờ xử lý" (Pending), hãy phê duyệt các CSR đó cho các máy trong cụm (cluster) của bạn:
  * Để phê duyệt từng yêu cầu một, hãy chạy lệnh sau cho mỗi CSR hợp lệ:

  ```shell
  $ oc adm certificate approve <csr_name>
  ```

  trong đó: `<csr_name>` chỉ định tên của một CSR từ danh sách các CSR hiện có.

  * Để phê duyệt tất cả các CSR đang chờ xử lý, hãy chạy lệnh sau:

  ```shell
  $ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | xargs oc adm certificate approve
  ```

6. Sau khi bạn phê duyệt tất cả các CSR của máy khách và máy chủ, các máy đó sẽ chuyển sang trạng thái Sẵn sàng (Ready). Hãy xác minh điều này bằng cách chạy lệnh sau:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME      STATUS    ROLES   AGE  VERSION
master-0  Ready     master  73m  v1.35.4
master-1  Ready     master  73m  v1.35.4
master-2  Ready     master  74m  v1.35.4
worker-0  Ready     worker  11m  v1.35.4
worker-1  Ready     worker  11m  v1.35.4
```

> ((Lưu ý:)) Bạn có thể cần đợi vài phút sau khi các CSR máy chủ được phê duyệt để các máy chuyển sang trạng thái `Ready`.

#### 2.1.18. Cấu hình Operator ban đầu
Sau khi mặt phẳng điều khiển (control plane) khởi tạo xong, bạn cần lập tức cấu hình một số Operator để đảm bảo tất cả chúng đều ở trạng thái sẵn sàng.

**Điều kiện tiên quyết**

* Mặt phẳng điều khiển của bạn đã được khởi tạo.

**Quy trình**

1. Theo dõi quá trình các thành phần trong cụm (cluster) chuyển sang trạng thái hoạt động:

```shell
$ watch -n5 oc get clusteroperators
```

Ví dụ đầu ra

```
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.22.0    True        False         False      19m
baremetal                                  4.22.0    True        False         False      37m
cloud-credential                           4.22.0    True        False         False      40m
cluster-autoscaler                         4.22.0    True        False         False      37m
config-operator                            4.22.0    True        False         False      38m
console                                    4.22.0    True        False         False      26m
csi-snapshot-controller                    4.22.0    True        False         False      37m
dns                                        4.22.0    True        False         False      37m
etcd                                       4.22.0    True        False         False      36m
image-registry                             4.22.0    True        False         False      31m
ingress                                    4.22.0    True        False         False      30m
insights                                   4.22.0    True        False         False      31m
kube-apiserver                             4.22.0    True        False         False      26m
kube-controller-manager                    4.22.0    True        False         False      36m
kube-scheduler                             4.22.0    True        False         False      36m
kube-storage-version-migrator              4.22.0    True        False         False      37m
machine-api                                4.22.0    True        False         False      29m
machine-approver                           4.22.0    True        False         False      37m
machine-config                             4.22.0    True        False         False      36m
marketplace                                4.22.0    True        False         False      37m
monitoring                                 4.22.0    True        False         False      29m
network                                    4.22.0    True        False         False      38m
node-tuning                                4.22.0    True        False         False      37m
openshift-apiserver                        4.22.0    True        False         False      32m
openshift-controller-manager               4.22.0    True        False         False      30m
openshift-samples                          4.22.0    True        False         False      32m
operator-lifecycle-manager                 4.22.0    True        False         False      37m
operator-lifecycle-manager-catalog         4.22.0    True        False         False      37m
operator-lifecycle-manager-packageserver   4.22.0    True        False         False      32m
service-ca                                 4.22.0    True        False         False      38m
storage                                    4.22.0    True        False         False      37m
```

2. Cấu hình các toán tử không khả dụng.

##### 2.1.18.1. Registry hình ảnh (Image Registry) bị loại bỏ trong quá trình cài đặt
Trên các nền tảng không cung cấp dịch vụ lưu trữ đối tượng (object storage) có khả năng chia sẻ, OpenShift Image Registry Operator sẽ tự khởi tạo ở trạng thái "Đã loại bỏ" (Removed). Điều này cho phép `openshift-installer` hoàn tất quá trình cài đặt trên các loại nền tảng đó.

Sau khi cài đặt, bạn cần chỉnh sửa cấu hình của Image Registry Operator để chuyển đổi `managementState` từ `Removed` sang `Managed`. Sau khi hoàn tất bước này, bạn phải tiến hành cấu hình bộ lưu trữ (storage).

##### 2.1.18.2. Cấu hình lưu trữ cho registry hình ảnh (image registry)
Image Registry Operator sẽ không khả dụng ngay từ đầu trên các nền tảng không cung cấp sẵn bộ lưu trữ mặc định. Sau khi cài đặt, bạn cần cấu hình bộ lưu trữ cho registry của mình để kích hoạt Image Registry Operator.

Hãy cấu hình một volume bền vững (persistent volume) – đây là yêu cầu bắt buộc đối với các cụm (cluster) trong môi trường sản xuất. Đối với các cụm không thuộc môi trường sản xuất, bạn có thể sử dụng một thư mục trống (empty directory) làm vị trí lưu trữ nếu phù hợp.

Bạn cũng có thể cho phép image registry sử dụng các loại lưu trữ khối (block storage) bằng cách áp dụng chiến lược triển khai "Recreate" (tạo lại) trong quá trình nâng cấp.

###### 2.1.18.2.1. Cấu hình lưu trữ cho registry trong các môi trường bare metal và các hình thức cài đặt thủ công khác
Với tư cách là quản trị viên cụm (cluster administrator), sau khi hoàn tất cài đặt, bạn cần cấu hình để registry sử dụng bộ lưu trữ.

**Các điều kiện tiên quyết**

* Bạn có quyền truy cập vào cụm với vai trò `cluster-admin`. 
* Cụm của bạn sử dụng các nút (node) Red Hat Enterprise Linux CoreOS (RHCOS) được cung cấp theo cách thủ công, chẳng hạn như trong môi trường bare metal. 
* Bạn đã cung cấp bộ lưu trữ bền vững (persistent storage) cho cụm của mình, ví dụ như Red Hat OpenShift Data Foundation. 

> **Quan trọng:** OpenShift Container Platform hỗ trợ quyền truy cập `ReadWriteOnce` cho bộ lưu trữ của image registry khi chỉ có một bản sao (replica). Quyền truy cập `ReadWriteOnce` cũng yêu cầu registry phải sử dụng chiến lược triển khai (rollout strategy) là `Recreate`. Để triển khai một image registry hỗ trợ tính sẵn sàng cao (high availability) với từ hai bản sao trở lên, cần phải có quyền truy cập `ReadWriteMany`. 

* Bạn cần chuẩn bị một hệ thống có dung lượng lưu trữ tối thiểu là 100Gi.

**Quy trình**

1. Để cấu hình registry sử dụng bộ lưu trữ, hãy thay đổi thuộc tính `spec.storage.pvc` trong tài nguyên `configs.imageregistry/cluster`.

> **Lưu ý:** Khi sử dụng bộ lưu trữ dùng chung, hãy xem lại các thiết lập bảo mật của bạn để ngăn chặn sự truy cập từ bên ngoài.

2. Hãy xác minh rằng bạn không có pod registry nào:

```shell
$ oc get pod -n openshift-image-registry -l docker-registry=default
```

Ví dụ đầu ra

```
No resources found in openshift-image-registry namespace
```

> **Lưu ý:** Nếu kết quả đầu ra của bạn đã có một pod registry, bạn không cần tiếp tục thực hiện quy trình này.

3. Kiểm tra cấu hình registry:

```shell
$ oc edit configs.imageregistry.operator.openshift.io
```

Ví dụ đầu ra

```yaml
storage:
  pvc:
    claim:
```

Để trống trường `claim field` nhằm cho phép tự động tạo một PVC `image-registry-storage`.

4. Kiểm tra trạng thái clusteroperator:

```shell
$ oc get clusteroperator image-registry
```

Ví dụ đầu ra

```
NAME             VERSION              AVAILABLE   PROGRESSING   DEGRADED   SINCE   MESSAGE
image-registry   4.22                 True        False         False      6h50m
```
5. Hãy đảm bảo rằng registry của bạn được thiết lập ở chế độ quản lý để cho phép xây dựng và đẩy ảnh hệ thống.
  * Chạy lệnh:

  ```shell
  $ oc edit configs.imageregistry/cluster
  ```

  Sau đó, thay đổi dòng `managementState: Removed` thành `managementState: Managed`

###### 2.1.18.2.2. Định cấu hình lưu trữ cho registry hình ảnh trong các cụm không thuộc môi trường sản xuất (non-production clusters)
Bạn phải định cấu hình lưu trữ cho Image Registry Operator. Đối với các cụm không thuộc môi trường sản xuất, bạn có thể thiết lập lưu trữ cho registry hình ảnh thành một thư mục trống; tuy nhiên, nếu bạn khởi động lại registry, tất cả các hình ảnh sẽ bị mất.

**Quy trình**

Để thiết lập lưu trữ cho registry hình ảnh thành một thư mục trống:

```shell
$ oc patch configs.imageregistry.operator.openshift.io cluster --type merge --patch '{"spec":{"storage":{"emptyDir":{}}}}'
```

> **Cảnh báo:** Chỉ cấu hình tùy chọn này cho các cụm không thuộc môi trường sản xuất.

Nếu bạn chạy lệnh này trước khi Image Registry Operator khởi tạo các thành phần của nó, lệnh `oc patch` sẽ thất bại với lỗi sau:

Ví dụ đầu ra

```
Error from server (NotFound): configs.imageregistry.operator.openshift.io "cluster" not found
```

Hãy đợi vài phút rồi chạy lại lệnh đó.

###### 2.1.18.2.3. Cấu hình lưu trữ dạng khối (block storage) cho registry trên hạ tầng vật lý (bare metal)
Để cho phép registry lưu trữ ảnh (image registry) sử dụng các loại lưu trữ dạng khối trong quá trình nâng cấp, với tư cách là quản trị viên cụm (cluster administrator), bạn có thể sử dụng chiến lược triển khai `Recreate`.

> **Quan trọng:** Các volume lưu trữ dạng khối (block storage volumes) hay các volume bền vững dạng khối (block persistent volumes) được hỗ trợ nhưng không được khuyến nghị sử dụng cho registry lưu trữ ảnh trên các cụm chạy thực tế (production clusters). Trong cấu hình này, registry sẽ không có tính sẵn sàng cao (high availability) vì nó chỉ có thể chạy với duy nhất một bản sao (replica).

Nếu bạn chọn sử dụng volume lưu trữ dạng khối cho registry lưu trữ ảnh, bạn bắt buộc phải sử dụng yêu cầu volume bền vững (Persistent Volume Claim - PVC) dựa trên hệ thống tệp (filesystem).

**Quy trình**

1. Nhập lệnh sau để thiết lập lưu trữ cho registry lưu trữ ảnh thành loại lưu trữ dạng khối, đồng thời điều chỉnh (patch) registry để sử dụng chiến lược triển khai `Recreate` và chỉ chạy với một (1) bản sao (replica):

```shell
$ oc patch config.imageregistry.operator.openshift.io/cluster \
--type=merge -p '{"spec":{"rolloutStrategy":"Recreate","replicas":1}}'
```

2. Cung cấp PV cho thiết bị lưu trữ khối (block storage device) và tạo PVC cho volume đó. Volume khối được yêu cầu sử dụng chế độ truy cập ReadWriteOnce (RWO). 
  * Tạo tệp `pvc.yaml` với nội dung dưới đây để định nghĩa một đối tượng PersistentVolumeClaim (PVC) cho VMware vSphere:

  ```yaml
  kind: PersistentVolumeClaim
  apiVersion: v1
  metadata:
    name: image-registry-storage
    namespace: openshift-image-registry
  spec:
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 100Gi
  ```

  Trong đó:

    * `metadata.name`: Chỉ định một tên duy nhất đại diện cho đối tượng `PersistentVolumeClaim`.
    * `metadata.namespace`: Chỉ định namespace cho đối tượng `PersistentVolumeClaim`; trong trường hợp này là `openshift-image-registry`.
    * `spec.accessModes`: Chỉ định chế độ truy cập (access mode) của `PersistentVolumeClaim`. Với tùy chọn `ReadWriteOnce`, volume có thể được mount (gắn) với quyền đọc và ghi bởi một node duy nhất.
    * `spec.resources.requests.storage`: Dung lượng của `PersistentVolumeClaim`.

  * Hãy nhập lệnh sau để tạo đối tượng PersistentVolumeClaim từ tệp:

  ```shell
  $ oc create -f pvc.yaml -n openshift-image-registry
  ```

3. Hãy nhập lệnh sau để chỉnh sửa cấu hình registry sao cho nó tham chiếu đến PVC chính xác:

```shell
$ oc edit config.imageregistry.operator.openshift.io -o yaml
```

Ví dụ đầu ra

```yaml
storage:
  pvc:
    claim:
```

Khi tạo một PVC tùy chỉnh, bạn có thể để trống trường `claim` để hệ thống tự động tạo một PVC `image-registry-storage` theo mặc định.

#### 2.1.19. Hoàn tất cài đặt trên cơ sở hạ tầng do người dùng cung cấp
Để hoàn tất quá trình cài đặt trên cơ sở hạ tầng do người dùng cung cấp, hãy thực hiện các bước cuối cùng để triển khai cụm (cluster) sau khi đã định cấu hình các Operator. Việc này đảm bảo cụm hoạt động hoàn chỉnh trên cơ sở hạ tầng mà bạn cung cấp.

**Điều kiện tiên quyết**

* Mặt phẳng điều khiển của bạn đã được khởi tạo. 
* Bạn đã hoàn tất việc định cấu hình ban đầu cho các Operator.

**Quy trình**

1. Xác nhận rằng tất cả các thành phần của cụm đều đang trực tuyến (online) bằng cách sử dụng lệnh sau:

```shell
$ watch -n5 oc get clusteroperators
```

Ví dụ đầu ra

```
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.22.0    True        False         False      19m
baremetal                                  4.22.0    True        False         False      37m
cloud-credential                           4.22.0    True        False         False      40m
cluster-autoscaler                         4.22.0    True        False         False      37m
config-operator                            4.22.0    True        False         False      38m
console                                    4.22.0    True        False         False      26m
csi-snapshot-controller                    4.22.0    True        False         False      37m
dns                                        4.22.0    True        False         False      37m
etcd                                       4.22.0    True        False         False      36m
image-registry                             4.22.0    True        False         False      31m
ingress                                    4.22.0    True        False         False      30m
insights                                   4.22.0    True        False         False      31m
kube-apiserver                             4.22.0    True        False         False      26m
kube-controller-manager                    4.22.0    True        False         False      36m
kube-scheduler                             4.22.0    True        False         False      36m
kube-storage-version-migrator              4.22.0    True        False         False      37m
machine-api                                4.22.0    True        False         False      29m
machine-approver                           4.22.0    True        False         False      37m
machine-config                             4.22.0    True        False         False      36m
marketplace                                4.22.0    True        False         False      37muser
monitoring                                 4.22.0    True        False         False      29m
network                                    4.22.0    True        False         False      38m
node-tuning                                4.22.0    True        False         False      37m
openshift-apiserver                        4.22.0    True        False         False      32muser
openshift-controller-manager               4.22.0    True        False         False      30m
openshift-samples                          4.22.0    True        False         False      32m
operator-lifecycle-manager                 4.22.0    True        False         False      37m
operator-lifecycle-manager-catalog         4.22.0    True        False         False      37m
operator-lifecycle-manager-packageserver   4.22.0    True        False         False      32m
service-ca                                 4.22.0    True        False         False      38m
storage                                    4.22.0    True        False         False      37m
```

Ngoài ra, lệnh sau đây sẽ thông báo cho bạn khi tất cả các cụm đã sẵn sàng. Lệnh này cũng truy xuất và hiển thị thông tin xác thực:

```shell
$ ./openshift-install --dir <installation_directory> wait-for install-complete
```

trong đó: `<installation_directory>` chỉ định đường dẫn đến thư mục nơi bạn lưu trữ các tệp cài đặt.

Ví dụ đầu ra

```
INFO Waiting up to 30m0s for the cluster to initialize...
```

Lệnh này thực thi thành công khi Cluster Version Operator hoàn tất việc triển khai cụm OpenShift Container Platform từ máy chủ API Kubernetes.

> **Quan trọng**
> 
> * Các tệp cấu hình Ignition do chương trình cài đặt tạo ra chứa các chứng chỉ có thời hạn 24 giờ; sau khoảng thời gian này, các chứng chỉ đó sẽ được gia hạn. Nếu cụm bị tắt trước khi các chứng chỉ được gia hạn và sau đó được khởi động lại khi đã qua mốc 24 giờ, cụm sẽ tự động khôi phục các chứng chỉ đã hết hạn. Một ngoại lệ là bạn phải phê duyệt thủ công các yêu cầu ký chứng chỉ (CSR) của node-bootstrapper đang ở trạng thái chờ để khôi phục các chứng chỉ kubelet. Vui lòng tham khảo tài liệu về cách khôi phục các chứng chỉ control plane đã hết hạn để biết thêm thông tin. 
> * Bạn nên sử dụng các tệp cấu hình Ignition trong vòng 12 giờ kể từ khi chúng được tạo ra, vì quá trình xoay vòng chứng chỉ (vốn có thời hạn 24 giờ) sẽ diễn ra trong khoảng từ 16 đến 22 giờ sau khi cụm được cài đặt. Việc sử dụng các tệp cấu hình Ignition trong vòng 12 giờ giúp tránh nguy cơ cài đặt thất bại trong trường hợp quá trình cập nhật chứng chỉ diễn ra đúng lúc đang thực hiện cài đặt.

2. Xác nhận rằng Kubernetes API server đang giao tiếp với các pod. 
  * Để xem danh sách tất cả các pod, hãy sử dụng lệnh sau:

  ```shell
  $ oc get pods --all-namespaces
  ```

  Ví dụ đầu ra

  ```
  NAMESPACE                         NAME                                            READY   STATUS      RESTARTS   AGE
  openshift-apiserver-operator      openshift-apiserver-operator-85cb746d55-zqhs8   1/1     Running     1          9m
  openshift-apiserver               apiserver-67b9g                                 1/1     Running     0          3m
  openshift-apiserver               apiserver-ljcmx                                 1/1     Running     0          1m
  openshift-apiserver               apiserver-z25h4                                 1/1     Running     0          2m
  openshift-authentication-operator authentication-operator-69d5d8bf84-vh2n8        1/1     Running     0          5m
  ```

  * Xem nhật ký của một pod có tên trong kết quả đầu ra của lệnh trước đó bằng cách sử dụng lệnh sau:

  ```shell
  $ oc logs <pod_name> -n <namespace>
  ```

  trong đó: `<namespace>` chỉ định tên pod và namespace (không gian tên), như được hiển thị trong kết quả của một lệnh trước đó. Nếu nhật ký (logs) của pod được hiển thị, nghĩa là máy chủ API Kubernetes có thể giao tiếp với các máy trong cụm (cluster).

3. Đối với hệ thống sử dụng Giao thức Fibre Channel (FCP), cần thực hiện các bước bổ sung để kích hoạt tính năng đa đường dẫn (multipathing). Không kích hoạt tính năng đa đường dẫn trong quá trình cài đặt.

#### 2.1.20. Truy cập Telemetry cho OpenShift Container Platform
Để cung cấp các chỉ số về tình trạng cụm (cluster) và kết quả của các bản cập nhật, dịch vụ Telemetry cần có quyền truy cập internet. Khi được kết nối, dịch vụ này sẽ tự động chạy theo mặc định và đăng ký cụm của bạn với [OpenShift Cluster Manager](https://console.redhat.com/openshift).

Sau khi xác nhận rằng danh mục tài nguyên (inventory) trong [OpenShift Cluster Manager](https://console.redhat.com/openshift) đã chính xác — dù được duy trì tự động bởi Telemetry hay được cập nhật thủ công thông qua OpenShift Cluster Manager — bạn có thể sử dụng tính năng "subscription watch" để theo dõi các gói đăng ký (subscription) OpenShift Container Platform của mình ở cấp độ tài khoản hoặc cấp độ đa cụm (multi-cluster).

### 2.2. Cài đặt cụm trên hạ tầng vật lý (bare metal) do người dùng cung cấp với các tùy chỉnh mạng
Trong OpenShift Container Platform 4.22, bạn có thể cài đặt một cụm trên hạ tầng vật lý (bare metal) do chính bạn cung cấp, với các tùy chọn cấu hình mạng được tùy chỉnh. Việc tùy chỉnh cấu hình mạng cho phép cụm của bạn cùng tồn tại với các phân bổ địa chỉ IP hiện có trong môi trường của bạn, đồng thời tích hợp được với các cấu hình MTU và VXLAN đã thiết lập sẵn.

Khi tùy chỉnh hệ thống mạng cho OpenShift Container Platform, bạn phải thiết lập hầu hết các tham số cấu hình mạng trong quá trình cài đặt. Đối với một cụm đang hoạt động, bạn chỉ có thể sửa đổi các tham số cấu hình mạng liên quan đến `kubeProxy`.

#### 2.2.1. Các điều kiện tiên quyết
* Bạn đã xem xét các thông tin chi tiết về quy trình cài đặt và cập nhật OpenShift Container Platform. 
* Bạn đã đọc tài liệu hướng dẫn về việc chọn phương thức cài đặt cụm (cluster) và chuẩn bị cụm đó cho người dùng. 
* Nếu bạn sử dụng tường lửa và dự định dùng dịch vụ Telemetry, bạn đã cấu hình tường lửa để cho phép truy cập vào các trang web mà cụm của bạn cần kết nối tới.

#### 2.2.2. Truy cập Internet cho OpenShift Container Platform
Trong OpenShift Container Platform 4.22, bạn cần có quyền truy cập Internet để cài đặt cụm (cluster) của mình.

Bạn bắt buộc phải có quyền truy cập Internet để thực hiện các thao tác sau:

* Truy cập Red Hat Hybrid Cloud Console để tải xuống chương trình cài đặt và quản lý đăng ký (subscription). Nếu cụm có quyền truy cập Internet và bạn không tắt tính năng Telemetry, dịch vụ này sẽ tự động cấp quyền sử dụng (entitlement) cho cụm của bạn. 
* Truy cập Quay.io để lấy các gói cần thiết cho việc cài đặt cụm. 
* Lấy các gói cần thiết để thực hiện cập nhật cụm.

> **Quan trọng:** Nếu cụm của bạn không thể truy cập Internet trực tiếp, bạn có thể thực hiện cài đặt trong môi trường mạng bị hạn chế (restricted network installation) trên một số loại cơ sở hạ tầng nhất định. Trong quá trình này, bạn sẽ tải xuống các nội dung cần thiết và sử dụng chúng để nạp vào một registry phản chiếu (mirror registry) chứa các gói cài đặt. Với một số kiểu cài đặt, môi trường nơi bạn triển khai cụm sẽ không yêu cầu quyền truy cập Internet. Trước khi cập nhật cụm, bạn cần cập nhật nội dung trong registry phản chiếu đó.

#### 2.2.3. Các yêu cầu đối với cụm sử dụng hạ tầng do người dùng cung cấp
Đối với một cụm bao gồm hạ tầng do người dùng cung cấp, bạn phải triển khai tất cả các máy cần thiết.

Phần này mô tả các yêu cầu để triển khai OpenShift Container Platform trên hạ tầng do người dùng cung cấp.

##### 2.2.3.1. Các máy cần thiết để cài đặt cụm (cluster)
Bạn phải xác định số lượng máy hoặc máy chủ tối thiểu cần thiết cho cụm của mình, nhằm đảm bảo cụm vẫn duy trì sự ổn định ngay cả khi một nút (node) gặp sự cố.

Các cụm OpenShift Container Platform với quy mô nhỏ nhất yêu cầu các máy chủ sau:
Quan trọng

Đối với một cụm sử dụng cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải triển khai tất cả các máy cần thiết.

**Bảng 2.10. Các máy chủ tối thiểu cần thiết**

| Máy chủ                                                                       | Mô tả                                                                                                                                                                                                  |
|-------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Một máy khởi động tạm thời                                                    | Cụm yêu cầu phải có máy bootstrap để triển khai cụm OpenShift Container Platform trên ba máy thuộc mặt phẳng điều khiển (control plane). Bạn có thể loại bỏ máy bootstrap sau khi đã cài đặt xong cụm. |
| Ba máy mặt phẳng điều khiển                                                   | Các máy thuộc mặt phẳng điều khiển (control plane) chạy các dịch vụ Kubernetes và OpenShift Container Platform, tạo nên mặt phẳng điều khiển đó.                                                       |
| Ít nhất hai máy tính toán, còn được gọi là các máy thực thi (worker machine). | Các khối lượng công việc do người dùng OpenShift Container Platform yêu cầu sẽ chạy trên các máy tính toán.                                                                                            |

> **Lưu ý:** Một trường hợp ngoại lệ là bạn có thể vận hành cụm máy chủ bare metal (phần cứng vật lý) mà không cần máy chủ tính toán (compute machine) nào, chỉ bao gồm ba máy chủ điều khiển (control plane machine). Cấu hình này tạo ra các cụm nhỏ gọn, sử dụng tài nguyên hiệu quả hơn, phù hợp để các quản trị viên và nhà phát triển sử dụng cho mục đích thử nghiệm, phát triển và vận hành thực tế. Việc vận hành chỉ với một máy chủ tính toán là không được hỗ trợ.

> **Quan trọng:** Để duy trì tính sẵn sàng cao cho cụm máy chủ, hãy sử dụng các máy chủ vật lý riêng biệt cho từng máy thành phần trong cụm.

Các máy chủ khởi động (bootstrap machine) và máy chủ điều khiển (control plane machine) bắt buộc phải sử dụng hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS). Trong khi đó, các máy chủ tính toán (compute machine) có thể sử dụng Red Hat Enterprise Linux CoreOS (RHCOS) hoặc Red Hat Enterprise Linux (RHEL) phiên bản 8.6 trở lên.

RHCOS được xây dựng dựa trên Red Hat Enterprise Linux (RHEL) 9.8 và kế thừa toàn bộ các chứng nhận cũng như yêu cầu về phần cứng của hệ điều hành này. Vui lòng tham khảo thông tin về [các khả năng và giới hạn công nghệ của Red Hat Enterprise Linux](https://access.redhat.com/articles/rhel-limits).

##### 2.2.3.2. Các yêu cầu tối thiểu về tài nguyên để cài đặt cụm (cluster)
Để đảm bảo cụm OpenShift Container Platform của bạn vận hành đúng như mong đợi, mỗi máy trong cụm phải đáp ứng các yêu cầu tối thiểu về CPU, bộ nhớ và lưu trữ.

**Bảng 2.11. Các yêu cầu tối thiểu về tài nguyên**

| Máy                  | Hệ điều hành | CPU | RAM   | Lưu trữ | IOPS |
|----------------------|--------------|-----|-------|---------|------|
| Bootstrap            | RHCOS        | 4   | 16 GB | 100 GB  | 300  |
| Mặt phẳng điều khiển | RHCOS        | 4   | 16 GB | 100 GB  | 300  |
| Tính toán            | RHCOS        | 2   | 8 GB  | 100 GB  | 300  |

* Một CPU tương đương với một nhân vật lý khi tính năng đa luồng đồng thời (SMT) – hay còn gọi là Hyper-Threading – không được kích hoạt. Khi tính năng này được bật, hãy sử dụng công thức sau để tính toán tỷ lệ tương ứng: (số luồng trên mỗi nhân × số nhân) × số socket = số CPU.
* OpenShift Container Platform và Kubernetes rất nhạy cảm với hiệu năng ổ đĩa. Red Hat khuyến nghị sử dụng hệ thống lưu trữ có tốc độ cao, đặc biệt là đối với etcd trên các nút mặt phẳng điều khiển (control plane nodes) – nơi yêu cầu thời gian fsync p99 đạt mức 10 ms. Trên nhiều nền tảng đám mây, dung lượng lưu trữ và chỉ số IOPS thường tỷ lệ thuận với nhau; do đó, bạn có thể cần cấp phát thêm dung lượng lưu trữ để đạt được hiệu năng cần thiết.
* Đối với các mô hình cài đặt do người dùng tự triển khai (user-provisioned installations), nếu bạn chọn sử dụng các máy tính (compute machines) chạy hệ điều hành RHEL trong cụm của mình, bạn sẽ chịu trách nhiệm quản lý toàn bộ vòng đời và bảo trì hệ điều hành đó. Điều này bao gồm việc thực hiện các bản cập nhật hệ thống, áp dụng các bản vá lỗi và hoàn tất mọi tác vụ cần thiết khác. OpenShift Container Platform phiên bản 4.10 trở về sau không hỗ trợ các máy tính chạy hệ điều hành RHEL 7.

> **Lưu ý:** Trong OpenShift Container Platform phiên bản 4.22, RHCOS sử dụng RHEL phiên bản 9.8, phiên bản này cập nhật các yêu cầu về vi kiến trúc. Mỗi kiến trúc yêu cầu các tập lệnh (ISA) tối thiểu sau đây:
> 
> * Kiến trúc x86-64 yêu cầu tập lệnh x86-64-v2
> * Kiến trúc ARM64 yêu cầu tập lệnh ARMv8.0-A
> * Kiến trúc ppc64le yêu cầu tập lệnh IBM® Power9
> * Kiến trúc s390x yêu cầu tập lệnh IBM® z14

Nếu một loại instance dành cho nền tảng của bạn đáp ứng các yêu cầu tối thiểu đối với các máy trong cụm (cluster), thì loại instance đó được hỗ trợ để sử dụng trong OpenShift Container Platform.

##### 2.2.3.3. Quản lý các yêu cầu ký chứng chỉ (Certificate Signing Requests - CSR)
Trên cơ sở hạ tầng do người dùng cung cấp, nếu cụm (cluster) của bạn bị hạn chế quyền truy cập vào các cơ chế quản lý máy tự động, bạn cần triển khai một quy trình để phê duyệt các yêu cầu ký chứng chỉ (CSR) của cụm sau khi hoàn tất cài đặt.

Thành phần `kube-controller-manager` chỉ thực hiện phê duyệt các CSR dành cho chứng chỉ máy khách (client certificate) của `kubelet`. Cơ chế phê duyệt máy (`machine-approver`) không thể đảm bảo tính hợp lệ của các chứng chỉ phục vụ (serving certificate) mà các thông tin xác thực của `kubelet` yêu cầu, do không thể xác nhận liệu yêu cầu đó có thực sự xuất phát từ đúng thiết bị hay không. Vì vậy, bạn phải tìm kiếm và triển khai một phương thức để xác minh tính hợp lệ của các yêu cầu cấp chứng chỉ phục vụ cho `kubelet`, đồng thời thực hiện phê duyệt các yêu cầu đó.

##### 2.2.3.4. Các yêu cầu về mạng đối với hạ tầng do người dùng cung cấp
Bạn phải định cấu hình mạng cho tất cả các máy chạy Red Hat Enterprise Linux CoreOS (RHCOS) trong giai đoạn initramfs khi khởi động, để các máy này có thể truy xuất các tệp cấu hình Ignition của chúng.

Trong quá trình khởi động ban đầu, các máy cần có cấu hình địa chỉ IP, được thiết lập thông qua máy chủ DHCP hoặc được thiết lập tĩnh bằng cách cung cấp các tùy chọn khởi động cần thiết. Sau khi kết nối mạng được thiết lập, các máy sẽ tải xuống các tệp cấu hình Ignition từ một máy chủ HTTP hoặc HTTPS. Các tệp cấu hình Ignition này sau đó được sử dụng để thiết lập trạng thái chính xác cho từng máy. Sau khi cài đặt, thành phần Machine Config Operator sẽ thực hiện thêm các thay đổi trên các máy, chẳng hạn như áp dụng các chứng chỉ hoặc khóa mới.

> **Lưu ý:**
> 
> * Bạn nên cân nhắc sử dụng máy chủ DHCP để quản lý lâu dài các máy trong cụm (cluster). Hãy đảm bảo rằng máy chủ DHCP được định cấu hình để cung cấp các địa chỉ IP cố định, thông tin máy chủ DNS và tên máy chủ (hostname) cho các máy trong cụm. 
Nếu dịch vụ DHCP không khả dụng trong hạ tầng do bạn cung cấp, bạn có thể cung cấp cấu hình mạng IP và địa chỉ máy chủ DNS cho các nút (node) tại thời điểm cài đặt RHCOS. Các thông tin này có thể được truyền dưới dạng tham số khởi động (boot argument) nếu bạn cài đặt từ tệp ảnh ISO. Xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform" để biết thêm thông tin về việc cung cấp địa chỉ IP tĩnh và các tùy chọn mạng nâng cao.
> * Máy chủ API Kubernetes (Kubernetes API server) phải có khả năng phân giải tên các nút trong cụm. Nếu các máy chủ API và các nút worker nằm trong các vùng (zone) khác nhau, bạn có thể định cấu hình vùng tìm kiếm DNS mặc định (default DNS search zone) để cho phép máy chủ API phân giải tên các nút. Một phương pháp khác được hỗ trợ là luôn tham chiếu đến các máy chủ (host) bằng tên miền đầy đủ (fully-qualified domain name - FQDN) trong cả các đối tượng nút (node object) và tất cả các yêu cầu DNS.

###### 2.2.3.4.1. Thiết lập tên máy chủ (hostname) cho các nút trong cụm thông qua DHCP
Trên các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS), tên máy chủ (hostname) được thiết lập thông qua NetworkManager. Theo mặc định, các máy này nhận tên máy chủ thông qua DHCP. Nếu tên máy chủ không được cung cấp qua DHCP, không được thiết lập tĩnh thông qua các tham số nhân (kernel arguments), hoặc không được xác định bằng phương thức nào khác, thì hệ thống sẽ lấy tên này thông qua truy vấn DNS ngược (reverse DNS lookup). Quá trình truy vấn DNS ngược diễn ra sau khi mạng đã được khởi tạo trên một nút và có thể mất một khoảng thời gian để hoàn tất. Các dịch vụ hệ thống khác có thể khởi chạy trước thời điểm đó và nhận diện tên máy chủ là "localhost" hoặc một tên tương tự. Bạn có thể tránh tình trạng này bằng cách sử dụng DHCP để cung cấp tên máy chủ cho từng nút trong cụm.

Ngoài ra, việc thiết lập tên máy chủ thông qua DHCP còn giúp tránh các lỗi cấu hình thủ công đối với bản ghi DNS trong các môi trường triển khai kiến trúc DNS "split-horizon" (DNS phân tách theo phạm vi truy cập).

###### 2.2.3.4.2. Các yêu cầu về kết nối mạng
Bạn phải cấu hình kết nối mạng giữa các máy để cho phép các thành phần trong cụm OpenShift Container Platform có thể giao tiếp với nhau. Mỗi máy phải có khả năng phân giải tên máy chủ (hostname) của tất cả các máy khác trong cụm.

Phần này cung cấp thông tin chi tiết về các cổng (port) cần thiết.

> **Quan trọng:** Trong các môi trường OpenShift Container Platform có kết nối mạng, tất cả các nút (node) đều cần có quyền truy cập internet để tải xuống các image cho các container của nền tảng, cũng như để gửi dữ liệu đo từ xa (telemetry data) tới Red Hat.

**Bảng 2.12. Các cổng được sử dụng cho hoạt động giao tiếp giữa tất cả các máy với nhau**

| Giao thức | Cổng            | Mô tả                                                                                                                                         |
|-----------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| ICMP      | N/A             | Kiểm tra khả năng kết nối mạng                                                                                                                |
| TCP       | `1936`          | Các chỉ số                                                                                                                                    |
| TCP       | `9000`-`9999`   | Các dịch vụ ở cấp độ máy chủ (host), bao gồm node exporter trên các cổng `9100`-`9101` và Cluster Version Operator trên cổng `9099`.          |
| TCP       | `10250`-`10259` | Các cổng mặc định mà Kubernetes dành riêng                                                                                                    |
| TCP       | `22623`         | Cổng này xử lý lưu lượng truy cập từ Machine Config Server và điều hướng lưu lượng đó đến các máy thuộc mặt phẳng điều khiển (control plane). |
| UDP       | `6081`          | Geneve                                                                                                                                        |
| UDP       | `9000`-`9999`   | Các dịch vụ ở cấp độ máy chủ, bao gồm node exporter trên các cổng `9100`-`9101`.                                                              |
| UDP       | `500`           | Các gói tin IPsec IKE                                                                                                                         |
| UDP       | `4500`          | Các gói tin IPsec NAT-T                                                                                                                       |
| UDP       | `123`           | Network Time Protocol (NTP) sử dụng cổng UDP `123`. Nếu máy chủ thời gian NTP bên ngoài được cấu hình, bạn phải mở cổng UDP `123`.            |
| TCP/UDP   | `30000`-`32767` | Cổng nút Kubernetes                                                                                                                           |
| ESP       | N/A             | Giao thức đóng gói bảo mật (ESP) trong IPsec                                                                                                  |

**Bảng 2.13. Các cổng được sử dụng cho hoạt động giao tiếp giữa tất cả các máy với mặt phẳng điều khiển (control plane)**

| Giao thức | Cổng   | Mô tả          |
|-----------|--------|----------------|
| TCP       | `6443` | Kubernetes API |

**Bảng 2.14. Các cổng được sử dụng cho hoạt động giao tiếp giữa các máy trong mặt phẳng điều khiển (control plane)**

| Giao thức | Cổng          | Mô tả                                                           |
|-----------|---------------|-----------------------------------------------------------------|
| TCP       | `2379`-`2380` | Các cổng dành cho máy chủ và các nút ngang hàng (peer) của etcd |

###### 2.2.3.4.3. Cấu hình NTP cho cơ sở hạ tầng do người dùng cung cấp
Theo mặc định, các cụm OpenShift Container Platform được cấu hình để sử dụng máy chủ Giao thức Thời gian Mạng (NTP) công cộng. Nếu bạn muốn sử dụng máy chủ NTP nội bộ của doanh nghiệp, hoặc nếu cụm của bạn được triển khai trong một mạng không kết nối với internet (disconnected network), bạn có thể cấu hình cụm để sử dụng một máy chủ thời gian cụ thể. Để biết thêm thông tin, hãy xem tài liệu về cách cấu hình dịch vụ thời gian chrony.

Nếu máy chủ DHCP cung cấp thông tin về máy chủ NTP, dịch vụ thời gian chrony trên các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) sẽ đọc thông tin đó và có thể đồng bộ hóa đồng hồ hệ thống với các máy chủ NTP này.

##### 2.2.3.5. Các yêu cầu về DNS do người dùng cung cấp
Trong các triển khai OpenShift Container Platform, bạn phải đảm bảo rằng các thành phần của cụm (cluster) đáp ứng được những tiêu chí nhất định về phân giải tên DNS, phục vụ cho các mục đích như giao tiếp nội bộ, xác thực chứng chỉ và tự động phát hiện nút (node).

Dưới đây là danh sách các thành phần cụm bắt buộc:

* Kubernetes API
* Wildcard (tên miền đại diện) cho ứng dụng trên OpenShift Container Platform
* Các máy bootstrap và các máy thuộc mặt phẳng điều khiển (control plane)
* Các máy tính toán (compute machines)

Việc phân giải DNS ngược (reverse DNS resolution) cũng là yêu cầu bắt buộc đối với Kubernetes API, máy bootstrap, các máy thuộc mặt phẳng điều khiển và các máy tính toán.

Các bản ghi DNS loại A/AAAA hoặc CNAME được sử dụng để phân giải tên, trong khi các bản ghi PTR được sử dụng để phân giải tên ngược. Các bản ghi ngược đóng vai trò quan trọng vì Red Hat Enterprise Linux CoreOS (RHCOS) sử dụng chúng để thiết lập tên máy (hostname) cho tất cả các nút, trừ khi tên máy đã được cung cấp thông qua DHCP. Ngoài ra, các bản ghi ngược còn được dùng để tạo các yêu cầu ký chứng chỉ (Certificate Signing Request - CSR) cần thiết cho hoạt động của OpenShift Container Platform.

> **Lưu ý:** Bạn nên sử dụng máy chủ DHCP để cung cấp tên máy cho từng nút trong cụm. Hãy tham khảo phần các khuyến nghị về DHCP cho hạ tầng do người dùng cung cấp (user-provisioned infrastructure) để biết thêm thông tin.

Các bản ghi DNS dưới đây là bắt buộc đối với cụm OpenShift Container Platform do người dùng cung cấp và phải được thiết lập trước khi tiến hành cài đặt. Trong mỗi bản ghi, `<cluster_name>` là tên của cụm và `<base_domain>` là tên miền cơ sở mà bạn chỉ định trong tệp `install-config.yaml`. Một bản ghi DNS hoàn chỉnh sẽ có dạng: `<component>.<cluster_name>.<base_domain>.`

**Bảng 2.15. Các bản ghi DNS bắt buộc**

| Thành phần                   | Bản ghi                                            | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|------------------------------|----------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kubernetes API               | `api.<cluster_name>.<base_domain>.`                | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định bộ cân bằng tải API. Các bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm và tất cả các nút bên trong cụm đó.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Kubernetes API               | `api-int.<cluster_name>.<base_domain>.`            | Cần có bản ghi DNS A/AAAA hoặc CNAME, cùng với bản ghi DNS PTR, để xác định bộ cân bằng tải API (API load balancer) trong nội bộ hệ thống. Các bản ghi này phải có thể được phân giải từ tất cả các nút (node) bên trong cụm (cluster). Quan trọng: Máy chủ API (API server) phải có khả năng phân giải các nút worker thông qua tên máy chủ (hostname) đã được ghi lại trong Kubernetes. Nếu máy chủ API không thể phân giải tên các nút, thì các lệnh gọi API thông qua proxy có thể bị lỗi, đồng thời bạn sẽ không thể truy xuất nhật ký (log) từ các pod.                                                                                                   |
| Routes                       | `*.apps.<cluster_name>.<base_domain>.`             | Một bản ghi DNS wildcard (dạng A/AAAA hoặc CNAME) trỏ đến bộ cân bằng tải ingress (ingress load balancer) của ứng dụng. Bộ cân bằng tải ingress này hướng lưu lượng truy cập đến các máy chủ đang chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller này hoạt động trên các máy chủ tính toán (compute machines). Các bản ghi này phải có thể được phân giải bởi cả các client bên ngoài cụm (cluster) lẫn tất cả các node nằm bên trong cụm đó. Ví dụ: `console-openshift-console.apps.<cluster_name>.<base_domain>` được sử dụng làm tuyến đường (route) wildcard dẫn đến bảng điều khiển (console) của OpenShift Container Platform. |
| Máy khởi động                | `bootstrap.<cluster_name>.<base_domain>.`          | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định máy khởi động (bootstrap machine). Các bản ghi này phải có thể được phân giải bởi các nút (node) nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Máy chủ mặt phẳng điều khiển | `<control_plane><n>.<cluster_name>.<base_domain>.` | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút thuộc mặt phẳng điều khiển (control plane nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                           |
| Máy tính toán                | `<compute><n>.<cluster_name>.<base_domain>.`       | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút worker (worker nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                                      |

> **Lưu ý:** Trong OpenShift Container Platform 4.4 trở về sau, bạn không cần chỉ định các bản ghi SRV và máy chủ etcd trong cấu hình DNS của mình.

> **Mẹo:** Bạn có thể sử dụng lệnh `dig` để xác minh quá trình phân giải tên miền và phân giải ngược. Hãy xem phần "Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp" để biết các bước xác thực chi tiết.

###### 2.2.3.5.1. Ví dụ về cấu hình DNS cho các cụm (cluster) do người dùng cung cấp
Hãy tham khảo các ví dụ về cấu hình DNS để hiểu cách thiết lập các bản ghi A và PTR nhằm đáp ứng các yêu cầu DNS khi triển khai OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp.

Các ví dụ về cấu hình DNS được cung cấp tại đây chỉ mang tính chất tham khảo và không nhằm mục đích đưa ra lời khuyên về việc lựa chọn giải pháp DNS này hay giải pháp DNS khác.

Trong các ví dụ này, tên cụm (cluster name) là ocp4 và tên miền cơ sở (base domain) là example.com.

Ví dụ dưới đây là một tệp vùng (zone file) BIND, minh họa các bản ghi DNS A mẫu phục vụ việc phân giải tên trong một cụm do người dùng cung cấp.

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
control-plane2.ocp4.example.com.  IN  A 192.168.1.99
;
compute0.ocp4.example.com.  IN  A 192.168.1.11
compute1.ocp4.example.com.  IN  A 192.168.1.7
;
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API.
* api-int.ocp4.example.com.: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho API và được sử dụng cho các giao tiếp nội bộ trong cụm (cluster).
* `*.apps.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các tuyến đường (route) dạng ký tự đại diện (wildcard). Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho ứng dụng (application ingress load balancer). Bộ cân bằng tải này hướng lưu lượng đến các máy chủ đang chạy các pod Ingress Controller.
* `bootstrap.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho máy chủ bootstrap.
* `control-plane0.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy chủ thuộc mặt phẳng điều khiển (control plane).
* `compute0.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy chủ tính toán (compute).

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
99.1.168.192.in-addr.arpa.  IN  PTR control-plane2.ocp4.example.com.
;
11.1.168.192.in-addr.arpa.  IN  PTR compute0.ocp4.example.com.
7.1.168.192.in-addr.arpa. IN  PTR compute1.ocp4.example.com.
;
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược (reverse DNS resolution) cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải (load balancer) API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải API và được sử dụng cho các hoạt động giao tiếp nội bộ trong cụm (cluster).
* `bootstrap.ocp4.example.com.`.: Cung cấp khả năng phân giải DNS ngược cho máy bootstrap.
* `control-plane0.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy thuộc mặt phẳng điều khiển (control plane).
* `compute0.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy tính toán (compute machines).

> **Lưu ý:** Không bắt buộc phải có bản ghi PTR cho tên miền đại diện (wildcard) của ứng dụng trong OpenShift Container Platform.

##### 2.2.3.6. Cấu hình tham số dnsRecordsType
Trong quá trình cài đặt cụm (cluster), bạn có thể chỉ định tham số `dnsRecordsType` trong tệp `install-config.yaml` để xác định xem các bản ghi DNS cần thiết cho `api`, `api-int` và `ingress` sẽ do dịch vụ DNS nội bộ cung cấp hay từ một nguồn bên ngoài.

> **Quan trọng:** Việc cấu hình tham số `dnsRecordsType` chỉ là tính năng ở giai đoạn "Xem trước công nghệ" (Technology Preview). Các tính năng này không nằm trong phạm vi hỗ trợ theo các thỏa thuận cấp độ dịch vụ (SLA) dành cho môi trường sản xuất của Red Hat và có thể chưa hoàn thiện về mặt chức năng. Red Hat khuyến cáo không nên sử dụng các tính năng này trong môi trường sản xuất. Những tính năng này cung cấp quyền truy cập sớm vào các chức năng sản phẩm sắp ra mắt, cho phép khách hàng thử nghiệm và đóng góp phản hồi trong suốt quá trình phát triển.

Tham số `dnsRecordsType` hỗ trợ các giá trị sau:

* `Internal`: Giá trị mặc định. Khi chọn giá trị này, cơ sở hạ tầng cụm (cluster infrastructure) sẽ tự động tạo và duy trì các bản ghi DNS cần thiết. 
* `External`: Bạn chỉ có thể sử dụng giá trị này nếu đặt tham số `loadBalancer.type` thành `UserManaged`. Cụm sẽ không quản lý các bản ghi DNS; bạn phải tự cấu hình các bản ghi DNS trên một máy chủ DNS bên ngoài.

**Điều kiện tiên quyết**

* Bạn đã tạo các bản ghi DNS, chẳng hạn như `api`, `api-int` hoặc `*.apps`. 
* Bạn đã cấu hình bộ cân bằng tải do người dùng quản lý (user-managed load balancer) cho cụm của mình. 
* Nếu dự định đặt `dnsRecordsType` thành `External` trong tài nguyên tùy chỉnh (CR) `infrastructure.config.openshift.io`, trước tiên bạn phải cấu hình các nút (node) trong cụm để sử dụng máy chủ DNS bên ngoài cụ thể cho việc phân giải DNS.

**Quy trình**

* Trong tệp `install-config.yaml` khi cài đặt cụm, hãy đặt tham số `featureSet` thành `TechPreviewNoUpgrade` và đặt tham số `dnsRecordsType` thành `External`:

```yaml
apiVersion: v1
baseDomain: example.com
metadata:
  name: dev-cluster
# ...
platform:
  baremetal:
# ...
    loadBalancer:
      type: UserManaged
    dnsRecordsType: External
# ...
featureSet: TechPreviewNoUpgrade
pullSecret: '{"auths":{"<local_registry>": {"auth": "<credentials>","email": "you@example.com"}}}'
sshKey: 'ssh-ed25519 AAAA...'
# ...
```

Trong đó:

* `type.UserManaged`: Chỉ định một bộ cân bằng tải (load balancer) bên ngoài cho cụm (cluster) của bạn.
* `dnsRecordsType.External`: Chỉ định rằng cụm không tạo các bản ghi DNS nội bộ cho cơ sở hạ tầng cốt lõi.
* `featureSet.TechPreviewNoUpgrade`: Chỉ định việc kích hoạt các tính năng không mặc định cho cụm của bạn.

##### 2.2.3.7. Các yêu cầu về cân bằng tải đối với hạ tầng do người dùng cung cấp
Trước khi cài đặt OpenShift Container Platform, bạn phải cung cấp hạ tầng cân bằng tải cho API và Ingress ứng dụng. Trong các môi trường thực tế (production), bạn có thể triển khai bộ cân bằng tải cho API và Ingress ứng dụng một cách riêng biệt, nhờ đó có thể mở rộng hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Nếu bạn muốn triển khai các bộ cân bằng tải cho API và Ingress ứng dụng trên một instance chạy hệ điều hành Red Hat Enterprise Linux (RHEL), bạn phải mua gói đăng ký (subscription) RHEL riêng.

Hạ tầng cân bằng tải phải đáp ứng các yêu cầu sau:

* Bộ cân bằng tải API: Cung cấp một điểm cuối (endpoint) chung để người dùng (bao gồm cả con người và máy móc) tương tác với nền tảng và thực hiện cấu hình. Cần thiết lập các điều kiện sau:
  * Chỉ sử dụng cân bằng tải ở Lớp 4 (Layer 4). Phương thức này còn được gọi là Raw TCP (TCP thô) hoặc SSL Passthrough (chuyển tiếp SSL). 
  * Sử dụng thuật toán cân bằng tải không lưu trạng thái (stateless). Các tùy chọn cụ thể sẽ khác nhau tùy thuộc vào giải pháp cân bằng tải mà bạn triển khai.

> **Quan trọng:** Không cấu hình tính năng duy trì phiên (session persistence) cho bộ cân bằng tải API. Việc cấu hình duy trì phiên cho Kubernetes API server có thể gây ra các vấn đề về hiệu năng do lưu lượng truy cập ứng dụng quá mức đổ dồn vào cụm OpenShift Container Platform và Kubernetes API đang chạy bên trong cụm đó.

Cấu hình các cổng (port) sau đây ở cả phía trước (front-end) và phía sau (back-end) của các bộ cân bằng tải API:

| Cổng    | Máy chủ phía sau                                                                                                                                                                                                                                                                                                         | Nội bộ | Bên ngoài | Mô tả                     |
|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|-----------|---------------------------|
| `6443`  | Bootstrap và mặt phẳng điều khiển (control plane). Sau khi máy bootstrap khởi tạo mặt phẳng điều khiển của cụm (cluster), bạn cần loại bỏ máy này khỏi bộ cân bằng tải (load balancer). Bạn phải cấu hình điểm cuối (endpoint) `/readyz` để phục vụ cho việc kiểm tra tình trạng hoạt động của máy chủ API (API server). | X      | X         | Máy chủ API Kubernetes    |
| `22623` | Bootstrap và mặt phẳng điều khiển (control plane). Bạn loại bỏ máy bootstrap khỏi bộ cân bằng tải sau khi máy này khởi tạo mặt phẳng điều khiển của cụm (cluster).                                                                                                                                                       | X      |           | Máy chủ cấu hình thiết bị |

> **Lưu ý:** Bộ cân bằng tải (load balancer) phải được cấu hình sao cho khoảng thời gian từ lúc máy chủ API tắt điểm cuối (endpoint) `/readyz` cho đến khi thực thể máy chủ API đó bị loại bỏ khỏi nhóm (pool) không vượt quá 30 giây. Trong khoảng thời gian sau khi `/readyz` trả về lỗi hoặc chuyển sang trạng thái khỏe mạnh (healthy), điểm cuối đó phải được loại bỏ hoặc thêm vào tương ứng. Việc thực hiện kiểm tra (probe) mỗi 5 hoặc 10 giây, với quy định cần 2 phản hồi thành công liên tiếp để xác định trạng thái khỏe mạnh và 3 phản hồi không thành công liên tiếp để xác định trạng thái không khỏe mạnh, là các thông số đã được kiểm chứng hiệu quả. 

* Bộ cân bằng tải Ingress cho ứng dụng: Cung cấp điểm truy cập (ingress point) cho lưu lượng ứng dụng đi từ bên ngoài cụm (cluster) vào. Cụm OpenShift Container Platform bắt buộc phải có cấu hình hoạt động cho bộ định tuyến Ingress. Hãy thiết lập các điều kiện sau:
  * Chỉ sử dụng cân bằng tải ở Lớp 4 (Layer 4). Cấu hình này còn được gọi là chế độ TCP thô (Raw TCP) hoặc chuyển tiếp SSL (SSL Passthrough). 
  * Khuyến nghị sử dụng cơ chế duy trì phiên (session persistence) dựa trên kết nối hoặc dựa trên phiên, tùy thuộc vào các tùy chọn khả dụng và loại ứng dụng sẽ được lưu trữ trên nền tảng.

> **Mẹo:** Nếu bộ cân bằng tải Ingress cho ứng dụng có thể nhận diện được địa chỉ IP thực của máy khách (client), thì việc kích hoạt cơ chế duy trì phiên dựa trên IP nguồn (source IP-based session persistence) có thể giúp cải thiện hiệu suất cho các ứng dụng sử dụng mã hóa TLS đầu cuối (end-to-end TLS encryption).

Cấu hình các cổng (port) sau đây ở cả phía trước (front-end) và phía sau (back-end) của các bộ cân bằng tải:

**Bảng 2.16. Bộ cân bằng tải Ingress cho ứng dụng**

| Cổng  | Máy chủ phía sau                                                                                                          | Nội bộ | Bên ngoài | Mô tả                    |
|-------|---------------------------------------------------------------------------------------------------------------------------|--------|-----------|--------------------------|
| `443` | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng truy cập HTTPS |
| `80`  | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng truy cập HTTP  |

> **Lưu ý:** Nếu bạn triển khai cụm gồm ba nút (node) mà không có nút tính toán (compute node) nào, các pod của Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển (control plane node). Đối với các cụm gồm ba nút, bạn phải cấu hình bộ cân bằng tải Ingress cho ứng dụng của mình để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển.

###### 2.2.3.7.1. Ví dụ về cấu hình bộ cân bằng tải cho các cụm do người dùng cung cấp
Hãy tham khảo ví dụ về cấu hình bộ cân bằng tải cho API và Ingress ứng dụng để hiểu cách đáp ứng các yêu cầu cân bằng tải đối với các cụm do người dùng cung cấp.

Ví dụ này là tệp cấu hình `/etc/haproxy/haproxy.cfg` dành cho bộ cân bằng tải HAProxy. Ví dụ này không nhằm mục đích đưa ra lời khuyên về việc chọn giải pháp cân bằng tải này thay vì giải pháp khác.

> **Mẹo:** Nếu bạn sử dụng HAProxy làm bộ cân bằng tải, bạn có thể kiểm tra xem tiến trình HAProxy có đang lắng nghe trên các cổng 6443, 22623, 443 và 80 hay không bằng cách chạy lệnh `netstat -nltupe` trên nút (node) chạy HAProxy.

Trong ví dụ này, cùng một bộ cân bằng tải được sử dụng cho cả API Kubernetes và lưu lượng truy cập Ingress ứng dụng. Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và Ingress ứng dụng để có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Nếu bạn sử dụng HAProxy làm bộ cân bằng tải và SELinux đang ở chế độ `enforcing`, bạn phải đảm bảo rằng dịch vụ HAProxy có thể liên kết (bind) với cổng TCP đã cấu hình bằng cách chạy lệnh `setsebool -P haproxy_connect_any=1`.

Ví dụ về cấu hình bộ cân bằng tải cho API và Ingress ứng dụng

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
  option  httpchk GET /readyz HTTP/1.0
  option  log-health-checks
  balance roundrobin
  server bootstrap bootstrap.ocp4.example.com:6443 verify none check check-ssl inter 10s fall 2 rise 3 backup
  server master0 master0.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server master1 master1.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server master2 master2.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
listen machine-config-server-22623
  bind *:22623
  mode tcp
  server bootstrap bootstrap.ocp4.example.com:22623 check inter 1s backup
  server master0 master0.ocp4.example.com:22623 check inter 1s
  server master1 master1.ocp4.example.com:22623 check inter 1s
  server master2 master2.ocp4.example.com:22623 check inter 1s
listen ingress-router-443
  bind *:443
  mode tcp
  balance source
  server compute0 compute0.ocp4.example.com:443 check inter 1s
  server compute1 compute1.ocp4.example.com:443 check inter 1s
listen ingress-router-80
  bind *:80
  mode tcp
  balance source
  server compute0 compute0.ocp4.example.com:80 check inter 1s
  server compute1 compute1.ocp4.example.com:80 check inter 1s
```

Trong đó:

* `listen api-server-6443`: Cổng 6443 xử lý lưu lượng truy cập Kubernetes API và trỏ đến các máy thuộc mặt phẳng điều khiển (control plane). Bạn phải định cấu hình kiểm tra trạng thái (health check) trên cổng này để đảm bảo rằng API server khả dụng trước khi định tuyến lưu lượng truy cập.
* `server bootstrap bootstrap.ocp4.example.com`: Các mục cấu hình bootstrap phải được thiết lập trước khi cài đặt cụm OpenShift Container Platform và phải được loại bỏ sau khi quá trình bootstrap hoàn tất.
* `listen machine-config-server`: Cổng 22623 xử lý lưu lượng truy cập của machine config server và trỏ đến các máy thuộc mặt phẳng điều khiển.
* `listen ingress-router-443`: Cổng 443 xử lý lưu lượng truy cập HTTPS và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán (compute nodes).
* `listen ingress-router-80`: Cổng 80 xử lý lưu lượng truy cập HTTP và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán. 

> **Lưu ý:** Nếu bạn đang triển khai một cụm gồm ba nút (compact three-node cluster) mà không có các nút tính toán riêng biệt, các pod Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển. Trong các triển khai cụm ba nút, bạn phải định cấu hình bộ cân bằng tải Ingress cho ứng dụng để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển.

#### 2.2.4. Tạo đối tượng manifest bao gồm cầu nối br-ex tùy chỉnh
Theo mặc định, OpenShift Container Platform tự động cấu hình cầu nối Open vSwitch (OVS) `br-ex` trên các nút (node) chạy trên phần cứng vật lý (bare-metal). Đối với các yêu cầu mạng nâng cao, bạn có thể ghi đè hành vi mặc định này trên các nền tảng bare-metal. Để thực hiện việc này, hãy tạo một đối tượng `MachineConfig` bao gồm tệp cấu hình NMState.

> **Lưu ý:** Bạn có thể sử dụng Butane để tạo các cấu hình máy (machine config). Để biết thêm thông tin, hãy xem phần "Tạo đối tượng `MachineConfig` bằng cách sử dụng Butane".

Hãy cân nhắc sử dụng cấu hình cầu nối `br-ex` tùy chỉnh cho bất kỳ tác vụ nào sau đây:

* Bạn cần sửa đổi cầu nối `br-ex` sau khi đã cài đặt cụm (cluster). 
* Bạn cần sửa đổi đơn vị truyền tải tối đa (MTU) cho cụm của mình. 
* Bạn cần cập nhật các giá trị DNS. 
* Bạn cần sửa đổi các thuộc tính cho một giao diện liên kết (bond interface) khác. Các ví dụ bao gồm MIImon (Media Independent Interface Monitor), chế độ liên kết (bonding mode) hoặc Chất lượng dịch vụ (QoS). 
* Bạn cần bật Giao thức khám phá lớp liên kết (Link Layer Discovery Protocol - LLDP) để khám phá và khắc phục sự cố kết nối với thiết bị chuyển mạch (switch).

> **Lưu ý:** Hãy sử dụng cầu nối OVS `br-ex` mặc định cho các môi trường tiêu chuẩn.  
> Hãy sử dụng cơ chế cầu nối OVS `br-ex` mặc định cho các môi trường chỉ có một bộ điều khiển giao diện mạng (NIC) với các thiết lập mạng mặc định.

Sau khi bạn cài đặt Red Hat Enterprise Linux CoreOS (RHCOS) và hệ thống khởi động lại, Machine Config Operator sẽ đưa các tệp cấu hình Ignition vào từng nút. Thao tác này đảm bảo rằng mỗi nút đều nhận được cấu hình mạng cho cầu nối `br-ex`. Để tránh xung đột cấu hình, cơ chế cầu nối OVS `br-ex` mặc định sẽ bị vô hiệu hóa.

> **Cảnh báo:** Danh sách các tên giao diện dưới đây đã được dành riêng; bạn không được phép sử dụng các tên này trong cấu hình NMstate:  
> `br-ext`, `br-int`, `br-local`, `br-nexthop`, `br0`, `ext-vxlan`, `ext`, `genev_sys_*`, `int`, `k8s-*`, `ovn-k8s-*`, `patch-br-*`, `tun0`, `vxlan_sys_*`

**Các điều kiện tiên quyết**

* Tùy chọn: Bạn đã cài đặt công cụ dòng lệnh (CLI) `nmstatectl` để xác thực cấu hình NMState của mình.

**Quy trình**

1. Tạo một tệp cấu hình NMState và xác định cấu hình mạng cầu nối (bridge network) `br-ex` tùy chỉnh trong tệp đó:

Ví dụ về cấu hình NMState cho mạng cầu nối `br-ex` tùy chỉnh

```yaml
interfaces:
- name: enp2s0
  type: ethernet
  state: up
  mtu: 9000
  ipv4:
    enabled: false
  ipv6:
    enabled: false
- name: br-ex
  type: ovs-bridge
  state: up
  ipv4:
    enabled: false
    dhcp: false
  ipv6:
    enabled: false
    dhcp: false
  bridge:
    options:
      mcast-snooping-enable: true
    port:
    - name: enp2s0
    - name: br-ex
- name: br-ex
  type: ovs-interface
  state: up
  copy-mac-from: enp2s0
  mtu: 9000
  ipv4:
    enabled: true
    dhcp: true
    auto-route-metric: 48
  ipv6:
    enabled: true
    dhcp: true
    auto-route-metric: 48
# ...
```

Trong đó:

  * `interfaces.name`: Xác định tên của giao diện (interface).
  * `interfaces.type`: Xác định loại giao diện Ethernet.
  * `interfaces.state`: Xác định trạng thái mong muốn cho giao diện sau khi được tạo.
  * `mtu`: Để đảm bảo tính ổn định và hiệu suất mạng, bạn phải khai báo rõ ràng giá trị MTU trong tệp cấu hình (manifest) cho mọi giao diện. Không nên dựa vào cơ chế tự động cấu hình MTU. Giá trị MTU được thiết lập trên một cổng cầu nối (bridge port) hoặc giao diện có gắn thẻ VLAN (VLAN-tagged interface) không được vượt quá kích thước khung hình (frame size) tối đa mà phương tiện truyền dẫn vật lý hỗ trợ. Nếu các giá trị này không khớp, sẽ dẫn đến tình trạng phân mảnh gói tin hoặc mất kết nối.
  * `ipv4.enable`d: Trong ví dụ này, tùy chọn này dùng để vô hiệu hóa IPv4 và IPv6.
  * `port.name`: Xác định card mạng (NIC) của nút (node) mà cầu nối (bridge) sẽ kết nối vào.
  * `auto-route-metric`: Thiết lập tham số này thành 48 để đảm bảo tuyến đường mặc định (default route) của br-ex luôn có mức ưu tiên cao nhất (giá trị metric thấp nhất). Cấu hình này giúp ngăn ngừa xung đột định tuyến với các giao diện khác được dịch vụ NetworkManager tự động cấu hình.

2. Sử dụng lệnh cat để mã hóa nội dung cấu hình NMState sang định dạng base64:

```shell
$ cat <nmstate_configuration>.yml | base64
```

`<nmstate_configuration>`: hãy thay thế `<nmstate_configuration>` bằng tên tệp YAML tài nguyên NMState của bạn.

3. Hãy tạo một tệp manifest MachineConfig và xác định cấu hình mạng cầu nối (bridge network) `br-ex` tùy chỉnh, tương tự như ví dụ dưới đây. Chương trình cài đặt sẽ tự động áp dụng các cập nhật từ đối tượng MachineConfig này vào cụm (cluster) của bạn.

```yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 10-br-ex-worker
spec:
  config:
    ignition:
      version: 3.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/worker-0.yml
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/worker-1.yml
# ...
```

Trong đó:

  * `metadata.name`: Xác định tên của chính sách (policy).
  * `contents.source`: Ghi thông tin đã mã hóa base64 vào đường dẫn được chỉ định.
  * `path`: Đối với mỗi nút (node) trong cụm (cluster) của bạn, hãy chỉ định đường dẫn dựa trên tên máy chủ (hostname) của nút đó cùng với dữ liệu tệp cấu hình Ignition (đã mã hóa base64) tương ứng với loại máy đó. Vai trò "worker" là vai trò mặc định cho các nút trong cụm của bạn. Bạn bắt buộc phải sử dụng phần mở rộng .yml cho các tệp cấu hình. Ví dụ: hãy sử dụng $(hostname -s).yml khi chỉ định đường dẫn theo tên máy chủ rút gọn cho từng nút hoặc tất cả các nút trong tệp manifest MachineConfig. 

Bạn có thể áp dụng một cấu hình chung duy nhất cho tất cả các nút trong cụm bằng cách sử dụng tệp cấu hình `/etc/nmstate/openshift/cluster.yml`. Trong trường hợp này, bạn không cần phải chỉ định đường dẫn theo tên máy chủ rút gọn cho từng nút (ví dụ: `/etc/nmstate/openshift/<node_hostname>.yml`). Ví dụ:

Ví dụ về tệp cấu hình `/etc/nmstate/openshift/cluster.yml`

```yaml
# ...
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/cluster.yml
# ...
```

**Các bước tiếp theo**

* Mở rộng quy mô các nút tính toán (compute node) bằng cách áp dụng đối tượng manifest — bao gồm cấu hình cầu nối `br-ex` tùy chỉnh — cho từng nút tính toán hiện có trong cụm (cluster) của bạn. Để biết thêm thông tin, hãy xem phần "Mở rộng cụm".

##### 2.2.4.1. Mở rộng quy mô từng tập hợp máy (machine set) thành các nút tính toán (compute node)
Để mở rộng quy mô từng tập hợp máy thành các nút tính toán, bạn phải áp dụng cấu hình cầu nối `br-ex` tùy chỉnh cho tất cả các nút tính toán trong cụm OpenShift Container Platform của mình. Sau đó, bạn cần chỉnh sửa tài nguyên tùy chỉnh (CR) `MachineConfig` và sửa đổi các vai trò (roles) của nó.

Ngoài ra, bạn phải tạo một CR `BareMetalHost` để xác định thông tin cho máy vật lý (bare-metal) của mình, chẳng hạn như tên máy chủ (hostname), thông tin xác thực và các tham số cần thiết khác. Sau khi định cấu hình các tài nguyên này, bạn cần mở rộng quy mô các tập hợp máy để chúng có thể áp dụng cấu hình tài nguyên cho từng nút tính toán và khởi động lại các nút đó.

**Điều kiện tiên quyết**

* Bạn đã tạo một đối tượng manifest `MachineConfig` bao gồm cấu hình cầu nối `br-ex` tùy chỉnh.

**Quy trình**

1. Chỉnh sửa CR `MachineConfig` bằng cách nhập lệnh sau:

```shell
$ oc edit mc <machineconfig_custom_resource_name>
```

2. Hãy thêm cấu hình của từng nút tính toán (compute node) vào CR, để CR có thể quản lý vai trò cho mỗi nút tính toán đã xác định trong cụm của bạn.
3. Tạo một đối tượng Secret có tên là `extraworker-secret`, chứa cấu hình IP tĩnh tối thiểu.
4. Áp dụng Secret `extraworker-secret` cho từng nút trong cụm bằng cách nhập lệnh sau. Bước này cho phép mỗi nút tính toán truy cập vào tệp cấu hình Ignition.

```shell
$ oc apply -f ./extraworker-secret.yaml
```
5. Tạo tài nguyên `BareMetalHost` và chỉ định bí mật mạng (network secret) trong tham số `preprovisioningNetworkDataName`:

Ví dụ về tài nguyên `BareMetalHost` có đính kèm bí mật mạng

```yaml
apiVersion: metal3.io/v1alpha1
kind: BareMetalHost
spec:
# ...
  preprovisioningNetworkDataName: ostest-extraworker-0-network-config-secret
# ...
```

6. Để quản lý đối tượng BareMetalHost trong namespace openshift-machine-api của cụm, hãy chuyển sang namespace đó bằng cách nhập lệnh sau:

```shell
$ oc project openshift-machine-api
```

7. Lấy các bộ máy:

```shell
$ oc get machinesets
```

8. Điều chỉnh quy mô từng tập hợp máy bằng cách nhập lệnh sau. Bạn phải chạy lệnh này cho mỗi tập hợp máy.

```shell
$ oc scale machineset <machineset_name> --replicas=<n>
```

trong đó:

  * `<machineset_name>`: Chỉ định tên của tập hợp máy (machine set).
  * `<n>`: Chỉ định số lượng nút tính toán (compute node).

#### 2.2.5. Chuẩn bị cơ sở hạ tầng do người dùng cung cấp
Trước khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải chuẩn bị cơ sở hạ tầng nền tảng.

Phần này cung cấp thông tin chi tiết về các bước tổng quát cần thiết để thiết lập cơ sở hạ tầng cụm (cluster) nhằm chuẩn bị cho việc cài đặt OpenShift Container Platform. Các bước này bao gồm cấu hình mạng IP và kết nối mạng cho các nút (node) trong cụm, mở các cổng (port) cần thiết trên tường lửa, cũng như thiết lập cơ sở hạ tầng DNS và cân bằng tải (load balancing).

Sau khi hoàn tất quá trình chuẩn bị, cơ sở hạ tầng cụm của bạn phải đáp ứng các yêu cầu được nêu trong phần "Các yêu cầu đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp".

**Điều kiện tiên quyết**

* Bạn đã xem qua trang "Các tích hợp đã được kiểm thử cho OpenShift Container Platform 4.x". 
* Bạn đã xem qua các yêu cầu về cơ sở hạ tầng được nêu chi tiết trong phần "Các yêu cầu đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp".

**Quy trình**

1. Nếu bạn sử dụng DHCP để cung cấp cấu hình mạng IP cho các nút trong cụm, hãy cấu hình dịch vụ DHCP của bạn. 
  * Thêm các địa chỉ IP cố định cho các nút vào cấu hình máy chủ DHCP. Trong cấu hình này, hãy ánh xạ địa chỉ MAC của giao diện mạng tương ứng với địa chỉ IP dự định cho mỗi nút. 
  * Khi sử dụng DHCP để cấu hình địa chỉ IP cho các máy trong cụm, các máy này cũng sẽ nhận thông tin máy chủ DNS thông qua DHCP. Hãy xác định địa chỉ máy chủ DNS cố định mà các nút trong cụm sẽ sử dụng thông qua cấu hình máy chủ DHCP của bạn. 

  > **Lưu ý:** Nếu không sử dụng dịch vụ DHCP, bạn phải cung cấp cấu hình mạng IP và địa chỉ máy chủ DNS cho các nút tại thời điểm cài đặt RHCOS. Các thông tin này có thể được truyền dưới dạng tham số khởi động (boot arguments) nếu bạn cài đặt từ tệp ảnh ISO. Xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform" để biết thêm thông tin về việc cấp phát IP tĩnh và các tùy chọn mạng nâng cao. 

  * Xác định tên máy chủ (hostname) cho các nút trong cụm thông qua cấu hình máy chủ DHCP. Xem phần "Thiết lập tên máy chủ cho các nút trong cụm thông qua DHCP" để biết chi tiết về các lưu ý liên quan đến tên máy chủ. 

  > **Lưu ý:** Nếu không sử dụng dịch vụ DHCP, các nút trong cụm sẽ nhận tên máy chủ thông qua truy vấn DNS ngược (reverse DNS lookup).

2. Đảm bảo rằng cơ sở hạ tầng mạng của bạn cung cấp khả năng kết nối mạng cần thiết giữa các thành phần trong cụm (cluster). Hãy xem phần "Yêu cầu về mạng đối với cơ sở hạ tầng do người dùng cung cấp" (Networking requirements for user-provisioned infrastructure) để biết thông tin chi tiết về các yêu cầu này.
3. Cấu hình tường lửa để mở các cổng cần thiết cho việc giao tiếp giữa các thành phần trong cụm OpenShift Container Platform. Hãy xem phần "Yêu cầu về mạng đối với cơ sở hạ tầng do người dùng cung cấp" để biết thông tin chi tiết về các cổng cần thiết.

> **Quan trọng:**  
> Theo mặc định, cổng 1936 luôn mở trong cụm OpenShift Container Platform, vì mỗi nút điều khiển (control plane node) đều cần truy cập vào cổng này.  
> Đối với các thăm dò kiểm tra trạng thái (health check probes) của Ingress, điểm cuối (endpoint) `/healthz/ready` khả dụng trên cổng này.  
> Tránh sử dụng bộ cân bằng tải Ingress (Ingress load balancer) để công khai cổng này, vì việc đó có thể dẫn đến việc lộ các thông tin nhạy cảm — chẳng hạn như số liệu thống kê và các chỉ số (metrics) — liên quan đến các Ingress Controller.

4. Thiết lập cơ sở hạ tầng DNS cần thiết cho cụm (cluster) của bạn. 
  * Cấu hình phân giải tên DNS cho Kubernetes API, wildcard cho ứng dụng, máy bootstrap, các máy thuộc control plane và các máy tính toán (compute machine). 
  * Cấu hình phân giải DNS ngược (reverse DNS) cho Kubernetes API, máy bootstrap, các máy thuộc control plane và các máy tính toán.  
  Xem phần "Các yêu cầu về DNS do người dùng cung cấp" (User-provisioned DNS requirements) để biết thêm thông tin về các yêu cầu DNS của OpenShift Container Platform.
5. Xác thực cấu hình DNS của bạn. 
  * Từ nút cài đặt (installation node), hãy thực hiện truy vấn DNS đối với các tên bản ghi của Kubernetes API, các wildcard route và các nút trong cụm. Xác nhận rằng các địa chỉ IP nhận được trong phản hồi tương ứng với đúng các thành phần đó. 
  * Từ nút cài đặt, hãy thực hiện truy vấn DNS ngược đối với các địa chỉ IP của bộ cân bằng tải (load balancer) và các nút trong cụm. Xác nhận rằng các tên bản ghi nhận được trong phản hồi tương ứng với đúng các thành phần đó.  
  Xem phần "Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp" (Validating DNS resolution for user-provisioned infrastructure) để biết các bước xác thực DNS chi tiết.
6. Cung cấp cơ sở hạ tầng cân bằng tải cần thiết cho API và ứng dụng (ingress load balancing). Xem phần "Các yêu cầu về cân bằng tải cho cơ sở hạ tầng do người dùng cung cấp" (Load balancing requirements for user-provisioned infrastructure) để biết thêm thông tin về các yêu cầu này.

> **Lưu ý:** Một số giải pháp cân bằng tải yêu cầu phải thiết lập xong phân giải tên DNS cho các nút trong cụm trước khi quá trình cân bằng tải được khởi tạo.

#### 2.2.6. Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp
Để ngăn ngừa các lỗi cài đặt liên quan đến mạng và đảm bảo khả năng kết nối giữa các nút (node) trong OpenShift Container Platform, hãy xác thực cấu hình DNS trước khi triển khai trên cơ sở hạ tầng do người dùng cung cấp.
Quan trọng

Các bước xác thực được nêu chi tiết trong phần này phải được thực hiện thành công trước khi bạn cài đặt cụm (cluster) của mình.

**Điều kiện tiên quyết**

* Bạn đã định cấu hình các bản ghi DNS cần thiết cho cơ sở hạ tầng do người dùng cung cấp.

**Quy trình**

1. Từ nút cài đặt (installation node), hãy thực hiện truy vấn DNS đối với tên các bản ghi của Kubernetes API, các tuyến đường đại diện (wildcard routes) và các nút trong cụm. Hãy xác thực rằng các địa chỉ IP nhận được trong phản hồi tương ứng với các thành phần chính xác. 
  * Thực hiện truy vấn đối với tên bản ghi Kubernetes API. Kiểm tra để đảm bảo kết quả trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API:

  ```shell
  $ dig +noall +answer @<nameserver_ip> api.<cluster_name>.<base_domain>
  ```

  Hãy thay thế `<nameserver_ip>` bằng địa chỉ IP của máy chủ tên miền (name server), `<cluster_name>` bằng tên cụm (cluster) của bạn và `<base_domain>` bằng tên miền cơ sở (base domain) của bạn.

  Ví dụ đầu ra

  ```
  api.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  * Thực hiện tra cứu dựa trên tên bản ghi API nội bộ của Kubernetes. Kiểm tra để đảm bảo rằng kết quả trỏ đến địa chỉ IP của bộ cân bằng tải API:

  ```shell
  $ dig +noall +answer @<nameserver_ip> api-int.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  api-int.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  * Hãy kiểm tra một ví dụ về truy vấn DNS wildcard dạng `*.apps.<cluster_name>.<base_domain>`. Tất cả các truy vấn wildcard cho ứng dụng đều phải phân giải về địa chỉ IP của bộ cân bằng tải (load balancer) dành cho ingress của ứng dụng:

  ```shell
  $ dig +noall +answer @<nameserver_ip> random.apps.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  random.apps.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  > **Lưu ý:** Trong các kết quả đầu ra ví dụ, cùng một bộ cân bằng tải (load balancer) được sử dụng cho cả Kubernetes API và lưu lượng truy cập ứng dụng (application ingress). Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và cho lưu lượng truy cập ứng dụng, nhờ đó có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

  Bạn có thể thay thế `random` bằng một giá trị đại diện (wildcard) khác. Chẳng hạn, bạn có thể truy vấn đường dẫn (route) đến bảng điều khiển OpenShift Container Platform:

  ```shell
  $ dig +noall +answer @<nameserver_ip> console-openshift-console.apps.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  console-openshift-console.apps.ocp4.example.com. 604800 IN  A 192.168.1.5
  ```

  * Thực hiện tra cứu dựa trên tên bản ghi DNS bootstrap. Kiểm tra để đảm bảo kết quả trỏ đến địa chỉ IP của nút bootstrap:

  ```shell
  $ dig +noall +answer @<nameserver_ip> bootstrap.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  bootstrap.ocp4.example.com.   604800  IN  A 192.168.1.96
  ```

  * Sử dụng phương pháp này để thực hiện tra cứu dựa trên các tên bản ghi DNS của các nút mặt phẳng điều khiển (control plane) và các nút tính toán (compute nodes). Hãy kiểm tra để đảm bảo rằng các kết quả thu được tương ứng với địa chỉ IP của từng nút.

2. Từ nút cài đặt của bạn, hãy thực hiện truy vấn DNS ngược (reverse DNS lookup) đối với các địa chỉ IP của bộ cân bằng tải (load balancer) và các nút trong cụm (cluster nodes). Hãy xác minh rằng các tên bản ghi (record names) trong phản hồi tương ứng với các thành phần chính xác. 
  * Thực hiện truy vấn ngược đối với địa chỉ IP của bộ cân bằng tải API. Kiểm tra xem phản hồi có bao gồm các tên bản ghi dành cho Kubernetes API và Kubernetes internal API hay không:

  ```shell
  $ dig +noall +answer @<nameserver_ip> -x 192.168.1.5
  ```

  Ví dụ đầu ra

  ```
  5.1.168.192.in-addr.arpa. 604800  IN  PTR api-int.ocp4.example.com.
  5.1.168.192.in-addr.arpa. 604800  IN  PTR api.ocp4.example.com.
  ```

  Trong đó:

    * api-int.ocp4.example.com: Chỉ định tên bản ghi cho API nội bộ của Kubernetes.
    * api.ocp4.example.com: Chỉ định tên bản ghi cho API của Kubernetes. 

  > **Lưu ý:** Không yêu cầu bản ghi PTR đối với wildcard ứng dụng của OpenShift Container Platform. Không cần thực hiện bước xác thực thông qua phân giải DNS ngược (reverse DNS resolution) đối với địa chỉ IP của bộ cân bằng tải ingress (ingress load balancer) cho ứng dụng.

  * Thực hiện tra cứu ngược (reverse lookup) đối với địa chỉ IP của nút khởi động (bootstrap node). Kiểm tra xem kết quả có trỏ đến tên bản ghi DNS của nút khởi động đó hay không:

  ```shell
  $ dig +noall +answer @<nameserver_ip> -x 192.168.1.96
  ```

  Ví dụ đầu ra

  ```
  96.1.168.192.in-addr.arpa. 604800 IN  PTR bootstrap.ocp4.example.com.
  ```

  * Sử dụng phương pháp này để thực hiện tra cứu ngược đối với các địa chỉ IP của các nút điều khiển (control plane) và các nút tính toán (compute nodes). Hãy kiểm tra để đảm bảo rằng các kết quả thu được tương ứng với tên bản ghi DNS của từng nút.

#### 2.2.7. Tạo cặp khóa để truy cập SSH vào các nút cụm (cluster node)
Trong quá trình cài đặt OpenShift Container Platform, bạn có thể cung cấp khóa công khai (public key) SSH cho chương trình cài đặt. Khóa này được chuyển đến các nút Red Hat Enterprise Linux CoreOS (RHCOS) thông qua các tệp cấu hình Ignition và được thêm vào tệp `~/.ssh/authorized_keys` của người dùng `core` trên mỗi nút.

Khóa này được thêm vào danh sách `~/.ssh/authorized_keys` của người dùng `core` trên từng nút, cho phép xác thực mà không cần mật khẩu. Sau khi khóa được chuyển đến các nút, bạn có thể sử dụng cặp khóa này để truy cập SSH vào các nút RHCOS với tư cách là người dùng `core`. Để truy cập các nút thông qua SSH, khóa bí mật (private key) phải được quản lý bởi SSH trên máy cục bộ của bạn.

Nếu bạn muốn truy cập SSH vào các nút trong cụm để thực hiện gỡ lỗi cài đặt hoặc khôi phục sau sự cố, bạn bắt buộc phải cung cấp khóa công khai SSH trong quá trình cài đặt. Lệnh `./openshift-install gather` cũng yêu cầu khóa công khai SSH phải được thiết lập sẵn trên các nút của cụm.

> **Quan trọng:** Đừng bỏ qua bước này trong các môi trường thực tế (production), nơi việc khôi phục sau sự cố và gỡ lỗi là cần thiết.

> **Lưu ý:** Bạn phải sử dụng khóa cục bộ, không phải khóa được cấu hình theo các phương thức dành riêng cho từng nền tảng (platform-specific).

Quy trình

1. Nếu bạn chưa có cặp khóa SSH trên máy cục bộ để sử dụng cho việc xác thực vào các nút trong cụm, hãy tạo một cặp khóa mới. Ví dụ, trên máy tính sử dụng hệ điều hành Linux, hãy chạy lệnh sau:

```shell
$ ssh-keygen -t ed25519 -N '' -f <path>/<file_name>
```

Chỉ định đường dẫn và tên tệp (ví dụ: `~/.ssh/id_ed25519`) cho khóa SSH mới. Nếu bạn đã có sẵn một cặp khóa, hãy đảm bảo rằng khóa công khai (public key) của bạn nằm trong thư mục `~/.ssh`.

> **Lưu ý:** Nếu bạn dự định cài đặt cụm OpenShift Container Platform sử dụng các thư viện mật mã RHEL — vốn đã được gửi tới NIST để đánh giá theo tiêu chuẩn FIPS 140-2/140-3 trên các kiến trúc `x86_64`, `ppc64le` và `s390x` — thì không nên tạo khóa sử dụng thuật toán ed25519. Thay vào đó, hãy tạo khóa sử dụng thuật toán `rsa` hoặc `ecdsa`.

2. Xem khóa SSH công khai:

```shell
$ cat <path>/<file_name>.pub
```

Ví dụ, hãy chạy lệnh sau để xem khóa công khai `~/.ssh/id_ed25519.pub`:

```shell
$ cat ~/.ssh/id_ed25519.pub
```

3. Hãy thêm khóa cá nhân (private key) SSH vào SSH agent của người dùng cục bộ, nếu khóa này chưa được thêm vào. Việc SSH agent quản lý khóa là yêu cầu bắt buộc để thực hiện xác thực SSH không cần mật khẩu vào các nút (node) trong cụm (cluster) của bạn, hoặc nếu bạn muốn sử dụng lệnh `./openshift-install gather`.

> **Lưu ý:** Trên một số bản phân phối, các khóa cá nhân SSH mặc định như `~/.ssh/id_rsa` và `~/.ssh/id_dsa` sẽ được tự động quản lý.

  * Nếu tiến trình ssh-agent chưa chạy cho người dùng cục bộ của bạn, hãy khởi chạy nó dưới dạng tác vụ chạy ngầm:

  ```shell
  $ eval "$(ssh-agent -s)"
  ```

  Ví dụ đầu ra

  ```
  Agent pid 31874
  ```

  > **Lưu ý:** Nếu cụm của bạn đang ở chế độ FIPS, chỉ sử dụng các thuật toán tuân thủ tiêu chuẩn FIPS để tạo khóa SSH. Khóa này phải thuộc loại RSA hoặc ECDSA.

4. Thêm khóa cá nhân SSH của bạn vào `ssh-agent`:

```shell
$ ssh-add <path>/<file_name>
```

Hãy chỉ rõ đường dẫn và tên tệp cho khóa cá nhân SSH của bạn, chẳng hạn như `~/.ssh/id_ed25519`.

Ví dụ đầu ra

```
Identity added: /home/<you>/<path>/<file_name> (<computer_name>)
```

**Các bước tiếp theo**

* Khi cài đặt OpenShift Container Platform, hãy cung cấp khóa công khai SSH cho chương trình cài đặt.

#### 2.2.8. Tải xuống chương trình cài đặt
Trước khi cài đặt OpenShift Container Platform, hãy tải xuống tệp cài đặt trên máy chủ mà bạn sử dụng cho quá trình cài đặt, để đảm bảo có sẵn các thành phần cần thiết cho việc triển khai trong môi trường của bạn.

**Điều kiện tiên quyết**

* Bạn cần có một máy tính chạy hệ điều hành Linux hoặc macOS, với 500 MB dung lượng ổ đĩa cục bộ.

**Quy trình**

1. Truy cập trang [Cluster Type](https://console.redhat.com/openshift/install) trên Red Hat Hybrid Cloud Console. Nếu đã có tài khoản Red Hat, hãy đăng nhập bằng thông tin xác thực của bạn. Nếu chưa có, hãy tạo một tài khoản mới. **Mẹo:** Bạn cũng có thể [tải xuống các tệp nhị phân cho một phiên bản OpenShift Container Platform cụ thể](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/).
2. Chọn nhà cung cấp cơ sở hạ tầng của bạn từ mục "Run it yourself" (Tự vận hành) trên trang này.
3. Chọn hệ điều hành máy chủ và kiến trúc hệ thống từ các menu thả xuống bên dưới mục "OpenShift Installer", sau đó nhấp vào "Download Installer" (Tải xuống trình cài đặt).
4. Đặt tệp đã tải xuống vào thư mục mà bạn muốn dùng để lưu trữ các tệp cấu hình cài đặt.

> **Quan trọng:**
> 
> * Chương trình cài đặt sẽ tạo ra một số tệp trên máy tính mà bạn sử dụng để cài đặt cụm (cluster) của mình. Bạn phải giữ lại chương trình cài đặt cùng các tệp do chương trình này tạo ra sau khi hoàn tất quá trình cài đặt cụm. Bạn sẽ cần đến các tệp này nếu muốn xóa cụm đó. 
> * Việc xóa các tệp do chương trình cài đặt tạo ra sẽ không loại bỏ cụm của bạn, ngay cả khi cụm đó gặp lỗi trong quá trình cài đặt. Để xóa cụm, hãy thực hiện các quy trình gỡ cài đặt OpenShift Container Platform dành riêng cho nhà cung cấp dịch vụ đám mây mà bạn đang sử dụng.

5. Giải nén chương trình cài đặt. Ví dụ, trên máy tính sử dụng hệ điều hành Linux, hãy chạy lệnh sau:

```shell
$ tar -xvf openshift-install-linux.tar.gz
```

6. Hãy tải xuống "pull secret" dùng cho quá trình cài đặt từ [Red Hat OpenShift Cluster Manager](https://console.redhat.com/openshift/install/pull-secret). "Pull secret" này cho phép bạn xác thực với các dịch vụ do những đơn vị được chỉ định cung cấp, bao gồm cả Quay.io – nơi lưu trữ các hình ảnh container (container images) cho các thành phần của OpenShift Container Platform.

#### 2.2.9. Cài đặt OpenShift CLI trên Linux
Để quản lý cụm (cluster) và triển khai các ứng dụng từ dòng lệnh trên Linux, bạn cần cài đặt tệp nhị phân OpenShift CLI (`oc`). Bạn có thể tải xuống OpenShift CLI (`oc`) từ Cổng thông tin khách hàng Red Hat (Red Hat Customer Portal).

> **Quan trọng:** Nếu bạn đã cài đặt phiên bản `oc` cũ hơn, bạn sẽ không thể sử dụng phiên bản đó để thực hiện tất cả các lệnh trong OpenShift Container Platform. Hãy tải xuống và cài đặt phiên bản `oc` mới nhất.

**Quy trình**

1. Truy cập trang Tải xuống OpenShift Container Platform (Download OpenShift Container Platform) trên Cổng thông tin khách hàng Red Hat. 
2. Chọn kiến trúc hệ thống từ danh sách Biến thể sản phẩm (Product Variant). 
3. Chọn phiên bản phù hợp từ danh sách Phiên bản (Version). 
4. Nhấp vào Tải xuống ngay (Download Now) bên cạnh mục OpenShift v4.22 Linux Clients và lưu tệp lại. 
5. Giải nén tệp lưu trữ:

```shell
$ tar xvf <file>
```

6. Đặt tệp nhị phân `oc` vào một thư mục nằm trong biến môi trường `PATH` của bạn. Để kiểm tra biến `PATH`, hãy chạy lệnh sau:

```shell
$ echo $PATH
```

**Xác minh**

Sau khi cài đặt OpenShift CLI, bạn có thể sử dụng công cụ này thông qua lệnh `oc`:

```shell
$ oc <command>
```

#### 2.2.10. Cài đặt OpenShift CLI trên Windows
Để quản lý cụm (cluster) và triển khai các ứng dụng từ dòng lệnh trên Windows, bạn cần cài đặt tệp nhị phân OpenShift CLI (oc). Bạn có thể tải xuống OpenShift CLI (oc) từ Cổng thông tin khách hàng Red Hat (Red Hat Customer Portal).

> **Quan trọng:** Nếu bạn đã cài đặt phiên bản `oc` cũ hơn, bạn sẽ không thể sử dụng phiên bản đó để thực hiện tất cả các lệnh trong OpenShift Container Platform. Hãy tải xuống và cài đặt phiên bản `oc` mới nhất.

**Quy trình**

1. Truy cập trang [Download OpenShift Container Platform](https://access.redhat.com/downloads/content/290) trên Cổng thông tin khách hàng Red Hat. 
2. Chọn phiên bản phù hợp từ danh sách Phiên bản (Version). 
3. Nhấp vào Tải xuống ngay (Download Now) bên cạnh mục OpenShift v4.22 Windows Client và lưu tệp lại. 
4. Giải nén tệp lưu trữ bằng chương trình hỗ trợ định dạng ZIP. 
5. Di chuyển tệp nhị phân `oc` vào một thư mục nằm trong biến môi trường `PATH` của bạn. 
6. Để kiểm tra biến môi trường `PATH`, hãy mở Command Prompt và chạy lệnh sau:

```shell
C:\> path
```

**Xác minh**

Sau khi cài đặt OpenShift CLI, bạn có thể sử dụng công cụ này thông qua lệnh `oc`:

```shell
C:\> oc <command>
```

#### 2.2.11. Cài đặt OpenShift CLI trên macOS
Để quản lý cụm (cluster) và triển khai các ứng dụng từ dòng lệnh trên macOS, bạn cần cài đặt tệp nhị phân OpenShift CLI (oc). Bạn có thể tải xuống OpenShift CLI (oc) từ Cổng thông tin khách hàng Red Hat (Red Hat Customer Portal).

> **Quan trọng:** Nếu bạn đã cài đặt phiên bản oc cũ hơn, bạn sẽ không thể sử dụng phiên bản đó để thực thi tất cả các lệnh trong OpenShift Container Platform. Hãy tải xuống và cài đặt phiên bản oc mới nhất.

**Quy trình**

1. Truy cập trang [Download OpenShift Container Platform](https://access.redhat.com/downloads/content/290) trên Cổng thông tin khách hàng Red Hat. 
2.  Chọn kiến trúc hệ thống từ danh sách **Product Variant**. 
3. Chọn phiên bản phù hợp từ danh sách **Version**. 
4. Nhấp vào **Download Now** bên cạnh mục **OpenShift v4.22 macOS Clients** và lưu tệp lại. **Lưu ý:** Đối với macOS arm64, hãy chọn mục **OpenShift v4.22 macOS arm64 Client**. 
5. Giải nén tệp lưu trữ. 
6. Di chuyển tệp nhị phân `oc` vào một thư mục nằm trong biến môi trường `PATH` của bạn. Để kiểm tra biến môi trường `PATH`, hãy mở terminal và chạy lệnh sau:

```shell
$ echo $PATH
```

**Xác minh**

Hãy xác minh quá trình cài đặt của bạn bằng cách sử dụng lệnh oc:

```shell
$ oc <command>
```

#### 2.2.12. Tạo thủ công tệp cấu hình cài đặt
Việc cài đặt cụm (cluster) yêu cầu bạn phải tạo thủ công tệp cấu hình cài đặt.

**Điều kiện tiên quyết**

* Bạn đã có khóa công khai SSH (SSH public key) trên máy cục bộ để sử dụng với chương trình cài đặt. Bạn có thể dùng khóa này để xác thực SSH vào các nút (node) trong cụm nhằm mục đích gỡ lỗi và khôi phục sau sự cố. 
* Bạn đã lấy được chương trình cài đặt OpenShift Container Platform và "pull secret" (bí mật kéo hình ảnh) cho cụm của mình.

**Quy trình**

1. Tạo một thư mục cài đặt để lưu trữ các tài nguyên cần thiết cho quá trình cài đặt:

```shell
$ mkdir <installation_directory>
```

> **Quan trọng:** Bạn phải tạo một thư mục mới. Một số thành phần cài đặt, chẳng hạn như các chứng chỉ X.509 khởi tạo (bootstrap), có thời hạn hiệu lực ngắn; do đó, bạn không được sử dụng lại thư mục cài đặt cũ. Nếu muốn sử dụng lại các tệp tin riêng lẻ từ một quá trình cài đặt cụm khác, bạn có thể sao chép chúng vào thư mục của mình. Tuy nhiên, tên của các tệp tin cài đặt có thể thay đổi giữa các phiên bản phát hành. Hãy thận trọng khi sao chép các tệp tin cài đặt từ một phiên bản OpenShift Container Platform cũ hơn.

2. Hãy tùy chỉnh tệp mẫu `install-config.yaml` được cung cấp và lưu tệp đó vào `<installation_directory>`.

> **Lưu ý:** Bạn bắt buộc phải đặt tên cho tệp cấu hình này là `install-config.yaml`.

3. Hãy sao lưu tệp `install-config.yaml` để có thể sử dụng tệp này cho việc cài đặt nhiều cụm (cluster).

> **Quan trọng:** Hãy sao lưu tệp `install-config.yaml` ngay bây giờ, vì tệp này sẽ được sử dụng trong bước tiếp theo của quy trình cài đặt.

##### 2.2.12.1. Tệp install-config.yaml mẫu cho môi trường bare metal
Bạn có thể tùy chỉnh tệp `install-config.yaml` để chỉ định thêm thông tin chi tiết về nền tảng cụm OpenShift Container Platform của mình hoặc sửa đổi các giá trị của các tham số bắt buộc.

```yaml
apiVersion: v1
baseDomain: example.com
compute:
- hyperthreading: Enabled
  name: worker
  replicas: 0
controlPlane:
  hyperthreading: Enabled
  name: master
  replicas: 3
metadata:
  name: test
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  networkType: OVNKubernetes
  machineNetwork:
  - cidr: 192.168.0.0/16
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
fips: false
pullSecret: '{"auths": ...}'
sshKey: 'ssh-ed25519 AAAA...'
```

**Trong đó:**

* `baseDomain`: Xác định tên miền cơ sở (base domain) của cụm (cluster). Tất cả các bản ghi DNS phải là tên miền phụ (sub-domain) của tên miền cơ sở này và phải bao gồm tên của cụm.
* `compute`: Xác định cấu hình cho các nút tính toán (compute node), được biểu diễn dưới dạng một chuỗi các ánh xạ (mappings). Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `compute` phải bắt đầu bằng một dấu gạch ngang (`-`).
* `controlPlane`: Xác định cấu hình cho các nút điều khiển (control plane node), được biểu diễn dưới dạng một ánh xạ duy nhất. Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `controlPlane` không được bắt đầu bằng dấu gạch ngang. Chỉ một nhóm (pool) nút điều khiển được sử dụng.
* `hyperthreading`: Xác định việc bật hay tắt tính năng đa luồng đồng thời (Simultaneous Multithreading - SMT), hay còn gọi là hyperthreading. Theo mặc định, SMT được bật để tăng hiệu suất cho các lõi (core) trên máy chủ của bạn. Bạn có thể tắt tính năng này bằng cách đặt giá trị tham số thành `Disabled`. Nếu tắt SMT, bạn phải thực hiện tắt trên tất cả các máy chủ trong cụm, bao gồm cả các nút điều khiển (control plane) và các nút tính toán (compute).

> **Lưu ý:** Tính năng đa luồng đồng thời (SMT) được bật theo mặc định. Nếu SMT chưa được bật trong cài đặt BIOS, thì tham số `hyperthreading` sẽ không có tác dụng.

> **Quan trọng:** Nếu bạn tắt hyperthreading (dù là trong BIOS hay trong tệp `install-config.yaml`), hãy đảm bảo rằng kế hoạch phân bổ tài nguyên (capacity planning) của bạn đã tính đến việc hiệu suất máy chủ sẽ giảm đáng kể.

* `compute.replicas`: Xác định số lượng máy tính toán (compute machine) mà cụm (cluster) sẽ tạo và quản lý thay cho bạn trong các quy trình cài đặt do trình cài đặt tự động cung cấp (installer-provisioned installations). Bạn phải đặt giá trị này bằng 0 khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng tự cung cấp (user-provisioned infrastructure). Ngoài ra, đối với các quy trình cài đặt do người dùng tự cung cấp, bạn phải triển khai thủ công các máy tính toán trước khi hoàn tất việc cài đặt cụm.

> **Lưu ý:** Nếu bạn đang cài đặt một cụm gồm ba nút (three-node cluster), đừng triển khai thêm bất kỳ máy tính toán nào khi thực hiện cài đặt các máy Red Hat Enterprise Linux CoreOS (RHCOS).

* `controlPlane.replicas`: Xác định số lượng máy thuộc mặt phẳng điều khiển (control plane machine) mà bạn thêm vào cụm. Vì cụm sử dụng các giá trị này để xác định số lượng điểm cuối (endpoint) etcd trong cụm, nên giá trị này phải khớp với số lượng máy thuộc mặt phẳng điều khiển mà bạn triển khai.
* `metadata.name`: Xác định tên của cụm, trùng với tên mà bạn đã chỉ định trong các bản ghi DNS của mình.
* `networking.clusterNetwork.cidr`: Xác định một khối địa chỉ IP dùng để cấp phát địa chỉ IP cho các pod. Khối này không được trùng lặp với các mạng vật lý hiện có. Các địa chỉ IP này được sử dụng cho mạng của pod. Nếu bạn cần truy cập vào các pod từ một mạng bên ngoài, bạn phải cấu hình các bộ cân bằng tải (load balancer) và bộ định tuyến (router) để quản lý lưu lượng truy cập.

> **Lưu ý:** Dải địa chỉ CIDR thuộc Lớp E (Class E) được dành riêng cho mục đích sử dụng trong tương lai. Để sử dụng dải CIDR Lớp E, bạn phải đảm bảo rằng môi trường mạng của mình chấp nhận các địa chỉ IP nằm trong dải này.

* `networking.cidr.hostPrefix`: Xác định độ dài tiền tố mạng con (subnet prefix length) được gán cho mỗi nút (node). Ví dụ: nếu `hostPrefix` được đặt là `23`, thì mỗi nút sẽ được gán một mạng con `/23` từ dải CIDR đã cho, cho phép tạo ra `510 (2^(32 - 23) - 2)` địa chỉ IP cho các pod. Nếu bạn cần cho phép truy cập vào các nút từ một mạng bên ngoài, hãy cấu hình bộ cân bằng tải (load balancer) và bộ định tuyến (router) để quản lý lưu lượng truy cập.
* `networking.networkType`: Xác định plugin mạng cụm (cluster network plugin) cần cài đặt. Giá trị mặc định `OVNKubernetes` là giá trị duy nhất được hỗ trợ.
* `networking.serviceNetwork`: Xác định nhóm địa chỉ IP (IP address pool) được sử dụng cho các địa chỉ IP của dịch vụ (service). Bạn chỉ có thể nhập một nhóm địa chỉ IP duy nhất. Khối địa chỉ này không được trùng lặp với các mạng vật lý hiện có. Nếu bạn cần truy cập các dịch vụ từ một mạng bên ngoài, hãy cấu hình bộ cân bằng tải và bộ định tuyến để quản lý lưu lượng truy cập.
* `platform`: Xác định nền tảng (platform). Bạn bắt buộc phải đặt giá trị `platform` là `none`. Bạn không thể cung cấp thêm các biến cấu hình nền tảng nào khác.

> **Quan trọng:** Các cụm được cài đặt với loại nền tảng là `none` sẽ không thể sử dụng một số tính năng, chẳng hạn như quản lý các máy tính toán (compute machine) thông qua Machine API. Hạn chế này vẫn áp dụng ngay cả khi các máy tính toán được kết nối với cụm nằm trên một nền tảng vốn hỗ trợ tính năng đó. Tham số này không thể thay đổi sau khi đã hoàn tất cài đặt.

* `fips`: Xác định việc bật hoặc tắt chế độ FIPS. Theo mặc định, chế độ FIPS không được bật. Nếu chế độ FIPS được bật, các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) – nền tảng mà OpenShift Container Platform vận hành trên đó – sẽ bỏ qua bộ mật mã mặc định của Kubernetes và thay vào đó sử dụng các mô-đun mật mã được cung cấp kèm theo RHCOS.

> **Quan trọng:** Để bật chế độ FIPS cho cụm (cluster) của bạn, bạn phải chạy chương trình cài đặt từ một máy tính sử dụng hệ điều hành Red Hat Enterprise Linux (RHEL) đã được cấu hình để hoạt động ở chế độ FIPS. Khi chạy Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã của RHEL – những thư viện đã được gửi tới NIST để thực hiện quy trình xác thực FIPS 140-2/140-3 (áp dụng cho các kiến trúc `x86_64`, `ppc64le` và `s390x`).

* `pullSecret`: Chỉ định ["pull secret" (bí mật truy xuất) từ Red Hat OpenShift Cluster Manager](https://console.redhat.com/openshift/install/pull-secret). "Pull secret" này cho phép bạn xác thực với các dịch vụ được cung cấp bởi các đơn vị liên quan, bao gồm cả Quay.io – nơi lưu trữ các hình ảnh container (container images) cho các thành phần của OpenShift Container Platform.
* `sshKey`: Chỉ định khóa công khai SSH (SSH public key) dành cho người dùng cốt lõi (core user) trong hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS).

> **Lưu ý:** Đối với các cụm OpenShift Container Platform trong môi trường sản xuất (production) mà bạn dự định thực hiện các thao tác gỡ lỗi cài đặt hoặc khôi phục sau thảm họa (disaster recovery), hãy chỉ định một khóa SSH mà tiến trình `ssh-agent` của bạn đang sử dụng.

#### 2.2.13. Các giai đoạn cấu hình mạng
Bạn có thể tùy chỉnh cấu hình plugin mạng của OpenShift Container Platform — chẳng hạn như CIDR của mạng cụm (cluster network) và các dải địa chỉ cho mạng dịch vụ (service network) — trong hai giai đoạn trước khi cài đặt, nhằm tích hợp nền tảng này vào môi trường mạng hiện có của bạn.

##### Giai đoạn 1
Bạn có thể tùy chỉnh các trường liên quan đến mạng sau đây trong tệp `install-config.yaml` trước khi tạo các tệp manifest:

* `networking.networkType`
* `networking.clusterNetwork`
* `networking.serviceNetwork`
* `networking.machineNetwork`
* `nodeNetworking`

Để biết thêm thông tin, hãy xem phần "Các tham số cấu hình cài đặt" (Installation configuration parameters). 

> **Lưu ý:** Hãy thiết lập `networking.machineNetwork` sao cho khớp với dải địa chỉ theo chuẩn CIDR (Classless Inter-Domain Routing) chứa mạng con (subnet) mà bạn muốn sử dụng. 

> **Quan trọng:** Dải CIDR `172.17.0.0/16` đã được libVirt dành riêng cho mục đích sử dụng riêng. Bạn không được phép sử dụng bất kỳ dải CIDR nào khác bị trùng lặp với dải `172.17.0.0/16` cho các mạng trong cụm của mình.

##### Giai đoạn 2
Sau khi tạo các tệp manifest bằng cách chạy lệnh `openshift-install create manifests`, bạn có thể xác định một tệp manifest tùy chỉnh cho Cluster Network Operator, trong đó chỉ bao gồm các trường mà bạn muốn sửa đổi. Bạn có thể sử dụng tệp manifest này để thiết lập cấu hình mạng nâng cao.

Trong giai đoạn 2, bạn không thể ghi đè lên các giá trị đã xác định ở giai đoạn 1 trong tệp `install-config.yaml`. Tuy nhiên, bạn có thể tùy chỉnh plugin mạng trong giai đoạn 2.

#### 2.2.14. Chỉ định cấu hình mạng nâng cao
Bạn có thể sử dụng cấu hình mạng nâng cao cho plugin mạng của OpenShift Container Platform để tích hợp cụm (cluster) của mình vào môi trường mạng hiện có.

Bạn chỉ có thể chỉ định cấu hình mạng nâng cao trước khi cài đặt cụm.

> **Quan trọng:** Việc tùy chỉnh cấu hình mạng bằng cách sửa đổi các tệp manifest (tệp mô tả cấu hình) của OpenShift Container Platform – vốn được tạo ra bởi chương trình cài đặt – là không được hỗ trợ. Tuy nhiên, việc áp dụng một tệp manifest do chính bạn tạo ra (như trong quy trình dưới đây) thì được hỗ trợ.

**Điều kiện tiên quyết**

* Bạn đã tạo tệp `install-config.yaml` và hoàn tất mọi sửa đổi cần thiết trên tệp này.

**Quy trình**

1. Di chuyển đến thư mục chứa chương trình cài đặt và tạo các tệp manifest:

```shell
$ ./openshift-install create manifests --dir <installation_directory>
```

trong đó `<installation_directory>` chỉ định tên của thư mục chứa tệp `install-config.yaml` cho cụm của bạn.

2. Tạo một tệp manifest mẫu cho cấu hình mạng nâng cao, có tên là cluster-network-03-config.yml, nằm trong thư mục `<installation_directory>/manifests/`:

```yaml
apiVersion: operator.openshift.io/v1
kind: Network
metadata:
  name: cluster
spec:
```

3. Hãy chỉ định cấu hình mạng nâng cao cho cụm của bạn trong tệp `cluster-network-03-config.yml`, chẳng hạn như trong ví dụ dưới đây:

Ví dụ sau đây kích hoạt IPsec cho nhà cung cấp mạng OVN-Kubernetes:

```yaml
apiVersion: operator.openshift.io/v1
kind: Network
metadata:
  name: cluster
spec:
  defaultNetwork:
    ovnKubernetesConfig:
      ipsecConfig:
        mode: Full
```

4. Tùy chọn: Hãy sao lưu tệp `manifests/cluster-network-03-config.yml`. Chương trình cài đặt sẽ sử dụng các tệp trong thư mục `manifests/` khi bạn tạo các tệp cấu hình Ignition.
5. Xóa các tệp manifest Kubernetes định nghĩa các máy thuộc mặt phẳng điều khiển (control plane) và các MachineSet tính toán (compute MachineSets):

```shell
$ rm -f openshift/99_openshift-cluster-api_master-machines-*.yaml \
  openshift/99_openshift-cluster-api_worker-machineset-*.yaml
```

Vì bạn tự tạo và quản lý các tài nguyên này, nên bạn không cần phải khởi tạo chúng. 

  * Bạn có thể giữ lại các tệp MachineSet để tạo các máy tính toán (compute machine) thông qua việc sử dụng API máy (machine API), nhưng bạn phải cập nhật các tham chiếu đến chúng sao cho phù hợp với môi trường của mình.

#### 2.2.15. Cấu hình Cluster Network Operator
Để quản lý mạng cụm (cluster networking), hãy định cấu hình tài nguyên tùy chỉnh (Custom Resource - CR) Network của Cluster Network Operator (CNO) với tên là `cluster`, nhằm đảm bảo cụm sử dụng các dải IP và thiết lập plugin mạng phù hợp, từ đó duy trì kết nối ổn định cho các pod và dịch vụ.

Một số thiết lập và trường thông tin được kế thừa tại thời điểm cài đặt hoặc thông qua plugin mặc định (`defaultNetwork.type`), cụ thể là OVN-Kubernetes.

Trong quá trình cài đặt cụm, cấu hình CNO sẽ kế thừa các trường sau từ Network API thuộc nhóm API `Network.config.openshift.io`:

* `clusterNetwork`: Các nhóm (pool) địa chỉ IP dùng để cấp phát địa chỉ IP cho các pod.
* `serviceNetwork`: Nhóm địa chỉ IP dành cho các dịch vụ (service).
* `defaultNetwork.type`: Plugin mạng của cụm. OVN-Kubernetes là plugin duy nhất được hỗ trợ trong quá trình cài đặt.

Bạn có thể chỉ định cấu hình plugin mạng cho cụm của mình bằng cách thiết lập các trường trong đối tượng `defaultNetwork` nằm bên trong đối tượng CNO có tên là `cluster`.

##### 2.2.15.1. Đối tượng cấu hình Cluster Network Operator
Các trường dành cho Cluster Network Operator (CNO) được mô tả trong bảng dưới đây:

**Bảng 2.17. Đối tượng cấu hình Cluster Network Operator**

| Trường                                         | Kiểu      | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|------------------------------------------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `metadata.name`                                | chuỗi     | Tên của đối tượng CNO. Tên này luôn là `cluster`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `spec.clusterNetwork`                          | mảng      | Một danh sách xác định các khối địa chỉ IP dùng để cấp phát địa chỉ IP cho các pod, cùng với độ dài tiền tố mạng con (subnet prefix length) được gán cho từng nút (node) trong cụm. Nếu sử dụng mạng dual-stack (hỗ trợ cả hai giao thức), bạn cần chỉ định các họ địa chỉ IPv4 và IPv6. Ví dụ: `spec: clusterNetwork: - cidr: 10.128.0.0/19 hostPrefix: 23 - cidr: fd01::/48 hostPrefix: 64` Nếu bạn cài đặt cụm trên AWS với cấu hình mạng dual-stack, thứ tự các địa chỉ phải khớp với cấu hình dual-stack mà bạn đã chọn. Chẳng hạn, nếu bạn đã chọn cấu hình `DualStackIPv4Primary`, hãy liệt kê địa chỉ IPv4 trước. |
| `spec.serviceNetwork`                          | mảng      | Một khối địa chỉ IP dành cho các dịch vụ. Nếu sử dụng mạng dual-stack (ngăn xếp kép), hãy chỉ định cả họ địa chỉ IPv4 và IPv6. Ví dụ: `spec: serviceNetwork: - 172.30.0.0/14 - fd02::/112` Nếu bạn cài đặt cụm (cluster) trên AWS với mạng dual-stack, thứ tự các địa chỉ phải khớp với cấu hình dual-stack mà bạn đã chọn. Chẳng hạn, nếu bạn đã chỉ định `DualStackIPv4Primary`, hãy liệt kê địa chỉ IPv4 trước. Bạn chỉ có thể tùy chỉnh trường này trong tệp `install-config.yaml` trước khi tạo các tệp manifest. Sau khi đã tạo tệp manifest, giá trị này sẽ ở trạng thái chỉ đọc (read-only).                      |
| `spec.defaultNetwork`                          | đối tượng | Cấu hình plugin mạng cho mạng cụm.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `spec.additionalRoutingCapabilities.providers` | mảng      | Thiết lập này cho phép sử dụng một nhà cung cấp định tuyến động. Nhà cung cấp khả năng định tuyến FRR là thành phần bắt buộc đối với tính năng quảng bá tuyến đường (route advertisement). Giá trị duy nhất được hỗ trợ là `FRR`. FRR: Nhà cung cấp định tuyến FRR. `spec: additionalRoutingCapabilities: providers: - FRR`                                                                                                                                                                                                                                                                                               |

> **Quan trọng:** Đối với một cụm (cluster) cần triển khai các đối tượng trên nhiều mạng khác nhau, hãy đảm bảo rằng bạn chỉ định cùng một giá trị cho tham số `clusterNetwork.hostPrefix` đối với mỗi loại mạng được định nghĩa trong tệp `install-config.yaml`. Việc thiết lập các giá trị khác nhau cho tham số `clusterNetwork.hostPrefix` có thể gây ảnh hưởng đến plugin mạng OVN-Kubernetes, khiến plugin này không thể định tuyến lưu lượng truy cập giữa các đối tượng trên những nút (node) khác nhau một cách hiệu quả.

##### 2.2.15.2. Cấu hình đối tượng defaultNetwork
Các giá trị cho đối tượng defaultNetwork được xác định trong bảng sau:

**Bảng 2.18. Đối tượng defaultNetwork**

| Trường                | Kiểu      | Mô tả                                                                                                                                                                                                                                                            |
|-----------------------|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `type`                | chuỗi     | `OVNKubernetes`. Plugin mạng của Red Hat OpenShift Networking được chọn trong quá trình cài đặt. Giá trị này không thể thay đổi sau khi cụm (cluster) đã được cài đặt. **Lưu ý:** OpenShift Container Platform sử dụng plugin mạng OVN-Kubernetes theo mặc định. |
| `ovnKubernetesConfig` | đối tượng | Đối tượng này chỉ hợp lệ đối với plugin mạng OVN-Kubernetes.                                                                                                                                                                                                     |

##### 2.2.15.3. Cấu hình cho plugin mạng OVN-Kubernetes
Bảng dưới đây mô tả các trường cấu hình cho plugin mạng OVN-Kubernetes:

**Bảng 2.19. Đối tượng ovnKubernetesConfig**

| Trường                | Kiểu      | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|-----------------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `mtu`                 | số nguyên | Đây là đơn vị truyền tải tối đa (MTU) cho mạng overlay Geneve (Generic Network Virtualization Encapsulation). Giá trị này được tự động phát hiện dựa trên MTU của giao diện mạng chính. Thông thường, bạn không cần phải thay đổi giá trị MTU đã được tự động phát hiện này. Nếu giá trị được tự động phát hiện không đúng như mong đợi, hãy kiểm tra xem MTU trên giao diện mạng chính của các nút (node) đã chính xác hay chưa. Bạn không thể sử dụng tùy chọn này để thay đổi giá trị MTU của giao diện mạng chính trên các nút. Nếu cụm (cluster) của bạn yêu cầu các giá trị MTU khác nhau cho các nút khác nhau, bạn phải đặt giá trị này thấp hơn `100` đơn vị so với giá trị MTU thấp nhất trong cụm. Ví dụ: nếu một số nút trong cụm có MTU là `9001` và một số nút khác có MTU là `1500`, bạn phải đặt giá trị này là `1400`.                                                                                       |
| `genevePort`          | số nguyên | Cổng được sử dụng cho tất cả các gói tin Geneve. Giá trị mặc định là `6081`. Giá trị này không thể thay đổi sau khi đã cài đặt cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `ipsecConfig`         | đối tượng | Chỉ định một đối tượng cấu hình để tùy chỉnh cấu hình IPsec.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `ipv4`                | đối tượng | Chỉ định một đối tượng cấu hình cho các thiết lập IPv4.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `ipv6`                | đối tượng | Chỉ định một đối tượng cấu hình cho các thiết lập IPv6.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `policyAuditConfig`   | đối tượng | Chỉ định một đối tượng cấu hình để tùy chỉnh việc ghi nhật ký kiểm tra chính sách mạng. Nếu không được thiết lập, các cài đặt nhật ký kiểm tra mặc định sẽ được sử dụng.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `routeAdvertisements` | chuỗi     | Xác định xem có quảng bá các tuyến đường mạng cụm (cluster network routes) hay không. Giá trị mặc định là `Disabled`. `Enabled`: Nhập các tuyến đường vào mạng cụm và quảng bá các tuyến đường mạng cụm theo cấu hình trong các đối tượng `RouteAdvertisements`. `Disabled`: Không nhập các tuyến đường vào mạng cụm, cũng không quảng bá các tuyến đường mạng cụm.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `gatewayConfig`       | đối tượng | Tùy chọn: Chỉ định một đối tượng cấu hình để tùy chỉnh cách lưu lượng truy cập đi (egress traffic) được gửi đến cổng kết nối nút (node gateway). Các giá trị hợp lệ bao gồm Shared (chia sẻ) và Local (cục bộ). Giá trị mặc định là Shared. Ở thiết lập mặc định, Open vSwitch (OVS) sẽ chuyển lưu lượng truy cập trực tiếp đến giao diện IP của nút. Nếu đang sử dụng tính năng giảm tải phần cứng (hardware offloading), Red Hat khuyến nghị nên sử dụng chế độ cổng kết nối Shared mặc định để bỏ qua mặt phẳng định tuyến (routing plane) của máy chủ. Với thiết lập Local, lưu lượng truy cập sẽ đi qua mạng của máy chủ; do đó, nó sẽ chịu sự chi phối của bảng định tuyến trên máy chủ đó. Lưu ý: Trong quá trình chuyển đổi lưu lượng truy cập đi, các khối lượng công việc (workload) và lưu lượng dịch vụ có thể bị gián đoạn cho đến khi Cluster Network Operator (CNO) hoàn tất việc triển khai các thay đổi này. |

**Bảng 2.20. Đối tượng ovnKubernetesConfig.ipv4**

| Trường                        | Kiểu  | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------|-------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `internalTransitSwitchSubnet` | chuỗi | Nếu cơ sở hạ tầng mạng hiện tại của bạn trùng với dải mạng con IPv4 `100.88.0.0/16`, bạn có thể chỉ định một dải địa chỉ IP khác để OVN-Kubernetes sử dụng nội bộ. Đây là dải mạng con dành cho bộ chuyển mạch trung chuyển phân tán (distributed transit switch) – thành phần cho phép lưu lượng truy cập theo chiều ngang (east-west traffic). Dải mạng con này không được phép trùng với bất kỳ dải mạng con nào khác đang được OVN-Kubernetes hoặc chính máy chủ (host) sử dụng. Ngoài ra, dải mạng con này phải đủ lớn để cung cấp ít nhất một địa chỉ IP cho mỗi nút (node) trong cụm (cluster) của bạn. Giá trị mặc định là `100.88.0.0/16`.        |
| `internalJoinSubnet`          | chuỗi | Nếu cơ sở hạ tầng mạng hiện tại của bạn trùng với dải mạng con IPv4 `100.64.0.0/16`, bạn có thể chỉ định một dải địa chỉ IP khác để OVN-Kubernetes sử dụng cho mục đích nội bộ. Bạn phải đảm bảo rằng dải địa chỉ IP này không trùng với bất kỳ dải mạng con nào khác đang được sử dụng trong quá trình cài đặt OpenShift Container Platform của bạn. Dải địa chỉ IP này phải có quy mô lớn hơn số lượng nút (node) tối đa có thể thêm vào cụm (cluster). Ví dụ: nếu giá trị `clusterNetwork.cidr` là `10.128.0.0/14` và giá trị `clusterNetwork.hostPrefix` là `/23`, thì số lượng nút tối đa sẽ là `2^(23-14)=512`. Giá trị mặc định là `100.64.0.0/16`. |

**Bảng 2.21. Đối tượng ovnKubernetesConfig.ipv6**

| Trường                        | Kiểu  | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|-------------------------------|-------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `internalTransitSwitchSubnet` | chuỗi | Nếu cơ sở hạ tầng mạng hiện tại của bạn trùng với dải mạng con (subnet) IPv6 `fd97::/64`, bạn có thể chỉ định một dải địa chỉ IP khác để OVN-Kubernetes sử dụng cho mục đích nội bộ. Đây là dải mạng con dành cho thiết bị chuyển mạch trung chuyển phân tán (distributed transit switch) – thành phần cho phép lưu lượng truy cập theo chiều ngang (east-west traffic). Dải mạng con này không được phép trùng với bất kỳ dải mạng con nào khác đang được OVN-Kubernetes hoặc chính máy chủ (host) sử dụng. Ngoài ra, dải mạng này phải đủ lớn để cung cấp ít nhất một địa chỉ IP cho mỗi nút (node) trong cụm (cluster) của bạn. Giá trị mặc định là `fd97::/64`. |
| `internalJoinSubnet`          | chuỗi | Nếu cơ sở hạ tầng mạng hiện tại của bạn trùng với mạng con IPv6 `fd98::/64`, bạn có thể chỉ định một dải địa chỉ IP khác để OVN-Kubernetes sử dụng cho mục đích nội bộ. Bạn phải đảm bảo rằng dải địa chỉ IP này không trùng với bất kỳ mạng con nào khác đang được sử dụng trong quá trình cài đặt OpenShift Container Platform của bạn. Dải địa chỉ IP này phải có quy mô lớn hơn số lượng nút (node) tối đa có thể được thêm vào cụm (cluster). Giá trị mặc định là `fd98::/64`.                                                                                                                                                                                 |

**Bảng 2.22. Đối tượng policyAuditConfig**

| Trường           | Kiểu      | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|------------------|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `rateLimit`      | số nguyên | Số lượng thông điệp tối đa được tạo ra mỗi giây trên mỗi nút. Giá trị mặc định là `20` thông điệp mỗi giây.                                                                                                                                                                                                                                                                                                                                          |
| `maxFileSize`    | số nguyên | Kích thước tối đa cho nhật ký kiểm toán (tính bằng byte). Giá trị mặc định là `50000000`, tương đương 50 MB.                                                                                                                                                                                                                                                                                                                                         |
| `maxLogFiles`    | số nguyên | Số lượng tệp nhật ký tối đa được lưu giữ.                                                                                                                                                                                                                                                                                                                                                                                                            |
| `destination`    | chuỗi     | Một trong các đích đến bổ sung cho nhật ký kiểm toán (audit log) dưới đây: `libc`: Hàm `syslog()` từ thư viện `libc` của tiến trình `journald` trên máy chủ. `udp:<host>:<port>`: Một máy chủ syslog. Hãy thay thế `<host>:<port>` bằng tên máy chủ (hoặc địa chỉ IP) và cổng của máy chủ syslog đó. `unix:<file>`: Một tệp UNIX Domain Socket được chỉ định bởi `<file>`. `null`: Không gửi nhật ký kiểm toán đến bất kỳ đích đến bổ sung nào khác. |
| `syslogFacility` | chuỗi     | Cơ chế syslog, chẳng hạn như `kern`, theo định nghĩa trong RFC5424. Giá trị mặc định là `local0`.                                                                                                                                                                                                                                                                                                                                                    |

**Bảng 2.23. Đối tượng gatewayConfig**

| Trường           | Kiểu      | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|------------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `routingViaHost` | boolean   | Hãy đặt trường này thành `true` để chuyển lưu lượng truy cập đi (egress traffic) từ các pod sang ngăn xếp mạng (networking stack) của máy chủ (host). Đối với các hệ thống hoặc ứng dụng có yêu cầu đặc thù, cần sử dụng các tuyến đường (route) được cấu hình thủ công trong bảng định tuyến của nhân hệ điều hành (kernel routing table), bạn có thể muốn chuyển lưu lượng truy cập đi sang ngăn xếp mạng của máy chủ. Theo mặc định, lưu lượng truy cập đi được xử lý trong OVN để rời khỏi cụm (cluster) và không chịu ảnh hưởng bởi các tuyến đường đặc thù trong bảng định tuyến của nhân hệ điều hành. Giá trị mặc định là `false`. Trường này có tác động đến tính năng giảm tải phần cứng (hardware offloading) của Open vSwitch. Nếu bạn đặt trường này thành `true`, bạn sẽ không nhận được các lợi ích về hiệu năng từ tính năng giảm tải này, vì lưu lượng truy cập đi lúc đó sẽ được xử lý bởi ngăn xếp mạng của máy chủ. |
| `ipForwarding`   | đối tượng | Bạn có thể kiểm soát việc chuyển tiếp IP (IP forwarding) cho tất cả lưu lượng truy cập trên các giao diện do OVN-Kubernetes quản lý bằng cách sử dụng thông số `ipForwarding` trong tài nguyên `Network`. Hãy đặt giá trị là `Restricted` nếu chỉ muốn cho phép chuyển tiếp IP đối với lưu lượng truy cập liên quan đến Kubernetes. Hãy đặt giá trị là `Global` nếu muốn cho phép chuyển tiếp tất cả lưu lượng truy cập IP. Đối với các hệ thống cài đặt mới, giá trị mặc định là `Restricted`. Đối với các bản cập nhật lên OpenShift Container Platform phiên bản 4.14 trở lên, giá trị mặc định là `Global`. Lưu ý: Giá trị mặc định `Restricted` sẽ thiết lập cơ chế chuyển tiếp IP thành loại bỏ (drop) lưu lượng.                                                                                                                                                                                                                 |
| `ipv4`           | đối tượng | Tùy chọn: Chỉ định một đối tượng để cấu hình địa chỉ giả trang (masquerade address) nội bộ của OVN-Kubernetes, áp dụng cho lưu lượng truy cập từ host đến service đối với các địa chỉ IPv4.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `ipv6`           | đối tượng | Tùy chọn: Chỉ định một đối tượng để cấu hình địa chỉ giả trang (masquerade address) nội bộ của OVN-Kubernetes, áp dụng cho lưu lượng truy cập từ host đến dịch vụ đối với các địa chỉ IPv6.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

**Bảng 2.24. Đối tượng gatewayConfig.ipv4**

| Trường                     | Kiểu  | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|----------------------------|-------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `internalMasqueradeSubnet` | chuỗi | Các địa chỉ IPv4 được sử dụng nội bộ để thực hiện cơ chế "masquerade" (ẩn địa chỉ), cho phép lưu lượng truy cập từ host (máy chủ) đến các dịch vụ. Host được cấu hình với các địa chỉ IP này cùng với giao diện cầu nối cổng mặc định (shared gateway bridge interface). Giá trị mặc định là `169.254.169.0/29`. Lưu ý quan trọng: Đối với OpenShift Container Platform phiên bản 4.17 trở về sau, các cụm (cluster) sử dụng `169.254.0.0/17` làm mạng con (subnet) mặc định cho cơ chế masquerade. Đối với các cụm đã được nâng cấp, mạng con masquerade mặc định sẽ không thay đổi. |

**Bảng 2.25. Đối tượng gatewayConfig.ipv6**

| Trường                     | Kiểu  | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|----------------------------|-------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `internalMasqueradeSubnet` | chuỗi | Các địa chỉ IPv6 dạng "masquerade" (ẩn danh/thay thế) được sử dụng nội bộ để cho phép lưu lượng truy cập từ máy chủ (host) đến các dịch vụ. Máy chủ được cấu hình với các địa chỉ IP này cùng với giao diện cầu nối cổng kết nối (gateway bridge interface) dùng chung. Giá trị mặc định là `fd69::/125`. Quan trọng: Đối với OpenShift Container Platform phiên bản 4.17 trở về sau, các cụm (cluster) sử dụng `fd69::/112` làm mạng con (subnet) masquerade mặc định. Đối với các cụm đã được nâng cấp, mạng con masquerade mặc định vẫn giữ nguyên, không có thay đổi. |

**Bảng 2.26. Đối tượng ipsecConfig**

| Trường | Kiểu  | Mô tả                                                                                                                                                                                                                                                                                                                                                 |
|--------|-------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `mode` | chuỗi | Xác định cách thức hoạt động của cơ chế triển khai IPsec. Phải là một trong các giá trị sau:<br>`Disabled`: IPsec không được bật trên các nút (node) của cụm (cluster).<br>`External`: IPsec được bật cho lưu lượng mạng với các máy chủ bên ngoài.<br>`Full`: IPsec được bật cho lưu lượng giữa các pod và lưu lượng mạng với các máy chủ bên ngoài. |


Ví dụ cấu hình OVN-Kubernetes với IPsec được kích hoạt

```yaml
defaultNetwork:
  type: OVNKubernetes
  ovnKubernetesConfig:
    mtu: 1400
    genevePort: 6081
    ipsecConfig:
      mode: Full
```

#### 2.2.16. Tạo các tệp cấu hình Ignition
Vì bạn phải khởi động thủ công các máy trong cụm (cluster), nên bạn cần tạo các tệp cấu hình Ignition cần thiết để cụm thiết lập các máy của nó.

> **Quan trọng**
> 
> * Các tệp cấu hình Ignition do chương trình cài đặt tạo ra chứa các chứng chỉ có thời hạn 24 giờ; sau khoảng thời gian này, các chứng chỉ đó sẽ được gia hạn. Nếu cụm bị tắt trước khi các chứng chỉ được gia hạn và sau đó được khởi động lại khi đã quá 24 giờ, cụm sẽ tự động khôi phục các chứng chỉ đã hết hạn. Một ngoại lệ là bạn phải phê duyệt thủ công các yêu cầu ký chứng chỉ (CSR) của node-bootstrapper đang chờ xử lý để khôi phục các chứng chỉ kubelet. Để biết thêm thông tin, hãy xem tài liệu về cách khôi phục từ các chứng chỉ control plane đã hết hạn. 
> * Bạn nên sử dụng các tệp cấu hình Ignition trong vòng 12 giờ kể từ khi chúng được tạo, vì quá trình xoay vòng chứng chỉ 24 giờ thường diễn ra từ 16 đến 22 giờ sau khi cụm được cài đặt. Việc sử dụng các tệp cấu hình Ignition trong vòng 12 giờ giúp tránh lỗi cài đặt trong trường hợp quá trình cập nhật chứng chỉ diễn ra đúng lúc đang cài đặt.

**Điều kiện tiên quyết**

* Lấy chương trình cài đặt OpenShift Container Platform và pull secret (bí mật kéo ảnh) cho cụm của bạn.

**Quy trình**

1. Lấy các tệp cấu hình Ignition:

```shell
$ ./openshift-install create ignition-configs --dir <installation_directory>
```

Đối với `<installation_directory>`, hãy chỉ định tên thư mục để lưu trữ các tệp do chương trình cài đặt tạo ra.

> **Quan trọng:** Nếu bạn đã tạo tệp install-config.yaml, hãy chỉ định thư mục chứa tệp đó. Nếu không, hãy chỉ định một thư mục trống. Một số thành phần cài đặt, chẳng hạn như chứng chỉ X.509 khởi động (bootstrap), có thời hạn hiệu lực ngắn; do đó, bạn không được sử dụng lại thư mục cài đặt. Nếu muốn sử dụng lại các tệp riêng lẻ từ quá trình cài đặt cụm khác, bạn có thể sao chép chúng vào thư mục của mình. Tuy nhiên, tên tệp của các thành phần cài đặt có thể thay đổi giữa các phiên bản. Hãy thận trọng khi sao chép các tệp cài đặt từ phiên bản OpenShift Container Platform cũ hơn.

Các tệp sau đây sẽ được tạo trong thư mục này:

```
.
├── auth
│   ├── kubeadmin-password
│   └── kubeconfig
├── bootstrap.ign
├── master.ign
├── metadata.json
└── worker.ign
```

#### 2.2.17. Cài đặt RHCOS và khởi động quy trình bootstrap của OpenShift Container Platform
Để cài đặt OpenShift Container Platform trên cơ sở hạ tầng phần cứng vật lý (bare-metal) do bạn cung cấp, hãy cài đặt Red Hat Enterprise Linux CoreOS (RHCOS) bằng cách sử dụng các tệp cấu hình Ignition đã được tạo. Việc cung cấp các tệp này đảm bảo rằng quy trình bootstrap sẽ tự động bắt đầu sau khi các máy khởi động lại.

Nếu bạn đã định cấu hình cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các máy chạy RHCOS khởi động lại.

Để cài đặt RHCOS trên các máy, hãy thực hiện theo các bước sử dụng tệp ảnh ISO hoặc khởi động qua mạng (PXE booting).

> **Lưu ý:** Các bước triển khai nút tính toán (compute node) trong tài liệu cài đặt này dành riêng cho RHCOS. Nếu bạn chọn triển khai các nút tính toán dựa trên RHEL, bạn sẽ chịu trách nhiệm quản lý vòng đời và bảo trì hệ điều hành, bao gồm thực hiện cập nhật hệ thống, áp dụng các bản vá và hoàn tất mọi tác vụ cần thiết khác. Chỉ các máy tính toán chạy RHEL 8 mới được hỗ trợ.

Bạn có thể định cấu hình RHCOS trong quá trình cài đặt qua ISO hoặc PXE bằng các phương pháp sau:

* Các tham số kernel (kernel arguments): Bạn có thể sử dụng các tham số kernel để cung cấp thông tin cụ thể cho quá trình cài đặt. Ví dụ, bạn có thể chỉ định vị trí các tệp cài đặt RHCOS đã tải lên máy chủ HTTP, cũng như vị trí tệp cấu hình Ignition tương ứng với loại nút (node) mà bạn đang cài đặt. Đối với cài đặt qua PXE, bạn có thể sử dụng tham số `APPEND` để truyền các tham số này vào kernel của trình cài đặt trực tiếp (live installer). Đối với cài đặt qua ISO, bạn có thể tạm dừng quá trình khởi động của trình cài đặt trực tiếp để thêm các tham số kernel. Trong cả hai trường hợp, bạn có thể sử dụng các tham số đặc thù `coreos.inst.*` để điều hướng trình cài đặt trực tiếp, đồng thời sử dụng các tham số khởi động kernel tiêu chuẩn để bật hoặc tắt các dịch vụ kernel thông thường. 
* Các tệp cấu hình Ignition: Các tệp cấu hình Ignition (`*.ign`) trong OpenShift Container Platform được thiết kế riêng cho từng loại nút mà bạn đang cài đặt. Bạn cần cung cấp vị trí tệp cấu hình Ignition cho nút bootstrap, nút control plane (mặt phẳng điều khiển) hoặc nút compute (nút tính toán) trong quá trình cài đặt RHCOS, để các cấu hình này có hiệu lực ngay từ lần khởi động đầu tiên. Trong một số trường hợp đặc biệt, bạn có thể tạo một tệp cấu hình Ignition riêng biệt với phạm vi giới hạn để truyền vào hệ thống trực tiếp. Tệp cấu hình này có thể thực hiện các tác vụ cụ thể, chẳng hạn như thông báo trạng thái thành công cho hệ thống cung cấp tài nguyên (provisioning system) sau khi quá trình cài đặt hoàn tất. Tệp cấu hình Ignition đặc biệt này sẽ được `coreos-installer` xử lý và áp dụng trong lần khởi động đầu tiên của hệ thống đã cài đặt. Lưu ý: Không nên cung cấp trực tiếp các tệp cấu hình Ignition tiêu chuẩn dành cho nút control plane hoặc nút compute vào tệp ISO cài đặt trực tiếp. 
* `coreos-installer`: Bạn có thể khởi động trình cài đặt ISO trực tiếp vào giao diện dòng lệnh (shell prompt), cho phép chuẩn bị hệ thống đích theo nhiều cách khác nhau trước khi khởi động lần đầu. Cụ thể, bạn có thể chạy lệnh `coreos-installer` để xác định các thành phần cần đưa vào hệ thống, thao tác với các phân vùng đĩa và thiết lập mạng. Trong một số trường hợp, bạn có thể cấu hình các tính năng trên hệ thống trực tiếp rồi sao chép chúng sang hệ thống đã cài đặt. 

> **Lưu ý:** Kể từ phiên bản `0.17.0-3`, `coreos-installer` yêu cầu hệ điều hành RHEL 9 trở lên để chạy chương trình. Tuy nhiên, bạn vẫn có thể sử dụng các phiên bản `coreos-installer` cũ hơn để tùy chỉnh các thành phần RHCOS cho các bản phát hành OpenShift Container Platform mới hơn, cũng như cài đặt các image (ảnh hệ thống) lên ổ đĩa vật lý. Hãy xem mục "coreos-installer image mirror" để tải xuống các phiên bản cũ hơn của tệp nhị phân `coreos-installer`.

Việc chọn sử dụng phương thức cài đặt qua ISO hay PXE tùy thuộc vào tình huống cụ thể của bạn. Cài đặt qua PXE đòi hỏi phải có sẵn dịch vụ DHCP và cần chuẩn bị kỹ lưỡng hơn, nhưng lại giúp quy trình cài đặt trở nên tự động hóa hơn. Ngược lại, cài đặt qua ISO đòi hỏi nhiều thao tác thủ công hơn và có thể gây bất tiện nếu bạn cần thiết lập số lượng máy lớn.

##### 2.2.17.1. Cài đặt RHCOS bằng tệp ảnh ISO
Để cung cấp tài nguyên cho các máy vật lý hoặc máy ảo, hãy cài đặt RHCOS bằng tệp ảnh ISO có khả năng khởi động (bootable ISO image).

**Các điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm (cluster) của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn có một máy chủ HTTP mà cả máy tính của bạn và các máy bạn tạo ra đều có thể truy cập được. 
* Bạn đã xem qua phần cấu hình cài đặt RHCOS nâng cao (Advanced RHCOS installation configuration) để tìm hiểu các cách thiết lập những tính năng khác nhau, chẳng hạn như mạng và phân vùng đĩa.

**Quy trình**

1. Lấy mã băm (digest) SHA512 cho từng tệp cấu hình Ignition của bạn. Ví dụ: trên một hệ thống chạy Linux, bạn có thể sử dụng lệnh sau để lấy mã băm SHA512 cho tệp cấu hình Ignition `bootstrap.ign`:

```shell
$ sha512sum <installation_directory>/bootstrap.ign
```

Các giá trị tóm tắt (digest) được cung cấp cho `coreos-installer` ở một bước sau đó để xác thực tính nguyên bản của các tệp cấu hình Ignition trên các nút của cụm (cluster).

2. Hãy tải các tệp cấu hình Ignition cho nút bootstrap, control plane và compute node — vốn đã được chương trình cài đặt tạo ra — lên máy chủ HTTP của bạn. Ghi lại các URL của những tệp này.

> **Quan trọng:** Bạn có thể thêm hoặc thay đổi các thiết lập cấu hình trong các tệp Ignition trước khi lưu chúng lên máy chủ HTTP. Nếu bạn dự định bổ sung thêm các máy tính toán (compute machine) vào cụm (cluster) của mình sau khi hoàn tất quá trình cài đặt, đừng xóa các tệp này.

3. Từ máy chủ thực hiện cài đặt (installation host), hãy xác thực rằng các tệp cấu hình Ignition đã sẵn sàng tại các URL tương ứng. Ví dụ dưới đây cho thấy cách truy xuất tệp cấu hình Ignition dành cho nút bootstrap:

```shell
$ curl -k http://<HTTP_server>/bootstrap.ign
```

  * `<HTTP_server>`: Thay thế `bootstrap.ign` bằng `master.ign` hoặc `worker.ign` trong lệnh để xác nhận rằng các tệp cấu hình Ignition cho mặt phẳng điều khiển và các nút tính toán cũng có sẵn.

Ví dụ về kết quả đầu ra

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0{"ignition":{"version":"3.2.0"},"passwd":{"users":[{"name":"core","sshAuthorizedKeys":["ssh-rsa...
```

4. Mặc dù bạn có thể lấy các hình ảnh (image) RHCOS cần thiết cho phương thức cài đặt hệ điều hành mà mình chọn từ trang [lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/x86_64/dependencies/rhcos/), nhưng cách thức được khuyến nghị để có được phiên bản hình ảnh RHCOS chính xác là thông qua kết quả đầu ra của lệnh `openshift-install`:

```shell
$ openshift-install coreos print-stream-json | grep '\.iso[^.]'
```

Ví dụ đầu ra

```
"location": "<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live.aarch64.iso",
"location": "<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live.ppc64le.iso",
"location": "<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live.s390x.iso",
"location": "<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live.x86_64.iso",
```

> **Quan trọng:** Các hình ảnh (image) RHCOS có thể không thay đổi theo mỗi phiên bản phát hành của OpenShift Container Platform. Bạn phải tải xuống các hình ảnh có phiên bản cao nhất nhưng không vượt quá phiên bản OpenShift Container Platform mà bạn đang cài đặt. Nếu có sẵn các phiên bản hình ảnh khớp với phiên bản OpenShift Container Platform của bạn, hãy sử dụng các phiên bản đó. Chỉ sử dụng các hình ảnh định dạng ISO cho quy trình này. Các hình ảnh RHCOS định dạng qcow2 không được hỗ trợ cho kiểu cài đặt này.

Tên tệp ISO có dạng như ví dụ sau: `rhcos-<phiên_bản>-live.<architecture>.iso`

5. Sử dụng tệp ISO để bắt đầu quá trình cài đặt RHCOS. Bạn có thể chọn một trong các phương thức sau:

  * Ghi tệp ảnh ISO ra đĩa và khởi động trực tiếp từ đĩa đó. 
  * Sử dụng tính năng chuyển hướng ISO (ISO redirection) thông qua giao diện quản trị từ xa (lights-out management - LOM).

6. Khởi động tệp ảnh ISO của RHCOS mà không cần chỉ định thêm tùy chọn nào hoặc can thiệp vào trình tự khởi động trực tiếp (live boot). Hãy đợi cho đến khi trình cài đặt khởi động vào giao diện dòng lệnh (shell prompt) trong môi trường RHCOS live.

> **Lưu ý:** Bạn có thể can thiệp vào quá trình khởi động cài đặt RHCOS để thêm các tham số nhân (kernel arguments). Tuy nhiên, đối với quy trình sử dụng tệp ISO này, bạn nên dùng lệnh `coreos-installer` theo các bước hướng dẫn dưới đây thay vì thêm tham số nhân.

7. Thực thi lệnh `coreos-installer` cùng với các tùy chọn phù hợp với yêu cầu cài đặt của bạn. Tối thiểu, bạn cần chỉ định URL dẫn đến tệp cấu hình Ignition (Ignition config file) tương ứng với loại nút (node type) và thiết bị đích nơi bạn muốn thực hiện cài đặt:

```shell
$ sudo coreos-installer install --ignition-url=http://<HTTP_server>/<node_type>.ign <device> \
--ignition-hash=sha512-<digest>
```

* <device>: Bạn phải chạy lệnh `coreos-installer` với quyền `sudo`, vì người dùng `core` không có đủ đặc quyền `root` cần thiết để thực hiện quá trình cài đặt.
* <digest>: Tùy chọn `--ignition-hash` là bắt buộc khi tệp cấu hình Ignition được lấy thông qua URL HTTP, nhằm xác thực tính nguyên bản của tệp cấu hình Ignition trên nút (node) của cụm (cluster). `<digest>` chính là mã băm SHA512 của tệp cấu hình Ignition, được lấy từ bước trước đó.

> Lưu ý

Nếu bạn muốn cung cấp các tệp cấu hình Ignition thông qua máy chủ HTTPS sử dụng TLS, bạn có thể thêm cơ quan cấp chứng chỉ (CA) nội bộ vào kho lưu trữ chứng chỉ tin cậy (trust store) của hệ thống trước khi chạy `coreos-installer`.

Ví dụ dưới đây khởi tạo quá trình cài đặt cho một nút khởi động (bootstrap node) trên thiết bị `/dev/sda`. Tệp cấu hình Ignition cho nút khởi động này được lấy từ một máy chủ web HTTP có địa chỉ IP là 192.168.1.2:

```shell
$ sudo coreos-installer install \
  --ignition-url=http://192.168.1.2:80/installation_directory/bootstrap.ign /dev/sda \
  --ignition-hash=sha512-a5a2d43879223273c9b60af66b44202a1d1248fc01cf156c46d4a79f552b6bad47bc8cc78ddf0116e80c59d2ea9e32ba53bc807afbca581aa059311def2c3e3b
```

8. Theo dõi tiến trình cài đặt RHCOS trên bảng điều khiển (console) của máy.

> **Quan trọng:** Hãy đảm bảo rằng quá trình cài đặt đã hoàn tất thành công trên từng nút (node) trước khi bắt đầu cài đặt OpenShift Container Platform. Việc quan sát quá trình cài đặt cũng giúp xác định nguyên nhân nếu xảy ra sự cố trong lúc cài đặt RHCOS.

9. Sau khi RHCOS được cài đặt, bạn phải khởi động lại hệ thống. Trong quá trình khởi động lại, hệ thống sẽ áp dụng tệp cấu hình Ignition mà bạn đã chỉ định.
10. Kiểm tra đầu ra trên bảng điều khiển để xác nhận rằng Ignition đã chạy.

Ví dụ về lệnh

```
Ignition: ran on 2022/03/14 14:48:33 UTC (this boot)
Ignition: user-provided config was applied
```

11. Tiếp tục tạo các máy khác cho cụm (cluster) của bạn.

> **Quan trọng:** Tại thời điểm này, bạn phải tạo các máy bootstrap và các máy control plane (mặt phẳng điều khiển). Nếu các máy control plane không được thiết lập để có thể lập lịch (schedulable), bạn cũng cần tạo thêm ít nhất hai máy tính toán (compute machine) trước khi cài đặt OpenShift Container Platform.

Nếu các cơ sở hạ tầng cần thiết về mạng, DNS và bộ cân bằng tải (load balancer) đã sẵn sàng, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các nút (node) chạy hệ điều hành RHCOS khởi động lại.

> **Lưu ý:** Các nút RHCOS không có mật khẩu mặc định cho người dùng `core`. Bạn có thể truy cập các nút này bằng cách chạy lệnh `ssh core@<node>.<cluster_name>.<base_domain>` với tư cách là người dùng có quyền truy cập vào khóa cá nhân SSH (SSH private key) tương ứng với khóa công khai (public key) mà bạn đã chỉ định trong tệp `install_config.yaml`. Các nút trong cụm OpenShift Container Platform 4 chạy RHCOS là các thành phần không thể thay đổi (immutable) và dựa vào các Operator để thực hiện các thay đổi đối với cụm. Việc truy cập vào các nút của cụm thông qua SSH không được khuyến khích. Tuy nhiên, khi cần điều tra các sự cố cài đặt — chẳng hạn như khi API của OpenShift Container Platform không khả dụng hoặc `kubelet` trên một nút mục tiêu không hoạt động bình thường — bạn có thể cần truy cập qua SSH để gỡ lỗi hoặc khôi phục hệ thống trong trường hợp xảy ra sự cố nghiêm trọng.

##### 2.2.17.2. Cài đặt RHCOS bằng phương thức khởi động PXE hoặc iPXE
Bạn có thể sử dụng phương thức khởi động PXE hoặc iPXE để cài đặt RHCOS trên các máy chủ.

**Các điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm (cluster) của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn đã thiết lập cơ sở hạ tầng PXE hoặc iPXE phù hợp. 
* Bạn có một máy chủ HTTP có thể truy cập được từ máy tính của bạn cũng như từ các máy chủ mà bạn tạo ra. 
* Bạn đã xem qua phần cấu hình cài đặt RHCOS nâng cao để biết các cách thiết lập những tính năng khác nhau, chẳng hạn như mạng và phân vùng đĩa.

**Quy trình**

1. Tải các tệp cấu hình Ignition dành cho nút bootstrap, nút điều khiển (control plane) và nút tính toán (compute node) — vốn được tạo bởi chương trình cài đặt — lên máy chủ HTTP của bạn. Hãy ghi lại các URL của những tệp này.

> **Quan trọng:** Bạn có thể thêm hoặc thay đổi các thiết lập cấu hình trong các tệp cấu hình Ignition trước khi lưu chúng vào máy chủ HTTP của mình. Nếu bạn dự định bổ sung thêm các máy tính toán (compute machine) vào cụm (cluster) sau khi hoàn tất quá trình cài đặt, đừng xóa các tệp này.

2. Từ máy chủ thực hiện cài đặt, hãy xác thực rằng các tệp cấu hình Ignition đã sẵn sàng tại các URL tương ứng. Ví dụ dưới đây cho thấy cách truy xuất tệp cấu hình Ignition dành cho nút khởi động (bootstrap node):

```shell
$ curl -k http://<HTTP_server>/bootstrap.ign
```

  * `<HTTP_server>`: Thay thế `bootstrap.ign` bằng `master.ign` hoặc `worker.ign` trong lệnh để xác nhận rằng các tệp cấu hình Ignition cho mặt phẳng điều khiển và các nút tính toán cũng có sẵn.

Ví dụ về kết quả đầu ra

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0{"ignition":{"version":"3.2.0"},"passwd":{"users":[{"name":"core","sshAuthorizedKeys":["ssh-rsa...
```

3. Mặc dù bạn có thể lấy các tệp RHCOS kernel, initramfs và rootfs — vốn cần thiết cho phương thức cài đặt hệ điều hành mà bạn chọn — từ trang [lưu trữ (mirror) hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/x86_64/dependencies/rhcos/), nhưng cách thức được khuyến nghị để có được phiên bản tệp RHCOS chính xác là thông qua kết quả đầu ra từ lệnh `openshift-install`:

```shell
$ openshift-install coreos print-stream-json | grep -Eo '"https.*(kernel-|initramfs.|rootfs.)\w+(\.img)?"'
```

Ví dụ đầu ra

```
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-kernel-aarch64"
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-initramfs.aarch64.img"
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-rootfs.aarch64.img"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/49.84.202110081256-0/ppc64le/rhcos-<release>-live-kernel-ppc64le"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live-initramfs.ppc64le.img"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live-rootfs.ppc64le.img"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-kernel-s390x"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-initramfs.s390x.img"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-rootfs.s390x.img"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-kernel-x86_64"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-initramfs.x86_64.img"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-rootfs.x86_64.img"
```

> **Quan trọng:** Các tệp thành phần (artifact) của RHCOS có thể không thay đổi qua mỗi phiên bản phát hành của OpenShift Container Platform. Bạn phải tải xuống các tệp hình ảnh (image) có phiên bản cao nhất nhưng không vượt quá phiên bản OpenShift Container Platform mà bạn đang cài đặt. Chỉ sử dụng các tệp kernel, initramfs và rootfs phù hợp như được mô tả bên dưới cho quy trình này. Các tệp hình ảnh RHCOS định dạng QCOW2 không được hỗ trợ cho kiểu cài đặt này.

Tên các tệp này có chứa số phiên bản của OpenShift Container Platform. Ví dụ như sau:

  * `kernel`: `rhcos-<version>-live-kernel-<architecture>`
  * `initramfs`: `rhcos-<version>-live-initramfs.<architecture>.img`
  * `rootfs`: `rhcos-<version>-live-rootfs.<architecture>.img`

4. Hãy tải các tệp rootfs, kernel và initramfs lên máy chủ HTTP của bạn.

> **Quan trọng:** Nếu bạn dự định thêm các máy tính toán (compute machine) vào cụm (cluster) của mình sau khi hoàn tất quá trình cài đặt, đừng xóa các tệp này.

5. Cấu hình cơ sở hạ tầng khởi động qua mạng (network boot) sao cho các máy tính sẽ khởi động từ ổ đĩa cục bộ sau khi RHCOS đã được cài đặt trên đó.
6. Thiết lập quy trình cài đặt qua PXE hoặc iPXE cho các ảnh (image) RHCOS và bắt đầu quá trình cài đặt.
7. Hãy điều chỉnh một trong các mục menu mẫu dưới đây cho phù hợp với môi trường của bạn, đồng thời xác minh rằng các tệp ảnh (image) và tệp Ignition đều có thể truy cập được:
  * Đối với PXE (x86_64):

  ```
  DEFAULT pxeboot
  TIMEOUT 20
  PROMPT 0
  LABEL pxeboot
      KERNEL http://<HTTP_server>/rhcos-<version>-live-kernel-<architecture>
      APPEND initrd=http://<HTTP_server>/rhcos-<version>-live-initramfs.<architecture>.img coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
  ```

  Trong đó:
    * `kernel`: Chỉ định vị trí của tệp kernel chạy trực tiếp (live kernel) mà bạn đã tải lên máy chủ HTTP của mình. URL phải thuộc loại HTTP, TFTP hoặc FTP; các giao thức HTTPS và NFS không được hỗ trợ.
    * `initrd=main`: Nếu sử dụng nhiều card mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên card mạng có tên là `eno1`, hãy đặt `ip=eno1:dhcp`. Hãy chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP của mình. Giá trị của tham số `initrd` là vị trí của tệp `initramfs`, giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp `rootfs`, và giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition khởi động (bootstrap Ignition config). Bạn cũng có thể thêm các đối số kernel khác vào dòng `APPEND` để cấu hình mạng hoặc các tùy chọn khởi động khác. 

  > **Lưu ý:** Cấu hình này không kích hoạt quyền truy cập qua console nối tiếp (serial console) trên các máy có console đồ họa. Để cấu hình một console khác, hãy thêm một hoặc nhiều đối số `console=` vào dòng `APPEND`. Ví dụ: thêm `console=tty0 console=ttyS0` để thiết lập cổng nối tiếp đầu tiên trên PC làm console chính và console đồ họa làm console phụ. Để biết thêm thông tin, hãy xem bài viết [Cách thiết lập terminal nối tiếp và/hoặc console trong Red Hat Enterprise Linux?](https://access.redhat.com/articles/7212) và mục "Kích hoạt console nối tiếp cho quá trình cài đặt qua PXE và ISO" trong phần "Cấu hình cài đặt RHCOS nâng cao".

  * Đối với iPXE (x86_64 + aarch64):

  ```
  kernel http://<HTTP_server>/rhcos-<version>-live-kernel-<architecture> initrd=main coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
  initrd --name main http://<HTTP_server>/rhcos-<version>-live-initramfs.<architecture>.img
  boot
  ```

    * `kernel`: Chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP của mình. Giá trị của tham số `kernel` là vị trí của tệp kernel; đối số `initrd=main` là bắt buộc để khởi động trên các hệ thống UEFI; giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp rootfs; và giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition dùng để khởi động (bootstrap). Nếu sử dụng nhiều card mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên card mạng có tên là `eno1`, hãy đặt `ip=eno1:dhcp`.
    * `initrd`: Chỉ định vị trí của tệp initramfs mà bạn đã tải lên máy chủ HTTP của mình. 

  > **Lưu ý:** Cấu hình này không kích hoạt quyền truy cập qua console nối tiếp (serial console) trên các máy có console đồ họa. Để cấu hình một console khác, hãy thêm một hoặc nhiều đối số `console=` vào dòng `kernel`. Ví dụ: thêm `console=tty0 console=ttyS0` để thiết lập cổng nối tiếp đầu tiên trên PC làm console chính và console đồ họa làm console phụ. Để biết thêm thông tin, hãy xem bài viết "Cách thiết lập terminal nối tiếp và/hoặc console trong Red Hat Enterprise Linux?" và mục "Kích hoạt console nối tiếp cho quá trình cài đặt qua PXE và ISO" trong phần "Cấu hình cài đặt RHCOS nâng cao". 
  
  > **Lưu ý:** Để khởi động kernel CoreOS qua mạng trên kiến trúc `aarch64`, bạn cần sử dụng phiên bản iPXE được xây dựng với tùy chọn `IMAGE_GZIP` đã được kích hoạt. Xem thêm về tùy chọn `IMAGE_GZIP` trong iPXE.

  * Đối với PXE (sử dụng UEFI và Grub làm giai đoạn thứ hai) trên kiến trúc aarch64:

  ```
  menuentry 'Install CoreOS' {
      linux rhcos-<version>-live-kernel-<architecture>  coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
      initrd rhcos-<version>-live-initramfs.<architecture>.img
  }
  ```

  Trong đó:

    * `coreos.live.rootfs_url`: Chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP/TFTP của mình.
    * `kernel`: Giá trị của tham số kernel là vị trí của tệp nhân (kernel) trên máy chủ TFTP của bạn. Giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp rootfs, còn giá trị của tham số coreos.inst.ignition_url là vị trí của tệp cấu hình Ignition khởi động (bootstrap) trên máy chủ HTTP của bạn. Nếu sử dụng nhiều giao diện mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn ip. Ví dụ: để sử dụng DHCP trên giao diện mạng có tên là eno1, hãy đặt ip=eno1:dhcp.
    * `initrd rhcos`: Chỉ định vị trí của tệp initramfs mà bạn đã tải lên máy chủ TFTP của mình.

8. Theo dõi tiến trình cài đặt RHCOS trên bảng điều khiển (console) của máy.

> **Quan trọng:** Hãy đảm bảo rằng quá trình cài đặt đã hoàn tất thành công trên từng nút (node) trước khi bắt đầu cài đặt OpenShift Container Platform. Việc quan sát quá trình cài đặt cũng giúp xác định nguyên nhân nếu xảy ra sự cố trong lúc cài đặt RHCOS.

9. Sau khi RHCOS được cài đặt, hệ thống sẽ khởi động lại. Trong quá trình khởi động lại, hệ thống sẽ áp dụng tệp cấu hình Ignition mà bạn đã chỉ định.
10. Kiểm tra đầu ra trên bảng điều khiển để xác nhận rằng Ignition đã chạy.

Ví dụ về lệnh

```
Ignition: ran on 2022/03/14 14:48:33 UTC (this boot)
Ignition: user-provided config was applied
```

11. Tiếp tục tạo các máy (machine) cho cụm (cluster) của bạn.

> **Quan trọng:** Tại bước này, bạn phải tạo các máy bootstrap và các máy control plane. Nếu các máy control plane không được thiết lập để có thể lập lịch (schedulable), bạn cũng cần tạo thêm ít nhất hai máy tính toán (compute machine) trước khi tiến hành cài đặt cụm.

Nếu các cơ sở hạ tầng cần thiết về mạng, DNS và bộ cân bằng tải (load balancer) đã sẵn sàng, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các nút (node) chạy hệ điều hành RHCOS khởi động lại.

> **Lưu ý:** Các nút chạy RHCOS không có mật khẩu mặc định cho người dùng `core`. Bạn có thể truy cập vào các nút này bằng lệnh `ssh core@<node>.<cluster_name>.<base_domain>`, với điều kiện bạn là người dùng sở hữu khóa cá nhân SSH (SSH private key) tương ứng với khóa công khai (public key) mà bạn đã chỉ định trong tệp `install_config.yaml`. Các nút trong cụm OpenShift Container Platform 4 chạy RHCOS là các thành phần không thể thay đổi (immutable); mọi thay đổi đối với cụm đều được thực hiện thông qua các Operator. Việc truy cập vào các nút của cụm thông qua SSH không được khuyến khích. Tuy nhiên, trong trường hợp cần điều tra các sự cố cài đặt — chẳng hạn như khi API của OpenShift Container Platform không khả dụng hoặc `kubelet` trên một nút mục tiêu không hoạt động bình thường — bạn có thể cần truy cập qua SSH để phục vụ mục đích gỡ lỗi hoặc khôi phục sau sự cố.

##### 2.2.17.3. Cấu hình cài đặt RHCOS nâng cao
Để áp dụng các cấu hình nâng cao không khả dụng thông qua các phương thức cài đặt mặc định, bạn cần thực hiện cung cấp (provision) thủ công các nút Red Hat Enterprise Linux CoreOS (RHCOS) cho OpenShift Container Platform.

Phương pháp này cho phép kiểm soát chi tiết cơ sở hạ tầng nút để đáp ứng các yêu cầu triển khai cụ thể, bao gồm:

* Truyền các tham số nhân (kernel arguments) vào trình cài đặt trực tiếp (live installer)
* Chạy `coreos-installer` theo cách thủ công từ hệ thống trực tiếp (live system)
* Tùy chỉnh tệp ISO trực tiếp hoặc hình ảnh khởi động qua PXE (PXE boot image)

Các chủ đề về cấu hình nâng cao cho việc cài đặt thủ công Red Hat Enterprise Linux CoreOS (RHCOS) được trình bày chi tiết trong phần này bao gồm phân vùng đĩa, thiết lập mạng và các phương thức cấu hình Ignition khác nhau.

###### 2.2.17.3.1. Sử dụng các tùy chọn mạng nâng cao cho quá trình cài đặt qua PXE và ISO
Theo mặc định, hệ thống mạng cho các nút (node) trong OpenShift Container Platform sử dụng DHCP để thu thập tất cả các thiết lập cấu hình cần thiết. Bạn có thể thiết lập địa chỉ IP tĩnh hoặc định cấu hình các thiết lập đặc biệt khác.

Để thiết lập địa chỉ IP tĩnh hoặc định cấu hình các thiết lập đặc biệt (ví dụ: gộp cổng mạng - bonding), bạn có thể thực hiện một trong các cách sau:

* Truyền các tham số nhân (kernel parameter) đặc biệt khi khởi động trình cài đặt trực tiếp (live installer). 
* Sử dụng cấu hình máy (machine config) để sao chép các tệp tin mạng vào hệ thống được cài đặt. 
* Định cấu hình mạng từ dấu nhắc lệnh (shell prompt) của trình cài đặt trực tiếp, sau đó sao chép các thiết lập đó vào hệ thống được cài đặt để chúng có hiệu lực ngay khi hệ thống này khởi động lần đầu tiên.

Để định cấu hình cho quá trình cài đặt qua PXE hoặc iPXE, hãy sử dụng một trong các tùy chọn sau:

* Tham khảo các bảng "coreos-installer và các tùy chọn khởi động cho quá trình cài đặt qua ISO và PXE". 
* Sử dụng cấu hình máy (machine config) để sao chép các tệp tin mạng vào hệ thống được cài đặt.

Để định cấu hình cho quá trình cài đặt qua ISO, hãy thực hiện theo quy trình dưới đây.

**Quy trình**

1. Khởi động trình cài đặt ISO. 
2. Từ dấu nhắc lệnh của hệ thống trực tiếp (live system), hãy định cấu hình mạng cho hệ thống này bằng cách sử dụng các công cụ có sẵn trên RHEL, chẳng hạn như `nmcli` hoặc `nmtui`. 
3. Chạy lệnh `coreos-installer` để cài đặt hệ thống, đồng thời thêm tùy chọn `--copy-network` để sao chép cấu hình mạng. Ví dụ:

```shell
$ sudo coreos-installer install --copy-network \
     --ignition-url=http://host/worker.ign /dev/disk/by-id/scsi-<serial_number>
```

> **Quan trọng:** Tùy chọn `--copy-network` chỉ sao chép cấu hình mạng nằm trong thư mục `/etc/NetworkManager/system-connections`. Cụ thể, tùy chọn này không sao chép tên máy chủ (hostname) của hệ thống.

4. Khởi động lại vào hệ thống đã cài đặt.

###### 2.2.17.3.2. Phân vùng đĩa
Các phân vùng đĩa được tạo trên các nút (node) của cụm OpenShift Container Platform trong quá trình cài đặt Red Hat Enterprise Linux CoreOS (RHCOS). Mỗi nút RHCOS thuộc một kiến trúc cụ thể sẽ sử dụng cùng một bố cục phân vùng, trừ khi bạn thay đổi cấu hình phân vùng mặc định.

Trong quá trình cài đặt RHCOS, kích thước của hệ thống tệp gốc (root file system) sẽ được mở rộng để tận dụng toàn bộ dung lượng trống còn lại trên thiết bị đích.

> **Quan trọng:** Việc sử dụng lược đồ phân vùng tùy chỉnh trên nút có thể khiến OpenShift Container Platform không giám sát hoặc không đưa ra cảnh báo đối với một số phân vùng trên nút đó. Để biết thêm thông tin về việc giám sát hệ thống tệp của máy chủ (host) khi sử dụng phân vùng tùy chỉnh, hãy xem phần Tìm hiểu về Giám sát Hệ thống tệp trong OpenShift (các điều kiện loại bỏ/eviction conditions).

OpenShift Container Platform giám sát hai định danh hệ thống tệp sau:

  * `nodefs`: hệ thống tệp chứa thư mục `/var/lib/kubelet`. 
  * `imagefs`: hệ thống tệp chứa thư mục `/var/lib/containers`.

Với lược đồ phân vùng mặc định, cả `nodefs` và `imagefs` đều giám sát cùng một hệ thống tệp gốc là `/`.

Để thay đổi lược đồ phân vùng mặc định khi cài đặt RHCOS trên một nút của cụm OpenShift Container Platform, bạn cần tạo các phân vùng riêng biệt. Chẳng hạn, nếu bạn muốn thêm một phân vùng lưu trữ riêng cho các container và hình ảnh container (container images), bạn có thể gắn (mount) thư mục `/var/lib/containers` vào một phân vùng riêng. Khi đó, kubelet sẽ giám sát `/var/lib/containers` dưới dạng `imagefs`, trong khi hệ thống tệp gốc vẫn được giám sát dưới dạng `nodefs`.

> **Quan trọng:** Nếu bạn đã tăng kích thước đĩa để chứa hệ thống tệp lớn hơn, hãy cân nhắc việc tạo một phân vùng riêng cho `/var/lib/containers`. Ngoài ra, đối với các đĩa sử dụng định dạng xfs, việc điều chỉnh kích thước đĩa có thể giúp giảm thiểu các vấn đề về tiêu tốn tài nguyên CPU phát sinh do số lượng nhóm phân bổ (allocation groups) quá lớn.

OpenShift Container Platform hỗ trợ thêm một phân vùng để gắn lưu trữ vào thư mục `/var` hoặc một thư mục con của `/var`. Ví dụ:

* `/var/lib/containers`: Chứa các nội dung liên quan đến container; dung lượng này có thể tăng lên khi có thêm nhiều hình ảnh và container được thêm vào hệ thống. 
* `/var/lib/etcd`: Chứa dữ liệu mà bạn có thể muốn tách riêng, chẳng hạn như để tối ưu hóa hiệu suất lưu trữ cho etcd.
* `/var`: Chứa dữ liệu mà bạn có thể muốn tách riêng, chẳng hạn như để phục vụ mục đích kiểm toán. 

> Quan trọng: Đối với các ổ đĩa có dung lượng lớn hơn 100GB, và đặc biệt là lớn hơn 1TB, hãy tạo một phân vùng `/var` riêng biệt.

Việc lưu trữ nội dung của thư mục `/var` trên một phân vùng riêng giúp dễ dàng mở rộng không gian lưu trữ cho các khu vực này khi cần thiết, đồng thời cho phép cài đặt lại OpenShift Container Platform trong tương lai mà vẫn giữ nguyên dữ liệu đó. Phương pháp này giúp loại bỏ nhu cầu tải lại các container hoặc sao chép các tệp nhật ký (log) có dung lượng lớn trong quá trình cập nhật hệ thống.

Việc sử dụng một phân vùng riêng cho thư mục `/var` (hoặc một thư mục con của `/var`) cũng giúp ngăn chặn tình trạng dữ liệu trong thư mục đó tăng lên quá mức, gây đầy hệ thống tệp gốc (root file system).

Quy trình dưới đây thiết lập một phân vùng /var riêng biệt bằng cách thêm một tệp cấu hình máy (machine config manifest) vào tệp cấu hình Ignition dành cho một loại nút (node type) cụ thể trong giai đoạn chuẩn bị cài đặt.

**Quy trình**

1. Trên máy chủ cài đặt của bạn, hãy chuyển đến thư mục chứa chương trình cài đặt OpenShift Container Platform và tạo các tệp manifest Kubernetes cho cụm:

```shell
$ openshift-install create manifests --dir <installation_directory>
```

2. Tạo một tệp cấu hình Butane để thiết lập phân vùng bổ sung. Chẳng hạn, bạn có thể đặt tên tệp là `$HOME/clusterconfig/98-var-partition.bu`, thay đổi tên thiết bị đĩa thành tên thiết bị lưu trữ trên các hệ thống worker, đồng thời thiết lập dung lượng lưu trữ cho phù hợp. Ví dụ này đặt thư mục `/var` trên một phân vùng riêng biệt:

```yaml
variant: openshift
version: 4.22.0
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 98-var-partition
storage:
  disks:
  - device: /dev/disk/by-id/<device_name>
    partitions:
    - label: var
      start_mib: <partition_start_offset>
      size_mib: <partition_size>
      number: 5
  filesystems:
    - device: /dev/disk/by-partlabel/var
      path: /var
      format: xfs
      mount_options: [defaults, prjquota]
      with_mount_unit: true
```

Trong đó:

  * `<device_name>`: Chỉ định tên thiết bị lưu trữ của ổ đĩa mà bạn muốn phân vùng.
  * `<partition_start_offset>`: Chỉ định giá trị độ lệch (offset) tối thiểu cho ổ đĩa khởi động. Để đạt hiệu suất tối ưu, hãy đặt giá trị độ lệch tối thiểu là 25000 mebibyte. Hệ thống tệp gốc (root file system) sẽ tự động được thay đổi kích thước để lấp đầy toàn bộ không gian khả dụng cho đến vị trí độ lệch đã chỉ định. Nếu không chỉ định giá trị độ lệch, hoặc nếu giá trị được chỉ định nhỏ hơn mức tối thiểu khuyến nghị, hệ thống tệp gốc sẽ quá nhỏ; điều này có thể dẫn đến việc các lần cài đặt lại RHCOS sau này ghi đè lên phần đầu của phân vùng dữ liệu.
  * `<partition_size>`: Chỉ định kích thước của phân vùng dữ liệu (tính bằng mebibyte).
  * `mount_options`: Tùy chọn gắn (mount option) `prjquota` phải được bật đối với các hệ thống tệp được sử dụng cho lưu trữ container. 

> **Lưu ý:** Khi tạo một phân vùng `/var` riêng biệt, bạn không thể sử dụng các loại instance (instance type) khác nhau cho các nút tính toán (compute node) nếu các loại instance đó không có cùng tên thiết bị.

3.  Create a manifest from the Butane config and save it to the `clusterconfig/openshift directory`. For example, run the following command: 

```shell
$ butane $HOME/clusterconfig/98-var-partition.bu -o $HOME/clusterconfig/openshift/98-var-partition.yaml
```

4. Tạo các tệp cấu hình Ignition bằng cách chạy lệnh sau:

```shell
$ openshift-install create ignition-configs --dir <installation_directory>
```

Trong đó: `<installation_directory>` chỉ định tên của thư mục cài đặt. 

Các tệp cấu hình Ignition được tạo cho các nút bootstrap, control plane (mặt phẳng điều khiển) và compute (nút tính toán) trong thư mục cài đặt:

```
.
├── auth
│   ├── kubeadmin-password
│   └── kubeconfig
├── bootstrap.ign
├── master.ign
├── metadata.json
└── worker.ign
```

Các tệp trong thư mục `<installation_directory>/manifest` và `<installation_directory>/openshift` được đóng gói vào các tệp cấu hình Ignition, bao gồm cả tệp chứa đối tượng MachineConfig tùy chỉnh có tên là `98-var-partition`.

5. Tùy chọn: Bạn có thể áp dụng cấu hình phân vùng đĩa tùy chỉnh bằng cách tham chiếu đến các tệp cấu hình Ignition trong quá trình cài đặt RHCOS.

###### 2.2.17.3.3. Ví dụ về việc giữ lại các phân vùng hiện có
Đối với quá trình cài đặt qua ISO, bạn có thể thêm các tùy chọn vào lệnh `coreos-installer` để yêu cầu chương trình cài đặt giữ lại một hoặc nhiều phân vùng hiện có. Đối với quá trình cài đặt qua PXE, bạn có thể thêm các tùy chọn `coreos.inst.*` vào tham số `APPEND` để bảo toàn các phân vùng.

Các phân vùng được giữ lại có thể là những phân vùng dữ liệu từ một hệ thống OpenShift Container Platform đã tồn tại. Bạn có thể xác định các phân vùng đĩa muốn giữ lại dựa trên nhãn phân vùng hoặc số thứ tự phân vùng.

> **Lưu ý:** Nếu bạn giữ lại các phân vùng hiện có mà những phân vùng đó không để lại đủ không gian trống cho RHCOS, quá trình cài đặt sẽ thất bại nhưng các phân vùng được giữ lại vẫn sẽ không bị hư hại.

Các ví dụ dưới đây minh họa việc bảo toàn bất kỳ phân vùng hiện có nào trong quá trình cài đặt qua ISO, với điều kiện nhãn của phân vùng đó bắt đầu bằng từ "data" (tức là `data*`):

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
  --save-partlabel 'data*' \
  /dev/disk/by-id/scsi-<serial_number>
```

Ví dụ sau đây giữ lại các phân vùng từ 5 trở lên:

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
  --save-partindex 5- /dev/disk/by-id/scsi-<serial_number>
```

Trong các ví dụ trước đây có sử dụng tính năng bảo toàn phân vùng, `coreos-installer` sẽ tạo lại phân vùng đó ngay lập tức.

Các ví dụ dưới đây giúp giữ nguyên các phân vùng hiện có trong quá trình cài đặt qua PXE. Tùy chọn `APPEND` sau đây cho phép bảo toàn bất kỳ phân vùng nào có nhãn bắt đầu bằng chuỗi 'data' ('data*').

```
coreos.inst.save_partlabel=data*
```

Tùy chọn `APPEND` dưới đây bảo toàn các phân vùng từ 5 trở lên:

```
coreos.inst.save_partindex=5-
```

Tùy chọn `APPEND` sau đây bảo toàn phân vùng 6:

```
coreos.inst.save_partindex=6
```

###### 2.2.17.3.4. Xác định các cấu hình Ignition
Khi thực hiện cài đặt thủ công RHCOS, bạn có thể cung cấp hai loại cấu hình Ignition: cấu hình Ignition cho cài đặt cố định (permanent install) và cấu hình Ignition cho cài đặt trực tiếp (live install).

Khi cài đặt thủ công RHCOS, bạn có thể cung cấp hai loại cấu hình Ignition sau:

  * **Cấu hình Ignition cho cài đặt cố định (Permanent install Ignition config)**: Mọi quá trình cài đặt thủ công RHCOS đều cần sử dụng một trong các tệp cấu hình Ignition do `openshift-installer` tạo ra — chẳng hạn như `bootstrap.ign`, `master.ign` hoặc `worker.ign` — để thực hiện việc cài đặt.

**Quan trọng:** Không được sửa đổi trực tiếp các tệp cấu hình Ignition này. Bạn có thể cập nhật các tệp manifest được đóng gói bên trong các tệp cấu hình Ignition đó, như đã nêu trong các ví dụ ở những phần trước.

Đối với cài đặt qua PXE, bạn có thể truyền các cấu hình Ignition trên dòng `APPEND` bằng cách sử dụng tùy chọn `coreos.inst.ignition_url=`. Đối với cài đặt qua ISO, sau khi ISO khởi động vào dấu nhắc lệnh (shell prompt), bạn phải chỉ định cấu hình Ignition trong dòng lệnh `coreos-installer` bằng tùy chọn `--ignition-url=`. Trong cả hai trường hợp, chỉ các giao thức HTTP và HTTPS mới được hỗ trợ. 

  * **Cấu hình Ignition cho cài đặt trực tiếp (Live install Ignition config)**: Loại cấu hình này có thể được tạo bằng cách sử dụng lệnh phụ `customize` của `coreos-installer` cùng với các tùy chọn đi kèm. Với phương pháp này, cấu hình Ignition được truyền vào phương tiện cài đặt trực tiếp (live install medium), tự động chạy ngay khi khởi động và thực hiện các tác vụ thiết lập trước hoặc sau khi hệ thống RHCOS được cài đặt vào ổ đĩa. Phương pháp này chỉ nên được sử dụng cho các tác vụ cần thực hiện một lần duy nhất và không cần áp dụng lại sau đó, chẳng hạn như các thao tác phân vùng nâng cao mà không thể thực hiện thông qua `machine config`.

Đối với khởi động qua PXE hoặc ISO, bạn có thể tạo cấu hình Ignition và thêm tùy chọn `ignition.config.url=` vào dòng `APPEND` để xác định vị trí của tệp cấu hình đó. Bạn cũng cần bổ sung các tham số `ignition.firstboot` và `ignition.platform.id=metal`; nếu thiếu các tham số này, tùy chọn `ignition.config.url` sẽ bị bỏ qua.

###### 2.2.17.3.5. Cấu hình bảng điều khiển (console) mặc định
Các nút (node) Red Hat Enterprise Linux CoreOS (RHCOS) được cài đặt từ ảnh khởi động (boot image) của OpenShift Container Platform 4.22 sử dụng cấu hình bảng điều khiển mặc định, được thiết kế để phù hợp với hầu hết các thiết lập trên môi trường ảo hóa và máy chủ vật lý (bare metal).

Các nền tảng đám mây và ảo hóa khác nhau có thể sử dụng các thiết lập mặc định khác nhau tùy thuộc vào kiến trúc được chọn.

Đối với các hệ thống máy chủ vật lý, cấu hình mặc định của nhân hệ điều hành (kernel) thường đặt bảng điều khiển đồ họa làm bảng điều khiển chính, trong khi bảng điều khiển nối tiếp (serial console) sẽ bị vô hiệu hóa.

Cấu hình bảng điều khiển mặc định có thể không khớp với cấu hình phần cứng cụ thể của bạn, hoặc bạn có thể có những yêu cầu đặc thù đòi hỏi phải điều chỉnh lại cấu hình này. Ví dụ:

* Bạn cần truy cập vào trình bao khẩn cấp (emergency shell) thông qua bảng điều khiển để phục vụ mục đích gỡ lỗi. 
* Nền tảng đám mây của bạn không cung cấp quyền truy cập tương tác vào bảng điều khiển đồ họa, nhưng lại cung cấp bảng điều khiển nối tiếp. 
* Bạn muốn kích hoạt nhiều bảng điều khiển cùng lúc.

Cấu hình bảng điều khiển được kế thừa từ ảnh khởi động. Điều này có nghĩa là các nút mới trong các cụm (cluster) hiện có sẽ không bị ảnh hưởng bởi những thay đổi đối với cấu hình bảng điều khiển mặc định.

Bạn có thể định cấu hình bảng điều khiển cho các hệ thống máy chủ vật lý theo những cách sau:

* Sử dụng công cụ `coreos-installer` trực tiếp trên dòng lệnh. 
* Sử dụng các lệnh phụ (subcommand) `coreos-installer iso customize` hoặc `coreos-installer pxe customize` cùng với tùy chọn `--dest-console` để tạo một ảnh tùy chỉnh, giúp tự động hóa quy trình này.

> **Lưu ý:** Đối với các tùy chỉnh nâng cao, hãy thực hiện cấu hình bảng điều khiển thông qua các lệnh phụ `coreos-installer iso` hoặc `coreos-installer pxe`, thay vì sử dụng các tham số của nhân hệ điều hành (kernel arguments).

###### 2.2.17.3.6. Kích hoạt bảng điều khiển nối tiếp (serial console) cho các quá trình cài đặt qua PXE và ISO
Theo mặc định, bảng điều khiển nối tiếp trên Red Hat Enterprise Linux CoreOS (RHCOS) bị vô hiệu hóa và mọi thông tin đầu ra đều được hiển thị trên bảng điều khiển đồ họa. Bạn có thể kích hoạt bảng điều khiển nối tiếp cho các quá trình cài đặt qua PXE và ISO.

**Quy trình**

1. Khởi động trình cài đặt từ tệp ISO. 
2. Chạy lệnh `coreos-installer` để cài đặt hệ thống; trong đó, hãy thêm tùy chọn `--console` hai lần: một lần để chỉ định bảng điều khiển đồ họa và một lần để chỉ định bảng điều khiển nối tiếp:

```shell
$ coreos-installer install \
  --console=tty0 \
  --console=ttyS0,<options> \
  --ignition-url=http://host/worker.ign /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `--console=tty0`: Thiết bị console phụ mong muốn. Trong trường hợp này là console đồ họa. Nếu bỏ qua tùy chọn này, console đồ họa sẽ bị vô hiệu hóa.
  * `--console=ttyS0`: Thiết bị console chính mong muốn. Trong trường hợp này là console nối tiếp (serial console). Phần tùy chọn (options) xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho phần này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux (kernel) là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về [console nối tiếp của nhân Linux](https://www.kernel.org/doc/html/latest/admin-guide/serial-console.html).


3. Khởi động lại vào hệ thống đã cài đặt.

> **Lưu ý:** Bạn có thể đạt được kết quả tương tự bằng cách sử dụng tùy chọn `coreos-installer install --append-karg` và chỉ định bảng điều khiển (console) thông qua tham số `console=`. Tuy nhiên, cách này chỉ thiết lập bảng điều khiển cho nhân hệ điều hành (kernel) chứ không áp dụng cho bộ nạp khởi động (bootloader).

Để cấu hình cài đặt qua PXE, hãy đảm bảo rằng tùy chọn dòng lệnh nhân hệ điều hành `coreos.inst.install_dev` không được đưa vào; sau đó, sử dụng dấu nhắc lệnh (shell prompt) để chạy thủ công `coreos-installer` theo quy trình cài đặt từ tệp ISO đã nêu ở trên.

###### 2.2.17.3.7. Tùy chỉnh quá trình cài đặt RHCOS qua live ISO hoặc PXE
Bạn có thể sử dụng tệp ảnh live ISO hoặc môi trường PXE để cài đặt RHCOS bằng cách đưa trực tiếp tệp cấu hình Ignition vào tệp ảnh đó. Thao tác này tạo ra một tệp ảnh tùy chỉnh mà bạn có thể dùng để thiết lập hệ thống của mình.

Đối với tệp ảnh ISO, cơ chế thực hiện là sử dụng lệnh phụ `coreos-installer iso customize`; lệnh này sẽ sửa đổi tệp `.iso` dựa trên cấu hình của bạn. Tương tự, đối với môi trường PXE, bạn sử dụng lệnh phụ `coreos-installer pxe customize` để tạo tệp `initramfs` mới bao gồm các tùy chỉnh của bạn.

Lệnh phụ `customize` là một công cụ đa năng, cho phép tích hợp nhiều loại tùy chỉnh khác nhau. Dưới đây là một số ví dụ về các tùy chỉnh phổ biến:

* Thêm các chứng chỉ CA tùy chỉnh (trong trường hợp chính sách bảo mật của doanh nghiệp yêu cầu). 
* Cấu hình các thiết lập mạng mà không cần sử dụng các tham số nhân (kernel arguments). 
* Nhúng các tập lệnh (script) hoặc tệp nhị phân tùy ý để chạy trước hoặc sau quá trình cài đặt.

###### 2.2.17.3.8. Tùy chỉnh tệp ISO RHCOS chạy trực tiếp (live ISO)
Bạn có thể tùy chỉnh trực tiếp tệp ISO RHCOS bằng lệnh phụ `iso customize` của công cụ `coreos-installer`. Khi bạn khởi động từ tệp ISO này, các tùy chỉnh sẽ được tự động áp dụng. Bạn có thể sử dụng tính năng này để cấu hình tệp ISO sao cho tự động thực hiện cài đặt RHCOS.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) của `coreos-installer`. 
2. Lấy tệp ISO RHCOS và tệp cấu hình Ignition từ trang lưu trữ (mirror) của RHCOS, sau đó chạy lệnh dưới đây để đưa cấu hình Ignition trực tiếp vào tệp ISO:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
  --dest-ignition bootstrap.ign \
  --dest-device /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `--dest-ignition`: Chỉ định tệp cấu hình Ignition được tạo ra từ chương trình cài đặt `openshift-installer`.
  * `--dest-device`: Khi bạn chỉ định tùy chọn này, tệp ảnh ISO sẽ tự động thực thi quá trình cài đặt. Nếu không, tệp ảnh vẫn được cấu hình sẵn cho việc cài đặt nhưng sẽ không tự động thực thi, trừ khi bạn chỉ định tham số nhân (kernel argument) là `coreos.inst.install_dev`.

3. Tùy chọn: Để xóa các tùy chỉnh trên tệp ảnh ISO và đưa tệp ảnh trở về trạng thái ban đầu, hãy chạy lệnh sau:

```shell
$ coreos-installer iso reset rhcos-<version>-live.x86_64.iso
```

Giờ đây, bạn có thể tùy chỉnh lại tệp ảnh ISO trực tiếp (live ISO image) hoặc sử dụng nó ở trạng thái nguyên bản.

Việc áp dụng các tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.2.17.3.8.1. Chỉnh sửa tệp ISO cài đặt trực tiếp (live install ISO) để kích hoạt bảng điều khiển nối tiếp (serial console)

Để chuyển hướng đầu ra của hệ thống từ giao diện đồ họa mặc định, hãy kích hoạt bảng điều khiển nối tiếp bằng cách chỉnh sửa tệp ISO cài đặt trực tiếp. Cấu hình này đảm bảo khả năng truy cập vào các thông báo khởi động trên các cụm OpenShift Container Platform phiên bản 4.12 trở lên.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy tệp ISO RHCOS từ trang [lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ISO này, qua đó kích hoạt bảng điều khiển nối tiếp để nhận dữ liệu đầu ra:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
  --dest-ignition <path> \
  --dest-console tty0 \
  --dest-console ttyS0,<options> \
  --dest-device /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `<path>`: Vị trí của tệp cấu hình Ignition cần cài đặt.
  * tty0: Bảng điều khiển (console) phụ mong muốn. Trong trường hợp này là bảng điều khiển đồ họa. Nếu bỏ qua tùy chọn này, bảng điều khiển đồ họa sẽ bị vô hiệu hóa.
  * `<options>`: Bảng điều khiển chính mong muốn. Trong trường hợp này là bảng điều khiển nối tiếp (serial console). Trường tùy chọn này xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho trường này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux (kernel) là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về [bảng điều khiển nối tiếp của nhân Linux](https://www.kernel.org/doc/html/latest/admin-guide/serial-console.html).
  * `<serial_number>`: Ổ đĩa được chỉ định để thực hiện cài đặt. Nếu bạn bỏ qua tùy chọn này, tệp ảnh ISO sẽ tự động chạy chương trình cài đặt, nhưng quá trình này sẽ thất bại trừ khi bạn cũng chỉ định tham số nhân `coreos.inst.install_dev`. 

> **Lưu ý:** Tùy chọn `--dest-console` ảnh hưởng đến hệ thống sau khi cài đặt chứ không phải hệ thống chạy trực tiếp từ tệp ảnh ISO (live ISO). Để thay đổi bảng điều khiển cho hệ thống chạy từ tệp ảnh ISO, hãy sử dụng tùy chọn `--live-karg-append` và chỉ định bảng điều khiển thông qua tham số `console=`. 

Các tùy chỉnh của bạn sẽ được áp dụng và có hiệu lực trong mọi lần khởi động tiếp theo từ tệp ảnh ISO này.

3. Tùy chọn: Để xóa các tùy chỉnh trên tệp ảnh ISO và đưa tệp ảnh về trạng thái ban đầu, hãy chạy lệnh sau:

```shell
$ coreos-installer iso reset rhcos-<version>-live.x86_64.iso
```

Giờ đây, bạn có thể tùy chỉnh lại tệp ảnh ISO trực tiếp (live ISO image) hoặc sử dụng nó ở trạng thái nguyên bản.

###### 2.2.17.3.8.2. Chỉnh sửa tệp ISO cài đặt trực tiếp để sử dụng cơ quan cấp chứng chỉ (CA) tùy chỉnh
Bạn có thể cung cấp các chứng chỉ của cơ quan cấp chứng chỉ (CA) cho Ignition thông qua cờ `--ignition-ca` của lệnh con `customize`. Các chứng chỉ CA này có thể được sử dụng trong cả giai đoạn khởi động cài đặt lẫn giai đoạn cung cấp cấu hình (provisioning) cho hệ thống đã cài đặt.

> **Lưu ý:** Các chứng chỉ CA tùy chỉnh ảnh hưởng đến cách Ignition truy xuất các tài nguyên từ xa, nhưng chúng không ảnh hưởng đến các chứng chỉ được cài đặt trên chính hệ thống đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy tệp ISO RHCOS từ trang [lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ISO này nhằm sử dụng với một CA tùy chỉnh:

```shell
coreos-installer iso customize rhcos-<version>-live.x86_64.iso --ignition-ca cert.pem
```

> **Quan trọng:** Tham số nhân (kernel parameter) `coreos.inst.ignition_url` không hoạt động với cờ `--ignition-ca`. Bạn phải sử dụng cờ `--dest-ignition` để tạo một ảnh (image) tùy chỉnh cho mỗi cụm (cluster).

Việc áp dụng chứng chỉ CA tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.2.17.3.8.3. Chỉnh sửa tệp ISO cài đặt trực tiếp (live install ISO) với các thiết lập mạng tùy chỉnh
Bạn có thể nhúng một tệp khóa (keyfile) của NetworkManager vào tệp ISO cài đặt trực tiếp và chuyển nó vào hệ thống được cài đặt thông qua tham số `--network-keyfile` của lệnh con `customize`.

> **Cảnh báo:** Khi tạo hồ sơ kết nối (connection profile), bạn bắt buộc phải sử dụng phần mở rộng tệp là `.nmconnection`. Nếu không có phần mở rộng này, cụm (cluster) sẽ áp dụng hồ sơ kết nối đó vào môi trường cài đặt trực tiếp, nhưng lại không áp dụng cấu hình này khi các nút (node) trong cụm khởi động lần đầu, dẫn đến việc hệ thống không hoạt động như mong đợi.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) của `coreos-installer`. 
2. Tạo một hồ sơ kết nối cho giao diện mạng được gộp (bonded interface). Ví dụ, hãy tạo tệp `bond0.nmconnection` trong thư mục cục bộ của bạn với nội dung như sau:

```
/[connection]
id=bond0
type=bond
interface-name=bond0
multi-connect=1

/[bond]
miimon=100
mode=active-backup

/[ipv4]
method=auto

/[ipv6]
method=auto
```

3. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em1.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em1
type=ethernet
interface-name=em1
master=bond0
multi-connect=1
slave-type=bond
```

4. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em2.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em2
type=ethernet
interface-name=em2
master=bond0
multi-connect=1
slave-type=bond
```

5. Hãy tải tệp ảnh ISO của RHCOS từ trang lưu trữ (mirror) ảnh RHCOS, sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO này với các thiết lập mạng mà bạn đã cấu hình:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
  --network-keyfile bond0.nmconnection \
  --network-keyfile bond0-proxy-em1.nmconnection \
  --network-keyfile bond0-proxy-em2.nmconnection
```

Các thiết lập mạng được áp dụng cho hệ thống đang hoạt động và được chuyển sang hệ thống đích.

###### 2.2.17.3.8.4. Tùy chỉnh tệp ISO cài đặt trực tiếp (live install ISO) cho thiết bị khởi động iSCSI
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình bằng cách sử dụng một phiên bản tùy chỉnh của tệp ISO RHCOS (live RHCOS image).

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) tệp tin `coreos-installer`. 
2. Lấy tệp ISO RHCOS từ trang lưu trữ (mirror) tệp tin RHCOS, sau đó chạy lệnh dưới đây để tùy chỉnh tệp ISO này với các thông tin sau:

```shell
$ coreos-installer iso customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/disk/by-path/<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.initiator=<initiator_iqn> \
  --dest-karg-append netroot=<target_iqn> \
  -o custom.iso rhcos-<version>-live.x86_64.iso
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
  * `<target_iqn>`: Xác định vị trí của hệ thống đích. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng (port number) tương ứng, nút iSCSI đích (target iSCSI node) ở định dạng IQN, và số đơn vị logic iSCSI (LUN).
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích. `<initiator_iqn>`::Xác định tên của iSCSI initiator (phía máy khách) ở định dạng IQN. Initiator này sẽ thiết lập một phiên kết nối tới iSCSI target. `<target_iqn>`::Xác định tên của iSCSI target (phía máy chủ) ở định dạng IQN.

Để biết thêm thông tin về các tùy chọn iSCSI được hỗ trợ bởi `dracut`, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.2.17.3.8.5. Tùy chỉnh tệp ảnh ISO cài đặt trực tiếp (live install ISO) cho thiết bị khởi động iSCSI sử dụng iBFT
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình hệ thống bằng cách sử dụng một phiên bản tùy chỉnh của tệp ảnh RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
* Tùy chọn: Bạn đã thiết lập đa đường dẫn (multipath) cho iSCSI target của mình.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) tệp ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy tệp ảnh ISO của RHCOS từ trang [lưu trữ tệp ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO đó với các thông tin sau:

```shell
$ coreos-installer iso customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/mapper/mpatha \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.firmware=1 \
  --dest-karg-append rd.multipath=default \
  -o custom.iso rhcos-<version>-live.x86_64.iso
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này nên chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này nên chứa lệnh `iscsiadm --mode node --logout=all`.
  * `/dev/mapper/mpatha`: Chỉ định đường dẫn đến thiết bị. Nếu bạn đang sử dụng tính năng đa đường dẫn (multipath), thiết bị đó sẽ là `/dev/mapper/mpatha`. Trong trường hợp có nhiều thiết bị đa đường dẫn được kết nối, hoặc để xác định một cách rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN) có sẵn trong thư mục `/dev/disk/by-path`.
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích. `rd.iscsi.firmware=1` quy định rằng tham số iSCSI sẽ được đọc từ firmware của BIOS.
  * `rd.multipath=default`: Xác định xem bạn có muốn kích hoạt tính năng đa đường dẫn hay không. Đây là một tham số tùy chọn.

Để biết thêm thông tin, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) của `dracut.cmdline`.

###### 2.2.17.3.9. Tùy chỉnh môi trường RHCOS PXE trực tiếp (live)
Bạn có thể tùy chỉnh trực tiếp môi trường RHCOS PXE bằng lệnh phụ `pxe customize` của `coreos-installer`. Khi khởi động môi trường PXE, các tùy chỉnh này sẽ được tự động áp dụng. Bạn có thể sử dụng tính năng này để cấu hình môi trường PXE nhằm tự động cài đặt RHCOS.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy các tệp `kernel`, `initramfs` và `rootfs` của RHCOS từ trang [lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), cùng với tệp cấu hình Ignition; sau đó, chạy lệnh dưới đây để tạo một tệp `initramfs` mới chứa các tùy chỉnh từ tệp cấu hình Ignition của bạn:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --dest-ignition bootstrap.ign \
  --dest-device /dev/disk/by-id/scsi-<serial_number> \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

Trong đó:

  * `--dest-ignition`: Chỉ định tệp cấu hình Ignition được tạo từ `openshift-installer`.
  * `<serial_number>`: Khi bạn chỉ định tùy chọn này, môi trường PXE sẽ tự động thực thi quá trình cài đặt. Nếu không, hình ảnh (image) vẫn được cấu hình sẵn sàng cho việc cài đặt nhưng sẽ không tự động thực hiện trừ khi bạn chỉ định tham số nhân (kernel argument) `coreos.inst.install_dev`.
  * `<version>`: Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn. 

Việc áp dụng các tùy chỉnh này sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.2.17.3.9.1. Chỉnh sửa môi trường PXE cài đặt trực tiếp (live install) để kích hoạt bảng điều khiển nối tiếp (serial console)
Để chuyển hướng đầu ra của hệ thống từ giao diện đồ họa mặc định, hãy kích hoạt bảng điều khiển nối tiếp bằng cách chỉnh sửa môi trường PXE cài đặt trực tiếp. Cấu hình này đảm bảo khả năng truy cập vào các thông báo khởi động trên các cụm OpenShift Container Platform phiên bản 4.12 trở lên.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy các tệp nhân (kernel), `initramfs` và `rootfs` của RHCOS từ trang [lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), cùng với tệp cấu hình Ignition; sau đó, chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới, cho phép bảng điều khiển nối tiếp nhận dữ liệu đầu ra:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --dest-ignition <path> \
  --dest-console tty0 \
  --dest-console ttyS0,<options> \
  --dest-device /dev/disk/by-id/scsi-<serial_number> \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

###### 2.2.17.3.9.2. Sửa đổi môi trường PXE cài đặt trực tiếp (live install) để sử dụng cơ quan cấp chứng chỉ (CA) tùy chỉnh
Bạn có thể cung cấp các chứng chỉ của cơ quan cấp chứng chỉ (CA) cho Ignition thông qua cờ `--ignition-ca` của lệnh phụ `customize`. Các chứng chỉ CA này có thể được sử dụng trong cả quá trình khởi động cài đặt lẫn giai đoạn cung cấp cấu hình (provisioning) cho hệ thống đã cài đặt.

> **Lưu ý:** Các chứng chỉ CA tùy chỉnh ảnh hưởng đến cách Ignition truy xuất các tài nguyên từ xa, nhưng chúng không ảnh hưởng đến các chứng chỉ được cài đặt trên chính hệ thống đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy các tệp `kernel`, `initramfs` và `rootfs` của RHCOS từ trang [lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới, phục vụ cho việc sử dụng CA tùy chỉnh:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --ignition-ca cert.pem \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

3. Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân (kernel arguments) `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn.

> **Quan trọng:** Tham số nhân `coreos.inst.ignition_url` không hoạt động với cờ `--ignition-ca`. Bạn phải sử dụng cờ `--dest-ignition` để tạo một hình ảnh (image) tùy chỉnh cho mỗi cụm (cluster).

Việc áp dụng chứng chỉ CA tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.2.17.3.9.3. Sửa đổi môi trường PXE cài đặt trực tiếp (live install) với các thiết lập mạng tùy chỉnh
Bạn có thể nhúng một tệp khóa (keyfile) của NetworkManager vào môi trường PXE cài đặt trực tiếp và chuyển nó sang hệ thống được cài đặt thông qua tham số `--network-keyfile` của lệnh con `customize`.

> **Cảnh báo:** Khi tạo hồ sơ kết nối (connection profile), bạn bắt buộc phải sử dụng phần mở rộng tệp là `.nmconnection`. Nếu không có phần mở rộng này, cụm (cluster) sẽ áp dụng hồ sơ kết nối vào môi trường cài đặt trực tiếp, nhưng sẽ không áp dụng cấu hình đó khi cụm khởi động các nút (node) lần đầu tiên, dẫn đến hệ thống không hoạt động như mong đợi.

> **Quy trình:**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) của `coreos-installer`. 
2. Tạo một hồ sơ kết nối cho giao diện mạng được gộp (bonded interface). Ví dụ: tạo tệp `bond0.nmconnection` trong thư mục cục bộ của bạn với nội dung như sau:

```
/[connection]
id=bond0
type=bond
interface-name=bond0
multi-connect=1

/[bond]
miimon=100
mode=active-backup

/[ipv4]
method=auto

/[ipv6]
method=auto
```

3. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em1.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em1
type=ethernet
interface-name=em1
master=bond0
multi-connect=1
slave-type=bond
```

4. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em2.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em2
type=ethernet
interface-name=em2
master=bond0
multi-connect=1
slave-type=bond
```

5. Hãy tải các tệp kernel, initramfs và rootfs của RHCOS từ trang [lưu trữ (mirror) hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tạo một tệp initramfs tùy chỉnh mới, trong đó đã bao gồm các cấu hình mạng của bạn:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --network-keyfile bond0.nmconnection \
  --network-keyfile bond0-proxy-em1.nmconnection \
  --network-keyfile bond0-proxy-em2.nmconnection \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

6. Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân (kernel arguments) `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn.

Các thiết lập mạng sẽ được áp dụng cho hệ thống đang chạy (live system) và được chuyển sang hệ thống đích.

###### 2.2.17.3.9.4. Tùy chỉnh môi trường PXE cài đặt trực tiếp (live install) cho thiết bị khởi động iSCSI
Bạn có thể thiết lập các giá trị đích (target) và khởi tạo (initiator) iSCSI để tự động gắn kết (mount), khởi động và cấu hình bằng cách sử dụng một phiên bản tùy chỉnh của ảnh (image) RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một đích (target) iSCSI mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy các tệp `kernel`, `initramfs` và `rootfs` của RHCOS từ [trang lưu trữ ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới với các thông tin sau:

```shell
$ coreos-installer pxe customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/disk/by-path/<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.initiator=<initiator_iqn> \
  --dest-karg-append netroot=<target_iqn> \
  -o custom.img rhcos-<version>-live-initramfs.x86_64.img
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
  * `<target_iqn>`: Xác định vị trí của hệ thống đích. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng tương ứng, nút iSCSI đích (target iSCSI node) ở định dạng IQN, và số đơn vị logic iSCSI (LUN).
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích.
  * `<initiator_iqn>`: Xác định tên của iSCSI initiator (phía máy khách) ở định dạng IQN. Initiator sẽ thiết lập một phiên kết nối tới iSCSI target.
  * `<target_iqn>`: Xác định tên của iSCSI target (phía máy chủ) ở định dạng IQN. 

Để biết thêm thông tin về các tùy chọn iSCSI được `dracut` hỗ trợ, vui lòng tham khảo trang[hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.2.17.3.9.5. Tùy chỉnh môi trường PXE cài đặt trực tiếp (live install) cho thiết bị khởi động iSCSI sử dụng iBFT
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình hệ thống bằng cách sử dụng một phiên bản tùy chỉnh của ảnh (image) RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
* Tùy chọn: Bạn đã thiết lập đa đường dẫn (multipath) cho iSCSI target của mình.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy các tệp `kernel`, `initramfs` và `rootfs` của RHCOS từ [trang lưu trữ ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới với các thông tin sau:

```shell
$ coreos-installer pxe customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/mapper/mpatha \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.firmware=1 \
  --dest-karg-append rd.multipath=default \
  -o custom.img rhcos-<version>-live-initramfs.x86_64.img
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target.
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
  * `/dev/mapper/mpatha`: Chỉ định đường dẫn đến thiết bị. Nếu bạn đang sử dụng multipath (đa đường dẫn), thiết bị multipath sẽ là `/dev/mapper/mpatha`. Trong trường hợp có nhiều thiết bị multipath được kết nối, hoặc để xác định rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN) có sẵn trong thư mục `/dev/disk/by-path`.
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích.
  * `rd.iscsi.firmware=1`: Chỉ định rằng tham số iSCSI sẽ được đọc từ firmware của BIOS.
  * `rd.multipath=default`: Chỉ định việc có kích hoạt tính năng multipath hay không. Đây là tham số tùy chọn. 

Để biết thêm thông tin, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) của `dracut.cmdline`.

##### 2.2.17.4. Các tùy chọn về mạng và liên kết giao diện mạng (bonding) cho quá trình cài đặt qua tệp ISO
Bạn có thể định cấu hình các tùy chọn nâng cao để điều chỉnh quy trình cài đặt thủ công Red Hat Enterprise Linux CoreOS (RHCOS). Các phần tiếp theo sẽ trình bày các ví dụ về tùy chọn mạng cho quá trình cài đặt thông qua tệp ISO.

Nếu cài đặt RHCOS từ tệp ảnh ISO, bạn có thể thêm thủ công các tham số nhân (kernel arguments) trong quá trình khởi động tệp ảnh để định cấu hình mạng cho một nút (node). Nếu không chỉ định tham số mạng nào, DHCP sẽ được kích hoạt trong initramfs khi RHCOS phát hiện cần có kết nối mạng để truy xuất tệp cấu hình Ignition.

> **Quan trọng:** Khi thêm thủ công các tham số mạng, bạn cũng phải thêm tham số nhân `rd.neednet=1` để khởi tạo mạng trong initramfs.

Thông tin dưới đây cung cấp các ví dụ về cách định cấu hình mạng và liên kết giao diện mạng (bonding) trên các nút RHCOS khi cài đặt qua tệp ISO. Các ví dụ này mô tả cách sử dụng các tham số nhân `ip=`, `nameserver=` và `bond=`.

> **Lưu ý:** Thứ tự các tham số nhân khi thêm vào rất quan trọng: `ip=`, `nameserver=`, rồi mới đến `bond=`.

Các tùy chọn mạng này sẽ được truyền tới công cụ `dracut` trong quá trình khởi động hệ thống. Để biết thêm thông tin về các tùy chọn mạng được `dracut` hỗ trợ, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.2.17.4.1. Cấu hình DHCP hoặc địa chỉ IP tĩnh
Bạn có thể cấu hình địa chỉ IP bằng cách sử dụng DHCP hoặc thiết lập một địa chỉ IP tĩnh riêng biệt. Nếu chọn sử dụng IP tĩnh, bạn phải xác định địa chỉ IP của máy chủ DNS trên mỗi nút (node).

Các ví dụ cấu hình trong quy trình này cập nhật địa chỉ IP cho các thành phần sau:

* Địa chỉ IP của nút: 10.10.10.2
* Địa chỉ cổng mặc định (gateway): 10.10.10.254
* Mặt nạ mạng (netmask): 255.255.255.0
* Tên máy chủ (hostname): core0.example.com
* Địa chỉ máy chủ DNS: 4.4.4.41
* Giá trị tự động cấu hình (auto-configuration): none (không). Không cần thực hiện tự động cấu hình khi mạng IP được thiết lập theo kiểu tĩnh.

**Quy trình**

1. Nhập một lệnh tương tự như lệnh dưới đây để cấu hình địa chỉ IP tĩnh:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
nameserver=4.4.4.41
```

2. Hãy nhập một lệnh như lệnh dưới đây để cấu hình địa chỉ IP DHCP:

```
ip=enp1s0:dhcp
```

> **Lưu ý:** Khi bạn sử dụng DHCP để cấu hình địa chỉ IP cho các máy RHCOS, các máy này cũng sẽ nhận thông tin máy chủ DNS thông qua DHCP. Đối với các hệ thống triển khai dựa trên DHCP, bạn có thể xác định địa chỉ máy chủ DNS mà các nút RHCOS sẽ sử dụng thông qua cấu hình trên máy chủ DHCP của mình.

3. Nếu có từ hai giao diện mạng trở lên, hãy vô hiệu hóa DHCP trên các giao diện không sử dụng. Trong ví dụ này, giao diện `enp1s0` được cấu hình với địa chỉ IP tĩnh, còn DHCP bị vô hiệu hóa trên giao diện `enp2s0` (giao diện không được sử dụng):

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
ip=::::core0.example.com:enp2s0:none
```

4. Nếu bạn cần kết hợp các cấu hình DHCP và IP tĩnh trên các hệ thống có nhiều giao diện mạng, hãy chạy lệnh ví dụ sau đây:

```
ip=enp1s0:dhcp
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp2s0:none
```

###### 2.2.17.4.2. Cấu hình địa chỉ IP mà không cần tên máy chủ (hostname) tĩnh

Bạn có thể cấu hình địa chỉ IP mà không cần chỉ định tên máy chủ tĩnh. Nếu người dùng không thiết lập tên máy chủ tĩnh, hệ thống sẽ tự động xác định và thiết lập tên máy chủ này thông qua truy vấn DNS ngược (reverse DNS lookup).

Các ví dụ cấu hình trong quy trình này cập nhật địa chỉ IP cho các thành phần sau:

* Địa chỉ IP của nút (node): `10.10.10.2`
* Địa chỉ cổng mặc định (gateway): `10.10.10.254`
* Mặt nạ mạng (netmask): `255.255.255.0`
* Địa chỉ máy chủ DNS: `4.4.4.41`
* Giá trị tự động cấu hình (auto-configuration): `none`. Không cần thực hiện tự động cấu hình khi mạng IP được thiết lập theo kiểu tĩnh (static).

**Quy trình**

* Để cấu hình địa chỉ IP mà không cần tên máy chủ tĩnh, hãy nhập một lệnh tương tự như lệnh dưới đây:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0::enp1s0:none
nameserver=4.4.4.41
```

###### 2.2.17.4.3. Chỉ định nhiều giao diện mạng và máy chủ DNS
Bạn có thể chỉ định nhiều giao diện mạng bằng cách thiết lập nhiều mục `ip=`. Bạn có thể cung cấp nhiều máy chủ DNS bằng cách thêm một mục `nameserver=` cho mỗi máy chủ.

**Quy trình**

* Để chỉ định nhiều giao diện mạng, bạn có thể nhập một lệnh tương tự như lệnh sau:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
ip=10.10.10.3::10.10.10.254:255.255.255.0:core0.example.com:enp2s0:none
```

* Để cung cấp nhiều máy chủ DNS bằng cách thêm một mục `nameserver=` cho mỗi máy chủ, hãy nhập một lệnh như lệnh sau đây:

```
nameserver=1.1.1.1
nameserver=8.8.8.8
```

###### 2.2.17.4.4. Cấu hình cổng mặc định (default gateway) và tuyến đường (route)
Là một tác vụ tùy chọn, bạn có thể cấu hình các tuyến đường đến những mạng bổ sung bằng cách thiết lập giá trị `rd.route=`.

> **Lưu ý:** Khi cấu hình một hoặc nhiều mạng, bắt buộc phải có một cổng mặc định (default gateway). Nếu cổng của mạng bổ sung khác với cổng của mạng chính, thì cổng mặc định phải là cổng của mạng chính.

**Quy trình**

* Để cấu hình cổng mặc định, hãy nhập lệnh sau:

```
ip=::10.10.10.254::::
```

* Để cấu hình tuyến đường cho một mạng bổ sung, hãy nhập lệnh sau:

```
rd.route=20.20.20.0/24:20.20.20.254:enp2s0
```

###### 2.2.17.4.5. Cấu hình VLAN trên các giao diện riêng lẻ
Là một tác vụ tùy chọn, bạn có thể cấu hình VLAN trên các giao diện riêng lẻ bằng cách sử dụng tham số `vlan=`.

**Quy trình**

* Để cấu hình VLAN trên một giao diện mạng và sử dụng địa chỉ IP tĩnh, hãy chạy lệnh sau:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp2s0.100:none
vlan=enp2s0.100:enp2s0
```

* Để cấu hình VLAN trên một giao diện mạng và sử dụng DHCP, hãy chạy lệnh sau:

```
ip=enp2s0.100:dhcp
vlan=enp2s0.100:enp2s0
```

###### 2.2.17.4.6. Gộp nhiều giao diện mạng thành một giao diện duy nhất
Là một tác vụ tùy chọn, bạn có thể gộp nhiều giao diện mạng thành một giao diện duy nhất bằng cách sử dụng tùy chọn `bond=`.

Ví dụ dưới đây minh họa việc chỉnh sửa tệp `/etc/config/network` và chỉ định cú pháp sau để gộp nhiều giao diện mạng thành một giao diện duy nhất:

```
bond=<name>[:<network_interfaces>][:<options>]
```

* `<name>`: Chỉ định tên của thiết bị liên kết (bonding device), ví dụ: `bond0`. 
* `<network_interfaces>`: Chỉ định danh sách các giao diện vật lý (ethernet) được phân tách bằng dấu phẩy, chẳng hạn như em1, em2. 
* `<options>`: Chỉ định danh sách các tùy chọn liên kết (bonding options) được phân tách bằng dấu phẩy. Hãy chạy lệnh `modinfo bonding` để xem các tùy chọn khả dụng.

Khi tạo một giao diện liên kết (bonded interface) bằng lệnh `bond=`, bạn phải chỉ định cách thức gán địa chỉ IP cùng các thông tin khác cho giao diện đó.

**Quy trình**

* Để cấu hình giao diện liên kết sử dụng DHCP, hãy chỉnh sửa tệp `/etc/config/network` và thiết lập địa chỉ IP cho giao diện liên kết đó là `dhcp`. Ví dụ:

```
ip=bond0:dhcp
```

* Để cấu hình giao diện được liên kết (bonded interface) sử dụng địa chỉ IP tĩnh, hãy chỉnh sửa tệp /etc/config/network rồi nhập địa chỉ IP cụ thể mà bạn muốn cùng các thông tin liên quan. Ví dụ:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:bond0:none
```

###### 2.2.17.4.7. Gộp (bonding) nhiều giao diện mạng SR-IOV vào một giao diện NIC hai cổng
Bạn có thể gộp nhiều giao diện mạng SR-IOV vào một giao diện NIC hai cổng bằng cách sử dụng tùy chọn `bond=`. Hãy đảm bảo thực hiện các bước quy trình trên mỗi nút (node).

**Quy trình**

1. Tạo các hàm ảo (Virtual Functions - VF) SR-IOV theo hướng dẫn trong phần Quản lý thiết bị SR-IOV. Thực hiện theo quy trình trong mục "Gắn các thiết bị mạng SR-IOV vào máy ảo". 
2.  Tạo nhóm gộp (bond), gắn các VF mong muốn vào nhóm đó và thiết lập trạng thái liên kết (link state) cho nhóm theo hướng dẫn trong phần Cấu hình gộp mạng (network bonding). Bạn có thể chọn bất kỳ quy trình nào được mô tả để tạo nhóm gộp. Các ví dụ dưới đây minh họa cú pháp cần sử dụng:
  * Cú pháp để cấu hình một giao diện được gộp là `bond=<name>[:<network_interfaces>][:options]`.
  * Trong đó, `<name>` là tên của thiết bị được gộp (ví dụ: `bond0`); `<network_interfaces>` đại diện cho các hàm ảo (VF) theo tên gọi của chúng trong nhân hệ thống (kernel) — tên này cũng xuất hiện trong kết quả của lệnh `ip link` (ví dụ: `eno1f0`, `eno2f0`); còn `options` là danh sách các tùy chọn gộp được phân tách bằng dấu phẩy. Hãy chạy lệnh `modinfo bonding` để xem các tùy chọn khả dụng. 
  * Khi tạo một giao diện được gộp bằng tùy chọn `bond=`, bạn cần chỉ định cách thức gán địa chỉ IP cũng như các thông tin khác cho giao diện đó. 
    * Để cấu hình giao diện được gộp sử dụng DHCP, hãy đặt địa chỉ IP của nhóm gộp là `dhcp`. Ví dụ:

    ```
    bond=bond0:eno1f0,eno2f0:mode=active-backup
    ip=bond0:dhcp::AA:BB:CC:DD:EE:FF
    ip=eno1f0:none::AA:BB:CC:DD:EE:FF
    ip=eno2f0:none::AA:BB:CC:DD:EE:FF
    ```

    * Để cấu hình giao diện được liên kết (bonded interface) sử dụng địa chỉ IP tĩnh, hãy nhập địa chỉ IP cụ thể mà bạn muốn cùng các thông tin liên quan. Ví dụ:

    ```
    bond=bond0:eno1f0,eno2f0:mode=active-backup
    ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:bond0:none
    ```

3. Tùy chọn: Bạn có thể sử dụng tính năng "network teaming" (nhóm mạng) thay thế cho "bonding" (kết hợp giao diện mạng) bằng cách dùng tham số `team=`. 
  * Cú pháp để cấu hình một giao diện "team" là: `team=name[:network_interfaces]`

  Trong đó, `name` là tên của thiết bị "team" (ví dụ: `team0`), còn `network_interfaces` là danh sách các giao diện vật lý (ethernet) được phân tách bằng dấu phẩy (ví dụ: `em1`, `em2`).

  Sử dụng ví dụ sau để cấu hình một nhóm mạng (network team):

  ```
  team=team0:em1,em2
  ip=team0:dhcp
  ```

###### 2.2.17.4.8. coreos-installer và các tùy chọn khởi động cho quá trình cài đặt qua ISO và PXE
Bạn có thể cài đặt RHCOS bằng cách chạy lệnh `coreos-installer install <các tùy chọn> <thiết bị>` tại dấu nhắc lệnh, sau khi khởi động vào môi trường trực tiếp (live environment) của RHCOS từ một tệp ảnh ISO.

Các bảng dưới đây liệt kê các lệnh phụ, tùy chọn và đối số mà bạn có thể truyền vào lệnh `coreos-installer`.

1. **Bảng 2.27. Các lệnh phụ, tùy chọn dòng lệnh và đối số của coreos-installer**

**Lệnh con install của `coreos-installer`**

| Lệnh con                                      | Mô tả                                    |
|-----------------------------------------------|------------------------------------------|
| `coreos-installer install <options> <device>` | Nhúng cấu hình Ignition vào tệp ảnh ISO. |

**Các tùy chọn cho lệnh con install của `coreos-installer`**

| Tùy chọn                       | Mô tả                                                                                                                                                                                                                                               |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-u`, `--image-url <url>`      | Nhập thủ công URL của hình ảnh.                                                                                                                                                                                                                     |
| `-f`, `--image-file <path>`    | Chỉ định thủ công một tệp hình ảnh cục bộ. Được sử dụng cho mục đích gỡ lỗi.                                                                                                                                                                        |
| `-i`, `--ignition-file <path>` | Nhúng cấu hình Ignition từ một tệp tin.                                                                                                                                                                                                             |
| `-I`, `--ignition-url <URL>`   | Nhúng cấu hình Ignition từ một URL.                                                                                                                                                                                                                 |
| `--ignition-hash <digest>`     | Tóm tắt giá trị kiểu dữ liệu (`type-value`) của cấu hình Ignition.                                                                                                                                                                                  |
| `-p`, `--platform <name>`      | Ghi đè ID nền tảng Ignition cho hệ thống đã cài đặt.                                                                                                                                                                                                |
| `--console <spec>`             | Thiết lập bảng điều khiển (console) cho nhân hệ điều hành (kernel) và bộ nạp khởi động (boot loader) của hệ thống đã cài đặt. Để biết thêm thông tin về định dạng của `<spec>`                                                                      |
| `--append-karg <arg>…`         | Thêm một tham số nhân (kernel argument) mặc định vào hệ thống đã cài đặt.                                                                                                                                                                           |
| `--delete-karg <arg>…`         | Xóa một tham số nhân mặc định khỏi hệ thống đã cài đặt.                                                                                                                                                                                             |
| `-n`, `--copy-network`         | Sao chép cấu hình mạng từ môi trường cài đặt. Quan trọng: Tùy chọn `--copy-network` chỉ sao chép cấu hình mạng nằm trong thư mục `/etc/NetworkManager/system-connections`. Cụ thể, tùy chọn này không sao chép tên máy chủ (hostname) của hệ thống. |
| `--network-dir <path>`         | Dùng với tùy chọn `-n`. Giá trị mặc định là `/etc/NetworkManager/system-connections/`.                                                                                                                                                              |
| `--save-partlabel <lx>..`      | Lưu các phân vùng khớp với mẫu nhãn này.                                                                                                                                                                                                            |
| `--save-partindex <id>…`       | Lưu các phân vùng có số hoặc phạm vi này.                                                                                                                                                                                                           |
| `--insecure`                   | Bỏ qua việc xác minh chữ ký ảnh RHCOS.                                                                                                                                                                                                              |
| `--insecure-ignition`          | Cho phép URL Ignition không có HTTPS hoặc dấu thăng (#).                                                                                                                                                                                            |
| `--architecture <name>`        | Kiến trúc CPU đích. Các giá trị hợp lệ là `x86_64` và `aarch64`.                                                                                                                                                                                    |
| `--preserve-on-error`          | Không xóa bảng phân vùng khi xảy ra lỗi.                                                                                                                                                                                                            |
| `-h`, `--help`                 | In thông tin trợ giúp.                                                                                                                                                                                                                              |

**Đối số của lệnh phụ install trong `coreos-installer`**

| Đối số     | Mô tả          |
|------------|----------------|
| `<device>` | Thiết bị đích. |

**Các lệnh con ISO của `coreos-installer`**

| Lệnh con                                                     | Mô tả                                                                                 |
|--------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `coreos-installer iso customize <options> <ISO_image>`       | Tùy chỉnh một tệp ISO live của RHCOS.                                                 |
| `coreos-installer iso reset <options> <ISO_image>`           | Khôi phục tệp ảnh ISO trực tiếp (live ISO image) của RHCOS về các thiết lập mặc định. |
| `coreos-installer iso ignition remove <options> <ISO_image>` | Xóa cấu hình Ignition được nhúng khỏi tệp ảnh ISO.                                    |

**Các tùy chọn của lệnh con customize trong `coreos-installer` ISO**

| Tùy chọn                      | Mô tả                                                                                                                                                           |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--dest-ignition <path>`      | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho hệ thống đích.                                                                  |
| `--dest-console <spec>`       | Hãy chỉ định nhân hệ điều hành và bảng điều khiển bộ nạp khởi động cho hệ thống đích.                                                                           |
| `--dest-device <path>`        | Cài đặt và ghi đè lên thiết bị đích đã chỉ định.                                                                                                                |
| `--dest-karg-append <arg>`    | Thêm một tham số nhân (kernel argument) vào mỗi lần khởi động của hệ thống đích.                                                                                |
| `--dest-karg-delete <arg>`    | Xóa một tham số nhân (kernel argument) khỏi mỗi lần khởi động của hệ thống đích.                                                                                |
| `--network-keyfile <path>`    | Cấu hình mạng bằng cách sử dụng tệp khóa (keyfile) NetworkManager được chỉ định cho các hệ thống trực tiếp (live system) và hệ thống đích (destination system). |
| `--ignition-ca <path>`        | Chỉ định một cơ quan cấp chứng chỉ TLS bổ sung để Ignition tin tưởng.                                                                                           |
| `--pre-install <path>`        | Chạy tập lệnh được chỉ định trước khi cài đặt.                                                                                                                  |
| `--post-install <path>`       | Chạy tập lệnh được chỉ định sau khi cài đặt.                                                                                                                    |
| `--installer-config <path>`   | Áp dụng tệp cấu hình trình cài đặt đã chỉ định.                                                                                                                 |
| `--live-ignition <path>`      | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho môi trường thực tế (live environment).                                          |
| `--live-karg-append <arg>`    | Thêm một tham số nhân (kernel argument) vào mỗi lần khởi động môi trường chạy trực tiếp (live environment).                                                     |
| `--live-karg-delete <arg>`    | Xóa một tham số nhân (kernel argument) khỏi mỗi lần khởi động môi trường chạy trực tiếp (live environment).                                                     |
| `--live-karg-replace <k=o=n>` | Thay thế một tham số nhân (kernel argument) trong mỗi lần khởi động môi trường chạy trực tiếp (live environment), theo định dạng `key=old=new`.                 |
| `-f`, `--force`               | Ghi đè lên cấu hình Ignition hiện có.                                                                                                                           |
| `-o`, `--output <path>`       | Ghi tệp ISO vào một tệp đầu ra mới.                                                                                                                             |
| `-h, --help`                  | In thông tin trợ giúp.                                                                                                                                          |

**Các lệnh con PXE của `coreos-installer`**

| Lệnh con                                                      | Mô tả                                                                 |
|---------------------------------------------------------------|-----------------------------------------------------------------------|
| `coreos-installer pxe customize <options> <path>`             | Tùy chỉnh cấu hình khởi động PXE trực tiếp (live PXE boot) cho RHCOS. |
| `coreos-installer pxe ignition wrap <options>`                | Đóng gói cấu hình Ignition vào một ảnh (image).                       |
| `coreos-installer pxe ignition unwrap <options> <image_name>` | Hiển thị cấu hình Ignition đã được bao gói trong một hình ảnh.        |

**Các tùy chọn cho lệnh con tùy chỉnh (customize) trong `coreos-installer` PXE**

| Tùy chọn                    | Mô tả                                                                                                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--dest-ignition <path>`    | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho hệ thống đích.                                                                  |
| `--dest-console <spec>`     | Hãy chỉ định nhân hệ điều hành và bảng điều khiển bộ nạp khởi động cho hệ thống đích.                                                                           |
| `--dest-device <path>`      | Cài đặt và ghi đè lên thiết bị đích đã chỉ định.                                                                                                                |
| `--network-keyfile <path>`  | Cấu hình mạng bằng cách sử dụng tệp khóa (keyfile) NetworkManager được chỉ định cho các hệ thống trực tiếp (live system) và hệ thống đích (destination system). |
| `--ignition-ca <path>`      | Chỉ định một cơ quan cấp chứng chỉ TLS bổ sung để Ignition tin tưởng.                                                                                           |
| `--pre-install <path>`      | Chạy tập lệnh được chỉ định trước khi cài đặt.                                                                                                                  |
| `--post-install <path>`     | Chạy tập lệnh được chỉ định sau khi cài đặt.                                                                                                                    |
| `--installer-config <path>` | Áp dụng tệp cấu hình trình cài đặt đã chỉ định.                                                                                                                 |
| `--live-ignition <path>`    | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho môi trường thực tế (live environment).                                          |
| `-o`, `--output <path>`     | Ghi initramfs vào một tệp đầu ra mới. Lưu ý: Tùy chọn này là bắt buộc đối với các môi trường PXE.                                                               |
| `-h`, `--help`              | In thông tin trợ giúp.                                                                                                                                          |

> Lưu ý rằng không phải tất cả các tùy chọn này đều được chấp nhận bởi mọi lệnh phụ.

Bạn có thể tự động áp dụng các tùy chọn của `coreos-installer` ngay khi khởi động bằng cách truyền các tham số khởi động `coreos.inst` vào trình cài đặt trực tiếp (live installer) của RHCOS. Các tham số này được bổ sung bên cạnh những tham số khởi động tiêu chuẩn. 

* Đối với các bản cài đặt từ tệp ISO, bạn có thể thêm các tùy chọn `coreos.inst` bằng cách ngắt quá trình khởi động tự động tại menu của trình khởi động (boot loader). Bạn có thể thực hiện việc này bằng cách nhấn phím TAB khi tùy chọn menu "RHEL CoreOS (Live)" đang được chọn. 
* Đối với các bản cài đặt qua PXE hoặc iPXE, các tùy chọn `coreos.inst` cần được thêm vào dòng `APPEND` trước khi trình cài đặt trực tiếp RHCOS khởi động.

Bảng dưới đây liệt kê các tùy chọn khởi động `coreos.inst` dành cho trình cài đặt trực tiếp RHCOS trong các trường hợp cài đặt từ tệp ISO hoặc qua PXE.

2. **Bảng 2.28. Các tùy chọn khởi động coreos.inst**

| Đối số                       | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `coreos.inst.install_dev`    | Bắt buộc. Thiết bị khối trên hệ thống dùng để cài đặt. Lưu ý: Bạn nên sử dụng đường dẫn đầy đủ, chẳng hạn như `/dev/sda`, mặc dù việc chỉ dùng `sda` cũng được chấp nhận.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `coreos.inst.ignition_url`   | Tùy chọn: URL của cấu hình Ignition cần nhúng vào hệ thống được cài đặt. Nếu không chỉ định URL, sẽ không có cấu hình Ignition nào được nhúng vào. Chỉ hỗ trợ các giao thức HTTP và HTTPS.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `coreos.inst.save_partlabel` | Tùy chọn: Danh sách các phân vùng cần giữ lại trong quá trình cài đặt, được phân tách bằng dấu phẩy. Có thể sử dụng các ký tự đại diện (wildcard) kiểu glob. Các phân vùng được chỉ định không nhất thiết phải tồn tại từ trước.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `coreos.inst.save_partindex` | Tùy chọn: Các chỉ số phân vùng cần giữ lại trong quá trình cài đặt, được phân tách bằng dấu phẩy. Có thể sử dụng các dải giá trị dạng `m-n`, trong đó một trong hai giá trị `m` hoặc `n` có thể được lược bỏ. Các phân vùng được chỉ định không nhất thiết phải tồn tại.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `coreos.inst.insecure`       | Tùy chọn: Cho phép tệp ảnh hệ điều hành (OS image) được chỉ định bởi `coreos.inst.image_url` không cần phải có chữ ký số.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `coreos.inst.image_url`      | Tùy chọn: Tải xuống và cài đặt hình ảnh (image) RHCOS được chỉ định. - Tham số này không nên được sử dụng trong các môi trường thực tế (production) mà chỉ dành cho mục đích gỡ lỗi (debugging). - Mặc dù tham số này có thể được dùng để cài đặt một phiên bản RHCOS không khớp với phương tiện cài đặt (live media) hiện có, bạn vẫn nên sử dụng phương tiện cài đặt tương ứng với phiên bản mà bạn muốn cài đặt. - Nếu sử dụng `coreos.inst.image_url`, bạn bắt buộc phải sử dụng kèm với `coreos.inst.insecure`. - Lý do là vì các phương tiện cài đặt trên phần cứng vật lý (bare-metal media) dành cho OpenShift Container Platform không được ký bằng chữ ký GPG. - Chỉ hỗ trợ các giao thức HTTP và HTTPS. |
| `coreos.inst.skip_reboot`    | Tùy chọn: Hệ thống sẽ không khởi động lại sau khi cài đặt. Khi quá trình cài đặt hoàn tất, bạn sẽ nhận được thông báo cho phép kiểm tra các diễn biến trong suốt quá trình cài đặt. Tham số này không nên được sử dụng trong môi trường thực tế (production) mà chỉ dành cho mục đích gỡ lỗi.                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `coreos.inst.platform_id`    | Tùy chọn: ID nền tảng Ignition của nền tảng mà trên đó hình ảnh RHCOS đang được cài đặt. Giá trị mặc định là `metal`. Tùy chọn này xác định xem có cần yêu cầu cấu hình Ignition từ nhà cung cấp dịch vụ đám mây (ví dụ: VMware) hay không. Ví dụ: `coreos.inst.platform_id=vmware`.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `ignition.config.url`        | Tùy chọn: URL của cấu hình Ignition dùng cho quá trình khởi động trực tiếp (live boot). Ví dụ, thông số này có thể được sử dụng để tùy chỉnh cách thức gọi `coreos-installer`, hoặc để thực thi mã lệnh trước hay sau quá trình cài đặt. Thông số này khác với `coreos.inst.ignition_url` — vốn là cấu hình Ignition dành cho hệ thống sau khi đã được cài đặt.                                                                                                                                                                                                                                                                                                                                                    |

##### 2.2.17.5. Kích hoạt tính năng đa đường dẫn (multipathing) bằng các tham số nhân (kernel arguments) trên RHCOS
RHCOS hỗ trợ tính năng đa đường dẫn (multipathing) trên đĩa chính, giúp tăng cường khả năng chống chịu trước các lỗi phần cứng, từ đó nâng cao tính sẵn sàng của máy chủ (host).

Bạn có thể kích hoạt tính năng đa đường dẫn ngay tại thời điểm cài đặt đối với các nút (node) được cung cấp trong OpenShift Container Platform phiên bản 4.8 trở lên. Mặc dù vẫn có thể kích hoạt tính năng này sau khi cài đặt thông qua cấu hình máy (machine config), Red Hat khuyến nghị nên thực hiện ngay trong quá trình cài đặt.

Trong các thiết lập mà mọi thao tác I/O trên các đường dẫn không được tối ưu hóa (non-optimized paths) đều dẫn đến lỗi hệ thống I/O, bạn bắt buộc phải kích hoạt tính năng đa đường dẫn ngay từ bước cài đặt.

> **Quan trọng:** Trên các hệ thống IBM Z® và IBM® LinuxONE, bạn chỉ có thể kích hoạt tính năng đa đường dẫn nếu đã cấu hình cho cụm (cluster) của mình ngay trong quá trình cài đặt. Để biết thêm thông tin, hãy xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform" trong tài liệu hướng dẫn cài đặt cụm trên z/VM dành cho IBM Z® và IBM® LinuxONE.

Quy trình dưới đây hướng dẫn cách kích hoạt tính năng đa đường dẫn tại thời điểm cài đặt bằng cách thêm các tham số nhân (kernel arguments) vào lệnh `coreos-installer install`, đảm bảo hệ thống sau khi cài đặt sẽ sử dụng tính năng này ngay từ lần khởi động đầu tiên.

> **Lưu ý:** OpenShift Container Platform không hỗ trợ kích hoạt tính năng đa đường dẫn như một thao tác vận hành sau cài đặt (day-2 activity) đối với các nút đã được nâng cấp từ phiên bản 4.6 trở về trước.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã xem qua nội dung về việc cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform.

**Quy trình**

1. Để kích hoạt tính năng đa đường dẫn và khởi chạy tiến trình nền (daemon) `multipathd`, hãy thực thi lệnh sau trên máy chủ cài đặt (installation host):

```shell
$ mpathconf --enable && systemctl start multipathd.service
```

  * Tùy chọn: Nếu khởi động qua PXE hoặc ISO, bạn có thể kích hoạt tính năng đa đường dẫn (multipath) bằng cách thêm tham số `rd.multipath=default` vào dòng lệnh kernel.

2. Thêm các tham số kernel bằng cách gọi chương trình coreos-installer:
  * Nếu chỉ có một thiết bị đa đường dẫn (multipath device) được kết nối với máy, thiết bị đó sẽ khả dụng tại đường dẫn /dev/mapper/mpatha. Ví dụ:

  ```shell
  $ coreos-installer install /dev/mapper/mpatha \
    --ignition-url=http://host/worker.ign \
    --append-karg rd.multipath=default \
    --append-karg root=/dev/disk/by-label/dm-mpath-root \
    --append-karg rw
  ```

    * `/dev/mapper/mpatha`: Cho biết đường dẫn của thiết bị đa đường dẫn (multipathed device) duy nhất.

  * Nếu có nhiều thiết bị đa đường dẫn (multipath) được kết nối với máy, thay vì sử dụng `/dev/mapper/mpatha`, Red Hat khuyến nghị sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN). Liên kết tượng trưng này có sẵn trong thư mục `/dev/disk/by-id`. Ví dụ:

  ```shell
  $ coreos-installer install /dev/disk/by-id/wwn-<wwn_ID> \
    --ignition-url=http://host/worker.ign \
    --append-karg rd.multipath=default \
    --append-karg root=/dev/disk/by-label/dm-mpath-root \
    --append-karg rw
  ```

  Trong đó: `<wwn_ID>` cho biết WWN ID của thiết bị đa đường dẫn (multipathed device) đích. Ví dụ: 0xx194e957fcedb4841. 

  Liên kết tượng trưng (symlink) này cũng có thể được sử dụng làm tham số nhân (kernel argument) `coreos.inst.install_dev` khi dùng các tham số `coreos.inst.*` đặc biệt để điều hướng trình cài đặt trực tiếp (live installer). Để biết thêm thông tin, hãy xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) của OpenShift Container Platform".

3. Khởi động lại vào hệ thống đã cài đặt.
4. Kiểm tra xem các tham số nhân (kernel arguments) đã có hiệu lực hay chưa bằng cách truy cập vào một trong các nút worker và liệt kê các tham số dòng lệnh của nhân (nằm trong tệp /proc/cmdline trên máy chủ):

```shell
$ oc debug node/ip-10-0-141-105.ec2.internal
```

Ví dụ đầu ra

```
Starting pod/ip-10-0-141-105ec2internal-debug ...
To use host binaries, run `chroot /host`

sh-4.2# cat /host/proc/cmdline
...
rd.multipath=default root=/dev/disk/by-label/dm-mpath-root
...

sh-4.2# exit
```

Bạn sẽ thấy các tham số nhân (kernel arguments) đã được thêm vào.

###### 2.2.17.5.1. Kích hoạt tính năng đa đường dẫn (multipathing) trên các đĩa phụ
RHCOS cũng hỗ trợ tính năng đa đường dẫn (multipathing) trên các đĩa phụ. Thay vì sử dụng các tham số nhân (kernel arguments), bạn có thể dùng Ignition để kích hoạt tính năng này cho đĩa phụ ngay tại thời điểm cài đặt.

**Điều kiện tiên quyết**

* Bạn đã đọc phần Phân vùng đĩa (Disk partitioning). 
* Bạn đã đọc phần Kích hoạt tính năng đa đường dẫn bằng các tham số nhân trên RHCOS. 
* Bạn đã cài đặt tiện ích Butane.

**Quy trình**

1. Tạo một tệp cấu hình Butane với thông tin tương tự như ví dụ dưới đây:

Ví dụ: `multipath-config.bu`

```yaml
variant: openshift
version: 4.22.0
systemd:
  units:
    - name: mpath-configure.service
      enabled: true
      contents: |
        [Unit]
        Description=Configure Multipath on Secondary Disk
        ConditionFirstBoot=true
        ConditionPathExists=!/etc/multipath.conf
        Before=multipathd.service
        DefaultDependencies=no

        [Service]
        Type=oneshot
        ExecStart=/usr/sbin/mpathconf --enable

        [Install]
        WantedBy=multi-user.target
    - name: mpath-var-lib-container.service
      enabled: true
      contents: |
        [Unit]
        Description=Set Up Multipath On /var/lib/containers
        ConditionFirstBoot=true
        Requires=dev-mapper-mpatha.device
        After=dev-mapper-mpatha.device
        After=ostree-remount.service
        Before=kubelet.service
        DefaultDependencies=no

        [Service]
        Type=oneshot
        ExecStart=/usr/sbin/mkfs.xfs -L containers -m reflink=1 /dev/mapper/mpatha
        ExecStart=/usr/bin/mkdir -p /var/lib/containers

        [Install]
        WantedBy=multi-user.target
    - name: var-lib-containers.mount
      enabled: true
      contents: |
        [Unit]
        Description=Mount /var/lib/containers
        After=mpath-var-lib-containers.service
        Before=kubelet.service

        [Mount]
        What=/dev/disk/by-label/dm-mpath-containers
        Where=/var/lib/containers
        Type=xfs

        [Install]
        WantedBy=multi-user.target
```

Trong đó:

  * `Before=multipathd.service`: Chỉ định rằng cấu hình phải được thiết lập trước khi khởi chạy tiến trình nền (daemon) multipath.
  * `ExecStart=/usr/sbin/mpathconf`: Chỉ định việc khởi chạy tiện ích `mpathconf`.
  * `ConditionFirstBoot=true`: Được đặt thành giá trị true.
  * `[Service]`: Chỉ định việc tạo hệ thống tệp và thư mục `/var/lib/containers`.
  * `Before=kubelet.service`: Chỉ định rằng thiết bị phải được gắn (mount) trước khi khởi động bất kỳ nút (node) nào.
  * `[Mount]`: Chỉ định việc gắn thiết bị vào điểm gắn `/var/lib/containers`. Vị trí này không được phép là một liên kết tượng trưng (symlink).

2. Tạo cấu hình Ignition bằng cách chạy lệnh sau:

```shell
$ butane --pretty --strict multipath-config.bu > multipath-config.ign
```

3. Tiếp tục thực hiện các bước còn lại trong quy trình cài đặt RHCOS ở lần khởi động đầu tiên.

> **Quan trọng:** Không thêm các tham số nhân (kernel arguments) `rd.multipath` hoặc `root` vào dòng lệnh (CLI) trong quá trình cài đặt, trừ khi ổ đĩa chính cũng được cấu hình đa đường dẫn (multipath).

##### 2.2.17.6. Cài đặt thủ công RHCOS trên thiết bị khởi động iSCSI
Bạn có thể cài đặt thủ công RHCOS trên một đích iSCSI (iSCSI target).

**Điều kiện tiên quyết**

* Bạn đang ở trong môi trường chạy trực tiếp (live environment) của RHCOS. 
* Bạn có một đích iSCSI mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Gắn (mount) đích iSCSI từ môi trường chạy trực tiếp bằng cách thực thi lệnh sau:

```shell
$ iscsiadm \
  --mode discovery \
  --type sendtargets
  --portal <IP_address> \
  --login
```

Trong đó: `<IP_address>` chỉ định địa chỉ IP của cổng đích (target portal).

2. Cài đặt RHCOS vào đích iSCSI bằng cách chạy lệnh sau và sử dụng các tham số nhân (kernel arguments) cần thiết, chẳng hạn như:

```shell
$ coreos-installer install \
  /dev/disk/by-path/ip-<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
  --append-karg rd.iscsi.initiator=<initiator_iqn> \
  --append.karg netroot=<target_iqn> \
  --console ttyS0,115200n8
  --ignition-file <path_to_file>
```

Trong đó:

  * `/dev/disk/by-path/ip`: Xác định vị trí cài đặt. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng tương ứng, nút iSCSI đích ở định dạng IQN và số đơn vị logic (LUN) iSCSI.
  * `<initiator_iqn>`: Xác định tên của iSCSI initiator (bên khởi tạo) – hay còn gọi là client – ở định dạng IQN. Initiator này sẽ thiết lập một phiên kết nối tới iSCSI target (đích đến).
  * `<target_iqn>`: Xác định tên của iSCSI target (đích đến) – hay còn gọi là server – ở định dạng IQN. 

Để biết thêm thông tin về các tùy chọn iSCSI được dracut hỗ trợ, hãy xem trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

3. Ngắt kết nối đĩa iSCSI bằng lệnh sau:

```shell
$ iscsiadm --mode node --logoutall=all
```

Quy trình này cũng có thể được thực hiện bằng cách sử dụng các lệnh phụ `coreos-installer iso customize` hoặc `coreos-installer pxe customize`.

##### 2.2.17.7. Cài đặt RHCOS trên thiết bị khởi động iSCSI sử dụng iBFT
Trên một máy hoàn toàn không có ổ đĩa cục bộ, các giá trị iSCSI target (đích iSCSI) và iSCSI initiator (bên khởi tạo iSCSI) có thể được truyền thông qua iBFT. Tính năng đa đường dẫn (multipathing) cho iSCSI cũng được hỗ trợ.

**Các điều kiện tiên quyết**

* Bạn đang ở trong môi trường chạy trực tiếp (live environment) của RHCOS. 
* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
* Tùy chọn: Bạn đã cấu hình tính năng đa đường dẫn (multipathing) cho iSCSI target của mình.

**Quy trình**

1. Gắn (mount) iSCSI target từ môi trường chạy trực tiếp bằng cách thực hiện lệnh sau:

```shell
$ iscsiadm \
  --mode discovery \
  --type sendtargets
  --portal <IP_address> \
  --login
```

Trong đó: `<IP_address>` chỉ định địa chỉ IP của cổng mục tiêu.

2. Tùy chọn: bật tính năng đa đường dẫn (multipathing) và khởi chạy tiến trình nền (daemon) bằng lệnh sau:

```shell
$ mpathconf --enable && systemctl start multipathd.service
```

3. Cài đặt RHCOS vào đích iSCSI bằng cách chạy lệnh sau và sử dụng các tham số nhân (kernel arguments) cần thiết, chẳng hạn như:

```shell
$ coreos-installer install \
  /dev/mapper/mpatha \
  --append-karg rd.iscsi.firmware=1 \
  --append-karg rd.multipath=default \
  --console ttyS0 \
  --ignition-file <path_to_file>
```

Trong đó:

  * `/dev/mapper/mpatha`: Chỉ định đường dẫn của một thiết bị đa đường dẫn (multipathed device). Nếu có nhiều thiết bị đa đường dẫn được kết nối, hoặc để xác định rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) theo tên định danh toàn cầu (World Wide Name - WWN) có sẵn trong thư mục `/dev/disk/by-path`.
  * `rd.iscsi.firmware=1`: Chỉ định rằng tham số iSCSI sẽ được đọc từ firmware của BIOS.
  * `rd.multipath=default`: Chỉ định kích hoạt tính năng đa đường dẫn (multipathing). Đây là một tham số tùy chọn. 

Để biết thêm thông tin về các tùy chọn iSCSI được dracut hỗ trợ, hãy xem trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

4. Ngắt kết nối đĩa iSCSI:

```shell
$ iscsiadm --mode node --logout=all
```

Bạn cũng có thể thực hiện quy trình này bằng cách sử dụng các lệnh phụ `coreos-installer iso customize` hoặc `coreos-installer pxe customize`.

#### 2.2.18. Chờ quá trình khởi động ban đầu (bootstrap) hoàn tất
Sau khi các nút (node) trong cụm (cluster) của bạn khởi động lần đầu vào môi trường RHCOS cố định đã được cài đặt trên ổ đĩa, hãy chờ cho quá trình khởi động ban đầu (bootstrap) của OpenShift Container Platform hoàn tất.

Thông tin cấu hình được cung cấp thông qua các tệp cấu hình Ignition sẽ được sử dụng để khởi tạo quy trình bootstrap và cài đặt OpenShift Container Platform trên các máy chủ.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn đã tải xuống chương trình cài đặt và tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã cài đặt RHCOS trên các máy chủ trong cụm và cung cấp các tệp cấu hình Ignition do chương trình cài đặt OpenShift Container Platform tạo ra. 
* Các máy chủ của bạn có quyền truy cập trực tiếp vào internet hoặc có sẵn proxy HTTP hoặc HTTPS.

**Quy trình**

1. Theo dõi quá trình khởi động ban đầu (bootstrap):

```shell
$ ./openshift-install --dir <installation_directory> wait-for bootstrap-complete \
  --log-level=info
```

Trong đó:

  * `<installation_directory>`: Chỉ định đường dẫn đến thư mục lưu trữ các tệp cài đặt.
  * `--log-level=info`: Có thể thay thế `info` bằng `warn`, `debug` hoặc `error` để xem các chi tiết cài đặt khác nhau. 

Ví dụ đầu ra

```
INFO Waiting up to 20m0s for the Kubernetes API at https://api.test.example.com:6443...
INFO API v1.35.4 up
INFO Waiting up to 1h0m0s for bootstrapping to complete...
INFO It is now safe to remove the bootstrap resources
```

Thời gian chờ hoàn tất quá trình khởi động ban đầu (bootstrapping) khác nhau tùy theo từng nền tảng.

Lệnh này thực thi thành công khi máy chủ API Kubernetes báo hiệu rằng quá trình khởi động ban đầu đã hoàn tất trên các máy thuộc mặt phẳng điều khiển (control plane).

2. Sau khi quá trình khởi động ban đầu (bootstrap) hoàn tất, hãy loại bỏ máy bootstrap khỏi bộ cân bằng tải (load balancer).

> **Quan trọng:** Tại thời điểm này, bạn bắt buộc phải loại bỏ máy bootstrap khỏi bộ cân bằng tải. Bạn cũng có thể xóa hoặc định dạng lại chính máy bootstrap đó.

#### 2.2.19. Đăng nhập vào cụm bằng CLI
Để đăng nhập vào cụm của bạn với tư cách là người dùng hệ thống mặc định, hãy xuất tệp kubeconfig. Cấu hình này cho phép CLI xác thực và kết nối với máy chủ API cụ thể đã được tạo trong quá trình cài đặt OpenShift Container Platform.

Tệp kubeconfig gắn liền với một cụm cụ thể và được OpenShift Container Platform tạo ra trong quá trình cài đặt.

**Điều kiện tiên quyết**

* Bạn đã triển khai một cụm OpenShift Container Platform. 
* Bạn đã cài đặt OpenShift CLI (oc).

**Quy trình**

1. Xuất thông tin xác thực kubeadmin bằng cách chạy lệnh sau:

```shell
$ export KUBECONFIG=<installation_directory>/auth/kubeconfig
```

Trong đó: `<installation_directory>` xác định đường dẫn đến thư mục lưu trữ các tệp cài đặt.

2. Hãy xác minh rằng bạn có thể chạy thành công các lệnh `oc` bằng cách sử dụng cấu hình đã xuất, thông qua việc thực thi lệnh sau:

```shell
$ oc whoami
```

Ví dụ đầu ra

```
system:admin
```

**Các bước tiếp theo**

  * "Tùy chỉnh cụm của bạn"
  * "Báo cáo tình trạng từ xa"

#### 2.2.20. Phê duyệt các yêu cầu ký chứng chỉ cho các máy của bạn
Để cho phép các máy mới được thêm vào tham gia cụm OpenShift Container Platform của bạn, hãy xác nhận rằng cụm đã phê duyệt các yêu cầu ký chứng chỉ (CSR) đang chờ xử lý, hoặc tự bạn thực hiện phê duyệt các yêu cầu đó. Hãy phê duyệt các yêu cầu từ phía máy khách (client) trước, sau đó mới đến các yêu cầu từ phía máy chủ (server).

**Điều kiện tiên quyết**

* Bạn đã thêm các máy vào cụm của mình.

**Quy trình**

1. Xác nhận rằng cụm đã nhận diện được các máy đó:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME      STATUS    ROLES   AGE  VERSION
master-0  Ready     master  63m  v1.35.4
master-1  Ready     master  63m  v1.35.4
master-2  Ready     master  64m  v1.35.4
```

Kết quả đầu ra liệt kê tất cả các máy mà bạn đã tạo.

> **Lưu ý:** Kết quả đầu ra nêu trên có thể chưa bao gồm các nút tính toán (compute node) cho đến khi bạn phê duyệt một số yêu cầu ký chứng chỉ (CSR).

2. Xem lại các yêu cầu CSR đang chờ xử lý và đảm bảo rằng bạn thấy các yêu cầu từ máy khách với trạng thái `Pending` hoặc `Approved` đối với từng máy mà bạn đã thêm vào cụm:

```shell
$ oc get csr
```

Ví dụ đầu ra

```
NAME        AGE     REQUESTOR                                                                   CONDITION
csr-8b2br   15m     system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
csr-8vnps   15m     system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
...
```

Trong ví dụ này, có hai máy đang tham gia vào cụm (cluster). Bạn có thể thấy nhiều yêu cầu ký chứng chỉ (CSR) đã được phê duyệt hơn trong danh sách.

3. Nếu các CSR chưa được phê duyệt, sau khi tất cả các CSR dành cho các máy bạn vừa thêm đều ở trạng thái "Đang chờ xử lý" (Pending), hãy tiến hành phê duyệt các CSR đó cho các máy trong cụm của bạn:

> **Lưu ý:** Bạn phải phê duyệt các CSR trong vòng một giờ kể từ khi thêm máy vào cụm. Nếu không thực hiện trong khoảng thời gian này, các chứng chỉ sẽ tự động xoay vòng (rotate), dẫn đến việc mỗi nút (node) sẽ có nhiều hơn hai chứng chỉ. Khi đó, bạn sẽ phải phê duyệt tất cả các chứng chỉ này. Sau khi bạn phê duyệt CSR của máy khách (client CSR), kubelet sẽ tạo thêm một CSR thứ hai cho chứng chỉ phục vụ (serving certificate); CSR này cũng cần được phê duyệt thủ công. Sau đó, thành phần "machine-approver" sẽ tự động phê duyệt các yêu cầu gia hạn chứng chỉ phục vụ tiếp theo, miễn là kubelet yêu cầu chứng chỉ mới với các tham số tương tự.

> **Lưu ý:** Đối với các cụm chạy trên những nền tảng không hỗ trợ "machine API" (ví dụ: hạ tầng vật lý - bare metal - hoặc hạ tầng do người dùng tự cung cấp), bạn cần triển khai một cơ chế tự động phê duyệt các yêu cầu ký chứng chỉ phục vụ (CSR) từ kubelet. Nếu bạn không phê duyệt yêu cầu này, các lệnh như `oc exec`, `oc rsh` và `oc logs` sẽ không thể thực thi thành công, vì API server yêu cầu phải có chứng chỉ phục vụ khi kết nối với kubelet. Mọi thao tác liên lạc với điểm cuối (endpoint) của kubelet đều đòi hỏi chứng chỉ này phải được phê duyệt. Cơ chế đó cần theo dõi các CSR mới, xác nhận rằng CSR được gửi bởi tài khoản dịch vụ (service account) `node-bootstrapper` (thuộc nhóm `system:node` hoặc `system:admin`), đồng thời xác thực danh tính của nút đó. 

  * Để phê duyệt từng CSR một, hãy chạy lệnh sau cho mỗi CSR hợp lệ:

  ```shell
  $ oc adm certificate approve <csr_name>
  ```

  Trong đó: `<csr_name>` chỉ định tên của một CSR từ danh sách các CSR hiện có.

  * Để phê duyệt tất cả các CSR đang chờ xử lý, hãy chạy lệnh sau:

  ```shell
  $ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | \
    xargs --no-run-if-empty oc adm certificate approve
  ```

  > **Lưu ý:** Một số Operator có thể chưa khả dụng cho đến khi bạn phê duyệt các yêu cầu ký chứng chỉ (CSR). Mỗi node sẽ gửi hai CSR, vì vậy có thể bạn sẽ cần chạy lệnh phê duyệt CSR nhiều lần.

4. Sau khi phê duyệt các yêu cầu từ phía client, hãy xem xét các yêu cầu từ phía server đối với từng máy mà bạn đã thêm vào cụm (cluster):

```shell
$ oc get csr
```

Ví dụ đầu ra

```
NAME        AGE     REQUESTOR                                                                   CONDITION
csr-bfd72   5m26s   system:node:ip-10-0-50-126.us-east-2.compute.internal                       Pending
csr-c57lv   5m26s   system:node:ip-10-0-95-157.us-east-2.compute.internal                       Pending
...
```

5. Nếu các yêu cầu ký chứng chỉ (CSR) còn lại chưa được phê duyệt và đang ở trạng thái "Đang chờ xử lý" (Pending), hãy phê duyệt các CSR đó cho các máy trong cụm (cluster) của bạn:
  * Để phê duyệt từng yêu cầu một, hãy chạy lệnh sau cho mỗi CSR hợp lệ:

  ```shell
  $ oc adm certificate approve <csr_name>
  ```

  Trong đó: `<csr_name>` chỉ định tên của một CSR từ danh sách các CSR hiện có.

  * Để phê duyệt tất cả các CSR đang chờ xử lý, hãy chạy lệnh sau:

  ```shell
  $ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | \
    xargs oc adm certificate approve
  ```

6. Sau khi bạn phê duyệt tất cả các CSR của máy khách và máy chủ, các máy đó sẽ chuyển sang trạng thái Sẵn sàng (Ready). Hãy xác minh điều này bằng cách chạy lệnh sau:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME      STATUS    ROLES   AGE  VERSION
master-0  Ready     master  73m  v1.35.4
master-1  Ready     master  73m  v1.35.4
master-2  Ready     master  74m  v1.35.4
worker-0  Ready     worker  11m  v1.35.4
worker-1  Ready     worker  11m  v1.35.4
```

> **Lưu ý:** Bạn có thể cần đợi vài phút sau khi các CSR máy chủ được phê duyệt để các máy chuyển sang trạng thái Sẵn sàng (Ready).

#### 2.2.21. Cấu hình các Operator ban đầu
Sau khi mặt phẳng điều khiển (control plane) khởi tạo xong, bạn cần lập tức cấu hình một số Operator để đảm bảo tất cả chúng đều ở trạng thái sẵn sàng.

**Điều kiện tiên quyết**

* Mặt phẳng điều khiển của bạn đã khởi tạo xong.

**Quy trình**

1. Theo dõi quá trình các thành phần trong cụm (cluster) chuyển sang trạng thái hoạt động:

```shell
$ watch -n5 oc get clusteroperators
```

Ví dụ đầu ra

```
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.22.0    True        False         False      19m
baremetal                                  4.22.0    True        False         False      37m
cloud-credential                           4.22.0    True        False         False      40m
cluster-autoscaler                         4.22.0    True        False         False      37m
config-operator                            4.22.0    True        False         False      38m
console                                    4.22.0    True        False         False      26m
csi-snapshot-controller                    4.22.0    True        False         False      37m
dns                                        4.22.0    True        False         False      37m
etcd                                       4.22.0    True        False         False      36m
image-registry                             4.22.0    True        False         False      31m
ingress                                    4.22.0    True        False         False      30m
insights                                   4.22.0    True        False         False      31m
kube-apiserver                             4.22.0    True        False         False      26m
kube-controller-manager                    4.22.0    True        False         False      36m
kube-scheduler                             4.22.0    True        False         False      36m
kube-storage-version-migrator              4.22.0    True        False         False      37m
machine-api                                4.22.0    True        False         False      29m
machine-approver                           4.22.0    True        False         False      37m
machine-config                             4.22.0    True        False         False      36m
marketplace                                4.22.0    True        False         False      37m
monitoring                                 4.22.0    True        False         False      29m
network                                    4.22.0    True        False         False      38m
node-tuning                                4.22.0    True        False         False      37m
openshift-apiserver                        4.22.0    True        False         False      32m
openshift-controller-manager               4.22.0    True        False         False      30m
openshift-samples                          4.22.0    True        False         False      32m
operator-lifecycle-manager                 4.22.0    True        False         False      37m
operator-lifecycle-manager-catalog         4.22.0    True        False         False      37m
operator-lifecycle-manager-packageserver   4.22.0    True        False         False      32m
service-ca                                 4.22.0    True        False         False      38m
storage                                    4.22.0    True        False         False      37m
```

2. Cấu hình các Operator không khả dụng.

##### 2.2.21.1. Registry hình ảnh bị loại bỏ trong quá trình cài đặt
Trên các nền tảng không cung cấp dịch vụ lưu trữ đối tượng (object storage) có thể chia sẻ, OpenShift Image Registry Operator sẽ tự khởi tạo ở trạng thái "Đã loại bỏ" (Removed). Điều này cho phép openshift-installer hoàn tất quá trình cài đặt trên các loại nền tảng đó.

Sau khi cài đặt, bạn phải chỉnh sửa cấu hình của Image Registry Operator để chuyển đổi `managementState` từ `Removed` sang `Managed`. Sau khi hoàn tất bước này, bạn cần thực hiện cấu hình lưu trữ.

##### 2.2.21.2. Cấu hình lưu trữ cho registry hình ảnh (image registry)
Image Registry Operator sẽ không khả dụng ngay từ đầu đối với các nền tảng không cung cấp bộ lưu trữ mặc định. Sau khi cài đặt, bạn phải cấu hình bộ lưu trữ cho registry của mình để Image Registry Operator trở nên khả dụng.

Hãy cấu hình một volume bền vững (persistent volume) – đây là yêu cầu bắt buộc đối với các cụm (cluster) trong môi trường sản xuất. Đối với các cụm không thuộc môi trường sản xuất, bạn có thể cấu hình một thư mục trống (empty directory) làm vị trí lưu trữ nếu phù hợp.

Bạn cũng có thể cho phép image registry sử dụng các loại lưu trữ khối (block storage) bằng cách áp dụng chiến lược triển khai "Recreate" (tạo lại) trong quá trình nâng cấp.

##### 2.2.21.3. Cấu hình lưu trữ dạng khối (block storage) cho registry trên hạ tầng vật lý (bare metal)
Để cho phép registry lưu trữ ảnh (image registry) sử dụng các loại lưu trữ dạng khối trong quá trình nâng cấp, với tư cách là quản trị viên cụm (cluster administrator), bạn có thể sử dụng chiến lược triển khai `Recreate`.

> **Quan trọng:** Các volume lưu trữ dạng khối (block storage volumes) — hay còn gọi là block persistent volumes — được hỗ trợ nhưng không được khuyến nghị sử dụng cho image registry trên các cụm môi trường thực tế (production clusters). Trong cấu hình này, registry không có tính sẵn sàng cao (high availability) vì nó chỉ có thể chạy với duy nhất một bản sao (replica).

Nếu bạn chọn sử dụng volume lưu trữ dạng khối cho image registry, bạn bắt buộc phải sử dụng yêu cầu lưu trữ bền vững (Persistent Volume Claim - PVC) dựa trên hệ thống tệp (filesystem).

**Quy trình**

1. Nhập lệnh sau để thiết lập lưu trữ cho image registry thành loại lưu trữ dạng khối, đồng thời cập nhật (patch) registry để sử dụng chiến lược triển khai `Recreate` và chỉ chạy với một (1) bản sao (replica):

```shell
$ oc patch config.imageregistry.operator.openshift.io/cluster --type=merge \
  -p '{"spec":{"rolloutStrategy":"Recreate","replicas":1}}'
```

2. Cung cấp PV cho thiết bị lưu trữ khối (block storage device) và tạo PVC cho volume đó. Volume khối được yêu cầu sử dụng chế độ truy cập ReadWriteOnce (RWO). 
  * Tạo tệp `pvc.yaml` với nội dung dưới đây để định nghĩa một đối tượng PersistentVolumeClaim (PVC) cho VMware vSphere:

  ```yaml
  kind: PersistentVolumeClaim
  apiVersion: v1
  metadata:
    name: image-registry-storage
    namespace: openshift-image-registry
  spec:
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 100Gi
  ```

  Trong đó:

    * `metadata.name`: Chỉ định một tên duy nhất đại diện cho đối tượng `PersistentVolumeClaim`.
    * `metadata.namespace`: Chỉ định namespace cho đối tượng `PersistentVolumeClaim`; trong trường hợp này là openshift-image-registry.
    * `spec.accessModes`: Chỉ định chế độ truy cập (access mode) của `PersistentVolumeClaim`. Với tùy chọn `ReadWriteOnce`, volume có thể được mount (gắn) với quyền đọc và ghi bởi một node duy nhất.
    * `spec.resources.requests.storage`: Dung lượng của `PersistentVolumeClaim`.

  * Hãy nhập lệnh sau để tạo đối tượng PersistentVolumeClaim từ tệp tin:

  ```shell
  $ oc create -f pvc.yaml -n openshift-image-registry
  ```

3. Hãy nhập lệnh sau để chỉnh sửa cấu hình registry sao cho nó tham chiếu đến PVC chính xác:

```shell
$ oc edit config.imageregistry.operator.openshift.io -o yaml
```

Ví dụ đầu ra

```yaml
storage:
  pvc:
    claim:
```

Khi tạo một PVC tùy chỉnh, bạn có thể để trống trường "claim" để hệ thống tự động tạo một PVC lưu trữ cho registry hình ảnh (image-registry-storage PVC) theo mặc định.

#### 2.2.22. Hoàn tất cài đặt trên cơ sở hạ tầng do người dùng cung cấp
Để hoàn tất quá trình cài đặt trên cơ sở hạ tầng do người dùng cung cấp, hãy thực hiện các bước cuối cùng để triển khai cụm (cluster) sau khi đã định cấu hình các Operator. Việc này đảm bảo cụm hoạt động hoàn chỉnh trên cơ sở hạ tầng mà bạn cung cấp.

**Điều kiện tiên quyết**

* Control plane của bạn đã được khởi tạo. 
* Bạn đã hoàn tất việc định cấu hình ban đầu cho các Operator.

**Quy trình**

1. Xác nhận rằng tất cả các thành phần của cụm đều đang trực tuyến (online) bằng lệnh sau:

```shell
$ watch -n5 oc get clusteroperators
```

Ví dụ đầu ra

```
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.22.0    True        False         False      19m
baremetal                                  4.22.0    True        False         False      37m
cloud-credential                           4.22.0    True        False         False      40m
cluster-autoscaler                         4.22.0    True        False         False      37m
config-operator                            4.22.0    True        False         False      38m
console                                    4.22.0    True        False         False      26m
csi-snapshot-controller                    4.22.0    True        False         False      37m
dns                                        4.22.0    True        False         False      37m
etcd                                       4.22.0    True        False         False      36m
image-registry                             4.22.0    True        False         False      31m
ingress                                    4.22.0    True        False         False      30m
insights                                   4.22.0    True        False         False      31m
kube-apiserver                             4.22.0    True        False         False      26m
kube-controller-manager                    4.22.0    True        False         False      36m
kube-scheduler                             4.22.0    True        False         False      36m
kube-storage-version-migrator              4.22.0    True        False         False      37m
machine-api                                4.22.0    True        False         False      29m
machine-approver                           4.22.0    True        False         False      37m
machine-config                             4.22.0    True        False         False      36m
marketplace                                4.22.0    True        False         False      37muser
monitoring                                 4.22.0    True        False         False      29m
network                                    4.22.0    True        False         False      38m
node-tuning                                4.22.0    True        False         False      37m
openshift-apiserver                        4.22.0    True        False         False      32muser
openshift-controller-manager               4.22.0    True        False         False      30m
openshift-samples                          4.22.0    True        False         False      32m
operator-lifecycle-manager                 4.22.0    True        False         False      37m
operator-lifecycle-manager-catalog         4.22.0    True        False         False      37m
operator-lifecycle-manager-packageserver   4.22.0    True        False         False      32m
service-ca                                 4.22.0    True        False         False      38m
storage                                    4.22.0    True        False         False      37m
```

Ngoài ra, lệnh sau đây sẽ thông báo cho bạn khi tất cả các cụm đã sẵn sàng. Lệnh này cũng truy xuất và hiển thị thông tin xác thực:

```shell
$ ./openshift-install --dir <installation_directory> wait-for install-complete
```

Trong đó: `<installation_directory>` chỉ định đường dẫn đến thư mục nơi bạn lưu trữ các tệp cài đặt.

Ví dụ đầu ra

```
INFO Waiting up to 30m0s for the cluster to initialize...
```

Lệnh này thực thi thành công khi Cluster Version Operator hoàn tất việc triển khai cụm OpenShift Container Platform từ máy chủ API Kubernetes.

> **Quan trọng:**
> 
> * Các tệp cấu hình Ignition do chương trình cài đặt tạo ra chứa các chứng chỉ có thời hạn 24 giờ; sau khoảng thời gian này, các chứng chỉ đó sẽ được gia hạn. Nếu cụm (cluster) bị tắt trước khi các chứng chỉ được gia hạn và sau đó được khởi động lại khi đã qua 24 giờ, cụm sẽ tự động khôi phục các chứng chỉ đã hết hạn. Một ngoại lệ là bạn phải phê duyệt thủ công các yêu cầu ký chứng chỉ (CSR) của node-bootstrapper đang chờ xử lý để khôi phục các chứng chỉ kubelet. Để biết thêm thông tin, hãy xem tài liệu về cách khôi phục các chứng chỉ control plane đã hết hạn. 
> * Bạn nên sử dụng các tệp cấu hình Ignition trong vòng 12 giờ kể từ khi chúng được tạo, vì quá trình xoay vòng chứng chỉ 24 giờ thường diễn ra trong khoảng từ 16 đến 22 giờ sau khi cụm được cài đặt. Việc sử dụng các tệp cấu hình Ignition trong vòng 12 giờ giúp tránh nguy cơ cài đặt thất bại trong trường hợp quá trình cập nhật chứng chỉ diễn ra đúng lúc đang thực hiện cài đặt.

2. Xác nhận rằng Kubernetes API server đang giao tiếp với các pod. 
  * Để xem danh sách tất cả các pod, hãy sử dụng lệnh sau:

  ```
  $ oc get pods --all-namespaces
  ```

  Ví dụ đầu ra

  ```
  NAMESPACE                         NAME                                            READY   STATUS      RESTARTS   AGE
  openshift-apiserver-operator      openshift-apiserver-operator-85cb746d55-zqhs8   1/1     Running     1          9m
  openshift-apiserver               apiserver-67b9g                                 1/1     Running     0          3m
  openshift-apiserver               apiserver-ljcmx                                 1/1     Running     0          1m
  openshift-apiserver               apiserver-z25h4                                 1/1     Running     0          2m
  openshift-authentication-operator authentication-operator-69d5d8bf84-vh2n8        1/1     Running     0          5m
  ```

  * Xem nhật ký của một pod có tên trong kết quả đầu ra của lệnh trước đó bằng cách sử dụng lệnh sau:

  ```shell
  $ oc logs <pod_name> -n <namespace>
  ```

  Trong đó: `<namespace>` chỉ định tên pod và namespace (không gian tên), như được hiển thị trong kết quả của một lệnh trước đó. 

  Nếu nhật ký (logs) của pod được hiển thị, nghĩa là máy chủ API Kubernetes có thể giao tiếp với các máy trong cụm (cluster).

3. Đối với hệ thống sử dụng Giao thức Fibre Channel (FCP), cần thực hiện các bước bổ sung để kích hoạt tính năng đa đường dẫn (multipathing). Không kích hoạt tính năng đa đường dẫn trong quá trình cài đặt.

#### 2.2.23. Truy cập Telemetry cho OpenShift Container Platform
Để cung cấp các chỉ số về tình trạng cụm (cluster) và kết quả của các bản cập nhật, dịch vụ Telemetry cần có quyền truy cập internet. Khi được kết nối, dịch vụ này sẽ tự động chạy theo mặc định và đăng ký cụm của bạn với [OpenShift Cluster Manager](https://console.redhat.com/openshift).

Sau khi xác nhận rằng danh mục tài nguyên (inventory) trên [OpenShift Cluster Manager](https://console.redhat.com/openshift) đã chính xác — dù được duy trì tự động bởi Telemetry hay được cập nhật thủ công thông qua OpenShift Cluster Manager — bạn có thể sử dụng tính năng "subscription watch" để theo dõi các gói đăng ký (subscription) OpenShift Container Platform của mình ở cấp độ tài khoản hoặc cấp độ đa cụm (multi-cluster). Để biết thêm thông tin về "subscription watch", hãy xem mục "Dữ liệu được thu thập và sử dụng bởi các dịch vụ đăng ký của Red Hat" trong phần Tài nguyên bổ sung.


### 2.3. Cài đặt cụm trên hạ tầng vật lý (bare metal) do người dùng cung cấp trong môi trường không kết nối mạng (disconnected environment)
Trong OpenShift Container Platform 4.22, bạn có thể cài đặt một cụm trên hạ tầng vật lý (bare metal) do chính bạn cung cấp trong một mạng bị giới hạn (restricted network).

> **Quan trọng:** Mặc dù bạn có thể thực hiện theo quy trình này để triển khai cụm trên các môi trường ảo hóa hoặc môi trường đám mây, bạn cần lưu ý đến các yếu tố bổ sung đối với các nền tảng không phải hạ tầng vật lý (non-bare metal). Hãy xem xét thông tin trong các hướng dẫn về việc triển khai OpenShift Container Platform trên các nền tảng chưa được kiểm thử trước khi tiến hành cài đặt cụm OpenShift Container Platform trong những môi trường như vậy.

#### 2.3.1. Các điều kiện tiên quyết
* Bạn đã xem xét các thông tin chi tiết về quy trình cài đặt và cập nhật OpenShift Container Platform. 
* Bạn đã đọc tài liệu hướng dẫn về cách chọn phương thức cài đặt cụm (cluster) và chuẩn bị cụm đó cho người dùng. 
* Bạn đã tạo một registry trên máy chủ mirror (mirror host) của mình và lấy dữ liệu `imageContentSources` tương ứng với phiên bản OpenShift Container Platform mà bạn đang sử dụng. 

> **Quan trọng:** Vì các phương tiện cài đặt (installation media) nằm trên máy chủ mirror, bạn có thể sử dụng máy tính đó để thực hiện tất cả các bước cài đặt. 

* Bạn đã cung cấp bộ lưu trữ bền vững (persistent storage) cho cụm của mình. Để triển khai một registry hình ảnh (image registry) riêng, bộ lưu trữ của bạn phải hỗ trợ chế độ truy cập `ReadWriteMany`. 
* Nếu bạn sử dụng tường lửa và dự định dùng dịch vụ Telemetry, bạn đã cấu hình tường lửa để cho phép truy cập vào các trang web mà cụm của bạn yêu cầu. 

> **Lưu ý:** Hãy đảm bảo xem xét danh sách các trang web này nếu bạn đang cấu hình proxy.

#### 2.3.2. Về việc cài đặt trong các mạng bị hạn chế
Bạn có thể cài đặt OpenShift Container Platform 4.22 trong một mạng bị hạn chế, không có kết nối internet trực tiếp. Các quy trình cài đặt trong môi trường hạn chế này sử dụng cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned) hoặc do người dùng cung cấp (user-provisioned), tùy thuộc vào nền tảng đám mây mà bạn sử dụng.

Nếu chọn thực hiện cài đặt trong mạng bị hạn chế trên một nền tảng đám mây, bạn vẫn cần quyền truy cập vào các API của nền tảng đó. Một số chức năng đám mây—ví dụ như dịch vụ DNS Route 53 và các dịch vụ IAM của Amazon Web Services (AWS)—yêu cầu phải có kết nối internet. Đối với các môi trường như phần cứng vật lý (bare-metal), Nutanix hoặc VMware vSphere, yêu cầu về kết nối internet có thể ít hơn, tùy thuộc vào cấu hình mạng cụ thể của bạn.

Để hoàn tất quá trình cài đặt trong mạng bị hạn chế, bạn cần thiết lập một registry đóng vai trò là bản sao (mirror) của registry chứa các image OpenShift, đồng thời lưu trữ các phương tiện cài đặt (installation media). Bạn có thể tạo registry này trên một máy chủ trung gian (mirror host)—máy chủ có khả năng kết nối với cả internet lẫn mạng nội bộ bị hạn chế của bạn—hoặc sử dụng các phương pháp khác phù hợp với các hạn chế về mạng của bạn.

> **Lưu ý quan trọng:** Do cấu hình cho các quy trình cài đặt sử dụng cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure) thường khá phức tạp, bạn nên thực hiện một quy trình cài đặt tiêu chuẩn với cơ sở hạ tầng do người dùng cung cấp trước khi tiến hành cài đặt trong mạng bị hạn chế. Việc thực hiện quy trình cài đặt thử nghiệm này sẽ giúp bạn dễ dàng cô lập và khắc phục các sự cố có thể phát sinh trong quá trình cài đặt thực tế tại mạng bị hạn chế.

##### 2.3.2.1. Các giới hạn bổ sung
Các cụm (cluster) trong mạng bị hạn chế có những giới hạn và ràng buộc bổ sung sau đây:

* Trạng thái ClusterVersion bao gồm lỗi "Unable to retrieve available updates". 
* Theo mặc định, bạn không thể sử dụng nội dung trong Developer Catalog (Danh mục Nhà phát triển) vì không thể truy cập các thẻ luồng hình ảnh (image stream tag) cần thiết.

#### 2.3.3. Truy cập Internet cho OpenShift Container Platform
Trong OpenShift Container Platform 4.22, bạn cần có quyền truy cập Internet để lấy các image (hình ảnh container) cần thiết cho việc cài đặt cụm (cluster) của mình.

Bạn bắt buộc phải có quyền truy cập Internet để thực hiện các thao tác sau:

* Truy cập Red Hat Hybrid Cloud Console để tải xuống chương trình cài đặt và quản lý đăng ký (subscription). Nếu cụm có quyền truy cập Internet và bạn không tắt tính năng Telemetry, dịch vụ này sẽ tự động cấp quyền sử dụng (entitlement) cho cụm của bạn. 
* Truy cập Quay.io để lấy các gói (package) cần thiết cho việc cài đặt cụm. 
* Lấy các gói cần thiết để thực hiện cập nhật cụm.

#### 2.3.4. Các yêu cầu đối với cụm sử dụng hạ tầng do người dùng cung cấp
Đối với một cụm bao gồm hạ tầng do người dùng cung cấp, bạn phải triển khai tất cả các máy cần thiết.

Phần này mô tả các yêu cầu để triển khai OpenShift Container Platform trên hạ tầng do người dùng cung cấp.

##### 2.3.4.1. Các máy cần thiết để cài đặt cụm (cluster)
Bạn phải xác định số lượng máy hoặc máy chủ tối thiểu cần thiết cho cụm của mình, nhằm đảm bảo cụm vẫn duy trì sự ổn định ngay cả khi một nút (node) gặp sự cố.

Các cụm OpenShift Container Platform với quy mô nhỏ nhất yêu cầu các máy chủ sau:
Quan trọng

Đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải triển khai tất cả các máy cần thiết.

**Bảng 2.29. Số lượng máy chủ tối thiểu cần thiết**

| Máy chủ                                                                       | Mô tả                                                                                                                                                                                                  |
|-------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Một máy khởi động tạm thời                                                    | Cụm yêu cầu phải có máy bootstrap để triển khai cụm OpenShift Container Platform trên ba máy thuộc mặt phẳng điều khiển (control plane). Bạn có thể loại bỏ máy bootstrap sau khi đã cài đặt xong cụm. |
| Ba máy thuộc mặt phẳng điều khiển                                             | Các máy thuộc mặt phẳng điều khiển (control plane) chạy các dịch vụ Kubernetes và OpenShift Container Platform, tạo nên mặt phẳng điều khiển đó.                                                       |
| Ít nhất hai máy tính toán, còn được gọi là các máy thực thi (worker machine). | Các khối lượng công việc do người dùng OpenShift Container Platform yêu cầu sẽ chạy trên các máy tính toán.                                                                                            |

> **Lưu ý:** Một trường hợp ngoại lệ là bạn có thể vận hành các cụm máy chủ bare metal (phần cứng vật lý thuần túy) mà không cần máy chủ tính toán (compute machine), chỉ bao gồm ba máy chủ điều khiển (control plane machine). Cấu hình này tạo ra các cụm nhỏ gọn, sử dụng tài nguyên hiệu quả hơn, phù hợp để các quản trị viên cụm và nhà phát triển sử dụng cho mục đích thử nghiệm, phát triển và vận hành thực tế. Lưu ý rằng việc vận hành chỉ với một máy chủ tính toán là không được hỗ trợ.

> **Quan trọng:** Để duy trì tính sẵn sàng cao cho cụm máy chủ, hãy sử dụng các máy chủ vật lý riêng biệt cho từng máy thành phần trong cụm.

Các máy chủ khởi động (bootstrap machine) và máy chủ điều khiển (control plane machine) bắt buộc phải sử dụng hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS). Trong khi đó, các máy chủ tính toán (compute machine) có thể sử dụng Red Hat Enterprise Linux CoreOS (RHCOS) hoặc Red Hat Enterprise Linux (RHEL) phiên bản 8.6 trở lên.

RHCOS được xây dựng dựa trên Red Hat Enterprise Linux (RHEL) 9.8 và kế thừa toàn bộ các chứng nhận cũng như yêu cầu về phần cứng của hệ điều hành này. Vui lòng tham khảo thông tin về [các khả năng và giới hạn công nghệ của Red Hat Enterprise Linux](https://access.redhat.com/articles/rhel-limits).

##### 2.3.4.2. Các yêu cầu tối thiểu về tài nguyên để cài đặt cụm (cluster)
Để đảm bảo cụm OpenShift Container Platform của bạn vận hành đúng như mong đợi, mỗi máy trong cụm phải đáp ứng các yêu cầu tối thiểu về CPU, bộ nhớ và lưu trữ.

**Bảng 2.30. Các yêu cầu tối thiểu về tài nguyên**

| Máy           | Hệ điều hành | CPU | RAM   | Lưu trữ | IOPS |
|---------------|--------------|-----|-------|---------|------|
| Bootstrap     | RHCOS        | 4   | 16 GB | 100 GB  | 300  |
| Control plane | RHCOS        | 4   | 16 GB | 100 GB  | 300  |
| Compute       | RHCOS        | 2   | 8 GB  | 100 GB  | 300  |

* Một CPU tương đương với một nhân vật lý khi tính năng đa luồng đồng thời (SMT) – hay còn gọi là Hyper-Threading – không được kích hoạt. Khi tính năng này được bật, hãy sử dụng công thức sau để tính toán tỷ lệ tương ứng: (số luồng trên mỗi nhân × số nhân) × số socket = số CPU. 
* OpenShift Container Platform và Kubernetes rất nhạy cảm với hiệu năng ổ đĩa. Red Hat khuyến nghị sử dụng hệ thống lưu trữ có tốc độ cao, đặc biệt là đối với etcd trên các nút mặt phẳng điều khiển (control plane nodes) – vốn yêu cầu thời gian thực hiện fsync (p99) đạt mức 10 ms. Trên nhiều nền tảng đám mây, dung lượng lưu trữ và chỉ số IOPS thường tỷ lệ thuận với nhau; do đó, bạn có thể cần cấp phát thêm dung lượng lưu trữ để đạt được hiệu năng cần thiết. 
* Đối với các mô hình cài đặt do người dùng tự triển khai (user-provisioned installations), nếu bạn chọn sử dụng các máy tính toán (compute machines) chạy hệ điều hành RHEL trong cụm (cluster) của mình, bạn sẽ chịu trách nhiệm quản lý vòng đời và bảo trì hệ điều hành đó. Điều này bao gồm việc thực hiện các bản cập nhật hệ thống, áp dụng các bản vá lỗi và hoàn tất mọi tác vụ cần thiết khác. OpenShift Container Platform phiên bản 4.10 trở về sau không hỗ trợ các máy tính toán chạy RHEL 7.

> **Lưu ý:** Trong OpenShift Container Platform phiên bản 4.22, RHCOS sử dụng RHEL phiên bản 9.8, với các yêu cầu cập nhật về kiến trúc vi xử lý (micro-architecture). Mỗi kiến trúc yêu cầu các tập lệnh (Instruction Set Architecture - ISA) tối thiểu như sau:
> 
> * Kiến trúc x86-64 yêu cầu ISA x86-64-v2
> * Kiến trúc ARM64 yêu cầu ISA ARMv8.0-A
> * Kiến trúc ppc64le yêu cầu ISA IBM® Power9
> * Kiến trúc s390x yêu cầu ISA IBM® z14

Nếu loại instance (instance type) trên nền tảng của bạn đáp ứng các yêu cầu tối thiểu dành cho các máy trong cụm, thì loại instance đó được hỗ trợ để sử dụng trong OpenShift Container Platform.

##### 2.3.4.3. Quản lý các yêu cầu ký chứng chỉ (Certificate Signing Requests - CSR)
Trên cơ sở hạ tầng do người dùng cung cấp, nếu cụm (cluster) của bạn bị hạn chế quyền truy cập vào các cơ chế quản lý máy tự động, bạn phải triển khai một quy trình để phê duyệt các yêu cầu ký chứng chỉ (CSR) của cụm sau khi quá trình cài đặt hoàn tất.

Thành phần `kube-controller-manager` chỉ phê duyệt các CSR dành cho máy khách (client) của `kubelet`. Cơ chế `machine-approver` không thể đảm bảo tính hợp lệ của chứng chỉ phục vụ (serving certificate) mà các thông tin xác thực (credentials) của `kubelet` yêu cầu, vì nó không thể xác nhận liệu yêu cầu đó có thực sự xuất phát từ đúng máy hay không. Do đó, bạn cần tìm kiếm và triển khai một phương thức để xác minh tính hợp lệ của các yêu cầu cấp chứng chỉ phục vụ cho `kubelet`, đồng thời thực hiện phê duyệt các yêu cầu đó.

##### 2.3.4.4. Các yêu cầu về mạng đối với cơ sở hạ tầng do người dùng cung cấp
Bạn phải định cấu hình mạng cho tất cả các máy chạy Red Hat Enterprise Linux CoreOS (RHCOS) trong giai đoạn initramfs khi khởi động, để các máy này có thể truy xuất các tệp cấu hình Ignition của chúng.

Trong quá trình khởi động ban đầu, các máy cần được thiết lập địa chỉ IP, thông qua máy chủ DHCP hoặc bằng cách thiết lập thủ công (tĩnh) thông qua các tùy chọn khởi động. Sau khi kết nối mạng được thiết lập, các máy sẽ tải xuống các tệp cấu hình Ignition từ một máy chủ HTTP hoặc HTTPS. Các tệp cấu hình Ignition này sau đó được sử dụng để thiết lập trạng thái cụ thể cho từng máy. Sau khi cài đặt, thành phần Machine Config Operator sẽ thực hiện thêm các thay đổi trên các máy đó, chẳng hạn như áp dụng các chứng chỉ hoặc khóa mới.

> **Lưu ý:**
> 
> * Bạn nên cân nhắc sử dụng máy chủ DHCP để quản lý lâu dài các máy trong cụm (cluster). Hãy đảm bảo rằng máy chủ DHCP được định cấu hình để cung cấp các địa chỉ IP cố định, thông tin máy chủ DNS và tên máy chủ (hostname) cho các máy trong cụm. 
> * Nếu cơ sở hạ tầng do bạn cung cấp không có dịch vụ DHCP, bạn có thể tự thiết lập cấu hình mạng IP và địa chỉ máy chủ DNS cho các nút (node) tại thời điểm cài đặt RHCOS. Các thông tin này có thể được truyền dưới dạng tham số khởi động nếu bạn cài đặt từ tệp ảnh ISO. Xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform" để biết thêm thông tin về việc thiết lập địa chỉ IP tĩnh và các tùy chọn mạng nâng cao.

Máy chủ API Kubernetes (Kubernetes API server) phải có khả năng phân giải tên của các nút trong cụm. Nếu các máy chủ API và các nút worker nằm trong các vùng (zone) khác nhau, bạn có thể định cấu hình vùng tìm kiếm DNS mặc định (default DNS search zone) để cho phép máy chủ API phân giải tên các nút. Một phương pháp khác được hỗ trợ là luôn sử dụng tên miền đầy đủ (fully-qualified domain name - FQDN) để tham chiếu đến các máy chủ, áp dụng cho cả các đối tượng nút (node objects) và tất cả các yêu cầu DNS.

###### 2.3.4.4.1. Thiết lập tên máy chủ (hostname) cho các nút trong cụm thông qua DHCP
Trên các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS), tên máy chủ (hostname) được thiết lập thông qua NetworkManager. Theo mặc định, các máy này nhận tên máy chủ thông qua DHCP. Nếu tên máy chủ không được cung cấp qua DHCP, không được thiết lập tĩnh thông qua các tham số nhân (kernel arguments), hoặc không được cấu hình bằng phương thức khác, hệ thống sẽ xác định tên máy chủ thông qua truy vấn DNS ngược (reverse DNS lookup). Quá trình truy vấn DNS ngược diễn ra sau khi mạng đã được khởi tạo trên một nút và có thể mất một khoảng thời gian để hoàn tất. Các dịch vụ hệ thống khác có thể khởi chạy trước thời điểm này và nhận diện tên máy chủ là "localhost" hoặc một giá trị tương tự. Bạn có thể tránh tình trạng này bằng cách sử dụng DHCP để cung cấp tên máy chủ cho từng nút trong cụm.

Ngoài ra, việc thiết lập tên máy chủ thông qua DHCP còn giúp ngăn ngừa các lỗi cấu hình thủ công đối với bản ghi DNS trong các môi trường triển khai kiến trúc DNS "split-horizon" (DNS phân tách theo phạm vi truy cập).

###### 2.3.4.4.2. Các yêu cầu về kết nối mạng
Bạn phải cấu hình kết nối mạng giữa các máy để cho phép các thành phần trong cụm OpenShift Container Platform có thể giao tiếp với nhau. Mỗi máy phải có khả năng phân giải tên máy chủ (hostname) của tất cả các máy khác trong cụm.

Phần này cung cấp thông tin chi tiết về các cổng (port) cần thiết.

**Bảng 2.31. Các cổng được sử dụng cho hoạt động giao tiếp giữa tất cả các máy với nhau**

| Giao thức | Cổng            | Mô tả                                                                                                                                         |
|-----------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| ICMP      | N/A             | Kiểm tra khả năng kết nối mạng                                                                                                                |
| TCP       | `1936`          | Các chỉ số                                                                                                                                    |
| TCP       | `9000`-`9999`   | Các dịch vụ ở cấp độ máy chủ (host), bao gồm node exporter trên các cổng `9100`-`9101` và Cluster Version Operator trên cổng `9099`.          |
| TCP       | `10250`-`10259` | Các cổng mặc định mà Kubernetes dành riêng                                                                                                    |
| TCP       | `22623`         | Cổng này xử lý lưu lượng truy cập từ Machine Config Server và điều hướng lưu lượng đó đến các máy thuộc mặt phẳng điều khiển (control plane). |
| UDP       | `6081`          | Geneve                                                                                                                                        |
| UDP       | `9000`-`9999`   | Các dịch vụ ở cấp độ máy chủ, bao gồm node exporter trên các cổng `9100`-`9101`.                                                              |
| UDP       | `500`           | Các gói tin IPsec IKE                                                                                                                         |
| UDP       | `4500`          | Các gói tin IPsec NAT-T                                                                                                                       |
| UDP       | `123`           | Network Time Protocol (NTP) sử dụng cổng UDP `123`. Nếu máy chủ thời gian NTP bên ngoài được cấu hình, bạn phải mở cổng UDP `123`.            |
| TCP/UDP   | `30000`-`32767` | Cổng nút Kubernetes                                                                                                                           |
| ESP       | N/A             | Giao thức đóng gói bảo mật (ESP) trong IPsec                                                                                                  |

**Bảng 2.32. Các cổng được sử dụng cho hoạt động giao tiếp giữa tất cả các máy với mặt phẳng điều khiển (control plane)**

| Giao thức | Cổng   | Mô tả          |
|-----------|--------|----------------|
| TCP       | `6443` | Kubernetes API |

**Bảng 2.33. Các cổng được sử dụng cho hoạt động giao tiếp giữa các máy trong mặt phẳng điều khiển (control plane)**

| Giao thức | Cổng          | Mô tả                                                           |
|-----------|---------------|-----------------------------------------------------------------|
| TCP       | `2379`-`2380` | Các cổng dành cho máy chủ và các nút ngang hàng (peer) của etcd |

###### 2.3.4.4.3. Cấu hình NTP cho cơ sở hạ tầng do người dùng cung cấp
Theo mặc định, các cụm OpenShift Container Platform được cấu hình để sử dụng máy chủ Giao thức Thời gian Mạng (NTP) công cộng. Nếu bạn muốn sử dụng máy chủ NTP nội bộ của doanh nghiệp, hoặc nếu cụm của bạn được triển khai trong một mạng không kết nối với internet (disconnected network), bạn có thể cấu hình cụm để sử dụng một máy chủ thời gian cụ thể. Để biết thêm thông tin, hãy xem tài liệu về cách cấu hình dịch vụ thời gian chrony.

Nếu máy chủ DHCP cung cấp thông tin về máy chủ NTP, dịch vụ thời gian chrony trên các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) sẽ đọc thông tin đó và có thể đồng bộ hóa đồng hồ hệ thống với các máy chủ NTP này.

##### 2.3.4.5. Các yêu cầu về DNS do người dùng cung cấp
Trong các triển khai OpenShift Container Platform, bạn phải đảm bảo rằng các thành phần của cụm (cluster) đáp ứng những tiêu chí nhất định về phân giải tên DNS, phục vụ cho các mục đích như giao tiếp nội bộ, xác thực chứng chỉ và tự động phát hiện nút (node).

Dưới đây là danh sách các thành phần cụm bắt buộc:

* Kubernetes API
* Wildcard (tên miền đại diện) cho ứng dụng trên OpenShift Container Platform
* Các máy bootstrap và các máy thuộc mặt phẳng điều khiển (control plane)
* Các máy tính toán (compute machines)

Phân giải DNS ngược (reverse DNS resolution) cũng là yêu cầu bắt buộc đối với Kubernetes API, máy bootstrap, các máy thuộc mặt phẳng điều khiển và các máy tính toán.

Các bản ghi DNS loại A/AAAA hoặc CNAME được sử dụng để phân giải tên, trong khi các bản ghi PTR được sử dụng để phân giải tên ngược. Các bản ghi ngược đóng vai trò quan trọng vì Red Hat Enterprise Linux CoreOS (RHCOS) sử dụng chúng để thiết lập tên máy (hostname) cho tất cả các nút, trừ khi các tên máy này đã được cung cấp thông qua DHCP. Ngoài ra, các bản ghi ngược còn được dùng để tạo các yêu cầu ký chứng chỉ (CSR) cần thiết cho hoạt động của OpenShift Container Platform.

> **Lưu ý:** Bạn nên sử dụng máy chủ DHCP để cung cấp tên máy cho từng nút trong cụm. Hãy tham khảo phần các khuyến nghị về DHCP cho hạ tầng do người dùng cung cấp (user-provisioned infrastructure) để biết thêm thông tin.

Các bản ghi DNS dưới đây là bắt buộc đối với cụm OpenShift Container Platform do người dùng cung cấp và phải được thiết lập trước khi tiến hành cài đặt. Trong mỗi bản ghi, `<cluster_name>` là tên cụm và `<base_domain>` là tên miền cơ sở mà bạn chỉ định trong tệp install-config.yaml. Một bản ghi DNS hoàn chỉnh sẽ có dạng: `<component>.<cluster_name>.<base_domain>.`.

**Bảng 2.34. Các bản ghi DNS bắt buộc**

| Thành phần                   | Bản ghi                                            | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|------------------------------|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kubernetes API               | `api.<cluster_name>.<base_domain>.`                | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định bộ cân bằng tải API. Các bản ghi này phải có khả năng được phân giải bởi cả các máy khách bên ngoài cụm và tất cả các nút bên trong cụm đó.                                                                                                                                                                                                                                                                                                                                                                                                              |
| Kubernetes API               | `api-int.<cluster_name>.<base_domain>.`            | Cần có bản ghi DNS A/AAAA hoặc CNAME, cùng với bản ghi DNS PTR, để xác định bộ cân bằng tải API (API load balancer) trong nội bộ hệ thống. Các bản ghi này phải có thể phân giải được từ tất cả các nút (node) bên trong cụm (cluster). **Quan trọng:** Máy chủ API (API server) phải có khả năng phân giải các nút worker thông qua tên máy chủ (hostname) đã được ghi lại trong Kubernetes. Nếu máy chủ API không thể phân giải được tên các nút, thì các lệnh gọi API thông qua proxy có thể bị lỗi, và bạn sẽ không thể truy xuất nhật ký (log) từ các pod.                                                                       |
| Routes                       | `*.apps.<cluster_name>.<base_domain>.`             | Một bản ghi DNS wildcard (dạng A/AAAA hoặc CNAME) trỏ đến bộ cân bằng tải ingress (ingress load balancer) của ứng dụng. Bộ cân bằng tải ingress này hướng lưu lượng truy cập đến các máy đang chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller này chạy trên các máy tính toán (compute machines). Các bản ghi này phải có thể được phân giải bởi cả các client bên ngoài cụm lẫn tất cả các node nằm bên trong cụm. Ví dụ: `console-openshift-console.apps.<cluster_name>.<base_domain>` được sử dụng làm tuyến đường (route) wildcard dẫn đến bảng điều khiển (console) của OpenShift Container Platform. |
| Máy khởi động                | `bootstrap.<cluster_name>.<base_domain>.`          | Một bản ghi DNS A/AAAA hoặc CNAME, cùng với một bản ghi DNS PTR, để xác định máy khởi động (bootstrap machine). Các bản ghi này phải có thể được phân giải bởi các nút (node) nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Máy chủ mặt phẳng điều khiển | `<control_plane><n>.<cluster_name>.<base_domain>.` | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút thuộc mặt phẳng điều khiển (control plane nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                 |
| Máy tính toán                | `<compute><n>.<cluster_name>.<base_domain>.`       | Các bản ghi DNS A/AAAA hoặc CNAME cùng các bản ghi DNS PTR được sử dụng để định danh từng máy trong số các nút worker (worker nodes). Các bản ghi này phải có khả năng được phân giải bởi các nút nằm trong cụm (cluster).                                                                                                                                                                                                                                                                                                                                                                                                            |

> **Lưu ý:** Trong OpenShift Container Platform 4.4 trở về sau, bạn không cần chỉ định các bản ghi SRV và máy chủ etcd trong cấu hình DNS của mình.

> **Mẹo:** Bạn có thể sử dụng lệnh `dig` để xác minh quá trình phân giải tên miền và phân giải ngược. Hãy xem phần "Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp" để biết các bước xác thực chi tiết.

###### 2.3.4.5.1. Ví dụ về cấu hình DNS cho các cụm (cluster) do người dùng cung cấp
Hãy tham khảo các ví dụ về cấu hình DNS để hiểu cách thiết lập các bản ghi A và PTR nhằm đáp ứng các yêu cầu DNS khi triển khai OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp.

Các ví dụ về cấu hình DNS được cung cấp tại đây chỉ mang tính chất tham khảo và không nhằm mục đích đưa ra lời khuyên về việc lựa chọn giải pháp DNS này thay vì giải pháp DNS khác.

Trong các ví dụ này, tên cụm (cluster name) là ocp4 và tên miền cơ sở (base domain) là example.com.

Ví dụ dưới đây là một tệp vùng (zone file) BIND, minh họa các bản ghi DNS loại A mẫu dùng cho việc phân giải tên trong một cụm do người dùng cung cấp.
Lưu ý

Trong ví dụ này, cùng một bộ cân bằng tải (load balancer) được sử dụng cho cả Kubernetes API và lưu lượng truy cập ứng dụng (application ingress). Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và cho lưu lượng truy cập ứng dụng, nhờ đó có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

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
control-plane2.ocp4.example.com.  IN  A 192.168.1.99
;
compute0.ocp4.example.com.  IN  A 192.168.1.11
compute1.ocp4.example.com.  IN  A 192.168.1.7
;
;
```

Trong đó:

* api.ocp4.example.com.: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API.
* api-int.ocp4.example.com.: Cung cấp khả năng phân giải tên cho Kubernetes API. Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho API và được sử dụng cho các giao tiếp nội bộ trong cụm (cluster).
* `*.apps.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các tuyến đường (route) dạng ký tự đại diện (wildcard). Bản ghi này trỏ đến địa chỉ IP của bộ cân bằng tải dành cho ứng dụng (application ingress load balancer). Bộ cân bằng tải này hướng lưu lượng đến các máy chủ đang chạy các pod Ingress Controller.
* `bootstrap.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho máy chủ bootstrap.
* `control-plane0.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy chủ thuộc mặt phẳng điều khiển (control plane).
* `compute0.ocp4.example.com.`: Cung cấp khả năng phân giải tên cho các máy chủ tính toán (compute).

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
99.1.168.192.in-addr.arpa.  IN  PTR control-plane2.ocp4.example.com.
;
11.1.168.192.in-addr.arpa.  IN  PTR compute0.ocp4.example.com.
7.1.168.192.in-addr.arpa. IN  PTR compute1.ocp4.example.com.
;
;
```

Trong đó:

* `api.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược (reverse DNS resolution) cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải (load balancer) API.
* `api-int.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho Kubernetes API. Bản ghi PTR trỏ đến tên bản ghi của bộ cân bằng tải API và được sử dụng cho các hoạt động giao tiếp nội bộ trong cụm (cluster).
* `bootstrap.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho máy bootstrap.
* `control-plane0.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy thuộc mặt phẳng điều khiển (control plane).
* `compute0.ocp4.example.com.`: Cung cấp khả năng phân giải DNS ngược cho các máy tính toán (compute machines).

> **Lưu ý:** Không bắt buộc phải có bản ghi PTR cho tên miền đại diện (wildcard) của ứng dụng trong OpenShift Container Platform.

##### 2.3.4.6. Cấu hình tham số dnsRecordsType
Trong quá trình cài đặt cụm (cluster), bạn có thể chỉ định tham số `dnsRecordsType` trong tệp `install-config.yaml` để xác định xem dịch vụ DNS nội bộ hay một nguồn bên ngoài sẽ cung cấp các bản ghi DNS cần thiết cho `api`, `api-int` và `ingress`.

> **Quan trọng:** Việc cấu hình tham số `dnsRecordsType` chỉ là tính năng ở giai đoạn "Xem trước công nghệ" (Technology Preview). Các tính năng này không nằm trong phạm vi hỗ trợ theo các thỏa thuận cấp độ dịch vụ (SLA) dành cho môi trường sản xuất của Red Hat và có thể chưa hoàn thiện về mặt chức năng. Red Hat khuyến cáo không nên sử dụng các tính năng này trong môi trường sản xuất. Những tính năng này cung cấp quyền truy cập sớm vào các chức năng sắp ra mắt, cho phép khách hàng thử nghiệm và đóng góp phản hồi trong quá trình phát triển sản phẩm. Để biết thêm thông tin về phạm vi hỗ trợ cho các tính năng "Xem trước công nghệ" của Red Hat, vui lòng xem phần [Technology Preview Features Support Scope](https://access.redhat.com/support/offerings/techpreview/).

Tham số `dnsRecordsType` hỗ trợ các giá trị sau:

* `Internal`: Giá trị mặc định. Khi chọn giá trị này, cơ sở hạ tầng của cụm sẽ tự động tạo và duy trì các bản ghi DNS cần thiết. 
* `External`: Bạn chỉ có thể sử dụng giá trị này nếu đã thiết lập tham số `loadBalancer.type` thành `UserManaged`. Cụm sẽ không quản lý các bản ghi DNS; thay vào đó, bạn phải tự cấu hình các bản ghi DNS trên một máy chủ DNS bên ngoài.

**Điều kiện tiên quyết**

* Bạn đã tạo các bản ghi DNS, chẳng hạn như `api`, `api-int` hoặc `*.apps`. 
* Bạn đã cấu hình bộ cân bằng tải do người dùng quản lý (user-managed load balancer) cho cụm của mình. 
* Nếu dự định đặt `dnsRecordsType` thành `External` trong tài nguyên tùy chỉnh (CR) `infrastructure.config.openshift.io`, trước tiên bạn phải cấu hình các nút (node) trong cụm để sử dụng máy chủ bên ngoài cụ thể đó cho việc phân giải DNS.

**Quy trình**

* Trong tệp `install-config.yaml` khi cài đặt cụm, hãy chỉ định `TechPreviewNoUpgrade` cho tham số `featureSet` và chỉ định `External` cho tham số `dnsRecordsType`:

```yaml
apiVersion: v1
baseDomain: example.com
metadata:
  name: dev-cluster
# ...
platform:
  baremetal:
# ...
    loadBalancer:
      type: UserManaged
    dnsRecordsType: External
# ...
featureSet: TechPreviewNoUpgrade
pullSecret: '{"auths":{"<local_registry>": {"auth": "<credentials>","email": "you@example.com"}}}'
sshKey: 'ssh-ed25519 AAAA...'
# ...
```

Trong đó:

* `type.UserManaged`: Chỉ định một bộ cân bằng tải (load balancer) bên ngoài cho cụm (cluster) của bạn.
* `dnsRecordsType.External`: Chỉ định rằng cụm không tạo các bản ghi DNS nội bộ cho cơ sở hạ tầng cốt lõi.
* `featureSet.TechPreviewNoUpgrade`: Chỉ định việc kích hoạt các tính năng không mặc định cho cụm của bạn.

##### 2.3.4.7. Các yêu cầu về cân bằng tải đối với hạ tầng do người dùng cung cấp
Trước khi cài đặt OpenShift Container Platform, bạn phải cung cấp hạ tầng cân bằng tải cho API và Ingress ứng dụng. Trong các môi trường thực tế (production), bạn có thể triển khai bộ cân bằng tải cho API và Ingress ứng dụng một cách riêng biệt, nhờ đó có thể mở rộng hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Nếu bạn muốn triển khai các bộ cân bằng tải cho API và Ingress ứng dụng trên một instance chạy hệ điều hành Red Hat Enterprise Linux (RHEL), bạn cần phải mua riêng gói đăng ký (subscription) cho RHEL.

Hạ tầng cân bằng tải phải đáp ứng các yêu cầu sau:

* Bộ cân bằng tải API: Cung cấp một điểm truy cập (endpoint) chung để người dùng—bao gồm cả con người và máy móc—tương tác với nền tảng và thực hiện cấu hình. Hãy thiết lập các điều kiện sau:
  * Chỉ sử dụng cân bằng tải ở Lớp 4 (Layer 4). Phương thức này còn được gọi là Raw TCP (TCP thô) hoặc SSL Passthrough (chuyển tiếp SSL). 
  * Sử dụng thuật toán cân bằng tải không lưu trạng thái (stateless). Các tùy chọn cụ thể sẽ khác nhau tùy thuộc vào giải pháp cân bằng tải mà bạn triển khai.

> **Quan trọng:** Không cấu hình tính năng duy trì phiên (session persistence) cho bộ cân bằng tải API. Việc cấu hình duy trì phiên cho Kubernetes API server có thể gây ra các vấn đề về hiệu năng do lưu lượng truy cập ứng dụng quá mức đổ dồn vào cụm OpenShift Container Platform và Kubernetes API đang chạy bên trong cụm đó.

Cấu hình các cổng (port) sau đây ở cả phía trước (front-end) và phía sau (back-end) của các bộ cân bằng tải API:

| Cổng  | Máy chủ phụ trợ                                                                                                                                                                                                                                                                                                          | Nội bộ | Bên ngoài | Mô tả                     |
|-------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|-----------|---------------------------|
| 6443  | Bootstrap và mặt phẳng điều khiển (control plane). Sau khi máy bootstrap khởi tạo mặt phẳng điều khiển của cụm (cluster), bạn cần loại bỏ máy này khỏi bộ cân bằng tải (load balancer). Bạn phải cấu hình điểm cuối (endpoint) `/readyz` để phục vụ cho việc kiểm tra tình trạng hoạt động của máy chủ API (API server). | X      | X         | Máy chủ API Kubernetes    |
| 22623 | Bootstrap và mặt phẳng điều khiển (control plane). Bạn loại bỏ máy bootstrap khỏi bộ cân bằng tải sau khi máy này khởi tạo mặt phẳng điều khiển của cụm (cluster).                                                                                                                                                       | X      |           | Máy chủ cấu hình thiết bị |

> **Lưu ý:** Bộ cân bằng tải (load balancer) phải được cấu hình sao cho khoảng thời gian từ lúc máy chủ API tắt điểm cuối (endpoint) `/readyz` cho đến khi thực thể máy chủ API đó bị loại bỏ khỏi nhóm (pool) không vượt quá 30 giây. Trong khoảng thời gian sau khi `/readyz` trả về lỗi hoặc chuyển sang trạng thái khỏe mạnh (healthy), điểm cuối đó phải được loại bỏ hoặc thêm vào tương ứng. Việc thực hiện kiểm tra (probe) mỗi 5 hoặc 10 giây, với quy tắc cần 2 phản hồi thành công liên tiếp để xác định trạng thái khỏe mạnh và 3 phản hồi không thành công liên tiếp để xác định trạng thái không khỏe mạnh, là các thông số đã được kiểm chứng hiệu quả. 

* Bộ cân bằng tải Ingress cho ứng dụng: Cung cấp điểm truy cập cho lưu lượng ứng dụng đi từ bên ngoài cụm (cluster) vào. Cụm OpenShift Container Platform yêu cầu phải có cấu hình hoạt động ổn định cho bộ định tuyến Ingress. Hãy thiết lập các điều kiện sau:
  * Chỉ sử dụng cân bằng tải ở Lớp 4 (Layer 4). Cấu hình này còn được gọi là chế độ TCP thô (Raw TCP) hoặc chuyển tiếp SSL (SSL Passthrough). 
  * Khuyến nghị sử dụng cơ chế duy trì phiên (session persistence) dựa trên kết nối hoặc dựa trên phiên, tùy thuộc vào các tùy chọn khả dụng và loại ứng dụng sẽ được lưu trữ trên nền tảng.

> **Mẹo:** Nếu bộ cân bằng tải Ingress có thể nhận diện được địa chỉ IP thực của máy khách (client), việc kích hoạt tính năng duy trì phiên dựa trên IP nguồn (source IP-based session persistence) có thể giúp cải thiện hiệu suất cho các ứng dụng sử dụng mã hóa TLS đầu cuối (end-to-end TLS encryption).

Cấu hình các cổng (port) sau đây ở cả phía trước (front-end) và phía sau (back-end) của các bộ cân bằng tải:

**Bảng 2.35. Bộ cân bằng tải Ingress cho ứng dụng**

| Cổng | Máy chủ phụ trợ                                                                                                           | Nội bộ | Bên ngoài | Mô tả                    |
|------|---------------------------------------------------------------------------------------------------------------------------|--------|-----------|--------------------------|
| 443  | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng truy cập HTTPS |
| 80   | Các máy chạy các pod Ingress Controller — dù là máy tính toán (compute) hay máy thực thi tác vụ (worker) — theo mặc định. | X      | X         | Lưu lượng truy cập HTTP  |

> **Lưu ý:** Nếu bạn triển khai cụm gồm ba nút (node) mà không có nút tính toán (compute node) nào, các pod của Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển (control plane node). Đối với các cụm gồm ba nút, bạn phải cấu hình bộ cân bằng tải Ingress cho ứng dụng của mình để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển.

###### 2.3.4.7.1. Ví dụ về cấu hình bộ cân bằng tải cho các cụm do người dùng cung cấp
Hãy tham khảo ví dụ về cấu hình bộ cân bằng tải cho API Kubernetes và lưu lượng truy cập ứng dụng (Ingress) để hiểu cách đáp ứng các yêu cầu cân bằng tải đối với các cụm do người dùng cung cấp.

Ví dụ này là tệp cấu hình `/etc/haproxy/haproxy.cfg` dành cho bộ cân bằng tải HAProxy. Ví dụ này không nhằm mục đích đưa ra lời khuyên về việc chọn giải pháp cân bằng tải này thay vì giải pháp khác.

> **Mẹo:** Nếu bạn sử dụng HAProxy làm bộ cân bằng tải, bạn có thể kiểm tra xem tiến trình HAProxy đã lắng nghe trên các cổng 6443, 22623, 443 và 80 hay chưa bằng cách chạy lệnh `netstat -nltupe` trên nút (node) chạy HAProxy.

Trong ví dụ này, cùng một bộ cân bằng tải được sử dụng cho cả API Kubernetes và lưu lượng truy cập ứng dụng (Ingress). Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và cho lưu lượng truy cập ứng dụng, nhờ đó có thể mở rộng cơ sở hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

> **Lưu ý:** Nếu bạn sử dụng HAProxy làm bộ cân bằng tải và SELinux đang ở chế độ thực thi (enforcing), bạn phải đảm bảo rằng dịch vụ HAProxy có thể liên kết (bind) với cổng TCP đã cấu hình bằng cách chạy lệnh `setsebool -P haproxy_connect_any=1`.

Ví dụ về cấu hình bộ cân bằng tải cho API và lưu lượng truy cập ứng dụng (Ingress)

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
  option  httpchk GET /readyz HTTP/1.0
  option  log-health-checks
  balance roundrobin
  server bootstrap bootstrap.ocp4.example.com:6443 verify none check check-ssl inter 10s fall 2 rise 3 backup
  server master0 master0.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server master1 master1.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
  server master2 master2.ocp4.example.com:6443 weight 1 verify none check check-ssl inter 10s fall 2 rise 3
listen machine-config-server-22623
  bind *:22623
  mode tcp
  server bootstrap bootstrap.ocp4.example.com:22623 check inter 1s backup
  server master0 master0.ocp4.example.com:22623 check inter 1s
  server master1 master1.ocp4.example.com:22623 check inter 1s
  server master2 master2.ocp4.example.com:22623 check inter 1s
listen ingress-router-443
  bind *:443
  mode tcp
  balance source
  server compute0 compute0.ocp4.example.com:443 check inter 1s
  server compute1 compute1.ocp4.example.com:443 check inter 1s
listen ingress-router-80
  bind *:80
  mode tcp
  balance source
  server compute0 compute0.ocp4.example.com:80 check inter 1s
  server compute1 compute1.ocp4.example.com:80 check inter 1s
```

Trong đó:

* `listen api-server-6443`: Cổng 6443 xử lý lưu lượng truy cập Kubernetes API và trỏ đến các máy thuộc mặt phẳng điều khiển (control plane). Bạn phải định cấu hình kiểm tra trạng thái (health check) trên cổng này để đảm bảo rằng API server khả dụng trước khi định tuyến lưu lượng truy cập.
* `server bootstrap bootstrap.ocp4.example.com`: Các mục cấu hình bootstrap phải được thiết lập trước khi cài đặt cụm OpenShift Container Platform và phải được loại bỏ sau khi quá trình bootstrap hoàn tất.
* `listen machine-config-server`: Cổng 22623 xử lý lưu lượng truy cập của machine config server và trỏ đến các máy thuộc mặt phẳng điều khiển.
* `listen ingress-router-443`: Cổng 443 xử lý lưu lượng truy cập HTTPS và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán (compute nodes).
* `listen ingress-router-80`: Cổng 80 xử lý lưu lượng truy cập HTTP và trỏ đến các máy chạy các pod Ingress Controller. Theo mặc định, các pod Ingress Controller chạy trên các máy tính toán. 

> **Lưu ý:** Nếu bạn đang triển khai một cụm gồm ba nút (compact three-node cluster) mà không có các nút tính toán riêng biệt, các pod Ingress Controller sẽ chạy trên các nút thuộc mặt phẳng điều khiển. Trong các triển khai cụm ba nút, bạn phải định cấu hình bộ cân bằng tải Ingress cho ứng dụng để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút thuộc mặt phẳng điều khiển.

#### 2.3.5. Tạo đối tượng manifest bao gồm cầu nối br-ex tùy chỉnh
Theo mặc định, OpenShift Container Platform tự động cấu hình cầu nối Open vSwitch (OVS) `br-ex` trên các nút bare-metal (phần cứng vật lý). Đối với các yêu cầu mạng nâng cao, bạn có thể ghi đè hành vi mặc định này trên các nền tảng bare-metal. Để thực hiện việc này, hãy tạo một đối tượng `MachineConfig` bao gồm tệp cấu hình NMState.

> **Lưu ý:** Bạn có thể sử dụng Butane để tạo các cấu hình máy (machine config). Để biết thêm thông tin, hãy xem phần "Tạo đối tượng MachineConfig bằng cách sử dụng Butane".

Hãy cân nhắc sử dụng cấu hình cầu nối `br-ex` tùy chỉnh cho bất kỳ tác vụ nào sau đây:

* Bạn cần sửa đổi cầu nối `br-ex` sau khi đã cài đặt cụm (cluster). 
* Bạn cần sửa đổi đơn vị truyền tải tối đa (MTU) cho cụm của mình. 
* Bạn cần cập nhật các giá trị DNS. 
* Bạn cần sửa đổi các thuộc tính cho một giao diện liên kết (bond interface) khác. Các ví dụ bao gồm MIImon (Media Independent Interface Monitor), chế độ liên kết (bonding mode) hoặc Chất lượng dịch vụ (QoS). 
* Bạn cần bật Giao thức khám phá lớp liên kết (Link Layer Discovery Protocol - LLDP) để phát hiện và khắc phục sự cố kết nối với thiết bị chuyển mạch (switch).

> **Lưu ý:** Hãy sử dụng cầu nối OVS br-ex mặc định cho các môi trường tiêu chuẩn. Hãy sử dụng cơ chế cầu nối OVS br-ex mặc định cho các môi trường chỉ có một bộ điều khiển giao diện mạng (NIC) với các thiết lập mạng mặc định.

Sau khi bạn cài đặt Red Hat Enterprise Linux CoreOS (RHCOS) và hệ thống khởi động lại, Machine Config Operator sẽ đưa các tệp cấu hình Ignition vào từng nút. Thao tác này đảm bảo rằng mỗi nút đều nhận được cấu hình mạng cho cầu nối br-ex. Để ngăn ngừa xung đột cấu hình, cơ chế cầu nối OVS br-ex mặc định sẽ bị vô hiệu hóa.

> Cảnh báo: Danh sách tên giao diện sau đây đã được dành riêng; bạn không được sử dụng các tên này trong cấu hình NMState:
> `br-ext`, `br-int`, `br-local`, `br-nexthop`, `br0`, `ext-vxlan`, `ext`, `genev_sys_*`, `int`, `k8s-*`, `ovn-k8s-*`, `patch-br-*`, `tun0`, `vxlan_sys_*`

**Điều kiện tiên quyết**

* Tùy chọn: Bạn đã cài đặt công cụ dòng lệnh (CLI) nmstatectl để xác thực cấu hình NMState của mình.

**Quy trình**

1. Tạo tệp cấu hình NMState và xác định cấu hình mạng cầu nối (bridge network) `br-ex` tùy chỉnh trong tệp đó:

Ví dụ về cấu hình NMState cho mạng cầu nối `br-ex` tùy chỉnh

```yaml
interfaces:
- name: enp2s0
  type: ethernet
  state: up
  mtu: 9000
  ipv4:
    enabled: false
  ipv6:
    enabled: false
- name: br-ex
  type: ovs-bridge
  state: up
  ipv4:
    enabled: false
    dhcp: false
  ipv6:
    enabled: false
    dhcp: false
  bridge:
    options:
      mcast-snooping-enable: true
    port:
    - name: enp2s0
    - name: br-ex
- name: br-ex
  type: ovs-interface
  state: up
  copy-mac-from: enp2s0
  mtu: 9000
  ipv4:
    enabled: true
    dhcp: true
    auto-route-metric: 48
  ipv6:
    enabled: true
    dhcp: true
    auto-route-metric: 48
# ...
```

Trong đó:

  * `interfaces.name`: Xác định tên của giao diện (interface).
  * `interfaces.type`: Xác định loại giao diện Ethernet.
  * `interfaces.state`: Xác định trạng thái mong muốn cho giao diện sau khi được tạo.
  * `mtu`: Để đảm bảo tính ổn định và hiệu suất mạng, bạn phải khai báo rõ ràng giá trị MTU trong tệp cấu hình (manifest) cho mọi giao diện. Không nên dựa vào cơ chế cấu hình MTU tự động. Giá trị MTU được thiết lập trên một cổng cầu nối (bridge port) hoặc giao diện có gắn thẻ VLAN không được vượt quá kích thước khung hình (frame size) tối đa mà phương tiện vật lý kết nối hỗ trợ. Nếu các giá trị này không khớp, sẽ dẫn đến tình trạng phân mảnh gói tin hoặc mất kết nối.
  * `ipv4.enabled`: Trong ví dụ này, tùy chọn này dùng để vô hiệu hóa IPv4 và IPv6.
  * `port.name`: Xác định card mạng (NIC) của nút (node) mà cầu nối (bridge) sẽ kết nối vào.
  * `auto-route-metric`: Thiết lập tham số này thành 48 để đảm bảo tuyến đường mặc định (default route) của br-ex luôn có mức ưu tiên cao nhất (giá trị metric thấp nhất). Cấu hình này giúp ngăn ngừa xung đột định tuyến với các giao diện khác được dịch vụ NetworkManager tự động cấu hình.

2. Sử dụng lệnh cat để mã hóa nội dung cấu hình NMState sang định dạng base64:

```shell
$ cat <nmstate_configuration>.yml | base64
```

Trong đó: Thay thế `<nmstate_configuration>` bằng tên tệp YAML tài nguyên NMState của bạn.

3. Hãy tạo một tệp manifest MachineConfig và xác định cấu hình mạng cầu nối (bridge network) `br-ex` tùy chỉnh, tương tự như ví dụ dưới đây. Chương trình cài đặt sẽ tự động áp dụng các cập nhật từ đối tượng MachineConfig này vào cụm (cluster) của bạn.

```yaml
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 10-br-ex-worker
spec:
  config:
    ignition:
      version: 3.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/worker-0.yml
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/worker-1.yml
# ...
```

Trong đó:

  * `metadata.name`: Xác định tên của chính sách (policy).
  * `contents.sourc`e: Ghi thông tin đã mã hóa base64 vào đường dẫn được chỉ định.
  * `path`: Đối với mỗi nút (node) trong cụm (cluster) của bạn, hãy chỉ định đường dẫn dựa trên tên máy chủ (hostname) của nút đó cùng với dữ liệu tệp cấu hình Ignition (đã mã hóa base64) tương ứng với loại máy đó. Vai trò `worker` là vai trò mặc định cho các nút trong cụm của bạn. Bạn bắt buộc phải sử dụng phần mở rộng `.yml` cho các tệp cấu hình. Ví dụ: hãy sử dụng `$(hostname -s)`.yml khi chỉ định đường dẫn theo tên máy chủ rút gọn cho từng nút hoặc tất cả các nút trong tệp manifest `MachineConfig`. 

Bạn có thể áp dụng một cấu hình chung duy nhất cho tất cả các nút trong cụm bằng cách sử dụng tệp cấu hình `/etc/nmstate/openshift/cluster.yml`. Trong trường hợp này, bạn không cần phải chỉ định đường dẫn theo tên máy chủ rút gọn cho từng nút (ví dụ: `/etc/nmstate/openshift/<node_hostname>.yml`). Ví dụ:

Ví dụ về tệp cấu hình `/etc/nmstate/openshift/cluster.yml`

```yaml
# ...
      - contents:
          source: data:text/plain;charset=utf-8;base64,<base64_encoded_nmstate_configuration>
        mode: 0644
        overwrite: true
        path: /etc/nmstate/openshift/cluster.yml
# ...
```

**Các bước tiếp theo**

* Mở rộng quy mô các nút tính toán (compute node) bằng cách áp dụng đối tượng manifest — bao gồm cấu hình cầu nối `br-ex` tùy chỉnh — cho từng nút tính toán hiện có trong cụm (cluster) của bạn. Để biết thêm thông tin, hãy xem phần "Mở rộng cụm".

##### 2.3.5.1. Mở rộng quy mô từng tập hợp máy (machine set) thành các nút tính toán (compute node)
Để mở rộng quy mô từng tập hợp máy thành các nút tính toán, bạn phải áp dụng cấu hình cầu nối `br-ex` tùy chỉnh cho tất cả các nút tính toán trong cụm OpenShift Container Platform của mình. Sau đó, bạn cần chỉnh sửa tài nguyên tùy chỉnh (Custom Resource - CR) `MachineConfig` và sửa đổi các vai trò (roles) của nó.

Ngoài ra, bạn phải tạo một CR `BareMetalHost` để xác định thông tin cho máy vật lý (bare-metal machine) của mình, chẳng hạn như tên máy chủ (hostname), thông tin xác thực và các tham số cần thiết khác. Sau khi định cấu hình các tài nguyên này, bạn cần mở rộng quy mô các tập hợp máy để chúng có thể áp dụng cấu hình tài nguyên đó cho từng nút tính toán và khởi động lại các nút này.

**Điều kiện tiên quyết**

* Bạn đã tạo một đối tượng manifest `MachineConfig` bao gồm cấu hình cầu nối `br-ex` tùy chỉnh.

**Quy trình**

1. Chỉnh sửa CR `MachineConfig` bằng cách nhập lệnh sau:

```shell
$ oc edit mc <machineconfig_custom_resource_name>
```

2. Hãy thêm cấu hình của từng nút tính toán (compute node) vào CR, để CR có thể quản lý vai trò cho mỗi nút tính toán đã xác định trong cụm của bạn.
3. Tạo một đối tượng Secret có tên là `extraworker-secret`, chứa cấu hình IP tĩnh tối thiểu.
4. Áp dụng Secret `extraworker-secret` cho từng nút trong cụm bằng cách nhập lệnh sau. Bước này cho phép mỗi nút tính toán truy cập vào tệp cấu hình Ignition.

```shell
$ oc apply -f ./extraworker-secret.yaml
```

5. Tạo tài nguyên `BareMetalHost` và chỉ định bí mật mạng (network secret) trong tham số `preprovisioningNetworkDataName`:

Ví dụ về tài nguyên `BareMetalHost` có đính kèm bí mật mạng

```yaml
apiVersion: metal3.io/v1alpha1
kind: BareMetalHost
spec:
# ...
  preprovisioningNetworkDataName: ostest-extraworker-0-network-config-secret
# ...
```

6. Để quản lý đối tượng `BareMetalHost` trong namespace `openshift-machine-api` của cụm, hãy chuyển sang namespace đó bằng cách nhập lệnh sau:

```shell
$ oc project openshift-machine-api
```

7. Lấy các machine set:

```shell
$ oc get machinesets
```

8. Điều chỉnh quy mô từng tập hợp máy bằng cách nhập lệnh sau. Bạn phải chạy lệnh này cho mỗi tập hợp máy.

```shell
$ oc scale machineset <machineset_name> --replicas=<n>
```

Trong đó:
  * `<machineset_name>`: Chỉ định tên của tập hợp máy (machine set).
  * `<n>`: Chỉ định số lượng nút tính toán (compute node).

#### 2.3.6. Chuẩn bị cơ sở hạ tầng do người dùng cung cấp
Trước khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải chuẩn bị cơ sở hạ tầng nền tảng.

Phần này cung cấp thông tin chi tiết về các bước tổng quát cần thiết để thiết lập cơ sở hạ tầng cụm (cluster) nhằm chuẩn bị cho việc cài đặt OpenShift Container Platform. Các bước này bao gồm cấu hình mạng IP và kết nối mạng cho các nút (node) trong cụm, mở các cổng (port) cần thiết thông qua tường lửa, cũng như thiết lập cơ sở hạ tầng DNS và cân bằng tải (load balancing).

Sau khi hoàn tất quá trình chuẩn bị, cơ sở hạ tầng cụm của bạn phải đáp ứng các yêu cầu được nêu trong phần "Các yêu cầu đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp".

**Điều kiện tiên quyết**

* Bạn đã xem qua trang "Các tích hợp đã được kiểm thử cho OpenShift Container Platform 4.x" (OpenShift Container Platform 4.x Tested Integrations). 
* Bạn đã xem qua các yêu cầu về cơ sở hạ tầng được nêu chi tiết trong phần "Các yêu cầu đối với cụm sử dụng cơ sở hạ tầng do người dùng cung cấp".

**Quy trình**

1. Nếu bạn sử dụng DHCP để cung cấp cấu hình mạng IP cho các nút trong cụm, hãy cấu hình dịch vụ DHCP của bạn. 
  * Thêm các địa chỉ IP cố định cho các nút vào cấu hình máy chủ DHCP. Trong cấu hình, hãy ánh xạ địa chỉ MAC của giao diện mạng tương ứng với địa chỉ IP dự định cho mỗi nút. 
  * Khi sử dụng DHCP để cấu hình địa chỉ IP cho các máy trong cụm, các máy này cũng sẽ nhận thông tin máy chủ DNS thông qua DHCP. Hãy xác định địa chỉ máy chủ DNS cố định mà các nút trong cụm sẽ sử dụng thông qua cấu hình máy chủ DHCP của bạn. 

  > **Lưu ý:** Nếu không sử dụng dịch vụ DHCP, bạn phải cung cấp cấu hình mạng IP và địa chỉ máy chủ DNS cho các nút tại thời điểm cài đặt RHCOS. Các thông tin này có thể được truyền dưới dạng tham số khởi động (boot arguments) nếu bạn cài đặt từ tệp ảnh ISO. Xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) OpenShift Container Platform" để biết thêm thông tin về việc cấp phát IP tĩnh và các tùy chọn mạng nâng cao. 

  * Xác định tên máy (hostname) cho các nút trong cụm thông qua cấu hình máy chủ DHCP. Xem phần "Thiết lập tên máy cho các nút trong cụm thông qua DHCP" để biết chi tiết về các lưu ý liên quan đến tên máy. 

  > **Lưu ý:** Nếu không sử dụng dịch vụ DHCP, các nút trong cụm sẽ nhận tên máy thông qua truy vấn DNS ngược (reverse DNS lookup).

2. Đảm bảo rằng cơ sở hạ tầng mạng của bạn cung cấp khả năng kết nối mạng cần thiết giữa các thành phần trong cụm (cluster). Hãy xem phần "Yêu cầu về mạng đối với cơ sở hạ tầng do người dùng cung cấp" (Networking requirements for user-provisioned infrastructure) để biết chi tiết về các yêu cầu này.

3. Cấu hình tường lửa để mở các cổng cần thiết cho phép các thành phần trong cụm OpenShift Container Platform giao tiếp với nhau. Hãy xem phần "Yêu cầu về mạng đối với cơ sở hạ tầng do người dùng cung cấp" để biết chi tiết về các cổng cần thiết.

> **Quan trọng:** Theo mặc định, cổng 1936 luôn mở trong cụm OpenShift Container Platform vì mỗi nút điều khiển (control plane node) đều cần truy cập vào cổng này. Đối với các thăm dò kiểm tra trạng thái (health check probes) của Ingress, điểm cuối (endpoint) `/healthz/ready` khả dụng trên cổng này. Tránh sử dụng bộ cân bằng tải Ingress (Ingress load balancer) để công khai cổng này, vì việc đó có thể làm lộ các thông tin nhạy cảm, chẳng hạn như số liệu thống kê và các chỉ số liên quan đến Ingress Controller.

4. Thiết lập cơ sở hạ tầng DNS cần thiết cho cụm của bạn. 
  * Cấu hình phân giải tên DNS cho Kubernetes API, tên miền đại diện (wildcard) cho ứng dụng, máy khởi động (bootstrap machine), các máy điều khiển (control plane machines) và các máy tính toán (compute machines). 
  * Cấu hình phân giải DNS ngược (reverse DNS resolution) cho Kubernetes API, máy khởi động, các máy điều khiển và các máy tính toán. Xem phần "Yêu cầu về DNS đối với cơ sở hạ tầng do người dùng cung cấp" (User-provisioned DNS requirements) để biết thêm thông tin về các yêu cầu DNS của OpenShift Container Platform.

5. Xác thực cấu hình DNS của bạn. 
  * Từ nút cài đặt (installation node), hãy thực hiện truy vấn DNS đối với các tên bản ghi của Kubernetes API, các tuyến đường đại diện (wildcard routes) và các nút trong cụm. Kiểm tra xem các địa chỉ IP nhận được có tương ứng với đúng các thành phần hay không. 
  * Từ nút cài đặt, hãy thực hiện truy vấn DNS ngược đối với các địa chỉ IP của bộ cân bằng tải và các nút trong cụm. Kiểm tra xem các tên bản ghi nhận được có tương ứng với đúng các thành phần hay không. Xem phần "Xác thực phân giải DNS đối với cơ sở hạ tầng do người dùng cung cấp" (Validating DNS resolution for user-provisioned infrastructure) để biết các bước xác thực DNS chi tiết.
6. Triển khai cơ sở hạ tầng cân bằng tải cần thiết cho API và Ingress ứng dụng. Xem phần "Yêu cầu về cân bằng tải đối với cơ sở hạ tầng do người dùng cung cấp" (Load balancing requirements for user-provisioned infrastructure) để biết thêm thông tin về các yêu cầu này.

> **Lưu ý:** Một số giải pháp cân bằng tải yêu cầu hệ thống phân giải tên DNS cho các nút trong cụm phải được thiết lập hoàn chỉnh trước khi quá trình cân bằng tải được khởi tạo.

#### 2.3.7. Xác thực phân giải DNS cho cơ sở hạ tầng do người dùng cung cấp
Để ngăn ngừa các lỗi cài đặt liên quan đến mạng và đảm bảo khả năng kết nối giữa các nút (node) trong OpenShift Container Platform, hãy xác thực cấu hình DNS trước khi triển khai trên cơ sở hạ tầng do người dùng cung cấp.

> **Quan trọng:** Các bước xác thực được nêu chi tiết trong phần này phải được thực hiện thành công trước khi bạn cài đặt cụm (cluster) của mình.

**Điều kiện tiên quyết**

* Bạn đã cấu hình các bản ghi DNS cần thiết cho cơ sở hạ tầng do người dùng cung cấp.

**Quy trình**

1. Từ nút cài đặt (installation node), hãy thực hiện truy vấn DNS đối với tên các bản ghi của Kubernetes API, các tuyến đường đại diện (wildcard routes) và các nút trong cụm. Hãy xác thực rằng các địa chỉ IP nhận được trong phản hồi tương ứng với các thành phần chính xác. 
  * Thực hiện truy vấn đối với tên bản ghi Kubernetes API. Kiểm tra để đảm bảo kết quả trỏ đến địa chỉ IP của bộ cân bằng tải (load balancer) dành cho API:

  ```shell
  $ dig +noall +answer @<nameserver_ip> api.<cluster_name>.<base_domain>
  ```

  Hãy thay thế `<nameserver_ip>` bằng địa chỉ IP của máy chủ tên miền (name server), `<cluster_name>` bằng tên cụm (cluster) của bạn và `<base_domain>` bằng tên miền cơ sở (base domain) của bạn.

  Ví dụ đầu ra

  ```
  api.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  * Thực hiện tra cứu dựa trên tên bản ghi API nội bộ của Kubernetes. Kiểm tra để đảm bảo rằng kết quả trỏ đến địa chỉ IP của bộ cân bằng tải API:

  ```shell
  $ dig +noall +answer @<nameserver_ip> api-int.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  api-int.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  * Hãy kiểm tra một ví dụ về truy vấn DNS wildcard dạng `*.apps.<cluster_name>.<base_domain>`. Tất cả các truy vấn wildcard cho ứng dụng đều phải phân giải về địa chỉ IP của bộ cân bằng tải (load balancer) dành cho ingress của ứng dụng:

  ```shell
  $ dig +noall +answer @<nameserver_ip> random.apps.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  random.apps.ocp4.example.com.   604800  IN  A 192.168.1.5
  ```

  > **Lưu ý:** Trong các kết quả đầu ra mẫu, cùng một bộ cân bằng tải (load balancer) được sử dụng cho cả Kubernetes API và lưu lượng truy cập ứng dụng (application ingress). Trong các môi trường thực tế (production), bạn có thể triển khai riêng biệt các bộ cân bằng tải cho API và cho lưu lượng truy cập ứng dụng, nhờ đó có thể mở rộng quy mô hạ tầng cân bằng tải cho từng thành phần một cách độc lập.

  Bạn có thể thay thế giá trị `random` bằng một giá trị đại diện (wildcard) khác. Chẳng hạn, bạn có thể truy vấn đường dẫn (route) đến bảng điều khiển OpenShift Container Platform:

  ```shell
  $ dig +noall +answer @<nameserver_ip> console-openshift-console.apps.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  console-openshift-console.apps.ocp4.example.com. 604800 IN  A 192.168.1.5
  ```

  * Thực hiện tra cứu dựa trên tên bản ghi DNS bootstrap. Kiểm tra để đảm bảo kết quả trỏ đến địa chỉ IP của nút bootstrap:

  ```shell
  $ dig +noall +answer @<nameserver_ip> bootstrap.<cluster_name>.<base_domain>
  ```

  Ví dụ đầu ra

  ```
  bootstrap.ocp4.example.com.   604800  IN  A 192.168.1.96
  ```

  * Sử dụng phương pháp này để thực hiện tra cứu dựa trên các tên bản ghi DNS của các nút mặt phẳng điều khiển (control plane) và các nút tính toán (compute nodes). Hãy kiểm tra để đảm bảo rằng các kết quả thu được tương ứng với địa chỉ IP của từng nút.

2. Từ nút cài đặt của bạn, hãy thực hiện truy vấn DNS ngược (reverse DNS lookup) đối với các địa chỉ IP của bộ cân bằng tải (load balancer) và các nút trong cụm (cluster nodes). Hãy xác minh rằng các tên bản ghi (record names) trong phản hồi tương ứng với các thành phần chính xác. 
  * Thực hiện truy vấn ngược đối với địa chỉ IP của bộ cân bằng tải API. Kiểm tra xem phản hồi có bao gồm các tên bản ghi dành cho Kubernetes API và Kubernetes internal API hay không:

  ```shell
  $ dig +noall +answer @<nameserver_ip> -x 192.168.1.5
  ```

  Ví dụ đầu ra

  ```
  5.1.168.192.in-addr.arpa. 604800  IN  PTR api-int.ocp4.example.com.
  5.1.168.192.in-addr.arpa. 604800  IN  PTR api.ocp4.example.com.
  ```

  Trong đó:

    * api-int.ocp4.example.com: Chỉ định tên bản ghi cho API nội bộ của Kubernetes.
    * api.ocp4.example.com: Chỉ định tên bản ghi cho API của Kubernetes. 

  > **Lưu ý:** Không yêu cầu bản ghi PTR đối với tên miền đại diện (wildcard) cho ứng dụng trên OpenShift Container Platform. Không cần thực hiện bước xác thực thông qua truy vấn DNS ngược (reverse DNS resolution) đối với địa chỉ IP của bộ cân bằng tải (load balancer) cho lưu lượng truy cập ứng dụng (ingress).

  * Thực hiện truy vấn ngược (reverse lookup) đối với địa chỉ IP của nút khởi động (bootstrap node). Kiểm tra để đảm bảo rằng kết quả trả về trỏ đến tên bản ghi DNS của nút khởi động đó:

  ```shell
  $ dig +noall +answer @<nameserver_ip> -x 192.168.1.96
  ```

  Ví dụ đầu ra

  ```
  96.1.168.192.in-addr.arpa. 604800 IN  PTR bootstrap.ocp4.example.com.
  ```

  * Sử dụng phương pháp này để thực hiện tra cứu ngược đối với các địa chỉ IP của các nút điều khiển (control plane) và các nút tính toán (compute nodes). Hãy kiểm tra để đảm bảo rằng các kết quả thu được tương ứng với tên bản ghi DNS của từng nút.

#### 2.3.8. Tạo cặp khóa để truy cập SSH vào các nút cụm (cluster node)
Trong quá trình cài đặt OpenShift Container Platform, bạn có thể cung cấp khóa công khai (public key) SSH cho chương trình cài đặt. Khóa này được chuyển đến các nút Red Hat Enterprise Linux CoreOS (RHCOS) thông qua các tệp cấu hình Ignition và được thêm vào tệp `~/.ssh/authorized_keys` của người dùng `core` trên mỗi nút.

Khóa này được thêm vào danh sách `~/.ssh/authorized_keys` của người dùng `core` trên từng nút, cho phép xác thực mà không cần mật khẩu. Sau khi khóa được chuyển đến các nút, bạn có thể sử dụng cặp khóa này để truy cập SSH vào các nút RHCOS với tư cách là người dùng `core`. Để truy cập các nút thông qua SSH, khóa bí mật (private key) phải được quản lý bởi SSH trên máy cục bộ của bạn.

Nếu bạn muốn truy cập SSH vào các nút trong cụm để thực hiện gỡ lỗi cài đặt hoặc khôi phục sau sự cố, bạn bắt buộc phải cung cấp khóa công khai SSH trong quá trình cài đặt. Lệnh `./openshift-install gather` cũng yêu cầu khóa công khai SSH phải được thiết lập sẵn trên các nút của cụm.

> **Quan trọng:** Đừng bỏ qua bước này trong các môi trường thực tế (production), nơi việc khôi phục sau sự cố và gỡ lỗi là cần thiết.

> **Lưu ý:** Bạn phải sử dụng khóa cục bộ (local key), không phải khóa được cấu hình theo các phương thức dành riêng cho từng nền tảng (platform-specific).

**Quy trình**

1. Nếu bạn chưa có cặp khóa SSH trên máy cục bộ để sử dụng cho việc xác thực vào các nút trong cụm, hãy tạo một cặp khóa mới. Ví dụ, trên máy tính sử dụng hệ điều hành Linux, hãy chạy lệnh sau:

```shell
$ ssh-keygen -t ed25519 -N '' -f <path>/<file_name>
```

Xác định đường dẫn và tên tệp (ví dụ: `~/.ssh/id_ed25519`) cho khóa SSH mới. Nếu bạn đã có sẵn một cặp khóa, hãy đảm bảo rằng khóa công khai (public key) của bạn nằm trong thư mục `~/.ssh`.

> **Lưu ý:** Nếu bạn dự định cài đặt cụm OpenShift Container Platform sử dụng các thư viện mật mã RHEL — vốn đã được gửi tới NIST để đánh giá theo tiêu chuẩn FIPS 140-2/140-3 trên các kiến trúc `x86_64`, `ppc64le` và `s390x` — thì không nên tạo khóa sử dụng thuật toán `ed25519`. Thay vào đó, hãy tạo khóa sử dụng thuật toán `rsa` hoặc `ecdsa`.

2. Xem khóa SSH công khai:

```shell
cat <path>/<file_name>.pub
```

Ví dụ, hãy chạy lệnh sau để xem khóa công khai `~/.ssh/id_ed25519.pub`:

```shell
$ cat ~/.ssh/id_ed25519.pub
```

3. Hãy thêm khóa cá nhân (private key) SSH vào SSH agent của người dùng cục bộ, nếu khóa đó chưa được thêm vào. Việc quản lý khóa thông qua SSH agent là bắt buộc để có thể xác thực SSH không cần mật khẩu vào các nút (node) trong cụm (cluster) của bạn, hoặc nếu bạn muốn sử dụng lệnh `./openshift-install gather`.

> **Lưu ý:** Trên một số bản phân phối, các khóa cá nhân SSH mặc định như `~/.ssh/id_rsa` và `~/.ssh/id_dsa` sẽ được tự động quản lý. 

  * Nếu tiến trình `ssh-agent` chưa chạy đối với người dùng cục bộ của bạn, hãy khởi chạy nó dưới dạng một tác vụ chạy ngầm (background task):

  ```shell
  $ eval "$(ssh-agent -s)"
  ```

  Ví dụ đầu ra

  ```
  Agent pid 31874
  ```

  > **Lưu ý:** Nếu cụm của bạn đang ở chế độ FIPS, chỉ sử dụng các thuật toán tuân thủ tiêu chuẩn FIPS để tạo khóa SSH. Khóa này phải thuộc loại RSA hoặc ECDSA.

4. Thêm khóa cá nhân SSH của bạn vào ssh-agent:

```shell
$ ssh-add <path>/<file_name>
```

Hãy chỉ rõ đường dẫn và tên tệp cho khóa cá nhân SSH của bạn, chẳng hạn như `~/.ssh/id_ed25519`.

Ví dụ đầu ra

```
Identity added: /home/<you>/<path>/<file_name> (<computer_name>)
```

**Các bước tiếp theo**

* Khi cài đặt OpenShift Container Platform, hãy cung cấp khóa công khai SSH cho chương trình cài đặt. Nếu bạn cài đặt cụm trên cơ sở hạ tầng do chính mình cung cấp, bạn bắt buộc phải cung cấp khóa này cho chương trình cài đặt.

#### 2.3.9. Tạo thủ công tệp cấu hình cài đặt
Việc cài đặt cụm (cluster) yêu cầu bạn phải tự tạo tệp cấu hình cài đặt.

**Các điều kiện tiên quyết**

* Bạn cần có khóa công khai SSH trên máy cục bộ để sử dụng với chương trình cài đặt. Khóa này có thể được dùng để xác thực SSH vào các nút (node) trong cụm nhằm mục đích gỡ lỗi và khôi phục sau sự cố. 
* Bạn đã có chương trình cài đặt OpenShift Container Platform và "pull secret" (bí mật kéo hình ảnh) cho cụm của mình. 
* Lấy phần `imageContentSources` từ kết quả đầu ra của lệnh thực hiện sao chép kho lưu trữ (mirror repository). 
* Lấy nội dung chứng chỉ cho registry sao chép (mirror registry) của bạn.

**Quy trình**

1. Tạo một thư mục cài đặt để lưu trữ các tài nguyên cần thiết cho quá trình cài đặt:

```shell
$ mkdir <installation_directory>
```

> **Quan trọng:** Bạn phải tạo một thư mục mới. Một số thành phần cài đặt, chẳng hạn như các chứng chỉ X.509 khởi tạo (bootstrap), có thời hạn hiệu lực ngắn; do đó, bạn không được sử dụng lại thư mục cài đặt cũ. Nếu muốn sử dụng lại các tệp tin riêng lẻ từ một quá trình cài đặt cụm khác, bạn có thể sao chép chúng vào thư mục của mình. Tuy nhiên, tên của các tệp tin cài đặt có thể thay đổi giữa các phiên bản phát hành. Hãy thận trọng khi sao chép các tệp tin cài đặt từ một phiên bản OpenShift Container Platform cũ hơn.

2. Hãy tùy chỉnh tệp mẫu `install-config.yaml` được cung cấp và lưu tệp đó vào `<thư_mục_cài_đặt>`.

> **Lưu ý:** Bạn bắt buộc phải đặt tên cho tệp cấu hình này là `install-config.yaml`. 

  * Trừ khi bạn sử dụng một registry mà RHCOS tin tưởng theo mặc định (ví dụ: `docker.io`), bạn phải cung cấp nội dung chứng chỉ cho kho lưu trữ (repository) nhân bản (mirror) của mình trong phần `additionalTrustBundle`. Trong hầu hết các trường hợp, bạn đều cần cung cấp chứng chỉ cho kho lưu trữ nhân bản đó. 
  * Bạn phải đưa phần `imageContentSources` (lấy từ kết quả đầu ra của lệnh thực hiện nhân bản kho lưu trữ) vào tệp cấu hình này.

> **Quan trọng:**
> 
> * Tệp `ImageContentSourcePolicy` được tạo ra dưới dạng kết quả đầu ra của lệnh `oc mirror` sau khi quá trình sao chép (mirroring) hoàn tất. 
> * Lệnh `oc mirror` tạo ra một tệp `ImageContentSourcePolicy` chứa mã YAML cần thiết để định nghĩa `ImageContentSourcePolicy`. Hãy sao chép nội dung từ tệp này và dán vào tệp `install-config.yaml` của bạn. 
> * Bạn phải chạy lệnh `oc mirror` hai lần. Lần chạy đầu tiên sẽ cung cấp một tệp ImageContentSourcePolicy đầy đủ. Lần chạy thứ hai chỉ cung cấp phần khác biệt so với lần chạy đầu tiên. Do cơ chế này, bạn luôn cần lưu giữ bản sao lưu của các tệp đó để phòng trường hợp cần hợp nhất chúng thành một tệp `ImageContentSourcePolicy` hoàn chỉnh. Việc lưu giữ bản sao lưu của cả hai tệp đầu ra này đảm bảo rằng bạn luôn có trong tay một tệp `ImageContentSourcePolicy` đầy đủ.

3. Hãy sao lưu tệp install-config.yaml để có thể sử dụng tệp này cho việc cài đặt nhiều cụm (cluster).

> **Quan trọng:** Hãy sao lưu tệp install-config.yaml ngay bây giờ, vì quy trình cài đặt sẽ tiêu thụ tệp này trong bước tiếp theo.

##### 2.3.9.1. Tệp install-config.yaml mẫu cho môi trường bare metal
Bạn có thể tùy chỉnh tệp install-config.yaml để chỉ định thêm thông tin chi tiết về nền tảng cụm OpenShift Container Platform của mình hoặc sửa đổi các giá trị của những tham số bắt buộc.

```yaml
apiVersion: v1
baseDomain: example.com
compute:
- hyperthreading: Enabled
  name: worker
  replicas: 0
controlPlane:
  hyperthreading: Enabled
  name: master
  replicas: 3
metadata:
  name: test
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  networkType: OVNKubernetes
  machineNetwork:
  - cidr: 192.168.0.0/16
  serviceNetwork:
  - 172.30.0.0/16
platform:
  none: {}
fips: false
pullSecret: '{"auths":{"<local_registry>": {"auth": "<credentials>","email": "you@example.com"}}}'
sshKey: 'ssh-ed25519 AAAA...'
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

Trong đó:

* `baseDomain`: Xác định tên miền cơ sở (base domain) của cụm (cluster). Tất cả các bản ghi DNS phải là tên miền phụ (sub-domain) của tên miền cơ sở này và phải bao gồm tên của cụm.
* `compute`: Xác định cấu hình cho các nút tính toán (compute node), được biểu diễn dưới dạng một chuỗi các ánh xạ (mappings). Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `compute` phải bắt đầu bằng một dấu gạch ngang (-).
* `controlPlane`: Xác định cấu hình cho các nút điều khiển (control plane node), được biểu diễn dưới dạng một ánh xạ duy nhất. Để đáp ứng các yêu cầu về cấu trúc dữ liệu, dòng đầu tiên của phần `controlPlane` không được bắt đầu bằng dấu gạch ngang. Chỉ một nhóm (pool) nút điều khiển được sử dụng.
* `hyperthreading`: Xác định việc bật hay tắt tính năng đa luồng đồng thời (Simultaneous Multithreading - SMT), hay còn gọi là `hyperthreading`. Theo mặc định, SMT được bật để tăng hiệu suất cho các lõi (core) trên máy chủ của bạn. Bạn có thể tắt tính năng này bằng cách đặt giá trị tham số thành `Disabled`. Nếu tắt SMT, bạn phải thực hiện tắt trên tất cả các máy chủ trong cụm, bao gồm cả các nút điều khiển (control plane) và các nút tính toán (compute).

> **Lưu ý:** Tính năng đa luồng đồng thời (SMT) được bật theo mặc định. Nếu SMT chưa được bật trong cài đặt BIOS, thì tham số `hyperthreading` sẽ không có tác dụng.

> **Quan trọng:** Nếu bạn tắt `hyperthreading` (dù là trong BIOS hay trong tệp `install-config.yaml`), hãy đảm bảo rằng kế hoạch phân bổ tài nguyên (capacity planning) của bạn đã tính đến việc hiệu suất máy chủ sẽ giảm đáng kể.

* `compute.replicas`: Xác định số lượng máy tính toán (compute machine) mà cụm (cluster) sẽ tạo và quản lý thay cho bạn trong các quy trình cài đặt do trình cài đặt cung cấp (installer-provisioned installations). Bạn phải đặt giá trị này là 0 khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure). Ngoài ra, đối với các quy trình cài đặt do người dùng cung cấp, bạn phải tự triển khai các máy tính toán trước khi hoàn tất việc cài đặt cụm.

> **Lưu ý:** Nếu bạn đang cài đặt một cụm gồm ba nút (three-node cluster), đừng triển khai thêm bất kỳ máy tính toán nào khi thực hiện cài đặt các máy Red Hat Enterprise Linux CoreOS (RHCOS).

* `controlPlane.replicas`: Xác định số lượng máy điều khiển (control plane machine) mà bạn thêm vào cụm. Vì cụm sử dụng các giá trị này để xác định số lượng điểm cuối (endpoint) etcd trong cụm, nên giá trị này phải khớp với số lượng máy điều khiển mà bạn triển khai.
* `metadata.name`: Xác định tên của cụm, tên này phải trùng với tên bạn đã chỉ định trong các bản ghi DNS của mình.
* `networking.clusterNetwork.cidr`: Xác định một khối địa chỉ IP dùng để cấp phát địa chỉ IP cho các pod. Khối này không được trùng lặp với các mạng vật lý hiện có. Các địa chỉ IP này được sử dụng cho mạng của pod. Nếu bạn cần truy cập vào các pod từ một mạng bên ngoài, bạn phải cấu hình bộ cân bằng tải (load balancer) và bộ định tuyến (router) để quản lý lưu lượng truy cập.

> **Lưu ý:** Dải CIDR lớp E (Class E) được dành riêng cho mục đích sử dụng trong tương lai. Để sử dụng dải CIDR lớp E, bạn phải đảm bảo rằng môi trường mạng của mình chấp nhận các địa chỉ IP nằm trong dải này.

* `networking.cidr.hostPrefix`: Xác định độ dài tiền tố mạng con (subnet prefix length) được gán cho mỗi nút riêng lẻ. Ví dụ: nếu `hostPrefix` được đặt là `23`, thì mỗi nút sẽ được gán một mạng con `/23` từ dải CIDR đã cho, cho phép tạo ra `510 (2^(32 - 23) - 2)` địa chỉ IP cho các pod. Nếu bạn cần cho phép truy cập vào các nút từ một mạng bên ngoài, hãy cấu hình bộ cân bằng tải và bộ định tuyến để quản lý lưu lượng truy cập.
* `networking.networkTyp`e: Xác định plugin mạng của cụm cần cài đặt. Giá trị mặc định là OVNKubernetes, đây cũng là giá trị duy nhất được hỗ trợ.
* `networking.serviceNetwork`: Xác định nhóm địa chỉ IP (IP address pool) được sử dụng cho các địa chỉ IP của dịch vụ (service). Bạn chỉ có thể nhập một nhóm địa chỉ IP duy nhất. Khối này không được chồng lấn với các mạng vật lý hiện có. Nếu bạn cần truy cập các dịch vụ từ một mạng bên ngoài, hãy định cấu hình bộ cân bằng tải (load balancer) và bộ định tuyến (router) để quản lý lưu lượng truy cập.
* `platform`: Chỉ định nền tảng (platform). Bạn bắt buộc phải đặt giá trị cho platform là `none`. Bạn không được cung cấp thêm các biến cấu hình nền tảng nào khác.

> **Quan trọng:** Các cụm (cluster) được cài đặt với loại nền tảng là `none` sẽ không thể sử dụng một số tính năng, chẳng hạn như quản lý các máy tính toán (compute machine) thông qua Machine API. Hạn chế này vẫn áp dụng ngay cả khi các máy tính toán được kết nối với cụm đó được cài đặt trên một nền tảng vốn hỗ trợ tính năng này. Tham số này không thể thay đổi sau khi đã hoàn tất cài đặt.

* `fips`: Chỉ định việc bật hoặc tắt chế độ FIPS. Theo mặc định, chế độ FIPS không được bật. Nếu chế độ FIPS được bật, các máy chạy hệ điều hành Red Hat Enterprise Linux CoreOS (RHCOS) – nền tảng mà OpenShift Container Platform vận hành trên đó – sẽ bỏ qua bộ mật mã mặc định của Kubernetes và thay vào đó sử dụng các mô-đun mật mã được cung cấp kèm theo RHCOS.

> **Quan trọng:** Để bật chế độ FIPS cho cụm (cluster) của mình, bạn phải chạy chương trình cài đặt từ một máy tính sử dụng hệ điều hành Red Hat Enterprise Linux (RHEL) đã được cấu hình để hoạt động ở chế độ FIPS. Để biết thêm thông tin về cách cấu hình chế độ FIPS trên RHEL, hãy xem phần Chuyển đổi RHEL sang chế độ FIPS (Switching RHEL to FIPS mode).  
> Khi chạy Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã của RHEL – những thư viện đã được gửi tới NIST để thực hiện quy trình xác thực FIPS 140-2/140-3 – trên các kiến trúc `x86_64`, `ppc64le` và `s390x`.

* `pullSecret`: Chỉ định tên miền registry cho `<local_registry>` (và tùy chọn là cổng) mà registry phản chiếu (mirror registry) của bạn sử dụng để cung cấp nội dung. Ví dụ: registry.example.com hoặc registry.example.com:5000. Đối với `<credentials>`, hãy cung cấp tên người dùng và mật khẩu đã được mã hóa base64 cho registry phản chiếu của bạn.
* `sshKey`: Chỉ định khóa công khai SSH (SSH public key) cho người dùng cốt lõi (core user) trong Red Hat Enterprise Linux CoreOS (RHCOS).

> **Lưu ý:** Đối với các cụm OpenShift Container Platform trong môi trường sản xuất (production) mà bạn muốn thực hiện gỡ lỗi cài đặt hoặc khôi phục sau thảm họa, hãy chỉ định khóa SSH mà tiến trình ssh-agent của bạn đang sử dụng.

* `additionalTrustBundle`: Chỉ định nội dung của tệp chứng chỉ mà bạn đã sử dụng cho registry phản chiếu của mình.
* `imageContentSources`: Chỉ định phần `imageContentSources` dựa trên kết quả đầu ra từ lệnh mà bạn đã sử dụng để phản chiếu kho lưu trữ (repository).

> **Quan trọng:**
> 
> * Khi sử dụng lệnh `oc adm release mirror`, hãy sử dụng kết quả đầu ra từ phần `imageContentSources`. 
> * Khi sử dụng lệnh `oc mirror`, hãy sử dụng phần repositoryDigestMirrors từ tệp ImageContentSourcePolicy được tạo ra sau khi chạy lệnh đó.
> * `ImageContentSourcePolicy` hiện đã lỗi thời (deprecated). Để biết thêm thông tin, hãy xem phần Cấu hình phản chiếu kho lưu trữ registry hình ảnh (Configuring image registry repository mirroring).

##### 2.3.9.2. Cấu hình proxy cho toàn bộ cụm (cluster) trong quá trình cài đặt
Trong các môi trường thực tế (production), quyền truy cập trực tiếp vào internet có thể bị chặn; thay vào đó, hệ thống sẽ sử dụng một proxy HTTP hoặc HTTPS. Bạn có thể cấu hình để một cụm OpenShift Container Platform mới sử dụng proxy bằng cách thiết lập các thông số proxy trong tệp `install-config.yaml`.

**Lưu ý:** Đối với các hệ thống cài đặt trên phần cứng vật lý (bare-metal), nếu bạn không gán địa chỉ IP cho các nút (node) từ dải địa chỉ được quy định trong trường `networking.machineNetwork[].cidr` tại tệp `install-config.yaml`, bạn bắt buộc phải thêm các địa chỉ đó vào trường `proxy.noProxy`.

**Điều kiện tiên quyết**

* Bạn đã có sẵn tệp `install-config.yaml`. 
* Bạn đã xem xét các trang web mà cụm của bạn cần truy cập và xác định xem có trang nào cần bỏ qua proxy hay không. Theo mặc định, proxy sẽ xử lý tất cả lưu lượng truy cập đi ra (egress traffic) từ cụm, bao gồm cả các yêu cầu gọi đến API của nhà cung cấp dịch vụ đám mây (cloud provider). Bạn cần thêm các trang web đó vào trường `spec.noProxy` của đối tượng Proxy (Proxy object) nếu muốn bỏ qua proxy cho các trang này. 

> **Lưu ý:** Trường `status.noProxy` của đối tượng Proxy sẽ tự động bao gồm các giá trị từ các trường `networking.machineNetwork[].cidr`, `networking.clusterNetwork[].cidr` và `networking.serviceNetwork[]` trong cấu hình cài đặt của bạn. 

* Đối với các hệ thống cài đặt trên Amazon Web Services (AWS), Google Cloud, Microsoft Azure và Red Hat OpenStack Platform (RHOSP), trường `status.noProxy` của đối tượng Proxy cũng sẽ bao gồm cả điểm cuối (endpoint) chứa siêu dữ liệu của thực thể (instance metadata endpoint: 169.254.169.254).

**Quy trình thực hiện**

1. Chỉnh sửa tệp `install-config.yaml` và thêm các thiết lập proxy. Ví dụ:

```yaml
apiVersion: v1
baseDomain: my.domain.com
proxy:
  httpProxy: http://<username>:<pswd>@<ip>:<port>
  httpsProxy: https://<username>:<pswd>@<ip>:<port>
  noProxy: example.com
additionalTrustBundle: |
    -----BEGIN CERTIFICATE-----
    <MY_TRUSTED_CA_CERT>
    -----END CERTIFICATE-----
additionalTrustBundlePolicy: <policy_to_add_additionalTrustBundle>
# ...
```

Trong đó:

  * `proxy.httpProxy`: Chỉ định URL proxy được sử dụng để thiết lập các kết nối HTTP bên ngoài cụm (cluster). Giao thức (scheme) của URL phải là `http`.
  * `proxy.httpsProxy`: Chỉ định URL proxy được sử dụng để thiết lập các kết nối HTTPS bên ngoài cụm.
  * `proxy.noProxy`: Chỉ định danh sách các tên miền đích, địa chỉ IP hoặc các dải mạng CIDR (được phân tách bằng dấu phẩy) không đi qua proxy. Thêm dấu `.` vào trước tên miền để chỉ khớp với các tên miền phụ (subdomain). Ví dụ: `.y.com` sẽ khớp với `x.y.com`, nhưng không khớp với `y.com`. Sử dụng `*` để bỏ qua proxy cho tất cả các đích đến.
  * additionalTrustBundle: Nếu bạn chỉ định giá trị này, chương trình cài đặt sẽ tạo một config map có tên là `user-ca-bundle` trong namespace `openshift-config` để lưu trữ các chứng chỉ CA bổ sung. Nếu bạn chỉ định `additionalTrustBundle`` `cùng với ít nhất một thiết lập proxy, đối tượng `Proxy` sẽ tham chiếu đến config map `user-ca-bundle` trong trường `trustedCA`. Sau đó, Cluster Network Operator sẽ tạo một config map có tên `trusted-ca-bundle`, trong đó hợp nhất nội dung được chỉ định cho tham số `trustedCA` với bộ chứng chỉ tin cậy (trust bundle) của RHCOS. Bạn bắt buộc phải thiết lập trường `additionalTrustBundle`, trừ khi chứng chỉ định danh của proxy được ký bởi một cơ quan cấp chứng chỉ (CA) đã nằm trong bộ chứng chỉ tin cậy của RHCOS.
  * `additionalTrustBundlePolicy`: Chỉ định chính sách xác định cách cấu hình đối tượng Proxy để tham chiếu đến config map `user-ca-bundle` trong trường `trustedCA`. Các giá trị được phép là `Proxyonly` và `Always`. Sử dụng `Proxyonly` nếu muốn tham chiếu đến config map `user-ca-bundle` chỉ khi bạn cấu hình proxy `http/https`. Sử dụng Always nếu muốn luôn luôn tham chiếu đến config map user-ca-bundle. Giá trị mặc định là `Proxyonly`. Đây là tham số tùy chọn.

> **Lưu ý:** Chương trình cài đặt không hỗ trợ trường `readinessEndpoints` của proxy.

> **Lưu ý:** Nếu chương trình cài đặt bị hết thời gian chờ (timeout), hãy khởi động lại rồi hoàn tất quá trình triển khai bằng cách sử dụng lệnh `wait-for` của chương trình cài đặt. Ví dụ: `./openshift-install wait-for install-complete --log-level debug`

2. Hãy lưu tệp này lại và tham chiếu đến nó khi cài đặt OpenShift Container Platform.
Chương trình cài đặt sẽ tạo một proxy trên toàn cụm (cluster-wide proxy) có tên là `cluster`, sử dụng các thiết lập proxy từ tệp `install-config.yaml`. Nếu bạn không cung cấp các thiết lập proxy, chương trình cài đặt vẫn sẽ tạo một đối tượng Proxy cho cụm, nhưng với phần đặc tả (spec) để trống (giá trị `nil`).

> **Lưu ý:** Chỉ đối tượng Proxy có tên là `cluster` mới được hỗ trợ; bạn không thể tạo thêm các proxy khác.

##### 2.3.9.3. Cấu hình cụm ba nút (three-node cluster)
Để tạo các cụm có quy mô nhỏ hơn và tiết kiệm tài nguyên phục vụ mục đích thử nghiệm hoặc triển khai thực tế, bạn có thể thiết lập một cụm trên hạ tầng vật lý (bare-metal) mà không cần các máy tính toán (compute machine). Cấu hình tùy chọn này chỉ sử dụng ba máy thuộc mặt phẳng điều khiển (control plane), giúp tối ưu hóa tài nguyên hạ tầng cho các quản trị viên và nhà phát triển.

Trong các môi trường OpenShift Container Platform gồm ba nút, ba máy thuộc mặt phẳng điều khiển này cũng có thể đảm nhận vai trò lập lịch (schedulable); nghĩa là các khối lượng công việc (workload) ứng dụng của bạn sẽ chạy trực tiếp trên các máy này.

**Điều kiện tiên quyết**

* Bạn đã có sẵn tệp `install-config.yaml`.

**Quy trình**

* Đảm bảo rằng số lượng bản sao máy tính toán (compute replica) được đặt thành 0 trong tệp `install-config.yaml` của bạn, như được hiển thị trong đoạn cấu hình `compute` dưới đây:

```yaml
compute:
- name: worker
  platform: {}
  replicas: 0
# ...
```

> **Lưu ý:** Khi cài đặt OpenShift Container Platform trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure), bạn phải đặt giá trị của tham số `replicas` cho các máy tính toán (compute machines) là 0, bất kể số lượng máy tính toán mà bạn triển khai là bao nhiêu. Đối với các quy trình cài đặt do trình cài đặt tự động thiết lập (installer-provisioned installations), tham số này sẽ kiểm soát số lượng máy tính toán mà cụm (cluster) tạo ra và quản lý thay cho bạn. Quy định này không áp dụng cho các quy trình cài đặt do người dùng tự cung cấp cơ sở hạ tầng, nơi bạn phải tự triển khai các máy tính toán theo cách thủ công.

Đối với các hệ thống cụm (cluster) gồm ba nút, hãy thực hiện các bước sau:

  * Nếu bạn đang triển khai cụm ba nút mà không có nút tính toán (compute node) nào, các pod của Ingress Controller sẽ chạy trên các nút mặt phẳng điều khiển (control plane node). Trong các hệ thống cụm ba nút, bạn phải cấu hình bộ cân bằng tải (load balancer) cho ứng dụng để định tuyến lưu lượng truy cập HTTP và HTTPS đến các nút mặt phẳng điều khiển. Để biết thêm thông tin, hãy xem phần các yêu cầu về cân bằng tải đối với cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure). 
  * Khi tạo các tệp manifest Kubernetes trong quy trình tiếp theo, hãy đảm bảo rằng tham số `mastersSchedulable` trong tệp `<installation_directory>/manifests/cluster-scheduler-02-config.yml` được đặt thành `true`. Điều này cho phép các khối lượng công việc (workload) ứng dụng của bạn chạy trên các nút mặt phẳng điều khiển. 
  * Không triển khai bất kỳ nút tính toán nào khi tạo các máy ảo chạy Red Hat Enterprise Linux CoreOS (RHCOS).

#### 2.3.10. Tạo các tệp manifest Kubernetes và tệp cấu hình Ignition
Vì bạn tự thiết lập cơ sở hạ tầng theo cách thủ công, bạn cần tạo các tệp manifest Kubernetes và tệp cấu hình Ignition mà cụm (cluster) yêu cầu.

Chương trình cài đặt sẽ chuyển đổi cấu hình cài đặt thành các tệp manifest Kubernetes, sau đó đóng gói chúng vào các tệp cấu hình Ignition. Bạn sử dụng các tệp Ignition này để cấu hình các máy trong cụm.

> **Quan trọng:** Các tệp cấu hình Ignition do chương trình cài đặt OpenShift Container Platform tạo ra chứa các chứng chỉ có thời hạn 24 giờ; sau đó, hệ thống sẽ tự động gia hạn các chứng chỉ này. Nếu bạn tắt cụm trước khi hệ thống kịp gia hạn chứng chỉ và khởi động lại cụm sau khi đã quá 24 giờ, cụm sẽ tự động khôi phục các chứng chỉ đã hết hạn. Một ngoại lệ là bạn phải phê duyệt thủ công các yêu cầu ký chứng chỉ (CSR) của `node-bootstrapper` đang ở trạng thái chờ để khôi phục các chứng chỉ `kubelet`. Để biết thêm thông tin, hãy xem tài liệu về cách khôi phục từ các chứng chỉ control plane đã hết hạn. 
Hãy sử dụng các tệp cấu hình Ignition trong vòng 12 giờ kể từ khi tạo, vì quá trình xoay vòng chứng chỉ 24 giờ thường diễn ra từ 16 đến 22 giờ sau khi cài đặt cụm. Việc sử dụng các tệp Ignition trong vòng 12 giờ giúp tránh lỗi cài đặt trong trường hợp quá trình cập nhật chứng chỉ diễn ra đúng lúc đang cài đặt.

**Điều kiện tiên quyết**

*  Bạn đã có chương trình cài đặt OpenShift Container Platform. Đối với trường hợp cài đặt trong mạng bị hạn chế (restricted network), các tệp này sẽ nằm trên máy chủ mirror (mirror host) của bạn. 
Bạn đã tạo tệp cấu hình cài đặt `install-config.yaml`.

**Quy trình**

1. Di chuyển đến thư mục chứa chương trình cài đặt OpenShift Container Platform và tạo các tệp manifest Kubernetes cho cụm:

```shell
$ ./openshift-install create manifests --dir <installation_directory>
```

Trong đó: `<installation_directory>` chỉ định thư mục cài đặt chứa tệp install-config.yaml mà bạn đã tạo.

> **Cảnh báo:** Nếu bạn đang cài đặt một cụm gồm ba nút, hãy bỏ qua bước sau để cho phép các nút trong mặt phẳng điều khiển (control plane) có thể nhận các tác vụ được lập lịch.

> **Quan trọng:** Khi bạn cấu hình các nút mặt phẳng điều khiển từ trạng thái mặc định không thể lập lịch sang trạng thái có thể lập lịch, bạn cần thêm các gói đăng ký vì các nút mặt phẳng điều khiển khi đó sẽ trở thành các nút tính toán.

2. Hãy xác minh rằng tham số `mastersSchedulable` trong tệp manifest Kubernetes `<installation_directory>/manifests/cluster-scheduler-02-config.yml` được đặt thành `false`. Thiết lập này ngăn không cho các pod được lập lịch trên các máy thuộc mặt phẳng điều khiển (control plane):

  * Mở tệp `<installation_directory>/manifests/cluster-scheduler-02-config.yml`. 
  * Tìm tham số `mastersSchedulable` và xác minh rằng nó được đặt thành `false`. 
  * Lưu tệp và thoát.

3. Để tạo các tệp cấu hình Ignition, hãy chạy lệnh sau từ thư mục chứa chương trình cài đặt:

```shell
$ ./openshift-install create ignition-configs --dir <installation_directory>
```

Trong đó: `<installation_directory>` chỉ định cùng một thư mục cài đặt.

Chương trình cài đặt tạo các tệp cấu hình Ignition cho các nút bootstrap, control plane và compute trong thư mục cài đặt. Chương trình này cũng tạo các tệp `kubeadmin-password` và `kubeconfig` trong thư mục `./<installation_directory>/auth`:

```
.
├── auth
│   ├── kubeadmin-password
│   └── kubeconfig
├── bootstrap.ign
├── master.ign
├── metadata.json
└── worker.ign
```

#### 2.3.11. Cấu hình dịch vụ thời gian chrony
Bạn phải thiết lập máy chủ thời gian cùng các cài đặt liên quan cho dịch vụ thời gian chrony (chronyd) bằng cách sửa đổi nội dung tệp chrony.conf, sau đó truyền nội dung này đến các nút (node) của bạn dưới dạng cấu hình máy (machine config).

**Quy trình**

1. Tạo một cấu hình Butane bao gồm nội dung của tệp `chrony.conf`. Ví dụ: để định cấu hình `chrony` trên các nút worker (worker node), hãy tạo tệp `99-worker-chrony.bu`. 

> **Lưu ý:** [Phiên bản Butane](https://coreos.github.io/butane/specs/) mà bạn chỉ định trong tệp cấu hình phải khớp với phiên bản OpenShift Container Platform và luôn kết thúc bằng số 0. Ví dụ: `4.22.0`. Xem phần "Tạo cấu hình máy (machine config) bằng Butane" để biết thêm thông tin về Butane.

```yaml
variant: openshift
version: 4.22.0
metadata:
  name: 99-worker-chrony
  labels:
    machineconfiguration.openshift.io/role: worker
storage:
  files:
  - path: /etc/chrony.conf
    mode: 0644
    overwrite: true
    contents:
      inline: |
        pool 0.rhel.pool.ntp.org iburst
        driftfile /var/lib/chrony/drift
        makestep 1.0 3
        rtcsync
        logdir /var/log/chrony
```

  * `name: 99-worker-chrony` - Chỉ định tên cho tệp cấu hình máy (machine config file). Đối với các nút trong mặt phẳng điều khiển (control plane nodes), hãy thay thế `worker` bằng `master`. 
  * `machineconfiguration.openshift.io/role: worker` - Đối với các nút trong mặt phẳng điều khiển, hãy thay thế `worker` bằng `master`. 
  * `mode: 0644` - Chỉ định giá trị bát phân (octal value) cho trường `mode` trong tệp cấu hình máy. Sau khi tạo tệp và áp dụng các thay đổi, giá trị này sẽ được chuyển đổi sang dạng thập phân. Bạn có thể kiểm tra tệp YAML bằng lệnh: `oc get mc <mc-name> -o yaml`. 
  * `pool 0.rhel.pool.ntp.org iburst` - Chỉ định bất kỳ nguồn thời gian hợp lệ và có thể truy cập được nào, chẳng hạn như nguồn do máy chủ DHCP của bạn cung cấp.

> **Lưu ý:** Đối với giao tiếp giữa các máy với nhau, giao thức Network Time Protocol (NTP) sử dụng cổng UDP 123. Nếu bạn định cấu hình một máy chủ thời gian NTP bên ngoài, bạn phải mở cổng UDP 123.

2. Sử dụng Butane để tạo tệp đối tượng `MachineConfig` có tên `99-worker-chrony.yaml`, chứa cấu hình sẽ được chuyển đến các nút (node):

```shell
$ butane 99-worker-chrony.bu -o 99-worker-chrony.yaml
```

3. Áp dụng các cấu hình theo một trong hai cách sau:
  * Nếu cụm (cluster) chưa hoạt động, sau khi tạo các tệp manifest, hãy thêm tệp đối tượng `MachineConfig` vào thư mục `<installation_directory>/openshift`, rồi tiếp tục thực hiện các bước tạo cụm. 
  * Nếu cụm đã hoạt động, hãy áp dụng tệp đó:

  ```shell
  $ oc apply -f ./99-worker-chrony.yaml
  ```

#### 2.3.12. Cài đặt RHCOS và khởi động quy trình bootstrap của OpenShift Container Platform
Để cài đặt OpenShift Container Platform trên cơ sở hạ tầng phần cứng vật lý (bare-metal) do bạn cung cấp, hãy cài đặt Red Hat Enterprise Linux CoreOS (RHCOS) bằng cách sử dụng các tệp cấu hình Ignition đã được tạo. Việc cung cấp các tệp này đảm bảo quy trình bootstrap sẽ tự động bắt đầu sau khi các máy khởi động lại.

Nếu bạn đã định cấu hình cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các máy chạy RHCOS khởi động lại.

Để cài đặt RHCOS trên các máy, hãy thực hiện theo các bước sử dụng tệp ảnh ISO hoặc khởi động qua mạng (PXE booting).

> **Lưu ý:** Các bước triển khai nút tính toán (compute node) có trong tài liệu cài đặt này dành riêng cho RHCOS. Nếu bạn chọn triển khai các nút tính toán dựa trên RHEL, bạn sẽ chịu trách nhiệm quản lý và bảo trì toàn bộ vòng đời của hệ điều hành, bao gồm thực hiện cập nhật hệ thống, áp dụng các bản vá và hoàn thành tất cả các tác vụ cần thiết khác. Chỉ các máy tính toán chạy RHEL 8 mới được hỗ trợ.

Bạn có thể định cấu hình RHCOS trong quá trình cài đặt qua ISO hoặc PXE bằng các phương pháp sau:

* Các tham số nhân (Kernel arguments): Bạn có thể sử dụng các tham số nhân để cung cấp thông tin cụ thể cho quá trình cài đặt. Ví dụ: bạn có thể chỉ định vị trí các tệp cài đặt RHCOS đã tải lên máy chủ HTTP của mình, cũng như vị trí tệp cấu hình Ignition tương ứng với loại nút mà bạn đang cài đặt. Đối với cài đặt qua PXE, bạn có thể sử dụng tham số APPEND để truyền các tham số này vào nhân (kernel) của trình cài đặt trực tiếp (live installer). Đối với cài đặt qua ISO, bạn có thể tạm dừng quá trình khởi động của trình cài đặt trực tiếp để thêm các tham số nhân. Trong cả hai trường hợp, bạn đều có thể sử dụng các tham số đặc biệt dạng `coreos.inst.*` để điều hướng trình cài đặt trực tiếp, cũng như các tham số khởi động tiêu chuẩn để bật hoặc tắt các dịch vụ nhân thông thường. 
* Các tệp cấu hình Ignition: Các tệp cấu hình Ignition (`*.ign`) của OpenShift Container Platform được thiết kế riêng cho từng loại nút mà bạn cài đặt. Bạn cần cung cấp vị trí tệp cấu hình Ignition tương ứng (cho nút bootstrap, nút điều khiển - control plane, hoặc nút tính toán) trong quá trình cài đặt RHCOS để tệp này có hiệu lực ngay từ lần khởi động đầu tiên. Trong một số trường hợp đặc biệt, bạn có thể tạo một tệp cấu hình Ignition riêng biệt với phạm vi giới hạn để truyền vào hệ thống đang chạy. Cấu hình Ignition đó có thể thực hiện một số tác vụ nhất định, chẳng hạn như thông báo trạng thái thành công cho hệ thống cung cấp tài nguyên (provisioning system) sau khi hoàn tất quá trình cài đặt. Cấu hình Ignition đặc biệt này được `coreos-installer` sử dụng để áp dụng vào lần khởi động đầu tiên của hệ thống đã cài đặt. Tuyệt đối không cung cấp trực tiếp các cấu hình Ignition tiêu chuẩn dành cho control plane (mặt phẳng điều khiển) hoặc compute node (nút tính toán) vào tệp ISO chạy trực tiếp (live ISO). 
* `coreos-installer`: Bạn có thể khởi động tệp ISO chạy trực tiếp (live ISO) vào giao diện dòng lệnh (shell prompt), cho phép chuẩn bị hệ thống chính thức theo nhiều cách khác nhau trước lần khởi động đầu tiên. Cụ thể, bạn có thể chạy lệnh `coreos-installer` để xác định các thành phần (artifacts) cần đưa vào, thao tác với các phân vùng ổ đĩa và thiết lập mạng. Trong một số trường hợp, bạn có thể cấu hình các tính năng trên hệ thống chạy trực tiếp rồi sao chép chúng sang hệ thống đã cài đặt. 

> **Lưu ý:** Kể từ phiên bản 0.17.0-3, `coreos-installer` yêu cầu hệ điều hành RHEL 9 trở lên để chạy chương trình. Bạn vẫn có thể sử dụng các phiên bản `coreos-installer` cũ hơn để tùy chỉnh các thành phần RHCOS của các bản phát hành OpenShift Container Platform mới hơn, cũng như cài đặt các image dành cho phần cứng vật lý (metal images) vào ổ đĩa. Hãy tham khảo mục "coreos-installer image mirror" để tải xuống các phiên bản nhị phân cũ hơn của `coreos-installer`.

Việc chọn cài đặt qua ISO hay PXE tùy thuộc vào tình huống cụ thể của bạn. Cài đặt qua PXE đòi hỏi phải có sẵn dịch vụ DHCP và cần chuẩn bị kỹ lưỡng hơn, nhưng lại giúp quy trình cài đặt trở nên tự động hóa hơn. Ngược lại, cài đặt qua ISO là quy trình thủ công hơn và có thể gây bất tiện nếu bạn cần thiết lập số lượng máy lớn.

##### 2.3.12.1. Cài đặt RHCOS bằng tệp ảnh ISO
Để cung cấp tài nguyên cho các máy vật lý hoặc máy ảo, hãy cài đặt RHCOS bằng tệp ảnh ISO có khả năng khởi động (bootable ISO image).

**Các điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm (cluster) của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn có một máy chủ HTTP mà từ đó cả máy tính của bạn và các máy bạn tạo ra đều có thể truy cập được. 
* Bạn đã xem qua phần cấu hình cài đặt RHCOS nâng cao để biết các phương thức thiết lập những tính năng khác nhau, chẳng hạn như mạng và phân vùng đĩa.

**Quy trình**

1. Lấy mã băm (digest) SHA512 cho từng tệp cấu hình Ignition của bạn. Ví dụ: trên một hệ thống chạy Linux, bạn có thể sử dụng lệnh sau để lấy mã băm SHA512 cho tệp cấu hình Ignition `bootstrap.ign`:

```shell
$ sha512sum <installation_directory>/bootstrap.ign
```

Các giá trị tóm tắt (digest) được cung cấp cho `coreos-installer` ở một bước sau đó để xác thực tính nguyên bản của các tệp cấu hình Ignition trên các nút của cụm (cluster).

2. Hãy tải các tệp cấu hình Ignition cho nút bootstrap, control plane và compute node — vốn đã được chương trình cài đặt tạo ra — lên máy chủ HTTP của bạn. Ghi lại các URL của những tệp này.

> **Quan trọng:** Bạn có thể thêm hoặc thay đổi các thiết lập cấu hình trong các tệp Ignition trước khi lưu chúng lên máy chủ HTTP. Nếu bạn dự định bổ sung thêm các máy tính toán (compute machine) vào cụm (cluster) của mình sau khi hoàn tất quá trình cài đặt, đừng xóa các tệp này.

3. Từ máy chủ thực hiện cài đặt (installation host), hãy xác thực rằng các tệp cấu hình Ignition đã sẵn sàng tại các URL tương ứng. Ví dụ dưới đây cho thấy cách truy xuất tệp cấu hình Ignition dành cho nút bootstrap:

```shell
$ curl -k http://<HTTP_server>/bootstrap.ign
```
  * `<HTTP_server>`: Hãy thay thế `bootstrap.ign` bằng `master.ign` hoặc `worker.ign` trong lệnh thực thi để xác nhận rằng các tệp cấu hình Ignition dành cho control plane (mặt phẳng điều khiển) và các nút tính toán (compute node) cũng đã sẵn sàng.

  Ví dụ đầu ra

  ```
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0{"ignition":{"version":"3.2.0"},"passwd":{"users":[{"name":"core","sshAuthorizedKeys":["ssh-rsa...
  ```

4. Mặc dù bạn có thể lấy các hình ảnh (image) RHCOS cần thiết cho phương thức cài đặt hệ điều hành mà mình chọn từ trang lưu trữ hình ảnh RHCOS, nhưng cách thức được khuyến nghị để có được phiên bản hình ảnh RHCOS chính xác là thông qua kết quả đầu ra của lệnh `openshift-install`:

```shell
$ openshift-install coreos print-stream-json | grep '\.iso[^.]'
```

Ví dụ đầu ra

```
"location": "<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live.aarch64.iso",
"location": "<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live.ppc64le.iso",
"location": "<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live.s390x.iso",
"location": "<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live.x86_64.iso",
```

> **Quan trọng:** Các hình ảnh (image) RHCOS có thể không thay đổi theo mỗi phiên bản phát hành của OpenShift Container Platform. Bạn phải tải xuống các hình ảnh có phiên bản cao nhất nhưng không vượt quá phiên bản OpenShift Container Platform mà bạn đang cài đặt. Nếu có sẵn các hình ảnh khớp với phiên bản OpenShift Container Platform của bạn, hãy sử dụng các phiên bản đó. Chỉ sử dụng các tệp hình ảnh định dạng ISO cho quy trình này. Các tệp hình ảnh RHCOS định dạng qcow2 không được hỗ trợ cho kiểu cài đặt này.

Tên tệp ISO có dạng như ví dụ sau: `rhcos-<phiên bản>-live.<architecture>.iso`

5. Sử dụng tệp ISO để bắt đầu quá trình cài đặt RHCOS. Bạn có thể chọn một trong các phương thức sau:

  * Ghi tệp ảnh ISO ra đĩa và khởi động trực tiếp từ đĩa đó. 
  * Sử dụng tính năng chuyển hướng ISO (ISO redirection) thông qua giao diện quản trị từ xa (lights-out management - LOM).

6. Khởi động tệp ảnh ISO của RHCOS mà không cần chỉ định thêm tùy chọn nào hoặc can thiệp vào trình tự khởi động trực tiếp (live boot). Hãy đợi cho đến khi trình cài đặt khởi động vào giao diện dòng lệnh (shell prompt) trong môi trường RHCOS live.

> **Lưu ý:** Bạn có thể can thiệp vào quá trình khởi động cài đặt RHCOS để thêm các tham số nhân (kernel arguments). Tuy nhiên, đối với quy trình sử dụng tệp ISO này, bạn nên dùng lệnh `coreos-installer` theo các bước hướng dẫn dưới đây thay vì thêm tham số nhân.

7. Thực thi lệnh `coreos-installer` cùng với các tùy chọn phù hợp với yêu cầu cài đặt của bạn. Tối thiểu, bạn cần chỉ định URL dẫn đến tệp cấu hình Ignition (Ignition config file) tương ứng với loại nút (node type) và thiết bị đích nơi bạn muốn thực hiện cài đặt:

```shell
$ sudo coreos-installer install --ignition-url=http://<HTTP_server>/<node_type>.ign <device> \
  --ignition-hash=sha512-<digest> --offline
```

  * <device>: Bạn phải chạy lệnh `coreos-installer` với quyền `sudo`, vì người dùng `core` không có đủ đặc quyền quản trị (root) cần thiết để thực hiện quá trình cài đặt.
  * <digest>: Tùy chọn `--ignition-hash` là bắt buộc khi tệp cấu hình Ignition được lấy thông qua URL HTTP; tùy chọn này giúp xác thực tính nguyên bản của tệp cấu hình Ignition trên nút (node) của cụm (cluster). `<digest>` chính là mã băm SHA512 của tệp cấu hình Ignition, được lấy từ bước trước đó.

> **Lưu ý:** Nếu bạn muốn cung cấp các tệp cấu hình Ignition thông qua máy chủ HTTPS sử dụng giao thức TLS, bạn có thể thêm cơ quan cấp chứng chỉ (CA) nội bộ vào kho lưu trữ chứng chỉ tin cậy của hệ thống trước khi chạy `coreos-installer`.

Ví dụ dưới đây thực hiện khởi tạo cài đặt cho một nút khởi động (bootstrap node) trên thiết bị `/dev/sda`. Tệp cấu hình Ignition cho nút khởi động này được lấy từ một máy chủ web HTTP có địa chỉ IP là 192.168.1.2:

```shell
$ sudo coreos-installer install --ignition-url=http://192.168.1.2:80/installation_directory/bootstrap.ign /dev/sda \
  --ignition-hash=sha512-a5a2d43879223273c9b60af66b44202a1d1248fc01cf156c46d4a79f552b6bad47bc8cc78ddf0116e80c59d2ea9e32ba53bc807afbca581aa059311def2c3e3b \
  --offline
```

8. Theo dõi tiến trình cài đặt RHCOS trên bảng điều khiển (console) của máy.

> **Quan trọng:** Hãy đảm bảo rằng quá trình cài đặt đã hoàn tất thành công trên từng nút (node) trước khi bắt đầu cài đặt OpenShift Container Platform. Việc quan sát quá trình cài đặt cũng giúp xác định nguyên nhân nếu xảy ra sự cố trong lúc cài đặt RHCOS.

9. Sau khi RHCOS được cài đặt, bạn phải khởi động lại hệ thống. Trong quá trình khởi động lại, hệ thống sẽ áp dụng tệp cấu hình Ignition mà bạn đã chỉ định.
10. Kiểm tra đầu ra trên bảng điều khiển để xác nhận rằng Ignition đã chạy.

Ví dụ về lệnh

```
Ignition: ran on 2022/03/14 14:48:33 UTC (this boot)
Ignition: user-provided config was applied
```

11. Tiếp tục tạo các máy khác cho cụm (cluster) của bạn.

> **Quan trọng:** Tại thời điểm này, bạn phải tạo các máy bootstrap và các máy control plane (mặt phẳng điều khiển). Nếu các máy control plane không được thiết lập để có thể lập lịch (schedulable), bạn cũng cần tạo thêm ít nhất hai máy tính toán (compute machine) trước khi cài đặt OpenShift Container Platform.

Nếu các cơ sở hạ tầng cần thiết về mạng, DNS và bộ cân bằng tải (load balancer) đã sẵn sàng, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các nút (node) chạy hệ điều hành RHCOS khởi động lại.

> **Lưu ý:** Các nút RHCOS không có mật khẩu mặc định cho người dùng `core`. Bạn có thể truy cập các nút này bằng cách chạy lệnh `ssh core@<node>.<cluster_name>.<base_domain>` với tư cách là người dùng có quyền truy cập vào khóa cá nhân SSH (SSH private key) tương ứng với khóa công khai (public key) mà bạn đã chỉ định trong tệp `install_config.yaml`. Các nút trong cụm OpenShift Container Platform 4 chạy RHCOS là các thành phần không thể thay đổi (immutable) và dựa vào các Operator để thực hiện các thay đổi đối với cụm. Việc truy cập vào các nút của cụm thông qua SSH không được khuyến khích. Tuy nhiên, khi cần điều tra các sự cố cài đặt — chẳng hạn như khi API của OpenShift Container Platform không khả dụng hoặc `kubelet` trên một nút mục tiêu không hoạt động bình thường — bạn có thể cần truy cập qua SSH để gỡ lỗi hoặc khôi phục hệ thống trong trường hợp xảy ra sự cố nghiêm trọng.

##### 2.3.12.2. Cài đặt RHCOS bằng phương thức khởi động PXE hoặc iPXE
Bạn có thể sử dụng phương thức khởi động PXE hoặc iPXE để cài đặt RHCOS trên các máy chủ.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm (cluster) của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn đã thiết lập cơ sở hạ tầng PXE hoặc iPXE phù hợp. 
* Bạn có một máy chủ HTTP có thể truy cập được từ máy tính của bạn cũng như từ các máy chủ mà bạn tạo ra. 
* Bạn đã xem qua phần cấu hình cài đặt RHCOS nâng cao để biết các cách thiết lập những tính năng khác nhau, chẳng hạn như mạng và phân vùng đĩa.

**Quy trình**

1. Tải các tệp cấu hình Ignition dành cho nút bootstrap, nút điều khiển (control plane) và nút tính toán (compute node) — vốn được tạo bởi chương trình cài đặt — lên máy chủ HTTP của bạn. Hãy ghi lại các URL của những tệp này. 

> **Quan trọng:** Bạn có thể thêm hoặc thay đổi các thiết lập cấu hình trong các tệp Ignition trước khi lưu chúng lên máy chủ HTTP. Nếu bạn dự định thêm các máy tính toán (compute machine) vào cụm sau khi hoàn tất cài đặt, đừng xóa các tệp này. 

2. Từ máy chủ cài đặt (installation host), hãy xác thực rằng các tệp cấu hình Ignition đã sẵn sàng tại các URL đó. Ví dụ dưới đây cho thấy cách truy xuất tệp cấu hình Ignition dành cho nút bootstrap:

```shell
$ curl -k http://<HTTP_server>/bootstrap.ign
```

  * `<HTTP_server>`: Hãy thay thế `bootstrap.ign` bằng `master.ign` hoặc `worker.ign` trong lệnh thực thi để xác nhận rằng các tệp cấu hình Ignition dành cho control plane (mặt phẳng điều khiển) và các nút tính toán (compute node) cũng đã sẵn sàng.

  Ví dụ đầu ra

  ```
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0{"ignition":{"version":"3.2.0"},"passwd":{"users":[{"name":"core","sshAuthorizedKeys":["ssh-rsa...
  ```

3. Mặc dù bạn có thể lấy các tệp RHCOS `kernel`, `initramfs` và `rootfs` — vốn cần thiết cho phương thức cài đặt hệ điều hành mà bạn chọn — từ trang [lưu trữ (mirror) hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/x86_64/dependencies/rhcos/), nhưng cách thức được khuyến nghị để có được phiên bản tệp RHCOS chính xác là thông qua kết quả đầu ra từ lệnh `openshift-install`:

```shell
$ openshift-install coreos print-stream-json | \
  grep -Eo '"https.*(kernel-|initramfs.|rootfs.)\w+(\.img)?"'
```

Ví dụ đầu ra

```
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-kernel-aarch64"
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-initramfs.aarch64.img"
"<url>/art/storage/releases/rhcos-4.22-aarch64/<release>/aarch64/rhcos-<release>-live-rootfs.aarch64.img"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/49.84.202110081256-0/ppc64le/rhcos-<release>-live-kernel-ppc64le"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live-initramfs.ppc64le.img"
"<url>/art/storage/releases/rhcos-4.22-ppc64le/<release>/ppc64le/rhcos-<release>-live-rootfs.ppc64le.img"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-kernel-s390x"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-initramfs.s390x.img"
"<url>/art/storage/releases/rhcos-4.22-s390x/<release>/s390x/rhcos-<release>-live-rootfs.s390x.img"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-kernel-x86_64"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-initramfs.x86_64.img"
"<url>/art/storage/releases/rhcos-4.22/<release>/x86_64/rhcos-<release>-live-rootfs.x86_64.img"
```

> **Quan trọng:** Các thành phần (artifact) của RHCOS có thể không thay đổi qua mỗi phiên bản OpenShift Container Platform. Bạn phải tải xuống các image có phiên bản cao nhất nhưng không vượt quá phiên bản OpenShift Container Platform mà bạn đang cài đặt. Chỉ sử dụng các thành phần kernel, initramfs và rootfs phù hợp được mô tả dưới đây cho quy trình này. Các image RHCOS định dạng QCOW2 không được hỗ trợ cho kiểu cài đặt này.

Tên tệp tin bao gồm số phiên bản OpenShift Container Platform. Ví dụ như sau:

  * `kernel`: `rhcos-<version>-live-kernel-<architecture>`
  * `initramfs`: `rhcos-<version>-live-initramfs.<architecture>.img`
  * `rootfs`: `rhcos-<version>-live-rootfs.<architecture>.img`

Tải các tệp tin `rootfs`, `kernel` và `initramfs` lên máy chủ HTTP của bạn.

> Quan trọng: Nếu bạn dự định thêm các máy tính toán (compute machine) vào cụm (cluster) của mình sau khi hoàn tất cài đặt, đừng xóa các tệp tin này.

5. Cấu hình cơ sở hạ tầng khởi động qua mạng (network boot) sao cho các máy sẽ khởi động từ ổ đĩa cục bộ sau khi RHCOS đã được cài đặt trên đó.
6. Cấu hình cài đặt qua PXE hoặc iPXE cho các image RHCOS và bắt đầu quá trình cài đặt.
7. Điều chỉnh một trong các mục menu mẫu dưới đây cho phù hợp với môi trường của bạn, đồng thời xác minh rằng các tệp tin image và Ignition đều có thể truy cập được:
  * Đối với PXE (x86_64):

  ```
  DEFAULT pxeboot
  TIMEOUT 20
  PROMPT 0
  LABEL pxeboot
      KERNEL http://<HTTP_server>/rhcos-<version>-live-kernel-<architecture>
      APPEND initrd=http://<HTTP_server>/rhcos-<version>-live-initramfs.<architecture>.img coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
  ```

  Trong đó:

    * `kernel`: Chỉ định vị trí của tệp kernel trực tiếp (live kernel) mà bạn đã tải lên máy chủ HTTP của mình. URL phải thuộc loại HTTP, TFTP hoặc FTP; các giao thức HTTPS và NFS không được hỗ trợ.
    * `initrd=main`: Nếu sử dụng nhiều card mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên card mạng có tên là `eno1`, hãy đặt `ip=eno1:dhcp`. Hãy chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP của mình. Giá trị của tham số `initrd` là vị trí của tệp `initramfs`, giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp `rootfs`, và giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition khởi động (bootstrap Ignition config). Bạn cũng có thể thêm các đối số kernel khác vào dòng `APPEND` để cấu hình mạng hoặc các tùy chọn khởi động khác. 

  > **Lưu ý:** Cấu hình này không kích hoạt quyền truy cập qua console nối tiếp (serial console) trên các máy có console đồ họa. Để cấu hình một console khác, hãy thêm một hoặc nhiều đối số `console=` vào dòng `APPEND`. Ví dụ: thêm `console=tty0 console=ttyS0` để thiết lập cổng nối tiếp đầu tiên trên PC làm console chính và console đồ họa làm console phụ. Để biết thêm thông tin, hãy xem bài viết "Cách thiết lập terminal nối tiếp và/hoặc console trong Red Hat Enterprise Linux?" và mục "Kích hoạt console nối tiếp cho quá trình cài đặt qua PXE và ISO" trong phần "Cấu hình cài đặt RHCOS nâng cao".

  * Đối với iPXE (`x86_64` + `aarch64`):

  ```
  kernel http://<HTTP_server>/rhcos-<version>-live-kernel-<architecture> initrd=main coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
  initrd --name main http://<HTTP_server>/rhcos-<version>-live-initramfs.<architecture>.img
  boot
  ```

    * kernel: Chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP của mình. Giá trị của tham số `kernel` là vị trí của tệp kernel; đối số `initrd=main` là bắt buộc để khởi động trên các hệ thống UEFI; giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp rootfs; và giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition dùng cho quá trình khởi động (bootstrap). Nếu bạn sử dụng nhiều card mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên một card mạng có tên là `eno1`, hãy đặt `ip=eno1:dhcp`.
    * initrd: Chỉ định vị trí của tệp initramfs mà bạn đã tải lên máy chủ HTTP của mình. 

  > **Lưu ý:** Cấu hình này không kích hoạt quyền truy cập qua console nối tiếp (serial console) trên các máy có console đồ họa. Để cấu hình một console khác, hãy thêm một hoặc nhiều đối số `console=` vào dòng kernel. Ví dụ: thêm `console=tty0 console=ttyS0` để thiết lập cổng nối tiếp đầu tiên trên PC làm console chính và console đồ họa làm console phụ. Để biết thêm thông tin, hãy xem bài viết "Cách thiết lập terminal nối tiếp và/hoặc console trong Red Hat Enterprise Linux?" và mục "Kích hoạt console nối tiếp cho quá trình cài đặt qua PXE và ISO" trong phần "Cấu hình cài đặt RHCOS nâng cao". 

  > **Lưu ý:** Để khởi động kernel CoreOS qua mạng trên kiến trúc `aarch64`, bạn cần sử dụng phiên bản iPXE được xây dựng (build) với tùy chọn `IMAGE_GZIP` đã được kích hoạt. Xem thêm về tùy chọn `IMAGE_GZIP` trong iPXE.

  * Đối với PXE (sử dụng UEFI và Grub làm giai đoạn thứ hai) trên kiến trúc `aarch64`:

  ```
  menuentry 'Install CoreOS' {
      linux rhcos-<version>-live-kernel-<architecture>  coreos.live.rootfs_url=http://<HTTP_server>/rhcos-<version>-live-rootfs.<architecture>.img coreos.inst.install_dev=/dev/sda coreos.inst.ignition_url=http://<HTTP_server>/bootstrap.ign
      initrd rhcos-<version>-live-initramfs.<architecture>.img
  }
  ```

  Trong đó:

    * `coreos.live.rootfs_url`: Chỉ định vị trí các tệp RHCOS mà bạn đã tải lên máy chủ HTTP/TFTP của mình. 
    * `kernel`: Giá trị của tham số kernel là vị trí của tệp kernel trên máy chủ TFTP của bạn. Giá trị của tham số `coreos.live.rootfs_url` là vị trí của tệp `rootfs`, còn giá trị của tham số `coreos.inst.ignition_url` là vị trí của tệp cấu hình Ignition khởi động (bootstrap) trên máy chủ HTTP của bạn. Nếu sử dụng nhiều card mạng (NIC), hãy chỉ định một giao diện duy nhất trong tùy chọn `ip`. Ví dụ: để sử dụng DHCP trên card mạng có tên là eno1, hãy đặt `ip=eno1:dhcp`. 
    * `initrd rhcos`: Chỉ định vị trí của tệp `initramfs` mà bạn đã tải lên máy chủ TFTP của mình.

8. Theo dõi tiến trình cài đặt RHCOS trên bảng điều khiển (console) của máy.

> **Quan trọng:** Hãy đảm bảo rằng quá trình cài đặt đã hoàn tất thành công trên từng nút (node) trước khi bắt đầu cài đặt OpenShift Container Platform. Việc quan sát quá trình cài đặt cũng giúp xác định nguyên nhân nếu xảy ra sự cố trong quá trình cài đặt RHCOS.

9. Sau khi RHCOS được cài đặt, hệ thống sẽ khởi động lại. Trong quá trình khởi động lại, hệ thống sẽ áp dụng tệp cấu hình Ignition mà bạn đã chỉ định.
10. Kiểm tra đầu ra trên bảng điều khiển để xác nhận rằng Ignition đã chạy.

Ví dụ lệnh

```
Ignition: ran on 2022/03/14 14:48:33 UTC (this boot)
Ignition: user-provided config was applied
```

10. Tiếp tục tạo các máy (machine) cho cụm (cluster) của bạn.

> **Quan trọng:** Tại bước này, bạn phải tạo các máy bootstrap và các máy control plane. Nếu các máy control plane không được thiết lập để có thể lập lịch (schedulable), bạn cũng cần tạo thêm ít nhất hai máy tính toán (compute machine) trước khi tiến hành cài đặt cụm.

Nếu các cơ sở hạ tầng cần thiết về mạng, DNS và bộ cân bằng tải (load balancer) đã sẵn sàng, quy trình bootstrap của OpenShift Container Platform sẽ tự động bắt đầu sau khi các nút (node) chạy hệ điều hành RHCOS khởi động lại.

> **Lưu ý:** Các nút chạy RHCOS không có mật khẩu mặc định cho người dùng `core`. Bạn có thể truy cập vào các nút này bằng lệnh `ssh core@<node>.<cluster_name>.<base_domain>`, với điều kiện bạn là người dùng sở hữu khóa cá nhân SSH (SSH private key) tương ứng với khóa công khai (public key) mà bạn đã chỉ định trong tệp `install_config.yaml`. Các nút trong cụm OpenShift Container Platform 4 chạy RHCOS là các thành phần không thể thay đổi (immutable); mọi thay đổi đối với cụm đều được thực hiện thông qua các Operator. Việc truy cập vào các nút của cụm thông qua SSH không được khuyến khích. Tuy nhiên, trong trường hợp cần điều tra các sự cố cài đặt — chẳng hạn như khi API của OpenShift Container Platform không khả dụng hoặc `kubelet` trên một nút mục tiêu không hoạt động bình thường — bạn có thể cần truy cập qua SSH để phục vụ mục đích gỡ lỗi hoặc khôi phục sau sự cố.

###### 2.3.12.3.1. Sử dụng các tùy chọn mạng nâng cao cho quá trình cài đặt qua PXE và ISO
Theo mặc định, hệ thống mạng cho các nút (node) trong OpenShift Container Platform sử dụng DHCP để thu thập tất cả các thiết lập cấu hình cần thiết. Bạn có thể thiết lập địa chỉ IP tĩnh hoặc định cấu hình các thiết lập đặc biệt khác.

Để thiết lập địa chỉ IP tĩnh hoặc định cấu hình các thiết lập đặc biệt (ví dụ: gộp cổng mạng - bonding), bạn có thể thực hiện một trong các cách sau:

* Truyền các tham số nhân (kernel parameter) đặc biệt khi khởi động trình cài đặt trực tiếp (live installer). 
* Sử dụng cấu hình máy (machine config) để sao chép các tệp tin mạng vào hệ thống được cài đặt. 
* Định cấu hình mạng từ dấu nhắc lệnh (shell prompt) của trình cài đặt trực tiếp, sau đó sao chép các thiết lập đó vào hệ thống được cài đặt để chúng có hiệu lực ngay khi hệ thống này khởi động lần đầu tiên.

Để định cấu hình cho quá trình cài đặt qua PXE hoặc iPXE, hãy sử dụng một trong các tùy chọn sau:

* Tham khảo các bảng "coreos-installer và các tùy chọn khởi động cho quá trình cài đặt qua ISO và PXE". 
* Sử dụng cấu hình máy (machine config) để sao chép các tệp tin mạng vào hệ thống được cài đặt.

Để định cấu hình cho quá trình cài đặt qua ISO, hãy thực hiện theo quy trình dưới đây.

**Quy trình**

1. Khởi động trình cài đặt ISO. 
2. Từ dấu nhắc lệnh của hệ thống trực tiếp (live system), hãy định cấu hình mạng cho hệ thống này bằng cách sử dụng các công cụ RHEL sẵn có, chẳng hạn như `nmcli` hoặc `nmtui`. 
3. Chạy lệnh `coreos-installer` để cài đặt hệ thống, đồng thời thêm tùy chọn `--copy-network` để sao chép cấu hình mạng. Ví dụ:

```shell
$ sudo coreos-installer install --copy-network \
  --ignition-url=http://host/worker.ign \
  --offline \
  /dev/disk/by-id/scsi-<serial_number>
```

> Quan trọng: Tùy chọn `--copy-network` chỉ sao chép cấu hình mạng nằm trong thư mục `/etc/NetworkManager/system-connections`. Cụ thể, tùy chọn này không sao chép tên máy (hostname) của hệ thống.

4. Hãy khởi động lại vào hệ thống đã cài đặt.

###### 2.3.12.3.2. Phân vùng đĩa
Các phân vùng đĩa được tạo trên các nút (node) của cụm OpenShift Container Platform trong quá trình cài đặt Red Hat Enterprise Linux CoreOS (RHCOS). Mỗi nút RHCOS thuộc một kiến trúc cụ thể sẽ sử dụng cùng một bố cục phân vùng, trừ khi bạn thay đổi cấu hình phân vùng mặc định.

Trong quá trình cài đặt RHCOS, kích thước của hệ thống tệp gốc (root file system) sẽ được mở rộng để tận dụng toàn bộ dung lượng trống còn lại trên thiết bị đích.

> **Quan trọng:** Việc sử dụng lược đồ phân vùng tùy chỉnh trên nút có thể khiến OpenShift Container Platform không giám sát hoặc không đưa ra cảnh báo đối với một số phân vùng trên nút đó. Để biết thêm thông tin về việc giám sát hệ thống tệp của máy chủ (host) khi sử dụng phân vùng tùy chỉnh, hãy xem phần Tìm hiểu về Giám sát Hệ thống tệp trong OpenShift (các điều kiện loại bỏ/eviction conditions).

OpenShift Container Platform giám sát hai định danh hệ thống tệp sau:

* `nodefs`: hệ thống tệp chứa thư mục `/var/lib/kubelet`. 
* `imagefs`: hệ thống tệp chứa thư mục `/var/lib/containers`.

Với lược đồ phân vùng mặc định, cả `nodefs` và `imagefs` đều giám sát cùng một hệ thống tệp gốc là `/`.

Để thay đổi cấu hình phân vùng mặc định khi cài đặt RHCOS trên một nút của cụm OpenShift Container Platform, bạn cần tạo các phân vùng riêng biệt. Chẳng hạn, nếu bạn muốn thêm một phân vùng lưu trữ riêng cho các container và hình ảnh container (container image), bạn có thể gắn (mount) thư mục `/var/lib/containers` vào một phân vùng riêng. Khi đó, kubelet sẽ giám sát `/var/lib/containers` dưới dạng `imagefs`, đồng thời giám sát hệ thống tệp gốc dưới dạng `nodefs`.

> **Quan trọng:** Nếu bạn đã thay đổi kích thước đĩa để chứa một hệ thống tệp lớn hơn, hãy cân nhắc việc tạo một phân vùng riêng cho `/var/lib/containers`. Ngoài ra, đối với các đĩa sử dụng định dạng `xfs`, việc điều chỉnh kích thước đĩa có thể giúp giảm thiểu các vấn đề về tiêu tốn tài nguyên CPU phát sinh do số lượng nhóm phân bổ (allocation groups) quá lớn.

OpenShift Container Platform hỗ trợ thêm một phân vùng duy nhất để gắn lưu trữ vào thư mục /var hoặc một thư mục con của /var. Ví dụ:

* `/var/lib/containers`: Chứa các nội dung liên quan đến container; dung lượng này có thể tăng lên khi có thêm nhiều hình ảnh và container được thêm vào hệ thống. 
* `/var/lib/etcd`: Chứa dữ liệu mà bạn có thể muốn tách riêng, chẳng hạn như để tối ưu hóa hiệu suất lưu trữ cho etcd.
* `/var`: Chứa dữ liệu mà bạn có thể muốn tách riêng, chẳng hạn như để phục vụ mục đích kiểm toán. 

> **Quan trọng:** Đối với các ổ đĩa có dung lượng lớn hơn 100GB, và đặc biệt là lớn hơn 1TB, hãy tạo một phân vùng /var riêng biệt.

Việc lưu trữ nội dung của thư mục `/var` trên một phân vùng riêng giúp dễ dàng mở rộng dung lượng lưu trữ cho các khu vực này khi cần thiết, đồng thời cho phép cài đặt lại OpenShift Container Platform sau này mà vẫn giữ nguyên dữ liệu đó. Phương pháp này giúp loại bỏ nhu cầu tải lại các container hoặc sao chép các tệp nhật ký (log) lớn trong quá trình cập nhật hệ thống.

Việc sử dụng một phân vùng riêng cho thư mục `/var` (hoặc một thư mục con của /var) cũng giúp ngăn chặn tình trạng dữ liệu trong thư mục đó tăng lên quá mức, gây đầy hệ thống tệp gốc (root file system).

Quy trình dưới đây thiết lập một phân vùng `/var` riêng biệt bằng cách thêm một tệp cấu hình máy (machine config manifest) vào tệp cấu hình Ignition dành cho một loại nút (node type) trong giai đoạn chuẩn bị cài đặt.

**Quy trình**

1. Trên máy chủ cài đặt (installation host), hãy di chuyển đến thư mục chứa chương trình cài đặt OpenShift Container Platform và tạo các tệp cấu hình Kubernetes (Kubernetes manifests) cho cụm (cluster):

```shell
$ openshift-install create manifests --dir <installation_directory>
```

2. Hãy tạo một cấu hình Butane để thiết lập phân vùng bổ sung. Chẳng hạn, bạn có thể đặt tên tệp là $HOME/clusterconfig/98-var-partition.bu, thay đổi tên thiết bị đĩa thành tên thiết bị lưu trữ trên các hệ thống worker, đồng thời thiết lập dung lượng lưu trữ cho phù hợp. Ví dụ này đặt thư mục /var trên một phân vùng riêng biệt:

```yaml
variant: openshift
version: 4.22.0
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 98-var-partition
storage:
  disks:
  - device: /dev/disk/by-id/<device_name>
    partitions:
    - label: var
      start_mib: <partition_start_offset>
      size_mib: <partition_size>
      number: 5
  filesystems:
    - device: /dev/disk/by-partlabel/var
      path: /var
      format: xfs
      mount_options: [defaults, prjquota]
      with_mount_unit: true
```

Trong đó:

  * `<device_name>`: Chỉ định tên thiết bị lưu trữ của ổ đĩa mà bạn muốn phân vùng.
  * `<partition_start_offset>`: Chỉ định giá trị độ lệch (offset) tối thiểu cho ổ đĩa khởi động. Để đạt hiệu suất tối ưu, hãy đặt giá trị độ lệch tối thiểu là 25000 mebibyte. Hệ thống tệp gốc (root file system) sẽ tự động được thay đổi kích thước để lấp đầy toàn bộ không gian khả dụng cho đến độ lệch đã chỉ định. Nếu không chỉ định giá trị độ lệch, hoặc nếu giá trị được chỉ định nhỏ hơn mức tối thiểu khuyến nghị, hệ thống tệp gốc sẽ quá nhỏ; điều này có thể dẫn đến việc các lần cài đặt lại RHCOS sau này ghi đè lên phần đầu của phân vùng dữ liệu.
  * `<partition_size>`: Chỉ định kích thước của phân vùng dữ liệu (tính bằng mebibyte).
  * `mount_options`: Tùy chọn gắn (mount option) `prjquota` phải được bật đối với các hệ thống tệp được sử dụng cho lưu trữ container. 

> **Lưu ý:** Khi tạo một phân vùng `/var` riêng biệt, bạn không thể sử dụng các loại instance (instance type) khác nhau cho các nút tính toán (compute node) nếu các loại instance đó không có cùng tên thiết bị.

3. Tạo tệp manifest từ cấu hình Butane và lưu tệp đó vào thư mục `clusterconfig/openshift`. Ví dụ: hãy chạy lệnh sau:

```shell
$ butane $HOME/clusterconfig/98-var-partition.bu -o $HOME/clusterconfig/openshift/98-var-partition.yaml
```

4. Tạo các tệp cấu hình Ignition bằng cách chạy lệnh sau:

```shell
$ openshift-install create ignition-configs --dir <installation_directory>
```

Trong đó: `<installation_directory>` chỉ định tên của thư mục cài đặt. 

Các tệp cấu hình Ignition được tạo cho các nút bootstrap, control plane (mặt phẳng điều khiển) và compute (nút tính toán) trong thư mục cài đặt:

```
.
├── auth
│   ├── kubeadmin-password
│   └── kubeconfig
├── bootstrap.ign
├── master.ign
├── metadata.json
└── worker.ign
```

Các tệp trong thư mục `<installation_directory>/manifest` và `<installation_directory>/openshift `được đóng gói vào các tệp cấu hình Ignition, bao gồm cả tệp chứa đối tượng `MachineConfig` tùy chỉnh có tên là `98-var-partition`.

5. Tùy chọn: Bạn có thể áp dụng cấu hình phân vùng đĩa tùy chỉnh bằng cách tham chiếu đến các tệp cấu hình Ignition trong quá trình cài đặt RHCOS.

###### 2.3.12.3.3. Ví dụ về việc giữ lại các phân vùng hiện có
Đối với quá trình cài đặt qua ISO, bạn có thể thêm các tùy chọn vào lệnh `coreos-installer` để yêu cầu chương trình cài đặt giữ lại một hoặc nhiều phân vùng hiện có. Đối với quá trình cài đặt qua PXE, bạn có thể thêm các tùy chọn `coreos.inst.*` vào tham số `APPEND` để bảo toàn các phân vùng.

Các phân vùng được giữ lại có thể là các phân vùng dữ liệu từ một hệ thống OpenShift Container Platform đang tồn tại. Bạn có thể xác định các phân vùng đĩa muốn giữ lại thông qua nhãn phân vùng (label) hoặc số thứ tự phân vùng.

> **Lưu ý:** Nếu bạn giữ lại các phân vùng hiện có mà các phân vùng đó không để lại đủ không gian trống cho RHCOS, quá trình cài đặt sẽ thất bại nhưng các phân vùng được giữ lại đó vẫn sẽ không bị hư hại.

Các ví dụ dưới đây minh họa việc bảo toàn bất kỳ phân vùng hiện có nào trong quá trình cài đặt qua ISO, với điều kiện nhãn của phân vùng đó bắt đầu bằng từ `data` (tức là `data*`):

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
  --save-partlabel 'data*' \
  --offline \
  /dev/disk/by-id/scsi-<serial_number>
```

Ví dụ sau đây chạy coreos-installer theo cách bảo toàn phân vùng thứ sáu (6) trên đĩa:

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
  --save-partindex 6 \
  --offline \
  /dev/disk/by-id/scsi-<serial_number>
```

Ví dụ sau đây giữ lại các phân vùng từ 5 trở lên:

```shell
$ coreos-installer install --ignition-url http://10.0.2.2:8080/user.ign \
  --save-partindex 5- \
  --offline \
  /dev/disk/by-id/scsi-<serial_number>
```

Trong các ví dụ trước đây có sử dụng tính năng bảo toàn phân vùng, `coreos-installer` sẽ tạo lại phân vùng đó ngay lập tức.

Các ví dụ dưới đây giúp giữ nguyên các phân vùng hiện có trong quá trình cài đặt qua PXE. Tùy chọn `APPEND` sau đây cho phép bảo toàn bất kỳ phân vùng nào có nhãn bắt đầu bằng chuỗi `data` (`data*`).

```
coreos.inst.save_partlabel=data*
```

Tùy chọn `APPEND` dưới đây bảo toàn các phân vùng từ 5 trở lên:

```
coreos.inst.save_partindex=5-
```

Tùy chọn `APPEND` sau đây bảo toàn phân vùng 6:

```
coreos.inst.save_partindex=6
```

###### 2.3.12.3.4. Xác định các cấu hình Ignition
Khi thực hiện cài đặt thủ công RHCOS, bạn có thể cung cấp hai loại cấu hình Ignition: cấu hình Ignition cho cài đặt cố định (permanent install) và cấu hình Ignition cho cài đặt trực tiếp (live install).

Khi cài đặt thủ công RHCOS, bạn có thể cung cấp hai loại cấu hình Ignition sau:

* Cấu hình Ignition cho cài đặt cố định (Permanent install Ignition config): Mọi quá trình cài đặt thủ công RHCOS đều cần sử dụng một trong các tệp cấu hình Ignition do `openshift-installer` tạo ra — chẳng hạn như `bootstrap.ign`, `master.ign` hoặc `worker.ign` — để thực hiện việc cài đặt.

> **Quan trọng:** Không được sửa đổi trực tiếp các tệp cấu hình Ignition này. Bạn có thể cập nhật các tệp manifest (tệp mô tả cấu hình) được đóng gói bên trong các tệp cấu hình Ignition, như đã nêu trong các ví dụ ở những phần trước.

Đối với cài đặt qua PXE, bạn có thể truyền các cấu hình Ignition trên dòng `APPEND` bằng cách sử dụng tùy chọn `coreos.inst.ignition_url=`. Đối với cài đặt qua ISO, sau khi ISO khởi động vào dấu nhắc lệnh (shell prompt), bạn phải chỉ định cấu hình Ignition trong dòng lệnh `coreos-installer` bằng tùy chọn `--ignition-url=`. Trong cả hai trường hợp, chỉ các giao thức HTTP và HTTPS mới được hỗ trợ. 

* Cấu hình Ignition cho cài đặt trực tiếp (Live install Ignition config): Loại cấu hình này có thể được tạo bằng cách sử dụng lệnh phụ `customize` của `coreos-installer` cùng với các tùy chọn đi kèm. Với phương pháp này, cấu hình Ignition được truyền vào phương tiện cài đặt trực tiếp (live install medium), tự động chạy ngay khi khởi động và thực hiện các tác vụ thiết lập trước hoặc sau khi hệ thống RHCOS được cài đặt vào ổ đĩa. Phương pháp này chỉ nên được sử dụng cho các tác vụ cần thực hiện một lần duy nhất và không cần áp dụng lại sau đó, chẳng hạn như các thao tác phân vùng nâng cao mà không thể thực hiện thông qua `machine config`.

Đối với khởi động qua PXE hoặc ISO, bạn có thể tạo cấu hình Ignition và thêm tùy chọn `ignition.config.url=` vào dòng `APPEND` để xác định vị trí của tệp cấu hình đó. Bạn cũng cần thêm các tham số `ignition.firstboot` và `ignition.platform.id=metal`; nếu thiếu các tham số này, tùy chọn `ignition.config.url` sẽ bị bỏ qua.

###### 2.3.12.3.5. Cấu hình bảng điều khiển (console) mặc định
Các nút (node) Red Hat Enterprise Linux CoreOS (RHCOS) được cài đặt từ ảnh khởi động (boot image) của OpenShift Container Platform 4.22 sử dụng cấu hình bảng điều khiển mặc định, được thiết kế để phù hợp với hầu hết các thiết lập trên môi trường ảo hóa và máy chủ vật lý (bare metal).

Các nền tảng đám mây và ảo hóa khác nhau có thể sử dụng các thiết lập mặc định khác nhau tùy thuộc vào kiến trúc được chọn.

Đối với các hệ thống máy chủ vật lý, cấu hình mặc định của nhân hệ điều hành (kernel) thường đặt bảng điều khiển đồ họa làm bảng điều khiển chính, trong khi bảng điều khiển nối tiếp (serial console) sẽ bị vô hiệu hóa.

Cấu hình bảng điều khiển mặc định có thể không khớp với cấu hình phần cứng cụ thể của bạn, hoặc bạn có thể có những yêu cầu đặc thù đòi hỏi phải điều chỉnh lại cấu hình này. Ví dụ:

* Bạn cần truy cập vào trình bao khẩn cấp (emergency shell) thông qua bảng điều khiển để phục vụ mục đích gỡ lỗi. 
* Nền tảng đám mây của bạn không cung cấp quyền truy cập tương tác vào bảng điều khiển đồ họa, nhưng lại cung cấp bảng điều khiển nối tiếp. 
* Bạn muốn kích hoạt nhiều bảng điều khiển cùng lúc.

Cấu hình bảng điều khiển được kế thừa từ ảnh khởi động. Điều này có nghĩa là các nút mới trong các cụm (cluster) hiện có sẽ không bị ảnh hưởng bởi những thay đổi đối với cấu hình bảng điều khiển mặc định.

Bạn có thể định cấu hình bảng điều khiển cho các hệ thống máy chủ vật lý theo những cách sau:

* Sử dụng công cụ `coreos-installer` trực tiếp trên dòng lệnh. 
* Sử dụng các lệnh phụ (subcommand) `coreos-installer iso customize` hoặc `coreos-installer pxe customize` cùng với tùy chọn `--dest-console` để tạo một ảnh tùy chỉnh giúp tự động hóa quy trình này.

> **Lưu ý:** Đối với các tùy chỉnh nâng cao, hãy thực hiện cấu hình bảng điều khiển thông qua các lệnh phụ `coreos-installer iso` hoặc `coreos-installer pxe`, thay vì sử dụng các tham số của nhân hệ điều hành (kernel arguments).

###### 2.3.12.3.6. Kích hoạt bảng điều khiển nối tiếp (serial console) cho các quá trình cài đặt qua PXE và ISO
Theo mặc định, bảng điều khiển nối tiếp trên Red Hat Enterprise Linux CoreOS (RHCOS) bị vô hiệu hóa và mọi thông tin đầu ra đều được hiển thị trên bảng điều khiển đồ họa. Bạn có thể kích hoạt bảng điều khiển nối tiếp cho các quá trình cài đặt qua PXE và ISO.

**Quy trình**

1. Khởi động trình cài đặt từ tệp ISO. 
2. Chạy lệnh `coreos-installer` để cài đặt hệ thống; trong đó, hãy thêm tùy chọn `--console` hai lần: một lần để chỉ định bảng điều khiển đồ họa và một lần để chỉ định bảng điều khiển nối tiếp:

```shell
$ coreos-installer install \
  --console=tty0 \
  --console=ttyS0,<options> \
  --ignition-url=http://host/worker.ign \
  --offline \
  /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * --console=tty0: Thiết lập console phụ mong muốn; ở đây là console đồ họa. Nếu bỏ qua tùy chọn này, console đồ họa sẽ bị vô hiệu hóa.
  * `--console=ttyS0`: Thiết lập console chính mong muốn; ở đây là console nối tiếp (serial console). Phần tùy chọn (options) xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho phần này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux (kernel) là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về console nối tiếp của nhân Linux.

3. Khởi động lại vào hệ thống đã cài đặt.

> **Lưu ý:** Bạn có thể đạt được kết quả tương tự bằng cách sử dụng tùy chọn `--append-karg` của `coreos-installer install` và chỉ định console thông qua tham số `console=`. Tuy nhiên, cách này chỉ thiết lập console cho nhân Linux (kernel) chứ không áp dụng cho bộ nạp khởi động (bootloader).

Để cấu hình cài đặt qua PXE, hãy đảm bảo rằng tùy chọn dòng lệnh nhân (kernel command-line option) `coreos.inst.install_dev` không được đưa vào; sau đó, sử dụng dấu nhắc lệnh (shell prompt) để chạy thủ công `coreos-installer` theo quy trình cài đặt từ tệp ISO đã nêu ở trên.

###### 2.3.12.3.7. Tùy chỉnh quá trình cài đặt RHCOS thông qua live ISO hoặc PXE
Bạn có thể sử dụng ảnh live ISO hoặc môi trường PXE để cài đặt RHCOS bằng cách đưa trực tiếp tệp cấu hình Ignition vào ảnh đó. Việc này tạo ra một ảnh tùy chỉnh mà bạn có thể sử dụng để cung cấp (provision) hệ thống của mình.

Đối với ảnh ISO, cơ chế thực hiện là sử dụng lệnh phụ `coreos-installer iso customize`; lệnh này sẽ sửa đổi tệp .iso dựa trên cấu hình của bạn. Tương tự, đối với môi trường PXE, bạn sử dụng lệnh phụ `coreos-installer pxe customize` để tạo một tệp initramfs mới bao gồm các tùy chỉnh của bạn.

Lệnh phụ `customize` là một công cụ đa năng, cho phép tích hợp nhiều loại tùy chỉnh khác nhau. Dưới đây là một số ví dụ về các tùy chỉnh phổ biến:

* Thêm các chứng chỉ CA tùy chỉnh (trong trường hợp chính sách bảo mật của doanh nghiệp yêu cầu). 
* Cấu hình các thiết lập mạng mà không cần sử dụng các tham số nhân (kernel arguments). 
* Nhúng các tập lệnh (script) hoặc tệp nhị phân (binary) tùy ý để chạy trước hoặc sau quá trình cài đặt.

###### 2.3.12.3.8. Tùy chỉnh tệp ISO RHCOS chạy trực tiếp (live ISO)
Bạn có thể tùy chỉnh trực tiếp tệp ISO RHCOS bằng lệnh phụ `iso customize` của `coreos-installer`. Khi khởi động từ tệp ISO này, các tùy chỉnh sẽ được tự động áp dụng. Bạn có thể sử dụng tính năng này để cấu hình tệp ISO sao cho tự động cài đặt RHCOS.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) của `coreos-installer`](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/). 
2. Lấy tệp ISO RHCOS và tệp cấu hình Ignition từ trang [lưu trữ (mirror) của RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để đưa cấu hình Ignition trực tiếp vào tệp ISO:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
  --dest-ignition bootstrap.ign \
  --dest-device /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `--dest-ignition`: Chỉ định tệp cấu hình Ignition được tạo ra từ chương trình cài đặt openshift-installer.
  * `--dest-device`: Khi bạn chỉ định tùy chọn này, tệp ảnh ISO sẽ tự động thực hiện quá trình cài đặt. Nếu không, tệp ảnh vẫn được cấu hình sẵn cho việc cài đặt nhưng sẽ không tự động tiến hành trừ khi bạn chỉ định tham số nhân (kernel argument) `coreos.inst.install_dev`.

3. Tùy chọn: Để xóa các tùy chỉnh trên tệp ảnh ISO và đưa tệp ảnh trở về trạng thái ban đầu, hãy chạy lệnh sau:

```shell
$ coreos-installer iso reset rhcos-<version>-live.x86_64.iso
```

Giờ đây, bạn có thể tùy chỉnh lại tệp ảnh ISO trực tiếp (live ISO image) hoặc sử dụng nó ở trạng thái nguyên bản.

Việc áp dụng các tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.3.12.3.8.1. Chỉnh sửa tệp ISO cài đặt trực tiếp (live install ISO) để kích hoạt bảng điều khiển nối tiếp (serial console)
Để chuyển hướng đầu ra của hệ thống từ giao diện đồ họa mặc định, hãy kích hoạt bảng điều khiển nối tiếp bằng cách chỉnh sửa tệp ISO cài đặt trực tiếp. Cấu hình này đảm bảo khả năng truy cập vào các thông báo khởi động trên các cụm OpenShift Container Platform phiên bản 4.12 trở lên.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy tệp ISO RHCOS từ trang [lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ISO này, qua đó kích hoạt bảng điều khiển nối tiếp để nhận dữ liệu đầu ra:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
  --dest-ignition <path> \
  --dest-console tty0 \
  --dest-console ttyS0,<options> \
  --dest-device /dev/disk/by-id/scsi-<serial_number>
```

Trong đó:

  * `<path>`: Vị trí của tệp cấu hình Ignition cần cài đặt.
  * `tty0`: Bảng điều khiển (console) phụ mong muốn. Trong trường hợp này là bảng điều khiển đồ họa. Nếu bỏ qua tùy chọn này, bảng điều khiển đồ họa sẽ bị vô hiệu hóa.
  * `<options>`: Bảng điều khiển chính mong muốn. Trong trường hợp này là bảng điều khiển nối tiếp (serial console). Trường tùy chọn này xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho trường này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về bảng điều khiển nối tiếp của nhân Linux.
  * `<serial_number>`: Ổ đĩa được chỉ định để thực hiện cài đặt. Nếu bạn bỏ qua tùy chọn này, tệp ảnh ISO sẽ tự động chạy chương trình cài đặt, nhưng quá trình này sẽ thất bại trừ khi bạn cũng chỉ định tham số nhân (kernel argument) `coreos.inst.install_dev`. 

> **Lưu ý:** Tùy chọn `--dest-console` chỉ ảnh hưởng đến hệ thống sau khi cài đặt, không ảnh hưởng đến hệ thống chạy trực tiếp từ tệp ảnh ISO (live ISO). Để thay đổi bảng điều khiển cho hệ thống chạy trực tiếp từ tệp ảnh ISO, hãy sử dụng tùy chọn `--live-karg-append` và chỉ định bảng điều khiển thông qua tham số `console=`. 

Các tùy chỉnh của bạn sẽ được áp dụng và có hiệu lực trong mọi lần khởi động tiếp theo từ tệp ảnh ISO này.

3. Tùy chọn: Để xóa các tùy chỉnh trên tệp ảnh ISO và đưa tệp ảnh về trạng thái ban đầu, hãy chạy lệnh sau:

```shell
$ coreos-installer iso reset rhcos-<version>-live.x86_64.iso
```

Giờ đây, bạn có thể tùy chỉnh lại tệp ảnh ISO trực tiếp (live ISO image) hoặc sử dụng nó ở trạng thái nguyên bản.

###### 2.3.12.3.8.2. Sửa đổi tệp ISO cài đặt trực tiếp (live install ISO) để sử dụng cơ quan cấp chứng chỉ (CA) tùy chỉnh
Bạn có thể cung cấp các chứng chỉ của cơ quan cấp chứng chỉ (CA) cho Ignition thông qua cờ `--ignition-ca` trong lệnh phụ `customize`. Các chứng chỉ CA này có thể được sử dụng trong cả giai đoạn khởi động cài đặt lẫn giai đoạn cung cấp cấu hình (provisioning) cho hệ thống đã cài đặt.

> **Lưu ý:** Các chứng chỉ CA tùy chỉnh ảnh hưởng đến cách Ignition truy xuất các tài nguyên từ xa, nhưng chúng không ảnh hưởng đến các chứng chỉ được cài đặt trên chính hệ thống đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) của `coreos-installer`. 
2. Lấy tệp ISO của RHCOS từ [trang lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ISO nhằm sử dụng với một CA tùy chỉnh:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso --ignition-ca cert.pem
```

> **Quan trọng:** Tham số nhân (kernel parameter) `coreos.inst.ignition_url` không hoạt động với cờ `--ignition-ca`. Bạn phải sử dụng cờ `--dest-ignition` để tạo một hình ảnh (image) tùy chỉnh cho mỗi cụm (cluster).

Việc áp dụng chứng chỉ CA tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động tiếp theo của RHCOS.

###### 2.3.12.3.8.3. Sửa đổi tệp ảnh ISO cài đặt trực tiếp (live install ISO) với các thiết lập mạng tùy chỉnh
Bạn có thể nhúng một tệp khóa (keyfile) của NetworkManager vào tệp ảnh ISO cài đặt trực tiếp và chuyển nó sang hệ thống được cài đặt thông qua tham số `--network-keyfile` của lệnh con `customize`.

> **Cảnh báo:** Khi tạo hồ sơ kết nối (connection profile), bạn bắt buộc phải sử dụng phần mở rộng tệp là `.nmconnection`. Nếu không sử dụng phần mở rộng này, hệ thống sẽ áp dụng hồ sơ kết nối đó vào môi trường cài đặt trực tiếp, nhưng lại không áp dụng cấu hình này khi các nút (node) khởi động lần đầu, dẫn đến việc thiết lập không hoạt động.

**Quy trình**

1. Tải xuống tệp thực thi `coreos-installer` từ trang [lưu trữ (mirror) tệp ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Tạo một hồ sơ kết nối cho giao diện mạng được gộp (bonded interface). Ví dụ: tạo tệp `bond0.nmconnection` trong thư mục cục bộ của bạn với nội dung như sau:

```
/[connection]
id=bond0
type=bond
interface-name=bond0
multi-connect=1

/[bond]
miimon=100
mode=active-backup

/[ipv4]
method=auto

/[ipv6]
method=auto
```

3. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em1.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em1
type=ethernet
interface-name=em1
master=bond0
multi-connect=1
slave-type=bond
```

4. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em2.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em2
type=ethernet
interface-name=em2
master=bond0
multi-connect=1
slave-type=bond
```

5. Hãy tải tệp ảnh ISO của RHCOS từ trang [lưu trữ (mirror) ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/?extIdCarryOver=true&sc_cid=RHCTG0250000454097), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO này với các thiết lập mạng mà bạn đã cấu hình:

```shell
$ coreos-installer iso customize rhcos-<version>-live.x86_64.iso \
  --network-keyfile bond0.nmconnection \
  --network-keyfile bond0-proxy-em1.nmconnection \
  --network-keyfile bond0-proxy-em2.nmconnection
```

Các thiết lập mạng được áp dụng cho hệ thống đang hoạt động và được chuyển sang hệ thống đích.

###### 2.3.12.3.8.4. Tùy chỉnh tệp ảnh ISO cài đặt trực tiếp (live install ISO) cho thiết bị khởi động iSCSI
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình bằng cách sử dụng một phiên bản tùy chỉnh của tệp ảnh RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) tệp ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy tệp ảnh ISO của RHCOS từ trang [lưu trữ (mirror) tệp ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO đó với các thông tin sau:

```shell
$ coreos-installer iso customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/disk/by-path/<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.initiator=<initiator_iqn> \
  --dest-karg-append netroot=<target_iqn> \
  -o custom.iso rhcos-<version>-live.x86_64.iso
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
  * `<target_iqn>`: Xác định vị trí của hệ thống đích. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng (port number) tương ứng, nút iSCSI đích (target iSCSI node) ở định dạng IQN, và số đơn vị logic iSCSI (LUN).
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích. `<initiator_iqn>`::Xác định tên của iSCSI initiator (phía máy khách) ở định dạng IQN. Initiator này sẽ thiết lập một phiên kết nối tới iSCSI target. `<target_iqn>`::Xác định tên của iSCSI target (phía máy chủ) ở định dạng IQN.

Để biết thêm thông tin về các tùy chọn iSCSI được hỗ trợ bởi `dracut`, vui lòng tham khảo trang hướng dẫn (manual page) `dracut.cmdline`.

###### 2.3.12.3.8.5. Tùy chỉnh tệp ảnh ISO cài đặt trực tiếp (live install ISO) cho thiết bị khởi động iSCSI sử dụng iBFT
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình hệ thống bằng cách sử dụng một phiên bản tùy chỉnh của tệp ảnh RHCOS dạng live.

**Điều kiện tiên quyết**

1. Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
2. Tùy chọn: Bạn đã thiết lập đa đường dẫn (multipath) cho iSCSI target của mình.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) tệp ảnh `coreos-installer`. 
2. Lấy tệp ảnh ISO của RHCOS từ trang lưu trữ (mirror) tệp ảnh RHCOS, sau đó chạy lệnh dưới đây để tùy chỉnh tệp ảnh ISO đó với các thông tin sau:

```shell
$ coreos-installer iso customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/mapper/mpatha \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.firmware=1 \
  --dest-karg-append rd.multipath=default \
  -o custom.iso rhcos-<version>-live.x86_64.iso
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này nên chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này nên chứa lệnh `iscsiadm --mode node --logout=all`.
  * `/dev/mapper/mpatha`: Chỉ định đường dẫn đến thiết bị. Nếu bạn đang sử dụng tính năng đa đường dẫn (multipath), thiết bị đó sẽ là `/dev/mapper/mpatha`. Trong trường hợp có nhiều thiết bị đa đường dẫn được kết nối, hoặc để xác định chính xác hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN) có sẵn trong thư mục `/dev/disk/by-path`.
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích. `rd.iscsi.firmware=1` cho biết tham số iSCSI sẽ được đọc từ firmware của BIOS.
  rd.multipath=default: Xác định xem bạn có muốn kích hoạt tính năng đa đường dẫn hay không. Đây là một tham số tùy chọn.

Để biết thêm thông tin, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) của `dracut.cmdline`.

###### 2.3.12.3.9. Tùy chỉnh môi trường RHCOS PXE trực tiếp
Bạn có thể tùy chỉnh trực tiếp môi trường RHCOS PXE bằng lệnh phụ `pxe customize` của `coreos-installer`. Khi khởi động môi trường PXE, các tùy chỉnh này sẽ được tự động áp dụng. Bạn có thể sử dụng tính năng này để cấu hình môi trường PXE nhằm tự động cài đặt RHCOS.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh `coreos-installer`. 
2. Lấy các tệp kernel, initramfs và rootfs của RHCOS từ trang lưu trữ hình ảnh RHCOS, cùng với tệp cấu hình Ignition; sau đó, chạy lệnh dưới đây để tạo tệp initramfs mới chứa các tùy chỉnh từ tệp cấu hình Ignition của bạn:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --dest-ignition bootstrap.ign \
  --dest-device /dev/disk/by-id/scsi-<serial_number> \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

Trong đó:

  * `--dest-ignition`: Chỉ định tệp cấu hình Ignition được tạo từ openshift-installer.
  * `<serial_number>`: Khi bạn chỉ định tùy chọn này, môi trường PXE sẽ tự động thực thi quá trình cài đặt. Nếu không, hình ảnh (image) vẫn được cấu hình sẵn cho việc cài đặt nhưng sẽ không tự động thực hiện trừ khi bạn chỉ định tham số nhân (kernel argument) `coreos.inst.install_dev`.
  * `<version>`: Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn.

Việc áp dụng các tùy chỉnh này sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.3.12.3.9.1. Sửa đổi môi trường PXE cài đặt trực tiếp (live install) để kích hoạt bảng điều khiển nối tiếp (serial console)
Để chuyển hướng đầu ra hệ thống từ giao diện đồ họa mặc định, hãy kích hoạt bảng điều khiển nối tiếp bằng cách sửa đổi môi trường PXE cài đặt trực tiếp. Cấu hình này đảm bảo khả năng truy cập vào các thông báo khởi động trên các cụm OpenShift Container Platform phiên bản 4.12 trở lên.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang [lưu trữ (mirror) hình ảnh](https://mirror.openshift.com/pub/openshift-v4/clients/coreos-installer/latest/) `coreos-installer`. 
2. Lấy các tệp nhân (kernel), `initramfs` và `rootfs` của RHCOS từ [trang lưu trữ hình ảnh RHCOS](https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/latest/), cùng với tệp cấu hình Ignition; sau đó, chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới, cho phép bảng điều khiển nối tiếp nhận dữ liệu đầu ra:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --dest-ignition <path> \
  --dest-console tty0 \
  --dest-console ttyS0,<options> \
  --dest-device /dev/disk/by-id/scsi-<serial_number> \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

Trong đó:

  * `<path>`: Vị trí của tệp cấu hình Ignition cần cài đặt.
  * `tty0`: Bảng điều khiển (console) phụ mong muốn. Trong trường hợp này là bảng điều khiển đồ họa. Nếu bỏ qua tùy chọn này, bảng điều khiển đồ họa sẽ bị vô hiệu hóa.
  * `<options>`: Bảng điều khiển chính mong muốn. Trong trường hợp này là bảng điều khiển nối tiếp (serial console). Trường tùy chọn này xác định tốc độ truyền (baud rate) và các thiết lập khác. Một giá trị phổ biến cho trường này là 115200n8. Nếu không cung cấp tùy chọn nào, hệ thống sẽ sử dụng giá trị mặc định của nhân Linux là 9600n8. Để biết thêm thông tin về định dạng của tùy chọn này, vui lòng tham khảo tài liệu về bảng điều khiển nối tiếp của nhân Linux.
  * `<serial_number>`: Ổ đĩa được chỉ định để thực hiện cài đặt. Nếu bạn bỏ qua tùy chọn này, môi trường PXE sẽ tự động chạy chương trình cài đặt, nhưng quá trình này sẽ thất bại trừ khi bạn cũng chỉ định tham số nhân `coreos.inst.install_dev`.
  * `<version>`: Sử dụng tệp initramfs tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn. 

Các tùy chỉnh của bạn sẽ được áp dụng và có hiệu lực trong mọi lần khởi động tiếp theo của môi trường PXE.

###### 2.3.12.3.9.2. Sửa đổi môi trường PXE cài đặt trực tiếp (live install) để sử dụng cơ quan cấp chứng chỉ (CA) tùy chỉnh
Bạn có thể cung cấp các chứng chỉ của cơ quan cấp chứng chỉ (CA) cho Ignition thông qua cờ `--ignition-ca` của lệnh phụ `customize`. Các chứng chỉ CA này có thể được sử dụng trong cả giai đoạn khởi động cài đặt lẫn giai đoạn cung cấp cấu hình (provisioning) cho hệ thống đã cài đặt.

> **Lưu ý:** Các chứng chỉ CA tùy chỉnh ảnh hưởng đến cách Ignition truy xuất các tài nguyên từ xa, nhưng chúng không ảnh hưởng đến các chứng chỉ được cài đặt trên chính hệ thống đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh `coreos-installer`. 
2. Lấy các tệp kernel, initramfs và rootfs của RHCOS từ trang lưu trữ hình ảnh RHCOS, sau đó chạy lệnh sau để tạo một tệp `initramfs` tùy chỉnh mới nhằm sử dụng với CA tùy chỉnh:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --ignition-ca cert.pem \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

3. Sử dụng tệp initramfs đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân (kernel arguments) `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn.

> **Quan trọng:** Tham số nhân `coreos.inst.ignition_url` không hoạt động với cờ `--ignition-ca`. Bạn phải sử dụng cờ `--dest-ignition` để tạo một hình ảnh (image) tùy chỉnh cho mỗi cụm (cluster).

Việc áp dụng chứng chỉ CA tùy chỉnh của bạn sẽ ảnh hưởng đến mọi lần khởi động RHCOS sau đó.

###### 2.3.12.3.9.3. Thay đổi môi trường PXE cài đặt trực tiếp (live install) với các thiết lập mạng tùy chỉnh
Bạn có thể nhúng một tệp khóa (keyfile) của NetworkManager vào môi trường PXE cài đặt trực tiếp và chuyển nó sang hệ thống được cài đặt thông qua tham số `--network-keyfile` của lệnh con `customize`.

> **Cảnh báo:** Khi tạo hồ sơ kết nối (connection profile), bạn bắt buộc phải sử dụng phần mở rộng tệp là `.nmconnection`. Nếu không có phần mở rộng này, cụm (cluster) sẽ áp dụng hồ sơ kết nối vào môi trường cài đặt trực tiếp, nhưng cấu hình đó sẽ không được áp dụng khi cụm khởi động các nút (node) lần đầu tiên, dẫn đến hệ thống không hoạt động như mong đợi.

> Quy trình

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) hình ảnh của `coreos-installer`. 
2. Tạo một hồ sơ kết nối cho giao diện mạng được gộp (bonded interface). Ví dụ: tạo tệp `bond0.nmconnection` trong thư mục cục bộ của bạn với nội dung như sau:

```
/[connection]
id=bond0
type=bond
interface-name=bond0
multi-connect=1

/[bond]
miimon=100
mode=active-backup

/[ipv4]
method=auto

/[ipv6]
method=auto
```

3. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em1.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em1
type=ethernet
interface-name=em1
master=bond0
multi-connect=1
slave-type=bond
```

4. Tạo một hồ sơ kết nối cho giao diện phụ để thêm vào liên kết (bond). Ví dụ, hãy tạo tệp `bond0-proxy-em2.nmconnection` trong thư mục cục bộ của bạn với nội dung sau:

```
/[connection]
id=em2
type=ethernet
interface-name=em2
master=bond0
multi-connect=1
slave-type=bond
```

5. Tải các tệp kernel, `initramfs` và `rootfs` của RHCOS từ trang lưu trữ (mirror) hình ảnh RHCOS, sau đó chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới, trong đó đã bao gồm các cấu hình mạng của bạn:

```shell
$ coreos-installer pxe customize rhcos-<version>-live-initramfs.x86_64.img \
  --network-keyfile bond0.nmconnection \
  --network-keyfile bond0-proxy-em1.nmconnection \
  --network-keyfile bond0-proxy-em2.nmconnection \
  -o rhcos-<version>-custom-initramfs.x86_64.img
```

6. Sử dụng tệp `initramfs` đã tùy chỉnh trong cấu hình PXE của bạn. Hãy thêm các tham số nhân (kernel arguments) `ignition.firstboot` và `ignition.platform.id=metal` nếu chúng chưa có sẵn.

Các thiết lập mạng sẽ được áp dụng cho hệ thống đang chạy (live system) và được chuyển sang hệ thống đích.

###### 2.3.12.3.9.4. Tùy chỉnh môi trường PXE cài đặt trực tiếp (live install) cho thiết bị khởi động iSCSI
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình bằng cách sử dụng một phiên bản tùy chỉnh của ảnh (image) RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) ảnh `coreos-installer`. 
2. Lấy các tệp `kernel`, `initramfs` và `rootfs` của RHCOS từ trang lưu trữ ảnh RHCOS, sau đó chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới với các thông tin sau:

```shell
$ coreos-installer pxe customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/disk/by-path/<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.initiator=<initiator_iqn> \
  --dest-karg-append netroot=<target_iqn> \
  -o custom.img rhcos-<version>-live-initramfs.x86_64.img
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target, cùng với các lệnh cần thiết để kích hoạt tính năng đa đường dẫn (multipathing).
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
  * `<target_iqn>`: Xác định vị trí của hệ thống đích. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng tương ứng, nút iSCSI đích (target iSCSI node) ở định dạng IQN, và số đơn vị logic iSCSI (LUN).
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích.
  * `<initiator_iqn>`: Xác định tên của iSCSI initiator (bên khởi tạo) hoặc máy khách (client) ở định dạng IQN. Initiator sẽ thiết lập một phiên kết nối tới iSCSI target.
  * `<target_iqn>`: Xác định tên của iSCSI target (bên đích) hoặc máy chủ (server) ở định dạng IQN. 

Để biết thêm thông tin về các tùy chọn iSCSI được hỗ trợ bởi `dracut`, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.3.12.3.9.5. Tùy chỉnh môi trường cài đặt trực tiếp (live install) qua PXE cho thiết bị khởi động iSCSI sử dụng iBFT
Bạn có thể thiết lập các giá trị iSCSI target (đích iSCSI) và initiator (bên khởi tạo iSCSI) để tự động gắn kết (mount), khởi động và cấu hình hệ thống bằng cách sử dụng một phiên bản tùy chỉnh của ảnh (image) RHCOS dạng live.

**Điều kiện tiên quyết**

* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
* Tùy chọn: Bạn đã thiết lập đa đường dẫn (multipath) cho iSCSI target của mình.

**Quy trình**

1. Tải xuống tệp nhị phân `coreos-installer` từ trang lưu trữ (mirror) ảnh `coreos-installer`. 
2. Lấy các tệp `kernel`, `initramfs` và `rootfs` của RHCOS từ trang lưu trữ ảnh RHCOS, sau đó chạy lệnh dưới đây để tạo một tệp `initramfs` tùy chỉnh mới với các thông tin sau:

```shell
$ coreos-installer pxe customize \
  --pre-install mount-iscsi.sh \
  --post-install unmount-iscsi.sh \
  --dest-device /dev/mapper/mpatha \
  --dest-ignition config.ign \
  --dest-karg-append rd.iscsi.firmware=1 \
  --dest-karg-append rd.multipath=default \
  -o custom.img rhcos-<version>-live-initramfs.x86_64.img
```

Trong đó:

  * `mount-iscsi.sh`: Chỉ định tập lệnh (script) sẽ được thực thi trước khi cài đặt. Tập lệnh này cần chứa các lệnh `iscsiadm` để gắn (mount) iSCSI target.
  * `unmount-iscsi.sh`: Chỉ định tập lệnh sẽ được thực thi sau khi cài đặt. Tập lệnh này cần chứa lệnh `iscsiadm --mode node --logout=all`.
  * `/dev/mapper/mpatha`: Chỉ định đường dẫn đến thiết bị. Nếu bạn đang sử dụng multipath (đa đường dẫn), thiết bị multipath sẽ là `/dev/mapper/mpatha`. Trong trường hợp có nhiều thiết bị multipath được kết nối, hoặc để xác định rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN) có sẵn trong thư mục `/dev/disk/by-path`.
  * `config.ign`: Chỉ định cấu hình Ignition cho hệ thống đích.
  * `rd.iscsi.firmware=1`: Chỉ định rằng tham số iSCSI sẽ được đọc từ firmware của BIOS.
  * `rd.multipath=default`: Chỉ định việc có kích hoạt tính năng multipath hay không. Đây là tham số tùy chọn. 

Để biết thêm thông tin, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) của `dracut.cmdline`.

##### 2.3.12.4. Các tùy chọn về mạng và liên kết (bonding) cho quá trình cài đặt qua ISO
Bạn có thể định cấu hình các tùy chọn nâng cao để điều chỉnh quy trình cài đặt thủ công Red Hat Enterprise Linux CoreOS (RHCOS). Các phần tiếp theo sẽ trình bày các ví dụ về tùy chọn mạng cho quá trình cài đặt bằng tệp ISO.

Nếu cài đặt RHCOS từ tệp ảnh ISO, bạn có thể thêm thủ công các tham số nhân (kernel arguments) trong quá trình khởi động tệp ảnh để định cấu hình mạng cho một nút (node). Nếu không có tham số mạng nào được chỉ định, DHCP sẽ tự động được kích hoạt trong `initramfs` khi RHCOS phát hiện cần có kết nối mạng để truy xuất tệp cấu hình Ignition.

> **Quan trọng:** Khi thêm thủ công các tham số mạng, bạn cũng phải thêm tham số nhân `rd.neednet=1` để khởi tạo mạng trong `initramfs`.

Thông tin dưới đây cung cấp các ví dụ về cách định cấu hình mạng và liên kết (bonding) trên các nút RHCOS khi cài đặt qua ISO. Các ví dụ này mô tả cách sử dụng các tham số nhân `ip=`, `nameserver=` và `bond=`.

> **Lưu ý:** Thứ tự khi thêm các tham số nhân là rất quan trọng: `ip=`, `nameserver=`, rồi mới đến `bond=`.

Các tùy chọn mạng này sẽ được chuyển đến công cụ `dracut` trong quá trình khởi động hệ thống. Để biết thêm thông tin về các tùy chọn mạng được `dracut` hỗ trợ, vui lòng tham khảo trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) `dracut.cmdline`.

###### 2.3.12.4.1. Cấu hình DHCP hoặc địa chỉ IP tĩnh
Bạn có thể cấu hình địa chỉ IP bằng cách sử dụng DHCP hoặc thiết lập một địa chỉ IP tĩnh riêng biệt. Nếu chọn sử dụng IP tĩnh, bạn phải xác định địa chỉ IP của máy chủ DNS trên mỗi nút (node).

Các ví dụ cấu hình trong quy trình này cập nhật địa chỉ IP cho các thành phần sau:

* Địa chỉ IP của nút: `10.10.10.2`
* Địa chỉ cổng mặc định (gateway): `10.10.10.254`
* Mặt nạ mạng (netmask): `255.255.255.0`
* Tên máy chủ (hostname): `core0.example.com`
* Địa chỉ máy chủ DNS: `4.4.4.41`
* Giá trị tự động cấu hình (auto-configuration): `none`. Không cần thực hiện tự động cấu hình khi mạng IP được thiết lập theo kiểu tĩnh.

**Quy trình**

1. Nhập một lệnh tương tự như lệnh dưới đây để cấu hình địa chỉ IP tĩnh:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
nameserver=4.4.4.41
```

2. Hãy nhập một lệnh như lệnh dưới đây để cấu hình địa chỉ IP DHCP:

```
ip=enp1s0:dhcp
```

> **Lưu ý:** Khi bạn sử dụng DHCP để cấu hình địa chỉ IP cho các máy RHCOS, các máy này cũng sẽ nhận thông tin máy chủ DNS thông qua DHCP. Đối với các triển khai dựa trên DHCP, bạn có thể xác định địa chỉ máy chủ DNS mà các nút RHCOS sẽ sử dụng thông qua cấu hình máy chủ DHCP của mình.

3. Nếu có từ hai giao diện mạng trở lên, hãy tắt DHCP trên các giao diện không cần thiết. Trong ví dụ này, giao diện enp1s0 được thiết lập cấu hình mạng tĩnh, còn DHCP đã bị tắt đối với giao diện enp2s0 (giao diện không được sử dụng):

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
ip=::::core0.example.com:enp2s0:none
```

4. Nếu bạn cần kết hợp các cấu hình DHCP và IP tĩnh trên các hệ thống có nhiều giao diện mạng, hãy chạy lệnh ví dụ sau đây:

```
ip=enp1s0:dhcp
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp2s0:none
```

###### 2.3.12.4.2. Cấu hình địa chỉ IP mà không cần tên máy chủ (hostname) tĩnh
Bạn có thể cấu hình địa chỉ IP mà không cần chỉ định tên máy chủ tĩnh. Nếu người dùng không thiết lập tên máy chủ tĩnh, hệ thống sẽ tự động xác định và thiết lập tên máy chủ này thông qua truy vấn DNS ngược (reverse DNS lookup).

Các ví dụ cấu hình trong quy trình này thực hiện cập nhật địa chỉ IP cho các thành phần sau:

* Địa chỉ IP của nút (node): `10.10.10.2`
* Địa chỉ cổng mặc định (gateway): `10.10.10.254`
* Mặt nạ mạng (netmask): `255.255.255.0`
* Địa chỉ máy chủ DNS: `4.4.4.41`
* Giá trị tự động cấu hình (auto-configuration): `none`. Khi mạng IP được cấu hình theo cách tĩnh (static), không cần thực hiện tự động cấu hình.

**Quy trình**

1. Để cấu hình địa chỉ IP mà không cần tên máy chủ tĩnh, hãy nhập một lệnh tương tự như lệnh dưới đây:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0::enp1s0:none
nameserver=4.4.4.41
```

###### 2.3.12.4.3. Chỉ định nhiều giao diện mạng và máy chủ DNS
Bạn có thể chỉ định nhiều giao diện mạng bằng cách thiết lập nhiều mục `ip=`. Bạn có thể cung cấp nhiều máy chủ DNS bằng cách thêm một mục `nameserver=` cho mỗi máy chủ.

**Quy trình**

* Để chỉ định nhiều giao diện mạng, bạn có thể nhập một lệnh tương tự như lệnh sau:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp1s0:none
ip=10.10.10.3::10.10.10.254:255.255.255.0:core0.example.com:enp2s0:none
```

* Để cung cấp nhiều máy chủ DNS bằng cách thêm một mục `nameserver=` cho mỗi máy chủ, hãy nhập một lệnh như lệnh sau đây:

```
nameserver=1.1.1.1
nameserver=8.8.8.8
```

###### 2.3.12.4.4. Cấu hình cổng mặc định (default gateway) và tuyến đường (route)
Là một tác vụ tùy chọn, bạn có thể cấu hình các tuyến đường đến những mạng bổ sung bằng cách thiết lập giá trị `rd.route=`.

> Lưu ý: Khi cấu hình một hoặc nhiều mạng, bắt buộc phải có một cổng mặc định (default gateway). Nếu cổng của mạng bổ sung khác với cổng của mạng chính, thì cổng mặc định phải là cổng của mạng chính.

**Quy trình**

* Để cấu hình cổng mặc định, hãy nhập lệnh sau:

```
ip=::10.10.10.254::::
```

* Để cấu hình tuyến đường cho một mạng bổ sung, hãy nhập lệnh sau:

```
rd.route=20.20.20.0/24:20.20.20.254:enp2s0
```

###### 2.3.12.4.5. Cấu hình VLAN trên các giao diện riêng lẻ
Là một tác vụ tùy chọn, bạn có thể cấu hình VLAN trên các giao diện riêng lẻ bằng cách sử dụng tham số `vlan=`.

**Quy trình**

* Để cấu hình VLAN trên một giao diện mạng và sử dụng địa chỉ IP tĩnh, hãy chạy lệnh sau:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:enp2s0.100:none
vlan=enp2s0.100:enp2s0
```

* Để cấu hình VLAN trên một giao diện mạng và sử dụng DHCP, hãy chạy lệnh sau:

```
ip=enp2s0.100:dhcp
vlan=enp2s0.100:enp2s0
```

###### 2.3.12.4.6. Gộp nhiều giao diện mạng thành một giao diện duy nhất
Là một tác vụ tùy chọn, bạn có thể gộp nhiều giao diện mạng thành một giao diện duy nhất bằng cách sử dụng tùy chọn `bond=`.

Ví dụ dưới đây minh họa việc chỉnh sửa tệp `/etc/config/network` và chỉ định cú pháp sau để gộp nhiều giao diện mạng thành một giao diện duy nhất:

```
bond=<name>[:<network_interfaces>][:<options>]
```

* `<name>`: Chỉ định tên của thiết bị liên kết (bonding device), ví dụ: bond0. 
* `<network_interfaces>`: Chỉ định danh sách các giao diện vật lý (ethernet), cách nhau bằng dấu phẩy, chẳng hạn như em1, em2. 
* `<options>`: Chỉ định danh sách các tùy chọn liên kết (bonding options), cách nhau bằng dấu phẩy. Hãy chạy lệnh `modinfo bonding` để xem các tùy chọn khả dụng.

Khi tạo một giao diện liên kết (bonded interface) bằng lệnh `bond=`, bạn phải chỉ định cách thức gán địa chỉ IP cùng các thông tin khác cho giao diện đó.

**Quy trình**

* Để cấu hình giao diện liên kết sử dụng DHCP, hãy chỉnh sửa tệp `/etc/config/network` và thiết lập địa chỉ IP cho giao diện liên kết đó là `dhcp`. Ví dụ:

```
ip=bond0:dhcp
```

* Để cấu hình giao diện được liên kết (bonded interface) sử dụng địa chỉ IP tĩnh, hãy chỉnh sửa tệp /etc/config/network rồi nhập địa chỉ IP cụ thể mà bạn muốn cùng các thông tin liên quan. Ví dụ:

```
ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:bond0:none
```

###### 2.3.12.4.7. Gộp (bond) nhiều giao diện mạng SR-IOV vào một giao diện NIC hai cổng
Bạn có thể gộp nhiều giao diện mạng SR-IOV vào một giao diện NIC hai cổng bằng cách sử dụng tùy chọn `bond=`. Hãy đảm bảo thực hiện các bước quy trình trên mỗi nút (node).

**Quy trình**

1. Tạo các hàm ảo (Virtual Functions - VF) SR-IOV theo hướng dẫn trong phần Quản lý thiết bị SR-IOV. Thực hiện theo quy trình trong phần "Gắn các thiết bị mạng SR-IOV vào máy ảo". 
2. Tạo nhóm gộp (bond), gắn các VF mong muốn vào nhóm đó và thiết lập trạng thái liên kết (link state) cho nhóm gộp theo hướng dẫn trong phần Cấu hình gộp mạng (network bonding). Bạn có thể chọn bất kỳ quy trình nào được mô tả để tạo nhóm gộp. 

Các ví dụ sau đây minh họa cú pháp cần sử dụng:

  * Cú pháp để cấu hình một giao diện được gộp là `bond=<name>[:<network_interfaces>][:options]`. Trong đó, `<name>` là tên của thiết bị được gộp (ví dụ: `bond0`); `<network_interfaces>` đại diện cho các hàm ảo (VF) theo tên gọi của chúng trong nhân hệ thống (kernel) — tên này cũng xuất hiện trong kết quả của lệnh `ip link` (ví dụ: `eno1f0`, `eno2f0`); còn `options` là danh sách các tùy chọn gộp được phân tách bằng dấu phẩy. Hãy chạy lệnh `modinfo bonding` để xem các tùy chọn khả dụng.
  * Khi tạo một giao diện được gộp bằng tùy chọn `bond=`, bạn cần chỉ định cách thức gán địa chỉ IP cũng như các thông tin khác cho giao diện đó. 
    * Để cấu hình giao diện được gộp sử dụng DHCP, hãy đặt địa chỉ IP của nhóm gộp là `dhcp`. Ví dụ:

    ```
    bond=bond0:eno1f0,eno2f0:mode=active-backup
    ip=bond0:dhcp::AA:BB:CC:DD:EE:FF
    ip=eno1f0:none::AA:BB:CC:DD:EE:FF
    ip=eno2f0:none::AA:BB:CC:DD:EE:FF
    ```

    * Để cấu hình giao diện được liên kết (bonded interface) sử dụng địa chỉ IP tĩnh, hãy nhập địa chỉ IP cụ thể mà bạn muốn cùng các thông tin liên quan. Ví dụ:

    ```
    bond=bond0:eno1f0,eno2f0:mode=active-backup
    ip=10.10.10.2::10.10.10.254:255.255.255.0:core0.example.com:bond0:none
    ```

3. Tùy chọn: Bạn có thể sử dụng tính năng "network teaming" (nhóm mạng) thay thế cho "bonding" (kết hợp giao diện mạng) bằng cách dùng tham số `team=`. 
  * Cú pháp để cấu hình một giao diện "team" là: `team=name[:network_interfaces]`. Trong đó, `name` là tên của thiết bị "team" (ví dụ: `team0`), còn `network_interfaces` là danh sách các giao diện vật lý (ethernet) được phân tách bằng dấu phẩy (ví dụ: `em1`, `em2`).

  Sử dụng ví dụ sau để cấu hình một nhóm mạng (network team):

  ```
  team=team0:em1,em2
  ip=team0:dhcp
  ```

###### 2.3.12.4.8. coreos-installer và các tùy chọn khởi động cho quá trình cài đặt qua ISO và PXE
Bạn có thể cài đặt RHCOS bằng cách chạy lệnh `coreos-installer install <các tùy chọn> <thiết bị>` tại dấu nhắc lệnh, sau khi khởi động vào môi trường chạy trực tiếp (live environment) của RHCOS từ một tệp ảnh ISO.

Bảng dưới đây liệt kê các lệnh phụ, tùy chọn và đối số mà bạn có thể truyền vào lệnh `coreos-installer`.

**Bảng 2.36. Các lệnh phụ, tùy chọn dòng lệnh và đối số của coreos-installer**


| Lệnh con                                        | Mô tả                                        |
|-------------------------------------------------|----------------------------------------------|
| `$ coreos-installer install <options> <device>` | Nhúng cấu hình Ignition vào một tệp ảnh ISO. |


* Các tùy chọn cho lệnh con install của coreos-installer

| Tùy chọn                       | Mô tả                                                                                                                                                                                                                                                   |
|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-u`, `--image-url <url>`      | Nhập thủ công URL của hình ảnh.                                                                                                                                                                                                                         |
| `-f`, `--image-file <path>`    | Chỉ định thủ công một tệp hình ảnh cục bộ. Được sử dụng cho mục đích gỡ lỗi.                                                                                                                                                                            |
| `-i`, `--ignition-file <path>` | Nhúng cấu hình Ignition từ một tệp tin.                                                                                                                                                                                                                 |
| `-I`, `--ignition-url <URL>`   | Nhúng cấu hình Ignition từ một URL.                                                                                                                                                                                                                     |
| `--ignition-hash <digest>`     | Digest `type-value` của cấu hình Ignition.                                                                                                                                                                                                              |
| `-p`, `--platform <name>`      | Ghi đè ID nền tảng Ignition cho hệ thống đã cài đặt.                                                                                                                                                                                                    |
| `--console <spec>`             | Thiết lập bảng điều khiển (console) cho nhân hệ điều hành (kernel) và bộ nạp khởi động (boot loader) của hệ thống đã cài đặt. Để biết thêm thông tin về định dạng của `<spec>`                                                                          |
| `--append-karg <arg>…`         | Thêm một tham số nhân (kernel argument) mặc định vào hệ thống đã cài đặt.                                                                                                                                                                               |
| `--delete-karg <arg>…`         | Xóa một tham số nhân mặc định khỏi hệ thống đã cài đặt.                                                                                                                                                                                                 |
| `-n`, `--copy-network`         | Sao chép cấu hình mạng từ môi trường cài đặt. **Quan trọng**: Tùy chọn `--copy-network` chỉ sao chép cấu hình mạng nằm trong thư mục `/etc/NetworkManager/system-connections`. Cụ thể, tùy chọn này không sao chép tên máy chủ (hostname) của hệ thống. |
| `--network-dir <path>`         | Dùng với tùy chọn `-n`. Giá trị mặc định là `/etc/NetworkManager/system-connections/`.                                                                                                                                                                  |
| `--save-partlabel <lx>..`      | Lưu các phân vùng khớp với mẫu nhãn này.                                                                                                                                                                                                                |
| `--save-partindex <id>…`       | Lưu các phân vùng có số hoặc phạm vi này.                                                                                                                                                                                                               |
| `--insecure`                   | Bỏ qua việc xác minh chữ ký ảnh RHCOS.                                                                                                                                                                                                                  |
| `--insecure-ignition`          | Cho phép URL Ignition không có HTTPS hoặc dấu thăng (#).                                                                                                                                                                                                |
|  `--architecture <name>`       | Kiến trúc CPU đích. Các giá trị hợp lệ là `x86_64` và `aarch64`.                                                                                                                                                                                        |
| `--preserve-on-error`          | Không xóa bảng phân vùng khi xảy ra lỗi.                                                                                                                                                                                                                |
| `-h`, `--help`                 | In thông tin trợ giúp.                                                                                                                                                                                                                                  |

* Đối số của lệnh con install trong `coreos-installer`

| Đối số     | Mô tả          |
|------------|----------------|
| `<device>` | Thiết bị đích. |


* Các lệnh con ISO của `coreos-installer`

| Lệnh con                                                     | Mô tả                                                                                 |
|--------------------------------------------------------------|---------------------------------------------------------------------------------------|
| `coreos-installer iso customize <options> <ISO_image>`       | Tùy chỉnh một tệp ISO live của RHCOS.                                                 |
| `coreos-installer iso reset <options> <ISO_image>`           | Khôi phục tệp ảnh ISO trực tiếp (live ISO image) của RHCOS về các thiết lập mặc định. |
| `coreos-installer iso ignition remove <options> <ISO_image>` | Xóa cấu hình Ignition được nhúng khỏi tệp ảnh ISO.                                    |

* Các tùy chọn của lệnh con tùy chỉnh ISO trong `coreos-installer`

| Tùy chọn                      | Mô tả                                                                                                                                                           |
|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--dest-ignition <path>`      | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho hệ thống đích.                                                                  |
| `--dest-console <spec>`       | Hãy chỉ định nhân hệ điều hành và bảng điều khiển bộ nạp khởi động cho hệ thống đích.                                                                           |
| `--dest-device <path>`        | Cài đặt và ghi đè lên thiết bị đích đã chỉ định.                                                                                                                |
| `--dest-karg-append <arg>`    | Thêm một tham số nhân (kernel argument) vào mỗi lần khởi động của hệ thống đích.                                                                                |
| `--dest-karg-delete <arg>`    | Xóa một tham số nhân (kernel argument) khỏi mỗi lần khởi động của hệ thống đích.                                                                                |
| `--network-keyfile <path>`    | Cấu hình mạng bằng cách sử dụng tệp khóa (keyfile) NetworkManager được chỉ định cho các hệ thống trực tiếp (live system) và hệ thống đích (destination system). |
| `--ignition-ca <path>`        | Chỉ định một cơ quan cấp chứng chỉ TLS bổ sung để Ignition tin tưởng.                                                                                           |
| `--pre-install <path>`        | Chạy tập lệnh được chỉ định trước khi cài đặt.                                                                                                                  |
| `--post-install <path>`       | Chạy tập lệnh được chỉ định sau khi cài đặt.                                                                                                                    |
| `--installer-config <path>`   | Áp dụng tệp cấu hình trình cài đặt đã chỉ định.                                                                                                                 |
| `--live-ignition <path>`      | Hợp nhất tệp cấu hình Ignition được chỉ định vào một đoạn cấu hình mới dành cho môi trường thực tế (live environment).                                          |
| `--live-karg-append <arg>`    | Thêm một tham số nhân (kernel argument) vào mỗi lần khởi động môi trường chạy trực tiếp (live environment).                                                     |
| `--live-karg-delete <arg>`    | Xóa một tham số nhân (kernel argument) khỏi mỗi lần khởi động môi trường chạy trực tiếp (live environment).                                                     |
| `--live-karg-replace <k=o=n>` | Thay thế một tham số nhân (kernel argument) trong mỗi lần khởi động môi trường chạy trực tiếp (live environment), theo định dạng `key=old=new`.                 |
| `-f`, `--force`               | Ghi đè lên cấu hình Ignition hiện có.                                                                                                                           |
| `-o`, `--output <path>`       | Ghi tệp ISO vào một tệp đầu ra mới.                                                                                                                             |
| `-h`, `--help`                | In thông tin trợ giúp.                                                                                                                                          |
Bạn có thể tự động áp dụng các tùy chọn của `coreos-installer` ngay khi khởi động bằng cách truyền các tham số khởi động `coreos.inst` vào trình cài đặt trực tiếp (live installer) của RHCOS. Các tham số này được bổ sung bên cạnh những tham số khởi động tiêu chuẩn. 

* Đối với các bản cài đặt từ tệp ISO, bạn có thể thêm các tùy chọn `coreos.inst` bằng cách ngắt quá trình khởi động tự động tại menu của bộ nạp khởi động (boot loader). Bạn có thể thực hiện việc này bằng cách nhấn phím TAB khi tùy chọn menu "RHEL CoreOS (Live)" đang được chọn. 
* Đối với các bản cài đặt qua PXE hoặc iPXE, các tùy chọn `coreos.inst` cần được thêm vào dòng `APPEND` trước khi trình cài đặt trực tiếp RHCOS khởi chạy.

Bảng dưới đây liệt kê các tùy chọn khởi động `coreos.inst` dành cho trình cài đặt trực tiếp RHCOS trong các trường hợp cài đặt từ tệp ISO hoặc qua PXE.

**Bảng 2.37. Các tùy chọn khởi động coreos.inst**


| Đối số                       | Mô tả                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `coreos.inst.install_dev`    | Bắt buộc. Thiết bị khối trên hệ thống dùng để cài đặt. Lưu ý: Bạn nên sử dụng đường dẫn đầy đủ, chẳng hạn như `/dev/sda`, mặc dù chỉ dùng `sda` cũng được chấp nhận.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `coreos.inst.ignition_url`   | Tùy chọn: URL của cấu hình Ignition cần nhúng vào hệ thống được cài đặt. Nếu không chỉ định URL, sẽ không có cấu hình Ignition nào được nhúng vào. Chỉ hỗ trợ các giao thức HTTP và HTTPS.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `coreos.inst.save_partlabel` | Tùy chọn: Danh sách các phân vùng cần giữ lại trong quá trình cài đặt, được phân tách bằng dấu phẩy. Có thể sử dụng các ký tự đại diện (wildcard) kiểu glob. Các phân vùng được chỉ định không nhất thiết phải tồn tại từ trước.                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `coreos.inst.save_partindex` | Tùy chọn: Các chỉ số phân vùng cần giữ lại trong quá trình cài đặt, được phân tách bằng dấu phẩy. Có thể sử dụng các dải giá trị dạng `m-n` (trong đó `m` hoặc `n` có thể được lược bỏ). Các phân vùng được chỉ định không nhất thiết phải tồn tại từ trước.                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `coreos.inst.insecure`       | Tùy chọn: Cho phép hình ảnh hệ điều hành (OS image) được chỉ định bởi coreos.inst.image_url không cần phải có chữ ký.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `coreos.inst.image_url`      | Tùy chọn: Tải xuống và cài đặt hình ảnh (image) RHCOS được chỉ định. Tham số này không nên được sử dụng trong các môi trường thực tế (production) mà chỉ dành cho mục đích gỡ lỗi (debugging). Mặc dù tham số này có thể được dùng để cài đặt một phiên bản RHCOS không khớp với phương tiện cài đặt (live media) hiện có, bạn vẫn nên sử dụng phương tiện cài đặt tương ứng với phiên bản mà bạn muốn cài đặt. Nếu sử dụng `coreos.inst.image_url`, bạn bắt buộc phải dùng kèm với `coreos.inst.insecure`. Lý do là vì các phương tiện cài đặt trên phần cứng vật lý (bare-metal) không được ký bằng GPG cho OpenShift Container Platform. Chỉ hỗ trợ các giao thức HTTP và HTTPS. |
| `coreos.inst.skip_reboot`    | Tùy chọn: Hệ thống sẽ không khởi động lại sau khi cài đặt. Khi quá trình cài đặt hoàn tất, bạn sẽ nhận được thông báo cho phép kiểm tra các diễn biến trong suốt quá trình cài đặt. Tham số này không nên được sử dụng trong môi trường thực tế (production) mà chỉ dành cho mục đích gỡ lỗi.                                                                                                                                                                                                                                                                                                                                                                                       |
| `coreos.inst.platform_id`    | Tùy chọn: ID nền tảng Ignition của nền tảng mà ảnh RHCOS đang được cài đặt. Mặc định là `metal`. Tùy chọn này xác định xem có yêu cầu cấu hình Ignition từ nhà cung cấp dịch vụ đám mây, chẳng hạn như VMware hay không. Ví dụ: `coreos.inst.platform_id=vmware`.                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `ignition.config.url`        | Tùy chọn: URL của cấu hình Ignition dùng cho quá trình khởi động trực tiếp (live boot). Ví dụ, thông số này có thể được sử dụng để tùy chỉnh cách thức gọi `coreos-installer`, hoặc để thực thi mã lệnh trước hay sau quá trình cài đặt. Thông số này khác với `coreos.inst.ignition_url` — vốn là cấu hình Ignition dành cho hệ thống sau khi đã được cài đặt.                                                                                                                                                                                                                                                                                                                     |

##### 2.3.12.5. Kích hoạt tính năng đa đường dẫn (multipathing) thông qua các tham số nhân (kernel arguments) trên RHCOS
RHCOS hỗ trợ tính năng đa đường dẫn (multipathing) trên đĩa chính, giúp tăng cường khả năng chống chịu trước các lỗi phần cứng, từ đó nâng cao tính sẵn sàng của máy chủ (host).

Bạn có thể kích hoạt tính năng đa đường dẫn ngay tại thời điểm cài đặt đối với các nút (node) được cung cấp trong OpenShift Container Platform phiên bản 4.8 trở lên. Mặc dù vẫn có thể kích hoạt tính năng này sau khi cài đặt thông qua cấu hình máy (machine config), Red Hat khuyến nghị nên thực hiện ngay trong quá trình cài đặt.

Trong các cấu hình mà mọi thao tác I/O trên các đường dẫn không được tối ưu hóa (non-optimized paths) đều dẫn đến lỗi hệ thống I/O, bạn bắt buộc phải kích hoạt tính năng đa đường dẫn ngay từ bước cài đặt.

> **Quan trọng:** Trên các hệ thống IBM Z® và IBM® LinuxONE, bạn chỉ có thể kích hoạt tính năng đa đường dẫn nếu đã cấu hình cụm (cluster) cho tính năng này ngay từ khi cài đặt. Để biết thêm thông tin, hãy xem phần "Cài đặt RHCOS và khởi động quy trình bootstrap của OpenShift Container Platform" trong tài liệu hướng dẫn cài đặt cụm trên z/VM dành cho IBM Z® và IBM® LinuxONE.

Quy trình dưới đây hướng dẫn cách kích hoạt tính năng đa đường dẫn tại thời điểm cài đặt bằng cách thêm các tham số nhân (kernel arguments) vào lệnh `coreos-installer install`, đảm bảo hệ thống sau khi cài đặt sẽ sử dụng tính năng đa đường dẫn ngay từ lần khởi động đầu tiên.

> Lưu ý: OpenShift Container Platform không hỗ trợ kích hoạt tính năng đa đường dẫn như một thao tác vận hành sau cài đặt (day-2 activity) đối với các nút đã được nâng cấp từ phiên bản 4.6 trở về trước.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã xem qua nội dung về việc cài đặt RHCOS và khởi động quy trình bootstrap của OpenShift Container Platform.

**Quy trình**

1. Để kích hoạt tính năng đa đường dẫn và khởi chạy tiến trình nền (daemon) `multipathd`, hãy thực thi lệnh sau trên máy chủ cài đặt (installation host):

```shell
$ mpathconf --enable && systemctl start multipathd.service
```

  * Tùy chọn: Nếu khởi động qua PXE hoặc ISO, bạn có thể kích hoạt tính năng đa đường dẫn (multipath) bằng cách thêm tham số `rd.multipath=default` vào dòng lệnh kernel.

2. Thêm các tham số kernel bằng cách gọi chương trình coreos-installer:
  * Nếu chỉ có một thiết bị đa đường dẫn (multipath device) được kết nối với máy, thiết bị đó sẽ khả dụng tại đường dẫn /dev/mapper/mpatha. Ví dụ:

  ```shell
  $ coreos-installer install /dev/mapper/mpatha \
    --ignition-url=http://host/worker.ign \
    --append-karg rd.multipath=default \
    --append-karg root=/dev/disk/by-label/dm-mpath-root \
    --append-karg rw \
    --offline
  ```

    * `/dev/mapper/mpatha`: Cho biết đường dẫn của thiết bị đa đường dẫn (multipathed device) duy nhất.

  * Nếu có nhiều thiết bị đa đường dẫn (multipath) được kết nối với máy, thay vì sử dụng `/dev/mapper/mpatha`, Red Hat khuyến nghị sử dụng liên kết tượng trưng (symlink) dựa trên World Wide Name (WWN). Liên kết tượng trưng này có sẵn trong thư mục `/dev/disk/by-id`. Ví dụ:

  ```shell
  $ coreos-installer install /dev/disk/by-id/wwn-<wwn_ID> \
    --ignition-url=http://host/worker.ign \
    --append-karg rd.multipath=default \
    --append-karg root=/dev/disk/by-label/dm-mpath-root \
    --append-karg rw \
    --offline
  ```

  Trong đó: `<wwn_ID>` cho biết WWN ID của thiết bị đa đường dẫn (multipathed device) đích. Ví dụ: 0xx194e957fcedb4841. 

  Liên kết tượng trưng (symlink) này cũng có thể được sử dụng làm tham số nhân (kernel argument) `coreos.inst.install_dev` khi dùng các tham số `coreos.inst.*` đặc biệt để điều hướng trình cài đặt trực tiếp (live installer). Để biết thêm thông tin, hãy xem phần "Cài đặt RHCOS và bắt đầu quy trình khởi động (bootstrap) của OpenShift Container Platform".

3. Khởi động lại hệ thống đã cài đặt.
4. Kiểm tra xem các tham số nhân đã hoạt động đúng hay chưa bằng cách truy cập vào một trong các nút worker (worker node) và liệt kê các tham số dòng lệnh của nhân (có trong tệp `/proc/cmdline` trên máy chủ):

```shell
$ oc debug node/ip-10-0-141-105.ec2.internal
```

Ví dụ đầu ra

```shell
Starting pod/ip-10-0-141-105ec2internal-debug ...
To use host binaries, run `chroot /host`

sh-4.2# cat /host/proc/cmdline
...
rd.multipath=default root=/dev/disk/by-label/dm-mpath-root
...

sh-4.2# exit
```

Bạn sẽ thấy các tham số nhân (kernel arguments) đã được thêm vào.

###### 2.3.12.5.1. Kích hoạt tính năng đa đường dẫn (multipathing) trên các ổ đĩa phụ
RHCOS cũng hỗ trợ tính năng đa đường dẫn (multipathing) trên các ổ đĩa phụ. Thay vì sử dụng các tham số nhân (kernel arguments), bạn có thể dùng Ignition để kích hoạt tính năng này cho ổ đĩa phụ ngay tại thời điểm cài đặt.

**Điều kiện tiên quyết**

* Bạn đã đọc phần Phân vùng ổ đĩa (Disk partitioning). 
* Bạn đã đọc phần Kích hoạt tính năng đa đường dẫn bằng các tham số nhân trên RHCOS. 
* Bạn đã cài đặt tiện ích Butane.

**Quy trình**

1. Tạo một tệp cấu hình Butane với thông tin tương tự như ví dụ dưới đây:

Ví dụ: `multipath-config.bu`

```yaml
variant: openshift
version: 4.22.0
systemd:
  units:
    - name: mpath-configure.service
      enabled: true
      contents: |
        [Unit]
        Description=Configure Multipath on Secondary Disk
        ConditionFirstBoot=true
        ConditionPathExists=!/etc/multipath.conf
        Before=multipathd.service
        DefaultDependencies=no

        [Service]
        Type=oneshot
        ExecStart=/usr/sbin/mpathconf --enable

        [Install]
        WantedBy=multi-user.target
    - name: mpath-var-lib-container.service
      enabled: true
      contents: |
        [Unit]
        Description=Set Up Multipath On /var/lib/containers
        ConditionFirstBoot=true
        Requires=dev-mapper-mpatha.device
        After=dev-mapper-mpatha.device
        After=ostree-remount.service
        Before=kubelet.service
        DefaultDependencies=no

        [Service]
        Type=oneshot
        ExecStart=/usr/sbin/mkfs.xfs -L containers -m reflink=1 /dev/mapper/mpatha
        ExecStart=/usr/bin/mkdir -p /var/lib/containers

        [Install]
        WantedBy=multi-user.target
    - name: var-lib-containers.mount
      enabled: true
      contents: |
        [Unit]
        Description=Mount /var/lib/containers
        After=mpath-var-lib-containers.service
        Before=kubelet.service

        [Mount]
        What=/dev/disk/by-label/dm-mpath-containers
        Where=/var/lib/containers
        Type=xfs

        [Install]
        WantedBy=multi-user.target
```

Trong đó:

  * `Before=multipathd.service`: Chỉ định rằng cấu hình phải được thiết lập trước khi khởi chạy tiến trình nền (daemon) `multipath`.
  * `ExecStart=/usr/sbin/mpathconf`: Chỉ định việc khởi chạy tiện ích `mpathconf`.
  * `ConditionFirstBoot=true`: Được đặt thành giá trị `true`.
  * `[Service]`: Chỉ định việc tạo hệ thống tệp và thư mục `/var/lib/containers`.
  * `Before=kubelet.service`: Chỉ định rằng thiết bị phải được gắn (mount) trước khi khởi động bất kỳ nút (node) nào.
  * `[Mount]`: Chỉ định việc gắn thiết bị vào điểm gắn `/var/lib/containers`. Vị trí này không được phép là một liên kết tượng trưng (symlink).

2. Tạo cấu hình Ignition bằng cách chạy lệnh sau:

```shell
$ butane --pretty --strict multipath-config.bu > multipath-config.ign
```

3. Tiếp tục thực hiện các bước còn lại trong quy trình cài đặt RHCOS ở lần khởi động đầu tiên.

> **Quan trọng:** Không thêm các tham số nhân (kernel arguments) `rd.multipath` hoặc `root` vào dòng lệnh (CLI) trong quá trình cài đặt, trừ khi ổ đĩa chính cũng được cấu hình đa đường dẫn (multipath).

##### 2.3.12.6. Cài đặt thủ công RHCOS trên thiết bị khởi động iSCSI
Bạn có thể cài đặt thủ công RHCOS trên một đích iSCSI (iSCSI target).

**Điều kiện tiên quyết**

* Bạn đang ở trong môi trường chạy trực tiếp (live environment) của RHCOS. 
* Bạn có một đích iSCSI mà bạn muốn cài đặt RHCOS lên đó.

**Quy trình**

1. Gắn (mount) đích iSCSI từ môi trường chạy trực tiếp bằng cách thực thi lệnh sau:

```shell
$ iscsiadm \
  --mode discovery \
  --type sendtargets
  --portal <IP_address> \
  --login
```

Trong đó: `<IP_address>` chỉ định địa chỉ IP của cổng (portal) đích.

2. Cài đặt RHCOS vào đích iSCSI bằng cách chạy lệnh sau và sử dụng các tham số nhân (kernel arguments) cần thiết, ví dụ:

```shell
$ coreos-installer install \
  /dev/disk/by-path/ip-<IP_address>:<port>-iscsi-<target_iqn>-lun-<lun> \
  --append-karg rd.iscsi.initiator=<initiator_iqn> \
  --append.karg netroot=<target_iqn> \
  --console ttyS0,115200n8 \
  --ignition-file <path_to_file> \
  --offline
```

Trong đó:

  * `/dev/disk/by-path/ip`: Xác định vị trí cài đặt. Bạn phải cung cấp địa chỉ IP của cổng đích (target portal), số cổng tương ứng, nút iSCSI đích ở định dạng IQN và số đơn vị logic (LUN) iSCSI.
  * `<initiator_iqn>`: Xác định tên của iSCSI initiator (bên khởi tạo) – hay còn gọi là client – ở định dạng IQN. Initiator này sẽ thiết lập một phiên kết nối tới iSCSI target (đích đến).
  * `<target_iqn>`: Xác định tên của iSCSI target (đích đến) – hay còn gọi là server – ở định dạng IQN. 

Để biết thêm thông tin về các tùy chọn iSCSI được dracut hỗ trợ, hãy xem trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) dracut.cmdline.

3. Ngắt kết nối đĩa iSCSI bằng lệnh sau:

```shell
$ iscsiadm --mode node --logoutall=all
```

Quy trình này cũng có thể được thực hiện bằng cách sử dụng các lệnh phụ `coreos-installer iso customize` hoặc `coreos-installer pxe customize`.

##### 2.3.12.7. Cài đặt RHCOS trên thiết bị khởi động iSCSI sử dụng iBFT
Trên một máy hoàn toàn không có ổ đĩa cục bộ, các giá trị iSCSI target (đích) và initiator (bên khởi tạo) có thể được truyền qua iBFT. Tính năng đa đường dẫn (multipathing) cho iSCSI cũng được hỗ trợ.

**Các điều kiện tiên quyết**

* Bạn đang ở trong môi trường chạy trực tiếp (live environment) của RHCOS. 
* Bạn đã có một iSCSI target mà bạn muốn cài đặt RHCOS lên đó. 
* Tùy chọn: Bạn đã cấu hình tính năng đa đường dẫn (multipathing) cho iSCSI target của mình.

**Quy trình**

1. Gắn (mount) iSCSI target từ môi trường chạy trực tiếp bằng cách thực hiện lệnh sau:

```shell
$ iscsiadm \
  --mode discovery \
  --type sendtargets
  --portal <IP_address> \
  --login
```

Trong đó: `<IP_address>` chỉ định địa chỉ IP của cổng đích (target portal).

2. Tùy chọn: bật tính năng đa đường dẫn (multipathing) và khởi chạy tiến trình nền (daemon) bằng lệnh sau:

```shell
$ mpathconf --enable && systemctl start multipathd.service
```

3. Cài đặt RHCOS vào đích iSCSI bằng cách chạy lệnh sau và sử dụng các tham số nhân (kernel arguments) cần thiết, chẳng hạn như:

```shell
$ coreos-installer install \
  /dev/mapper/mpatha \
  --append-karg rd.iscsi.firmware=1 \
  --append-karg rd.multipath=default \
  --console ttyS0 \
  --ignition-file <path_to_file> \
  --offline
```

Trong đó:

  * `/dev/mapper/mpatha`: Chỉ định đường dẫn của một thiết bị đa đường dẫn (multipathed device). Nếu có nhiều thiết bị đa đường dẫn được kết nối, hoặc để xác định rõ ràng hơn, bạn có thể sử dụng liên kết tượng trưng (symlink) theo tên định danh toàn cầu (World Wide Name - WWN) có sẵn trong `/dev/disk/by-path`.
  * `rd.iscsi.firmware=1`: Chỉ định rằng tham số iSCSI được đọc từ firmware của BIOS.
  * `rd.multipath=default`: Chỉ định kích hoạt tính năng đa đường dẫn (multipathing). Đây là tham số tùy chọn. 

  Để biết thêm thông tin về các tùy chọn iSCSI được dracut hỗ trợ, hãy xem trang [hướng dẫn](https://www.man7.org/linux/man-pages/man7/dracut.cmdline.7.html) dracut.cmdline.

3. Ngắt kết nối (unmount) đĩa iSCSI:

```shell
$ iscsiadm --mode node --logout=all
```

Bạn cũng có thể thực hiện quy trình này bằng cách sử dụng các lệnh phụ `coreos-installer iso customize` hoặc `coreos-installer pxe customize`.

#### 2.3.13. Chờ quá trình khởi động ban đầu (bootstrap) hoàn tất
Sau khi các nút (node) trong cụm (cluster) của bạn khởi động lần đầu vào môi trường RHCOS cố định đã được cài đặt trên ổ đĩa, hãy chờ cho quá trình khởi động ban đầu (bootstrap) của OpenShift Container Platform hoàn tất.

Thông tin cấu hình được cung cấp thông qua các tệp cấu hình Ignition sẽ được sử dụng để khởi tạo quy trình bootstrap và cài đặt OpenShift Container Platform trên các máy chủ.

**Điều kiện tiên quyết**

* Bạn đã tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã thiết lập cơ sở hạ tầng mạng, DNS và cân bằng tải phù hợp. 
* Bạn đã tải xuống chương trình cài đặt và tạo các tệp cấu hình Ignition cho cụm của mình. 
* Bạn đã cài đặt RHCOS trên các máy chủ trong cụm và cung cấp các tệp cấu hình Ignition do chương trình cài đặt OpenShift Container Platform tạo ra.

**Quy trình**

1. Theo dõi quá trình khởi động ban đầu (bootstrap):

```shell
$ ./openshift-install --dir <installation_directory> wait-for bootstrap-complete \
  --log-level=info
```

Trong đó:

* `<installation_directory>`: Chỉ định đường dẫn đến thư mục lưu trữ các tệp cài đặt.
* `--log-level=info`: Có thể thay thế `info` bằng `warn`, `debug` hoặc `error` để xem các chi tiết cài đặt khác nhau. 

Ví dụ đầu ra

```
INFO Waiting up to 20m0s for the Kubernetes API at https://api.test.example.com:6443...
INFO API v1.35.4 up
INFO Waiting up to 1h0m0s for bootstrapping to complete...
INFO It is now safe to remove the bootstrap resources
```

Thời gian chờ hoàn tất quá trình khởi động ban đầu (bootstrapping) khác nhau tùy theo từng nền tảng.

Lệnh này sẽ thực thi thành công khi Kubernetes API server phát tín hiệu xác nhận rằng quá trình khởi động ban đầu đã hoàn tất trên các máy thuộc mặt phẳng điều khiển (control plane).

2. Sau khi quá trình khởi động ban đầu hoàn tất, hãy loại bỏ máy khởi động (bootstrap machine) khỏi bộ cân bằng tải (load balancer).

> **Quan trọng:** Tại thời điểm này, bạn bắt buộc phải loại bỏ máy khởi động khỏi bộ cân bằng tải. Bạn cũng có thể xóa hoặc định dạng lại chính máy khởi động đó.

#### 2.3.14. Đăng nhập vào cụm bằng CLI
Để đăng nhập vào cụm của bạn với tư cách là người dùng hệ thống mặc định, hãy xuất tệp kubeconfig. Cấu hình này cho phép CLI xác thực và kết nối với máy chủ API cụ thể đã được tạo trong quá trình cài đặt OpenShift Container Platform.

Tệp kubeconfig gắn liền với một cụm cụ thể và được OpenShift Container Platform tạo ra trong quá trình cài đặt.

**Điều kiện tiên quyết**

* Bạn đã triển khai một cụm OpenShift Container Platform. 
* Bạn đã cài đặt OpenShift CLI (oc).

**Quy trình**

1. Xuất thông tin xác thực `kubeadmin` bằng cách chạy lệnh sau:

```shell
$ export KUBECONFIG=<installation_directory>/auth/kubeconfig
```

Trong đó: `<installation_directory>` chỉ định đường dẫn đến thư mục lưu trữ các tệp cài đặt.

2. Hãy xác minh rằng bạn có thể chạy thành công các lệnh `oc` bằng cách sử dụng cấu hình đã xuất, thông qua việc thực thi lệnh sau:

```shell
$ oc whoami
```

Ví dụ đầu ra

```
system:admin
```

**Các bước tiếp theo**

* "Tùy chỉnh cụm của bạn"
* "Báo cáo tình trạng từ xa"

#### 2.3.15. Phê duyệt các yêu cầu ký chứng chỉ cho các máy của bạn
Để cho phép các máy mới được thêm vào tham gia cụm OpenShift Container Platform của bạn, hãy đảm bảo rằng cụm đã phê duyệt các yêu cầu ký chứng chỉ (CSR) đang chờ xử lý, hoặc tự bạn thực hiện phê duyệt các yêu cầu đó. Hãy phê duyệt các yêu cầu từ phía máy khách (client) trước, sau đó mới đến các yêu cầu từ phía máy chủ (server).

**Điều kiện tiên quyết**

* Bạn đã thêm các máy vào cụm của mình.

**Quy trình**

1. Xác nhận rằng cụm đã nhận diện được các máy đó:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME      STATUS    ROLES   AGE  VERSION
master-0  Ready     master  63m  v1.35.4
master-1  Ready     master  63m  v1.35.4
master-2  Ready     master  64m  v1.35.4
```

Kết quả đầu ra liệt kê tất cả các máy mà bạn đã tạo.

> **Lưu ý:** Kết quả đầu ra nêu trên có thể chưa bao gồm các nút tính toán (compute node) cho đến khi bạn phê duyệt một số yêu cầu ký chứng chỉ (CSR).

3. Xem xét các CSR đang chờ xử lý và đảm bảo rằng bạn thấy các yêu cầu từ máy khách (client request) ở trạng thái "Đang chờ xử lý" (Pending) hoặc "Đã phê duyệt" (Approved) đối với mỗi máy mà bạn đã thêm vào cụm (cluster):

```shell
$ oc get csr
```

Ví dụ đầu ra

```
NAME        AGE     REQUESTOR                                                                   CONDITION
csr-8b2br   15m     system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
csr-8vnps   15m     system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
...
```

Trong ví dụ này, có hai máy đang tham gia vào cụm (cluster). Bạn có thể thấy nhiều yêu cầu ký chứng chỉ (CSR) đã được phê duyệt hơn trong danh sách.

3. Nếu các CSR chưa được phê duyệt, sau khi tất cả các CSR dành cho các máy bạn vừa thêm đều ở trạng thái "Đang chờ xử lý" (Pending), hãy tiến hành phê duyệt các CSR đó cho các máy trong cụm của bạn:

> **Lưu ý:** Bạn phải phê duyệt các CSR trong vòng một giờ kể từ khi thêm máy vào cụm. Nếu không thực hiện trong khoảng thời gian này, các chứng chỉ sẽ tự động xoay vòng (rotate), dẫn đến việc mỗi nút (node) sẽ có nhiều hơn hai chứng chỉ. Khi đó, bạn sẽ phải phê duyệt tất cả các chứng chỉ này. Sau khi bạn phê duyệt CSR của máy khách (client CSR), kubelet sẽ tạo thêm một CSR thứ hai cho chứng chỉ phục vụ (serving certificate); CSR này cũng cần được phê duyệt thủ công. Sau đó, thành phần "machine-approver" sẽ tự động phê duyệt các yêu cầu gia hạn chứng chỉ phục vụ tiếp theo, miễn là kubelet yêu cầu chứng chỉ mới với các tham số tương tự.

> **Lưu ý:** Đối với các cụm chạy trên những nền tảng không hỗ trợ "machine API" (ví dụ: hạ tầng vật lý - bare metal - hoặc hạ tầng do người dùng tự cung cấp), bạn cần triển khai một cơ chế tự động phê duyệt các yêu cầu ký chứng chỉ phục vụ (CSR) từ kubelet. Nếu bạn không phê duyệt yêu cầu này, các lệnh như `oc exec`, `oc rsh` và `oc logs` sẽ không thể thực thi thành công, vì API server yêu cầu phải có chứng chỉ phục vụ khi kết nối với kubelet. Mọi thao tác liên lạc với điểm cuối (endpoint) của kubelet đều đòi hỏi chứng chỉ này phải được phê duyệt. Cơ chế đó cần theo dõi các CSR mới, xác nhận rằng CSR được gửi bởi tài khoản dịch vụ (service account) `node-bootstrapper` (thuộc nhóm `system:node` hoặc `system:admin`), đồng thời xác thực danh tính của nút đó. 

`* Để phê duyệt từng CSR một, hãy chạy lệnh sau cho mỗi CSR hợp lệ:

  ```shell
  $ oc adm certificate approve <csr_name>
  ```

  Trong đó: `<csr_name>` chỉ định tên của một CSR từ danh sách các CSR hiện có.

  * Để phê duyệt tất cả các CSR đang chờ xử lý, hãy chạy lệnh sau:

  ```shell
  $ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | \
    xargs --no-run-if-empty oc adm certificate approve
  ```

  > **Lưu ý:** Một số Operator có thể chưa khả dụng cho đến khi bạn phê duyệt các yêu cầu ký chứng chỉ (CSR). Mỗi node sẽ gửi hai CSR, vì vậy có thể bạn sẽ cần chạy lệnh phê duyệt CSR nhiều lần.

4. Sau khi phê duyệt các yêu cầu từ phía client, hãy xem xét các yêu cầu từ phía server đối với từng máy mà bạn đã thêm vào cụm (cluster):

```shell
$ oc get csr
```

Ví dụ đầu ra

```
NAME        AGE     REQUESTOR                                                                   CONDITION
csr-bfd72   5m26s   system:node:ip-10-0-50-126.us-east-2.compute.internal                       Pending
csr-c57lv   5m26s   system:node:ip-10-0-95-157.us-east-2.compute.internal                       Pending
...
```

5. Nếu các yêu cầu ký chứng chỉ (CSR) còn lại chưa được phê duyệt và đang ở trạng thái "Đang chờ xử lý" (Pending), hãy phê duyệt các CSR đó cho các máy trong cụm (cluster) của bạn:

  * Để phê duyệt từng yêu cầu một, hãy chạy lệnh sau cho mỗi CSR hợp lệ:

  ```shell
  $ oc adm certificate approve <csr_name>
  ```

  Trong đó: `<csr_name>` chỉ định tên của một CSR từ danh sách các CSR hiện có.

  * Để phê duyệt tất cả các CSR đang chờ xử lý, hãy chạy lệnh sau:

  ```shell
  $ oc get csr -o go-template='{{range .items}}{{if not .status}}{{.metadata.name}}{{"\n"}}{{end}}{{end}}' | \
    xargs oc adm certificate approve
  ```

6. Sau khi bạn phê duyệt tất cả các CSR của máy khách và máy chủ, các máy đó sẽ chuyển sang trạng thái Sẵn sàng (Ready). Hãy xác minh điều này bằng cách chạy lệnh sau:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME      STATUS    ROLES   AGE  VERSION
master-0  Ready     master  73m  v1.35.4
master-1  Ready     master  73m  v1.35.4
master-2  Ready     master  74m  v1.35.4
worker-0  Ready     worker  11m  v1.35.4
worker-1  Ready     worker  11m  v1.35.4
```

> **Lưu ý:** Bạn có thể cần đợi vài phút sau khi các CSR máy chủ được phê duyệt để các máy chuyển sang trạng thái `Ready`.

##### 2.3.16. Cấu hình Operator ban đầu
Sau khi mặt phẳng điều khiển (control plane) khởi tạo xong, bạn cần lập tức cấu hình một số Operator để đảm bảo tất cả chúng đều ở trạng thái sẵn sàng.

**Điều kiện tiên quyết**

* Mặt phẳng điều khiển của bạn đã khởi tạo xong.

**Quy trình**

1. Theo dõi quá trình các thành phần trong cụm (cluster) chuyển sang trạng thái hoạt động:

```shell
$ watch -n5 oc get clusteroperators
```

Ví dụ đầu ra

```
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.22.0    True        False         False      19m
baremetal                                  4.22.0    True        False         False      37m
cloud-credential                           4.22.0    True        False         False      40m
cluster-autoscaler                         4.22.0    True        False         False      37m
config-operator                            4.22.0    True        False         False      38m
console                                    4.22.0    True        False         False      26m
csi-snapshot-controller                    4.22.0    True        False         False      37m
dns                                        4.22.0    True        False         False      37m
etcd                                       4.22.0    True        False         False      36m
image-registry                             4.22.0    True        False         False      31m
ingress                                    4.22.0    True        False         False      30m
insights                                   4.22.0    True        False         False      31m
kube-apiserver                             4.22.0    True        False         False      26m
kube-controller-manager                    4.22.0    True        False         False      36m
kube-scheduler                             4.22.0    True        False         False      36m
kube-storage-version-migrator              4.22.0    True        False         False      37m
machine-api                                4.22.0    True        False         False      29m
machine-approver                           4.22.0    True        False         False      37m
machine-config                             4.22.0    True        False         False      36m
marketplace                                4.22.0    True        False         False      37m
monitoring                                 4.22.0    True        False         False      29m
network                                    4.22.0    True        False         False      38m
node-tuning                                4.22.0    True        False         False      37m
openshift-apiserver                        4.22.0    True        False         False      32m
openshift-controller-manager               4.22.0    True        False         False      30m
openshift-samples                          4.22.0    True        False         False      32m
operator-lifecycle-manager                 4.22.0    True        False         False      37m
operator-lifecycle-manager-catalog         4.22.0    True        False         False      37m
operator-lifecycle-manager-packageserver   4.22.0    True        False         False      32m
service-ca                                 4.22.0    True        False         False      38m
storage                                    4.22.0    True        False         False      37m
```

2. Cấu hình các Operator không khả dụng.

##### 2.3.16.1. Vô hiệu hóa các nguồn danh mục phần mềm mặc định
Để chỉ sử dụng các danh mục Operator đáng tin cậy hoặc có sẵn tại địa phương, hãy vô hiệu hóa các nguồn danh mục phần mềm mặc định mà OpenShift Container Platform thiết lập trong quá trình cài đặt. Trong môi trường mạng bị hạn chế, với tư cách là quản trị viên cụm (cluster administrator), bạn bắt buộc phải vô hiệu hóa các danh mục mặc định này.

**Quy trình**

* Vô hiệu hóa các nguồn danh mục mặc định bằng cách thêm `disableAllDefaultSources: true` vào đối tượng `OperatorHub`:

```shell
$ oc patch OperatorHub cluster --type json \
  -p '[{"op": "add", "path": "/spec/disableAllDefaultSources", "value": true}]'
```

> **Mẹo:** Ngoài ra, bạn có thể sử dụng bảng điều khiển web để quản lý các nguồn danh mục (catalog source). Tại trang **Administration** > **Cluster Settings** > **Configuration** > **OperatorHub**, hãy nhấp vào tab **Sources**; tại đây, bạn có thể tạo, cập nhật, xóa, vô hiệu hóa hoặc kích hoạt từng nguồn riêng lẻ.

##### 2.3.16.2. Cấu hình lưu trữ cho registry hình ảnh (image registry)
Image Registry Operator sẽ không khả dụng ngay từ đầu trên các nền tảng không cung cấp bộ lưu trữ mặc định. Sau khi cài đặt, bạn cần cấu hình bộ lưu trữ cho registry của mình để Image Registry Operator trở nên khả dụng.

Hãy cấu hình một volume bền vững (persistent volume) – đây là yêu cầu bắt buộc đối với các cụm (cluster) trong môi trường sản xuất. Đối với các cụm không thuộc môi trường sản xuất, bạn có thể cấu hình một thư mục trống (empty directory) làm vị trí lưu trữ.

Bạn cũng có thể cho phép registry hình ảnh sử dụng các loại lưu trữ khối (block storage) bằng cách áp dụng chiến lược triển khai `Recreate` trong quá trình nâng cấp.

###### 2.3.16.2.1. Thay đổi trạng thái quản lý của registry hình ảnh
Để khởi chạy registry hình ảnh (image registry), bạn phải thay đổi `managementState` trong cấu hình của Image Registry Operator từ `Removed` thành `Managed`.

**Quy trình**

* Thay đổi `managementState` trong cấu hình Image Registry Operator từ `Removed` thành `Managed`. Ví dụ:

```shell
$ oc patch configs.imageregistry.operator.openshift.io cluster --type merge \
  --patch '{"spec":{"managementState":"Managed"}}'
```

###### 2.3.16.2.2. Cấu hình lưu trữ cho registry trong các môi trường bare metal và các hình thức cài đặt thủ công khác
Với tư cách là quản trị viên cụm (cluster administrator), sau khi hoàn tất cài đặt, bạn cần cấu hình để registry sử dụng bộ lưu trữ.

**Các điều kiện tiên quyết**

* Bạn có quyền truy cập vào cụm với vai trò `cluster-admin`. 
* Cụm của bạn sử dụng các nút (node) Red Hat Enterprise Linux CoreOS (RHCOS) được cung cấp theo cách thủ công, chẳng hạn như trong môi trường bare metal. 
* Bạn đã cung cấp bộ lưu trữ bền vững (persistent storage) cho cụm của mình, ví dụ như Red Hat OpenShift Data Foundation. 

> **Quan trọng:** OpenShift Container Platform hỗ trợ quyền truy cập `ReadWriteOnce` cho bộ lưu trữ của image registry khi chỉ có một bản sao (replica). Quyền truy cập `ReadWriteOnce` cũng yêu cầu registry phải sử dụng chiến lược triển khai (rollout strategy) là `Recreate`. Để triển khai một image registry hỗ trợ tính sẵn sàng cao (high availability) với từ hai bản sao trở lên, cần phải có quyền truy cập `ReadWriteMany`. 
Bạn cần chuẩn bị một hệ thống có dung lượng lưu trữ tối thiểu là 100Gi.

**Quy trình thực hiện**

1. Để cấu hình registry sử dụng bộ lưu trữ, hãy thay đổi thuộc tính `spec.storage.pvc` trong tài nguyên `configs.imageregistry/cluster`. 

> **Lưu ý:** Khi sử dụng bộ lưu trữ chia sẻ (shared storage), hãy xem xét các thiết lập bảo mật của bạn để ngăn chặn truy cập từ bên ngoài. 

2. Xác minh rằng hiện không có pod registry nào đang chạy:

```shell
$ oc get pod -n openshift-image-registry -l docker-registry=default
```

Ví dụ đầu ra

```
No resources found in openshift-image-registry namespace
```

> **Lưu ý:** Nếu bạn đã có một pod registry trong kết quả đầu ra, bạn không cần tiếp tục thực hiện quy trình này nữa.

3. Kiểm tra cấu hình registry:

```shell
$ oc edit configs.imageregistry.operator.openshift.io
```

Ví dụ đầu ra

```yaml
storage:
  pvc:
    claim:
```

Để trống trường `claim` để cho phép tự động tạo PVC `image-registry-storage`.

4. Kiểm tra trạng thái của `clusteroperator`:

```shell
$ oc get clusteroperator image-registry
```

5. Hãy đảm bảo rằng registry của bạn được thiết lập ở chế độ quản lý để cho phép xây dựng và đẩy ảnh hệ thống.
  Chạy lệnh:

  ```shell
  $ oc edit configs.imageregistry/cluster
  ```

  Sau đó, thay đổi dòng `managementState: Removed` thành `managementState: Managed`

###### 2.3.16.2.3. Định cấu hình lưu trữ cho registry hình ảnh trong các cụm không thuộc môi trường sản xuất (non-production clusters)
Bạn phải định cấu hình lưu trữ cho Image Registry Operator. Đối với các cụm không thuộc môi trường sản xuất, bạn có thể thiết lập registry hình ảnh trỏ đến một thư mục trống; tuy nhiên, nếu bạn khởi động lại registry, tất cả các hình ảnh sẽ bị mất.

**Quy trình**

* Để thiết lập lưu trữ cho registry hình ảnh trỏ đến một thư mục trống:

```shell
$ oc patch configs.imageregistry.operator.openshift.io cluster --type merge \
  --patch '{"spec":{"storage":{"emptyDir":{}}}}'
```

> **Cảnh báo:** Chỉ cấu hình tùy chọn này cho các cụm không thuộc môi trường sản xuất (non-production clusters).

Nếu bạn chạy lệnh này trước khi Image Registry Operator khởi tạo các thành phần của nó, lệnh `oc patch` sẽ thất bại và hiển thị lỗi sau:

Ví dụ đầu ra

```
Error from server (NotFound): configs.imageregistry.operator.openshift.io "cluster" not found
```

Hãy đợi vài phút rồi chạy lại lệnh đó.

###### 2.3.16.2.4. Cấu hình lưu trữ dạng khối (block storage) cho registry trên hạ tầng vật lý (bare metal)
Để cho phép registry lưu trữ ảnh (image registry) sử dụng các loại lưu trữ dạng khối trong quá trình nâng cấp, với tư cách là quản trị viên cụm (cluster administrator), bạn có thể sử dụng chiến lược triển khai `Recreate`.

> **Quan trọng:** Các volume lưu trữ dạng khối (block storage volumes) — hay còn gọi là các volume bền vững dạng khối (block persistent volumes) — được hỗ trợ nhưng không được khuyến khích sử dụng cho registry lưu trữ ảnh trên các cụm môi trường thực tế (production clusters). Một hệ thống registry được cấu hình trên lưu trữ dạng khối sẽ không có tính sẵn sàng cao (high availability), vì registry trong trường hợp này không thể có nhiều hơn một bản sao (replica).

Nếu bạn chọn sử dụng volume lưu trữ dạng khối cho registry lưu trữ ảnh, bạn bắt buộc phải sử dụng yêu cầu volume bền vững (Persistent Volume Claim - PVC) dựa trên hệ thống tệp (filesystem).

**Quy trình**

1. Nhập lệnh sau để thiết lập lưu trữ cho registry lưu trữ ảnh thành loại lưu trữ dạng khối, đồng thời điều chỉnh (patch) registry để sử dụng chiến lược triển khai `Recreate` và chỉ chạy với một (`1`) bản sao (replica):

```shell
$ oc patch config.imageregistry.operator.openshift.io/cluster --type=merge -p '{"spec":{"rolloutStrategy":"Recreate","replicas":1}}'
```

2. Cung cấp PV cho thiết bị lưu trữ khối (block storage device) và tạo PVC cho volume đó. Volume khối được yêu cầu sử dụng chế độ truy cập ReadWriteOnce (RWO). 
  * Tạo tệp `pvc.yaml` với nội dung dưới đây để định nghĩa một đối tượng PersistentVolumeClaim (PVC) cho VMware vSphere:

  ```yaml
  kind: PersistentVolumeClaim
  apiVersion: v1
  metadata:
    name: image-registry-storage
    namespace: openshift-image-registry
  spec:
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 100Gi
  ```
  Trong đó:

    * `metadata.name`: Chỉ định một tên duy nhất đại diện cho đối tượng `PersistentVolumeClaim`.
    * `metadata.namespace:` Chỉ định namespace cho đối tượng `PersistentVolumeClaim`; trong trường hợp này là openshift-image-registry.
    * `spec.accessModes`: Chỉ định chế độ truy cập (access mode) của `PersistentVolumeClaim`. Với tùy chọn `ReadWriteOnce`, volume có thể được mount (gắn) với quyền đọc và ghi bởi một node duy nhất.
    * `spec.resources.requests.storage`: Kích thước của `PersistentVolumeClaim`.

  * Hãy nhập lệnh sau để tạo đối tượng `PersistentVolumeClaim` từ tệp tin này:

  ```shell
  $ oc create -f pvc.yaml -n openshift-image-registry
  ```

3. Hãy nhập lệnh sau để chỉnh sửa cấu hình registry sao cho nó tham chiếu đến PVC chính xác:

```shell
$ oc edit config.imageregistry.operator.openshift.io -o yaml
```

Ví dụ đầu ra

```yaml
storage:
  pvc:
    claim:
```

Khi tạo một PVC tùy chỉnh, bạn có thể để trống trường `claim` để hệ thống tự động tạo một PVC `image-registry-storage` theo mặc định.

#### 2.3.17. Hoàn tất cài đặt trên cơ sở hạ tầng do người dùng cung cấp
Để hoàn tất quá trình cài đặt trên cơ sở hạ tầng do người dùng cung cấp, hãy thực hiện các bước cuối cùng để triển khai cụm (cluster) sau khi đã định cấu hình các Operator. Việc này đảm bảo cụm hoạt động hoàn chỉnh trên cơ sở hạ tầng mà bạn cung cấp.

**Điều kiện tiên quyết**

* Control plane của bạn đã được khởi tạo. 
* Bạn đã hoàn tất việc định cấu hình ban đầu cho các Operator.

**Quy trình**

1. Xác nhận rằng tất cả các thành phần của cụm đều đang trực tuyến (online) bằng lệnh sau:

```shell
$ watch -n5 oc get clusteroperators
```

Ví dụ đầu ra

```
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.22.0    True        False         False      19m
baremetal                                  4.22.0    True        False         False      37m
cloud-credential                           4.22.0    True        False         False      40m
cluster-autoscaler                         4.22.0    True        False         False      37m
config-operator                            4.22.0    True        False         False      38m
console                                    4.22.0    True        False         False      26m
csi-snapshot-controller                    4.22.0    True        False         False      37m
dns                                        4.22.0    True        False         False      37m
etcd                                       4.22.0    True        False         False      36m
image-registry                             4.22.0    True        False         False      31m
ingress                                    4.22.0    True        False         False      30m
insights                                   4.22.0    True        False         False      31m
kube-apiserver                             4.22.0    True        False         False      26m
kube-controller-manager                    4.22.0    True        False         False      36m
kube-scheduler                             4.22.0    True        False         False      36m
kube-storage-version-migrator              4.22.0    True        False         False      37m
machine-api                                4.22.0    True        False         False      29m
machine-approver                           4.22.0    True        False         False      37m
machine-config                             4.22.0    True        False         False      36m
marketplace                                4.22.0    True        False         False      37muser
monitoring                                 4.22.0    True        False         False      29m
network                                    4.22.0    True        False         False      38m
node-tuning                                4.22.0    True        False         False      37m
openshift-apiserver                        4.22.0    True        False         False      32muser
openshift-controller-manager               4.22.0    True        False         False      30m
openshift-samples                          4.22.0    True        False         False      32m
operator-lifecycle-manager                 4.22.0    True        False         False      37m
operator-lifecycle-manager-catalog         4.22.0    True        False         False      37m
operator-lifecycle-manager-packageserver   4.22.0    True        False         False      32m
service-ca                                 4.22.0    True        False         False      38m
storage                                    4.22.0    True        False         False      37m
```

Ngoài ra, lệnh sau đây sẽ thông báo cho bạn khi tất cả các cụm đã sẵn sàng. Lệnh này cũng truy xuất và hiển thị thông tin xác thực:

```shell
$ ./openshift-install --dir <installation_directory> wait-for install-complete
```

Trong đó: `<installation_directory>` chỉ định đường dẫn đến thư mục nơi bạn lưu trữ các tệp cài đặt.

Ví dụ đầu ra

```
INFO Waiting up to 30m0s for the cluster to initialize...
```

Lệnh này thực thi thành công khi Cluster Version Operator hoàn tất việc triển khai cụm OpenShift Container Platform từ máy chủ API Kubernetes.

> **Quan trọng:**
> 
> * Các tệp cấu hình Ignition do chương trình cài đặt tạo ra chứa các chứng chỉ có thời hạn 24 giờ; sau khoảng thời gian này, các chứng chỉ đó sẽ được gia hạn. Nếu cụm (cluster) bị tắt trước khi các chứng chỉ được gia hạn và sau đó được khởi động lại khi đã qua 24 giờ, cụm sẽ tự động khôi phục các chứng chỉ đã hết hạn. Một ngoại lệ là bạn phải phê duyệt thủ công các yêu cầu ký chứng chỉ (CSR) của node-bootstrapper đang chờ xử lý để khôi phục các chứng chỉ kubelet. Để biết thêm thông tin, hãy xem tài liệu về cách khôi phục các chứng chỉ control plane đã hết hạn. 
> * Bạn nên sử dụng các tệp cấu hình Ignition trong vòng 12 giờ kể từ khi chúng được tạo, vì quá trình xoay vòng chứng chỉ 24 giờ thường diễn ra trong khoảng từ 16 đến 22 giờ sau khi cụm được cài đặt. Việc sử dụng các tệp cấu hình Ignition trong vòng 12 giờ giúp tránh nguy cơ cài đặt thất bại trong trường hợp quá trình cập nhật chứng chỉ diễn ra đúng lúc đang thực hiện cài đặt.

2. Xác nhận rằng Kubernetes API server đang giao tiếp với các pod. 
  * Để xem danh sách tất cả các pod, hãy sử dụng lệnh sau:

  ```shell
  $ oc get pods --all-namespaces
  ```

  Ví dụ đầu ra

  ```
  NAMESPACE                         NAME                                            READY   STATUS      RESTARTS   AGE
  openshift-apiserver-operator      openshift-apiserver-operator-85cb746d55-zqhs8   1/1     Running     1          9m
  openshift-apiserver               apiserver-67b9g                                 1/1     Running     0          3m
  openshift-apiserver               apiserver-ljcmx                                 1/1     Running     0          1m
  openshift-apiserver               apiserver-z25h4                                 1/1     Running     0          2m
  openshift-authentication-operator authentication-operator-69d5d8bf84-vh2n8        1/1     Running     0          5m
  ```

  * Xem nhật ký của một pod có tên trong kết quả đầu ra của lệnh trước đó bằng cách sử dụng lệnh sau:

  ```shell
  $ oc logs <pod_name> -n <namespace>
  ```

  Trong đó: `<namespace>` chỉ định tên pod và namespace (không gian tên), như được hiển thị trong kết quả của một lệnh trước đó. 

  Nếu nhật ký (logs) của pod được hiển thị, nghĩa là máy chủ API Kubernetes có thể giao tiếp với các máy trong cụm (cluster).

3. Đối với hệ thống sử dụng Giao thức Fibre Channel (FCP), cần thực hiện các bước bổ sung để kích hoạt tính năng đa đường dẫn (multipathing). Không kích hoạt tính năng đa đường dẫn trong quá trình cài đặt.

Để biết thêm thông tin, hãy xem mục "Kích hoạt tính năng đa đường dẫn bằng các tham số nhân (kernel arguments) trên RHCOS" trong tài liệu về các tác vụ cấu hình máy sau cài đặt.

4. Đăng ký cụm (cluster) của bạn trên trang [Đăng ký cụm](https://console.redhat.com/openshift/register).

#### 2.3.18. Truy cập Telemetry cho OpenShift Container Platform
Để cung cấp các chỉ số về tình trạng cụm (cluster) và kết quả của các bản cập nhật, dịch vụ Telemetry cần có quyền truy cập internet. Khi được kết nối, dịch vụ này sẽ tự động chạy theo mặc định và đăng ký cụm của bạn với [OpenShift Cluster Manager](https://console.redhat.com/openshift).

Sau khi xác nhận rằng danh mục tài nguyên (inventory) trên [OpenShift Cluster Manager](https://console.redhat.com/openshift) đã chính xác — dù được duy trì tự động bởi Telemetry hay được cập nhật thủ công thông qua OpenShift Cluster Manager — bạn có thể sử dụng tính năng "subscription watch" để theo dõi các gói đăng ký (subscription) OpenShift Container Platform ở cấp độ tài khoản hoặc cấp độ đa cụm. Để biết thêm thông tin về "subscription watch", hãy xem mục "Dữ liệu được thu thập và sử dụng bởi các dịch vụ đăng ký của Red Hat" trong phần Tài nguyên bổ sung.

#### 2.3.19. Các bước tiếp theo

* Xác thực quá trình cài đặt. 
* Tùy chỉnh cụm (cluster) của bạn. 
* Cấu hình các luồng hình ảnh (image stream) cho Cluster Samples Operator và công cụ must-gather. 
* Tìm hiểu cách sử dụng Operator Lifecycle Manager trong các môi trường không kết nối mạng (disconnected environments). 
* Nếu registry phản chiếu (mirror registry) mà bạn đã sử dụng để cài đặt cụm có chứa chứng chỉ từ một cơ quan cấp chứng chỉ (CA) đáng tin cậy, hãy thêm chứng chỉ đó vào cụm bằng cách cấu hình các kho lưu trữ tin cậy (trust store) bổ sung. 
* Nếu cần, bạn có thể thiết lập tính năng báo cáo tình trạng từ xa (remote health reporting). 
* Nếu cần, hãy xem hướng dẫn về việc đăng ký cụm trong môi trường không kết nối mạng.

### 2.4. Mở rộng quy mô cụm do người dùng cung cấp bằng Bare Metal Operator
Sau khi triển khai một cụm trên cơ sở hạ tầng do người dùng cung cấp, bạn có thể sử dụng Bare Metal Operator (BMO) cùng các thành phần metal khác để mở rộng quy mô các máy chủ vật lý (bare-metal host) trong cụm đó. Phương pháp này giúp bạn mở rộng quy mô cụm do người dùng cung cấp theo cách tự động hơn.

#### 2.4.1. Về việc mở rộng quy mô cụm do người dùng cung cấp bằng Bare Metal Operator
Bạn có thể mở rộng quy mô các cụm hạ tầng do người dùng cung cấp (user-provisioned infrastructure clusters) bằng cách sử dụng Bare Metal Operator (BMO) cùng các thành phần metal3 khác.

Các hệ thống hạ tầng do người dùng cung cấp không bao gồm Machine API Operator. Thông thường, Machine API Operator đảm nhận việc quản lý vòng đời của các nút (node) vật lý trong một cụm. Tuy nhiên, bạn vẫn có thể sử dụng BMO và các thành phần metal3 khác để mở rộng quy mô các nút trong các cụm do người dùng cung cấp mà không cần đến Machine API Operator.

##### 2.4.1.1. Các điều kiện tiên quyết để mở rộng quy mô cụm do người dùng cung cấp
Cần đáp ứng các điều kiện tiên quyết sau đây trước khi mở rộng quy mô một cụm do người dùng cung cấp. 

* Bạn đã cài đặt một cụm trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure) trên phần cứng vật lý (bare metal). 
* Bạn có quyền truy cập vào bộ điều khiển quản lý bo mạch chủ (BMC) của các máy chủ.

##### 2.4.1.2. Các hạn chế khi mở rộng quy mô cụm do người dùng cung cấp
Các hạn chế sau đây áp dụng cho việc mở rộng quy mô cụm do người dùng cung cấp (user-provisioned cluster). 

* Bạn không thể sử dụng mạng cung cấp (provisioning network) để mở rộng quy mô các cụm hạ tầng do người dùng cung cấp thông qua Bare Metal Operator (BMO). 
* Do đó, bạn chỉ có thể sử dụng các trình điều khiển máy chủ vật lý (bare-metal host drivers) hỗ trợ khởi động qua mạng bằng phương tiện ảo (virtual media networking booting), chẳng hạn như `redfish-virtualmedia` và `idrac-virtualmedia`. 
* Bạn không thể mở rộng quy mô các đối tượng MachineSet trong các cụm hạ tầng do người dùng cung cấp thông qua BMO.

#### 2.4.2. Cấu hình tài nguyên cung cấp (provisioning resource) để mở rộng quy mô các cụm do người dùng cung cấp
Tạo một tài nguyên tùy chỉnh (Custom Resource - CR) loại Provisioning để kích hoạt các thành phần nền tảng Metal trên một cụm hạ tầng do người dùng cung cấp.

**Điều kiện tiên quyết**

* Bạn đã cài đặt một cụm hạ tầng do người dùng cung cấp trên nền tảng phần cứng vật lý (bare metal).

**Quy trình**

1. Tạo một CR loại Provisioning. 
  * Lưu nội dung YAML dưới đây vào tệp `provisioning.yaml`:

  ```yaml
  apiVersion: metal3.io/v1alpha1
  kind: Provisioning
  metadata:
    name: provisioning-configuration
  spec:
    provisioningNetwork: "Disabled"
    watchAllNamespaces: false
  ```

  > **Lưu ý:** OpenShift Container Platform 4.22 không hỗ trợ việc kích hoạt mạng cung cấp (provisioning network) khi bạn mở rộng quy mô một cụm do người dùng cung cấp (user-provisioned cluster) bằng cách sử dụng Bare Metal Operator.

2. Tạo `Provisioning` CR bằng cách chạy lệnh sau:

```shell
$ oc create -f provisioning.yaml
```

Ví dụ đầu ra

```
provisioning.metal3.io/provisioning-configuration created
```

**Xác minh**

Xác minh rằng dịch vụ cung cấp (provisioning service) đang chạy bằng cách thực thi lệnh sau:

```shell
$ oc get pods -n openshift-machine-api
```

Ví dụ đầu ra

```
NAME                                                  READY   STATUS    RESTARTS        AGE
cluster-autoscaler-operator-678c476f4c-jjdn5          2/2     Running   0               5d21h
cluster-baremetal-operator-6866f7b976-gmvgh           2/2     Running   0               5d21h
control-plane-machine-set-operator-7d8566696c-bh4jz   1/1     Running   0               5d21h
ironic-proxy-64bdw                                    1/1     Running   0               5d21h
ironic-proxy-rbggf                                    1/1     Running   0               5d21h
ironic-proxy-vj54c                                    1/1     Running   0               5d21h
machine-api-controllers-544d6849d5-tgj9l              7/7     Running   1 (5d21h ago)   5d21h
machine-api-operator-5c4ff4b86d-6fjmq                 2/2     Running   0               5d21h
metal3-6d98f84cc8-zn2mx                               5/5     Running   0               5d21h
metal3-image-customization-59d745768d-bhrp7           1/1     Running   0               5d21h
```

#### 2.4.3. Cấp phát các host mới trong cụm do người dùng cấp phát (user-provisioned cluster) bằng cách sử dụng BMO
Bạn có thể sử dụng Bare Metal Operator (BMO) để cấp phát các host vật lý (bare-metal host) trong một cụm do người dùng cấp phát bằng cách tạo một tài nguyên tùy chỉnh (custom resource - CR) có tên là BareMetalHost.

> **Lưu ý:** Khi cấp phát các host vật lý vào cụm bằng BMO, tham số `spec.externallyProvisioned` trong tài nguyên tùy chỉnh BareMetalHost sẽ mặc định được đặt thành `false`. Không được đặt tham số `spec.externallyProvisioned` thành `true`, vì thiết lập này sẽ dẫn đến các hành vi không mong muốn.

**Điều kiện tiên quyết**

* Bạn đã tạo một cụm bare-metal do người dùng cấp phát. 
* Bạn có quyền truy cập vào bộ điều khiển quản lý bo mạch chủ (Baseboard Management Controller - BMC) của các host. 
* Bạn đã triển khai dịch vụ cấp phát (provisioning service) trong cụm bằng cách tạo một CR loại Provisioning.

**Quy trình**

1. Tạo tệp cấu hình cho node bare-metal. Tùy thuộc vào việc bạn sử dụng cấu hình tĩnh (static configuration) hay máy chủ DHCP, hãy chọn một trong các tệp `bmh.yaml` mẫu dưới đây và điều chỉnh theo nhu cầu của bạn bằng cách thay thế các giá trị trong tệp YAML sao cho phù hợp với môi trường thực tế:
  * Để triển khai với cấu hình tĩnh, hãy tạo tệp `bmh.yaml` như sau:

  ```yaml
  ---
  apiVersion: v1
  kind: Secret
  metadata:
    name: openshift-worker-<num>-network-config-secret
    namespace: openshift-machine-api
  type: Opaque
  stringData:
    nmstate: |
      interfaces:
      - name: <nic1_name>
        type: ethernet
        state: up
        ipv4:
          address:
          - ip: <ip_address>
            prefix-length: 24
          enabled: true
      dns-resolver:
        config:
          server:
          - <dns_ip_address>
      routes:
        config:
        - destination: 0.0.0.0/0
          next-hop-address: <next_hop_ip_address>
          next-hop-interface: <next_hop_nic1_name>
  ---
  apiVersion: v1
  kind: Secret
  metadata:
    name: openshift-worker-<num>-bmc-secret
    namespace: openshift-machine-api
  type: Opaque
  data:
    username: <base64_of_uid>
    password: <base64_of_pwd>
  ---
  apiVersion: metal3.io/v1alpha1
  kind: BareMetalHost
  metadata:
    name: openshift-worker-<num>
    namespace: openshift-machine-api
  spec:
    online: true
    bootMACAddress: <nic1_mac_address>
    bmc:
      address: <protocol>://<bmc_url>
      credentialsName: openshift-worker-<num>-bmc-secret
      disableCertificateVerification: false
    customDeploy:
      method: install_coreos
    userData:
      name: worker-user-data-managed
      namespace: openshift-machine-api
    rootDeviceHints:
      deviceName: <root_device_hint>
    preprovisioningNetworkDataName: openshift-worker-<num>-network-config-secret
  ```

  Trong đó:

    * `metadata.name`: Chỉ định số thứ tự duy nhất cho nút tính toán (compute node). Hãy thay thế tất cả các vị trí có chứa `<num>` bằng một số thứ tự duy nhất cho các nút bare-metal (nút vật lý) tại các trường `name`, `credentialsName` và preprovisioningNetworkDataName.
    * `stringData.nmstate`: Chỉ định cú pháp YAML của NMState để cấu hình các giao diện (interface) của máy chủ. Để cấu hình giao diện mạng cho một nút mới được tạo, hãy chỉ định tên của đối tượng secret chứa cấu hình mạng đó. Hãy tuân theo cú pháp `nmstate` để định nghĩa cấu hình mạng cho nút của bạn. Xem phần "Chuẩn bị nút bare-metal" để biết chi tiết về cách cấu hình theo cú pháp NMState.
    * `stringData.nmstate.interfaces`: Chỉ định các giao diện mạng. Nếu bạn đã cấu hình giao diện mạng bằng `nmstate` và muốn vô hiệu hóa một giao diện, hãy đặt `state: up`, đồng thời thiết lập các địa chỉ IP với thuộc tính `enabled: false`. Trường này là tùy chọn.
    * `stringData.nmstate.interfaces[].name`: Chỉ định tên của bộ điều khiển giao diện mạng (NIC) đầu tiên trên nút bare-metal. Hãy thay thế `<nic1_name>` bằng tên của NIC đó.
    * `stringData.nmstate.interfaces[].ipv4.address[].ip`: Chỉ định địa chỉ IP cho NIC của nút bare-metal. Hãy thay thế `<ip_address>` bằng địa chỉ IP tương ứng.
    * `stringData.nmstate.dns-resolver.config.server[]`: Chỉ định địa chỉ IP của bộ phân giải DNS (DNS resolver) cho nút bare-metal. Hãy thay thế `<dns_ip_address>` bằng địa chỉ IP của máy chủ DNS.
    * `stringData.nmstate.routes.config[].next-hop-address`: Chỉ định địa chỉ IP của cổng kết nối bên ngoài (external gateway) cho nút bare-metal. Hãy thay thế `<next_hop_ip_address>` bằng địa chỉ IP của cổng kết nối này.
    * `stringData.nmstate.routes.config[].next-hop-interface`: Chỉ định tên của giao diện cổng kết nối bên ngoài trên nút bare-metal. Hãy thay thế `<next_hop_nic1_name>` bằng tên của giao diện đó.
    * `data.username`: Chỉ định thông tin xác thực (credentials) đã được mã hóa bằng base64 cho đối tượng BMC Secret. Thay thế `<base64_of_uid`> và `<base64_of_pwd>` bằng chuỗi mã hóa base64 của tên người dùng và mật khẩu.
    * `spec.bootMACAddress`: Chỉ định địa chỉ MAC của card mạng (NIC) đầu tiên trên nút phần cứng vật lý (bare-metal node). Xem phần "Đánh địa chỉ BMC" (BMC addressing) để biết thêm các tùy chọn cấu hình BMC khác.
    * `spec.bmc.address`: Chỉ định địa chỉ BMC. Thay thế `<protocol>` bằng giao thức BMC, chẳng hạn như IPMI, Redfish hoặc các giao thức khác. Thay thế `<bmc_url`> bằng URL của bộ điều khiển quản lý bo mạch chủ (Baseboard Management Controller - BMC) trên nút phần cứng vật lý.
    * `spec.rootDeviceHints.deviceName`: Chỉ định đường dẫn gợi ý thiết bị gốc (root device hint). Thay thế `<root_device_hint>` bằng đường dẫn thiết bị khi xác định gợi ý thiết bị gốc. Xem phần "Gợi ý thiết bị gốc" (Root device hints) để biết thêm chi tiết. Trường này là tùy chọn.

  * Khi định cấu hình giao diện mạng với thiết lập tĩnh bằng cách sử dụng nmstate, hãy đặt `state: up` và thiết lập các địa chỉ IP với tùy chọn `enabled: false`:

  ```yaml
  ---
  apiVersion: v1
  kind: Secret
  metadata:
    name: openshift-worker-<num>-network-config-secret
    namespace: openshift-machine-api
   # ...
  interfaces:
    - name: <nic_name>
      type: ethernet
      state: up
      ipv4:
        enabled: false
      ipv6:
        enabled: false
  # ...
  ```

  * Để triển khai với cấu hình DHCP, hãy tạo tệp bmh.yaml như sau:

  ```yaml
  ---
  apiVersion: v1
  kind: Secret
  metadata:
    name: openshift-worker-<num>-bmc-secret
    namespace: openshift-machine-api
  type: Opaque
  data:
    username: <base64_of_uid>
    password: <base64_of_pwd>
  ---
  apiVersion: metal3.io/v1alpha1
  kind: BareMetalHost
  metadata:
    name: openshift-worker-<num>
    namespace: openshift-machine-api
  spec:
    online: true
    bootMACAddress: <nic1_mac_address>
    bmc:
      address: <protocol>://<bmc_url>
      credentialsName: openshift-worker-<num>-bmc
      disableCertificateVerification: false
    customDeploy:
      method: install_coreos
    userData:
      name: worker-user-data-managed
      namespace: openshift-machine-api
    rootDeviceHints:
      deviceName: <root_device_hint>
  ```

  Trong đó:

    * `metadata.name`: Xác định tên của BMC Secret. Hãy thay thế `<num>` bằng một số định danh duy nhất cho mỗi nút bare-metal (máy chủ vật lý) trong các trường `name` và `credentialsName`. 
    * `data.username`: Chứa thông tin xác thực đã được mã hóa bằng `base64`. Hãy thay thế `<base64_of_uid>` và `<base64_of_pwd>` lần lượt bằng chuỗi `base64` của tên người dùng và mật khẩu. 
    * `spec.bootMACAddress`: Xác định địa chỉ MAC của card mạng (NIC) đầu tiên trên nút bare-metal. Xem phần "Đánh địa chỉ BMC" (BMC addressing) để biết thêm các tùy chọn cấu hình BMC khác. 
    * `spec.bmc.address`: Xác định địa chỉ BMC. Hãy thay thế `<protocol>` bằng giao thức BMC tương ứng (ví dụ: IPMI, Redfish, v.v.) và thay thế `<bmc_url>` bằng URL của bộ điều khiển quản lý bo mạch chủ (Baseboard Management Controller - BMC) trên nút bare-metal đó. 
    * `spec.rootDeviceHints.deviceName`: Xác định đường dẫn gợi ý thiết bị gốc (root device hint). Hãy thay thế `<root_device_hint>` bằng đường dẫn thiết bị cụ thể. Xem phần "Gợi ý thiết bị gốc" (Root device hints) để biết thêm chi tiết. Trường này là tùy chọn. 

  > **Quan trọng:** Nếu địa chỉ MAC của một nút bare-metal hiện có trùng với địa chỉ MAC của máy chủ bare-metal mà bạn đang cố gắng cấp phát (provision), quá trình cài đặt sẽ thất bại. Nếu các bước như đăng ký máy chủ (enrollment), kiểm tra (inspection), làm sạch (cleaning) hoặc các bước khác gặp lỗi, Bare Metal Operator sẽ liên tục thử lại quá trình cài đặt. Xem phần "Chẩn đoán lỗi trùng lặp địa chỉ MAC khi cấp phát máy chủ mới trong cụm" để biết thêm chi tiết.

2. Tạo nút bare-metal bằng cách chạy lệnh sau:

```shell
$ oc create -f bmh.yaml
```

Ví dụ đầu ra

```
secret/openshift-worker-<num>-network-config-secret created
secret/openshift-worker-<num>-bmc-secret created
baremetalhost.metal3.io/openshift-worker-<num> created
```

3. Kiểm tra nút bare-metal bằng cách chạy lệnh sau:

```shell
$ oc -n openshift-machine-api get bmh openshift-worker-<num>
```

Trong đó: `<num>` chỉ định số hiệu nút tính toán.

Ví dụ đầu ra

```
NAME                    STATE       CONSUMER   ONLINE   ERROR
openshift-worker-<num>  provisioned true
```

4. Phê duyệt tất cả các yêu cầu ký chứng chỉ (CSR). 
  * Lấy danh sách các CSR đang chờ xử lý bằng cách chạy lệnh sau:

  ```shell
  $ oc get csr
  ```

  Ví dụ đầu ra

  ```
  NAME        AGE   SIGNERNAME                                    REQUESTOR                                         REQUESTEDDURATION CONDITION
  csr-gfm9f   33s   kubernetes.io/kube-apiserver-client-kubelet   system:serviceaccount:openshift-machine-config-o
  perator:node-bootstrapper   <none>              Pending
  ```

  * Phê duyệt CSR bằng cách chạy lệnh sau:

  ```shell
  $ oc adm certificate approve <csr_name>
  ```

  Ví dụ đầu ra

  ```
  certificatesigningrequest.certificates.k8s.io/<csr_name> approved
  ```

**Xác minh**

* Hãy xác minh rằng nút (node) đã sẵn sàng bằng cách chạy lệnh sau:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME        STATUS   ROLES           AGE     VERSION
app1        Ready    worker          47s     v1.24.0+dc5a2fd
controller1 Ready    master,worker   2d22h   v1.24.0+dc5a2fd
```

#### 2.4.4. Tùy chọn: Quản lý các máy chủ hiện có trong cụm do người dùng cung cấp (user-provisioned cluster) bằng cách sử dụng BMO
Bạn có thể tùy chọn sử dụng Bare Metal Operator (BMO) để quản lý các máy chủ vật lý (bare-metal) hiện có trong một cụm do người dùng cung cấp, bằng cách tạo đối tượng `BareMetalHost` cho các máy chủ đó.

Việc quản lý các máy chủ do người dùng cung cấp không phải là yêu cầu bắt buộc; tuy nhiên, bạn có thể đăng ký chúng dưới dạng các máy chủ được cung cấp từ bên ngoài (externally-provisioned hosts) để phục vụ mục đích kiểm kê (inventory).

> **Quan trọng:** Để quản lý các máy chủ hiện có bằng BMO, bạn phải đặt thuộc tính `spec.externallyProvisioned` trong tài nguyên tùy chỉnh (custom resource) `BareMetalHost` thành `true`. Điều này giúp ngăn chặn BMO thực hiện cấp phát lại (re-provisioning) cho máy chủ đó.

**Điều kiện tiên quyết**

* Bạn đã tạo một cụm máy chủ vật lý (bare-metal cluster) do người dùng cung cấp. 
* Bạn có quyền truy cập vào bộ điều khiển quản lý bo mạch chủ (Baseboard Management Controller - BMC) của các máy chủ. 
* Bạn đã triển khai dịch vụ cấp phát (provisioning service) trong cụm bằng cách tạo một tài nguyên tùy chỉnh `Provisioning`.

**Quy trình**

1. Tạo tài nguyên tùy chỉnh `Secret` và `BareMetalHost`. 
  * Lưu nội dung YAML dưới đây vào tệp `controller.yaml`:

  ```yaml
  ---
  apiVersion: v1
  kind: Secret
  metadata:
    name: controller1-bmc
    namespace: openshift-machine-api
  type: Opaque
  data:
    username: <base64_of_uid>
    password: <base64_of_pwd>
  ---
  apiVersion: metal3.io/v1alpha1
  kind: BareMetalHost
  metadata:
    name: controller1
    namespace: openshift-machine-api
  spec:
    bmc:
      address: <protocol>://<bmc_url>
      credentialsName: "controller1-bmc"
    bootMACAddress: <nic1_mac_address>
    customDeploy:
      method: install_coreos
    externallyProvisioned: true
    online: true
    userData:
      name: controller-user-data-managed
      namespace: openshift-machine-api
  ```

  Trong đó:
    * `spec.bmc.address`: Chỉ định địa chỉ BMC. Chỉ các trình điều khiển máy chủ vật lý (bare-metal host) hỗ trợ khởi động qua mạng bằng phương tiện ảo (virtual media) mới được chấp nhận; ví dụ: redfish-virtualmedia và idrac-virtualmedia.
    * `spec.externallyProvisioned`: Xác định xem máy chủ có được cung cấp (provision) từ bên ngoài hay không. Bạn phải đặt giá trị này là `true` để ngăn BMO thực hiện cung cấp lại (re-provision) cho máy chủ vật lý đó.

2. Tạo đối tượng máy chủ bare-metal bằng cách chạy lệnh sau:

```shell
$ oc create -f controller.yaml
```

Ví dụ đầu ra

```
secret/controller1-bmc created
baremetalhost.metal3.io/controller1 created
```

**Xác minh**

Xác minh rằng BMO đã tạo đối tượng máy chủ vật lý (bare-metal host object) bằng cách chạy lệnh sau:

```shell
$ oc get bmh -A
```

Ví dụ đầu ra

```
NAMESPACE               NAME          STATE                    CONSUMER   ONLINE   ERROR   AGE
openshift-machine-api   controller1   externally provisioned              true             13s
```

#### 2.4.5. Loại bỏ các máy chủ khỏi cụm do người dùng cung cấp bằng cách sử dụng BMO
Bạn có thể sử dụng Bare Metal Operator (BMO) để loại bỏ các máy chủ vật lý (bare-metal host) khỏi một cụm do người dùng cung cấp.

**Điều kiện tiên quyết**

* Bạn đã tạo một cụm máy chủ vật lý do người dùng cung cấp. 
* Bạn có quyền truy cập vào bộ điều khiển quản lý bo mạch chủ (BMC) của các máy chủ đó. 
* Bạn đã triển khai dịch vụ cung cấp tài nguyên (provisioning service) trong cụm bằng cách tạo một đối tượng Provisioning CR.

**Quy trình**

1. Thực hiện chặn (cordon) và giải phóng (drain) nút bằng cách chạy lệnh sau:

```shell
$ oc adm drain app1 --force --ignore-daemonsets=true
```

Ví dụ đầu ra

```
node/app1 cordoned
WARNING: ignoring DaemonSet-managed Pods: openshift-cluster-node-tuning-operator/tuned-tvthg, openshift-dns/dns-
default-9q6rz, openshift-dns/node-resolver-zvt42, openshift-image-registry/node-ca-mzxth, openshift-ingress-cana
ry/ingress-canary-qq5lf, openshift-machine-config-operator/machine-config-daemon-v79dm, openshift-monitoring/nod
e-exporter-2vn59, openshift-multus/multus-additional-cni-plugins-wssvj, openshift-multus/multus-fn8tg, openshift
-multus/network-metrics-daemon-5qv55, openshift-network-diagnostics/network-check-target-jqxn2, openshift-ovn-ku
bernetes/ovnkube-node-rsvqg
evicting pod openshift-operator-lifecycle-manager/collect-profiles-27766965-258vp
evicting pod openshift-operator-lifecycle-manager/collect-profiles-27766950-kg5mk
evicting pod openshift-operator-lifecycle-manager/collect-profiles-27766935-stf4s
pod/collect-profiles-27766965-258vp evicted
pod/collect-profiles-27766950-kg5mk evicted
pod/collect-profiles-27766935-stf4s evicted
node/app1 drained
```

2. Xóa đặc tả `customDeploy` khỏi CR `BareMetalHost`. 
  * Chỉnh sửa CR `BareMetalHost` cho máy chủ bằng cách chạy lệnh sau:

  ```shell
  $ oc edit bmh -n openshift-machine-api <host_name>
  ```

  * Xóa các dòng `spec.customDeploy` và `spec.customDeploy.method`:

  ```yaml
  ...
    customDeploy:
      method: install_coreos
  ```

  * Hãy xác minh rằng trạng thái cung cấp (provisioning state) của máy chủ chuyển sang trạng thái hủy cung cấp (deprovisioning) bằng cách chạy lệnh sau:

  ```shell
  $ oc get bmh -A
  ```

  Ví dụ đầu ra

  ```
  NAMESPACE               NAME          STATE                    CONSUMER   ONLINE   ERROR   AGE
  openshift-machine-api   controller1   externally provisioned              true             58m
  openshift-machine-api   worker1       deprovisioning                      true             57m
  ```

3. Hãy xóa host bằng cách chạy lệnh sau khi trạng thái của `BareMetalHost` chuyển sang `available`:

```shell
$ oc delete bmh -n openshift-machine-api <bmh_name>
```

> **Lưu ý:** Bạn có thể thực hiện bước này mà không cần chỉnh sửa CR `BareMetalHost`. Có thể sẽ mất một khoảng thời gian để trạng thái của `BareMetalHost` chuyển từ `deprovisioning` (đang hủy cấp phát) sang `available` (sẵn sàng).

4. Xóa nút bằng cách chạy lệnh sau:

```shell
$ oc delete node <node_name>
```

**Xác minh**

* Hãy xác minh rằng bạn đã xóa nút bằng cách chạy lệnh sau:

```shell
$ oc get nodes
```

Ví dụ đầu ra

```
NAME          STATUS   ROLES           AGE     VERSION
controller1   Ready    master,worker   2d23h   v1.24.0+dc5a2fd
```
