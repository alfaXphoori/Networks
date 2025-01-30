## 🌐 EVE-NG Web GUI

### 🔹 1. Main - File Manager
- Displays the **file manager** for managing labs and configurations.
- ![main](imgs/main.png)

### 🔹 2. Management - User Management
- Add and delete users in **User Management**.
- ![manage](imgs/manage.png)

### 🔹 3. System - Status & Logs
- View **system status** and logs.
- Stop all running nodes from this panel.
- ![system](imgs/system.png)

### 🔹 4. Information - EVE-NG Details
- Displays **EVE-NG system information**.
- ![info](imgs/info.png)

---

## 🛠 Create Node in EVE-NG

### 🔹 1. Add Folder
- Enter the **folder name** and add it.
- ![add folder](imgs/addFolder.png)

### 🔹 2. Add New Lab
1️⃣ Enter **CE-Network** and click **'Add New Lab'**.
- ![add lab](imgs/addLab.png)
2️⃣ Fill in the **lab information**.
- ![add info](imgs/addInfo.png)
3️⃣ Finish **adding the lab**.
- ![finish add lab](imgs/doneLab.png)

### 🔹 3. Add New Object (Node)
1️⃣ Right-click and select **'Node'**.
- ![add node](imgs/addNode.png)
2️⃣ Choose **'Virtual PC'**.
- ![choose node](imgs/selectNode.png)
3️⃣ Configure the node (change name, icon, and number of nodes).
- ![conf node](imgs/confNode.png)
4️⃣ Two Virtual PCs should now be displayed.
- ![two vPc](imgs/twoVpc.png)
5️⃣ Connect **two PCs** by clicking the **orange plug** and dropping it on the other PC.
- ![conn node](imgs/connNode.png)
6️⃣ A **configuration connection page** will pop up. Choose an interface to connect between VPCs.
- ![conn config](imgs/connConfig.png)

### 🎨 Customization
- Add **text, shapes, and pictures** by right-clicking and selecting the corresponding icon.
- ![add text](imgs/addText.png)

### 🔹 4. Start & Connect to Virtual PCs
1️⃣ Start the VPC by **right-clicking and selecting 'Start'**.
- ![start node](imgs/startNode.png)
2️⃣ Click the **VPC icon** to open a **remote session**.
- A **pop-up** will appear, allowing you to click **'Open SSH'**.
- ![open ssh](imgs/openSsh.png)

### 🔹 5. Configure VPC IP Address
```bash
ip 192.168.1.101 255.255.255.0 192.168.1.1
```
- ![add ip node](imgs/addIpNode.png)

### 🔹 6. Verify Connectivity
- **Ping the other VPC** to test the connection.
```bash
ping 192.168.1.101
```
- ![ping node](imgs/pingNode.png)

### 🔹 7. Stop and Close the Lab
- Once you finish the lab, **stop the VPC and close it**.
- ![close lab](imgs/closeLab.png)

✅ **Lab setup and verification completed successfully!** 🚀
