Setup-and-Use-a-Firewall-on-Windows-Linux
Windows (Victim)
---------------------------

Basic commands:
ipconfig
Win + R → wf.msc


Block inbound Telnet (Port 23)
------------------------------
1. Open Windows Defender Firewall with Advanced Security
2. Click Inbound Rules
3. Click New Rule
4. Rule type: Port
5. Protocol: TCP
6. Specific local ports: 23
7. Action: Block the connection
8. Profile: Domain, Private, Public
9. Name: Block Telnet Port 23
10. Finish


Allow SSH (Port 22)
-------------------
Only if OpenSSH Server is installed.

1. Inbound Rules → New Rule
2. Port → TCP → 22
3. Action → Allow the connection
4. Profiles → All
5. Name → Allow SSH Port 22


Install OpenSSH Server on Windows
---------------------------------
Settings → Apps → Optional Features → Add a feature → OpenSSH Server


Start SSH Service (PowerShell Admin)
------------------------------------
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic
Get-Service sshd

New-NetFirewallRule -Name "Allow SSH" -Protocol TCP -LocalPort 22 -Action Allow -Direction Inbound


Kali (Attacker)
------------------------------
Test Telnet:
telnet 10.10.10.9

Test SSH:
ssh hi@10.10.10.9


Windows (Disabling SSH)
-----------------------
Stop-Service sshd
Set-Service -Name sshd -StartupType Disabled
Get-Service sshd

Check SSH capability:
Get-WindowsCapability -Online | Where-Object Name -Like "OpenSSH.Server*"

Remove SSH server:
Remove-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0


Parrot OS (Victim)
-----------------------------
Check network and firewall:
ip a
sudo ufw status verbose

Enable ufw:
sudo ufw enable
sudo ufw status verbose
sudo ufw status numbered


Block inbound Telnet (23/tcp):
sudo ufw deny in 23/tcp
sudo ufw status numbered


Allow inbound SSH (22/tcp):
sudo ufw allow in 22/tcp
sudo ufw status


Install and enable SSH server:
sudo apt update
sudo apt install openssh-server -y
sudo systemctl start ssh
sudo systemctl enable ssh
sudo systemctl status ssh


Kali (Attacker)
----------------------------
Check network:
ifconfig
ping 10.10.10.10

Test Telnet (fail expected):
telnet 10.10.10.10 23

Test SSH (success expected):
ssh user@10.10.10.10
ls


Parrot OS (Removing Rules)
--------------------------
View rule numbers:
sudo ufw status numbered

Delete rules:
sudo ufw delete 1
sudo ufw delete 3

Confirm:
sudo ufw status


Summary
---------------------------------
A host-only network was used to isolate attacker and victim systems. 
Windows and Parrot OS firewall rules were configured to block Telnet 
(port 23) and allow SSH (port 22). Kali Linux was used to verify results. 
Telnet connections were blocked as expected, while SSH access worked 
successfully. Secure port control was confirmed on both platforms.
