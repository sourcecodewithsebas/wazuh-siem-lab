# 🛡️ Wazuh Home Lab – SIEM & File Integrity Monitoring  

## 📖 Overview  
Wazuh is a free, open-source security platform that provides:  
- Log analysis  
- File integrity monitoring (FIM)  
- Intrusion detection  
- Vulnerability detection  
- Real-time alerting  

This project demonstrates how I built a **basic Wazuh home lab** for learning and experimentation.   

---

## 🏗️ Lab Architecture  

| Component      | Host             | Role                                                                 |
|----------------|-----------------|----------------------------------------------------------------------|
| **Wazuh Manager** | Ubuntu (VirtualBox) | Collects, analyzes, and stores data from agents |
| **Wazuh Agent**   | Windows (Host machine) | Sends logs and system events to the Wazuh manager |

**Network Configuration:**  
- Use **Bridged Adapter** in VirtualBox so the Ubuntu server is on the same network as the host.  
- This ensures communication between host and guest.  

---

## ✅ Prerequisites  
- VirtualBox installed  
- Ubuntu Server 20.04+ (bridged networking)  
- Internet access on the Ubuntu VM  
- Administrative access on Windows host  
- *(Optional)* Basic Linux and system administration knowledge  

---

## ⚙️ Step 1: Installing the Wazuh Manager (Ubuntu VM)  

1. **Add Wazuh GPG Key**  
   ```bash
   curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | \
   sudo gpg --dearmor -o /usr/share/keyrings/wazuh-archive-keyring.gpg

2. **Download & Run Installation Script**

   ```bash
   curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh && \
   sudo bash ./wazuh-install.sh -a -i
   ```

   * `-a` → Installs all components (manager, indexer, etc.)
   * `-i` → Interactive mode

After installation, verify services are running.

---

## 🌐 Step 2: Access the Wazuh Dashboard

1. Get Ubuntu VM IP:

   ```bash
   ifconfig
   ```
2. Open browser: `https://<ubuntu-vm-ip>`
3. Accept self-signed certificate warning.
4. Log in using credentials shown at the end of installation.

---

## 💻 Step 3: Installing the Wazuh Agent (Windows Host)

1. Download the [Wazuh Agent MSI](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-windows.html).
2. Install with default settings.

---

## 🔑 Step 4: Registering the Agent

### 4.1 Generate Agent Key (Ubuntu Manager)

```bash
sudo /var/ossec/bin/manage_agents
```

* Select **A** → Add agent
* Enter name (e.g., *WindowsHost*)
* Extract key with **E** → Copy key

### 4.2 Apply Key (Windows Agent)

* Open **Wazuh Agent Manager** GUI
* Paste agent key + manager IP
* Save & restart agent service

You should now see the Windows agent onboarded in the Wazuh dashboard.

---

## 📂 Step 5: File Integrity Monitoring (FIM) on Windows

1. Edit configuration file:
   `C:\Program Files (x86)\ossec-agent\ossec.conf`

2. Add directory monitoring:

   ```xml
   <directories realtime="yes">C:\Users\abc\Test</directories>
   ```

3. Restart the Wazuh Agent service.

---

## 🔍 Step 6: Verifying Setup

1. In the Wazuh Dashboard:

   * Check **Agents → Status = Active**
   * Go to **Integrity Monitoring**

2. Test by creating/modifying/deleting files in the monitored folder.

3. Confirm alerts appear in dashboard.

---

## 📝 Reflections & Learnings

Through this lab I learned:

* How Wazuh centralizes and analyzes logs from endpoints
* How File Integrity Monitoring works in real-time
* The process of onboarding agents securely

