---

title: Fixing a Broken SSH Service on Ubuntu

date: 2025-09-05 00:00:00 +0200

categories: [Linux, Troubleshooting]

tags: [SSH, Ubuntu, CLI]


author: elis_cazacu

description: "Step-by-step walkthrough of how I diagnosed and fixed a broken SSH service on Ubuntu."

---


## 🛠️ Project Overview



I encountered a scenario where SSH access to a remote Ubuntu server stopped working.  

This project walks through the troubleshooting steps I took to identify and fix the issue.



---
> Screenshot showing the `error` message i got in the CLI.
{: .prompt-info }

![Desktop View](https://cdn.jsdelivr.net/gh/Elis420/jekyll-theme-chirpy@master/assets/img/ssh-project/ssh-conn-fail.png){: width="972" height="589" }
_Screenshot from client CLI_


## 🔎 Step 1: Verify SSH Service Status



First, I checked whether the SSH service was running:

```bash

sudo systemctl status ssh

```
![Desktop View](https://cdn.jsdelivr.net/gh/Elis420/jekyll-theme-chirpy@master/assets/img/ssh-project/ssh-status.png){: width="972" height="589" }
_Screenshot from server's terminal_

> It showed inactive`(dead)`,  meaning the SSH daemon was not running.
{: .prompt-info }

## 🔎 Step 2: Restart SSH Service


I restarted the SSH service:

```bash

sudo systemctl restart ssh

sudo systemctl enable ssh

```

Then I confirmed it was running:

```bash

sudo systemctl status ssh

```

![Desktop View](https://cdn.jsdelivr.net/gh/Elis420/jekyll-theme-chirpy@master/assets/img/ssh-project/enabled-ssh.png){: width="972" height="589" }
_Screenshot from server's terminal_

> Now it showed active `(running)`.
{: .prompt-info }

## 🔎 Step 3: Made sure i had the Forwarding Port rules set up in the VM

![Desktop View](https://cdn.jsdelivr.net/gh/Elis420/jekyll-theme-chirpy@master/assets/img/ssh-project/fp-rules-check.png){: width="972" height="589" }
_Screenshot from VM Network NAT Advanced settings_

> I confirmed i had the host port set up for 2222 and guest to 22.
{: .prompt-info }

## 🔎 Step 4: Check Firewall Rules



To make sure port 22 was open, I ran:

```bash

sudo ufw allow ssh

sudo ufw reload

```

## ✅ Outcome

![Desktop View](https://cdn.jsdelivr.net/gh/Elis420/jekyll-theme-chirpy@master/assets/img/ssh-project/retry-succes.png){: width="972" height="589" }
_Client CLI side_

After completing these steps, SSH access was restored successfully.

I was able to log back in remotely without issues.



## Lessons Learned



Always check whether the SSH service is running before making other changes.


Enabling the service on boot prevents future downtime.


Firewall misconfigurations can be a silent cause of access issues: double-check them.




