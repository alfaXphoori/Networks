## Add Router
1. Dowload & Install FTP Software.
    - https://filezilla-project.org
-  ![ftp](imgs/ftp.png)

2. Use the IP address to connect to the EVE-NG server on port 22.
- ![conn Ftp](imgs/connFtp.png)

3. Download Router & Switch File
    - [Download](https://drive.google.com/drive/folders/1-PfBdcoa2BJnFKN_nMBsjHqRNQ2H4avU?usp=sharing)

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