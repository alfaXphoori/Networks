## 🖧 Add Router & Switch

### 🔹 1. Download & Install FTP Software
- 🔗 [FileZilla](https://filezilla-project.org)
- ![FTP](imgs/ftp.png)

### 🔹 2. Connect to EVE-NG Server via FTP
- Use the **IP address** to connect to the EVE-NG server on **port 22**.
- ![conn Ftp](imgs/connFtp.png)

### 🔹 3. Download & Unzip Router and Switch Files
- 📥 [Download Files](https://drive.google.com/drive/folders/1-PfBdcoa2BJnFKN_nMBsjHqRNQ2H4avU?usp=sharing)

### 🔹 4. Upload Files to the EVE-NG Server
1️⃣ Navigate to the **Dynamips Directory** on the remote site.
```bash
/opt/unetlab/addons/qemu
```
2️⃣ Drag and drop the **router & switch files**.
- ![upload Ftp](imgs/upvIOS.png)

### 🔹 5. Connect to EVE-NG Server via SSH (Using PuTTY)
1️⃣ Open **PuTTY** and enter the EVE-NG **IP address**.
- ![putty](imgs/putty.png)
2️⃣ Log in and execute the following command:
```bash
/opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```
- ![puttyCmd](imgs/puttyCmd.png)

### 🔹 6. Verify Successful Installation
1️⃣ Open your **browser**, enter the **EVE-NG IP address**, and connect to the **EVE-NG site**.
2️⃣ Click **'Add New Node'** and verify that **Cisco IOL and IOS** are highlighted in **blue**.
- ![check node](imgs/checkWeb.png)

✅ **Installation and configuration are complete!** 🚀
