Cài đặt OpenShift - Tổng quan về cài đặt
----------------------------------------

# II. Lựa chọn phương thức cài đặt cụm (cluster) và chuẩn bị cho người dùng
Trước khi cài đặt OpenShift Container Platform, hãy quyết định quy trình cài đặt nào sẽ được áp dụng và xác minh rằng bạn đã có đầy đủ các tài nguyên cần thiết để chuẩn bị cụm (cluster) cho người dùng.

## II.1. Chọn loại hình cài đặt cụm
Hãy quyết định quy trình cài đặt nào phù hợp nhất dựa trên cơ sở hạ tầng, kinh nghiệm và các yêu cầu bảo mật của bạn.

Trước khi cài đặt một cụm OpenShift Container Platform, bạn cần chọn hướng dẫn cài đặt tối ưu nhất. Hãy cân nhắc các câu trả lời cho những câu hỏi dưới đây để đưa ra lựa chọn phù hợp nhất.

## II.1.1. Bạn có muốn tự mình cài đặt và quản lý cụm OpenShift Container Platform không?
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

## II.1.2. Bạn đã từng sử dụng OpenShift Container Platform 3 và muốn chuyển sang sử dụng OpenShift Container Platform 4?
Nếu bạn đã từng sử dụng OpenShift Container Platform 3 và muốn trải nghiệm OpenShift Container Platform 4, bạn cần hiểu rõ những điểm khác biệt của phiên bản này. OpenShift Container Platform 4 kết hợp một cách liền mạch các Operator — vốn dùng để đóng gói, triển khai và quản lý các ứng dụng Kubernetes — với hệ điều hành mà nền tảng này vận hành trên đó, chính là Red Hat Enterprise Linux CoreOS (RHCOS). Thay vì phải triển khai các máy chủ rồi cấu hình hệ điều hành trên đó để cài đặt OpenShift Container Platform, thì giờ đây, hệ điều hành RHCOS đã trở thành một phần không thể tách rời của cụm (cluster) OpenShift Container Platform. Việc triển khai hệ điều hành cho các máy chủ trong cụm chính là một phần của quy trình cài đặt OpenShift Container Platform. Hãy tham khảo mục [Sự khác biệt giữa OpenShift Container Platform 3 và 4](https://docs.redhat.com/en/documentation/openshift_container_platform/4.22/html-single/migrating_from_version_3_to_4/#migration-comparing-ocp-3-4).

Do bạn cần cung cấp (provision) các máy chủ như một phần của quy trình cài đặt cụm OpenShift Container Platform, nên bạn không thể nâng cấp trực tiếp một cụm OpenShift Container Platform 3 lên phiên bản 4. Thay vào đó, bạn phải tạo một cụm OpenShift Container Platform 4 mới, rồi di chuyển các khối lượng công việc (workload) từ cụm OpenShift Container Platform 3 cũ sang cụm mới này. Để biết thêm thông tin về quá trình di chuyển, hãy xem phần [Tổng quan về việc di chuyển từ OpenShift Container Platform 3 sang 4](https://docs.redhat.com/en/documentation/openshift_container_platform/4.22/html-single/migrating_from_version_3_to_4/#migration-from-version-3-to-4-overview). Vì bắt buộc phải thực hiện di chuyển sang OpenShift Container Platform 4, bạn có thể sử dụng bất kỳ quy trình cài đặt cụm tiêu chuẩn nào dành cho môi trường sản xuất (production) để tạo cụm mới của mình.

## II.1.3. Bạn có muốn sử dụng các thành phần hiện có trong cụm (cluster) của mình không?
Vì hệ điều hành là một phần không thể thiếu của OpenShift Container Platform, nên cách đơn giản nhất là để chương trình cài đặt OpenShift Container Platform tự thiết lập toàn bộ cơ sở hạ tầng. Phương thức này được gọi là "cài đặt với cơ sở hạ tầng do trình cài đặt cung cấp" (installer-provisioned infrastructure). Trong kiểu cài đặt này, dù bạn có thể cung cấp một số thành phần cơ sở hạ tầng sẵn có cho cụm, thì chương trình cài đặt vẫn sẽ triển khai tất cả các máy (nodes) cần thiết cho cụm ngay từ giai đoạn đầu.

Bạn có thể triển khai một cụm theo phương thức này trên AWS, Azure, Azure Stack Hub, Google Cloud hoặc Nutanix mà không cần chỉ định bất kỳ tùy chỉnh nào cho cụm hoặc các máy thành phần bên dưới.

Nếu cần thực hiện các cấu hình cơ bản cho cụm (chẳng hạn như chọn loại máy ảo/instance cho các máy trong cụm), bạn có thể tùy chỉnh quá trình cài đặt trên AWS, Azure, Google Cloud hoặc Nutanix.

Đối với các cụm sử dụng cơ sở hạ tầng do trình cài đặt cung cấp, bạn có thể tận dụng VPC hiện có trên AWS, vNet trên Azure hoặc VPC trên Google Cloud. Bạn cũng có thể tái sử dụng một phần cơ sở hạ tầng mạng sẵn có, giúp cụm trên AWS, Azure hoặc Google Cloud tương thích với các dải địa chỉ IP hiện tại trong môi trường của bạn, đồng thời tích hợp được với các cấu hình MTU và VXLAN đang được sử dụng. Nếu đã có sẵn tài khoản và thông tin xác thực trên các nền tảng đám mây này, bạn có thể tiếp tục sử dụng chúng; tuy nhiên, có thể cần điều chỉnh lại tài khoản để đảm bảo có đủ các quyền hạn cần thiết cho việc cài đặt các cụm OpenShift Container Platform.

Bạn cũng có thể sử dụng phương thức này để tạo các máy ảo (machine instances) trên phần cứng của mình đối với các nền tảng như vSphere hoặc môi trường máy vật lý (bare metal). Ngoài ra, đối với vSphere, bạn còn có thể tùy chỉnh thêm các tham số mạng trong quá trình cài đặt.

Đối với một số trường hợp sử dụng cơ sở hạ tầng do trình cài đặt cung cấp—ví dụ như trên nền tảng VMware vSphere hoặc môi trường máy vật lý—lưu lượng truy cập bên ngoài đi vào địa chỉ IP ảo (VIP) của Ingress sẽ không được cân bằng tải giữa các bản sao (replicas) mặc định của IngressController. Trong các trường hợp này, nếu dự kiến lưu lượng truy cập sẽ vượt quá hiệu năng xử lý cơ bản của bộ định tuyến IngressController, bạn bắt buộc phải cấu hình một bộ cân bằng tải (load balancer) bên ngoài. Việc cấu hình bộ cân bằng tải bên ngoài sẽ giúp đạt được hiệu năng tương ứng với nhiều bản sao IngressController cùng hoạt động. Để biết thêm thông tin về hiệu năng cơ sở của IngressController, hãy xem phần Hiệu năng cơ sở của Ingress Controller (bộ định tuyến). Để biết thêm thông tin về cách cấu hình bộ cân bằng tải (load balancer) bên ngoài, hãy xem phần Cấu hình bộ cân bằng tải do người dùng quản lý.

Nếu muốn tận dụng cơ sở hạ tầng đám mây hiện có, bạn có thể thực hiện cài đặt trên cơ sở hạ tầng do người dùng cung cấp (user-provisioned infrastructure). Với phương thức này, bạn sẽ tự triển khai các máy chủ cần thiết cho cụm (cluster) trong quá trình cài đặt. Nếu thực hiện cài đặt trên cơ sở hạ tầng do người dùng cung cấp trên AWS, Azure hoặc Azure Stack Hub, bạn có thể sử dụng các mẫu (template) được cung cấp để hỗ trợ thiết lập tất cả các thành phần cần thiết. Bạn cũng có thể sử dụng lại VPC dùng chung trên Google Cloud. Ngoài ra, bạn có thể sử dụng phương thức cài đặt không phụ thuộc vào nhà cung cấp (provider-agnostic) để triển khai cụm trên các nền tảng đám mây khác.

Bạn cũng có thể thực hiện cài đặt trên cơ sở hạ tầng do người dùng cung cấp ngay trên phần cứng hiện có của mình. Nếu sử dụng RHOSP, IBM Z® hoặc IBM® LinuxONE (bao gồm cả cấu hình với RHEL KVM hoặc trong LPAR), IBM Power, hoặc vSphere, hãy tuân theo các hướng dẫn cài đặt dành riêng cho từng nền tảng để triển khai cụm. Nếu sử dụng các loại phần cứng khác được hỗ trợ, hãy thực hiện theo quy trình cài đặt trên phần cứng vật lý (bare metal). Đối với một số nền tảng như vSphere và phần cứng vật lý, bạn còn có thể tùy chỉnh thêm các tham số mạng trong quá trình cài đặt.

## II.1.4. Bạn có cần tăng cường bảo mật cho cụm (cluster) của mình không?
Nếu sử dụng phương pháp cài đặt do người dùng cung cấp (user-provisioned installation), bạn có thể định cấu hình proxy cho cụm của mình. Các hướng dẫn chi tiết đã được bao gồm trong từng quy trình cài đặt.

Nếu muốn ngăn chặn việc các điểm cuối (endpoint) của cụm trên nền tảng đám mây công cộng (public cloud) bị lộ ra bên ngoài, bạn có thể triển khai một cụm riêng tư (private cluster) bằng cơ sở hạ tầng do trình cài đặt cung cấp (installer-provisioned infrastructure) trên AWS, Azure hoặc Google Cloud.

Nếu cần cài đặt cụm trong môi trường bị hạn chế truy cập internet (ví dụ: mạng bị ngắt kết nối hoặc mạng bị hạn chế), bạn có thể [sao chép (mirror) các gói cài đặt](disconnected-environments.md) và thực hiện cài đặt từ các gói đó. Hãy làm theo hướng dẫn chi tiết dành cho việc cài đặt trên cơ sở hạ tầng do người dùng cung cấp trong các mạng bị hạn chế đối với các nền tảng: AWS, Google Cloud, IBM Z® hoặc IBM® LinuxONE, IBM Z® hoặc IBM® LinuxONE với RHEL KVM, IBM Z® hoặc IBM® LinuxONE trong LPAR, IBM Power®, vSphere hoặc máy chủ vật lý (bare metal). Bạn cũng có thể cài đặt cụm trong mạng bị hạn chế bằng cơ sở hạ tầng do trình cài đặt cung cấp, thông qua các hướng dẫn chi tiết dành cho AWS, Google Cloud, IBM Cloud®, Nutanix, RHOSP và vSphere.

Nếu cần triển khai cụm tại các khu vực như AWS GovCloud, AWS China hoặc Azure Government, bạn có thể định cấu hình các khu vực tùy chỉnh này trong quá trình cài đặt bằng cơ sở hạ tầng do trình cài đặt cung cấp.

Ngoài ra, trong quá trình cài đặt, bạn cũng có thể định cấu hình các máy trong cụm để sử dụng các thư viện mật mã RHEL đã được gửi tới NIST để thẩm định theo tiêu chuẩn FIPS 140-2/140-3.

> **Quan trọng:** Khi chạy Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã RHEL đã được gửi tới NIST để thẩm định theo tiêu chuẩn FIPS 140-2/140-3; lưu ý rằng điều này chỉ áp dụng trên các kiến trúc x86_64, ppc64le và s390x.

## II.2. Chuẩn bị cụm (cluster) cho người dùng sau khi cài đặt
Cấu hình cụm trong môi trường thực tế (production) để người dùng có thể truy cập.

Một số cấu hình không bắt buộc phải có để cài đặt cụm, nhưng được khuyến nghị thực hiện trước khi người dùng bắt đầu truy cập. Bạn có thể tùy chỉnh chính cụm đó thông qua việc điều chỉnh các Operator tạo nên cụm, đồng thời tích hợp cụm với các hệ thống cần thiết khác, chẳng hạn như nhà cung cấp danh tính (identity provider).

Đối với một cụm trong môi trường thực tế, bạn cần cấu hình các thành phần tích hợp sau:

* Lưu trữ bền vững (persistent storage)
* Trinh cung cấp danh tính (identity provider)
* Giám sát các thành phần cốt lõi của OpenShift Container Platform

## II.3. Chuẩn bị cụm (cluster) cho các khối lượng công việc (workload)
Tùy thuộc vào nhu cầu của các khối lượng công việc, bạn có thể cần thực hiện thêm một số bước chuẩn bị cho cụm trước khi triển khai ứng dụng.

Chẳng hạn, sau khi đã chuẩn bị cơ sở hạ tầng để hỗ trợ chiến lược xây dựng ứng dụng, bạn có thể cần thực hiện các thiết lập bổ sung cho những khối lượng công việc đòi hỏi độ trễ thấp hoặc cần bảo vệ các khối lượng công việc nhạy cảm. Bạn cũng có thể cấu hình tính năng giám sát cho các khối lượng công việc của ứng dụng.

Nếu dự định chạy các khối lượng công việc trên Windows, bạn bắt buộc phải bật tính năng mạng lai (hybrid networking) với OVN-Kubernetes trong quá trình cài đặt; tính năng này không thể được kích hoạt sau khi cụm đã được cài đặt xong.
