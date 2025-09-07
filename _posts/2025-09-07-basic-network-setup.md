---

title: Basic Network Setup Using 2 Linux VMs

date: 2025-09-07 00:00:00 +0200

categories: [Network Setup, Virtual Machines]

tags: [VMs, Networking , Ubuntu, CLI]


author: elis_cazacu

description: "Step-by-step walkthrough of how I set up a basic Network using 2 Ubuntu Virtual Machines"

---
# Networking Basics Lab 🖧

This project demonstrates a basic networking setup using VirtualBox, Ubuntu Desktop, and Ubuntu Server.
It covers VM setup, VirtualBox networking configuration, IP configuration, testing, and troubleshooting.

## Lab Topology 🖼️

![Desktop View](/assets/img/network-project/flowchart.png)

> This is a simple flowchart explaining the Network.
{: .prompt-info }

## 1. Lab Setup ⚙️

### VirtualBox Network Configuration

- Created a Host-Only Adapter in VirtualBox:

  - IPv4: 192.168.56.1

  - Netmask: 255.255.255.0

  - DHCP: Disabled (because we use static IPs)
  
![Desktop View](/assets/img/network-project/vm-network-settings.png)

### Added two VMs:

  - Ubuntu Desktop VM

    - Adapter 1: Host-Only Adapter

  - Ubuntu Server VM

    - Adapter 1: Host-Only Adapter
	
![Desktop View](/assets/img/network-project/vbox-show.png)


## 2. Network Configuration 🖧

### Ubuntu Desktop (192.168.56.10):
```bash
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.56.10/24
      gateway4: 192.168.56.1
      nameservers:
        addresses: 
		- 8.8.8.8
		- 8.8.4.4
```
> Here you can see how we edit the Netplan Config in Desktop using `(nano)`.
{: .prompt-info }

![Desktop View](/assets/img/network-project/edit-netconfg-ubuntu-1.png)
_Screenshot from Netplan config being edited with nano_

### Ubuntu Server (192.168.56.11):
```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.56.11/24
      gateway4: 192.168.56.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```
> Here you can see how we edit the Netplan Config in the server file using `(nano)`.
{: .prompt-info }
![Desktop View](/assets/img/network-project/redid-netconfig-server-dhcp-off.png)

> Here i applied the netplan and it prompted us with `(Warning)`.
{: .prompt-info }
![Desktop View](/assets/img/network-project/permissions-issue-1.png)
_Screenshot from Desktops's CLI_

![Desktop View](/assets/img/network-project/netplan-apply.png)
_Screenshot from server's CLI_

> We fixed it by removing write `(w)` permissions from group and others .
{: .prompt-info }

![Desktop View](/assets/img/network-project/fixed-permissions-1.png)
_Permission Warning Fix_


## Testing ✅

Ping from Desktop to Server:

```bash

ping 192.168.56.11

```

![Desktop View](/assets/img/network-project/finally-pingserver-fromdesktop.png)
_Screenshot from desktop's Terminal_

Ping from Server to Desktop:

```bash

ping 192.168.56.10

```

![Desktop View](/assets/img/network-project/ping-fromserver-to-desktop.png)
_Screenshot from Server_

> Both pings are successful.
{: .prompt-info }

## Troubleshooting 🔧

### PROBLEM 1
> `(Issue)`: No IP address on enp0s3.
{: .prompt-warning }

![Desktop View](/assets/img/network-project/changes-not-applied-through-ip-address-set.png)
_Screenshot from Desktop's Terminal_

> `(Fix)`: Configured static IP in Netplan and applied it with sudo netplan apply.
{: .prompt-tip }
.
![Desktop View](/assets/img/network-project/edit-netconfg-ubuntu-1.png)
_Screenshot from nano_

> `(Result)`: Problem fixed.
{: .prompt-info }
![Desktop View](/assets/img/network-project/verified-ipaddress-it-works.png)
_Screenshot from nano_

### PROBLEM 2
> `(Issue)`: Could ping from server to desktop but not the other way
{: .prompt-warning }

![Desktop View](/assets/img/network-project/server-showaddr-problem.png)

> `(Fix)`: Assigned consistent static IPs to both machines to avoid conflicts, turned dchp off. (previously server had .101).
{: .prompt-tip }

![Desktop View](/assets/img/network-project/server-addr-after-dhcp-off.png)


## Lessons Learned 📚

- How to configure static IPs with Netplan.

- How to troubleshoot connectivity using ping and ip addr show.

- Importance of consistent network settings in VirtualBox.

## Future Work 🔮

- Add DHCP server to the lab.

- Test routing between multiple networks.

- Automate network configuration with Ansible.