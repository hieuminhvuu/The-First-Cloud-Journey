# Table of Contents:

## [I. Giới thiệu](#100)

## [II. Chuẩn bị](#200)
- ### [1. Tạo VPC](#201)    
- ### [2. Tạo Subnet](#202)
- ### [3. Tạo Internet Gateway](#203)
- ### [4. Tạo Route Table](#204)
- ### [5. Tạo Security Group](#205)

## [III. Tạo máy chủ EC2](#300)
- ### [1. Tạo máy chủ EC2](#301)
- ### [2. Kiểm tra kết nối](#302)
- ### [3. Tạo NAT Gateway](#303)

## [IV. Set up các tier](#400)
- ### [1. Database tier](#401)
- ### [2. Application tier](#402)
- ### [3. Web tier](#403)

## [V. Dọn dẹp tài nguyên](#500)


-----

<a name="100"></a>

# I. Giới thiệu

- Trong bài Workshop này, mình sẽ chia sẻ cách chỉ sử dụng EC2 ở trong các Subnet Public và Private deploy một three-tier website. Chi tiết về công nghệ các tier như sau :
    - Database Tier : MongoDB
    - Application Tier : Fastapi - Python
    - Web Tier : React - JavaScript
- Bài viết chia sẻ từ tạo các tài nguyên trên AWS cho tới chi tiết cách deploy ở từng EC2 như thế nào.
- Đây là hình ảnh kiến trúc bài Workshop :

<img src= images/100/001.png>


<a name="200"></a>

# II. Chuẩn bị


<a name="201"></a>

## 1. Tạo VPC

1. Truy cập giao diện **AWS Management Console**
    - Tìm **VPC**
    - Chọn **VPC**

<img src= images/201/001.png>

2. Trong giao diện **VPC**
    - Chọn **Yours VPC**
    - Chọn **Create VPC**

<img src= images/201/002.png>

3. Tiến hành các bước tạo VPC
    - **Resource**, chọn **VPC only**
    - **Name tag**, nhập **Workshop1**
    - **IPv4 CIDR**, nhập **10.10.0.0/16**
    - Chọn **Create VPC**

<img src= images/201/003.png>
<img src= images/201/004.png>

4. Hoàn thành tạo **VPC**

<img src= images/201/005.png>

5. Xem chi tiết VPC vừa tạo. Kiểm nếu chưa **Enable DNS resolution and DNS Hostname**

    - Vào **Edit VPC setting**
    - Chọn **DNS setting**
    - Chọn và **Save**.

<img src= images/201/006.png>

<a name="202"></a>

## 2. Tạo Subnet

1. Trong giao diện **VPC**

    - Chọn **Subnets**
    - Chọn **Create subnet**

<img src= images/202/001.png>

2. Trong giao diện **Create subnet**

    - Chọn **Workshop1** VPC
    - Subnet name, nhập **Public Subnet 1**
    - Chọn AZ **eu-north-1a**
    - IPv4 CIDR block, nhập **10.10.1.0/24** theo kiến trúc mô tả
    - Chọn **Create subnet**

<img src= images/202/002.png>
<img src= images/202/003.png>


3. Hoàn thành tạo **subnet**

<img src= images/202/004.png>

4. Tương tự, tạo thêm 3 **subnet** nữa
    - **Public subnet 2** với CIDR là **10.10.2.0/24** nằm trong Availability Zone **eu-north-1b**
    - **Private subnet 1** với CIDR là **10.10.3.0/24** nằm trong Availability Zone **eu-north-1a**
    - **Private subnet 2** với CIDR là **10.10.4.0/24** nằm trong Availability Zone **eu-north-1a**

<img src= images/202/005.png>

5. Trong giao diện **VPC**

    - Chọn **Subnets**
    - Chọn **Public Subnet 1**
    - Chọn **Actions**
    - Chọn **Edit subnet settings**

<img src= images/202/006.png>

6. Trong mục **Auto-assign IP settings**

    - Chọn **Enable auto-assign publi IPv4 address**
    - Chọn **Save**

<img src= images/202/007.png>
<img src= images/202/008.png>

7. Sau đó thực hiện tương tự với **Public subnet 2**.

<img src= images/202/009.png>

<a name="203"></a>

## 3. Tạo Internet Gateway

1. Trong giao diện **VPC**

    - Chọn **Internet Gateways**
    - Chọn **Create internet gateway**

<img src= images/203/001.png>

2. Thực hiện cấu hình

    - Name tag, nhập **Internet Gateway**
    - Chọn **Create internet gateway**

<img src= images/203/002.png>

3. Hoàn thành tạo **Internet Gateway**

4. Thực hiện **Attach VPC**

    - Chọn **Actions**
    - Chọn **Attach to VPC**
    - Chọn **Workshop1**, VPC ID sẽ được tự động điền.
    - Chọn **Attach intermet gateway**

<img src= images/203/003.png>
<img src= images/203/004.png>

5. Khi attach thành công **State** internet gateway sẽ chuyển sang **Attached**

<img src= images/203/005.png>

<a name="204"></a>

## 4. Tạo Route Table

### Tạo Security Group cho máy chủ nằm trong Public subnet

1. Trong giao diện **VPC**

    - Chọn **Route Tables**
    - Chọn **Create route table**

<img src= images/204/001.png>

2. Tiến hành cấu hình **Route table**

    - Name, nhập **Route table-Public**
    - VPC, chọn **Workshop1** VPC. VPC id sẽ được tự động điền vào.
    - Chọn **Cretae route table**

<img src= images/204/002.png>

3. Hoàn thành tạo Route table

<img src= images/204/003.png>

4. Thực hiện **Edit route**

    - Chọn **Actions**
    - Chọn **Edit routes**

<img src= images/204/004.png>

5. Trong giao diện **Edit routes**

    - Chọn **Add route**
    - Điền phần **Destination CIDR** : **0.0.0.0/0** đại diện cho Internet.
    - Trong phần **Target** chọn **Internet Gateway**, sau đó chọn **Internet Gateway** chúng ta đã tạo. **Internet Gateway ID** sẽ được tự động điền.
    - Chọn **Save changes**

<img src= images/204/005.png>

6. Gắn 2 Public subnet vào

    - Chọn **subnet associations**
    - Chọn **Edit subnet associations**
    - Chọn đúng 2 subnet public chúng ta đã tạo
    - Chọn **Save associations**

<img src= images/204/006.png>
<img src= images/204/007.png>

7. Hoàn thành và kiểm tra lại **Subnet associations**

<a name="205"></a>

## 5. Tạo Security Group

1. Trong giao diện **VPC**

    - Chọn **Security Group**
    - Chọn **Cretae security group**

<img src= images/205/001.png>

2. Thực hiện cấu hình **Security group**

    - **Security Group name**, nhập **Public subnet - SG**
    - **Description**, nhập **Allow SSH and Ping for servers in public subnet.**
    - Chọn **Workshop1** VPC

<img src= images/205/002.png>

3. Thực hiện cấu hình **Inbound rules**

    - Trong **Inbound rules**, click **Add rule**
    - Chọn **Type: SSH** và **Source: My IP. My IP** đại diện cho 1 địa chỉ public IPv4 bạn đang sử dụng(sẽ thay đổi khi bạn đổi mạng)
    - Chọn **Add rule** để thêm 1 rule mới.
    - Chọn **Type: All ICMP - IPv4** và **Source: Anywhere**. Cho phép ping từ bất kì địa chỉ IP nào.

<img src= images/205/003.png>

4. Kiểm tra **Outbound rules** và chọn **Cretae security group**

<img src= images/205/004.png>

5. Hoàn thành tạo security group cho máy chủ nằm trong Public subnet

<img src= images/205/005.png>

### Tương tự, tạo Security Group cho máy chủ nằm trong Private subnet

<img src= images/205/006.png>
<img src= images/205/007.png>
<img src= images/205/008.png>
<img src= images/205/009.png>

6. Hoàn thành tạo security group cho máy chủ nằm trong Private subnet

<img src= images/205/010.png>

<a name="300"></a>

# III. Tạo máy chủ EC2

<a name="301"></a>

## 1. Tạo máy chủ EC2

1. Truy cập **AWS Management Console**

    - Tìm **EC2**
    - Chọn **EC2**

<img src= images/301/001.png>

2. Trong giao diện **EC2**

    - Chọn **Instances**
    - Chọn Launch **instances**

<img src= images/301/002.png>

3. Name and tags của instance, nhập **EC2 Public - Web**

<img src= images/301/003.png>

4. Thực hiện chọn **AMI**

    - Chọn **Quick Start**
    - Chọn **Ubuntu Server 22.04**
    - Chọn **AMI**

<img src= images/301/004.png>

5. Thực hiện chọn Instance type 

<img src= images/301/005.png>

6. Chọn Create new key pair

<img src= images/301/006.png>

7. Trong giao diện **Cretae key pair**

    - **Key pair name**, nhập **Workshop1** (tên tùy chọn, bạn có thể đặt bất kỳ).
    - **Key pair type**, chọn **RSA**
    - **Private key file format**, chọn **.pem**

<img src= images/301/007.png>

8. Thực hiện cấu hình **Network**

    - **VPC**, chọn **Workshop1**
    - **Subnet**, chọn **Public Subnet 1**
    - **Auto-assign public IP**, chọn **Enable**
    - **Firewall (Security Group)**, chọn **Select existing security group**
    - Chọn **Public subnet - SG**
    - Chọn **Launch instance**

<img src= images/301/008.png>

9. Hoàn thành tạo instance

<img src= images/301/009.png>

10. Đợi khoảng 5 phút **Status check** sẽ chuyển sang **2/2 checks passed**

<img src= images/301/010.png>

### Tưởng tự, tạo EC2 nằm trong Private subnet

<img src= images/301/011.png>
<img src= images/301/012.png>
<img src= images/301/013.png>
<img src= images/301/014.png>
<img src= images/301/015.png>
<img src= images/301/016.png>
<img src= images/301/017.png>
<img src= images/301/018.png>
<img src= images/301/019.png>
<img src= images/301/020.png>

11. Hoàn thành tạo 3 instances

<img src= images/301/021.png>


<a name="302"></a>

## 2. Kiểm tra kết nối

1. Truy cập vào trang EC2

    - Chọn Instances
    - Chọn EC2 Public
    - Chọn Connect

<img src= images/302/001.png>

2. Trong tab **Connect to instance** :
    - Chọn **SSH Client**
    - Sao chép địa chỉ ssh mẫu

<img src= images/302/002.png>

3. Chuẩn bị key
    - Chuyển key đã download về vào **~/.ssh** , đây là thư mục chuyên lưu trữ key ssh

<img src= images/302/003.png>

4. Trước khi connect, cần chmod 400 cho key, sau đó dựa vào địa chỉ ssh mẫu ở bước 2, thay đổi địa chỉ key local để ssh tới EC2 Public

<img src= images/302/004.png>

5. Thử ping tới internet, ping tới 2 EC2 ở Private Subnet

<img src= images/302/005.png>
<img src= images/302/006.png>

6. Copy key từ local tới EC2 Public bằng lệnh scp, dùng để ssh tới EC2 Private

<img src= images/302/007.png>

7. Kiểm tra đã nhận được key ở EC2 public chưa

<img src= images/302/008.png>

8. Trước khi ssh nhớ chmod 400 cho key

<img src= images/302/009.png>

9. Dùng key ssh tới 2 instance Private

<img src= images/302/010.png>
<img src= images/302/011.png>

10. Thử ping tới internet từ EC2 Private

<img src= images/302/012.png>

11. Kết quả là không thể ping được, ở bước sau chúng ta sẽ config.


<a name="303"></a>

## 3. Tạo NAT Gateway

### Tạo một địa chỉ Elastic IP

1. Truy cập **EC2**

    - Chọn **Elastic IPs**
    - Chọn **Allocate Elastic IP address**

<img src= images/303/001.png>

2. Trong giao diện **Allocate Elastic IP address**

    - **Public IPv4 address pool**, chọn **Amazon’s pool of IPv4 addresses**
    - Chọn **Allocate**

<img src= images/303/002.png>

3. Chúng ta vừa tạo thành công một địa chỉ Public IP Address

<img src= images/303/003.png>

### Tạo NAT Gateway

4. Truy cập vào **VPC**

    - Chọn **NAT Gateways**
    - **Create NAT gateway**

<img src= images/303/004.png>

5. Trong giao diện NAT gateway

    - **Name**, nhập **NAT gateway**
    - **Subnet**, chọn **Public subnet 2**
    - **Connectivity type**, chọn **Public**
    - **Elastic IP allocation ID**, chọn **Elastic IP** vừa tạo.

<img src= images/303/005.png>

6. Chọn Create NAT gateway

<img src= images/303/006.png>
<img src= images/303/007.png>

### Tạo Route table - Private và gáng vào các private subnet

7. Trong giao diện **VPC**

    - Chọn **Route Tables**
    - Chọn **Create route table**

<img src= images/303/008.png>

8. Trong giao diện **Route table**

    - **Name**, nhập **Route table - Private**
    - **VPC**, chọn **Workshop1** vpc
    - Chọn **Cretae route table**

<img src= images/303/009.png>

9. Hoàn thành tạo **Route table - Private**

<img src= images/303/010.png>

10. Trong giao diện **Route table - Private**

    - Chọn **Subnet Associations**
    - Chọn **Edit subnet associations**

<img src= images/303/011.png>

11. Trong giao diện **Edit subnet associations**

    - Chọn 2 private subnet
    - Chọn **Save associations**

<img src= images/303/012.png>

12. Trong giao diện **Route table - Private**

    - Chọn **Routes**
    - Chọn **Edit routes**

<img src= images/303/013.png>

13. Trong giao diện **Edit routes**

    - Chọn **Add route**
    - Chọn **Destination: 0.0.0.0/0**
    - **Target**: **NAT Gateway**
    - Chọn **Save changes**

<img src= images/303/014.png>

14. Kiểm tra lại **Routes**

<img src= images/303/015.png>

15. Kiểm tra từ 2 Private EC2 đã ping ra internet được chưa. Kết quả ở đây đã ok.

<img src= images/303/016.png>
<img src= images/303/017.png>

<a name="400"></a>

# IV. Set up các tier

<a name="401"></a>

## 1. Database tier

- SSH tới **EC2 Private - Database**, cài đặt mongodb

```
sudo apt install software-properties-common gnupg apt-transport-https ca-certificates -y
curl -fsSL https://pgp.mongodb.com/server-7.0.asc |  sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

<img src= images/401/001.png>


```
sudo apt update
sudo apt install mongodb-org -y
mongod --version
```

<img src= images/401/002.png>
<img src= images/401/003.png>

- Khởi động MongoDB service và kiểm tra

```
sudo systemctl status mongod
sudo systemctl start mongod
sudo systemctl status mongod
```
<img src= images/401/004.png>
<img src= images/401/005.png>

```
sudo ss -pnltu | grep 27017
mongosh
```

<img src= images/401/006.png>
<img src= images/401/007.png>

- Config MongoDB cho truy cập từ xa

```
sudo nano /etc/mongod.conf  
```

- Tìm và sửa thành :

```
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0
```

<img src= images/401/008.png>

```
sudo systemctl restart mongod
```

- Kiểm tra cổng 27017 đã mở chưa :

<img src= images/401/009.png>

<a name="402"></a>

## 2. Application tier

1. Đầu tiên, ta cần zip code lại và copy tới EC2 Public

<img src= images/402/001.png>

2. Kiểm tra xem đã nhận được chưa

<img src= images/402/002.png>

3. Copy backend.zip tới EC2 Private - Application

<img src= images/402/003.png>

4. SSH tới EC2 Private - Application và kiểm tra

<img src= images/402/004.png>

5. Install unzip sau đó giải nén backend.zip

<img src= images/402/005.png>
<img src= images/402/006.png>

6. Tạo user backend, tạo thư mục /projects và chuyển thư folder backend tới

<img src= images/402/008.png>

7. chown, chmod folder backend, kiểm tra

<img src= images/402/009.png>

8. Cài đặt python, pip, venv

<img src= images/402/010.png>
<img src= images/402/011.png>
<img src= images/402/012.png>

9. Tạo virtualenv - môi trường cho python để chạy dự án, sau đó activate môi trường đó

<img src= images/402/014.png>

10. Cài đặt các dependencies của dự án 

```
pip install -r requirements.txt
```

<img src= images/402/015.png>


11. Sửa ip database thành ip của Database tier

<img src= images/402/017.png>
<img src= images/402/016.png>

12. Run dự án, gặp lỗi với database

<img src= images/402/018.png>

13. Sửa Security Group của Private Subnet, thêm rule TCP cổng 27017 của MongoDB

<img src= images/402/025.png>

14. Khởi động lại dự án, thành công 

<img src= images/402/019.png>

15. Tiếp theo, ta sẽ dùng nginx để làm revert proxy cho backend, install

<img src= images/402/020.png>

16. Tạo 1 file mới có tên là fastapi_nginx ở đường dẫn /etc/nginx/sites-enabled/ có nội dung như sau :

<img src= images/402/021.png>

17. Kiểm tra syntax của nginx và restart 

<img src= images/402/022.png>

18. Khởi động lại dự án, giờ nginx sẽ làm nhiệm vụ revert proxy, chuyển các request ở cổng 80 tới cổng 8000.

<img src= images/402/023.png>

<a name="403"></a>

## 3. Web tier

1. Cài đặt unzip và giải nén code từ file zip frontend.zip

<img src= images/403/001.png>
<img src= images/403/002.png>

2. Tương tự với Application Tier, ta cũng tạo thư mục và user sử dụng cho dự án frontend

<img src= images/403/003.png>
<img src= images/403/004.png>

3. Sửa url của backend thành ip của Application tier EC2

<img src= images/403/005.png>
<img src= images/403/006.png>

4. Install node, npm

5. Install nginx

<img src= images/403/007.png>
<img src= images/403/008.png>

6. Build dự án, sau khi build xong 1 thư mục tên là **build** sẽ được tạo ra

```
npm run build
```

<img src= images/403/009.png>

7. Dùng nginx làm web server : tạo ra 1 file nginx có tên là react.conf ở đường dẫn **/etc/nginx/conf.d/**. Thêm nội dung (như ảnh). Sau đó restart lại nginx

<img src= images/403/010.png>
<img src= images/403/011.png>
<img src= images/403/012.png>

8. Dùng nginx để revert proxy : tạo ra 1 file có tên là react_nginx ở đường dẫn **/etc/nginx/sites-enabled/ với nội dung :

<img src= images/403/013.png>

9. Mở thêm cổng 80 HTTP của Security Group - Public subnet

<img src= images/403/018.png>

10. nginx sẽ chuyển các request từ cổng 80 tới cổng 3000 đang chạy dự án, giờ hãy truy cập vào dự án bằng chính ip của EC2 Public

<img src= images/403/014.png>

11. Lỗi 500. Lỗi này xảy ra vì chúng ta chưa cấp phép cho user khác (other) truy cập vào folder **build**. Ta chỉ cần kiểm tra nginx là user nào, ở đây chính là www-data, sau đó thêm vào group **frontend**

<img src= images/403/015.png>
<img src= images/403/016.png>

12. Done, web của chúng ta đã hoạt động qua ip của EC2 cổng 80 rồi.

<img src= images/403/017.png>

<a name="500"></a>

# V. Dọn dẹp tài nguyên

1. Terminate các EC2 Instance

    - Truy cập Amazon EC2 console tại địa chỉ EC2.
    - Trên thanh điều hướng bên trái, chọn Intances
    - Chọn tất cả EC2 Instance liên quan tới bài lab.
    - Chọn **Instance state**
    - Chọn **Terminate instance**

<img src= images/500/001.png>
<img src= images/500/002.png>

2. Xóa NAT Gateway 

    - Xóa NAT Gateway và Elastic IP Address. AWS sẽ thu tiền cho các EIP lãng phí nên bạn cần kiểm tra kỹ để tránh bị trừ chi phí ngoài ý muốn.
    - Truy cập trang Amazon VPC console tại địa chỉ VPC
    - Trên thanh điều hướng bên trái , click NAT Gateway.
    - Chọn NAT Gateway.
    - Click Action.
    - Click Delete NAT Gateway.

<img src= images/500/003.png>
<img src= images/500/004.png>

3. Elastic IP Address

    - Tiếp tục xóa Elastic IP Address.
    - Truy cập trang Amazon VPC console tại địa chỉ https://console.aws.amazon.com/vpc/
    - Trên thanh điều hướng bên trái , click Elastic IP.
    - Chọn Elastic IP Address chúng ta đã tạo.
    - Click Action.
    - Click Release Elastic IP Address
    - Click Release.

<img src= images/500/005.png>
<img src= images/500/006.png>

4. Xoá hết các Subnet liên quan

<img src= images/500/007.png>
<img src= images/500/008.png>

5. Xoá VPC

<img src= images/500/009.png>
<img src= images/500/010.png>
