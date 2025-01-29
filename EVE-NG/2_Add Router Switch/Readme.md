## Add Router
#### 1. Dowload & Install FTP Software.
    - https://filezilla-project.org
-  ![ftp](imgs/ftp.png)

#### 2. Use the IP address to connect to the EVE-NG server on port 22.
- ![conn Ftp](imgs/connFtp.png)

#### 3. Download the router and switch files, then unzip them.
- [Download](https://drive.google.com/drive/folders/1-PfBdcoa2BJnFKN_nMBsjHqRNQ2H4avU?usp=sharing)

#### 4. Go to the dynamips directory on the remote site.
- Upload router files.
```bash
    /opt/unetlab/addons/dynamips
```
- Drag and drop the router files.
- ![upload Ftp](imgs/upFtp.png)

#### 5. Go to the iol/bin directory on the remote site.
- Upload switch files.
```bash
    /opt/unetlab/addons/iol/bin
```

#### 6. Use PuTTY to connect to the EVE-NG server via SSH.
- Add an IP address for the connection.
- ![putty](imgs/putty.png)
- Log in and execute commands.
```bash
    /opt/unetlab/wrappers/unl_wrapper -a fixpermissions
```
#### 7. Verify that this was added successfully.
- Open your browser, enter the IP address, and connect to the EVE-NG site. Click 'Add New Node.' If successful, Cisco IOL and IOS will appear highlighted in blue.
- ![check node](imgs/checkNode.png)