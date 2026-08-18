# DO1_LINUX

## Part 1. OS Installation
Checking OS version:<br>
<b>cat /etc/issue</b><br>

<img src="img/Part_1.png"><br>

## Part 2. User Creation

Task: Create a new user, add to adm group.<br>

User creation command:<br>

sudo adduser newuser<br>
Command to add to adm/sudo:<br>

sudo usermod -aG adm newuser<br>
<img src="img/Part_2.png"><br>

Checking in /etc/passwd:<br>

cat /etc/passwd | grep newuser<br>
<img src="img/Part_2_2.png"><br>


## Part 3. OS Network Configuration<br>

Task: Rename host, set time zone, display interfaces and IP, configure static IP.<br>

Renaming the machine:<br>

sudo hostnamectl set-hostname user-1<br>

Setting the time zone:<br>

sudo timedatectl set-timezone Asia/Samarkand<br>

Displaying interfaces:<br>

ip link show<br>

Explanation of the lo interface: this is a loopback interface for internal OS communications.4. Obtaining IP from DHCP:<br>

ip addr show<br>

DHCP (Dynamic Host Configuration Protocol) — dynamic host configuration protocol.5. External and internal gateway IP:<br>

ip route | grep default<br>

Static configuration (example for /etc/netplan/00-netplan-config.yaml):<br>

network:<br>
  version: 2<br>
  renderer: networkd<br>
  ethernets:<br>
    enp0s3:<br>
      addresses: [192.168.1.100/24]<br>
      gateway4: 192.168.1.1<br>
      nameservers:<br>
        addresses: [1.1.1.1, 8.8.8.8]<br>

Application:<br>

sudo netplan apply<br>

Reboot and verification:<br>

sudo reboot<br>
# After boot<br>
ip addr show enp0s3<br>
ip route<br>
cat /etc/resolv.conf<br>

Checking ping of remote hosts:<br>

ping -c 4 1.1.1.1<br>
ping -c 4 ya.ru<br>
<img src="img/ping_ya_ru.png"><br>

The output must contain "0% packet loss".<br>
<img src="img/ping_1_1_1_1.png"><br>

## Part 4. OS Update<br>

Task: Update packages to the latest versions.<br>

sudo apt update && sudo apt upgrade -y<br>
sudo apt update<br>
<img src="img/Part4_1.png"><br>

On a repeated update, a message indicating no updates are available.<br>
<img src="img/Part4_2.png"><br>

## Part 5. Using the sudo command<br>

Task: Allow sudo for newuser and change the hostname on their behalf.<br>

Adding to sudoers:<br>

sudo usermod -aG sudo newuser<br>

Changing hostname as newuser:<br>

sudo hostnamectl set-hostname new-hostname<br>

Comment: The sudo command allows you to perform privileged operations on behalf of the superuser, while preserving the user's account.<br>
<img src="img/Part5.png"><br>

## Part 6. Installation and configuration of the time service<br>

Task: Configure automatic time synchronization.<br>

sudo apt install -y chrony<br>
sudo systemctl enable chrony --now<br>

Verification:<br>

timedatectl show | grep NTPSynchronized<br>
timedatectl<br>

The output must contain NTPSynchronized=yes.<br>
<img src="img/Part6.png"><br>

## Part 7. Installation and use of text editors<br>

Task: Install VIM, NANO, JOE.<br>

sudo apt install -y vim nano joe<br>

Creating files and saving them in the editors:<br>

VIM: vim test_vim.txt → enter nickname → :wq<br>
<img src="img/Part7_vim.png"><br>

NANO: nano test_nano.txt → enter nickname → Ctrl+X, Enter<br>
<img src="img/Part7_nano.png"><br>

JOE: joe test_joe.txt → enter nickname → Ctrl+K, X<br>
<img src="img/Part7_joe.png"><br>


## Part 8. Installation and basic configuration of the SSHD service<br>

Task: Install SSH, auto-start, reconfigure port to 2022.<br>

sudo apt install -y openssh-server - installing the base = openSSH server<br>
sudo systemctl enable ssh --now - enabling the ssh service<br>
sudo sed -i 's/#Port 22/Port 2022/' /etc/ssh/sshd_config - changing port to 2022<br>
sudo systemctl restart ssh - restarting the service to apply settings<br>

Checking the process:<br>

ps aux | grep sshd<br>  - grep command extracts service data

Checking the port:<br>

sudo netstat -tan | grep 2022<br>

The output must contain 0.0.0.0:2022 0.0.0.0:* LISTEN.<br>
<img src="img/Part8.png"><br>

## Part 9. Using top, htop utilities<br>

Task: Install and run top, htop; take metrics.<br>

sudo apt install -y htop<br>
top -n 1 > top_output.txt<br>
htop
<img src="img/Part9_htop.png"><br>

Data: uptime: 23min, users: 1, load average: 0.0, processes: 26, CPU: 0.3, MEM: 0.0, pid-max: 637.<br>
<img src="img/Part9_top.png"><br>

Filtered data:<br>
PID, PERCENT_CPU:<br>
<img src="img/Part9_percent_cpu.png"><br>

PERCENT_MEM:<br>
<img src="img/Part9_mem.png"><br>

TIME:<br>
<img src="img/Part9_time.png"><br>

Filtered for the sshd process:<br>
<img src="img/Part9_sshd.png"><br>

With the syslog process found using search:<br>
<img src="img/Part9_syslog.png"><br>

With added output of hostname, clock, and uptime:<br>
<img src="img/Part9_done.png"><br>


## Part 10. Using the fdisk utility<br>

Task: Run fdisk -l.<br>

sudo fdisk -l<br>

In the report: Disk name: /dev/sda, disk size: 25.00Gb, number of sectors: 3, swap size: 2Gb.<br>
<img src="img/Part10.png"><br>

## Part 11. Using the df utility<br>

Task: Run df and df -Th.<br>

df /<br>
df -Th /<br>
<img src="img/Part11_df.png"><br>

In the report (/): size: 11670060, used: 0, available: 11670060, %use: 0%<br>
In df -Th: add FS type: ext4.<br>
<img src="img/Part11_dfTh.png"><br>

## Part 12. Using the du utility<br>

Task: du for /home, /var, /var/log, and also du for each element in /var/log.<br>

du -b /home /var /var/log<br>
du -h /var/log/*<br>
<img src="img/Part12.png"><br>

Size of /home: 118.8Kb<br>
Size of /var: 936.7Kb<br>
Size of /var/log: 37.3Kb<br>

## Part 13. Installation and use of the ncdu utility<br>

Task: Install ncdu, output sizes.<br>

sudo apt install -y ncdu<br>
ncdu /home /var /var/log<br>
<img src="img/Part13.png"><br>

Size of /home: 148.0 Kb<br>
Size of /var: 909Mb<br>
Size of /var/log: 42Mb<br>

## Part 14. Working with system logs<br>

Task: View /var/log/dmesg, /var/log/syslog, /var/log/auth.log.<br>

sudo tail -n 50 /var/log/dmesg<br>
<img src="img/Part14_dmesg.png"><br>
tail -n 50 /var/log/syslog<br>
tail -n 50 /var/log/auth.log<br>
<img src="img/Part14_authlog.png"><br>

In the report: time of the last successful authorization, name, method.<br>

sudo systemctl restart ssh<br>
grep sshd /var/log/syslog | tail -n 10<br>
<img src="img/Part14_main.png"><br>

## Part 15. Using the CRON task scheduler<br>

Task: Run uptime every 2 minutes via crontab.<br>

(crontab -l 2>/dev/null; echo "*/2 * * * * uptime >> /var/log/cron_uptime.log") | crontab <br>
<img src="img/Part15_main.png"><br>

Verification in logs:<br>
<img src="img/Part15_logs.png"><br>

grep CRON /var/log/syslog | grep uptime<br>
crontab -l<br>

Deleting tasks:<br>

crontab -r<br>
crontab -l<br>
<img src="img/Part15_done.png"><br>
