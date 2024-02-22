### In this lab, we will be constructing a model based on the following diagram:
<img src=images/001.png>

# Agenda :
### [I. Create VPC](#VPC1)
### [II. Create Subnet](#SUBNET1)
### [III. Create Internet Gateway](#GATEWAY1)
### [IV. Create Route Table](#RT1)
### [V. Create Security Group](#SG1)
### [VI. Create EC2 Server](#EC2)
### [VII. Test Connection](#CONNECT)
### [VIII. Create NAT Gateway](#NAT)
### [IX. Using Reachability Analyzer](#URA)
### [X. Setting up Site-to-Site VPN Connection in AWS](#STS)

<a name="VPC1"></a>
## I. Create VPC

1. Access to the **AWS Management Console** interface:
    - Locate and click on **VPC**
    - Choose **VPC**
<img src=images/002.png>

2. Within the VPC interface:
    - Select Your VPC
    - Click on Create VPC
<img src=images/003.png>

3. Follow these steps to create a VPC:
    - Choose Resource and select VPC only
    - Enter Name tag as **FCJ-0003**
    - Set IPv4 CIDR to 10.10.0.0/16
    - Click on **Create VPC**
<img src=images/004.png>

4. Complete the process of creating the **VPC**
<img src=images/005.png>

5. Review the details of the newly created VPC. Ensure that Enable DNS resolution and DNS Hostname is disabled:
    - Go to Edit VPC settings
<img src=images/006.png>
    - Navigate to DNS settings
    - Choose and then click Save
<img src=images/007.png>

<a name="SUBNET1"></a>
## II. Create Subnet

1. In the VPC Interface:
    - Select Subnets
    - Select Create subnet
<img src=images/008.png>

2. In the Create subnet Interface:
    - Select **FCJ-0003** VPC
    - Subnet name: Enter **Public Subnet 1**
    - Select AZ eu-north-1a
    - IPv4 CIDR block: Import 10.10.1.0/24 according to the architecture description
    - Select Create subnet
<img src=images/009.png>

3. Finish Creating Subnet
<img src=images/010.png>
  
4. Follow the same steps to create more subnets:
- Public subnet 2 with CIDR of 10.10.2.0/24 located in Availability Zone eu-north-1b
<img src=images/011.png>

- Private subnet 1 with CIDR of 10.10.3.0/24 located in Availability Zone eu-north-1a
<img src=images/012.png>

- Private subnet 2 with CIDR of 10.10.4.0/24 located in Availability Zone eu-north-1b
<img src=images/013.png>

<img src=images/014.png>

5. Allow Automatic Allocation of Public IP Addresses for 2 Public Subnets

- In the VPC Interface
   - Select Subnets
   - Select Public Subnet 1
   - Select Actions
   - Select Edit subnet settings

<img src=images/015.png>

- Under Auto-assign IP settings:
    - Select Enable auto-assign public IPv4 address
    - Select Save
  
<img src=images/016.png>

- Repeat the same process for Public subnet 2.
<img src=images/017.png>
  

<a name="GATEWAY1"></a>
## III. Create Internet Gateway

1. In the VPC interface:
    - Select Internet Gateways
    - Click on Create internet gateway
<img src=images/018.png>

2. Configure the internet gateway:
    - Enter **FCJ - Internet Gateway** for the Name tag
    - Click on Create **internet gateway**

<img src=images/019.png>

3. Complete the creation of the **Internet Gateway**
<img src=images/020.png>

4. Implement Attach VPC:
    - Click on **Actions**
    - Click on **Attach to VPC**
<img src=images/021.png>
    - Select **FCJ-0003;** the VPC ID will be automatically populated
    - Click on **Attach internet gateway**
<img src=images/022.png>

5. Once attached successfully, the **State** of the internet gateway will change to **Attached**
<img src=images/023.png>

<a name="RT1"></a>
## IV. Create Route Table
```Create Route Table for Outbound Internet Routing via Internet Gateway```

1. In the VPC interface:
    - Select Route Tables.
    - Click on Create route table.

<img src=images/024.png>

2. Configure the Route table:
    - Enter a Name: **FCJ - Route table-public**
    - Choose the VPC: Select FCJ VPC (VPC ID will auto-fill).
    - Click on Create route table.

<img src=images/025.png>

3. Complete creating the **Route table**.
<img src=images/026.png>

4. To make route edits:
    - Select Actions.
    - Choose Edit routes.
<img src=images/027.png>

5. In the Edit routes interface:
    - Click on Add route.
    - Fill in the Destination CIDR: 0.0.0.0/0 representing the Internet.
    - In the Target section, select Internet Gateway, then choose the created Internet Gateway (Gateway ID auto-fills).
    - Click Save changes.

<img src=images/028.png>

6. Review and confirm the updated **Routes**.

<img src=images/032.png>


7. Ensure that Route table - Public is selected.
    - Select Subnet Associations.
    - Click on Edit subnet associations.

<img src=images/029.png>


8. In the Edit subnet associations step:
    - Adjust the width of the **Subnet ID** column by dragging the pane to the right.
    - Select the appropriate **2 public subnets** that were created.
    - Click on **Save associations**.

<img src=images/030.png>

9. Review and confirm the updated **Subnet associations**.

<img src=images/031.png>

<a name="SG1"></a>
## V. Create Security Group
```
Create Security Group for Servers in Public Subnet
```

1. In the VPC interface:
    - Select Security Group
    - Select Create security group

<img src=images/033.png>

2. Configure the Security Group:
 -  Security Group name: Enter Public subnet - FCJ
 -  Description: Enter Allow SSH and Ping for servers in the public subnet.
 -  Select the ASG FCJ
 -  In Inbound rules, click Add rule.
 - Select Type: SSH and Source: Anywhere
 - Select Add rule to add a new rule.
 - Select Type: All ICMP - IPv4 and Source: Anywhere. Allow ping from any IP address.
 - select Create security group

<img src=images/034.png>

3. Complete the creation of the security group for the server located in the public subnet

<img src=images/035.png>

.
```
Create a Security Group for a Server in a Private Subnet
```

4. In the VPC interface:
    - Select Security Group
    - Select Create security group

<img src=images/033.png>

5. Configure the Security Group:

- In the Security group name field, enter Private subnet - SG
- In the Description section, enter Allow SSH and Ping for servers in the private subnet.
- Select the VPC named ASG
- Configure Inbound rules:
    - In Inbound rules, select Add rule.
    - Select Type: SSH and leave Source: Custom. Search and select Public subnet SG to allow SSH from servers in the public subnet.
- Select Add rule to add a new rule:
    - Select Type: All ICMP IPv4 and Source: Anywhere. Allow ping from any IP address.
- Select Create security group

<img src=images/036.png>

6. Complete the creation of the security group for the server located in the private subnet

<img src=images/037.png>

7. Two Security Groups have been created for servers located in the public and private subnets:

<img src=images/038.png>


<a name="EC2"></a>
## VI. Create EC2 Server

- In this step, we will create 2 EC2 servers (EC2 instances) following the architecture shown below:
<img src=images/039.png>

### Create EC2 Instances in Subnets

1. Access the AWS Management Console:
    - Navigate to EC2
    - Click on Instances

<img src=images/040.png>

2. In the EC2 interface:

    - Select Instances
    - Choose Launch instances

<img src=images/041.png>

3. Provide a Name and tags for the instance, enter EC2 **Public - FCJ**
<img src=images/042.png>

4. Choose the AMI:

    - Select Quick Start
    - Choose Amazon Linux 2
    - Select an AMI

<img src=images/043.png>

5. Select an Instance type and opt to Create a new key pair
<img src=images/044.png>

6. In the Create key pair interface:

-   Specify the Key pair name, e.g., aws-keypair-FCJ (optional name, you can set any).
-   Choose Key pair type: RSA
-   Select Private key file format: .pem

<img src=images/045.png>

7. Configure the Network:

    - Select the VPC: ASG
    - Choose the Subnet: Public Subnet 1
    - Enable Auto-assign public IP
    - For Firewall (Security Group), select Select existing security group
    - Choose Public subnet -SG
    - Click Launch instance

<img src=images/046.png>

8. Complete the instance creation
<img src=images/047.png>

### Create EC2 Instances in Private Subnets

<img src=images/048.png>
<img src=images/049.png>
<img src=images/050.png>
<img src=images/051.png>
<img src=images/052.png>

- Select EC2 Private:
    - Choose Details
    - Store Private IPv4 addresses

<img src=images/053.png>


<a name="CONNECT"></a>
## VII. Test Connection

<img src=images/054.png>

1. SSH to EC2 Public instance using terminal

<img src=images/055.png>

2. Testing Internet Connection of EC2 Public
- Execute the following command to test the internet connection of the EC2 Public instance

```
ping amazon.com -c5
```

<img src=images/056.png>

- Make a ping to EC2 private
```
ping <IP Private EC2 Private>
```

<img src=images/057.png>

3. SSH to EC2 private instance using terminal

- Convert aws-keypair-FCJ.pem -> aws-keypair-FCJ.ppk
```
puttygen aws-keypaiOAr-FCJ.pem -o aws-keypair-FCJ.ppk
```

- Copy file aws-keypair-FCJ.ppk to /home 
```
scp -i ~/Downloads/aws-keypair-FCJ.pem ~/Downloads/aws-keypair-FCJ.pem ec2-user@ec2-51-20-72-46.eu-north-1.compute.amazonaws.com:/home/ec2-user
```
<img src=images/058.png>

- Check file on EC2 instance
<img src=images/059.png>

- Cập nhật quyền cho file aws-keypair.pem bằng cách chạy lệnh chmod 400 aws-keypair.pem. AWS yêu cầu file key pair cần được hạn chế quyền truy cập trước khi được sử dụng để kết nối tới máy chủ EC2.
```
chmod 400 aws-keypair.pem
```

- SSH to EC2 Private
<img src=images/060.png>

- Ping to amazon.com. We can see cannot connect to internet from EC2 Private.
<img src=images/061.png>



<a name="NAT"></a>
## VIII. Create NAT Gateway

### Create a Elastic IP

1. Access EC2:
    - Select Elastic IPs
    - Select Allocate Elastic IP address

<img src=images/062.png>

2. In the Allocate Elastic IP address interface:
    - Public IPv4 address pool: Select Amazon’s pool of IPv4 addresses
    - Select Allocate

<img src=images/063.png>

3. Successfully created a Public IP Address
<img src=images/064.png>

4. Access VPC:
    - Select NAT Gateways
    - Create NAT gateway
<img src=images/065.png>

5. In NAT gateway interface:
    - Name: Enter **NAT gateway - FCJ**
    - Subnet: Select **Public subnet 2**
    - Connectivity type: Select **Public**
    - Elastic IP allocation ID: Select recently created **Elastic IP**
    - Select Create NAT gateway

<img src=images/066.png>

6. Successfully created NAT gateway

<img src=images/067.png>

### Create Route table - Private and assign to private subnets

7. In the VPC interface:
    - Select Route Tables
    - Select Create route table

<img src=images/068.png>

8. In the Route table interface:
    - Name: Enter **FCJ-Route table-private**
    - VPC: Select **FCJ VPC**
    - Select Create route table

<img src=images/069.png>

9. Finish creating **Route table - Private**

<img src=images/070.png>

10. In the Route table - Private interface:
    - Select Subnet Associations
    - Select Edit subnet associations

<img src=images/071.png>

11. In the Edit subnet associations interface:
    - Choose 2 private subnets
    - Select Save associations

<img src=images/072.png>

12. In the Route table - Private interface:
    - Select Routes
    - Select Edit routes

<img src=images/073.png>

13. In the Edit routes interface:
    - Select Add route
    - Destination: 0.0.0.0/0
    - Target: NAT Gateway
    - Select Save changes

<img src=images/074.png>

14. Check Routes created

<img src=images/075.png>

15. Test ping amazon.com successfully from EC2 Private:

<img src=images/076.png>


<a name="URA"></a>
## IX. Using Reachability Analyzer

1. Access to VPC interface
    - Select **Network Manager**
    - Select **Reachability Analyzer**
    - Select **Create and analyze path**

<img src=images/077.png>
<img src=images/078.png>

2. Implement Path Configuration

    - Name tag, enter **EC2 private with EC2 Public**
    - For Source type, select Instance
    - Select source as EC2 Public
    - For Destination type, select Instance
    - For Destination, select EC2 Private
    - The remaining parameters are left to default.
    - Select Create and analyze path

<img src=images/079.png>

3. Wait 5 minutes will show the Reachable status

<img src=images/080.png>

4. Then see path details.

<img src=images/081.png>






