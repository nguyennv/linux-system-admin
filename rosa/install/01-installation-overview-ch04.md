Cài đặt OpenShift - Tổng quan về cài đặt
----------------------------------------

# IV. Hỗ trợ mật mã học FIPS
Bạn có thể cài đặt cụm OpenShift Container Platform ở chế độ FIPS.

OpenShift Container Platform được thiết kế để hỗ trợ FIPS. Khi chạy trên Red Hat Enterprise Linux (RHEL) hoặc Red Hat Enterprise Linux CoreOS (RHCOS) được khởi động ở chế độ FIPS, các thành phần cốt lõi của OpenShift Container Platform sẽ sử dụng các thư viện mật mã của RHEL—những thư viện đã được gửi tới NIST để đánh giá theo tiêu chuẩn FIPS 140-2/140-3 trên các kiến trúc x86_64, ppc64le và s390x.

Để biết thêm thông tin về chương trình đánh giá của NIST, hãy xem mục "Cryptographic Module Validation Program" (Chương trình Đánh giá Mô-đun Mật mã) trong phần Tài nguyên bổ sung. Để cập nhật trạng thái mới nhất từ NIST đối với từng phiên bản thư viện mật mã RHEL đã được gửi để đánh giá, hãy xem mục "Compliance Activities and Government Standards" (Các hoạt động tuân thủ và tiêu chuẩn chính phủ) trong phần Tài nguyên bổ sung.

> **Quan trọng:** Để bật chế độ FIPS cho cụm của mình, bạn phải chạy chương trình cài đặt từ một máy tính sử dụng hệ điều hành RHEL 9 đã được cấu hình để hoạt động ở chế độ FIPS; đồng thời, bạn phải sử dụng phiên bản chương trình cài đặt có hỗ trợ FIPS. Hãy tham khảo phần có tiêu đề "Obtaining a FIPS-capable installation program using `oc adm extract`" (Cách lấy chương trình cài đặt hỗ trợ FIPS bằng lệnh `oc adm extract`).

Để biết thêm thông tin về cách cấu hình chế độ FIPS trên RHEL, hãy xem mục "Installing the system in FIPS mode" (Cài đặt hệ thống ở chế độ FIPS) trong phần Tài nguyên bổ sung.

Đối với các máy chạy Red Hat Enterprise Linux CoreOS (RHCOS) trong cụm, thay đổi này sẽ được áp dụng tại thời điểm triển khai máy, dựa trên trạng thái của một tùy chọn trong tệp `install-config.yaml` (tệp quy định các tùy chọn cụm mà người dùng có thể thay đổi trong quá trình triển khai). Đối với các máy chạy Red Hat Enterprise Linux (RHEL), bạn phải bật chế độ FIPS ngay khi cài đặt hệ điều hành trên các máy mà bạn dự định sử dụng làm máy worker (worker machine).

Vì FIPS phải được bật trước khi hệ điều hành mà cụm của bạn sử dụng khởi động lần đầu tiên, nên bạn không thể bật FIPS sau khi đã triển khai cụm.

## IV.1. Lấy chương trình cài đặt hỗ trợ FIPS bằng lệnh `oc adm extract`
Để cài đặt cụm OpenShift Container Platform ở chế độ FIPS, hãy trích xuất tệp nhị phân cài đặt hỗ trợ FIPS từ hình ảnh phát hành (release image) bằng cách sử dụng giao diện dòng lệnh OpenShift (oc).

Sau khi trích xuất tệp nhị phân, hãy tiến hành cài đặt cụm, trong đó thay thế tất cả các lệnh `openshift-install` bằng `openshift-install-fips`.

**Điều kiện tiên quyết**  
Bạn đã cài đặt giao diện dòng lệnh OpenShift (oc) phiên bản 4.16 trở lên.

**Quy trình**  
1. Trích xuất tệp nhị phân hỗ trợ FIPS từ chương trình cài đặt bằng cách chạy lệnh sau:

```shell
oc adm release extract --registry-config "${pullsecret_file}" \
  --command=openshift-install-fips --to "${extract_dir}" ${RELEASE_IMAGE}
```

trong đó:

`<pullsecret_file>` Chỉ định tên tệp chứa thông tin xác thực (pull secret) của bạn.

`<extract_dir>` Chỉ định thư mục nơi bạn muốn giải nén tệp nhị phân.
`<RELEASE_IMAGE>` Chỉ định URL trên Quay.io của phiên bản OpenShift Container Platform mà bạn đang sử dụng.

2. Tiến hành cài đặt cụm, trong đó thay thế tất cả các lần xuất hiện của lệnh `openshift-install` bằng `openshift-install-fips`.

## IV.3. Lấy chương trình cài đặt hỗ trợ FIPS thông qua mirror OpenShift công khai
Để cài đặt cụm OpenShift Container Platform ở chế độ FIPS, hãy tải xuống tệp nhị phân cài đặt hỗ trợ FIPS từ mirror OpenShift công khai.

Sau khi có tệp nhị phân này, hãy tiến hành cài đặt cụm, trong đó thay thế tất cả các tệp nhị phân `openshift-install` bằng `openshift-install-fips`.

**Điều kiện tiên quyết**  
Bạn có quyền truy cập internet.

**Quy trình**  
1. Tải xuống chương trình cài đặt từ địa chỉ https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest-4.18/openshift-install-rhel9-amd64.tar.gz. 
2. Giải nén chương trình cài đặt. Ví dụ: trên máy tính sử dụng hệ điều hành Linux, hãy chạy lệnh sau:

```shell
tar -xvf openshift-install-rhel9-amd64.tar.gz
```

3. Tiến hành cài đặt cụm, trong đó thay thế tất cả các lần xuất hiện của lệnh `openshift-install` bằng `openshift-install-fips`.

## IV.4. Xác thực FIPS trong OpenShift Container Platform
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

## IV.5. Hỗ trợ FIPS trong các thành phần mà cụm (cluster) sử dụng
Mặc dù bản thân cụm OpenShift Container Platform sử dụng các mô-đun đã được xác thực FIPS hoặc đang trong quá trình xác thực (Modules In Process), bạn cần đảm bảo rằng các hệ thống hỗ trợ cụm OpenShift Container Platform của bạn cũng sử dụng các mô-đun đã được xác thực FIPS hoặc đang trong quá trình xác thực cho các tác vụ mật mã.

**etcd**  
Để đảm bảo rằng các thông tin bí mật (secrets) lưu trữ trong etcd sử dụng cơ chế mã hóa đã được xác thực FIPS hoặc đang trong quá trình xác thực, hãy khởi động nút (node) ở chế độ FIPS. Sau khi cài đặt cụm ở chế độ FIPS, bạn có thể mã hóa dữ liệu etcd bằng thuật toán mật mã aes-cbc đã được FIPS phê duyệt.

**Lưu trữ (Storage)**  
Đối với lưu trữ cục bộ (local storage), hãy sử dụng tính năng mã hóa đĩa do RHEL cung cấp hoặc giải pháp Container Native Storage có sử dụng tính năng mã hóa đĩa của RHEL. Bằng cách lưu trữ toàn bộ dữ liệu trong các ổ đĩa (volumes) được mã hóa thông qua cơ chế của RHEL và kích hoạt chế độ FIPS cho cụm, cả dữ liệu lưu trữ (data at rest) lẫn dữ liệu đang truyền tải (data in motion/network data) đều sẽ được bảo vệ bởi các mô-đun mã hóa đã được xác thực FIPS hoặc đang trong quá trình xác thực. Bạn có thể cấu hình cụm để mã hóa hệ thống tệp gốc (root filesystem) của từng nút. Để biết thêm thông tin, hãy xem phần "Tùy chỉnh các nút" (Customizing nodes) trong mục Tài nguyên bổ sung.

**Môi trường thực thi (Runtimes)**  
Để đảm bảo các container nhận biết được rằng chúng đang chạy trên một máy chủ sử dụng các mô-đun mật mã đã được xác thực FIPS hoặc đang trong quá trình xác thực, hãy sử dụng CRI-O để quản lý các môi trường thực thi của bạn.

## IV.6. Cài đặt cụm ở chế độ FIPS
Để cài đặt một cụm ở chế độ FIPS, hãy làm theo các hướng dẫn cài đặt cụm tùy chỉnh trên cơ sở hạ tầng mà bạn chọn. Đảm bảo rằng bạn đã thiết lập `fips: true` trong tệp `install-config.yaml` trước khi triển khai cụm của mình.

> **Lưu ý:** Nếu bạn đang sử dụng Azure File storage, bạn không thể bật chế độ FIPS.

Để áp dụng mã hóa `AES CBC` cho kho dữ liệu etcd của bạn, hãy thực hiện theo quy trình "Mã hóa dữ liệu etcd" sau khi đã cài đặt cụm (cluster) của mình.
