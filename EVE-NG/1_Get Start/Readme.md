## EVE-NG Web Gui
1. Main shows the file manager
- ![main](imgs/main.png)

2. Management shows the User management
    - You can add and delete use on this.
- ![manage](imgs/manage.png)

3. System shows the system status & log
    - You can stop all nodes on this.
- ![system](imgs/system.png)

4. Information shows the information of EVE-NG
- ![info](imgs/info.png)

## Create Node
1. Add Folder
    - Enter the folder name and add it.
- ![add folder](imgs/addFolder.png)

2. Add New Lab
    - Enter the CE-Network and click the 'Add New Lab' icon.
- ![add lab](imgs/addLab.png)
    - Add lab information.
- ![add info](imgs/addInfo.png)
    - Finish Adding the Lab.
- ![finish add lab](imgs/doneLab.png)

3. Add New Object
    - Right-click and choose 'Node'.
- ![add node](imgs/addNode.png)
    - Choose 'Virtual PC'.
- ![choose node](imgs/selectNode.png)
    - Config Node. You can change the node name, icon, and number of nodes.
- ![conf node](imgs/confNode.png)
    - Now you can see two pc on display.
- ![two vPc](imgs/twoVpc.png)
    - Connect two PCs by clicking the orange plug and dropping it on the other PC.
- ![conn node](imgs/connNode.png)
    - If you are successful, the configuration connection page will pop up, and you can choose an interface to connect between VPCs.
- ![conn config](imgs/connConfig.png)
    - You can add text, shapes, and pictures by right-clicking and selecting the icon
- ![add text](imgs/addText.png)
    - Start the VPC by right-clicking and selecting 'Start'.
- ![start node](imgs/startNode.png)
    - Click the VPC icon to remote into the VPC. A pop-up will appear, allowing you to click 'Open SSH'.
- ![open ssh](imgs/openSsh.png)
    - Now, you have remotely connected to the VPC via PuTTY. You can configure the IP address using the following code.
```bash
ip 192.168.1.101 255.255.255.0 192.168.1.1
```
- ![add ip node](imgs/addIpNode.png)
    - Now, you can verify the connectivity between the two VPCs using ping.
```bash
ping 192.168.1.101
```
- ![ping node](imgs/pingNode.png)
    - Once you finish the lab, stop the VPC and close it.
- ![close lab](imgs/closeLab.png)