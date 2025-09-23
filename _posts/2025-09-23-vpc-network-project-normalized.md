---
title: Building a Complete AWS VPC
date: 2025-09-23 00:00:00 +0200
categories: [AWS, Cloud, VPC]
tags: [AWS, VPC, Networking, Cloud Computing, NAT, Peering, FlowLogs]
author: elis_cazacu
description: "Step-by-step walkthrough of building a fully functional AWS VPC with subnets, NAT Gateway, Bastion Host, VPC Peering, Endpoints, and Flow Logs."
---
# VPC Network Project

This project demonstrates how to create a complete, self-sufficient VPC in AWS. We will set up subnets, routing, NAT Gateway, Bastion Host, VPC Peering, Endpoints, Flow Logs, and more.

---

## VPC Components Diagram

![VPC Components Diagram](/assets/img/vpc-project/vpc-components-diagram.png)

> **Note:** This diagram shows the high-level components of our VPC setup.



---

## 1. VPC Creation

We start by creating our DemoVPC.

![Create VPC](/assets/img/vpc-project/vpc-create-vpc-buttonshow.png)

![VPC CIDR Settings](/assets/img/vpc-project/vpc-name-cidr.png)


---

## 2. Subnet Configuration

We configure two public and two private subnets in different Availability Zones.

![Create Subnet](/assets/img/vpc-project/create-subnet-showbutton.png)

![Public Subnet A Settings](/assets/img/vpc-project/public-subnet-a-settings.png)

![Public Subnet B Settings](/assets/img/vpc-project/public-subnet-b-settings.png)

![Private Subnet A Settings](/assets/img/vpc-project/private-subnet-a-settings.png)

![Private Subnet B Settings](/assets/img/vpc-project/private-subnet-b-settings.png)

![Subnets in Dashboard](/assets/img/vpc-project/created-subnets-show-indashboard.png)


---

## 3. EC2 Instance Setup

We configure auto-assign public IPs and launch EC2 instances in public subnets.

![Enable Auto-Assign Public IP](/assets/img/vpc-project/enable-auto-assign-public-address-button-show.png)

![EC2 Instance Connect](/assets/img/vpc-project/showing-ec2-instance-connect.png)

![Successful EC2 Connection](/assets/img/vpc-project/showing-succesful-connection-ec2-instance-public-subnet-a.png)


---

## 4. Internet Gateway Configuration

We create and attach an Internet Gateway to allow internet access for public subnets.

![Internet Gateway Menu](/assets/img/vpc-project/create-gateway-dashboard-and-button-show.png)

![Create Internet Gateway](/assets/img/vpc-project/create-demoigw-internet-gateway-show.png)

![Attach IGW](/assets/img/vpc-project/showing-vpc-dettached-and-attach-button.png)

![IGW Attached](/assets/img/vpc-project/showing-how-we-attach-igw-to-demo-vpc.png)


---

## 5. Route Tables

We create separate public and private route tables and associate subnets.

![Route Tables Menu](/assets/img/vpc-project/showing-how-we-access-route-tables-in-dashboard-and-the-default-route-table.png)

![Edit Public Route Table](/assets/img/vpc-project/edit-public-route-table-show.png)

![Adding Public Subnets](/assets/img/vpc-project/showing-how-im-adding-public-subnets-associations.png)

![Subnet Associations](/assets/img/vpc-project/add-subnets-associations-show-public-route-table.png)

![Associating Private Subnets](/assets/img/vpc-project/doing-the-same-for-private-subnets.png)

![Public Route Table Rules](/assets/img/vpc-project/creating-public-route-rable.png)

![Private Route Table Rules](/assets/img/vpc-project/private-route-table-show.png)


---

## 6. Bastion Host Setup

We configure a Bastion Host to allow SSH access to private instances.

![Rename Bastion Host](/assets/img/vpc-project/renaming-out-ec2-instance-bastion-host.png)

![Create Key Pair](/assets/img/vpc-project/showing-keypair-create.png)

![Key Pair Settings](/assets/img/vpc-project/showing-keypair-settings.png)

![Private EC2 Instance with Key Pair](/assets/img/vpc-project/creating-private-ec2-instance-showing-keypair-selected.png)

![Private Instance IPv4 Address](/assets/img/vpc-project/show-private-instance-ipv4-address.png)

![Add KeyPair in Bastion Host](/assets/img/vpc-project/adding-keypair-to-bastion-host-nano-command.png)

![Paste Key Pair](/assets/img/vpc-project/paste-contents-of-keypair-in-nano.png)

![Check Key Pair](/assets/img/vpc-project/we-cat-or-check-contents-using-the-cat-command-in-our-demokeypair.pem-file.png)

![Change Key Permissions](/assets/img/vpc-project/change-permissions-keypair-command.png)

![SSH Connection Successful](/assets/img/vpc-project/show-the-authenticity-of-host-cant-be-established-and-ssh-connection-succesful.png)


---

## 7. NAT Gateway

We create a NAT Gateway for private subnets to access the internet.

![Elastic IP Menu](/assets/img/vpc-project/show-where-to-go-for-elastic-ip.png)

![Elastic IP Allocation](/assets/img/vpc-project/elastic-address-settings-page1.png)

![Associate Elastic IP](/assets/img/vpc-project/actions-associate-elastic-ip-address.png)

![NAT Gateway Menu](/assets/img/vpc-project/go-to-vpc-nat-gateways.png)

![Create NAT Gateway](/assets/img/vpc-project/nat-gateway-creation.png)

![NAT Gateway Available](/assets/img/vpc-project/nat-gateway-showing-available.png)

![Private Route Table NAT Rule](/assets/img/vpc-project/private-subnet-route-table-add-nat-gateway-rule.png)

![Private EC2 Internet Access](/assets/img/vpc-project/now-internet-connection-works-in-our-private-ec2-connected-through-ssh-from-bastion-and-pinged-google.png)


---

## 8. Network ACLs

We test Network ACLs to enforce subnet-level security.

![Edit Network ACL Rules](/assets/img/vpc-project/network-acl-show-press-edit-inbound-rules.png)

![Deny HTTP Rule](/assets/img/vpc-project/show-deny-http-port-80-rule-nacl-setting.png)

![Connection Failed via NACL](/assets/img/vpc-project/connection-failed-now-bastion-host-after-changing-network-acl.png)

![Rule Number 140 Allow](/assets/img/vpc-project/showing-inbound-rule-number-140-nacl.png)

![HTTP Access Restored](/assets/img/vpc-project/show-hello-world-connection-successful-after-changing-rule-to-140.png)

![Inbound Rules Order](/assets/img/vpc-project/show-inbound-rules-order-in-network-acls-dashboard.png)

![Delete Outbound Rules](/assets/img/vpc-project/show-how-we-delete-security-group-outbound-rules-traffic-and-remove-all-outbound-traffic.png)

![Restore Outbound Rules](/assets/img/vpc-project/showing-how-i-fixed-back-my-security-group-outbound-rules-to-allow-traffic-anywhere.png)


---

## 9. Bastion Host Web Server Test

We install Apache HTTP server on Bastion Host and test connectivity.

![Install Apache](/assets/img/vpc-project/sudo-yum-install-y-httpd-show.png)

![Enable HTTPD](/assets/img/vpc-project/sudo-systemctl-enable-start-httpd.png)

![Create index.html](/assets/img/vpc-project/sudo-su-echo-hello-world-file-create-and-show-with-cat.png)

![Bastion Host HTTP Rule](/assets/img/vpc-project/bastion-host-security-group-http-enable-0.0.0.0-show.png)

![Bastion Public IPv4](/assets/img/vpc-project/testing-connection-bastion-host-showing-ipv4-address-in-dashboard.png)

![Successful Hello World](/assets/img/vpc-project/testing-connection-bastion-host-showing-connection-successful-browser-hello-world.png)


---

## 10. VPC Peering

We set up VPC peering to connect DemoVPC with DefaultVPC.

![VPC Peering Non-Transitive](/assets/img/vpc-project/vpc-peering-diagram-show-not-transitive.png)

![Diagram before Peering](/assets/img/vpc-project/show-current-state-of-our-diagram-and-how-we-will-add-our-vpc-peering-connection.png)

![Rename Default VPC Instance](/assets/img/vpc-project/now-lets-rename-it-default-vpc-instance-show.png)

![Curl Failed](/assets/img/vpc-project/show-curl-fail-to-our-bastion-host-private-ipv4-address-from-our-defaultvpc-instance.png)

![Create Peering Connection](/assets/img/vpc-project/peering-connections-create-peering-button-show.png)

![Peering Connection Settings](/assets/img/vpc-project/show-vpc-pairing-connection-settings.png)

![Accept Peering Connection](/assets/img/vpc-project/vpc-connection-has-been-request-press-accept-request-button-show.png)

![Peering Connection Established](/assets/img/vpc-project/now-we-can-see-that-vpc-connection-has-been-established-successfully.png)

![Rename Default Route Table](/assets/img/vpc-project/show-how-we-rename-defaultvpc-route-table.png)

![Edit Routes](/assets/img/vpc-project/vpc-peering-public-route-table-routes-edit-routes-button-show.png)

![Add Route to Default VPC](/assets/img/vpc-project/vpc-peering-show-adding-route-to-our-default-vpc-cidr-and-target-is-a-peering-connection-demopeeringconnection.png)

![Add Route from Default VPC](/assets/img/vpc-project/vpc-peering-show-adding-route-to-our-demo-vpc-routetable-from-our-defaultvpc.png)

![Curl Success after Routes](/assets/img/vpc-project/show-curl-success-to-our-bastion-host-private-ipv4-address-from-our-defaultvpc-instance-after-changing-routes.png)


---

## 11. VPC Endpoints

We configure a Gateway Endpoint for S3.

![Diagram with Endpoint](/assets/img/vpc-project/show-current-state-of-our-diagram-2-and-how-we-will-add-our-vpc-endpoint.png)

![Connect Private Instance](/assets/img/vpc-project/vpc-endpoint-now-we-connect-from-bastion-to-our-private-ec2-instance.png)

![Modify IAM Role](/assets/img/vpc-project/vpc-endpoint-modify-iamrole-button-show.png)

![Create IAM Role](/assets/img/vpc-project/vpc-endpoint-modify-iam-role-press-create-iam-role.png)

![IAM Create Role](/assets/img/vpc-project/vpc-endpoint-iam-dashboard-create-role-button.png)

![IAM Select EC2](/assets/img/vpc-project/vpc-endpoint-iam-role-creation-select-ec2.png)

![IAM S3 Read Only](/assets/img/vpc-project/vpc-endpoint-iam-select-permission-amazon-s3-read-only.png)

![Finalize IAM Role](/assets/img/vpc-project/vpc-endpoint-iam-final-step-name-the-role-and-create.png)

![Attach IAM Role](/assets/img/vpc-project/vpc-endpoint-modify-iam-role-finally-back-refresh-and-select-our-role.png)

![Remove NATGW Route](/assets/img/vpc-project/vpc-endpoint-s3-natgw-remove-route-test-show-remove.png)

![Fail without Endpoint](/assets/img/vpc-project/vpc-endpoint-s3-natgw-remove-route-test-show-testing-awss3-and-curl.png)

![Create Endpoint](/assets/img/vpc-project/vpc-endpoint-show-endpoint-create-button-in-dashboard.png)

![Select AWS Services](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-1-aws-services-select.png)

![Select S3 Gateway](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-2-s3-gw-select.png)

![Configure Endpoint Network](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-3-network-and-routetable-select.png)

![Endpoint Added to Route Table](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-4-show-that-our-endpoint-has-been-added-to-route-table.png)

![Successful S3 Connection](/assets/img/vpc-project/vpc-endpoint-creating-endpoint-5-test-s3-connectionnow-success.png)


---

## 12. VPC Flow Logs

We configure VPC Flow Logs and analyze them with Athena.

![Flow Logs Creation](/assets/img/vpc-project/vpc-flowlogs-creation-page-show.png)

![Create Flow Logs S3 Bucket](/assets/img/vpc-project/vpc-flowlogs-create-s3bucket.png)

![Bucket ARN](/assets/img/vpc-project/vpc-flowlogs-show-bucket-arn-in-details.png)

![Copy Bucket ARN](/assets/img/vpc-project/vpc-flowlogs-copy-bucket-arn-into-flowlog-settings.png)

![Flow Logs Dashboard](/assets/img/vpc-project/vpc-flowlogs-show-button-in-dashboard.png)

![Create CloudWatch Flow Logs](/assets/img/vpc-project/vpc-flowlogs-create-cloudwatch-logs-page.png)

![Flow Logs Role Trusted Entity](/assets/img/vpc-project/vpc-flowlogs-create-role-select-trusted-entity.png)

![CloudWatch Logs Full Access](/assets/img/vpc-project/vpc-flowlogs-create-role-add-permissions-cw-logs-full-access.png)

![Create Log Group](/assets/img/vpc-project/vpc-flowlogs-create-log-groups-button-show.png)

![Log Group Settings](/assets/img/vpc-project/vpc-flowlogs-create-log-groups-show-settings.png)

![Select Destination Log Group](/assets/img/vpc-project/vpc-flowlogs-create-destination-log-group-select.png)

![Select Service Role](/assets/img/vpc-project/vpc-flowlogs-select-servicerole-show.png)

![Both Flow Logs Created](/assets/img/vpc-project/vpc-flowlogs-show-both-created-flowlogs.png)

![S3 Logs Created](/assets/img/vpc-project/vpc-flowlogs-show-created-s3-logs.png)

![Log Stream Bastion](/assets/img/vpc-project/vpc-flowlogs-show-logstream-bastion.png)

![Rejected Events Bastion](/assets/img/vpc-project/vpc-flowlogs-bastion-eni-events-show-rejected.png)

![Athena Settings](/assets/img/vpc-project/vpc-flowlogs-athena-show-edit-settings-button.png)

![Athena Query Result Location](/assets/img/vpc-project/vpc-flowlogs-athena-queryeditor-manage-settings-show-selecting-query-result-bucket.png)

![Copy S3 URI](/assets/img/vpc-project/vpc-flowlogs-copy-s3-uri.png)

![Paste S3 URI](/assets/img/vpc-project/vpc-flowlogs-athena-pasting-s3uri-in-location.png)

![Table Created](/assets/img/vpc-project/vpc-flowlogs-table-created-after-running.png)

![Copy Date URI](/assets/img/vpc-project/vpc-flowlogs-copy-s3-uri-2-date.png)

![Add Partition](/assets/img/vpc-project/vpc-flowlogs-copy-into-editor-and-date.png)

![Athena Query Results](/assets/img/vpc-project/vpc-flowlogs-athena-runquery-100-results.png)


---

## 13. VPN and Direct Connect (Overview)

We briefly show how to set up VPN connections and mention Direct Connect.

![Create Virtual Private Gateway](/assets/img/vpc-project/vpc-virtual-private-gateway-create-button.png)

![Virtual Private Gateway Settings](/assets/img/vpc-project/vpc-virtual-private-gateway-settings-page.png)

![Create Site-to-Site VPN](/assets/img/vpc-project/vpc-site-2-site-create-button.png)

![VPN Connection Settings](/assets/img/vpc-project/vpc-vpn-site2site-connection-settings.png)


---

## Lessons Learned

- How to set up subnets, routing, and gateways in AWS
- Configuring Bastion Hosts and NAT Gateways
- Applying Security Groups vs NACLs
- Setting up VPC Peering and Endpoints
- Using Flow Logs with Athena for analysis


## Future Work

- Configure Transit Gateway across multiple VPCs and accounts
- Set up Direct Connect integration
- Explore Egress-only Internet Gateway
