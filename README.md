# 🐉 Kali Linux Cybersecurity Lab Setup

> A practical cybersecurity lab built with **Kali Linux + Oracle VirtualBox + NAT Network**.

This repository documents the setup of a Kali Linux virtual lab using Oracle VirtualBox. The environment provides an isolated and reproducible platform for learning **cybersecurity, networking, system hardening, and defensive testing** in a controlled environment.

---

## 📌 Lab Overview

The lab consists of a Kali Linux virtual machine connected to a dedicated VirtualBox **NAT Network**.

### ⚙️ Environment

| Component       | Configuration     |
| --------------- | ----------------- |
| 🖥️ Hypervisor   | Oracle VirtualBox |
| 🐉 OS           | Kali Linux 2026.2 |
| 💻 Architecture | x64               |
| 🌐 Network Mode | NAT Network       |
| 🔌 Network Name | `Lab-Network`     |
| 📡 Network      | `10.0.0.0/24`     |
| 🚪 Gateway      | `10.0.0.1`        |
| 📦 DHCP         | Enabled           |
| 🖥️ Kali IP      | `10.0.0.3`        |
| 🧠 Memory       | 2048 MB           |
| ⚙️ CPU          | 2 Cores           |
| 💾 Storage      | ~80 GB            |

> **Note:** The Kali IP shown above was observed during this setup. Since DHCP is enabled, the address may change after a reboot.

---

# 🗺️ Lab Architecture

```text
                         🌐 Internet
                              │
                              ▼
                    ┌──────────────────┐
                    │  Host Machine    │
                    │     Windows      │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   Oracle        │
                    │   VirtualBox    │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   NAT Network   │
                    │   Lab-Network   │
                    │  10.0.0.0/24    │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   🐉 Kali Linux  │
                    │    10.0.0.3      │
                    │                  │
                    │ Gateway:         │
                    │ 10.0.0.1         │
                    └──────────────────┘
```

### Why NAT Network?

The NAT Network provides:

* 🌐 Internet connectivity
* 🔄 Communication between lab VMs
* 🛡️ Isolation from the physical network
* 🧪 A controlled environment for security testing

> **Important:** Use **NAT Network** instead of regular **NAT** when multiple virtual machines need to communicate with each other.

---

# 🛠️ Prerequisites

Before starting the lab, make sure you have:

* [ ] Oracle VirtualBox installed
* [ ] Kali Linux VirtualBox image or ISO
* [ ] At least 4 GB RAM available
* [ ] At least 80 GB free disk space
* [ ] Hardware virtualization enabled in BIOS/UEFI
* [ ] Basic Linux terminal knowledge

---

# 01 · 🌐 NAT Network Setup

The first step is to create a dedicated NAT Network for the cybersecurity lab.

## Create the Network

Open **Oracle VirtualBox Manager** and navigate to:

```text
Network → NAT Networks
```

Create a new NAT Network with the following configuration:

```text
Name:          Lab-Network
IPv4 Prefix:   10.0.0.0/24
DHCP:          Enabled
```

### 📸 NAT Network Configuration

![NAT Network Setup](./setting-up-network%20in%20virtualbox.png)

The expected configuration is:

```text
┌─────────────────────────────────┐
│          Lab-Network            │
├─────────────────────────────────┤
│ Network : 10.0.0.0/24           │
│ Gateway : 10.0.0.1              │
│ DHCP    : Enabled               │
└─────────────────────────────────┘
```

---

# 02 · 🐉 Kali Linux Setup

## Create or Import the VM

Create a new Kali Linux virtual machine or import the official Kali VirtualBox image.

Recommended configuration:

```text
Name:        kali-linux-2026.2-virtualbox-amd64
OS Type:     Debian (64-bit)
Memory:      2048 MB
Processors:  2
Disk:        ~80 GB
```

### 📸 Kali VM Configuration

![Kali VM Configuration](./VirtualBox_kali-linux-2026.2-virtualbox-amd64.png)

![Kali Login Setup](./logins-set-up.png)

---

## 🔌 Configure Adapter 1

Open:

```text
Kali VM
   ↓
Settings
   ↓
Network
   ↓
Adapter 1
```

Configure the adapter as:

```text
Enable Network Adapter:  ✓
Attached to:             NAT Network
Name:                    Lab-Network
```

The final configuration should be:

```text
Adapter 1
    │
    └── NAT Network
            │
            └── Lab-Network
```

Start the Kali VM once the configuration is complete.

---

# 03 · 🔌 Kali Network Configuration

After Kali Linux boots, open a terminal and verify the network configuration.

## Check the Network Interface

Run:

```bash
ip addr
```

or:

```bash
ip -4 addr
```

Look for an Ethernet interface such as:

```text
eth0
```

or:

```text
enp0s3
```

---

## Check the IP Address

Run:

```bash
ip addr
```

Kali should receive an address from:

```text
10.0.0.0/24
```

Example:

```text
10.0.0.3
```

---

## Check the Default Gateway

Run:

```bash
ip route
```

You should see:

```text
default via 10.0.0.1
```

This confirms that Kali is using the VirtualBox NAT Network gateway.

---

## Check DNS

Run:

```bash
cat /etc/resolv.conf
```

A working DNS server should be listed.

The DNS server observed during this setup was:

```text
192.168.1.1
```

> **Note:** DNS values may differ depending on your host network configuration.

---

## 📸 Network Configuration

![Network Settings](./network%20setting.png)

![Wired Network Connection](./setting-wired-network-connection1.png)

---

# 🧪 Network Verification

Verify connectivity in three simple steps.

## 1️⃣ Test the Gateway

```bash
ping -c 4 10.0.0.1
```

Expected:

```text
Gateway connectivity ✓
```

---

## 2️⃣ Test Internet Connectivity

```bash
ping -c 4 8.8.8.8
```

Expected:

```text
Internet connectivity ✓
```

---

## 3️⃣ Test DNS Resolution

```bash
ping -c 4 google.com
```

Expected:

```text
DNS resolution ✓
```

---

## 🔍 Quick Verification

Run the following commands whenever you need to troubleshoot the network:

```bash
ip addr
ip route
cat /etc/resolv.conf
```

Expected configuration:

```text
┌─────────────────────────────────┐
│      Kali Network Settings      │
├─────────────────────────────────┤
│ IP Address : 10.0.0.3           │
│ Network    : 10.0.0.0/24        │
│ Gateway    : 10.0.0.1           │
│ DNS        : 192.168.1.1        │
└─────────────────────────────────┘
```

---

# ✅ Final Checklist

### 🌐 NAT Network

* [ ] Create `Lab-Network`
* [ ] Configure `10.0.0.0/24`
* [ ] Enable DHCP
* [ ] Verify gateway `10.0.0.1`

### 🐉 Kali Linux

* [ ] Install/import Kali Linux
* [ ] Configure Adapter 1
* [ ] Select `NAT Network`
* [ ] Select `Lab-Network`
* [ ] Start Kali successfully

### 🔌 Network

* [ ] Check IP address
* [ ] Check default gateway
* [ ] Check DNS
* [ ] Ping `10.0.0.1`
* [ ] Ping `8.8.8.8`
* [ ] Test `google.com`

---

# 📸 Lab Setup Images

Here are the screenshots used in this setup:

![VirtualBox Kali Setup](./VirtualBox_kali-linux-2026.2-virtualbox-amd64.png)

![Network Configuration](./network%20setting.png)

![Wired Connection Setup](./setting-wired-network-connection1.png)

![Kali Login Setup](./logins-set-up.png)

---

# 🚀 Next Steps

Once the base Kali environment is ready, the lab can be expanded with additional machines and services.

Possible additions include:

* 🎯 Vulnerable Linux machines
* 🪟 Windows test machines
* 🌐 Web application targets
* 🗄️ Database servers
* 🔐 Active Directory environments
* 🧪 CTF environments
* 🔎 Network monitoring tools
* 🛡️ Defensive security tools

---

# ⚠️ Responsible Use

This lab is intended for:

* Education
* Cybersecurity training
* Authorized penetration testing
* Personal lab experimentation

> **Only test systems that you own or have explicit permission to assess.**

---

## 🔐 Happy Learning & Stay Ethical!

**Built for learning. Built for experimentation. Built responsibly. 🐉🔐**
