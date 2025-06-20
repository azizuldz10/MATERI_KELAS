# 🏗️ Red Team Lab Environment Setup
## Panduan Lengkap Membangun Lab Penetration Testing

---

## 🎯 Tujuan Lab Environment

Lab environment yang baik harus memiliki:
- **Isolated Network** - Terpisah dari jaringan produksi
- **Vulnerable Targets** - Sistem dengan kerentanan untuk dipraktikkan
- **Attack Platform** - Sistem untuk melakukan serangan
- **Monitoring Tools** - Untuk memantau aktivitas
- **Documentation** - Pencatatan semua aktivitas

---

## 🖥️ Hardware Requirements

### Minimum Requirements
- **CPU**: Intel i5 atau AMD Ryzen 5
- **RAM**: 16GB (32GB recommended)
- **Storage**: 500GB SSD
- **Network**: Gigabit Ethernet

### Recommended Setup
- **CPU**: Intel i7/i9 atau AMD Ryzen 7/9
- **RAM**: 32GB atau lebih
- **Storage**: 1TB NVMe SSD
- **Network**: Dual NIC untuk network isolation

---

## 🔧 Software Requirements

### Hypervisor Options
1. **VMware Workstation Pro** (Recommended)
2. **VirtualBox** (Free alternative)
3. **Hyper-V** (Windows)
4. **KVM/QEMU** (Linux)

### Operating Systems Needed
- **Kali Linux** - Attack platform
- **Windows 10/11** - Target system
- **Windows Server 2019/2022** - Domain controller
- **Ubuntu Server** - Web server target
- **Metasploitable** - Vulnerable Linux
- **DVWA** - Vulnerable web application

---

## 🏗️ Lab Architecture

### Network Topology
```
Internet
    |
[Router/Firewall]
    |
[Management Network] - 192.168.100.0/24
    |
[Attack Network] - 192.168.1.0/24
    |
    +-- Kali Linux (192.168.1.10)
    +-- Windows 10 (192.168.1.20)
    |
[Target Network] - 192.168.2.0/24
    |
    +-- Metasploitable (192.168.2.10)
    +-- DVWA (192.168.2.20)
    +-- Windows Server (192.168.2.30)
    +-- Ubuntu Server (192.168.2.40)
```

---

## 🐧 Kali Linux Setup

### Download dan Instalasi
```bash
# Download Kali Linux
wget https://cdimage.kali.org/kali-2023.4/kali-linux-2023.4-installer-amd64.iso

# Buat VM dengan spesifikasi:
# - RAM: 4GB minimum, 8GB recommended
# - Storage: 80GB
# - Network: NAT + Host-only
```

### Post-Installation Setup
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install additional tools
sudo apt install -y \
    metasploit-framework \
    burpsuite \
    zaproxy \
    sqlmap \
    gobuster \
    enum4linux \
    smbclient \
    crackmapexec \
    bloodhound \
    neo4j \
    responder \
    impacket-scripts

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# Install additional wordlists
sudo apt install -y seclists

# Setup Metasploit database
sudo msfdb init
```

### Kali Linux Customization
```bash
# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Install useful aliases
echo 'alias ll="ls -la"' >> ~/.zshrc
echo 'alias nse="ls /usr/share/nmap/scripts/ | grep"' >> ~/.zshrc
echo 'alias www="python3 -m http.server 8000"' >> ~/.zshrc

# Install VS Code
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

---

## 🪟 Windows Target Setup

### Windows 10 Vulnerable Setup
```powershell
# Disable Windows Defender (for lab only!)
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableIOAVProtection $true

# Enable RDP
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

# Create vulnerable users
net user vulnerable Password123! /add
net localgroup administrators vulnerable /add
net user guest Password123! /add
net user guest /active:yes

# Install vulnerable software
# - Old version of Java
# - Adobe Reader (old version)
# - VNC with weak password
```

### Windows Server Domain Setup
```powershell
# Install AD DS
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promote to Domain Controller
Install-ADDSForest -DomainName "lab.local" -SafeModeAdministratorPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force)

# Create domain users
New-ADUser -Name "John Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@lab.local" -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) -Enabled $true

# Create service accounts
New-ADUser -Name "SQL Service" -SamAccountName "sqlsvc" -UserPrincipalName "sqlsvc@lab.local" -AccountPassword (ConvertTo-SecureString "MyVeryLongPassword123!" -AsPlainText -Force) -Enabled $true

# Set SPNs for Kerberoasting
setspn -A MSSQLSvc/sql.lab.local:1433 sqlsvc
```

---

## 🐧 Linux Target Setup

### Metasploitable 2 Setup
```bash
# Download Metasploitable 2
wget https://sourceforge.net/projects/metasploitable/files/Metasploitable2/metasploitable-linux-2.0.0.zip

# Extract and import to VM
# Default credentials: msfadmin/msfadmin
```

### Ubuntu Server Vulnerable Setup
```bash
# Install Ubuntu Server 20.04
# Configure with weak settings

# Install vulnerable services
sudo apt update
sudo apt install -y \
    vsftpd \
    telnetd \
    rsh-server \
    apache2 \
    mysql-server \
    php \
    samba

# Configure weak FTP
sudo sed -i 's/#write_enable=YES/write_enable=YES/' /etc/vsftpd.conf
sudo sed -i 's/#anonymous_enable=NO/anonymous_enable=YES/' /etc/vsftpd.conf

# Configure weak MySQL
sudo mysql -e "CREATE USER 'root'@'%' IDENTIFIED BY 'password';"
sudo mysql -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';"

# Configure weak Samba
sudo tee -a /etc/samba/smb.conf << EOF
[share]
    path = /tmp
    browsable = yes
    writable = yes
    guest ok = yes
    read only = no
EOF

# Create weak users
sudo useradd -m -s /bin/bash user1
echo 'user1:password' | sudo chpasswd
sudo useradd -m -s /bin/bash admin
echo 'admin:admin' | sudo chpasswd
sudo usermod -aG sudo admin
```

---

## 🌐 Web Application Targets

### DVWA (Damn Vulnerable Web Application)
```bash
# Using Docker
docker run --rm -it -p 80:80 vulnerables/web-dvwa

# Manual installation
git clone https://github.com/digininja/DVWA.git
cd DVWA
# Configure database in config/config.inc.php
# Setup Apache/PHP/MySQL
```

### WebGoat
```bash
# Using Docker
docker run -p 8080:8080 -t webgoat/webgoat-8.0

# Access: http://localhost:8080/WebGoat
```

### Mutillidae II
```bash
# Using Docker
docker run -d -p 80:80 -p 3306:3306 citizenstig/nowasp

# Access: http://localhost/mutillidae
```

### bWAPP
```bash
# Download and setup
wget http://www.itsecgames.com/bWAPP_latest.zip
unzip bWAPP_latest.zip
# Setup LAMP stack and configure
```

---

## 🔧 Network Configuration

### VMware Network Setup
```bash
# Create custom networks
# 1. VMnet1 (Host-only) - Management: 192.168.100.0/24
# 2. VMnet2 (Host-only) - Attack: 192.168.1.0/24  
# 3. VMnet3 (Host-only) - Target: 192.168.2.0/24

# Configure VM network adapters:
# Kali Linux: VMnet1 + VMnet2
# Windows targets: VMnet2 + VMnet3
# Linux targets: VMnet3
```

### pfSense Firewall Setup
```bash
# Download pfSense
wget https://www.pfsense.org/download/

# Configure as router between networks
# WAN: VMnet2 (192.168.1.1)
# LAN: VMnet3 (192.168.2.1)

# Firewall rules:
# Allow all from Attack to Target network
# Block Target to Attack network
# Log all traffic for analysis
```

---

## 📊 Monitoring and Logging

### ELK Stack Setup
```bash
# Using Docker Compose
cat > docker-compose.yml << EOF
version: '3'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.15.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"
  
  kibana:
    image: docker.elastic.co/kibana/kibana:7.15.0
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
  
  logstash:
    image: docker.elastic.co/logstash/logstash:7.15.0
    ports:
      - "5044:5044"
    depends_on:
      - elasticsearch
EOF

docker-compose up -d
```

### Security Onion
```bash
# Download Security Onion
wget https://github.com/Security-Onion-Solutions/securityonion/releases/download/v2.3.100/securityonion-2.3.100.iso

# Install as network monitoring VM
# Configure to monitor all lab traffic
```

---

## 🎯 Lab Scenarios

### Scenario 1: Basic Network Penetration
**Objective**: Compromise Windows 10 machine
**Steps**:
1. Network discovery
2. Port scanning
3. Service enumeration
4. Vulnerability identification
5. Exploitation
6. Post-exploitation

### Scenario 2: Web Application Testing
**Objective**: Exploit DVWA application
**Steps**:
1. Application mapping
2. Vulnerability assessment
3. SQL injection
4. XSS exploitation
5. File upload bypass

### Scenario 3: Active Directory Attack
**Objective**: Domain compromise
**Steps**:
1. Initial foothold
2. Domain enumeration
3. Kerberoasting
4. Lateral movement
5. Domain admin compromise

### Scenario 4: Red Team Exercise
**Objective**: Full network compromise
**Steps**:
1. External reconnaissance
2. Initial access
3. Persistence
4. Privilege escalation
5. Lateral movement
6. Data exfiltration
7. Covering tracks

---

## 📚 Lab Maintenance

### Regular Updates
```bash
# Update Kali Linux
sudo apt update && sudo apt upgrade -y

# Update Metasploit
sudo msfupdate

# Update wordlists
sudo apt update seclists

# Update Docker images
docker pull vulnerables/web-dvwa
docker pull webgoat/webgoat-8.0
```

### Backup Strategy
```bash
# Create VM snapshots before testing
# Export VM configurations
# Backup custom scripts and tools
# Document all configurations
```

### Reset Procedures
```bash
# Revert VMs to clean state
# Clear logs
# Reset passwords
# Restore default configurations
```

---

## 🔒 Security Considerations

### Lab Isolation
- **Network Isolation**: Gunakan isolated networks
- **No Internet Access**: Target VMs tidak boleh akses internet
- **Firewall Rules**: Blokir akses ke jaringan produksi
- **VPN Separation**: Pisahkan lab dari VPN perusahaan

### Legal Considerations
- **Written Permission**: Selalu dapatkan izin tertulis
- **Scope Definition**: Definisikan scope dengan jelas
- **Data Protection**: Lindungi data sensitif
- **Documentation**: Dokumentasikan semua aktivitas

---

*Lab environment ini dirancang untuk pembelajaran dan pengembangan skill penetration testing dalam lingkungan yang aman dan terkontrol.*
