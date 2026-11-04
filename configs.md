# SSH Lab - Configuration Notes

## Network Configuration
| Item | Value |
|---|---|
| Network Type | Host-Only Adapter (both VMs) |
| Ubuntu VM (SSH server) | 192.168.56.104 |
| Windows 10 VM (SSH client) | 192.168.56.101 |
| Network Interface (Ubuntu) | enp0s3 |
| SSH Port | 22 |

Purpose of Host-Only: lets the VMs communicate with each other while staying isolated from the internet. Both VMs must be on the same adapter type.

## Finding IP Addresses
```
ip addr          # Ubuntu
ipconfig         # Windows
```

## Connectivity Test (from Windows VM)
```
ping 192.168.56.104
```
Result: 4 successful replies = the VMs can communicate.

## Ubuntu SSH Server Setup
```
sudo apt update
sudo apt install openssh-server
sudo systemctl start ssh
sudo systemctl enable ssh
sudo systemctl status ssh
sudo ufw status
```

| Command | Purpose |
|---|---|
| sudo apt install openssh-server | Lets Ubuntu receive incoming SSH connections. Without it no other machine can SSH in |
| sudo systemctl start ssh | Starts the SSH service right now |
| sudo systemctl enable ssh | Makes SSH start automatically on every boot |
| sudo systemctl status ssh | Checks the service is running (look for active (running)) |
| sudo ufw status | Checks Ubuntu's built-in firewall. If inactive, nothing is blocking port 22. If active, run sudo ufw allow ssh |
### Firewall Check Result
sudo ufw status returned: Status: inactive
Meaning: the firewall was not filtering traffic, so port 22 was not blocked and SSH connections could get through. No ufw rule was needed.


## Windows SSH Client Setup
Windows 10 has an SSH client built in, so no installation is needed.

Verify it is available by typing `ssh` in PowerShell. A list of usage options means it is installed.

## Connecting From Windows to Ubuntu
```
ssh liam@192.168.56.104
```
- First connection shows the host fingerprint. Typed `yes` to accept
- Fingerprint is stored in C:\Users\liam\.ssh\known_hosts "there should be a \ between liam and .ssh but its not showing up"
- On a real network, verify the fingerprint before accepting (protects against man-in-the-middle attacks)

## Remote Commands Tested (running on Ubuntu, typed from Windows)
```
whoami
pwd
uname -a
ip addr
ls -la
exit
```
- whoami confirms which user you are logged in as
- uname -a shows the OS and kernel version of the remote machine
- exit closes the SSH session and returns to the local machine
- The SSH server keeps running after disconnecting, so you can reconnect any time with the same ssh command

## Problems & Solutions
| | |
|---|---|
| Problem | Could not install openssh-server. apt returned "Temporary failure resolving" errors |
| Cause | Host-Only Adapter blocks internet access, which is needed to download packages |
| Fix | Temporarily switched the Ubuntu VM to NAT, installed openssh-server, then switched back to Host-Only for the lab |
