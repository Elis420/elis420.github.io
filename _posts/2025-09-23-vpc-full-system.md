---
title: "Building a Full VPC System in AWS (With NAT, Bastion, Endpoints, and Flow Logs)"
date: 2025-09-23 12:00:00 +0300
categories: [AWS, Networking]
tags: [AWS, VPC, NAT, Bastion, Subnets, Route Tables, Endpoints, FlowLogs]
author: Elis
description: "Step-by-step guide on setting up a complete AWS VPC system with public/private subnets, NAT Gateway, Bastion host, VPC Peering, Endpoints, and Flow Logs."
---

# VPC Network Project 🏗️

In this project, we will create a fully functional, self-sufficient AWS VPC. The setup will include public and private subnets, NAT Gateway, Bastion host, VPC Peering, Endpoints, and Flow Logs sent to both CloudWatch and S3. We'll also set up Athena to query our Flow Logs for analysis.

Let's start with a **VPC Components Diagram** to visualize our architecture:

![VPC Components Diagram](/assets/img/vpc-project/vpc-components-diagram.png)

> This diagram shows how all VPC components connect: Internet Gateway, NAT Gateway, Route Tables, Public/Private Subnets, EC2 Instances, VPC Peering, Endpoints, and Flow Logs.
{: .prompt-info }

---

## 1. Creating the VPC 🏗️

First, access the **VPC creation page** in the AWS Console.

![Create VPC Dashboard](/assets/img/vpc-project/vpc-create-vpc-buttonshow.png)

After filling out the details, our **VPC is created**:

![VPC Created](/assets/img/vpc-project/show-default-vpc-in-dashboard.png)

---

## 2. Creating Subnets 🌐

### Public Subnet A
- Region: Europe (Frankfurt) eu-central-1a  
- CIDR: 10.0.0.0/24  

![Public Subnet A Settings](/assets/img/vpc-project/public-subnet-a-settings.png)

### Public Subnet B
- Region: Europe (Frankfurt) eu-central-1b  
- CIDR: 10.0.1.0/24  

![Public Subnet B Settings](/assets/img/vpc-project/public-subnet-b-settings.png)

### Private Subnet A
- Region: Europe (Frankfurt) eu-central-1a  
- CIDR: 10.0.16.0/20  

![Private Subnet A Settings](/assets/img/vpc-project/private-subnet-a-settings.png)

### Private Subnet B
- Region: Europe (Frankfurt) eu-central-1b  
- CIDR: 10.0.32.0/20  

![Private Subnet B Settings](/assets/img/vpc-project/private-subnet-b-settings.png)

Finally, let's see our **subnets in the dashboard**:

![Created Subnets Dashboard](/assets/img/vpc-project/created-subnets-show-indashboard.png)

---

## 3. Launching the EC2 Instances 🚀

Before creating EC2 instances, ensure **auto-assign Public IPv4** is enabled for public subnets:

![Enable Auto-Assign Public IPv4](/assets/img/vpc-project/enable-auto-assign-public-address-button-show.png)

> This ensures our public instances receive a public IP automatically.
{: .prompt-info }

For detailed info on setting up your first EC2 instance, refer to my previous guide: `_posts/2025-09-10-you-first-ec2instance.md`

### Public EC2 Instance (Bastion Host)
- Select **DemoVPC** and **Public Subnet A**
- Key Pair: `DemoKeyPair`
- Security Group: Allow SSH (port 22) from anywhere

![Key Pair Creation](/assets/img/vpc-project/showing-keypair-create.png)

Attempting to connect **before IGW setup**:

```bash
ssh -i "DemoKeyPair.pem" ec2-user@<Public-IP-Address>
``` 
This will **fail** because the Internet Gateway is not attached yet:

![SSH Failed](/assets/img/vpc-project/showing-how-ssh-failed-through-instance-connect.png)

---

## 4. Setting Up Internet Gateway (IGW) 🌐

### Creating the IGW
![Create IGW](/assets/img/vpc-project/create-demoigw-internet-gateway-show.png)

Attach it to **DemoVPC**:

![Attach IGW to VPC](/assets/img/vpc-project/showing-vpc-dettached-and-attach-button.png)

> Even now, connecting to the EC2 instance fails without proper route tables.
{: .prompt-info }

---

## 5. Configuring Route Tables 🛣️

Create separate **public and private route tables**.  

Associate **public subnets** to the public route table:

![Public Route Table Associations](/assets/img/vpc-project/showing-how-im-adding-public-subnets-associations.png)

Associate **private subnets** to the private route table:

![Private Route Table Associations](/assets/img/vpc-project/showing-private-subnets-association.png)

---

## 6. Public Route Table Rule

To connect public subnets to the internet:

- Destination: `0.0.0.0/0`
- Target: IGW (DemoIGW)

Now we can **successfully connect to our public EC2 instance**:

![EC2 Instance Connect Success](/assets/img/vpc-project/showing-succesful-connection-ec2-instance-public-subnet-a.png)

---

## 7. Private EC2 Instance via Bastion Host 🔐

We need a **Bastion Host** to connect to private instances.  
We will use our **already created public EC2 instance** as the Bastion Host.

Rename it:

![Rename EC2 to BastionHost](/assets/img/vpc-project/rename-public-ec2-to-bastionhost.png)

Create a **key pair** for the private instance:

![Create Key Pair](/assets/img/vpc-project/showing-keypair-create.png)

> Save the `.pem` file securely. This will be used to SSH into the private instance through the Bastion Host.
{: .prompt-info }

---

### Private EC2 Instance Network Settings

- VPC: DemoVPC
- Subnet: Private Subnet A
- Security Group: PrivateSG
  - Inbound rules: Allow SSH **only** from Bastion Host security group
- Key Pair: DemoKeyPair

![Private Instance Network Settings](/assets/img/vpc-project/private-instance-network-settings.png)

---

### Connecting to Private EC2 via Bastion Host

1. Check **Private IPv4 address** of the private instance:

![Private IPv4 Address](/assets/img/vpc-project/check-private-ip-address.png)

2. Create `.pem` file on Bastion Host:

```bash
nano DemoKeyPair.pem
```
3. Paste the contents of your private key, save, and exit (Ctrl + X, Y, Enter).

4. Verify key contents:

```bash
cat DemoKeyPair.pem
```

5. Set correct permissions:

```bash
chmod 0400 DemoKeyPair.pem
```

6. Connect to private instance:

```bash
ssh -i "DemoKeyPair.pem" ec2-user@10.0.18.74
```

You may see:

> "The authenticity of host can't be established…"
{: .prompt-info }

Approve it to continue. Connection succeeds:

![SSH Connection Successful](/assets/img/vpc-project/show-the-authenticity-of-host-cant-be-established-and-ssh-connection-succesful.png)

At this point, pinging external websites will fail until we set up NAT Gateway.
{: .prompt-info }

---

## 8. Setting Up NAT Gateway 🌐🛡️

NAT allows **private instances** to access the internet securely without exposing their IPs.

### 1. Allocate Elastic IP
Go to **VPC → Elastic IPs → Allocate Elastic IP Address**.  

![Allocate Elastic IP](/assets/img/vpc-project/nat-allocate-elastic-ip.png)

> Our Elastic IP: `3.126.86.249`
{: .prompt-info }

### 2. Create NAT Gateway
Go to **VPC → NAT Gateways → Create NAT Gateway**:

- Name: `MyNATGateway`
- Subnet: Public Subnet (must have route to IGW)
- Elastic IP: Use the one allocated

![Create NAT Gateway](/assets/img/vpc-project/nat-create-natgw.png)

Wait until **Status = Available**.

### 3. Update Private Route Table
Add a new route in the **Private Route Table**:

- Destination: `0.0.0.0/0`
- Target: `MyNATGateway`

![Private Route Table NAT Route](/assets/img/vpc-project/nat-add-route-private-subnet.png)

Now our private EC2 can access the internet:

```bash
ssh -i "DemoKeyPair.pem" ec2-user@10.0.18.74
ping google.com
``` 

NATGW requires IGW: Private Subnet → NATGW → IGW
{: .prompt-info }

## 9. HTTP Test on Bastion Host 🌐

```bash
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd
sudo su
echo "hello world" > /var/www/html/index.html
cat /var/www/html/index.html
```
-  Update Bastion Host Security Group to allow HTTP (port 80) from anywhere:

![Bastion Host HTTP Rule](/assets/img/vpc-project/bastion-host-security-group-http-enable-0.0.0.0-show.png)


- Open browser: http://<Bastion-Public-IP>

![Successful Hello World](/assets/img/vpc-project/testing-connection-bastion-host-showing-connection-successful-browser-hello-world.png)

## 10. Network ACLs (NACLs) 🔒

-  Default ACL is attached to all subnets.

-  Add HTTP Deny Rule

  - Rule number: 80

  - Inbound: Deny port 80
  
![Edit Network ACL Rules](/assets/img/vpc-project/network-acl-show-press-edit-inbound-rules.png)

![Deny HTTP Rule](/assets/img/vpc-project/show-deny-http-port-80-rule-nacl-setting.png)

-  Try to access HTTP → fails

![Connection Failed via NACL](/assets/img/vpc-project/connection-failed-now-bastion-host-after-changing-network-acl.png)




- Now let's set up our inbound rule number to 140 and see that connection will be successful, since, they have numerical priority and our http allow rule number 100 has precedence over rule number 140 , deny acccess.

  - We already have our HTTP ALLOW access rule 

  - Rule number: 100

  - Inbound: Allow port 80
  
![Rule Number 140 Allow](/assets/img/vpc-project/showing-inbound-rule-number-140-nacl.png)

- Now if we refresh our website for our public ip we can see that it will now show hello world!


![HTTP Access Restored](/assets/img/vpc-project/show-hello-world-connection-successful-after-changing-rule-to-140.png)

- Now show our nacl inbound security group order in the dashboard and show it's indeed showing as the second in line, since, the rule 100 has precedence.

![Inbound Rules Order](/assets/img/vpc-project/show-inbound-rules-order-in-network-acls-dashboard.png)

- We're not going to show this now, but you can test it, that the same applies for outbound rules, if you remove
access to everyone in outbound traffic, that will block return traffic and connection will fail.
So it's good practice, if we have issues with our connection to our instance, to not just check security groups
but also our Network NACLs, which act at the subnet level, while Security Groups act on the attached EC2 instances


Note: NACLs are stateless. Security Groups are stateful. Outbound restrictions affect NAT/EC2 differently.
{: .prompt-info }

- So if for example we remove our outbound rules, it will still allow us to connect to the instance, because our inbound connection has already been allowed so we will automatically be bypassed in our return traffic

---

## 11. VPC Peering 🔗

> VPC Peering requires non-overlapping CIDRs and is non-transitive.  

### Peering Diagram
![VPC Peering Diagram](/assets/img/vpc-project/vpc-peering-diagram.png)

Peer **DemoVPC (10.0.0.0/16)** → **DefaultVPC (172.31.0.0/16)**  

### Step 1: Create Peering Connection

- Go to **VPC → Peering Connections → Create Peering Connection**
- Select **DemoVPC** → **DefaultVPC**
- Ensure CIDRs do not overlap

![Create VPC Peering](/assets/img/vpc-project/vpc-peering-create-connection-dashboard.png)

### Step 2: Accept Peering

- Accept the pending peering connection in the console

![Accept Peering](/assets/img/vpc-project/vpc-connection-has-been-request-press-accept-request-button-show.png)

- Status: **Active**  

![Peering Active](/assets/img/vpc-project/now-we-can-see-that-vpc-connection-has-been-established-successfully.png)

### Step 3: Update Route Tables

**DemoVPC Private Route Table:**

- Destination: `172.31.0.0/16`
- Target: Peering connection

![DemoVPC Route Table Update](/assets/img/vpc-project/vpc-peering-show-adding-route-to-our-default-vpc-cidr-and-target-is-a-peering-connection-demopeeringconnection.png)

**DefaultVPC Route Table:**

- Destination: `10.0.0.0/16`
- Target: Peering connection

![DefaultVPC Route Table Update](/assets/img/vpc-project/vpc-peering-show-adding-route-to-our-demo-vpc-routetable-from-our-defaultvpc.png)

### Step 4: Test Connectivity

```bash
ssh -i "DemoKeyPair.pem" ec2-user@10.0.18.74
curl <Private-IP-BastionHost>
``` 
> Connection works after updating both route tables.
{: .prompt-info }

## 12. VPC Endpoints (PrivateLink) 🛠️

> VPC Endpoints allow private EC2 instances to access AWS services internally without going through the internet (IGW/NATGW). This improves security and efficiency.

![Diagram with Endpoint](/assets/img/vpc-project/show-current-state-of-our-diagram-2-and-how-we-will-add-our-vpc-endpoint.png)

---

### Step 1: Test Current S3 Access (via NAT)

```bash
ssh -i "DemoKeyPair.pem" ec2-user@10.0.18.74
aws s3 ls
curl https://www.google.com
```

### Step 2: Remove NAT Gateway Route

- Edit PrivateRouteTable → Remove route 0.0.0.0/0 pointing to NATGW

![Remove NATGW Route](/assets/img/vpc-project/vpc-endpoint-s3-natgw-remove-route-test-show-remove.png)

- Test connectivity again:

```bash
aws s3 ls
curl https://www.google.com
```
![Fail without Endpoint](/assets/img/vpc-project/vpc-endpoint-s3-natgw-remove-route-test-show-testing-awss3-and-curl.png)

> Access fails since internet is disconnected. Now we will use VPC Endpoint.
{: .prompt-tip }

## Step 3: Create VPC Endpoint (S3 Gateway)

- Navigate to VPC → Endpoints → Create Endpoint

![Create Endpoint](/assets/img/vpc-project/vpc-endpoint-show-endpoint-create-button-in-dashboard.png)

  - Select AWS Services, then choose S3 Gateway
  
![Select AWS Services](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-1-aws-services-select.png)

![Select S3 Gateway](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-2-s3-gw-select.png)


  - Select DemoVPC and PrivateRouteTable
  
![Configure Endpoint Network](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-3-network-and-routetable-select.png)

![Endpoint Added to Route Table](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-4-show-that-our-endpoint-has-been-added-to-route-table.png)

- For this matter, we also have to create e role for this instance in order to be able to access S3 from it
so in our EC2 Instances dashboard, right click our PrivateInstance, Security and press modify IAM Role
![Modify IAM Role](/assets/img/vpc-project/vpc-endpoint-modify-iamrole-button-show.png)

-  Now let's press on create IAM Role
![Create IAM Role](/assets/img/vpc-project/vpc-endpoint-modify-iam-role-press-create-iam-role.png)

now in iam press create role
![IAM Create Role](/assets/img/vpc-project/vpc-endpoint-iam-dashboard-create-role-button.png)

select ec2 instance role
![IAM Select EC2](/assets/img/vpc-project/vpc-endpoint-iam-role-creation-select-ec2.png)

Now click on next, and for the policy select AmazonS3ReadOnlyAccess, and click on Next
![IAM S3 Read Only](/assets/img/vpc-project/vpc-endpoint-iam-select-permission-amazon-s3-read-only.png)

And now finally, in the review let's give a name to our role - DemoRoleEC2-S3ReadOnly and press create Role
![Finalize IAM Role](/assets/img/vpc-project/vpc-endpoint-iam-final-step-name-the-role-and-create.png)

And now we can go back to our EC2 Instance, and press refresh on the role selection and select our role.
![Attach IAM Role](/assets/img/vpc-project/vpc-endpoint-modify-iam-role-finally-back-refresh-and-select-our-role.png)

Now we will go back to our Private EC2 Instance and run aws s3 ls command  we can see that it's listed all of my s3 keys

> The private instance now has permission to access S3 through the VPC Endpoint.
{: .prompt-tip }

## Step 5: Test S3 Access via Endpoint

- Connect to the private instance via Bastion and run:

```bash

aws s3 ls

```

![Successful S3 Connection](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-5-test-s3-connectionnow-success.png)

The command succeeds. Private EC2 instance now accesses S3 without using NAT Gateway or Internet. ✅
{: .prompt-info }

## 13. Flow Logs and Monitoring 📊

> Flow Logs allow you to capture network traffic metadata for your VPC, subnets, or ENIs. Sending logs to S3 or CloudWatch enables analytics and security monitoring.

---

### Step 1: Create Flow Log to S3

- Navigate to **VPC → DemoVPC → Flow Logs → Create Flow Log**
- Set:
  - Name: `DemoFlowLogS3`
  - Maximum aggregation interval: 1 minute
  - Destination: **S3 Bucket**
- Create S3 bucket for logs: `demo-elis-vpc-flow-logs-v1`
- Copy bucket ARN and paste in Flow Log settings

![Flow Logs Setup Page](/assets/img/vpc-project/vpc-flowlogs-creation-page-show.png)
![Show Bucket ARN](/assets/img/vpc-project/vpc-flowlogs-show-bucket-arn-in-details.png)
![Copy Bucket ARN](/assets/img/vpc-project/vpc-flowlogs-copy-bucket-arn-into-flowlog-settings.png)

> The Flow Log will automatically attach the necessary IAM policy to the S3 bucket.

---

### Step 2: Create Flow Log to CloudWatch

- Create a Flow Log: `DemoFlowLogCWLogs`
- Aggregation interval: 1 minute
- Destination: **CloudWatch Logs**
- Create Service Role:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Statement1",
      "Effect": "Allow",
      "Principal": {
        "Service": "vpc-flow-logs.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

-  Now Click on next, Assign CloudWatchLogsFullAccess permission

![CloudWatch Logs Full Access](/assets/img/vpc-project/vpc-flowlogs-create-role-add-permissions-cw-logs-full-access.png)

- Role name: flowlogsRole




- Create a Log Group in CloudWatch: VPCFlowLogs, retention: 1 day

![Create Log Group](/assets/img/vpc-project/vpc-flowlogs-create-log-groups-button-show.png)

![Log Group Settings](/assets/img/vpc-project/vpc-flowlogs-create-log-groups-show-settings.png)

![Select Destination Log Group](/assets/img/vpc-project/vpc-flowlogs-create-destination-log-group-select.png)

![Select Service Role](/assets/img/vpc-project/vpc-flowlogs-select-servicerole-show.png)

- Now, Flow Logs are being captured in both S3 and CloudWatch.

![Both Flow Logs Created](/assets/img/vpc-project/vpc-flowlogs-show-both-created-flowlogs.png)

- Now if we check our S3 Bucket objects, we can observe that some logs have already been created

![S3 Logs Created](/assets/img/vpc-project/vpc-flowlogs-show-created-s3-logs.png)

- Now let's check CloudWatch, and see that we already have some log streams created, these are associated to our
EC2 Instance attached ENI, eni-0fad996cf359c5485-all is attached to our Bastion Host, 
 
![Log Stream Bastion](/assets/img/vpc-project/vpc-flowlogs-show-logstream-bastion.png)

> Logs show rejected requests, which are usually random attack attempts.
{: .prompt-info }

![Rejected Events Bastion](/assets/img/vpc-project/vpc-flowlogs-bastion-eni-events-show-rejected.png)



## Step 4: Analyze Flow Logs with Athena 🧐

> Athena allows you to run SQL queries on your VPC Flow Logs stored in S3. This helps analyze network traffic, detect security events, and generate insights.

---

### Step 4.1: Configure Athena Query Result Location

- Navigate to **Athena → Settings → Manage Settings**
- Set **Query result location** to an S3 bucket (e.g., `demo-athena-elis-v1/athena`)
- This is where Athena will store query results.

![Athena Query Location](/assets/img/vpc-project/vpc-flowlogs-athena-queryeditor-manage-settings-show-selecting-query-result-bucket.png)

> Make sure the bucket exists and you have write permissions.

---

### Step 4.2: Create a Table for VPC Flow Logs

- Copy the DDL statement from AWS documentation:
 https://docs.aws.amazon.com/athena/latest/ug/vpc-flow-logs-create-table-statement.html 

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS vpc_flow_logs (
  version int,
  account_id string,
  interface_id string,
  srcaddr string,
  dstaddr string,
  srcport int,
  dstport int,
  protocol int,
  packets bigint,
  bytes bigint,
  start bigint,
  end bigint,
  action string,
  log_status string
)
PARTITIONED BY (date string)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ' '
LOCATION 's3://<your-flowlogs-bucket>/AWSLogs/<account-id>/vpcflowlogs/<region>/';
```

-  Replace <your-flowlogs-bucket>, <account-id>, and <region> with your actual values.
  - This creates a table linked to your S3 Flow Logs.

![Copy S3 URI](/assets/img/vpc-project/vpc-flowlogs-copy-s3-uri.png)

![Paste S3 URI](/assets/img/vpc-project/vpc-flowlogs-athena-pasting-s3uri-in-location.png)

 - Then we click hit run and see that our table has been created:

![Table Created](/assets/img/vpc-project/vpc-flowlogs-table-created-after-running.png)

### Step 4.3: Add Partitions 🗂️

- Athena requires partitions to read daily logs efficiently.
- Use the following `ALTER TABLE` statement:

```sql
ALTER TABLE vpc_flow_logs
ADD PARTITION (`date`='YYYY-MM-dd')
LOCATION 's3://<your-flowlogs-bucket>/AWSLogs/<account-id>/vpcflowlogs/<region>/YYYY/MM/dd';
```

- Replace YYYY-MM-dd and the S3 URI with your current date and bucket path.

![Copy Date URI](/assets/img/vpc-project/vpc-flowlogs-copy-s3-uri-2-date.png)
![Add Partition](/assets/img/vpc-project/vpc-flowlogs-copy-into-editor-and-date.png)

- After running this, Athena can query the Flow Logs for the specified date.

### Step 4.4: Run Sample Queries 🔍

![Athena Query Results](/assets/img/vpc-project/vpc-flowlogs-athena-runquery-100-results.png)

```sql
SELECT day_of_week(date) AS day,
       date,
       interface_id,
       srcaddr,
       action,
       protocol
FROM vpc_flow_logs
WHERE action = 'REJECT' AND protocol = 6
LIMIT 100;
```
- Execute this query in Athena Query Editor.

- Results include:

    - day – Day of the week

    - date – Log date

    - interface_id – ENI associated with instance

    - srcaddr – Source IP address

    - action – ACCEPT or REJECT

    - protocol – Protocol number (6 = TCP)
	
## Step 4.5: Next Steps with Athena 🚀

- Create more complex queries, for example:

  - Group by source IP to identify frequent attackers

  - Count REJECT vs ACCEPT actions

  - Analyze traffic patterns over time

  - Athena allows deep insights into your network security, helping you monitor and troubleshoot your AWS VPC environment efficiently.
  
  
## 14. VPN Connections (Overview) 🔒

Setting up a VPN allows secure communication between your on-premises network and AWS VPC.  
For this project, we will only outline the steps and show screenshots; no live connection is created.

### 14.1 Create Virtual Private Gateway

- Navigate to **VPC → Virtual Private Gateways → Create Virtual Private Gateway**.
- Name it: `DemoVPGW`.
- Attach it to your VPC: `DemoVPC`.

![Virtual Private Gateway Create](/assets/img/vpc-project/vpc-virtual-private-gateway-create-button.png)

![VPGW Settings](/assets/img/vpc-project/vpc-virtual-private-gateway-settings-page.png)

> The Virtual Private Gateway is now ready to handle VPN connections.  
{: .prompt-info }

---

### 14.2 Site-to-Site VPN Connection

- Navigate to **VPC → VPN Connections → Create VPN Connection**.
- Settings:
  - Virtual Private Gateway: `DemoVPGW`
  - Customer Gateway: `<Your Customer Gateway>`
  - Routing Options: Static or Dynamic
- Click **Create VPN Connection**.

![Site-to-Site VPN Create](/assets/img/vpc-project/vpc-site-2-site-create-button.png)

![VPN Connection Settings](/assets/img/vpc-project/vpc-vpn-site2site-connection-settings.png)

> Note: AWS Direct Connect, Transit Gateway, and Egress-Only Internet Gateway are additional options for advanced connectivity.  
{: .prompt-info }

---

## 🎉 Congratulations! 🎉

You have successfully built a full AWS VPC with:

- Public and Private Subnets  
- NAT Gateway  
- Bastion Host  
- Security Groups & NACLs  
- VPC Peering  
- VPC Endpoints  
- Flow Logs & Athena Queries  
- VPN (Overview)

> Your environment is now fully operational and ready for further scaling, monitoring, and security enhancements.  
{: .prompt-info }

## Future Projects & To-Do List 🛠️

Future ideas: use **Transit Gateway** for multi-VPC connectivity, **Direct Connect** for private high-bandwidth links, and **Egress-Only IGW** for IPv6 private subnets.  
Keep a **To-Do List website** to track improvements and document VPC designs.  
Next steps: automate with **CloudFormation/Terraform** and expand **Athena analytics** for Flow Logs.  
{: .prompt-info }

