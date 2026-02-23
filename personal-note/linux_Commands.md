```java

1. ✅ Basic Linux checks
2. ✅ Networking checks
3. ✅ Commands needed for Network Automation
4. ✅ Generate SSH Key
5. ✅ Run Ansible ping
---

# 🔹 PART 1 — Basic Linux Commands (Daily Use)
---
## 1️⃣ `pwd`

**What it does:**
Shows your current location (folder).

```bash
pwd
```

**Purpose:**
To know where you are before creating or running files.

**Use Case (Automation):**
Before running `ansible-playbook`, confirm you're inside project folder.

---

## 2️⃣ `ls`

**What it does:**
Shows files in current folder.

```bash
ls
ls -l
ls -a
```

* `-l` → detailed view
* `-a` → show hidden files

**Use Case:**
Check if `inventory.ini` or `playbook.yml` exists.

---

## 3️⃣ `cd`

**What it does:**
Change directory.

```bash
cd foldername
cd ..
cd ~
```

* `..` → one step back
* `~` → home directory

**Use Case:**
Move into your ansible project folder.

---

## 4️⃣ `mkdir`

**What it does:**
Create new folder.

```bash
mkdir ansible-project
```

**Use Case:**
Create folder to store automation files.

---

## 5️⃣ `touch`

**What it does:**
Create empty file.

```bash
touch inventory.ini
touch playbook.yml
```

**Use Case:**
Create Ansible inventory and playbook file.

---

## 6️⃣ `rm`

**What it does:**
Delete file.

```bash
rm file.txt
rm -r foldername
```

⚠ Be careful. No recycle bin.

---

# 🔹 PART 2 — File Viewing (Very Important)

---

## 7️⃣ `cat`

**What it does:**
Show file content.

```bash
cat inventory.ini
```

**Use Case:**
Quickly check inventory content.

---

## 8️⃣ `nano`

**What it does:**
Open file editor.

```bash
nano inventory.ini
```

**Use Case:**
Edit Ansible inventory or playbook.

---

## 9️⃣ `less`

**What it does:**
View large file page by page.

```bash
less /var/log/syslog
```

**Use Case:**
Check logs when automation fails.

---

# 🔹 PART 3 — System & User Checks

---

## 🔟 `whoami`

**What it does:**
Shows current user.

```bash
whoami
```

**Purpose:**
Check if you are root or normal user.

---

## 1️⃣1️⃣ `sudo`

**What it does:**
Run command as administrator.

```bash
sudo apt update
```

**Use Case:**
Install Ansible, install tools.

---

## 1️⃣2️⃣ `chmod`

**What it does:**
Change file permission.

```bash
chmod 600 id_rsa
chmod +x script.sh
```

**VERY IMPORTANT for SSH keys**

If permission wrong → SSH will fail.

---

# 🔹 PART 4 — Networking Commands (Must Know 🚀)

---

## 1️⃣3️⃣ `ip addr`

**What it does:**
Show IP address.

```bash
ip addr
```

**Use Case:**
Check your server IP before SSH.

---

## 1️⃣4️⃣ `ip route`

**What it does:**
Show routing table.

```bash
ip route
```

**Use Case:**
Check default gateway.

---

## 1️⃣5️⃣ `ping`

**What it does:**
Test connectivity.

```bash
ping 8.8.8.8
ping google.com
```

**Use Case:**
Check if device reachable before running automation.

---

## 1️⃣6️⃣ `ss -tulnp`

**What it does:**
Show open ports.

```bash
ss -tulnp
```

**Use Case:**
Check if SSH port 22 is open.

---

## 1️⃣7️⃣ `curl`

**What it does:**
Test HTTP/API.

```bash
curl http://example.com
```

**Use Case:**
Test REST API from automation script.

---

# 🔹 PART 5 — SSH (VERY IMPORTANT FOR ANSIBLE)

---

## 1️⃣8️⃣ `ssh`

**What it does:**
Connect to remote server.

```bash
ssh user@192.168.1.10
```

**Use Case:**
Manual login test before Ansible.

---

# 🔹 PART 6 — Install Ansible

First update system:

```bash
sudo apt update
```

Then install:

```bash
sudo apt install ansible -y
```

Check version:

```bash
ansible --version
```

---

# 🔹 PART 7 — Generate SSH Key (Step-by-Step)

This is required for passwordless login.

---

## Step 1: Generate key

```bash
ssh-keygen -t rsa -b 4096
```

Press Enter 3 times.

It will create:

```
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

---

## Step 2: Copy key to remote server

```bash
ssh-copy-id user@192.168.1.10
```

Enter password once.

Now passwordless login enabled.

Test:

```bash
ssh user@192.168.1.10
```

If no password asked → Success ✅

---

# 🔹 PART 8 — Create Simple Ansible Inventory

Go to your project folder:

```bash
mkdir ansible-project
cd ansible-project
```

Create inventory:

```bash
nano inventory.ini
```

Add this:

```
[servers]
192.168.1.10
```

Save & exit.

---

# 🔹 PART 9 — Run Ansible Ping

Test connectivity:

```bash
ansible all -i inventory.ini -m ping
```

If everything correct, output:

```
192.168.1.10 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

That means:

✅ SSH working
✅ Python installed on remote
✅ Ansible working

---
1️⃣ ip (MOST IMPORTANT)
🔹 What it is

Modern networking command (replaces ifconfig, route).

🔹 Syntax
ip addr
ip route
ip link
🔹 Purpose

Check IP address

Check routing table

Check interface status

🔹 Real Network Use Case
Check IP address
ip addr

Output:

inet 192.168.1.20/24

👉 You verify server IP before SSH.

Check routing table
ip route

Output:

default via 192.168.1.1

👉 Confirm default gateway is correct.

🔥 2️⃣ ping
🔹 What it is

Connectivity test tool.

🔹 Syntax
ping <destination>
ping -c 4 8.8.8.8

-c 4 → send only 4 packets

🔹 Purpose

Check reachability

Check packet loss

Check latency

🔹 Network Use Case

Before running Ansible:

ping 192.168.1.10

If unreachable → automation will fail.

🔥 3️⃣ traceroute
🔹 What it is

Shows path packets take.

🔹 Syntax
traceroute 8.8.8.8
🔹 Purpose

Identify where traffic stops

Troubleshoot routing problems

🔹 Network Use Case

If remote device not reachable:

traceroute 192.168.10.1

You find which hop drops traffic.

🔥 4️⃣ ss (VERY IMPORTANT)
🔹 What it is

Shows open ports and listening services.

🔹 Syntax
ss -tulnp

t → TCP

u → UDP

l → Listening

n → numeric

p → process

🔹 Purpose

Check if service (SSH, HTTP) is running.

🔹 Network Use Case

Check SSH running:

ss -tulnp | grep 22

If no output → SSH service not running.

🔥 5️⃣ netstat (Old but common)
🔹 Syntax
netstat -tulnp

Used same as ss.

🔥 6️⃣ curl (VERY IMPORTANT FOR AUTOMATION)
🔹 What it is

Command-line API tester.

🔹 Syntax
curl http://example.com
curl -I http://example.com

-I → headers only

🔹 Purpose

Test REST API

Check HTTP status code

🔹 Network Automation Use Case

Test network controller API:

curl -u admin:password http://10.10.10.5/api/devices

Before writing automation script.

🔥 7️⃣ ssh
🔹 What it is

Secure remote login.

🔹 Syntax
ssh user@192.168.1.10
ssh -i key.pem user@IP
🔹 Purpose

Access remote device

Manual check before automation

🔹 Network Use Case

Before Ansible:

ssh admin@192.168.1.10

If SSH fails → Ansible will fail.

🔥 8️⃣ ssh-keygen
🔹 What it is

Generate SSH key.

🔹 Syntax
ssh-keygen -t rsa -b 4096
🔹 Purpose

Enable passwordless authentication.

🔹 Network Automation Use Case

Ansible requires SSH key authentication.

🔥 9️⃣ ssh-copy-id
🔹 Syntax
ssh-copy-id user@192.168.1.10
🔹 Purpose

Copy public key to remote server.

🔹 Use Case

Enable automation login without password.

🔥 🔟 tcpdump
🔹 What it is

Packet capture tool.

🔹 Syntax
sudo tcpdump -i eth0
sudo tcpdump port 22
🔹 Purpose

Capture and analyze traffic.

🔹 Network Use Case

Check if SSH packets reaching server:

sudo tcpdump port 22
🔥 1️⃣1️⃣ nmap
🔹 What it is

Port scanning tool.

🔹 Syntax
nmap 192.168.1.10
nmap -p 22 192.168.1.10
🔹 Purpose

Check open ports remotely.

🔹 Use Case

Before automation:

Check if port 22 open.

🔥 1️⃣2️⃣ hostname
🔹 Syntax
hostname
hostname -I
🔹 Purpose

Check device name and IP.

🔥 1️⃣3️⃣ grep (Automation Support Tool)
🔹 Syntax
grep "error" file.log
grep -i ssh file.txt
🔹 Purpose

Search inside logs.

🔹 Use Case

Find automation failure:

grep failed ansible.log
🔥 1️⃣4️⃣ journalctl
🔹 Syntax
journalctl -u ssh
🔹 Purpose

Check service logs.

🔹 Use Case

If SSH not working:

journalctl -u ssh

🔥 1️⃣5️⃣ ansible Command (For Network Automation)

Test connectivity
ansible all -i inventory.ini -m ping

Part	Meaning
ansible	run ansible command
all	target all devices in inventory
-i inventory.ini	use this inventory file
-m ping	run ping module

❗ Important
This is NOT normal ICMP ping.
This is Ansible ping module.
It checks:
SSH working?
Python installed?
Authentication working?

Example Output:
192.168.1.10 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

If you see: SUCCESS

👉 That means automation connection is working.

if UNREACHABLE
Possible reasons:
SSH not working
Wrong username
SSH key not copied
Python not installed


Run playbook
ansible-playbook playbook.yml -i inventory.ini

Ansible will:
read inventory
SSH to device
Execute tasks
Show result

What is playbook.yml?
Example simple playbook:

- name: Check uptime
  hosts: all
  tasks:
    - name: Run uptime command
      command: uptime

This means:
👉 Login to all servers
👉 Run uptime command

🎯 Minimum Commands You MUST Master as Network Engineer

ip addr
ip route
ping
traceroute
ss
ssh
ssh-keygen
curl
tcpdump
ansible

| Topic             | Linux          | Network Device                       |
| ----------------- | -------------- | ------------------------------------ |
| Check IP          | `ip addr`      | `show ip interface brief`            |
| Check route       | `ip route`     | `show ip route`                      |
| Check open ports  | `ss -tulnp`    | `show control-plane host open-ports` |
| View logs         | `journalctl`   | `show logging`                       |
| Enter config mode | Not needed     | `configure terminal`                 |
| File system       | Yes            | Limited                              |
| SSH service       | System service | Built-in                             |

🔥 Big Real-World Difference

Linux:

sudo systemctl restart ssh

Network device:

conf t
no ip ssh
ip ssh version 2
🔥 Why This Matters for Automation

When automating:

Linux → uses SSH + shell commands

Network devices → use SSH + network modules (ios, nxos, junos)

Example:

Linux automation:

command: uptime

Cisco automation:

ios_command:
  commands:
    - show ip route
🎯 Simple Summary

👉 ansible -m ping = connection test
👉 ansible-playbook = run automation script
👉 Linux CLI = server management
👉 Network CLI = router/switch configuration

```
