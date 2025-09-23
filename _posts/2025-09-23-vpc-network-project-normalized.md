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

![VPC Components Diagram]({ { '/assets/img/vpc-project/vpc-components-diagram.png' | relative_url } })

> **Note:** This diagram shows the high-level components of our VPC setup.

---

## 1. VPC Creation

We start by creating our DemoVPC.

![Create VPC]({ { '/assets/img/vpc-project/vpc-create-vpc-buttonshow.png' | relative_url } })

![VPC CIDR Settings]({ { '/assets/img/vpc-project/vpc-name-cidr.png' | relative_url } })

---

## 2. Subnet Configuration

We configure two public and two private subnets in different Availability Zones.

![Create Subnet]({ { '/assets/img/vpc-project/create-subnet-showbutton.png' | relative_url } })

![Public Subnet A Settings]({ { '/assets/img/vpc-project/public-subnet-a-settings.png' | relative_url } })

![Public Subnet B Settings]({ { '/assets/img/vpc-project/public-subnet-b-settings.png' | relative_url } })

![Private Subnet A Settings]({ { '/assets/img/vpc-project/private-subnet-a-settings.png' | relative_url } })

![Private Subnet B Settings]({ { '/assets/img/vpc-project/private-subnet-b-settings.png' | relative_url } })

![Subnets in Dashboard]({ { '/assets/img/vpc-project/created-subnets-show-indashboard.png' | relative_url } })

---

## 3. EC2 Instance Setup

We configure auto-assign public IPs and launch EC2 instances in public subnets.

![Enable Auto-Assign Public IP]({ { '/assets/img/vpc-project/enable-auto-assign-public-address-button-show.png' | relative_url } })

![EC2 Instance Connect]({ { '/assets/img/vpc-project/showing-ec2-instance-connect.png' | relative_url } })

![Successful EC2 Connection]({ { '/assets/img/vpc-project/showing-succesful-connection-ec2-instance-public-subnet-a.png' | relative_url } })

---

## 4. Internet Gateway Configuration

We create and attach an Internet Gateway to allow internet access for public subnets.

![Internet Gateway Menu]({ { '/assets/img/vpc-project/create-gateway-dashboard-and-button-show.png' | relative_url } })

![Create Internet Gateway]({ { '/assets/img/vpc-project/create-demoigw-internet-gateway-show.png' | relative_url } })

![Attach IGW]({ { '/assets/img/vpc-project/showing-vpc-dettached-and-attach-button.png' | relative_url } })

![IGW Attached]({ { '/assets/img/vpc-project/showing-how-we-attach-igw-to-demo-vpc.png' | relative_url } })

---

## 5. Route Tables

We create separate public and private route tables and associate subnets.

![Route Tables Menu]({ { '/assets/img/vpc-project/showing-how-we-access-route-tables-in-dashboard-and-the-default-route-table.png' | relative_url } })

![Edit Public Route Table]({ { '/assets/img/vpc-project/edit-public-route-table-show.png' | relative_url } })

![Adding Public Subnets]({ { '/assets/img/vpc-project/showing-how-im-adding-public-subnets-associations.png' | relative_url } })

![Subnet Associations]({ { '/assets/img/vpc-project/add-subnets-associations-show-public-route-table.png' | relative_url } })

![Associating Private Subnets]({ { '/assets/img/vpc-project/doing-the-same-for-private-subnets.png' | relative_url } })

![Public Route Table Rules]({ { '/assets/img/vpc-project/creating-public-route-rable.png' | relative_url } })

![Private Route Table Rules]({ { '/assets/img/vpc-project/private-route-table-show.png' | relative_url } })

---

## 6. Bastion Host Setup

We configure a Bastion Host to allow 
```bash
SSH access to private instances
```
.

![Rename Bastion Host]({ { '/assets/img/vpc-project/renaming-out-ec2-instance-bastion-host.png' | relative_url } })

![Create Key Pair]({ { '/assets/img/vpc-project/showing-keypair-create.png' | relative_url } })

![Key Pair Settings]({ { '/assets/img/vpc-project/showing-keypair-settings.png' | relative_url } })

![Private EC2 Instance with Key Pair]({ { '/assets/img/vpc-project/creating-private-ec2-instance-showing-keypair-selected.png' | relative_url } })

![Private Instance IPv4 Address]({ { '/assets/img/vpc-project/show-private-instance-ipv4-address.png' | relative_url } })

![Add KeyPair in Bastion Host]({ { '/assets/img/vpc-project/adding-keypair-to-bastion-host-nano-command.png' | relative_url } })

![Paste Key Pair]({ { '/assets/img/vpc-project/paste-contents-of-keypair-in-nano.png' | relative_url } })

![Check Key Pair]({ { '/assets/img/vpc-project/we-cat-or-check-contents-using-the-cat-command-in-our-demokeypair.pem-file.png' | relative_url } })

![Change Key Permissions]({ { '/assets/img/vpc-project/change-permissions-keypair-command.png' | relative_url } })

![SSH Connection Successful]({ { '/assets/img/vpc-project/show-the-authenticity-of-host-cant-be-established-and-ssh-connection-succesful.png' | relative_url } })

---

## 7. NAT Gateway

We create a NAT Gateway for private subnets to access the internet.

![Elastic IP Menu]({ { '/assets/img/vpc-project/show-where-to-go-for-elastic-ip.png' | relative_url } })

![Elastic IP Allocation]({ { '/assets/img/vpc-project/elastic-address-settings-page1.png' | relative_url } })

![Associate Elastic IP]({ { '/assets/img/vpc-project/actions-associate-elastic-ip-address.png' | relative_url } })

![NAT Gateway Menu]({ { '/assets/img/vpc-project/go-to-vpc-nat-gateways.png' | relative_url } })

![Create NAT Gateway]({ { '/assets/img/vpc-project/nat-gateway-creation.png' | relative_url } })

![NAT Gateway Available]({ { '/assets/img/vpc-project/nat-gateway-showing-available.png' | relative_url } })

![Private Route Table NAT Rule]({ { '/assets/img/vpc-project/private-subnet-route-table-add-nat-gateway-rule.png' | relative_url } })

![Private EC2 Internet Access]({ { '/assets/img/vpc-project/now-internet-connection-works-in-our-private-ec2-connected-through-ssh-from-bastion-and-pinged-google.png' | relative_url } })

---

## 8. Network ACLs

We test Network ACLs to enforce subnet-level security.

![Edit Network ACL Rules]({ { '/assets/img/vpc-project/network-acl-show-press-edit-inbound-rules.png' | relative_url } })

![Deny HTTP Rule]({ { '/assets/img/vpc-project/show-deny-http-port-80-rule-nacl-setting.png' | relative_url } })

![Connection Failed via NACL]({ { '/assets/img/vpc-project/connection-failed-now-bastion-host-after-changing-network-acl.png' | relative_url } })

![Rule Number 140 Allow]({ { '/assets/img/vpc-project/showing-inbound-rule-number-140-nacl.png' | relative_url } })

![HTTP Access Restored]({ { '/assets/img/vpc-project/show-hello-world-connection-successful-after-changing-rule-to-140.png' | relative_url } })

![Inbound Rules Order]({ { '/assets/img/vpc-project/show-inbound-rules-order-in-network-acls-dashboard.png' | relative_url } })

![Delete Outbound Rules]({ { '/assets/img/vpc-project/show-how-we-delete-security-group-outbound-rules-traffic-and-remove-all-outbound-traffic.png' | relative_url } })

![Restore Outbound Rules]({ { '/assets/img/vpc-project/showing-how-i-fixed-back-my-security-group-outbound-rules-to-allow-traffic-anywhere.png' | relative_url } })

---

## 9. Bastion Host Web Server Test

We install Apache HTTP server on Bastion Host and test connectivity.

![Install Apache]({ { '/assets/img/vpc-project/sudo-yum-install-y-httpd-show.png' | relative_url } })

![Enable HTTPD]({ { '/assets/img/vpc-project/sudo-systemctl-enable-start-httpd.png' | relative_url } })

![Create index.html]({ { '/assets/img/vpc-project/sudo-su-echo-hello-world-file-create-and-show-with-cat.png' | relative_url } })

![Bastion Host HTTP Rule]({ { '/assets/img/vpc-project/bastion-host-security-group-http-enable-0.0.0.0-show.png' | relative_url } })

![Bastion Public IPv4]({ { '/assets/img/vpc-project/testing-connection-bastion-host-showing-ipv4-address-in-dashboard.png' | relative_url } })

![Successful Hello World]({ { '/assets/img/vpc-project/testing-connection-bastion-host-showing-connection-successful-browser-hello-world.png' | relative_url } })

---

## 10. VPC Peering

We set up VPC peering to connect DemoVPC with DefaultVPC.

![VPC Peering Non-Transitive]({ { '/assets/img/vpc-project/vpc-peering-diagram-show-not-transitive.png' | relative_url } })

![Diagram before Peering]({ { '/assets/img/vpc-project/show-current-state-of-our-diagram-and-how-we-will-add-our-vpc-peering-connection.png' | relative_url } })

![Rename Default VPC Instance]({ { '/assets/img/vpc-project/now-lets-rename-it-default-vpc-instance-show.png' | relative_url } })


```bash
Curl Failed]
```
({ { '/assets/img/vpc-project/show-curl-fail-to-our-bastion-host-private-ipv4-address-from-our-defaultvpc-instance.png' | relative_url } })

![Create Peering Connection]({ { '/assets/img/vpc-project/peering-connections-create-peering-button-show.png' | relative_url } })

![Peering Connection Settings]({ { '/assets/img/vpc-project/show-vpc-pairing-connection-settings.png' | relative_url } })

![Accept Peering Connection]({ { '/assets/img/vpc-project/vpc-connection-has-been-request-press-accept-request-button-show.png' | relative_url } })

![Peering Connection Established]({ { '/assets/img/vpc-project/now-we-can-see-that-vpc-connection-has-been-established-successfully.png' | relative_url } })

![Rename Default Route Table]({ { '/assets/img/vpc-project/show-how-we-rename-defaultvpc-route-table.png' | relative_url } })

![Edit Routes]({ { '/assets/img/vpc-project/vpc-peering-public-route-table-routes-edit-routes-button-show.png' | relative_url } })

![Add Route to Default VPC]({ { '/assets/img/vpc-project/vpc-peering-show-adding-route-to-our-default-vpc-cidr-and-target-is-a-peering-connection-demopeeringconnection.png' | relative_url } })

![Add Route from Default VPC]({ { '/assets/img/vpc-project/vpc-peering-show-adding-route-to-our-demo-vpc-routetable-from-our-defaultvpc.png' | relative_url } })

![Curl Success after Routes]({ { '/assets/img/vpc-project/show-curl-success-to-our-bastion-host-private-ipv4-address-from-our-defaultvpc-instance-after-changing-routes.png' | relative_url } })

---

## 11. VPC Endpoints

We configure a Gateway Endpoint for S3.

![Diagram with Endpoint]({ { '/assets/img/vpc-project/show-current-state-of-our-diagram-2-and-how-we-will-add-our-vpc-endpoint.png' | relative_url } })

![Connect Private Instance]({ { '/assets/img/vpc-project/vpc-endpoint-now-we-connect-from-bastion-to-our-private-ec2-instance.png' | relative_url } })

![Modify IAM Role]({ { '/assets/img/vpc-project/vpc-endpoint-modify-iamrole-button-show.png' | relative_url } })

![Create IAM Role]({ { '/assets/img/vpc-project/vpc-endpoint-modify-iam-role-press-create-iam-role.png' | relative_url } })

![IAM Create Role]({ { '/assets/img/vpc-project/vpc-endpoint-iam-dashboard-create-role-button.png' | relative_url } })

![IAM Select EC2]({ { '/assets/img/vpc-project/vpc-endpoint-iam-role-creation-select-ec2.png' | relative_url } })

![IAM S3 Read Only]({ { '/assets/img/vpc-project/vpc-endpoint-iam-select-permission-amazon-s3-read-only.png' | relative_url } })

![Finalize IAM Role]({ { '/assets/img/vpc-project/vpc-endpoint-iam-final-step-name-the-role-and-create.png' | relative_url } })

![Attach IAM Role]({ { '/assets/img/vpc-project/vpc-endpoint-modify-iam-role-finally-back-refresh-and-select-our-role.png' | relative_url } })

![Remove NATGW Route]({ { '/assets/img/vpc-project/vpc-endpoint-s3-natgw-remove-route-test-show-remove.png' | relative_url } })

![Fail without Endpoint]({ { '/assets/img/vpc-project/vpc-endpoint-s3-natgw-remove-route-test-show-testing-awss3-and-curl.png' | relative_url } })

![Create Endpoint]({ { '/assets/img/vpc-project/vpc-endpoint-show-endpoint-create-button-in-dashboard.png' | relative_url } })

![Select AWS Services]({ { '/assets/img/vpc-project/vpc-endpoint-creating-endpoint-1-aws-services-select.png' | relative_url } })

![Select S3 Gateway]({ { '/assets/img/vpc-project/vpc-endpoint-creating-endpoint-2-s3-gw-select.png' | relative_url } })

![Configure Endpoint Network]({ { '/assets/img/vpc-project/vpc-endpoint-creating-endpoint-3-network-and-routetable-select.png' | relative_url } })

![Endpoint Added to Route Table]({ { '/assets/img/vpc-project/vpc-endpoint-creating-endpoint-4-show-that-our-endpoint-has-been-added-to-route-table.png' | relative_url } })

![Successful S3 Connection]({ { '/assets/img/vpc-project/vpc-endpoint-creating-endpoint-5-test-s3-connectionnow-success.png' | relative_url } })

---

## 12. VPC Flow Logs

We configure VPC Flow Logs and analyze them with Athena.

![Flow Logs Creation]({ { '/assets/img/vpc-project/vpc-flowlogs-creation-page-show.png' | relative_url } })

![Create Flow Logs S3 Bucket]({ { '/assets/img/vpc-project/vpc-flowlogs-create-s3bucket.png' | relative_url } })

![Bucket ARN]({ { '/assets/img/vpc-project/vpc-flowlogs-show-bucket-arn-in-details.png' | relative_url } })

![Copy Bucket ARN]({ { '/assets/img/vpc-project/vpc-flowlogs-copy-bucket-arn-into-flowlog-settings.png' | relative_url } })

![Flow Logs Dashboard]({ { '/assets/img/vpc-project/vpc-flowlogs-show-button-in-dashboard.png' | relative_url } })

![Create CloudWatch Flow Logs]({ { '/assets/img/vpc-project/vpc-flowlogs-create-cloudwatch-logs-page.png' | relative_url } })

![Flow Logs Role Trusted Entity]({ { '/assets/img/vpc-project/vpc-flowlogs-create-role-select-trusted-entity.png' | relative_url } })

![CloudWatch Logs Full Access]({ { '/assets/img/vpc-project/vpc-flowlogs-create-role-add-permissions-cw-logs-full-access.png' | relative_url } })

![Create Log Group]({ { '/assets/img/vpc-project/vpc-flowlogs-create-log-groups-button-show.png' | relative_url } })

![Log Group Settings]({ { '/assets/img/vpc-project/vpc-flowlogs-create-log-groups-show-settings.png' | relative_url } })

![Select Destination Log Group]({ { '/assets/img/vpc-project/vpc-flowlogs-create-destination-log-group-select.png' | relative_url } })

![Select Service Role]({ { '/assets/img/vpc-project/vpc-flowlogs-select-servicerole-show.png' | relative_url } })

![Both Flow Logs Created]({ { '/assets/img/vpc-project/vpc-flowlogs-show-both-created-flowlogs.png' | relative_url } })

![S3 Logs Created]({ { '/assets/img/vpc-project/vpc-flowlogs-show-created-s3-logs.png' | relative_url } })

![Log Stream Bastion]({ { '/assets/img/vpc-project/vpc-flowlogs-show-logstream-bastion.png' | relative_url } })

![Rejected Events Bastion]({ { '/assets/img/vpc-project/vpc-flowlogs-bastion-eni-events-show-rejected.png' | relative_url } })

![Athena Settings]({ { '/assets/img/vpc-project/vpc-flowlogs-athena-show-edit-settings-button.png' | relative_url } })

![Athena Query Result Location]({ { '/assets/img/vpc-project/vpc-flowlogs-athena-queryeditor-manage-settings-show-selecting-query-result-bucket.png' | relative_url } })

![Copy S3 URI]({ { '/assets/img/vpc-project/vpc-flowlogs-copy-s3-uri.png' | relative_url } })

![Paste S3 URI]({ { '/assets/img/vpc-project/vpc-flowlogs-athena-pasting-s3uri-in-location.png' | relative_url } })

![Table Created]({ { '/assets/img/vpc-project/vpc-flowlogs-table-created-after-running.png' | relative_url } })

![Copy Date URI]({ { '/assets/img/vpc-project/vpc-flowlogs-copy-s3-uri-2-date.png' | relative_url } })

![Add Partition]({ { '/assets/img/vpc-project/vpc-flowlogs-copy-into-editor-and-date.png' | relative_url } })

![Athena Query Results]({ { '/assets/img/vpc-project/vpc-flowlogs-athena-runquery-100-results.png' | relative_url } })

---

## 13. VPN and Direct Connect (Overview)

We briefly show how to set up VPN connections and mention Direct Connect.

![Create Virtual Private Gateway]({ { '/assets/img/vpc-project/vpc-virtual-private-gateway-create-button.png' | relative_url } })

![Virtual Private Gateway Settings]({ { '/assets/img/vpc-project/vpc-virtual-private-gateway-settings-page.png' | relative_url } })

![Create Site-to-Site VPN]({ { '/assets/img/vpc-project/vpc-site-2-site-create-button.png' | relative_url } })

![VPN Connection Settings]({ { '/assets/img/vpc-project/vpc-vpn-site2site-connection-settings.png' | relative_url } })

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
