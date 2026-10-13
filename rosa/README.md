Quản trị Red Hat OpenShift
--------------------------

# Quản trị Red Hat OpenShift I: Vận hành Cụm Môi trường Sản xuất
1. [Chương 1. Giới thiệu về Kubernetes và OpenShift](do180/ch01.md)  
Xác định các dịch vụ chính của cụm Kubernetes và nền tảng OpenShift, đồng thời giám sát chúng bằng cách sử dụng bảng điều khiển web.
2. [Chương 2, Các giao diện dòng lệnh và API của Kubernetes và OpenShift](do180/ch02.md)  
Truy cập cụm OpenShift bằng dòng lệnh và truy vấn các tài nguyên Kubernetes API của cụm để đánh giá tình trạng hoạt động của cụm đó.
3. [Chương 3, Chạy ứng dụng dưới dạng Container và Pod](do180/ch03.md)  
Chạy và khắc phục sự cố các ứng dụng được đóng gói trong container dưới dạng các pod Kubernetes không được quản lý.
4. [Chương 4, Triển khai các ứng dụng được quản lý và có kết nối mạng trên Kubernetes](do180/ch04.md)  
Triển khai các ứng dụng và cho phép truy cập mạng từ cả bên trong lẫn bên ngoài cụm Kubernetes.
5. [Chương 5, Quản lý lưu trữ cho cấu hình và dữ liệu ứng dụng](do180/ch05.md)  
Tách biệt cấu hình ứng dụng ra khỏi mã nguồn và đưa vào các tài nguyên Kubernetes, đồng thời cấp phát các volume lưu trữ cho các tệp dữ liệu cần duy trì.
6. [Chương 6, Cấu hình ứng dụng để đảm bảo độ tin cậy](do180/ch06.md)  
Cấu hình các ứng dụng để hoạt động với Kubernetes nhằm đảm bảo tính sẵn sàng cao và khả năng phục hồi.
7. [Chương 7, Quản lý cập nhật ứng dụng](do180/ch07.md)  
Quản lý việc cập nhật và hoàn tác (rollback) mã nguồn cũng như cấu hình ứng dụng theo quy trình có thể tái lập.

# Quản trị Red Hat OpenShift II: Cấu hình Cụm Môi trường Sản xuất
1. [Chương 1, Quản lý tài nguyên theo phương thức khai báo](do180/ch01.md)  
Triển khai và cập nhật các ứng dụng từ các tệp định nghĩa tài nguyên (resource manifests) được tham số hóa cho các môi trường đích khác nhau.
2. [Chương 2, Triển khai các ứng dụng đã đóng gói](do180/ch02.md)  
Triển khai và cập nhật các ứng dụng từ các tệp định nghĩa tài nguyên (resource manifest) được đóng gói để chia sẻ và phân phối.
3. [Chương 3, Xác thực và Phân quyền](do180/ch03.md)  
Cấu hình xác thực với nhà cung cấp danh tính HTPasswd và gán vai trò cho người dùng cũng như các nhóm.
4. [Chương 4, Bảo mật mạng](do180/ch04.md)  
Bảo vệ lưu lượng mạng giữa các ứng dụng bên trong và bên ngoài cụm.
5. [Chương 5, Phơi bày các ứng dụng không phải HTTP/SNI](do180/ch05.md)  
Cho phép truy cập từ bên ngoài vào các ứng dụng mà không cần sử dụng ingress controller.
6. [Chương 6, Cho phép nhà phát triển tự phục vụ](do180/ch06.md)  
Cấu hình các cụm để hỗ trợ quy trình tự phục vụ an toàn cho các nhà phát triển từ nhiều nhóm, đồng thời vô hiệu hóa tính năng tự phục vụ nếu nhân viên vận hành là người phải cấp phát dự án.
7. [Chương 7, Quản lý Kubernetes Operator](do180/ch07.md)  
Cài đặt và cập nhật các Operator do Operator Lifecycle Manager và Cluster Version Operator quản lý.
7. [Chương 8, Bảo mật ứng dụng](do180/ch08.md)  
Chạy các ứng dụng yêu cầu quyền nâng cao hoặc đặc quyền đặc biệt từ hệ điều hành máy chủ hoặc Kubernetes.
7. [Chương 9, Các bản cập nhật OpenShift](do180/ch09.md)  
Cập nhật cụm OpenShift và giảm thiểu gián đoạn đối với các ứng dụng đã được triển khai.
