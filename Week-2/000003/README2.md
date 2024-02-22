<a name="STS"></a>
## X. Setting up Site-to-Site VPN Connection in AWS

Phần tiếp theo của bài lab giúp chúng ta học được cách thiết lập một kết nối Site to Site VPN trong AWS. Trong thực tế, giải pháp này khá được ưa chuộng do ưu điểm giá thành rẻ, đồng thời rất dễ cấu hình do AWS cung cấp hướng dẫn cho từng loại thiết bị phía đầu Customer. Việc Customer bận tâm duy nhất đó là chuẩn bị đường internet để từ đó tạo đường hầm an toàn bí mật (sử dụng IPSec) kết nối tới AWS thông qua AWS VPN tunnel.
Trong phạm vi bài lab, giả lập rằng chúng ta có Main office ( VPC ASG ) và Branch office ( VPC ASG VPN ) đặt tại 2 VPC thuộc 2 AZ khác nhau để có sự khác biệt về mặt network. Trên mỗi VPC thực hiện tạo EC2 cho phép SSH từ bên ngoài, nhưng không có khả năng kết nối và ping lẫn nhau sử dụng địa chỉ Private IP của mỗi EC2. Việc ta cần làm là cấu hình VPN để các địa chỉ Private IP có thể ping được lẫn nhau sử dụng VPN Site-to-Site.

<img src=images/082.png>

# Agenda :

## [I. Create VPN enviroment](#VPN1)

-   ### [1. Create VPC for VPN](#VPV1.1)
-   ### [2. Create EC2 as a Customer Gateway](#VPN1.2)

## [II. Configure VPN Connection](#VPN2)

-   ### [1. Create Virtual Private](#VPN2.1)
-   ### [2. Create Customer Gateway](#VPN2.2)
-   ### [3. Create VPN Connection](#VPN2.3)
-   ### [4. Customer Gateway Configuration](#VPN2.4)
-   ### [5. Modify AWS VPN Tunnel](#VPN2.5)

<a name="VNP1"></a>
### I. Create VPN Environment

<a name="VPN1.1"></a>
### 1. Create VPC for VPN

<img src=images/083.png>
<img src=images/084.png>
<img src=images/085.png>
### 2. Create Subnet

<img src=images/086.png>
<img src=images/087.png>
<img src=images/088.png>
<img src=images/089.png>
<img src=images/090.png>
<img src=images/091.png>
### 3. Create Internet Gateway

<img src=images/092.png>
<img src=images/093.png>
<img src=images/094.png>
### 4. Attach Internet Gateway

<img src=images/095.png>
<img src=images/096.png>
### 5. Create Route Table

<img src=images/097.png>
<img src=images/098.png>
<img src=images/099.png>
### 6. Edit Routes Interface

<img src=images/100.png>
<img src=images/101.png>
### 7. Edit Subnet Associations

<img src=images/102.png>
<img src=images/103.png>

<a name="VPN1.2"></a>
## 2. Create EC2 as a Customer Gateway

1. Create security group
<img src=images/104.png>
<img src=images/105.png>

2. Complete the creation of VPN Public - SG. A Security Group has been created. Next, we will proceed to create an EC2 server that plays the Customer Gateway role.
<img src=images/106.png>

3. Access to EC2 -> Launch instances
<img src=images/107.png>
<img src=images/108.png>
<img src=images/109.png>
<img src=images/110.png>
<img src=images/111.png>
<img src=images/112.png>

4. View details of the Customer Gateway instance
<img src=images/113.png>


<a name="VNP2"></a>
### II. Configure VPN Connection

In this step, we will proceed to set up a Virtual Private Gateway, Customer Gateway, and VPN Site-to-Site connection.
<img src=images/114.png>

<a name="VPN2.1"></a>
### 1. Create Virtual Private

1. Create Virtual Private Gateway
<img src=images/115.png>
<img src=images/116.png>

2. Attach to VPC
<img src=images/117.png>
<img src=images/118.png>

3. Finish and observe State as Attached
<img src=images/119.png>

<a name="VPN2.2"></a>
### 2. Create Customer Gateway

1. Create Customer Gateway
<img src=images/120.png>
<img src=images/121.png>

2. Wait for approximately 5 minutes for the Customer Gateway creation to complete:
<img src=images/122.png>

<a name="VPN2.3"></a>
### 3. Create VPN Connection

1. Create VPN Connection
<img src=images/123.png>
<img src=images/124.png>

2. Wait for about 5 minutes to finish creating the VPN Connection
<img src=images/125.png>

3. Edit Route Propagation
<img src=images/126.png>
<img src=images/127.png>

4. Complete and Recheck: Route Propagation should have changed to **Yes**
<img src=images/128.png>

5. Similar Route Propagation for Private Subnet:
<img src=images/129.png>
<img src=images/130.png>

6. Complete and Recheck: Route Propagation should have changed to **Yes**
<img src=images/131.png>


<a name="VPN2.4"></a>
### 4. Customer Gateway Configuration

1. Access to VPC
    - Select Site-to-Site VPN Connection
    - Select VPN Connection created
    - Select Download Configuration  

<img src=images/132.png>
<img src=images/133.png>

2. Save the image file information to the folder used for storing the key pair and lab tools. Modify the configuration based on your device.
<img src=images/134.png>

3. Connect SSH to EC2 Customer Gateway.
<img src=images/135.png>

4. Install OpenSwan
<img src=images/136.png>

5. Check the configuration file /etc/ipsec.conf
```
vi /etc/ipsec.conf
```
<img src=images/137.png>


6.  Configuration file /etc/sysctl.conf
```
vi /etc/sysctl.conf
```

<img src=images/138.png>

7. Then to apply this configuration, run the command:
```
sysctl -p
```
- Add the following configuration at the end of the configuration file.
```
net.ipv4.ip_forward = 1
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
```
<img src=images/139.png>

8. Next we will configure the file /etc/ipsec.d/aws.conf
```
vi /etc/ipsec.d/aws.conf
```
- Add the following configuration to the configuration file. We will create 2 Tunnel with information taken from the VPN Connection configuration file you downloaded and saved in the folder containing the key pair earlier.
- Make sure you edit the IP address and network class accordingly before copying the above configuration.
- For Amazon Linux, we will omit the auth=esp line in the original configuration file.
- Since we only have 1 public IP addres for Customer Gateway, we will need to configure overlapip=yes.
- leftid: IP Public Address on the Onprem side. (Here is public IP of EC2 Customer Gateway in ASG VPN VPC) .
- right: IP Public Address on AWS VPN Tunnel side.
- leftsubnet: CIDR of Local Side Network (If there are multiple network layers, you can leave it as 0.0.0.0/0).
- rightsubnet: CIDR of Private Subnet on AWS.

<img src=images/140.png>

9. Check the next step in the configuration file we downloaded.
<img src=images/141.png>
<img src=images/142.png>
<img src=images/143.png>

10. Create and configure the file etc/ipsec.d/aws.secrets Create a new file with the following configuration to set up authentication for the 2 Tunnels.

```
touch /etc/ipsec.d/aws.secrets
```
<img src=images/144.png>

11. Restart Network service & IPSEC service

```
service network restart
chkconfig ipsec on
service ipsec start
service ipsec status
```

12. If the status tunnel is still not running correctly, after checking and updating the configuration you will need to run the command to restart service network and IPsec :
```
sudo service network restart
sudo service ipsec restart
```


13. After completing the configuration.Try to ping from the Customer Gateway server side to the EC2 Private server. If the VPN configuration is successful you will get the result as below.

```
ping <EC2 Private IP> -c5
```
14. After completing the configuration.Try to ping from the EC2 Private server side to the Customer Gateway server. If the VPN configuration is successful you will get the result as below.
```
ping <Customer gateway instance IP> -c5
```

