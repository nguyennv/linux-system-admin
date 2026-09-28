# VPS, Bare Metal và Cloud: Hướng dẫn so sánh chi tiết
## Giới thiệu
Việc lựa chọn cơ sở hạ tầng lưu trữ (hosting) phù hợp là một trong những quyết định quan trọng nhất đối với doanh nghiệp hoặc dự án của bạn. Cho dù bạn đang triển khai một ứng dụng web quy mô nhỏ, vận hành các khối lượng công việc cấp doanh nghiệp hay xây dựng một nền tảng SaaS có khả năng mở rộng, thì việc hiểu rõ sự khác biệt giữa VPS (Máy chủ riêng ảo), máy chủ Bare-metal và cơ sở hạ tầng Cloud là điều cần thiết để đưa ra quyết định sáng suốt.

Hướng dẫn toàn diện này sẽ đi sâu vào kiến trúc kỹ thuật, đặc điểm hiệu năng, các yếu tố chi phí và các trường hợp sử dụng lý tưởng cho từng giải pháp lưu trữ. Chúng tôi sẽ phân tích chi tiết các công nghệ ảo hóa như KVM/QEMU, xem xét các tình huống thực tế và cung cấp những thông tin hữu ích, thiết thực để giúp bạn chọn ra cơ sở hạ tầng tối ưu nhất cho các yêu cầu cụ thể của mình.

Sau khi tham khảo hướng dẫn này, bạn sẽ nắm bắt được các yếu tố đánh đổi về mặt kỹ thuật, tác động đến hiệu năng cũng như các cân nhắc mang tính chiến lược tạo nên sự khác biệt giữa ba mô hình lưu trữ này; từ đó, bạn có thể đưa ra các quyết định về cơ sở hạ tầng dựa trên dữ liệu, đảm bảo sự phù hợp với các mục tiêu kỹ thuật và kinh doanh của mình.

## Tìm hiểu về VPS (Máy chủ riêng ảo)
### VPS là gì?
Máy chủ riêng ảo (VPS) là một phiên bản máy chủ ảo hóa chạy trên phần cứng vật lý được chia sẻ với các máy ảo khác. Thông qua công nghệ hypervisor (thường là KVM/QEMU, Xen hoặc VMware), một máy chủ vật lý duy nhất được phân chia thành nhiều môi trường ảo biệt lập; mỗi môi trường hoạt động như một máy chủ độc lập với các tài nguyên chuyên dụng.

### Kiến trúc VPS và Công nghệ ảo hóa
VPS hosting tận dụng công nghệ ảo hóa phần cứng để tạo ra các môi trường biệt lập:

**KVM (Kernel-based Virtual Machine)** là công nghệ ảo hóa phổ biến nhất cho dịch vụ lưu trữ VPS:
* Kiểm tra xem hệ thống của bạn có hỗ trợ ảo hóa hay không.

```console
egrep -c '(vmx|svm)' /proc/cpuinfo
# Nếu kết quả đầu ra > 0, tính năng ảo hóa được hỗ trợ.
```

* Xác minh các mô-đun KVM đã được nạp.

```console
lsmod | grep kvm
# Kết quả hiển thị cần là: kvm_intel hoặc kvm_amd
```

Mỗi VPS hoạt động với:

* Các nhân CPU chuyên dụng (hoặc phân bổ thời gian CPU)
* Phân bổ RAM được đảm bảo
* Bộ nhớ lưu trữ biệt lập (ổ đĩa ảo)
* Địa chỉ IP riêng
* Hệ điều hành được cài đặt độc lập

### Mô hình phân bổ tài nguyên VPS
Các nhà cung cấp VPS sử dụng các chiến lược phân bổ khác nhau:

**Nguồn lực được đảm bảo:**

```console
# Kiểm tra dung lượng RAM được đảm bảo trên VPS của bạn.
free -h

# Xem phân bổ CPU
nproc
lscpu

# Kiểm tra việc phân bổ đĩa
df -h
```

**Cân nhắc về việc phân bổ quá mức:** Một số nhà cung cấp phân bổ quá mức CPU và RAM, nghĩa là tổng tài nguyên được phân bổ vượt quá dung lượng vật lý. Điều này hoạt động được vì không phải tất cả các máy ảo đều đạt đỉnh điểm cùng một lúc.

### Ưu điểm của VPS
1. Khả năng mở rộng với chi phí hiệu quả
    * Chi phí thấp hơn so với máy chủ chuyên dụng
    * Dễ dàng nâng cấp tài nguyên mà không cần chuyển đổi hệ thống
    * Chỉ trả phí cho những tài nguyên bạn thực sự cần
2. Quyền truy cập Root và toàn quyền kiểm soát

```console
# Quyền truy cập root đầy đủ cho phép tùy biến hoàn toàn.
sudo su -

# Cài đặt bất kỳ phần mềm hoặc dịch vụ nào
dnf -y install docker
systemctl enable docker
```

3. Sự cô lập và bảo mật: Mỗi VPS được cô lập thông qua công nghệ ảo hóa:

```console
# VPS sử dụng cơ chế cách ly không gian tên.
ls /proc/*/ns/
# Mỗi tiến trình có các không gian tên riêng.

# cgroups kiểm soát việc sử dụng tài nguyên.
cat /proc/cgroups
```

4. Các instance VPS triển khai nhanh có thể được thiết lập chỉ trong vài phút, thay vì hàng giờ hay hàng ngày.

### Nhược điểm của VPS
1. Chia sẻ tài nguyên: Phần cứng vật lý được chia sẻ, điều này có thể ảnh hưởng đến:
    * Hiệu năng I/O trong quá trình hoạt động của thiết bị lân cận
    * Lưu lượng mạng trong thời gian sử dụng cao điểm
    * Khả năng sử dụng CPU nếu nhà cung cấp vượt quá tải
2. Sự biến động về hiệu suất

```console
# Kiểm tra sự biến thiên hiệu năng I/O
fio --name=random-write --ioengine=libaio --rw=randwrite \
    --bs=4k --size=1G --numjobs=1 --iodepth=16 \
    --runtime=60 --time_based --end_fsync=1
```

3. Khả năng tùy biến phần cứng hạn chế - Không thể thay đổi:
    * Mẫu (model) hoặc kiến trúc CPU
    * Loại hoặc tốc độ RAM
    * Bộ điều khiển lưu trữ
    * Card giao tiếp mạng

### Các trường hợp sử dụng VPS lý tưởng
**Dịch vụ lưu trữ web và các ứng dụng:**

```console
# Hoàn hảo cho các stack LAMP/LEMP.
dnf -y install nginx mysql-server php-fpm
systemctl enable nginx mysql

```

**Môi trường Phát triển và Kiểm thử:**

```console
# Triển khai nhanh môi trường
docker run -d --name dev-env alpine:latest
```

**Cơ sở dữ liệu quy mô nhỏ đến trung bình:**

```console
# MySQL/PostgreSQL với mức tải trung bình
dnf -y install postgresql-14
systemctl enable postgresql
```

**Microservices và các ứng dụng được đóng gói trong container:**

```console
# Docker và điều phối container
dnf -y install docker docker-compose
```

## Tìm hiểu về máy chủ Bare-metal
### Bare metal là gì?
Máy chủ Bare-metal là máy chủ vật lý chuyên dụng được cấp phát toàn bộ cho một khách hàng duy nhất. Khác với VPS, loại máy chủ này không có lớp ảo hóa; bạn có quyền truy cập trực tiếp vào toàn bộ tài nguyên phần cứng mà không phải chia sẻ với người dùng khác.

### Kiến trúc Bare-metal
**Truy cập phần cứng trực tiếp:**

```console
# Xem phần cứng thực tế
lscpu | grep "Model name"
dmidecode -t processor
dmidecode -t memory

# Kiểm tra các ổ đĩa vật lý
lsblk -d
smartctl -a /dev/sda
```

**Dành riêng toàn bộ tài nguyên:** Mọi thành phần đều dành riêng cho bạn:

* Tất cả các nhân và luồng CPU
* Toàn bộ RAM vật lý
* Tất cả các thiết bị lưu trữ
* Toàn bộ băng thông mạng
* Tất cả các khe cắm PCIe và khả năng mở rộng

### Đặc tính hiệu suất
**Hiệu suất ổn định:**

```console
# Đo hiệu năng CPU
sysbench cpu --cpu-max-prime=20000 run

# Kiểm tra băng thông bộ nhớ
sysbench memory --memory-total-size=10G run

# Đo hiệu năng I/O của ổ đĩa
hdparm -Tt /dev/nvme0n1
```

**Không xảy ra hiệu ứng "hàng xóm ồn ào" (Noisy Neighbor):** Hiệu năng vẫn ổn định do không có người dùng nào khác chia sẻ tài nguyên.

**Tùy biến phần cứng:**

```console
# Cấu hình các mảng RAID
mdadm --create /dev/md0 --level=10 --raid-devices=4 \
      /dev/sda /dev/sdb /dev/sdc /dev/sdd

# Tối ưu hóa kernel cho phần cứng cụ thể
echo "performance" | tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

### Ưu điểm của Bare-metal
1. **Hiệu năng tối đa** – Không có chi phí hao hụt do ảo hóa đồng nghĩa với việc:
    * Hiệu năng CPU 100%
    * Băng thông bộ nhớ tối đa
    * I/O phần cứng trực tiếp
    * Độ trễ thấp nhất có thể
2. Điều khiển phần cứng

```console
# Thiết lập BIOS/UEFI tùy chỉnh
# Tùy chọn cấu hình RAID
# Giám sát phần cứng trực tiếp
ipmitool sensor list
```

3. Tuân thủ và Bảo mật
    * Cách ly vật lý để đáp ứng các yêu cầu quy định
    * Không có lỗ hổng bảo mật ở tầng hypervisor
    * Toàn quyền kiểm soát hệ thống bảo mật
4. Hiệu năng ổn định, có thể dự đoán được

```console
# Kết quả kiểm chuẩn nhất quán
for i in {1..10}; do
    sysbench cpu --cpu-max-prime=20000 run | grep "events per second"
done
```

### Nhược điểm của Bare-metal
1. Chi phí cao hơn
    * Trả phí cho toàn bộ máy chủ bất kể mức độ sử dụng
    * Các cam kết tối thiểu (thường là theo tháng)
    * Chi phí đầu tư ban đầu cao hơn
2. Quy trình triển khai chậm hơn
    * Việc chuẩn bị máy chủ vật lý mất hàng giờ hoặc hàng ngày
    * Cấu hình phần cứng thủ công
    * Thời gian cài đặt và thiết lập hệ điều hành
3. Khả năng mở rộng hạn chế
    * Mở rộng theo chiều dọc đòi hỏi:  
        - Lên kế hoạch nâng cấp phần cứng
        - Lắp đặt các linh kiện vật lý
        - Nguy cơ gián đoạn dịch vụ (downtime)
    * Mở rộng theo chiều ngang đòi hỏi:  
        - Mua sắm máy chủ mới
        - Thiết lập và cấu hình thủ công
4. Tài nguyên cố định
    * Khó hạ cấp
    * Trả phí cho dung lượng không sử dụng
    * Lãng phí tài nguyên khi nhu cầu thấp

### Các trường hợp sử dụng lý tưởng cho Bare Metal
**Tính toán hiệu năng cao:**

* Tính toán khoa học, mô phỏng
* Huấn luyện học máy
* Xử lý dữ liệu lớn

**Cơ sở dữ liệu quy mô lớn:**

* PostgreSQL với dữ liệu quy mô terabyte (TB)
* Các bộ bản sao (replica sets) MongoDB
* Các cụm MySQL xử lý khối lượng giao dịch lớn

**Máy chủ trò chơi:**

* Yêu cầu độ trễ thấp
* Tốc độ cập nhật (tick rate) ổn định
* Tài nguyên chuyên dụng

**Các khối lượng công việc bắt buộc tuân thủ:**

* Chăm sóc sức khỏe - Healthcare (HIPAA)
* Tài chính - Finance (PCI-DSS)
* Chính phủ - Government (FedRAMP)

## Hiểu về cơ sở hạ tầng đám mây
### Đám mây là gì?
Hạ tầng đám mây cung cấp các tài nguyên điện toán theo nhu cầu thông qua mạng lưới các trung tâm dữ liệu được phân bố trên toàn cầu. Khác với VPS truyền thống hay máy chủ vật lý (bare-metal), các nền tảng đám mây mang lại khả năng phân bổ tài nguyên linh hoạt, mức độ tự động hóa cao và mô hình tính phí dựa trên mức độ sử dụng thực tế.

### Các mô hình dịch vụ đám mây
**Cơ sở hạ tầng như một dịch vụ (IaaS):**

```console
# AWS EC2, Google Compute Engine, Azure VMs
# Ví dụ: Khởi chạy instance EC2 thông qua CLI
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t3.medium \
    --key-name my-key
```

**Nền tảng như một Dịch vụ (PaaS):**

* Heroku, Google App Engine, Azure App Service
* Quản lý cơ sở hạ tầng được trừu tượng hóa

**Điện toán không máy chủ:**

* AWS Lambda, Google Cloud Functions
* Không cần quản lý máy chủ

### Các đặc điểm của kiến trúc đám mây
1. Tính linh hoạt và Tự động mở rộng:

```console
# Ví dụ về tự động mở rộng AWS
aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name my-asg \
    --min-size 2 \
    --max-size 10 \
    --desired-capacity 3
```

2. Phân bố địa lý:

```console
# Triển khai trên nhiều khu vực
aws ec2 describe-regions --output table
# Chọn các khu vực dựa trên khoảng cách đến người dùng
```

3. Dịch vụ Quản lý:
    * Cơ sở dữ liệu được quản lý (RDS, Cloud SQL)
    * Kubernetes được quản lý (EKS, GKE, AKS)
    * Lưu trữ đối tượng được quản lý (S3, GCS)

4. Cơ sở hạ tầng dựa trên API:

```console
# Cơ sở hạ tầng dưới dạng mã với OpenTofu
tofu init
tofu plan
tofu apply
```

### Ưu điểm của cơ sở hạ tầng đám mây
1. Khả năng mở rộng không giới hạn
    * Mở rộng theo chiều ngang với bộ cân bằng tải
    * Thêm các instance theo nhu cầu
    * Tự động mở rộng dựa trên các chỉ số
2. Khả dụng trên toàn cầu
    * Triển khai tại nhiều khu vực
    * Tích hợp CDN
    * Khả năng tính toán tại biên (edge computing)
3. Mô hình trả phí theo mức sử dụng
    * Chỉ trả phí cho tài nguyên đã sử dụng
    * Không cần đầu tư ban đầu
    * Tắt tài nguyên khi không cần thiết
4. Hệ sinh thái phong phú. Các dịch vụ tích hợp:
    * Các nền tảng AI/ML
    * Phân tích dữ liệu
    * Điều phối container
    * Điện toán không máy chủ (Serverless)
    * Hàng đợi tin nhắn (Message queues)
    * Giám sát và ghi nhật ký (Monitoring and logging)
5. Tính sẵn sàng cao
    * Cơ chế dự phòng tích hợp
    * Tự động chuyển đổi dự phòng (failover)
    * Cam kết SLA (từ 99,99% trở lên)

### Nhược điểm của cơ sở hạ tầng đám mây
1. Khó lường về chi phí

```console
# Theo dõi chi tiêu cho dịch vụ đám mây
aws ce get-cost-and-usage \
    --time-period Start=2024-01-01,End=2024-01-31 \
    --granularity MONTHLY
```

2. Sự phụ thuộc vào nhà cung cấp
    * API độc quyền
    * Sự phụ thuộc vào dịch vụ
    * Độ phức tạp khi chuyển đổi
3. Độ phức tạp
    * Quá trình học hỏi đòi hỏi nhiều nỗ lực
    * Nhiều điểm tương tác dịch vụ
    * Các mô hình định giá phức tạp
4. Sự biến động về hiệu suất
    * Vẫn sử dụng ảo hóa
    * Độ trễ mạng tới các trung tâm dữ liệu
    * Hạ tầng dùng chung
5. Các thách thức về tuân thủ
    * Các vấn đề về chủ quyền dữ liệu
    * Mô hình trách nhiệm chung
    * Ít quyền kiểm soát hơn so với Bare Metal

### Các trường hợp sử dụng đám mây lý tưởng
**Các ứng dụng web có khả năng mở rộng:**

* Các nền tảng thương mại điện tử
* Các ứng dụng SaaS
* Các hệ thống quản lý nội dung

**Xử lý và phân tích dữ liệu:**

* Các luồng dữ liệu lớn (Big data pipelines)
* Phân tích thời gian thực
* Các tác vụ ETL

**Phát triển và CI/CD:**

* Môi trường kiểm thử tự động
* Máy chủ build
* Môi trường staging

**Các ứng dụng toàn cầu:**

* Triển khai đa khu vực
* Phân phối nội dung hỗ trợ bởi CDN
* Truy cập toàn cầu với độ trễ thấp

## Bảng so sánh chi tiết
### So sánh hiệu năng
| Số liệu đo       | VPS                         | Baremetal                    | Đám mây                           |
|------------------|-----------------------------|------------------------------|-----------------------------------|
| Hiệu năng CPU    | Tốt (được chia sẻ)          | Xuất sắc (dedicated)         | Tốt đến Xuất sắc (tùy trường hợp) |
| Hiệu năng bộ nhớ | Tốt (được đảm bảo)          | Xuất sắc (băng thông đầy đủ) | Tốt (được đảm bảo)                |
| Disk I/O         | Biến đổi (SSD/NVMe)         | Tuyệt vời (có thể tùy chỉnh) | Tốt - Xuất sắc (được quản lý)     |
| Độ trễ mạng      | Thấp                        | Thấp nhất                    | Biến đổi (phụ thuộc vào khu vực)  |
| Tính nhất quán   | Trung bình (hàng xóm ồn ào) | Xuất sắc                     | Trung bình (được ảo hóa)          |

### So sánh chi phí
**Cấu trúc giá VPS:**

* Mức giá VPS phổ biến (theo tháng)
* 2 vCPU, 4GB RAM, 80GB SSD: 10-25 USD/tháng
* 4 vCPU, 8GB RAM, 160GB SSD: 20-50 USD/tháng
* 8 vCPU, 16GB RAM, 320GB SSD: 40-100 USD/tháng

**Cấu trúc giá dịch vụ Bare Metal:**

* Mức giá tham khảo cho máy chủ vật lý (Bare-metal) theo tháng
* Cấu hình cơ bản: 4C/8T, 32GB RAM, 2TB HDD: $80-150/tháng
* Cấu hình tầm trung: 8C/16T, 64GB RAM, 2x1TB NVMe: $150-300/tháng
* Cấu hình cao cấp: 16C/32T, 128GB RAM, 4x2TB NVMe: $300-600/tháng

**Cấu trúc giá dịch vụ đám mây:**

* AWS EC2 theo nhu cầu (tính phí theo giờ)
* t3.medium (2 vCPU, 4GB): $0,0416/giờ ($30/tháng)
* c5.2xlarge (8 vCPU, 16GB): $0,34/giờ ($247/tháng)

* Reserved Instances (cam kết sử dụng 1 năm)
* Giảm giá lên đến 40%

* Spot Instances (giá thay đổi linh hoạt)
* Giảm giá lên đến 90% (có thể bị chấm dứt sử dụng)

### So sánh khả năng mở rộng
**Mở rộng quy mô VPS:**

* Mở rộng theo chiều dọc (nâng cấp gói dịch vụ)
* Thường yêu cầu:
    1. Tắt instance
    2. Nâng cấp gói dịch vụ
    3. Khởi động lại instance
* Mở rộng theo chiều ngang
* Thủ công: Triển khai VPS mới
* Cấu hình cân bằng tải

**Mở rộng quy mô Bare-metal:**

* Mở rộng theo chiều dọc (Vertical scaling)
    * Yêu cầu nâng cấp phần cứng vật lý
    * Thời gian ngừng hoạt động đáng kể
* Mở rộng theo chiều ngang (Horizontal scaling)
    * Mua thêm máy chủ
    * Cấu hình thủ công
    * Thiết lập bộ cân bằng tải

**Mở rộng quy mô đám mây:**  

```console
# Mở rộng theo chiều dọc
aws ec2 modify-instance-attribute \
    --instance-id i-1234567890abcdef0 \
    --instance-type t3.large

# Tự động mở rộng theo chiều ngang
aws autoscaling put-scaling-policy \
    --auto-scaling-group-name my-asg \
    --policy-name scale-up \
    --scaling-adjustment 2
```

### Quản lý và Bảo trì
**Quản lý VPS:**

* Hệ điều hành và ứng dụng do người dùng tự quản lý
* Sao lưu tự động (thường được bao gồm)
* Bảng điều khiển (đôi khi có)

```console
# Cập nhật hệ thống
dnf upgrade -y

# Giám sát
systemctl status
htop
```

**Quản lý máy chủ vật lý:**

* Chịu trách nhiệm toàn diện về:
    - Giám sát phần cứng
    - Quản lý hệ điều hành
    - Cập nhật bản vá bảo mật
    - Triển khai sao lưu

```console
# Giám sát phần cứng bằng IPMI
ipmitool sensor list
ipmitool sel list

# Giải pháp sao lưu tùy chỉnh
rsync -avz /data/ backup-server:/backups/
```

**Quản lý đám mây:**

* Quản lý dựa trên API
* Các tùy chọn tự động hóa đa dạng
* Tích hợp dịch vụ được quản lý

```console
# Cơ sở hạ tầng dưới dạng mã
terraform apply
ansible-playbook deploy.yml

# Sao lưu tự động
aws backup create-backup-plan --backup-plan file://plan.json
```

## Khung ra quyết định
### Chọn VPS khi:
1. Ngân sách hạn chế (khoảng 10–100 USD/tháng)
2. Yêu cầu hiệu năng ở mức vừa phải (web hosting, các ứng dụng nhỏ)
3. Cần khả năng mở rộng linh hoạt (nâng cấp nhanh chóng)
4. Yêu cầu triển khai nhanh (tính bằng phút)
5. Phục vụ học tập và thử nghiệm (thử nghiệm rủi ro thấp)

**Ví dụ về tình huống:**

* Website doanh nghiệp nhỏ
    - 1.000–10.000 lượt truy cập mỗi ngày
    - WordPress hoặc CMS tùy chỉnh
    - Máy chủ email
    - Môi trường phát triển
* VPS đề xuất:
* 4 vCPU, 8GB RAM, 160GB SSD
* Chi phí: 30–60 USD/tháng

### Chọn Bare Metal khi:
1. Yêu cầu hiệu năng tối đa (cơ sở dữ liệu, tính toán)
2. Khối lượng công việc ổn định (mức sử dụng tài nguyên có thể dự đoán được)
3. Yêu cầu tuân thủ quy định (HIPAA, PCI-DSS)
4. Cần tùy chỉnh phần cứng (CPU, GPU cụ thể)
5. Tính dự báo được về chi phí là yếu tố quan trọng (chi phí cố định hàng tháng)

**Ví dụ về tình huống:**

* Máy chủ cơ sở dữ liệu có lưu lượng truy cập cao
    - PostgreSQL với cơ sở dữ liệu dung lượng 500GB
    - Hơn 10.000 kết nối đồng thời
    - Yêu cầu thời gian truy vấn dưới 1 mili giây
    - Yêu cầu tuân thủ quy định (HIPAA)
* Cấu hình Bare-metal đề xuất:
* 16 nhân/32 luồng, 128GB RAM, 4x2TB NVMe RAID10
* Chi phí: 400-600 USD/tháng

### Chọn giải pháp đám mây khi:
1. Khối lượng công việc biến động (lưu lượng truy cập tăng đột biến)
2. Cần phân phối toàn cầu (đa khu vực)
3. Yêu cầu khả năng mở rộng nhanh chóng (tự động mở rộng)
4. Mong muốn sử dụng các dịch vụ được quản lý (RDS, S3, v.v.)
5. Chú trọng vào DevOps/tự động hóa (Cơ sở hạ tầng dưới dạng mã - IaC)

**Ví dụ về tình huống:**

* Nền tảng thương mại điện tử
    - Lưu lượng truy cập biến động (cao điểm theo mùa)
    - Cơ sở khách hàng toàn cầu
    - Kiến trúc microservices
    - Triển khai liên tục (Continuous Deployment)
* Cấu hình Cloud đề xuất:
    - Nhóm tự động mở rộng (Auto-scaling group: 2-20 instance)
    - Cơ sở dữ liệu được quản lý (RDS)
    - CDN (CloudFront/CloudFlare)
    - Lưu trữ đối tượng (Object storage - S3)
* Chi phí: 200 - 2.000 USD/tháng (biến động)

## Phương pháp lai
### VPS + Đám mây
* Sử dụng VPS cho:
    - Các dịch vụ chạy liên tục (cơ sở dữ liệu)
    - Control plane (lớp điều khiển)
* Sử dụng Cloud cho:
    - Khả năng mở rộng tức thời (burst capacity)
    - Lưu trữ đối tượng (object storage)
    - CDN
* Ví dụ về kiến trúc:
* VPS: Cơ sở dữ liệu PostgreSQL + Máy chủ API
* Cloud: S3 để lưu trữ dữ liệu đa phương tiện + CDN CloudFront

### Bare-metal + Đám mây
* Sử dụng máy chủ vật lý (Baremetal) cho:
    - Máy chủ cơ sở dữ liệu cốt lõi
    - Xử lý dữ liệu nhạy cảm
* Sử dụng điện toán đám mây (Cloud) cho:
    - Máy chủ ứng dụng
    - Môi trường phát triển
    - Hệ thống phân tích dữ liệu (Analytics pipeline)

```console
# Kết nối qua VPN
strongswan configuration
ipsec tunnel between Baremetal <-> Cloud
```

### Chiến lược đa đám mây
* Phân bổ khối lượng công việc cho các nhà cung cấp
    - Tránh phụ thuộc vào một nhà cung cấp duy nhất (vendor lock-in)
    - Tối ưu hóa theo vị trí địa lý
    - Tối ưu hóa chi phí
    - Dự phòng

```console
# Terraform đa đám mây
provider "aws" { ... }
provider "gcp" { ... }
provider "azure" { ... }
```

## Kiểm thử hiệu năng và Đo hiệu năng (Benchmarking)
### Đo hiệu năng CPU
```console
# Cài đặt sysbench
dnf -y install sysbench

# Kiểm tra hiệu năng CPU
sysbench cpu --cpu-max-prime=20000 --threads=4 run
```

* So sánh kết quả giữa các nền tảng
    * VPS: ~2000-4000 sự kiện/giây
    * Bare-metal: ~5000-10000 sự kiện/giây
    * Cloud: ~2000-8000 sự kiện/giây (có sự thay đổi)

### Đo hiệu năng bộ nhớ
```console
# Kiểm tra thông lượng bộ nhớ
sysbench memory --memory-total-size=10G --threads=4 run

# Kiểm tra độ trễ bộ nhớ
lat_mem_rd -P 4 -N 10 1024
```

### Đo hiệu năng I/O đĩa
```console
# Cài đặt fio
dnf -y install fio

# Kiểm tra đọc/ghi ngẫu nhiên
fio --name=randwrite --ioengine=libaio --rw=randrw \
    --bs=4k --size=4G --numjobs=4 --iodepth=32 \
    --runtime=60 --time_based --group_reporting

# Đọc/ghi tuần tự
fio --name=seqwrite --ioengine=libaio --rw=write \
    --bs=1M --size=4G --numjobs=1 --iodepth=16 \
    --runtime=60 --time_based
```

### Đánh giá hiệu năng mạng
```console
# Cài đặt iperf3
dnf -y install iperf3

# Phía máy chủ
iperf3 -s

# Phía máy khách
iperf3 -c server-ip -t 60 -P 4
```

* Kết quả điển hình:
    * VPS: 1-10 Gbps
    * Bare-metal: 1-10 Gbps (chuyên dụng)
    * Cloud: 1-100 Gbps (tùy thuộc vào loại instance)

## Các chiến lược tối ưu hóa chi phí
### Tối ưu hóa chi phí VPS
1. Điều chỉnh quy mô instance cho phù hợp

```console
# Theo dõi mức sử dụng thực tế
htop
free -h
df -h
```

2. Sử dụng các cam kết dài hạn. Nhiều nhà cung cấp đưa ra mức giảm giá (10-20%) cho gói thanh toán hàng năm.
3. Tối ưu hóa hiệu năng ứng dụng. Giảm yêu cầu về tài nguyên thông qua tối ưu hóa mã nguồn.
4. Triển khai bộ nhớ đệm

```console
dnf -y install redis-server memcached
```

### Tối ưu hóa chi phí máy chủ vật lý
1. Tối đa hóa hiệu suất sử dụng

```console
# Chạy nhiều dịch vụ trên một máy chủ
docker-compose up -d
```

2. Triển khai ảo hóa nếu cần thiết

```console
# Sử dụng KVM để tạo các máy ảo trên máy chủ vật lý (bare-metal).
dnf -y install qemu-kvm libvirt virt-install 
```

3. Chọn phần cứng phù hợp. Đừng cấp phát dư thừa
4. Hợp đồng dài hạn. Đàm phán mức giá tốt hơn cho các cam kết từ 12 tháng trở lên.

### Tối ưu hóa chi phí đám mây
1. Sử dụng Reserved Instance

```console
aws ec2 purchase-reserved-instances \
    --instance-type t3.medium \
    --instance-count 2 \
    --offering-type All Upfront
```

2. Triển khai tính năng tự động mở rộng quy mô. Giảm quy mô trong giờ thấp điểm
3. Sử dụng Spot Instance cho các khối lượng công việc không quan trọng.

```console
aws ec2 request-spot-instances \
    --spot-price "0.05" \
    --instance-count 5
```

4. Điều chỉnh kích thước instance cho phù hợp. Sử dụng các đề xuất từ AWS Cost Explorer.
5. Xóa các tài nguyên không sử dụng

```console
aws ec2 describe-volumes --filters "Name=status,Values=available"
```

6. Sử dụng các chính sách vòng đời S3. Chuyển dữ liệu cũ sang các tầng lưu trữ có chi phí thấp hơn.

## Các chiến lược di chuyển
### Chuyển đổi từ VPS sang máy chủ vật lý (Bare Metal)
1. Sao lưu VPS hiện tại

```console
rsync -avz --progress /var/www/ backup-location/
```

2. Triển khai máy chủ vật lý (Bare-metal server). Cài đặt cùng phiên bản hệ điều hành.
3. Khôi phục dữ liệu

```console
rsync -avz backup-location/ /var/www/
```

4. Cập nhật DNS. Trỏ về IP mới.
5. Kiểm thử và xác minh

```console
curl -I https://yourdomain.com
```

### Chuyển đổi từ hệ thống Bare-metal sang Cloud
1. Tạo cơ sở hạ tầng đám mây

```console
terraform apply
```

2. Sử dụng các công cụ di chuyển
    * AWS Server Migration Service
    * Azure Migrate
    * Google Cloud Migrate
3. Đồng bộ hóa dữ liệu

```console
rsync -avz --progress source-server:/data/ /mnt/cloud-storage/
```

4. Cập nhật cấu hình: Điều chỉnh cấu hình ứng dụng cho các dịch vụ đám mây.
5. Chuyển đổi với thời gian gián đoạn tối thiểu: Sử dụng chiến lược giảm giá trị TTL của DNS.

### Chuyển đổi từ VPS sang Cloud
1. Tạo các tài nguyên đám mây tương đương. Đạt hoặc vượt thông số kỹ thuật của VPS.
2. Di chuyển cơ sở dữ liệu

```console
pg_dump dbname | psql -h cloud-db-host dbname
```

3. Di chuyển ứng dụng

```console
git clone repository
docker build -t app:latest .
docker push registry.cloud.com/app:latest
```

4. Cập nhật DNS và kiểm tra: Chuyển dịch lưu lượng truy cập dần dần sử dụng DNS có trọng số

## Các vấn đề cần cân nhắc về bảo mật
### Bảo mật VPS

```console
# 1. Cập nhật thường xuyên
dnf -y upgrade

# 2. Cấu hình tường lửa
firewall-cmd --permanent --add-port=22/tcp
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=443/tcp
firewall-cmd --reload

# 3. Fail2ban
dnf -y install fail2ban
systemctl enable fail2ban

# 4. Tăng cường bảo mật SSH
# Chỉnh sửa /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no
```

### Bảo mật Bare-metal
**Tất cả các biện pháp bảo mật VPS, cùng với:**

1. Bảo mật phần cứng: Mật khẩu BIOS; Secure Boot
2. Kiểm soát truy cập vật lý: Bảo mật trung tâm dữ liệu
3. Bảo mật IPMI: Mạng quản lý tách biệt; Mật khẩu IPMI mạnh.
4. Mã hóa toàn bộ ổ đĩa

```console
cryptsetup luksFormat /dev/sda
```

### Bảo mật đám mây
1. Các chính sách IAM

```console
aws iam create-policy --policy-name LeastPrivilege
```

2. Nhóm bảo mật

```console
aws ec2 create-security-group \
    --group-name web-servers \
    --description "Web servers security group"
```

3. Mã hóa dữ liệu tĩnh
    * Bật mã hóa EBS
    * Sử dụng KMS để quản lý khóa

4. Phân đoạn mạng: VPC, subnet, NACL
5. Giám sát tuân thủ

```console
aws config start-configuration-recorder
```

## Giám sát và Khả năng quan sát
### Giám sát VPS
```console
# Giám sát cơ bản
htop
iotop
nethogs

# Giám sát nâng cao
dnf -y install prometheus node-exporter
systemctl enable node-exporter

# Tổng hợp nhật ký
dnf -y install rsyslog
# Cấu hình ghi nhật ký từ xa
```

### Giám sát Bare-metal
Tất cả các dịch vụ giám sát VPS, cộng thêm:

```console
# Giám sát phần cứng
dnf -y install lm-sensors
sensors-detect
sensors

# Giám sát RAID
cat /proc/mdstat
mdadm --detail /dev/md0

# Giám sát thông minh
dnf -y install smartmontools
smartctl -a /dev/sda
```

### Giám sát đám mây
Giám sát đám mây tích hợp sẵn

```console
# AWS CloudWatch
aws cloudwatch put-metric-alarm

# Azure Monitor
az monitor metrics alert create

# GCP Cloud Monitoring
gcloud monitoring dashboards create
```

* APM của bên thứ ba
* New Relic, Datadog, Dynatrace

## Khắc phục các sự cố thường gặp
### Khắc phục sự cố VPS

```console
# Tải trọng cao
top
ps aux --sort=-%cpu | head

# Đĩa đầy
df -h
du -sh /* | sort -rh | head

# Sự cố mạng
netstat -tulpn
ss -tulpn

# Rò rỉ bộ nhớ
ps aux --sort=-%mem | head
free -h
```

### Khắc phục sự cố Bare-metal

```console
# Lỗi phần cứng
dmesg | grep -i error
journalctl -p err -b

# Suy giảm hiệu năng RAID
cat /proc/mdstat
mdadm --detail /dev/md0

# Các vấn đề về nhiệt độ
sensors
ipmitool sensor list | grep Temp

# Các vấn đề về gộp kết nối mạng
cat /proc/net/bonding/bond0
```

### Khắc phục sự cố đám mây

```console
# Khả năng kết nối với instance
aws ec2 describe-instances --instance-ids i-xxx

# Các vấn đề về nhóm bảo mật
aws ec2 describe-security-groups

# Các vấn đề về tự động mở rộng quy mô
aws autoscaling describe-auto-scaling-groups

# Nhật ký CloudWatch
aws logs tail /aws/ec2/instance-id --follow
```

## Kết luận
Việc lựa chọn giữa cơ sở hạ tầng VPS, Bare Metal và Cloud đòi hỏi sự phân tích kỹ lưỡng về các yêu cầu cụ thể, ngân sách và năng lực kỹ thuật của bạn. Mỗi tùy chọn đều mang lại những ưu điểm và sự đánh đổi riêng, phù hợp với các trường hợp sử dụng khác nhau.

VPS mang lại sự cân bằng tuyệt vời giữa chi phí, hiệu năng và khả năng kiểm soát đối với các khối lượng công việc quy mô vừa và nhỏ. Đây là giải pháp lý tưởng cho các doanh nghiệp và nhà phát triển cần tài nguyên chuyên dụng mà không phải gánh vác các công việc quản lý phần cứng vật lý phức tạp. Nhờ công nghệ ảo hóa KVM hiện đại, hiệu năng của VPS đã được cải thiện đáng kể, đáp ứng tốt hầu hết các nhu cầu phổ biến như lưu trữ web (web hosting), môi trường phát triển và các cơ sở dữ liệu nhỏ.

Bare Metal mang lại hiệu năng tối đa, khả năng kiểm soát phần cứng toàn diện và chi phí ổn định, dễ dự đoán cho các ứng dụng đòi hỏi tài nguyên lớn. Khi cần hiệu năng tính toán cao và ổn định mà không muốn chịu ảnh hưởng từ lớp ảo hóa, Bare Metal là lựa chọn sáng suốt. Giải pháp này đặc biệt hữu ích cho các khối lượng công việc đòi hỏi tuân thủ quy định nghiêm ngặt, cơ sở dữ liệu lớn và các ứng dụng mà độ trễ dù chỉ vài mili-giây cũng có thể gây ảnh hưởng lớn.

Cơ sở hạ tầng Cloud vượt trội trong các tình huống đòi hỏi khả năng co giãn linh hoạt, phân phối toàn cầu và hệ sinh thái dịch vụ được quản lý (managed services) phong phú. Mặc dù chi phí có thể cao hơn đối với các khối lượng công việc duy trì liên tục, nhưng khả năng mở rộng linh hoạt, tận dụng các dịch vụ quản lý sẵn có và triển khai trên phạm vi toàn cầu khiến Cloud trở thành lựa chọn vô giá cho các ứng dụng hiện đại có nhu cầu biến động.

Đối với nhiều tổ chức, mô hình lai (hybrid) kết hợp nhiều loại hình cơ sở hạ tầng mang lại giải pháp tổng thể tối ưu nhất. Bạn có thể sử dụng Bare Metal cho các máy chủ cơ sở dữ liệu cốt lõi, VPS cho các dịch vụ hỗ trợ, và Cloud để đáp ứng nhu cầu tăng đột biến về tài nguyên cũng như phân phối dịch vụ toàn cầu. Chiến lược này cho phép bạn tối ưu hóa cả hiệu năng lẫn chi phí mà vẫn duy trì được sự linh hoạt cần thiết.

Cuối cùng, lựa chọn phù hợp phụ thuộc vào các yêu cầu cụ thể của bạn về hiệu năng, ngân sách, khả năng mở rộng, quyền kiểm soát và độ phức tạp trong quản lý. Hãy sử dụng khung ra quyết định và các phương pháp đánh giá hiệu năng được đề cập trong hướng dẫn này để xem xét các tùy chọn và đưa ra quyết định sáng suốt về cơ sở hạ tầng, đảm bảo phù hợp với mục tiêu kinh doanh và yêu cầu kỹ thuật của bạn.

Hãy nhớ thường xuyên đánh giá lại các lựa chọn cơ sở hạ tầng khi nhu cầu thay đổi, công nghệ phát triển và các mô hình định giá có sự điều chỉnh. Lĩnh vực lưu trữ luôn biến động không ngừng, và giải pháp tối ưu ở thời điểm hiện tại chưa chắc đã là lựa chọn tốt nhất trong tương lai.
