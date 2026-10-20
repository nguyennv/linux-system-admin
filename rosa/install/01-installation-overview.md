Cài đặt OpenShift
-----------------

# Tổng quan về cài đặt
## I. Tổng quan về cài đặt OpenShift Container Platform
### I.1. Các tùy chọn cài đặt OpenShift Container Platform
Chương trình cài đặt OpenShift Container Platform cung cấp bốn phương thức để triển khai một cụm (cluster). Mỗi phương thức đều có những đặc điểm riêng biệt, cho phép bạn lựa chọn phương án phù hợp nhất với nhu cầu của mình.

Dưới đây là chi tiết về các phương thức này:

* **Interactive (Tương tác)**: Bạn có thể triển khai cụm thông qua công cụ Assisted Installer dựa trên nền tảng web. Đây là lựa chọn lý tưởng cho các cụm có kết nối internet. Assisted Installer là cách đơn giản nhất để cài đặt OpenShift Container Platform; công cụ này cung cấp các thiết lập mặc định thông minh và thực hiện kiểm tra xác thực trước khi tiến hành cài đặt. Ngoài ra, Assisted Installer còn cung cấp giao diện lập trình ứng dụng (RESTful API) phục vụ cho các kịch bản tự động hóa và cấu hình nâng cao. 
* **Local Agent-based (Dựa trên tác nhân cục bộ)**: Bạn có thể triển khai cụm tại chỗ (local) bằng công cụ Agent-based Installer, phù hợp cho các môi trường không kết nối internet hoặc các mạng bị hạn chế. Phương thức này mang lại nhiều lợi ích tương tự như Assisted Installer, nhưng yêu cầu bạn phải tải xuống và cấu hình công cụ trước khi sử dụng. Việc cấu hình được thực hiện thông qua giao diện dòng lệnh (CLI). Đây là giải pháp tối ưu cho các môi trường không có kết nối internet. 
    * Ngoài ra, bạn có thể triển khai cụm mà không cần đến registry bên ngoài bằng cách sử dụng phương tiện cài đặt độc lập (self-contained installation media). Phương thức này cũng cung cấp giao diện người dùng đơn giản, tương tự như Assisted Installer, khi thực hiện cài đặt tại cơ sở (on-premise). Để biết thêm thông tin, vui lòng xem phần "Cài đặt cụm mà không cần registry bên ngoài". 
* Tự động (Automated): Bạn có thể triển khai cụm trên cơ sở hạ tầng do chương trình cài đặt tự động cung cấp (installer-provisioned infrastructure). Chương trình cài đặt sẽ sử dụng bộ điều khiển quản lý bo mạch chủ (BMC) của từng máy chủ trong cụm để thực hiện việc cung cấp tài nguyên. Phương thức này có thể áp dụng cho cả môi trường có kết nối internet lẫn môi trường không kết nối internet. 
* Toàn quyền kiểm soát (Full control): Bạn có thể triển khai cụm trên cơ sở hạ tầng do chính mình chuẩn bị và duy trì, mang lại khả năng tùy chỉnh tối đa. Phương thức này cũng có thể áp dụng cho cả môi trường có kết nối internet lẫn môi trường không kết nối internet.

Mỗi phương thức đều triển khai cụm với các đặc điểm sau:

* Cơ sở hạ tầng có tính sẵn sàng cao, không tồn tại điểm lỗi duy nhất (single point of failure) – đây là thiết lập mặc định. 
* Người quản trị có toàn quyền kiểm soát việc áp dụng các bản cập nhật, bao gồm cả nội dung và thời điểm cập nhật.

#### I.1.1. Bảng thuật ngữ các thuật ngữ phổ biến trong quá trình cài đặt OpenShift Container Platform
Bảng thuật ngữ này định nghĩa các thuật ngữ phổ biến liên quan đến nội dung cài đặt. Hãy đọc danh sách các thuật ngữ dưới đây để hiểu rõ hơn về quy trình cài đặt.

**Assisted Installer (Trình cài đặt có hỗ trợ)**  
Một trình cài đặt được lưu trữ tại [console.redhat.com](https://console.redhat.com/openshift/assisted-installer/clusters/~new), cung cấp giao diện người dùng dựa trên web hoặc API RESTful để tạo cấu hình cụm (cluster). [Assisted Installer](https://docs.redhat.com/en/documentation/assisted_installer_for_openshift_container_platform/2026/html/installing_openshift_container_platform_with_the_assisted_installer/index) tạo ra một "hình ảnh khám phá" (discovery image). Các máy trong cụm sẽ khởi động bằng hình ảnh này; quá trình đó sẽ cài đặt RHCOS cùng một tác nhân (agent). Assisted Installer và tác nhân này phối hợp với nhau để thực hiện kiểm tra trước khi cài đặt và tiến hành cài đặt cho cụm.

**Agent-based Installer (Trình cài đặt dựa trên tác nhân)**  
Một trình cài đặt tương tự như Assisted Installer, nhưng bạn cần tải xuống trình cài đặt này trước khi sử dụng. [Agent-based Installer](https://console.redhat.com/openshift/install/metal/agent-based) là lựa chọn lý tưởng cho các môi trường không kết nối internet (disconnected environments).

**Bootstrap node (Nút khởi động)**  
Một máy tạm thời chạy cấu hình Kubernetes tối giản, cần thiết để triển khai mặt phẳng điều khiển (control plane) của OpenShift Container Platform.

**Control plane (Mặt phẳng điều khiển)**  
Lớp điều phối container cung cấp API và các giao diện để định nghĩa, triển khai và quản lý vòng đời của các container. Còn được gọi là các máy thuộc mặt phẳng điều khiển (control plane machines).

**Compute node (Nút tính toán)**  
Các nút chịu trách nhiệm thực thi khối lượng công việc (workload) cho người dùng trong cụm. Còn được gọi là các nút worker (worker nodes).

**Disconnected installation (Cài đặt trong môi trường không kết nối internet)**  
Trong một số trường hợp, các phần của trung tâm dữ liệu có thể không truy cập được internet, ngay cả thông qua máy chủ proxy. Bạn vẫn có thể cài đặt OpenShift Container Platform trong các môi trường này, nhưng cần tải xuống trước các phần mềm và hình ảnh (image) cần thiết, sau đó cung cấp chúng cho môi trường không kết nối internet đó.

**OpenShift Container Platform installation program (Chương trình cài đặt OpenShift Container Platform)**  
Một chương trình thực hiện cung cấp cơ sở hạ tầng và triển khai cụm.

**Installer-provisioned infrastructure (Cơ sở hạ tầng do trình cài đặt cung cấp)**  
Chương trình cài đặt sẽ triển khai và cấu hình cơ sở hạ tầng mà cụm chạy trên đó.

**Ignition config files (Các tệp cấu hình Ignition)**  
Các tệp được công cụ Ignition sử dụng để cấu hình Red Hat Enterprise Linux CoreOS (RHCOS) trong quá trình khởi tạo hệ điều hành. Chương trình cài đặt tạo ra các tệp cấu hình Ignition khác nhau để khởi tạo các nút bootstrap, nút thuộc mặt phẳng điều khiển (control plane) và các nút worker.

**Kubernetes manifests (Các tệp khai báo Kubernetes)**  
Các thông số kỹ thuật của một đối tượng API Kubernetes ở định dạng JSON hoặc YAML. Một tệp cấu hình có thể bao gồm các thành phần như deployment, config map, secret, daemonset, v.v.

**kubelet**  
Một tác nhân chính chạy trên mỗi nút trong cụm, đảm bảo rằng các container đang hoạt động bên trong một pod.

**Load balancer (Bộ cân bằng tải)**  
Bộ cân bằng tải đóng vai trò là điểm tiếp xúc duy nhất cho các client. Đối với API, các bộ cân bằng tải sẽ phân phối lưu lượng truy cập đến các node trong mặt phẳng điều khiển (control plane).

**Machine Config Operator**  
Một Operator có chức năng quản lý và áp dụng các cấu hình cũng như bản cập nhật cho hệ điều hành cơ sở và môi trường chạy container (container runtime) — bao gồm mọi thành phần nằm giữa nhân hệ điều hành (kernel) và kubelet — cho các node trong cụm (cluster).

**Operator**  
Phương thức ưu tiên để đóng gói, triển khai và quản lý ứng dụng Kubernetes trong cụm OpenShift Container Platform. Một Operator chuyển đổi kiến thức vận hành của con người thành phần mềm, giúp việc đóng gói và chia sẻ với khách hàng trở nên dễ dàng.

**Hạ tầng do người dùng cung cấp (User-provisioned infrastructure)**  
Bạn có thể cài đặt OpenShift Container Platform trên hạ tầng do chính bạn cung cấp. Bạn có thể sử dụng chương trình cài đặt để tạo các tài nguyên cần thiết cho việc thiết lập hạ tầng cụm, tạo hạ tầng đó, rồi triển khai cụm trên hạ tầng mà bạn đã cung cấp.

#### II.1.2. Quy trình cài đặt
Chương trình cài đặt OpenShift Container Platform chuyển đổi một tập hợp các tài nguyên thành một cụm (cluster) đang hoạt động, thông qua quy trình cài đặt khác nhau tùy thuộc vào phương thức cài đặt mà bạn chọn.

Ngoại trừ trường hợp sử dụng Assisted Installer (Trình cài đặt hỗ trợ), khi triển khai cụm OpenShift Container Platform, bạn cần tải xuống chương trình cài đặt từ trang tương ứng với loại cụm (Cluster Type) trên OpenShift Cluster Manager Hybrid Cloud Console. Bảng điều khiển này quản lý:

* REST API cho các tài khoản. 
* Các token đăng ký (registry tokens) – hay còn gọi là "pull secret" – được sử dụng để tải xuống các thành phần cần thiết. 
* Việc đăng ký cụm, giúp liên kết định danh của cụm với tài khoản Red Hat của bạn, từ đó hỗ trợ thu thập các số liệu về mức độ sử dụng.

Trong OpenShift Container Platform 4.22, chương trình cài đặt là một tệp nhị phân (binary file) được viết bằng ngôn ngữ Go, thực hiện hàng loạt thao tác chuyển đổi trên một tập hợp các tài nguyên. Cách thức tương tác với chương trình cài đặt sẽ khác nhau tùy theo loại hình cài đặt. Dưới đây là các trường hợp cài đặt cụ thể:

* Đối với Assisted Installer: Bạn cần cấu hình các thiết lập cụm thông qua chính công cụ này. Bạn không cần tải xuống hay cấu hình chương trình cài đặt riêng biệt. Sau khi hoàn tất cấu hình, bạn tải xuống một tệp ISO khám phá (discovery ISO) và khởi động các máy trong cụm bằng tệp ảnh (image) đó. Assisted Installer hỗ trợ triển khai cụm trên Nutanix, vSphere và hạ tầng vật lý (bare metal) với khả năng tích hợp đầy đủ, cũng như trên các nền tảng khác mà không cần tích hợp sâu. Nếu cài đặt trên hạ tầng vật lý, bạn phải tự chuẩn bị toàn bộ cơ sở hạ tầng và tài nguyên cho cụm, bao gồm hệ thống mạng, cân bằng tải, lưu trữ và các máy chủ thành phần. 
* Đối với Agent-based Installer (Trình cài đặt dựa trên tác nhân): Bạn tải xuống công cụ này, sau đó cấu hình cụm và tạo một tệp ảnh khám phá (discovery image). Khi khởi động các máy trong cụm bằng tệp ảnh này, một tác nhân (agent) sẽ được cài đặt; tác nhân này sẽ giao tiếp với chương trình cài đặt và tự động xử lý quá trình cung cấp tài nguyên (provisioning), giúp bạn không cần phải trực tiếp thao tác với chương trình cài đặt hay tự thiết lập một máy chuyên dụng để thực hiện việc cung cấp tài nguyên. Bạn vẫn cần chuẩn bị toàn bộ cơ sở hạ tầng và tài nguyên cho cụm, bao gồm hệ thống mạng, cân bằng tải, lưu trữ và các máy chủ thành phần. Phương pháp này đặc biệt phù hợp cho các môi trường không có kết nối internet (disconnected environments). 
* Đối với các cụm sử dụng hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure): Bạn ủy quyền việc khởi tạo (bootstrapping) và cung cấp cơ sở hạ tầng cho chương trình cài đặt, thay vì phải tự mình thực hiện các công việc đó. Chương trình cài đặt sẽ tạo ra tất cả các thành phần mạng, máy chủ và hệ điều hành cần thiết để hỗ trợ cụm (cluster), trừ trường hợp bạn thực hiện cài đặt trên nền tảng phần cứng vật lý (bare metal). Nếu cài đặt trên phần cứng vật lý, bạn phải tự cung cấp toàn bộ cơ sở hạ tầng và tài nguyên cho cụm, bao gồm máy khởi động (bootstrap machine), hệ thống mạng, cân bằng tải, lưu trữ và các máy chủ riêng lẻ trong cụm. 
* Nếu bạn tự thiết lập và quản lý cơ sở hạ tầng cho cụm của mình, bạn phải cung cấp toàn bộ cơ sở hạ tầng và tài nguyên cho cụm đó, bao gồm máy khởi động, hệ thống mạng, cân bằng tải, lưu trữ và các máy chủ riêng lẻ trong cụm.

Trong quá trình cài đặt, chương trình cài đặt sử dụng ba nhóm tệp tin: tệp cấu hình cài đặt có tên là `install-config.yaml`, các tệp định nghĩa (manifests) của Kubernetes và các tệp cấu hình Ignition dành cho từng loại máy chủ của bạn.

> **Quan trọng:** Bạn có thể sửa đổi các tệp cấu hình Kubernetes và Ignition – vốn kiểm soát hệ điều hành RHCOS bên dưới – trong quá trình cài đặt. Tuy nhiên, không có cơ chế kiểm chứng nào để xác nhận tính phù hợp của các sửa đổi mà bạn thực hiện đối với những thành phần này. Nếu sửa đổi chúng, bạn có thể khiến cụm (cluster) của mình không còn hoạt động được. Do rủi ro này, việc sửa đổi các tệp cấu hình Kubernetes và Ignition sẽ không được hỗ trợ, trừ khi bạn thực hiện theo các quy trình đã được ghi lại trong tài liệu hoặc làm theo hướng dẫn từ bộ phận hỗ trợ của Red Hat.

Tệp cấu hình cài đặt được chuyển đổi thành các tệp kê khai (manifest) Kubernetes, sau đó các tệp kê khai này được đóng gói vào các tệp cấu hình Ignition. Chương trình cài đặt sử dụng các tệp cấu hình Ignition này để tạo cụm (cluster).

Tất cả các tệp cấu hình cài đặt sẽ bị loại bỏ khi bạn chạy chương trình cài đặt; vì vậy, hãy đảm bảo bạn đã sao lưu mọi tệp cấu hình mà mình muốn sử dụng lại.

> **Quan trọng:** Bạn không thể sửa đổi các tham số đã thiết lập trong quá trình cài đặt, nhưng bạn có thể thay đổi nhiều thuộc tính của cụm sau khi quá trình cài đặt hoàn tất.

##### Quy trình cài đặt với Assisted Installer
Việc cài đặt bằng Assisted Installer bao gồm quá trình tạo cấu hình cụm (cluster) theo cách tương tác thông qua giao diện người dùng trên nền tảng web hoặc RESTful API. Giao diện người dùng của Assisted Installer sẽ yêu cầu bạn nhập các giá trị cần thiết, đồng thời cung cấp các giá trị mặc định hợp lý cho những tham số còn lại (trừ khi bạn thay đổi chúng thông qua giao diện hoặc API). Assisted Installer sẽ tạo ra một "hình ảnh khám phá" (discovery image); bạn cần tải xuống hình ảnh này để khởi động các máy trong cụm. Hình ảnh đó sẽ cài đặt RHCOS cùng một tác nhân (agent), và tác nhân này sẽ đảm nhận việc cung cấp tài nguyên (provisioning) cho bạn. Bạn có thể cài đặt OpenShift Container Platform bằng Assisted Installer với khả năng tích hợp đầy đủ trên các nền tảng Nutanix, vSphere và hạ tầng vật lý (bare metal). Ngoài ra, bạn cũng có thể sử dụng Assisted Installer để cài đặt OpenShift Container Platform trên các nền tảng khác mà không cần tích hợp. 

OpenShift Container Platform quản lý mọi khía cạnh của cụm, bao gồm cả chính hệ điều hành. Mỗi máy khi khởi động đều sử dụng một cấu hình tham chiếu đến các tài nguyên được lưu trữ trong cụm mà nó tham gia. Cấu hình này cho phép cụm tự quản lý trong suốt quá trình áp dụng các bản cập nhật. 

Nếu có thể, hãy sử dụng tính năng Assisted Installer để tránh việc phải tải xuống và cấu hình Agent-based Installer.

##### Quy trình cài đặt với cơ sở hạ tầng dựa trên tác nhân (Agent-based infrastructure)
Cài đặt dựa trên tác nhân (Agent-based installation) tương tự như sử dụng trình cài đặt hỗ trợ (Assisted Installer), điểm khác biệt là bạn cần tải xuống và cài đặt trình cài đặt dựa trên tác nhân (Agent-based Installer) trước. Phương pháp này hữu ích khi bạn muốn tận dụng sự tiện lợi của trình cài đặt hỗ trợ, nhưng lại cần triển khai cụm (cluster) trong một môi trường không kết nối mạng (disconnected environment). 

Nếu có thể, hãy sử dụng tính năng cài đặt dựa trên tác nhân để tránh việc phải tạo một máy cung cấp tài nguyên (provisioner machine) kèm theo máy ảo khởi động (bootstrap VM), cũng như không cần phải thực hiện các công việc thiết lập và duy trì cơ sở hạ tầng cho cụm.

##### Quy trình cài đặt với cơ sở hạ tầng do trình cài đặt cung cấp
Kiểu cài đặt mặc định sử dụng cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure). Theo mặc định, chương trình cài đặt hoạt động như một trình hướng dẫn cài đặt, yêu cầu bạn cung cấp các giá trị mà nó không thể tự xác định, đồng thời đưa ra các giá trị mặc định hợp lý cho các tham số còn lại. Bạn cũng có thể tùy chỉnh quy trình cài đặt để hỗ trợ các kịch bản cơ sở hạ tầng nâng cao. Chương trình cài đặt sẽ thực hiện cung cấp (provision) cơ sở hạ tầng nền tảng cho cụm (cluster). 

Bạn có thể cài đặt một cụm tiêu chuẩn hoặc một cụm tùy chỉnh. Với cụm tiêu chuẩn, bạn chỉ cần cung cấp các thông tin tối thiểu cần thiết để cài đặt cụm. Với cụm tùy chỉnh, bạn có thể chỉ định chi tiết hơn về nền tảng, chẳng hạn như số lượng máy được sử dụng cho mặt phẳng điều khiển (control plane), loại máy ảo mà cụm triển khai, hoặc dải CIDR cho mạng dịch vụ Kubernetes. 

Nếu có thể, hãy sử dụng tính năng này để tránh việc phải tự cung cấp và duy trì cơ sở hạ tầng cho cụm. Trong các môi trường khác, bạn sẽ sử dụng chương trình cài đặt để tạo ra các tài nguyên cần thiết cho việc thiết lập cơ sở hạ tầng cụm của mình. 

Đối với các cụm sử dụng cơ sở hạ tầng do trình cài đặt cung cấp, OpenShift Container Platform sẽ quản lý mọi khía cạnh của cụm, bao gồm cả hệ điều hành. Mỗi máy khi khởi động đều sử dụng một cấu hình tham chiếu đến các tài nguyên được lưu trữ ngay trong cụm mà nó tham gia. Cấu hình này cho phép cụm tự quản lý trong quá trình áp dụng các bản cập nhật.

##### Quy trình cài đặt với cơ sở hạ tầng do người dùng cung cấp
Bạn cũng có thể cài đặt OpenShift Container Platform trên cơ sở hạ tầng do chính bạn cung cấp. Bạn sẽ sử dụng chương trình cài đặt để tạo ra các thành phần cần thiết cho việc thiết lập cơ sở hạ tầng cụm, tạo cơ sở hạ tầng đó, rồi triển khai cụm trên cơ sở hạ tầng mà bạn đã cung cấp. 

Nếu không sử dụng cơ sở hạ tầng do chương trình cài đặt thiết lập, bạn phải tự quản lý và duy trì các tài nguyên của cụm. Danh sách dưới đây nêu chi tiết một số tài nguyên mà bạn cần tự quản lý:

* Cơ sở hạ tầng nền tảng cho mặt phẳng điều khiển (control plane) và các máy tính toán (compute machines) tạo nên cụm
* Các bộ cân bằng tải (load balancer)
* Hệ thống mạng của cụm, bao gồm các bản ghi DNS và các mạng con (subnet) cần thiết
* Lưu trữ cho cơ sở hạ tầng cụm và các ứng dụng
* Nếu cụm của bạn sử dụng cơ sở hạ tầng do người dùng cung cấp, bạn có tùy chọn bổ sung các máy tính toán chạy hệ điều hành RHEL vào cụm của mình.

#### I.1.3. Xác minh trạng thái nút sau khi cài đặt
Quá trình cài đặt OpenShift Container Platform hoàn tất khi các bước kiểm tra tình trạng sau đây đạt kết quả thành công:

Người thực hiện cài đặt có thể truy cập vào bảng điều khiển web (web console) của OpenShift Container Platform.
Tất cả các nút trong mặt phẳng điều khiển (control plane nodes) đều ở trạng thái sẵn sàng. 
Tất cả các thành phần vận hành cụm (cluster Operators) đều ở trạng thái khả dụng.

> **Lưu ý:** Sau khi quá trình cài đặt hoàn tất, các Operator cụm (cluster Operator) chịu trách nhiệm về các nút worker (worker node) sẽ liên tục cố gắng cung cấp tài nguyên cho tất cả các nút worker đó. Cần một khoảng thời gian nhất định để tất cả các nút worker chuyển sang trạng thái SẴN SÀNG (READY). Đối với các hệ thống cài đặt trên phần cứng vật lý (bare metal), hãy đợi ít nhất 60 phút trước khi tiến hành khắc phục sự cố cho một nút worker. Đối với các hệ thống cài đặt trên các nền tảng khác, hãy đợi ít nhất 40 phút trước khi tiến hành khắc phục sự cố cho một nút worker. Trạng thái SUY GIẢM (DEGRADED) của các Operator cụm chịu trách nhiệm về các nút worker phụ thuộc vào chính các tài nguyên của các Operator đó, chứ không phụ thuộc vào trạng thái của các nút.

Sau khi quá trình cài đặt hoàn tất, bạn có thể tiếp tục theo dõi tình trạng các nút trong cụm của mình.

##### Các điều kiện tiên quyết
Chương trình cài đặt chạy thành công trong cửa sổ dòng lệnh (terminal).

##### Quy trình
1. Hiển thị trạng thái của tất cả các nút worker:

```console
oc get nodes
```

Ví dụ đầu ra

```
NAME                           STATUS   ROLES    AGE   VERSION
example-compute1.example.com   Ready    worker   13m   v1.21.6+bb8d50a
example-compute2.example.com   Ready    worker   13m   v1.21.6+bb8d50a
example-compute4.example.com   Ready    worker   14m   v1.21.6+bb8d50a
example-control1.example.com   Ready    master   52m   v1.21.6+bb8d50a
example-control2.example.com   Ready    master   55m   v1.21.6+bb8d50a
example-control3.example.com   Ready    master   55m   v1.21.6+bb8d50a
```

2. Hiển thị trạng thái của tất cả các nút máy worker:

```console
oc get machines -A
```

Ví dụ đầu ra

```
NAMESPACE               NAME                           PHASE         TYPE   REGION   ZONE   AGE
openshift-machine-api   example-zbbt6-master-0         Running                              95m
openshift-machine-api   example-zbbt6-master-1         Running                              95m
openshift-machine-api   example-zbbt6-master-2         Running                              95m
openshift-machine-api   example-zbbt6-worker-0-25bhp   Running                              49m
openshift-machine-api   example-zbbt6-worker-0-8b4c2   Running                              49m
openshift-machine-api   example-zbbt6-worker-0-jkbqt   Running                              49m
openshift-machine-api   example-zbbt6-worker-0-qrl5b   Running                              49m
```

#### I.1.4. Phạm vi cài đặt
Phạm vi của chương trình cài đặt OpenShift Container Platform được cố tình giới hạn ở mức hẹp. Nó được thiết kế để đảm bảo sự đơn giản và khả năng thành công cao. Bạn có thể thực hiện thêm nhiều tác vụ cấu hình khác sau khi quá trình cài đặt hoàn tất.

#### I.1.5. Tổng quan về OpenShift Local
OpenShift Local hỗ trợ phát triển ứng dụng nhanh chóng, giúp người dùng bắt đầu xây dựng các cụm (cluster) OpenShift Container Platform.

OpenShift Local được thiết kế để chạy trên máy tính cá nhân, giúp đơn giản hóa quá trình thiết lập và kiểm thử, đồng thời mô phỏng môi trường phát triển trên nền tảng đám mây ngay tại máy tính của bạn, với đầy đủ các công cụ cần thiết để phát triển các ứng dụng dựa trên container.

Bất kể bạn sử dụng ngôn ngữ lập trình nào, OpenShift Local đều có thể lưu trữ ứng dụng của bạn và cung cấp một cụm Red Hat OpenShift Container Platform tối giản, được cấu hình sẵn trên máy tính cá nhân mà không cần đến cơ sở hạ tầng dựa trên máy chủ (server-based infrastructure).

Trong môi trường này, OpenShift Local cho phép tạo các dịch vụ siêu nhỏ (microservices), chuyển đổi chúng thành các image (hình ảnh ứng dụng) và chạy chúng trong các container trên nền tảng Kubernetes, ngay trên máy tính xách tay hoặc máy tính để bàn của bạn (hỗ trợ các hệ điều hành Linux, macOS, hoặc Windows 10 trở lên).

### I.2. Các nền tảng được hỗ trợ cho các cụm OpenShift Container Platform
Hãy xem qua ma trận hỗ trợ nền tảng để chọn phương thức cài đặt đáp ứng các yêu cầu của bạn.

**Bảng 1.1. Các nền tảng được hỗ trợ**

| Nền tảng                               | Hạ tầng do trình cài đặt cung cấp | Hạ tầng do người dùng cung cấp | Trình cài đặt dựa trên tác nhân | Trình cài đặt có hỗ trợ |
|----------------------------------------|-----------------------------------|--------------------------------|---------------------------------|-------------------------|
| Amazon Web Services (AWS)              | X                                 | X                              |                                 |                         |
| Bare metal                             | X                                 | X                              | X                               | X                       |
| External                               |                                   |                                | X                               | X                       |
| Google Cloud                           | X                                 | X                              |                                 |                         |
| IBM Cloud® Classic                     | X                                 |                                |                                 |                         |
| IBM Cloud® Virtual Private Cloud (VPC) | X                                 |                                |                                 |                         |
| IBM Power®                             |                                   | X                              | X                               | X                       |
| IBM Z® or IBM® LinuxONE                |                                   | X                              | X                               | X                       |
| Microsoft Azure                        | X                                 | X                              |                                 |                         |
| Microsoft Azure Stack Hub              | X                                 | X                              |                                 |                         |
| None                                   |                                   |                                | X                               | X                       |
| Nutanix                                | X                                 |                                |                                 | X                       |
| Oracle Cloud Infrastructure (OCI)      |                                   |                                | X                               | X                       |
| Red Hat OpenStack Platform (RHOSP)     | X                                 | X                              |                                 |                         |
| VMware vSphere                         | X                                 | X                              | X                               | X                       |

Danh sách dưới đây mô tả ba lộ trình triển khai khác nhau cùng các điều kiện tiên quyết tương ứng:

* Đối với cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure): Tất cả các máy, bao gồm cả máy tính mà bạn sử dụng để chạy quy trình cài đặt, đều phải có quyền truy cập internet trực tiếp để tải xuống các hình ảnh (image) cho các container nền tảng và gửi dữ liệu đo từ xa (telemetry data) tới Red Hat.

> **Quan trọng:** Sau khi cài đặt, các thay đổi sau đây không được hỗ trợ:
> 
> * Kết hợp các nền tảng nhà cung cấp dịch vụ đám mây khác nhau. 
> * Kết hợp các thành phần từ những nhà cung cấp dịch vụ đám mây khác nhau. Ví dụ: sử dụng khung lưu trữ bền vững (persistent storage framework) từ một nền tảng khác trên nền tảng mà bạn đã cài đặt cụm (cluster).

* Đối với cơ sở hạ tầng do người dùng cung cấp: Tùy thuộc vào các trường hợp được nền tảng hỗ trợ, bạn có thể thực hiện cài đặt trên cơ sở hạ tầng do người dùng cung cấp. Điều này cho phép bạn vận hành các máy với quyền truy cập internet đầy đủ, đặt cụm (cluster) của mình phía sau proxy hoặc thực hiện cài đặt trong môi trường không kết nối internet (disconnected installation).  
Trong quy trình cài đặt không kết nối internet, bạn có thể tải xuống các image cần thiết để cài đặt cụm, lưu trữ chúng trong một registry phản chiếu (mirror registry), rồi sử dụng dữ liệu đó để tiến hành cài đặt. Mặc dù vẫn cần truy cập internet để tải các image cho các container của nền tảng, nhưng với phương thức cài đặt không kết nối internet trên vSphere hoặc cơ sở hạ tầng vật lý (bare-metal), các máy trong cụm của bạn sẽ không cần phải truy cập trực tiếp vào internet.
* Đối với Red Hat OpenStack Platform (RHOSP): Phiên bản OpenShift Container Platform mới nhất hỗ trợ cả phiên bản RHOSP có vòng đời dài (long-life release) mới nhất lẫn các phiên bản trung gian (intermediate release). Để biết thông tin đầy đủ về khả năng tương thích với các phiên bản RHOSP, vui lòng tham khảo "Ma trận hỗ trợ OpenShift Container Platform trên RHOSP" (OpenShift Container Platform on RHOSP support matrix). Ngoài ra, hãy xem mục "Các tích hợp đã được kiểm thử trên OpenShift Container Platform 4.x" (OpenShift Container Platform 4.x Tested Integrations) để biết chi tiết về quá trình kiểm thử tích hợp trên các nền tảng khác nhau.

## II. Lựa chọn phương thức cài đặt cụm (cluster) và chuẩn bị cho người dùng
Trước khi cài đặt OpenShift Container Platform, hãy quyết định quy trình cài đặt nào sẽ được áp dụng và xác minh rằng bạn đã có đầy đủ các tài nguyên cần thiết để chuẩn bị cụm (cluster) cho người dùng.

### II.1. Chọn loại hình cài đặt cụm
Hãy quyết định quy trình cài đặt nào phù hợp nhất dựa trên cơ sở hạ tầng, kinh nghiệm và các yêu cầu bảo mật của bạn.

Trước khi cài đặt một cụm OpenShift Container Platform, bạn cần chọn hướng dẫn cài đặt tối ưu nhất. Hãy cân nhắc các câu trả lời cho những câu hỏi dưới đây để đưa ra lựa chọn phù hợp nhất.

#### II.1.1. Bạn có muốn tự mình cài đặt và quản lý cụm OpenShift Container Platform không?
Nếu muốn tự cài đặt và quản lý OpenShift Container Platform, bạn có thể thực hiện trên các nền tảng sau:

* Amazon Web Services (AWS) trên các instance x86 64-bit
* Amazon Web Services (AWS) trên các instance ARM 64-bit
* Microsoft Azure trên các instance x86 64-bit
* Microsoft Azure trên các instance ARM 64-bit
* Microsoft Azure Stack Hub
* Google Cloud trên các instance x86 64-bit
* Google Cloud trên các instance ARM 64-bit
* Red Hat OpenStack Platform (RHOSP)
* IBM Cloud®
* IBM Z® hoặc IBM® LinuxONE với z/VM
* IBM Z® hoặc IBM® LinuxONE với Red Hat Enterprise Linux (RHEL) KVM
* IBM Z® hoặc IBM® LinuxONE trong LPAR
* IBM Power®
* IBM Power® Virtual Server
* Nutanix
* VMware vSphere
* Hệ thống phần cứng vật lý (bare metal) hoặc các cơ sở hạ tầng độc lập với nền tảng khác

Bạn có thể triển khai cụm OpenShift Container Platform 4 trên cả phần cứng tại chỗ (on-premise) lẫn các dịch vụ lưu trữ trên nền tảng đám mây (cloud hosting); tuy nhiên, tất cả các máy trong cùng một cụm phải nằm trong cùng một trung tâm dữ liệu hoặc dịch vụ lưu trữ đám mây.

Nếu muốn sử dụng OpenShift Container Platform nhưng không muốn tự quản lý cụm, bạn có thể chọn một trong các tùy chọn dịch vụ được quản lý (managed service). Nếu muốn một cụm được quản lý hoàn toàn bởi Red Hat, bạn có thể sử dụng OpenShift Dedicated. Bạn cũng có thể sử dụng OpenShift dưới dạng dịch vụ được quản lý trên Azure, AWS, IBM Cloud® hoặc Google Cloud. Để biết thêm thông tin về các dịch vụ được quản lý, vui lòng xem trang Sản phẩm OpenShift (OpenShift Products). Nếu bạn cài đặt cụm OpenShift Container Platform bằng máy ảo trên nền tảng đám mây theo mô hình "bare metal ảo" (virtual bare metal), thì các giải pháp lưu trữ dựa trên nền tảng đám mây tương ứng sẽ không được hỗ trợ.

#### II.1.2. Bạn đã từng sử dụng OpenShift Container Platform 3 và muốn chuyển sang sử dụng OpenShift Container Platform 4?
Nếu bạn đã từng sử dụng OpenShift Container Platform 3 và muốn trải nghiệm OpenShift Container Platform 4, bạn cần hiểu rõ những điểm khác biệt của phiên bản này. OpenShift Container Platform 4 kết hợp một cách liền mạch các Operator — vốn dùng để đóng gói, triển khai và quản lý các ứng dụng Kubernetes — với hệ điều hành mà nền tảng này vận hành trên đó, chính là Red Hat Enterprise Linux CoreOS (RHCOS). Thay vì phải triển khai các máy chủ rồi cấu hình hệ điều hành trên đó để cài đặt OpenShift Container Platform, thì giờ đây, hệ điều hành RHCOS đã trở thành một phần không thể tách rời của cụm (cluster) OpenShift Container Platform. Việc triển khai hệ điều hành cho các máy chủ trong cụm chính là một phần của quy trình cài đặt OpenShift Container Platform. Hãy tham khảo mục [Sự khác biệt giữa OpenShift Container Platform 3 và 4](https://docs.redhat.com/en/documentation/openshift_container_platform/4.22/html-single/migrating_from_version_3_to_4/#migration-comparing-ocp-3-4).

Do bạn cần cung cấp (provision) các máy chủ như một phần của quy trình cài đặt cụm OpenShift Container Platform, nên bạn không thể nâng cấp trực tiếp một cụm OpenShift Container Platform 3 lên phiên bản 4. Thay vào đó, bạn phải tạo một cụm OpenShift Container Platform 4 mới, rồi di chuyển các khối lượng công việc (workload) từ cụm OpenShift Container Platform 3 cũ sang cụm mới này. Để biết thêm thông tin về quá trình di chuyển, hãy xem phần [Tổng quan về việc di chuyển từ OpenShift Container Platform 3 sang 4](https://docs.redhat.com/en/documentation/openshift_container_platform/4.22/html-single/migrating_from_version_3_to_4/#migration-from-version-3-to-4-overview). Vì bắt buộc phải thực hiện di chuyển sang OpenShift Container Platform 4, bạn có thể sử dụng bất kỳ quy trình cài đặt cụm tiêu chuẩn nào dành cho môi trường sản xuất (production) để tạo cụm mới của mình.

#### II.1.3. Bạn có muốn sử dụng các thành phần hiện có trong cụm (cluster) của mình không?
Vì hệ điều hành là một phần không thể thiếu của OpenShift Container Platform, nên cách đơn giản nhất là để chương trình cài đặt OpenShift Container Platform tự thiết lập toàn bộ cơ sở hạ tầng. Phương thức này được gọi là "cài đặt với cơ sở hạ tầng do trình cài đặt cung cấp" (installer-provisioned infrastructure). Trong kiểu cài đặt này, dù bạn có thể cung cấp một số thành phần cơ sở hạ tầng sẵn có cho cụm, thì chương trình cài đặt vẫn sẽ triển khai tất cả các máy (nodes) cần thiết cho cụm ngay từ giai đoạn đầu.

Bạn có thể triển khai một cụm theo phương thức này trên AWS, Azure, Azure Stack Hub, Google Cloud hoặc Nutanix mà không cần chỉ định bất kỳ tùy chỉnh nào cho cụm hoặc các máy thành phần bên dưới.

Nếu cần thực hiện các cấu hình cơ bản cho cụm (chẳng hạn như chọn loại máy ảo/instance cho các máy trong cụm), bạn có thể tùy chỉnh quá trình cài đặt trên AWS, Azure, Google Cloud hoặc Nutanix.

Đối với các cụm sử dụng cơ sở hạ tầng do trình cài đặt cung cấp, bạn có thể tận dụng VPC hiện có trên AWS, vNet trên Azure hoặc VPC trên Google Cloud. Bạn cũng có thể tái sử dụng một phần cơ sở hạ tầng mạng sẵn có, giúp cụm trên AWS, Azure hoặc Google Cloud tương thích với các dải địa chỉ IP hiện tại trong môi trường của bạn, đồng thời tích hợp được với các cấu hình MTU và VXLAN đang được sử dụng. Nếu đã có sẵn tài khoản và thông tin xác thực trên các nền tảng đám mây này, bạn có thể tiếp tục sử dụng chúng; tuy nhiên, có thể cần điều chỉnh lại tài khoản để đảm bảo có đủ các quyền hạn cần thiết cho việc cài đặt các cụm OpenShift Container Platform.

Bạn cũng có thể sử dụng phương thức này để tạo các máy ảo (machine instances) trên phần cứng của mình đối với các nền tảng như vSphere hoặc môi trường máy vật lý (bare metal). Ngoài ra, đối với vSphere, bạn còn có thể tùy chỉnh thêm các tham số mạng trong quá trình cài đặt.

Đối với một số trường hợp sử dụng cơ sở hạ tầng do trình cài đặt cung cấp—ví dụ như trên nền tảng VMware vSphere hoặc môi trường máy vật lý—lưu lượng truy cập bên ngoài đi vào địa chỉ IP ảo (VIP) của Ingress sẽ không được cân bằng tải giữa các bản sao (replicas) mặc định của IngressController. Trong các trường hợp này, nếu dự kiến lưu lượng truy cập sẽ vượt quá hiệu năng xử lý cơ bản của bộ định tuyến IngressController, bạn bắt buộc phải cấu hình một bộ cân bằng tải (load balancer) bên ngoài. Việc cấu hình bộ cân bằng tải bên ngoài sẽ giúp đạt được hiệu năng tương ứng với nhiều bản sao IngressController cùng hoạt động. Để biết thêm thông tin về hiệu năng cơ sở của IngressController, hãy xem phần Hiệu năng cơ sở của Ingress Controller (bộ định tuyến). Để biết thêm thông tin về cách cấu hình bộ cân bằng tải (load balancer) bên ngoài, hãy xem phần Cấu hình bộ cân bằng tải do người dùng quản lý.

Nếu muốn tận dụng cơ sở hạ tầng đám mây hiện có, bạn có thể thực hiện cài đặt trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure). Với phương thức này, bạn sẽ tự triển khai các máy chủ cần thiết cho cụm (cluster) trong quá trình cài đặt. Nếu thực hiện cài đặt trên cơ sở hạ tầng do người dùng cung cấp trên AWS, Azure hoặc Azure Stack Hub, bạn có thể sử dụng các mẫu (template) được cung cấp để hỗ trợ thiết lập tất cả các thành phần cần thiết. Bạn cũng có thể sử dụng lại VPC dùng chung trên Google Cloud. Ngoài ra, bạn có thể sử dụng phương thức cài đặt không phụ thuộc vào nhà cung cấp (provider-agnostic) để triển khai cụm trên các nền tảng đám mây khác.

Bạn cũng có thể thực hiện cài đặt trên cơ sở hạ tầng do người dùng cung cấp ngay trên phần cứng hiện có của mình. Nếu sử dụng RHOSP, IBM Z® hoặc IBM® LinuxONE (bao gồm cả cấu hình với RHEL KVM hoặc trong LPAR), IBM Power, hoặc vSphere, hãy tuân theo các hướng dẫn cài đặt dành riêng cho từng nền tảng để triển khai cụm. Nếu sử dụng các loại phần cứng khác được hỗ trợ, hãy thực hiện theo quy trình cài đặt trên phần cứng vật lý (bare metal). Đối với một số nền tảng như vSphere và phần cứng vật lý, bạn còn có thể tùy chỉnh thêm các tham số mạng trong quá trình cài đặt.

#### II.1.4. Bạn có cần tăng cường bảo mật cho cụm (cluster) của mình không?
Nếu sử dụng phương pháp cài đặt do người dùng cung cấp (user-provisioned installation), bạn có thể định cấu hình proxy cho cụm của mình. Các hướng dẫn chi tiết đã được bao gồm trong từng quy trình cài đặt.

Nếu muốn ngăn chặn việc các điểm cuối (endpoint) của cụm trên nền tảng đám mây công cộng (public cloud) bị lộ ra bên ngoài, bạn có thể triển khai một cụm riêng tư (private cluster) bằng cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure) trên AWS, Azure hoặc Google Cloud.

Nếu cần cài đặt cụm trong môi trường bị hạn chế truy cập internet (ví dụ: mạng bị ngắt kết nối hoặc mạng bị hạn chế), bạn có thể [sao chép (mirror) các gói cài đặt](disconnected-environments.md) và thực hiện cài đặt từ các gói đó. Hãy làm theo hướng dẫn chi tiết dành cho việc cài đặt trên cơ sở hạ tầng do người dùng cung cấp trong các mạng bị hạn chế đối với các nền tảng: AWS, Google Cloud, IBM Z® hoặc IBM® LinuxONE, IBM Z® hoặc IBM® LinuxONE với RHEL KVM, IBM Z® hoặc IBM® LinuxONE trong LPAR, IBM Power®, vSphere hoặc máy chủ vật lý (bare metal). Bạn cũng có thể cài đặt cụm trong mạng bị hạn chế bằng cơ sở hạ tầng do trình cài đặt cung cấp, thông qua các hướng dẫn chi tiết dành cho AWS, Google Cloud, IBM Cloud®, Nutanix, RHOSP và vSphere.

Nếu cần triển khai cụm tại các khu vực như AWS GovCloud, AWS China hoặc Azure Government, bạn có thể định cấu hình các khu vực tùy chỉnh này trong quá trình cài đặt bằng cơ sở hạ tầng do trình cài đặt cung cấp.

Ngoài ra, trong quá trình cài đặt, bạn cũng có thể định cấu hình các máy trong cụm để sử dụng các thư viện mật mã RHEL đã được gửi tới NIST để thẩm định theo tiêu chuẩn FIPS 140-2/140-3.

> **Quan trọng:** Khi chạy Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã RHEL đã được gửi tới NIST để thẩm định theo tiêu chuẩn FIPS 140-2/140-3; lưu ý rằng điều này chỉ áp dụng trên các kiến trúc x86_64, ppc64le và s390x.

### II.2. Chuẩn bị cụm (cluster) cho người dùng sau khi cài đặt
Cấu hình cụm trong môi trường thực tế (production) để người dùng có thể truy cập.

Một số cấu hình không bắt buộc phải có để cài đặt cụm, nhưng được khuyến nghị thực hiện trước khi người dùng bắt đầu truy cập. Bạn có thể tùy chỉnh chính cụm đó thông qua việc điều chỉnh các Operator tạo nên cụm, đồng thời tích hợp cụm với các hệ thống cần thiết khác, chẳng hạn như nhà cung cấp danh tính (identity provider).

Đối với một cụm trong môi trường thực tế, bạn cần cấu hình các thành phần tích hợp sau:

* Lưu trữ bền vững (persistent storage)
* Trinh cung cấp danh tính (identity provider)
* Giám sát các thành phần cốt lõi của OpenShift Container Platform

### II.3. Chuẩn bị cụm (cluster) cho các khối lượng công việc (workload)
Tùy thuộc vào nhu cầu của các khối lượng công việc, bạn có thể cần thực hiện thêm một số bước chuẩn bị cho cụm trước khi triển khai ứng dụng.

Chẳng hạn, sau khi đã chuẩn bị cơ sở hạ tầng để hỗ trợ chiến lược xây dựng ứng dụng, bạn có thể cần thực hiện các thiết lập bổ sung cho những khối lượng công việc đòi hỏi độ trễ thấp hoặc cần bảo vệ các khối lượng công việc nhạy cảm. Bạn cũng có thể cấu hình tính năng giám sát cho các khối lượng công việc của ứng dụng.

Nếu dự định chạy các khối lượng công việc trên Windows, bạn bắt buộc phải bật tính năng mạng lai (hybrid networking) với OVN-Kubernetes trong quá trình cài đặt; tính năng này không thể được kích hoạt sau khi cụm đã được cài đặt xong.

## III. Các năng lực của cụm
Với tư cách là quản trị viên cụm, bạn có thể sử dụng các tính năng của cụm để bật hoặc tắt các thành phần tùy chọn trước khi cài đặt. Ngoài ra, bạn cũng có thể bật các tính năng của cụm vào bất kỳ thời điểm nào sau khi cài đặt.

> **Lưu ý:** Bạn không thể tắt một tính năng cụm sau khi tính năng đó đã được bật.

### III.1. Bật các tính năng cụm (cluster capabilities)
Nếu bạn sử dụng phương pháp cài đặt cho phép tùy chỉnh cụm thông qua việc tạo tệp `install-config.yaml`, bạn có thể chọn các tính năng cụm mà bạn muốn kích hoạt trên cụm đó.
Lưu ý

Nếu bạn tùy chỉnh cụm bằng cách bật hoặc tắt các tính năng cụm cụ thể, bạn phải tự duy trì tệp `install-config.yaml` của mình. Các bản cập nhật mới của OpenShift Container Platform có thể bổ sung các định danh tính năng (capability handles) mới cho các thành phần hiện có, hoặc giới thiệu hoàn toàn các thành phần mới. Người dùng tùy chỉnh tệp `install-config.yaml` nên cân nhắc cập nhật tệp này định kỳ theo các bản cập nhật của OpenShift Container Platform.

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

### III.2. Các tính năng tùy chọn của cụm trong OpenShift Container Platform 4.22
Hiện tại, các Operator của cụm (cluster Operator) cung cấp những tính năng cho các khả năng tùy chọn này.

Các phần dưới đây tóm tắt những tính năng do từng khả năng cung cấp, cũng như các chức năng mà bạn sẽ mất đi nếu vô hiệu hóa một khả năng nào đó.

#### III.2.1. Cluster Baremetal Operator
Cluster Baremetal Operator cung cấp các tính năng hỗ trợ cho khả năng làm việc với hạ tầng máy chủ vật lý (bare-metal).

Cluster Baremetal Operator (CBO) triển khai tất cả các thành phần cần thiết để chuyển đổi một máy chủ vật lý (bare-metal server) thành một worker node hoàn chỉnh, sẵn sàng chạy các compute node trong OpenShift Container Platform.

CBO đảm bảo rằng quá trình triển khai metal3 — bao gồm các container Bare Metal Operator (BMO) và Ironic — được thực thi trên một trong các control plane node thuộc cụm OpenShift Container Platform. CBO cũng theo dõi các cập nhật đối với những tài nguyên mà nó quản lý trong OpenShift Container Platform để thực hiện các thao tác phù hợp.

Khả năng làm việc với hạ tầng máy chủ vật lý là yêu cầu bắt buộc đối với các mô hình triển khai sử dụng hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure). Việc vô hiệu hóa tính năng này có thể dẫn đến các vấn đề không mong muốn trong quá trình triển khai.

> **Quan trọng:** Nếu khả năng làm việc với hạ tầng máy chủ vật lý bị vô hiệu hóa, cụm (cluster) sẽ không thể cung cấp (provision) hoặc quản lý các máy chủ vật lý (bare-metal node). Chỉ nên vô hiệu hóa tính năng này nếu không có bất kỳ tài nguyên BareMetalHost nào trong môi trường triển khai của bạn. Khả năng làm việc với hạ tầng máy chủ vật lý phụ thuộc vào tính năng MachineAPI; do đó, nếu bạn kích hoạt khả năng làm việc với hạ tầng máy chủ vật lý, bạn cũng phải kích hoạt MachineAPI.

> **Lưu ý:** Red Hat khuyến nghị các quản trị viên cụm chỉ nên vô hiệu hóa khả năng làm việc với hạ tầng máy chủ vật lý trong các trường hợp triển khai sử dụng hạ tầng do người dùng cung cấp (user-provisioned infrastructure) và không có bất kỳ tài nguyên BareMetalHost nào tồn tại trong cụm.

#### III.2.2. Khả năng Build (Build capability)
Khả năng Build cho phép sử dụng Build API. Build API quản lý vòng đời của các đối tượng Build và BuildConfig.

> **Quan trọng:** Nếu bạn vô hiệu hóa khả năng Build, các tài nguyên sau đây sẽ không còn khả dụng trong cụm (cluster):
> 
> * Các tài nguyên Build và BuildConfig
> * Tài khoản dịch vụ (service account) dành cho trình xây dựng (builder)
> 
> Chỉ vô hiệu hóa khả năng Build nếu bạn không cần sử dụng các tài nguyên Build, BuildConfig hoặc tài khoản dịch vụ dành cho trình xây dựng trong cụm.

#### III.2.3. Cloud Controller Manager Operator
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

#### III.2.4. Cloud Credential Operator
Cloud Credential Operator cung cấp các tính năng cho khả năng `CloudCredential`.

> **Lưu ý:** Hiện tại, việc vô hiệu hóa khả năng `CloudCredential` chỉ được hỗ trợ đối với các cụm (cluster) chạy trên hạ tầng vật lý (bare-metal).

Cloud Credential Operator (CCO) quản lý thông tin xác thực (credential) của nhà cung cấp dịch vụ đám mây dưới dạng các định nghĩa tài nguyên tùy chỉnh (Custom Resource Definitions - CRD) trong Kubernetes.

CCO đồng bộ hóa dựa trên các tài nguyên tùy chỉnh (Custom Resource - CR) có tên là `CredentialsRequest`, cho phép các thành phần của OpenShift Container Platform yêu cầu thông tin xác thực từ nhà cung cấp dịch vụ đám mây với các quyền cụ thể cần thiết để cụm hoạt động.

Bằng cách thiết lập các giá trị khác nhau cho tham số `credentialsMode` trong tệp `install-config.yaml`, CCO có thể được cấu hình để vận hành theo nhiều chế độ khác nhau. Nếu không chỉ định chế độ nào, hoặc tham số `credentialsMode` được đặt thành một chuỗi rỗng (`""`), CCO sẽ hoạt động ở chế độ mặc định.

#### III.2.5. Cluster Image Registry Operator
Cluster Image Registry Operator cung cấp các tính năng cho khả năng `ImageRegistry` (registry hình ảnh).

Cluster Image Registry Operator quản lý một thực thể duy nhất (singleton instance) của registry hình ảnh OpenShift. Nó quản lý toàn bộ cấu hình của registry, bao gồm cả việc thiết lập bộ lưu trữ.

Khi khởi chạy lần đầu, Operator sẽ tạo một thực thể tài nguyên `image-registry` mặc định dựa trên cấu hình được phát hiện trong cụm (cluster). Thực thể này xác định loại bộ lưu trữ đám mây cần sử dụng, tùy thuộc vào nhà cung cấp dịch vụ đám mây.

Nếu không có đủ thông tin để xác định một tài nguyên `image-registry` hoàn chỉnh, Operator sẽ tạo một tài nguyên chưa hoàn thiện và cập nhật trạng thái của tài nguyên đó với thông tin về những thành phần còn thiếu.

Cluster Image Registry Operator chạy trong namespace `openshift-image-registry` và quản lý thực thể registry tại vị trí đó. Tất cả các tài nguyên cấu hình và khối lượng công việc (workload) dành cho registry đều nằm trong namespace này.

Để tích hợp registry hình ảnh vào hệ thống xác thực và ủy quyền người dùng của cụm, một "image pull secret" (bí mật dùng để kéo hình ảnh) sẽ được tạo cho mỗi tài khoản dịch vụ (service account) trong cụm.

> **Quan trọng:** Nếu bạn vô hiệu hóa khả năng `ImageRegistry` hoặc vô hiệu hóa registry hình ảnh OpenShift tích hợp trong cấu hình của Cluster Image Registry Operator, thì "image pull secret" sẽ không được tạo cho các tài khoản dịch vụ.

Việc vô hiệu hóa khả năng `ImageRegistry` có thể giúp giảm mức tiêu thụ tài nguyên tổng thể của OpenShift Container Platform trong các môi trường viễn thông (Telco). Tùy thuộc vào mô hình triển khai, bạn có thể vô hiệu hóa thành phần này nếu không cần sử dụng đến nó.

#### III.2.6. Cluster Storage Operator
Cluster Storage Operator cung cấp các tính năng cho khả năng lưu trữ (storage capability).

Cluster Storage Operator thiết lập các cấu hình lưu trữ mặc định trên toàn bộ cụm OpenShift Container Platform. Thành phần này đảm bảo luôn có một lớp lưu trữ (storageclass) mặc định cho các cụm OpenShift Container Platform. Ngoài ra, nó còn cài đặt các trình điều khiển Container Storage Interface (CSI), cho phép cụm của bạn sử dụng nhiều hệ thống lưu trữ (storage backend) khác nhau.

> **Quan trọng:** Nếu tính năng lưu trữ của cụm bị vô hiệu hóa, cụm sẽ không có lớp lưu trữ mặc định cũng như bất kỳ trình điều khiển CSI nào. Người dùng có đặc quyền quản trị viên vẫn có thể tạo lớp lưu trữ mặc định và tự cài đặt các trình điều khiển CSI theo cách thủ công ngay cả khi tính năng lưu trữ của cụm đã bị vô hiệu hóa.

**Lưu ý**  
Lớp lưu trữ do Operator tạo ra có thể được chuyển đổi thành lớp không mặc định bằng cách chỉnh sửa chú thích (annotation) của nó; tuy nhiên, lớp lưu trữ này không thể bị xóa khi Operator đang hoạt động.

#### III.2.7. Console Operator
Console Operator cung cấp các tính năng cho chức năng Console (bảng điều khiển).

Console Operator thực hiện cài đặt và duy trì giao diện web (web console) của OpenShift Container Platform trên một cụm (cluster). Console Operator được cài đặt theo mặc định và tự động duy trì giao diện console này.

#### III.2.8. Cluster CSI Snapshot Controller Operator
Cluster CSI Snapshot Controller Operator cung cấp các tính năng cho khả năng `CSISnapshot`.

Cluster CSI Snapshot Controller Operator thực hiện cài đặt và duy trì CSI Snapshot Controller. CSI Snapshot Controller chịu trách nhiệm theo dõi các đối tượng CRD `VolumeSnapshot`, đồng thời quản lý vòng đời tạo và xóa các bản chụp (snapshot) của volume.

#### III.2.9. Tính năng DeploymentConfig
Tính năng `DeploymentConfig` cho phép sử dụng và quản lý API DeploymentConfig.

> **Quan trọng:** Nếu bạn tắt tính năng `DeploymentConfig`, các tài nguyên sau đây sẽ không còn khả dụng trong cụm (cluster):
> 
> * Các tài nguyên `DeploymentConfig`
> * Tài khoản dịch vụ (service account) dành cho trình triển khai (`deployer`)
> 
> Chỉ nên tắt tính năng `DeploymentConfig` nếu bạn không cần sử dụng các tài nguyên `DeploymentConfig` và tài khoản dịch vụ dành cho trình triển khai (`deployer`) trong cụm.

#### III.2.10. Trình điều khiển Ingress
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

```console
oc get clusteringresses.ingress.openshift.io -n openshift-ingress-operator default -o yaml
```

**Ghi chú**  
Ingress Operator thiết lập bộ định tuyến (router) trong dự án openshift-ingress và tạo deployment cho bộ định tuyến đó:

```console
oc get deployment -n openshift-ingress
```

Ingress Operator sử dụng thông tin `clusterNetwork[].cidr` từ trạng thái `network/cluster` để xác định chế độ hoạt động (IPv4, IPv6 hoặc dual-stack) cho Ingress Controller (bộ định tuyến) mà nó quản lý. Chẳng hạn, nếu `clusterNetwork` chỉ chứa một CIDR phiên bản 6 (v6), thì Ingress Controller sẽ hoạt động ở chế độ chỉ hỗ trợ IPv6.

Trong ví dụ dưới đây, các Ingress Controller do Ingress Operator quản lý sẽ chạy ở chế độ chỉ hỗ trợ IPv4, vì chỉ có một mạng cụm (cluster network) tồn tại và mạng đó là một CIDR IPv4:

```console
oc get network/cluster -o jsonpath='{.status.clusterNetwork[*]}'
```

Ví dụ đầu ra

```
map[cidr:10.128.0.0/14 hostPrefix:23]
```

#### III.2.11. Insights Operator
Insights Operator cung cấp các tính năng cho khả năng Insights.

Insights Operator thu thập dữ liệu cấu hình của OpenShift Container Platform và gửi dữ liệu đó đến Red Hat.

Dữ liệu này được sử dụng để đưa ra các khuyến nghị chủ động về những vấn đề tiềm ẩn mà cụm (cluster) có thể gặp phải. Các khuyến nghị này được truyền đạt đến quản trị viên cụm thông qua dịch vụ tư vấn Red Hat Lightspeed trên [console.redhat.com](https://console.redhat.com/).

> **Lưu ý:** Insights Operator bổ sung cho tính năng Telemetry của OpenShift Container Platform.

#### III.2.12. Khả năng Machine API
Các toán tử (operator) gồm `machine-api-operator`, `cluster-autoscaler-operator` và `cluster-control-plane-machine-set-operator` cung cấp các tính năng cho khả năng Machine API. Bạn chỉ có thể vô hiệu hóa khả năng này nếu cài đặt cụm (cluster) với cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure).

Khả năng Machine API chịu trách nhiệm cho toàn bộ quá trình cấu hình và quản lý máy (machine) trong cụm. Nếu bạn vô hiệu hóa khả năng Machine API trong quá trình cài đặt, bạn sẽ cần phải tự mình thực hiện thủ công mọi tác vụ liên quan đến các máy đó.

#### III.2.13. Marketplace Operator
Marketplace Operator cung cấp các tính năng cho chức năng marketplace (thị trường ứng dụng).

Marketplace Operator đơn giản hóa quy trình đưa các Operator từ bên ngoài cụm (off-cluster) vào cụm của bạn bằng cách sử dụng một tập hợp các danh mục (catalog) mặc định của Operator Lifecycle Manager (OLM) trên cụm đó. Khi Marketplace Operator được cài đặt, nó sẽ tạo ra namespace `openshift-marketplace`. OLM đảm bảo rằng các nguồn danh mục (catalog source) được cài đặt trong namespace `openshift-marketplace` sẽ khả dụng cho tất cả các namespace khác trên cụm.

Nếu bạn vô hiệu hóa chức năng marketplace, Marketplace Operator sẽ không tạo namespace `openshift-marketplace`. Các nguồn danh mục vẫn có thể được cấu hình và quản lý thủ công trên cụm, nhưng OLM cần namespace `openshift-marketplace` để làm cho các danh mục đó khả dụng với tất cả các namespace trên cụm. Những người dùng có quyền hạn cao, chẳng hạn như quản trị viên hệ thống hoặc quản trị viên cụm (cluster administrator) — những người có quyền tạo các namespace bắt đầu bằng tiền tố `openshift-` — có thể tự tạo namespace `openshift-marketplace` theo cách thủ công.

Nếu bạn kích hoạt chức năng marketplace, bạn có thể bật hoặc tắt từng danh mục riêng lẻ bằng cách cấu hình Marketplace Operator.

#### III.2.14. Node Tuning Operator
Node Tuning Operator cung cấp các tính năng cho khả năng NodeTuning (tinh chỉnh nút).

Node Tuning Operator hỗ trợ quản lý việc tinh chỉnh ở cấp độ nút thông qua việc điều phối tiến trình nền (daemon) TuneD, đồng thời đạt được hiệu năng có độ trễ thấp nhờ sử dụng bộ điều khiển Performance Profile (cấu hình hiệu năng).

Phần lớn các ứng dụng hiệu năng cao đều yêu cầu tinh chỉnh nhân hệ điều hành (kernel) ở một mức độ nhất định. Node Tuning Operator cung cấp cho người dùng một giao diện quản lý thống nhất đối với các tham số sysctl ở cấp độ nút, đồng thời mang lại sự linh hoạt cao hơn trong việc bổ sung các cấu hình tinh chỉnh tùy chỉnh theo nhu cầu cụ thể.

Nếu bạn vô hiệu hóa khả năng NodeTuning, một số thiết lập tinh chỉnh mặc định sẽ không được áp dụng cho các nút trong mặt phẳng điều khiển (control-plane nodes). Điều này có thể hạn chế khả năng mở rộng và hiệu năng của các cụm (cluster) quy mô lớn, với số lượng nút hoặc tuyến đường (route) vượt quá 900.

#### III.2.15. Cluster Samples Operator
Cluster Samples Operator cung cấp các tính năng cho thành phần `openshift-samples`.

Cluster Samples Operator quản lý các image stream (luồng hình ảnh) mẫu và các template (mẫu cấu hình) được lưu trữ trong namespace `openshift`.

Khi khởi chạy lần đầu, Operator này sẽ tạo tài nguyên cấu hình mẫu mặc định để bắt đầu quá trình tạo các image stream và template. Đối tượng cấu hình này có phạm vi toàn cụm (cluster-scoped), với khóa (key) là `cluster` và loại (type) là `configs.samples`.

Các image stream này dựa trên Red Hat Enterprise Linux CoreOS (RHCOS) dành cho OpenShift Container Platform, trỏ đến các hình ảnh (image) trên registry.redhat.io. Tương tự, các template ở đây là những mẫu được phân loại dành riêng cho OpenShift Container Platform.

Nếu bạn vô hiệu hóa thành phần samples, người dùng sẽ không thể truy cập vào các image stream, mẫu (sample) và template mà thành phần này cung cấp. Tùy thuộc vào quá trình triển khai, bạn có thể cân nhắc vô hiệu hóa thành phần này nếu không cần sử dụng đến nó.

#### III.2.16. Giới thiệu về Operator Lifecycle Manager (OLM) Classic
OLM (Classic) cung cấp các tính năng cho khả năng (capability) `OperatorLifecycleManager`.

Operator Lifecycle Manager (OLM) Classic hỗ trợ người dùng cài đặt, cập nhật và quản lý vòng đời của các ứng dụng gốc trên Kubernetes (được gọi là các Operator) cùng các dịch vụ liên quan, chạy trên các cụm OpenShift Container Platform. Operator Lifecycle Manager (OLM) Classic là một phần của Operator Framework – một bộ công cụ mã nguồn mở được thiết kế để quản lý các Operator theo cách hiệu quả, tự động và có khả năng mở rộng.

Nếu một Operator yêu cầu bất kỳ API nào dưới đây, bạn bắt buộc phải kích hoạt khả năng `OperatorLifecycleManager`:

* `ClusterServiceVersion`
* `CatalogSource`
* `Subscription`
* `InstallPlan`
* `OperatorGroup`

> **Quan trọng:** Khả năng `marketplace` phụ thuộc vào khả năng `OperatorLifecycleManager`. Bạn không thể vô hiệu hóa khả năng `OperatorLifecycleManager` trong khi vẫn kích hoạt khả năng `marketplace`.

#### III.2.17. Operator Lifecycle Manager (OLM) v1 Operator
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

### III.3. Xem các khả năng của cụm (cluster capabilities)
Với tư cách là quản trị viên cụm, bạn có thể xem các khả năng của cụm thông qua trạng thái của tài nguyên `clusterversion`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (oc).

**Quy trình**  
Để xem trạng thái các khả năng của cụm, hãy chạy lệnh sau:

```console
oc get clusterversion version -o \
jsonpath='{.spec.capabilities}{"\n"}{.status.capabilities}{"\n"}'
```

Ví dụ đầu ra

```json
{"additionalEnabledCapabilities":["openshift-samples"],"baselineCapabilitySet":"None"}
{"enabledCapabilities":["openshift-samples"],"knownCapabilities":["CSISnapshot","Console","Insights","Storage","baremetal","marketplace","openshift-samples"]}
```

### 3.4. Kích hoạt các tính năng của cụm thông qua việc thiết lập tập hợp tính năng cơ sở (baseline capability set)
Với tư cách là quản trị viên cụm, bạn có thể kích hoạt các tính năng của cụm vào bất kỳ thời điểm nào sau khi cài đặt OpenShift Container Platform bằng cách thiết lập tham số cấu hình `baselineCapabilitySet`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (oc).

**Quy trình**  
Để thiết lập tham số cấu hình `baselineCapabilitySet`, hãy chạy lệnh sau:

```console
oc patch clusterversion version --type merge -p \
'{"spec":{"capabilities":{"baselineCapabilitySet":"vCurrent"}}}'
```

Đối với `baselineCapabilitySet`, bạn có thể chỉ định vCurrent, v4.22 hoặc None.

### III.5. Kích hoạt các tính năng cụm bằng cách thiết lập các tính năng bổ sung được kích hoạt
Với tư cách là quản trị viên cụm, bạn có thể kích hoạt các tính năng cụm vào bất kỳ thời điểm nào sau khi cài đặt OpenShift Container Platform bằng cách thiết lập tham số cấu hình `additionalEnabledCapabilities`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (CLI - `oc`).

**Quy trình**  
1. Xem các tính năng bổ sung đã được kích hoạt bằng cách chạy lệnh sau:

```console
oc get clusterversion version -o jsonpath='{.spec.capabilities.additionalEnabledCapabilities}{"\n"}'
```

Ví dụ đầu ra

```json
["openshift-samples"]
```

2. Để thiết lập tham số cấu hình additionalEnabledCapabilities, hãy chạy lệnh sau:

```console
oc patch clusterversion/version --type merge -p \
'{"spec":{"capabilities":{"additionalEnabledCapabilities":["openshift-samples", "marketplace"]}}}'
```

> **Quan trọng:** Bạn không thể vô hiệu hóa một tính năng đã được kích hoạt trong cụm (cluster). Cluster Version Operator (CVO) sẽ tiếp tục duy trì trạng thái của tính năng đã được kích hoạt trong cụm đó.

Nếu bạn cố gắng vô hiệu hóa một tính năng, CVO sẽ hiển thị thông số kỹ thuật khác biệt (divergent spec):

```console
oc get clusterversion version -o \
jsonpath='{.status.conditions[?(@.type=="ImplicitlyEnabledCapabilities")]}{"\n"}'
```

Ví dụ đầu ra

```json
{"lastTransitionTime":"2022-07-22T03:14:35Z","message":"The following capabilities could not be disabled: openshift-samples","reason":"CapabilitiesImplicitlyEnabled","status":"True","type":"ImplicitlyEnabledCapabilities"}
```

> **Lưu ý:** Trong quá trình nâng cấp cụm (cluster), một tính năng nhất định có thể được tự động kích hoạt. Nếu một tài nguyên đã chạy trên cụm trước thời điểm nâng cấp, thì bất kỳ tính năng nào gắn liền với tài nguyên đó cũng sẽ được kích hoạt. Chẳng hạn, trong quá trình nâng cấp cụm, một tài nguyên đang chạy trên cụm có thể được hệ thống chuyển sang thuộc phạm vi của tính năng "marketplace" (thị trường). Ngay cả khi quản trị viên cụm không chủ động kích hoạt tính năng này, nó vẫn sẽ được hệ thống tự động kích hoạt.

## IV. Hỗ trợ mật mã học FIPS
Bạn có thể cài đặt cụm OpenShift Container Platform ở chế độ FIPS.

OpenShift Container Platform được thiết kế để hỗ trợ FIPS. Khi chạy trên Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) được khởi động ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã của RHEL—những thư viện đã được gửi tới NIST để đánh giá theo tiêu chuẩn FIPS 140-2/140-3 trên các kiến trúc x86_64, ppc64le và s390x.

Để biết thêm thông tin về chương trình đánh giá của NIST, hãy xem mục "Cryptographic Module Validation Program" (Chương trình Đánh giá Mô-đun Mật mã) trong phần Tài nguyên bổ sung. Để cập nhật trạng thái mới nhất từ NIST đối với từng phiên bản thư viện mật mã RHEL đã được gửi để đánh giá, hãy xem mục "Compliance Activities and Government Standards" (Các hoạt động tuân thủ và tiêu chuẩn chính phủ) trong phần Tài nguyên bổ sung.

> **Quan trọng:** Để bật chế độ FIPS cho cụm của mình, bạn phải chạy chương trình cài đặt từ một máy tính sử dụng hệ điều hành RHEL 9 đã được cấu hình để hoạt động ở chế độ FIPS; đồng thời, bạn phải sử dụng phiên bản chương trình cài đặt có hỗ trợ FIPS. Hãy tham khảo phần có tiêu đề "Obtaining a FIPS-capable installation program using `oc adm extract`" (Cách lấy chương trình cài đặt hỗ trợ FIPS bằng lệnh `oc adm extract`).

Để biết thêm thông tin về cách cấu hình chế độ FIPS trên RHEL, hãy xem mục "Installing the system in FIPS mode" (Cài đặt hệ thống ở chế độ FIPS) trong phần Tài nguyên bổ sung.

Đối với các máy chạy Red Hat Enterprise Linux CoreOS (RHCOS) trong cụm, thay đổi này sẽ được áp dụng tại thời điểm triển khai máy, dựa trên trạng thái của một tùy chọn trong tệp `install-config.yaml` (tệp quy định các tùy chọn cụm mà người dùng có thể thay đổi trong quá trình triển khai). Đối với các máy chạy Red Hat Enterprise Linux (RHEL), bạn phải bật chế độ FIPS ngay khi cài đặt hệ điều hành trên các máy mà bạn dự định sử dụng làm máy worker (worker machine).

Vì FIPS phải được bật trước khi hệ điều hành mà cụm của bạn sử dụng khởi động lần đầu tiên, nên bạn không thể bật FIPS sau khi đã triển khai cụm.

### IV.1. Lấy chương trình cài đặt hỗ trợ FIPS bằng lệnh `oc adm extract`
Để cài đặt cụm OpenShift Container Platform ở chế độ FIPS, hãy trích xuất tệp nhị phân cài đặt hỗ trợ FIPS từ hình ảnh phát hành (release image) bằng cách sử dụng giao diện dòng lệnh OpenShift (oc).

Sau khi trích xuất tệp nhị phân, hãy tiến hành cài đặt cụm, trong đó thay thế tất cả các lệnh `openshift-install` bằng `openshift-install-fips`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (oc) phiên bản 4.16 trở lên.

**Quy trình**  
1. Trích xuất tệp nhị phân hỗ trợ FIPS từ chương trình cài đặt bằng cách chạy lệnh sau:

```console
oc adm release extract --registry-config "${pullsecret_file}" \
--command=openshift-install-fips --to "${extract_dir}" ${RELEASE_IMAGE}
```

trong đó:

`<pullsecret_file>` Chỉ định tên tệp chứa thông tin xác thực (pull secret) của bạn.

`<extract_dir>` Chỉ định thư mục nơi bạn muốn giải nén tệp nhị phân.
`<RELEASE_IMAGE>` Chỉ định URL trên Quay.io của phiên bản OpenShift Container Platform mà bạn đang sử dụng.

2. Tiến hành cài đặt cụm, trong đó thay thế tất cả các lần xuất hiện của lệnh `openshift-install` bằng `openshift-install-fips`.

### IV.3. Lấy chương trình cài đặt hỗ trợ FIPS thông qua mirror OpenShift công khai
Để cài đặt cụm OpenShift Container Platform ở chế độ FIPS, hãy tải xuống tệp nhị phân cài đặt hỗ trợ FIPS từ mirror OpenShift công khai.

Sau khi có tệp nhị phân này, hãy tiến hành cài đặt cụm, trong đó thay thế tất cả các tệp nhị phân `openshift-install` bằng `openshift-install-fips`.

**Điều kiện tiên quyết**  
Bạn có quyền truy cập internet.

**Quy trình**  
1. Tải xuống chương trình cài đặt từ địa chỉ https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest-4.18/openshift-install-rhel9-amd64.tar.gz. 
2. Giải nén chương trình cài đặt. Ví dụ: trên máy tính sử dụng hệ điều hành Linux, hãy chạy lệnh sau:

```console
tar -xvf openshift-install-rhel9-amd64.tar.gz
```

3. Tiến hành cài đặt cụm, trong đó thay thế tất cả các lần xuất hiện của lệnh `openshift-install` bằng `openshift-install-fips`.

### IV.4. Xác thực FIPS trong OpenShift Container Platform
OpenShift Container Platform sử dụng một số mô-đun đã được xác thực FIPS hoặc các mô-đun đang trong quá trình xác thực (Modules In Process) bên trong RHEL và RHCOS cho các thành phần hệ điều hành mà nền tảng này vận hành.

Để biết thêm thông tin, hãy xem mục "Các thành phần mật mã cốt lõi của RHEL" (RHEL core crypto components) trong phần Tài nguyên bổ sung. Chẳng hạn, khi người dùng sử dụng SSH để kết nối với các cụm (cluster) và container của OpenShift Container Platform, các kết nối đó sẽ được mã hóa đúng cách.

Các thành phần của OpenShift Container Platform được viết bằng ngôn ngữ Go và được xây dựng bằng trình biên dịch Go (golang) của Red Hat. Khi bạn kích hoạt chế độ FIPS cho cụm của mình, tất cả các thành phần OpenShift Container Platform yêu cầu ký mật mã sẽ gọi đến các thư viện mật mã của RHEL và RHCOS.

**Bảng 4.1. Các thuộc tính và hạn chế của chế độ FIPS trong OpenShift Container Platform 4.22**

| Các thuộc tính                                                                                                                                                                  | Các hạn chế                                                                                                                                                                                                                                                                       |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hỗ trợ FIPS trong các hệ điều hành RHEL 9 và RHCOS.                                                                                                                             | Việc triển khai FIPS không sử dụng hàm nào thực hiện đồng thời cả thao tác tính toán hàm băm (hash) lẫn thao tác tạo hoặc xác thực chữ ký trong một bước duy nhất. Hạn chế này sẽ tiếp tục được đánh giá và cải thiện trong các phiên bản OpenShift Container Platform tương lai. |
| Hỗ trợ FIPS trong các runtime CRI-O.                                                                                                                                            | Như trên                                                                                                                                                                                                                                                                          |
| Hỗ trợ FIPS trong các dịch vụ của OpenShift Container Platform.                                                                                                                 | Như trên                                                                                                                                                                                                                                                                          |
| Các mô-đun và thuật toán mật mã đã được xác thực theo tiêu chuẩn FIPS hoặc đang trong quá trình xác thực, được lấy từ các tệp nhị phân và hình ảnh (image) của RHEL 9 và RHCOS. | Như trên                                                                                                                                                                                                                                                                          |
| Sử dụng trình biên dịch Golang tương thích với FIPS.                                                                                                                            | Hỗ trợ TLS FIPS hiện chưa hoàn thiện nhưng đã được lên kế hoạch cho các phiên bản OpenShift Container Platform trong tương lai.                                                                                                                                                   |
| Hỗ trợ FIPS trên nhiều kiến trúc.                                                                                                                                               | Hiện tại, FIPS chỉ được hỗ trợ trên các triển khai OpenShift Container Platform sử dụng các kiến trúc x86_64, ppc64le và s390x.                                                                                                                                                   |

### IV.5. Hỗ trợ FIPS trong các thành phần mà cụm (cluster) sử dụng
Mặc dù bản thân cụm OpenShift Container Platform sử dụng các mô-đun đã được xác thực FIPS hoặc đang trong quá trình xác thực (Modules In Process), bạn cần đảm bảo rằng các hệ thống hỗ trợ cụm OpenShift Container Platform của bạn cũng sử dụng các mô-đun đã được xác thực FIPS hoặc đang trong quá trình xác thực cho các tác vụ mật mã.

**etcd**  
Để đảm bảo rằng các thông tin bí mật (secrets) lưu trữ trong etcd sử dụng cơ chế mã hóa đã được xác thực FIPS hoặc đang trong quá trình xác thực, hãy khởi động nút (node) ở chế độ FIPS. Sau khi cài đặt cụm ở chế độ FIPS, bạn có thể mã hóa dữ liệu etcd bằng thuật toán mật mã aes-cbc đã được FIPS phê duyệt.

**Lưu trữ (Storage)**  
Đối với lưu trữ cục bộ (local storage), hãy sử dụng tính năng mã hóa đĩa do RHEL cung cấp hoặc giải pháp Container Native Storage có sử dụng tính năng mã hóa đĩa của RHEL. Bằng cách lưu trữ toàn bộ dữ liệu trong các ổ đĩa (volumes) được mã hóa thông qua cơ chế của RHEL và kích hoạt chế độ FIPS cho cụm, cả dữ liệu lưu trữ (data at rest) lẫn dữ liệu đang truyền tải (data in motion/network data) đều sẽ được bảo vệ bởi các mô-đun mã hóa đã được xác thực FIPS hoặc đang trong quá trình xác thực. Bạn có thể cấu hình cụm để mã hóa hệ thống tệp gốc (root filesystem) của từng nút. Để biết thêm thông tin, hãy xem phần "Tùy chỉnh các nút" (Customizing nodes) trong mục Tài nguyên bổ sung.

**Môi trường thực thi (Runtimes)**  
Để đảm bảo các container nhận biết được rằng chúng đang chạy trên một máy chủ sử dụng các mô-đun mật mã đã được xác thực FIPS hoặc đang trong quá trình xác thực, hãy sử dụng CRI-O để quản lý các môi trường thực thi của bạn.

### 4.6. Cài đặt cụm ở chế độ FIPS
Để cài đặt một cụm ở chế độ FIPS, hãy làm theo các hướng dẫn cài đặt cụm tùy chỉnh trên cơ sở hạ tầng mà bạn chọn. Đảm bảo rằng bạn đã thiết lập `fips: true` trong tệp `install-config.yaml` trước khi triển khai cụm của mình.

> **Lưu ý:** Nếu bạn đang sử dụng Azure File storage, bạn không thể bật chế độ FIPS.

Để áp dụng mã hóa AES CBC cho kho dữ liệu etcd của bạn, hãy thực hiện theo quy trình "Mã hóa dữ liệu etcd" sau khi đã cài đặt cụm (cluster) của mình.
