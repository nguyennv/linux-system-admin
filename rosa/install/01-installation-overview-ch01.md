Cài đặt OpenShift - Tổng quan về cài đặt
----------------------------------------

# I. Tổng quan về cài đặt OpenShift Container Platform
## I.1. Các tùy chọn cài đặt OpenShift Container Platform
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

## I.1.1. Bảng thuật ngữ các thuật ngữ phổ biến trong quá trình cài đặt OpenShift Container Platform
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

## I.1.2. Quy trình cài đặt
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

### Quy trình cài đặt với Assisted Installer
Việc cài đặt bằng Assisted Installer bao gồm quá trình tạo cấu hình cụm (cluster) theo cách tương tác thông qua giao diện người dùng trên nền tảng web hoặc RESTful API. Giao diện người dùng của Assisted Installer sẽ yêu cầu bạn nhập các giá trị cần thiết, đồng thời cung cấp các giá trị mặc định hợp lý cho những tham số còn lại (trừ khi bạn thay đổi chúng thông qua giao diện hoặc API). Assisted Installer sẽ tạo ra một "hình ảnh khám phá" (discovery image); bạn cần tải xuống hình ảnh này để khởi động các máy trong cụm. Hình ảnh đó sẽ cài đặt RHCOS cùng một tác nhân (agent), và tác nhân này sẽ đảm nhận việc cung cấp tài nguyên (provisioning) cho bạn. Bạn có thể cài đặt OpenShift Container Platform bằng Assisted Installer với khả năng tích hợp đầy đủ trên các nền tảng Nutanix, vSphere và hạ tầng vật lý (bare metal). Ngoài ra, bạn cũng có thể sử dụng Assisted Installer để cài đặt OpenShift Container Platform trên các nền tảng khác mà không cần tích hợp. 

OpenShift Container Platform quản lý mọi khía cạnh của cụm, bao gồm cả chính hệ điều hành. Mỗi máy khi khởi động đều sử dụng một cấu hình tham chiếu đến các tài nguyên được lưu trữ trong cụm mà nó tham gia. Cấu hình này cho phép cụm tự quản lý trong suốt quá trình áp dụng các bản cập nhật. 

Nếu có thể, hãy sử dụng tính năng Assisted Installer để tránh việc phải tải xuống và cấu hình Agent-based Installer.

### Quy trình cài đặt với cơ sở hạ tầng dựa trên tác nhân (Agent-based infrastructure)
Cài đặt dựa trên tác nhân (Agent-based installation) tương tự như sử dụng trình cài đặt hỗ trợ (Assisted Installer), điểm khác biệt là bạn cần tải xuống và cài đặt trình cài đặt dựa trên tác nhân (Agent-based Installer) trước. Phương pháp này hữu ích khi bạn muốn tận dụng sự tiện lợi của trình cài đặt hỗ trợ, nhưng lại cần triển khai cụm (cluster) trong một môi trường không kết nối mạng (disconnected environment). 

Nếu có thể, hãy sử dụng tính năng cài đặt dựa trên tác nhân để tránh việc phải tạo một máy cung cấp tài nguyên (provisioner machine) kèm theo máy ảo khởi động (bootstrap VM), cũng như không cần phải thực hiện các công việc thiết lập và duy trì cơ sở hạ tầng cho cụm.

### Quy trình cài đặt với cơ sở hạ tầng do trình cài đặt cung cấp
Kiểu cài đặt mặc định sử dụng cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure). Theo mặc định, chương trình cài đặt hoạt động như một trình hướng dẫn cài đặt, yêu cầu bạn cung cấp các giá trị mà nó không thể tự xác định, đồng thời đưa ra các giá trị mặc định hợp lý cho các tham số còn lại. Bạn cũng có thể tùy chỉnh quy trình cài đặt để hỗ trợ các kịch bản cơ sở hạ tầng nâng cao. Chương trình cài đặt sẽ thực hiện cung cấp (provision) cơ sở hạ tầng nền tảng cho cụm (cluster). 

Bạn có thể cài đặt một cụm tiêu chuẩn hoặc một cụm tùy chỉnh. Với cụm tiêu chuẩn, bạn chỉ cần cung cấp các thông tin tối thiểu cần thiết để cài đặt cụm. Với cụm tùy chỉnh, bạn có thể chỉ định chi tiết hơn về nền tảng, chẳng hạn như số lượng máy được sử dụng cho mặt phẳng điều khiển (control plane), loại máy ảo mà cụm triển khai, hoặc dải CIDR cho mạng dịch vụ Kubernetes. 

Nếu có thể, hãy sử dụng tính năng này để tránh việc phải tự cung cấp và duy trì cơ sở hạ tầng cho cụm. Trong các môi trường khác, bạn sẽ sử dụng chương trình cài đặt để tạo ra các tài nguyên cần thiết cho việc thiết lập cơ sở hạ tầng cụm của mình. 

Đối với các cụm sử dụng cơ sở hạ tầng do trình cài đặt cung cấp, OpenShift Container Platform sẽ quản lý mọi khía cạnh của cụm, bao gồm cả hệ điều hành. Mỗi máy khi khởi động đều sử dụng một cấu hình tham chiếu đến các tài nguyên được lưu trữ ngay trong cụm mà nó tham gia. Cấu hình này cho phép cụm tự quản lý trong quá trình áp dụng các bản cập nhật.

### Quy trình cài đặt với cơ sở hạ tầng do người dùng cung cấp
Bạn cũng có thể cài đặt OpenShift Container Platform trên cơ sở hạ tầng do chính bạn cung cấp. Bạn sẽ sử dụng chương trình cài đặt để tạo ra các thành phần cần thiết cho việc thiết lập cơ sở hạ tầng cụm, tạo cơ sở hạ tầng đó, rồi triển khai cụm trên cơ sở hạ tầng mà bạn đã cung cấp. 

Nếu không sử dụng cơ sở hạ tầng do chương trình cài đặt thiết lập, bạn phải tự quản lý và duy trì các tài nguyên của cụm. Danh sách dưới đây nêu chi tiết một số tài nguyên mà bạn cần tự quản lý:

* Cơ sở hạ tầng nền tảng cho mặt phẳng điều khiển (control plane) và các máy tính toán (compute machines) tạo nên cụm
* Các bộ cân bằng tải (load balancer)
* Hệ thống mạng của cụm, bao gồm các bản ghi DNS và các mạng con (subnet) cần thiết
* Lưu trữ cho cơ sở hạ tầng cụm và các ứng dụng
* Nếu cụm của bạn sử dụng cơ sở hạ tầng do người dùng cung cấp, bạn có tùy chọn bổ sung các máy tính toán chạy hệ điều hành RHEL vào cụm của mình.

## I.1.3. Xác minh trạng thái nút sau khi cài đặt
Quá trình cài đặt OpenShift Container Platform hoàn tất khi các bước kiểm tra tình trạng sau đây đạt kết quả thành công:

Người thực hiện cài đặt có thể truy cập vào bảng điều khiển web (web console) của OpenShift Container Platform.
Tất cả các nút trong mặt phẳng điều khiển (control plane nodes) đều ở trạng thái sẵn sàng. 
Tất cả các thành phần vận hành cụm (cluster Operators) đều ở trạng thái khả dụng.

> **Lưu ý:** Sau khi quá trình cài đặt hoàn tất, các Operator cụm (cluster Operator) chịu trách nhiệm về các nút worker (worker node) sẽ liên tục cố gắng cung cấp tài nguyên cho tất cả các nút worker đó. Cần một khoảng thời gian nhất định để tất cả các nút worker chuyển sang trạng thái SẴN SÀNG (READY). Đối với các hệ thống cài đặt trên phần cứng vật lý (bare metal), hãy đợi ít nhất 60 phút trước khi tiến hành khắc phục sự cố cho một nút worker. Đối với các hệ thống cài đặt trên các nền tảng khác, hãy đợi ít nhất 40 phút trước khi tiến hành khắc phục sự cố cho một nút worker. Trạng thái SUY GIẢM (DEGRADED) của các Operator cụm chịu trách nhiệm về các nút worker phụ thuộc vào chính các tài nguyên của các Operator đó, chứ không phụ thuộc vào trạng thái của các nút.

Sau khi quá trình cài đặt hoàn tất, bạn có thể tiếp tục theo dõi tình trạng các nút trong cụm của mình.

### Các điều kiện tiên quyết
Chương trình cài đặt chạy thành công trong cửa sổ dòng lệnh (terminal).

### Quy trình
1. Hiển thị trạng thái của tất cả các nút worker:

```shell
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

```shell
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

## I.1.4. Phạm vi cài đặt
Phạm vi của chương trình cài đặt OpenShift Container Platform được cố tình giới hạn ở mức hẹp. Nó được thiết kế để đảm bảo sự đơn giản và khả năng thành công cao. Bạn có thể thực hiện thêm nhiều tác vụ cấu hình khác sau khi quá trình cài đặt hoàn tất.

## I.1.5. Tổng quan về OpenShift Local
OpenShift Local hỗ trợ phát triển ứng dụng nhanh chóng, giúp người dùng bắt đầu xây dựng các cụm (cluster) OpenShift Container Platform.

OpenShift Local được thiết kế để chạy trên máy tính cá nhân, giúp đơn giản hóa quá trình thiết lập và kiểm thử, đồng thời mô phỏng môi trường phát triển trên nền tảng đám mây ngay tại máy tính của bạn, với đầy đủ các công cụ cần thiết để phát triển các ứng dụng dựa trên container.

Bất kể bạn sử dụng ngôn ngữ lập trình nào, OpenShift Local đều có thể lưu trữ ứng dụng của bạn và cung cấp một cụm Red Hat OpenShift Container Platform tối giản, được cấu hình sẵn trên máy tính cá nhân mà không cần đến cơ sở hạ tầng dựa trên máy chủ (server-based infrastructure).

Trong môi trường này, OpenShift Local cho phép tạo các dịch vụ siêu nhỏ (microservices), chuyển đổi chúng thành các image (hình ảnh ứng dụng) và chạy chúng trong các container trên nền tảng Kubernetes, ngay trên máy tính xách tay hoặc máy tính để bàn của bạn (hỗ trợ các hệ điều hành Linux, macOS, hoặc Windows 10 trở lên).

## I.2. Các nền tảng được hỗ trợ cho các cụm OpenShift Container Platform
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
