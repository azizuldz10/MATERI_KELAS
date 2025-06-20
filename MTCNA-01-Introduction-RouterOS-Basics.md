# 🎯 MTCNA - Introduction & RouterOS Basics

## 📚 Daftar Isi
- [Pengenalan MTCNA](#pengenalan-mtcna)
- [Sejarah dan Filosofi MikroTik](#sejarah-dan-filosofi-mikrotik)
- [RouterOS Overview](#routeros-overview)
- [Hardware MikroTik](#hardware-mikrotik)
- [Instalasi dan Setup Awal](#instalasi-dan-setup-awal)
- [Interface Management](#interface-management)
- [Basic Configuration](#basic-configuration)
- [Lab Exercise 1](#lab-exercise-1)

---

## 🎓 Pengenalan MTCNA

### Apa itu MTCNA?
**MTCNA (MikroTik Certified Network Associate)** adalah sertifikasi entry-level dari MikroTik yang memvalidasi kemampuan dasar dalam:
- Konfigurasi dan manajemen RouterOS
- Implementasi routing dan switching dasar
- Setup wireless networking
- Konfigurasi firewall dan security
- Troubleshooting network issues

### Mengapa MTCNA Penting?
- **Industry Recognition**: Diakui secara global sebagai standar kompetensi networking
- **Career Advancement**: Membuka peluang karir sebagai Network Engineer
- **Practical Skills**: Fokus pada implementasi real-world scenarios
- **Cost-Effective**: Solusi networking yang ekonomis untuk berbagai skala bisnis

### Struktur Sertifikasi MikroTik
```
MTCNA (Associate) → MTCRE (Routing Engineer) → MTCWE (Wireless Engineer)
                  → MTCTCE (Traffic Control Engineer) → MTCINE (Inter-networking Engineer)
                                                     → MTCSE (Security Engineer)
```

---

## 🏢 Sejarah dan Filosofi MikroTik

### Sejarah Singkat
- **1996**: Didirikan di Latvia oleh John Trully dan Arnis Riekstins
- **1997**: RouterOS versi pertama diluncurkan
- **2002**: Mulai memproduksi hardware RouterBOARD
- **2010**: Ekspansi global dengan training center worldwide
- **Sekarang**: Leader dalam solusi networking cost-effective

### Filosofi MikroTik
- **Simplicity**: Interface yang user-friendly
- **Flexibility**: Satu platform untuk berbagai kebutuhan
- **Affordability**: Solusi berkualitas dengan harga terjangkau
- **Innovation**: Terus mengembangkan fitur-fitur baru

---

## 🖥️ RouterOS Overview

### Apa itu RouterOS?
RouterOS adalah sistem operasi berbasis Linux yang dikembangkan khusus untuk networking. Fitur utama:

#### Core Features
- **Routing**: Static, RIP, OSPF, BGP
- **Switching**: VLAN, STP, Link Aggregation
- **Wireless**: 802.11a/b/g/n/ac/ax support
- **Security**: Firewall, VPN, User Management
- **Quality of Service**: Traffic shaping, queuing
- **Monitoring**: SNMP, logging, graphing

#### Management Interfaces
1. **WinBox**: GUI management tool (Windows/Wine)
2. **WebFig**: Web-based interface
3. **CLI**: Command Line Interface
4. **SSH/Telnet**: Remote command line access
5. **API**: Programming interface
6. **Mobile App**: Basic monitoring dan configuration

### RouterOS Architecture
```
┌─────────────────────────────────────┐
│           Applications              │
├─────────────────────────────────────┤
│         RouterOS Services           │
├─────────────────────────────────────┤
│           Linux Kernel              │
├─────────────────────────────────────┤
│            Hardware                 │
└─────────────────────────────────────┘
```

---

## 🔧 Hardware MikroTik

### Kategori Hardware

#### 1. RouterBOARD Series
- **hEX Series**: Entry-level home/SOHO routers
- **CCR Series**: Carrier-class routers untuk ISP
- **CRS Series**: Cloud Router Switch
- **RB Series**: Various form factors dan capabilities

#### 2. Wireless Products
- **wAP Series**: Wireless Access Points
- **SXT Series**: Point-to-point wireless links
- **BaseBox Series**: Outdoor wireless solutions

#### 3. Switch Products
- **CRS Series**: Managed switches dengan RouterOS
- **CSS Series**: Pure switching solutions

### Pemilihan Hardware
| Use Case | Recommended Series | Specifications |
|----------|-------------------|----------------|
| Home/SOHO | hEX, hAP | 5-10 ports, basic routing |
| Small Business | RB750, CRS | 10-24 ports, VLAN support |
| Enterprise | CCR, CRS3xx | High throughput, advanced features |
| ISP/Carrier | CCR1xxx, CCR2xxx | Carrier-grade performance |

---

## 🚀 Instalasi dan Setup Awal

### Metode Instalasi RouterOS

#### 1. Pre-installed (RouterBOARD)
- RouterOS sudah terinstall
- Tinggal konfigurasi initial setup
- License sudah included

#### 2. Installation pada PC/Server
```bash
# Download RouterOS ISO
# Burn ke CD/USB
# Boot dari installation media
# Follow installation wizard
```

#### 3. Virtual Machine Installation
- **VMware**: Supported dengan network adapters
- **VirtualBox**: Basic functionality
- **Hyper-V**: Limited support
- **EVE-NG/GNS3**: Lab simulation

### First Time Setup

#### 1. Physical Connection
```
PC ──── Ethernet Cable ──── MikroTik Router
     (192.168.88.0/24)      (192.168.88.1)
```

#### 2. Default Configuration
- **IP Address**: 192.168.88.1/24
- **Username**: admin
- **Password**: (blank)
- **DHCP**: Enabled pada ether1

#### 3. Initial Access Methods
```bash
# Via WinBox
- Download WinBox dari mikrotik.com
- Connect via MAC address atau IP
- Login dengan admin/(blank)

# Via Web Browser
- Browse ke http://192.168.88.1
- Login dengan admin/(blank)

# Via SSH/Telnet
ssh admin@192.168.88.1
telnet 192.168.88.1
```

---

## 🔌 Interface Management

### Jenis-jenis Interface

#### 1. Physical Interfaces
- **Ethernet**: ether1, ether2, dst
- **Wireless**: wlan1, wlan2, dst
- **SFP**: sfp1, sfp-sfpplus1, dst

#### 2. Virtual Interfaces
- **VLAN**: vlan-id
- **Bridge**: bridge1, bridge2, dst
- **Bonding**: bond1, bond2, dst
- **Tunnel**: Various VPN interfaces

### Interface Configuration

#### Basic Interface Settings
```bash
# Melihat semua interface
/interface print

# Enable/Disable interface
/interface disable ether1
/interface enable ether1

# Set interface comment
/interface set ether1 comment="WAN Connection"

# Set MTU
/interface set ether1 mtu=1500
```

#### Interface Monitoring
```bash
# Monitor traffic
/interface monitor-traffic ether1

# Interface statistics
/interface print stats

# Interface status
/interface print status
```

---

## ⚙️ Basic Configuration

### IP Address Configuration

#### Static IP Assignment
```bash
# Add IP address
/ip address add address=192.168.1.1/24 interface=ether1

# Remove IP address
/ip address remove [find address="192.168.1.1/24"]

# View IP addresses
/ip address print
```

#### DHCP Client Configuration
```bash
# Enable DHCP client
/ip dhcp-client add interface=ether1 disabled=no

# View DHCP client status
/ip dhcp-client print
```

### Default Gateway Configuration
```bash
# Add default route
/ip route add dst-address=0.0.0.0/0 gateway=192.168.1.1

# View routing table
/ip route print
```

### DNS Configuration
```bash
# Set DNS servers
/ip dns set servers=8.8.8.8,8.8.4.4

# Enable DNS cache
/ip dns set allow-remote-requests=yes

# View DNS settings
/ip dns print
```

### Basic Security Configuration

#### Change Default Password
```bash
# Set admin password
/user set admin password=NewSecurePassword123!
```

#### Create New User
```bash
# Add new user
/user add name=netadmin password=SecurePass123! group=full

# View users
/user print
```

#### Basic Firewall Rules
```bash
# Allow established connections
/ip firewall filter add chain=input connection-state=established,related action=accept

# Allow local network
/ip firewall filter add chain=input src-address=192.168.88.0/24 action=accept

# Drop everything else
/ip firewall filter add chain=input action=drop
```

---

## 🧪 Lab Exercise 1: Basic RouterOS Setup

### Objective
Melakukan setup dasar RouterOS dan konfigurasi basic networking.

### Prerequisites
- MikroTik router atau RouterOS VM
- PC dengan WinBox installed
- Ethernet cable

### Lab Topology
```
Internet ──── [ISP Router] ──── [MikroTik Router] ──── [PC Client]
                              192.168.1.1/24      192.168.1.100/24
```

### Step-by-Step Instructions

#### Step 1: Initial Connection
1. Connect PC ke MikroTik router
2. Open WinBox
3. Scan for devices atau connect via 192.168.88.1
4. Login dengan admin/(blank)

#### Step 2: Basic Configuration
```bash
# Reset to default configuration
/system reset-configuration no-defaults=yes

# Set system identity
/system identity set name="MTCNA-Lab-Router"

# Configure WAN interface (ether1)
/ip address add address=192.168.1.1/24 interface=ether1

# Configure LAN interface (ether2)
/ip address add address=192.168.100.1/24 interface=ether2

# Set default route
/ip route add dst-address=0.0.0.0/0 gateway=192.168.1.254

# Configure DNS
/ip dns set servers=8.8.8.8,1.1.1.1 allow-remote-requests=yes
```

#### Step 3: DHCP Server Setup
```bash
# Create DHCP pool
/ip pool add name=lan-pool ranges=192.168.100.10-192.168.100.100

# Configure DHCP server
/ip dhcp-server add name=lan-dhcp interface=ether2 address-pool=lan-pool disabled=no

# Set DHCP network
/ip dhcp-server network add address=192.168.100.0/24 gateway=192.168.100.1 dns-server=8.8.8.8,1.1.1.1
```

#### Step 4: Basic Security
```bash
# Change admin password
/user set admin password=MTCNA2024!

# Basic firewall rules
/ip firewall filter add chain=input connection-state=established,related action=accept
/ip firewall filter add chain=input src-address=192.168.100.0/24 action=accept
/ip firewall filter add chain=input protocol=icmp action=accept
/ip firewall filter add chain=input action=drop

# NAT rule for internet access
/ip firewall nat add chain=srcnat out-interface=ether1 action=masquerade
```

#### Step 5: Verification
```bash
# Test connectivity
/ping 8.8.8.8
/ping google.com

# Check interface status
/interface print

# Check IP addresses
/ip address print

# Check routes
/ip route print

# Check DHCP leases
/ip dhcp-server lease print
```

### Expected Results
- Router dapat ping ke internet
- DHCP server memberikan IP ke client
- Client dapat akses internet melalui NAT
- Basic security rules aktif

### Troubleshooting Tips
1. **No Internet Access**: Check default route dan DNS settings
2. **DHCP Not Working**: Verify DHCP server configuration dan network settings
3. **Can't Connect**: Check physical connections dan IP addressing
4. **Firewall Issues**: Review firewall rules order dan logic

---

## 📝 Summary

Dalam modul ini, kita telah mempelajari:

✅ **Pengenalan MTCNA** dan struktur sertifikasi MikroTik
✅ **Sejarah dan filosofi** MikroTik sebagai vendor networking
✅ **RouterOS overview** dan arsitektur sistem
✅ **Hardware MikroTik** dan pemilihan yang tepat
✅ **Instalasi dan setup awal** RouterOS
✅ **Interface management** dan konfigurasi dasar
✅ **Basic configuration** untuk networking
✅ **Lab exercise** untuk praktik hands-on

### Next Steps
Pada modul selanjutnya, kita akan mempelajari:
- Routing protocols (Static, RIP, OSPF)
- Switching dan VLAN configuration
- Network topology dan design principles
- Advanced interface management

### Key Takeaways
- RouterOS adalah platform yang powerful dan flexible
- Basic configuration meliputi IP addressing, routing, dan security
- Hands-on practice sangat penting untuk menguasai MikroTik
- Documentation dan troubleshooting skills adalah essential

---

## 🔗 References
- [MikroTik Official Documentation](https://help.mikrotik.com/)
- [RouterOS Manual](https://help.mikrotik.com/docs/display/ROS/RouterOS)
- [MikroTik Wiki](https://wiki.mikrotik.com/)
- [MTCNA Certification Guide](https://mikrotik.com/training/certificates)
