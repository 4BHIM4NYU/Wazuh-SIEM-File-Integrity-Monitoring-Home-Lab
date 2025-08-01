# ⚙️ Wazuh Home Lab Setup Guide

This guide provides detailed instructions for recreating the Wazuh SIEM & File Integrity Monitoring lab.

---

## 1️⃣ Overview
Wazuh is an open-source security platform offering:
- Log analysis
- File integrity monitoring (FIM)
- Intrusion detection
- Vulnerability detection
- Real-time alerting

This lab demonstrates how to build a Wazuh setup for **learning and experimentation**.

---

## 2️⃣ Lab Architecture
**Components:**
- **Wazuh Manager:** Ubuntu VM (VirtualBox)
- **Wazuh Agent:** Windows VM

**Network:** Bridged adapter so the Ubuntu server and Windows VM are on the same LAN.

---

## 3️⃣ Prerequisites
- VirtualBox installed  
- Ubuntu Server 20.04+ installed in VirtualBox (bridged networking)  
- Internet access on Ubuntu VM  
- Admin access on Windows host  
- Basic Linux knowledge (optional but useful)  

---

## 4️⃣ Installing the Wazuh Manager (Ubuntu)
Run the following commands in the Ubuntu VM.

### 4.1 Add Wazuh GPG Key
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg
```

### 4.2 Download & Run Wazuh Installation Script
```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i
```
- `-a` installs all components (manager, indexer).  
- `-i` runs in interactive mode.

This installs all required services automatically.

---

## 5️⃣ Accessing the Wazuh Dashboard
- Find Ubuntu VM IP:
```bash
ifconfig
```
- Open browser: `https://<ubuntu-vm-ip>`  
- Accept browser’s self-signed certificate warning.  
- Log in with credentials shown after installation.

---

## 6️⃣ Installing the Wazuh Agent (Windows)
1. Download the latest [Wazuh agent for Windows](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html).  
2. Run the MSI installer with default settings.

---

## 7️⃣ Registering the Agent with the Manager

### 7.1 Generate Agent Key on Ubuntu Manager
```bash
sudo /var/ossec/bin/manage_agents
```
- Select **A** to add agent.  
- Name it (e.g., WindowsHost).  
- Leave IP blank unless static needed.  
- After creation, select **E** to extract the key.  
- Copy the key.

### 7.2 Apply Key in Windows Agent
- Open **Wazuh Agent Manager** from Start Menu.  
- Paste key into key field.  
- Add manager’s IP (Ubuntu VM IP).  
- Save and restart agent service.

---

## 8️⃣ File Integrity Monitoring (FIM) on Windows
1. Open `C:\Program Files (x86)\ossec-agent\ossec.conf`.  
2. Add this line inside the `<directories>` block:
```xml
<directories realtime="yes">C:\Users\abc\Test</directories>
```
3. Restart the Wazuh agent service.

---

## 9️⃣ Verify Setup
- Open Wazuh dashboard.  
- Go to **Agents** → Check agent status = Active.  
- In monitored folder, create/modify/delete files.  
- See alerts in dashboard.

---

✅ Your Wazuh Home Lab is now ready!
