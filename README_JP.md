# DO1_LINUX

## Part 1. OSのインストール
OSバージョンの確認:<br>
<b>cat /etc/issue</b><br>

<img src="img/Part_1.png"><br>

## Part 2. ユーザーの作成

タスク: 新しいユーザーを作成し、admグループに追加する。<br>

ユーザー作成コマンド:<br>

sudo adduser newuser<br>
adm/sudoに追加するコマンド:<br>

sudo usermod -aG adm newuser<br>
<img src="img/Part_2.png"><br>

/etc/passwdでの確認:<br>

cat /etc/passwd | grep newuser<br>
<img src="img/Part_2_2.png"><br>


## Part 3. OSネットワーク設定<br>

タスク: ホスト名の変更、タイムゾーンの設定、インターフェースとIPの表示、静的IPの設定。<br>

マシンの名前変更:<br>

sudo hostnamectl set-hostname user-1<br>

タイムゾーンの設定:<br>

sudo timedatectl set-timezone Asia/Samarkand<br>

インターフェースの表示:<br>

ip link show<br>

loインターフェースの説明: これはOS内部通信用のループバックインターフェースです。4. DHCPからのIP取得:<br>

ip addr show<br>

DHCP (Dynamic Host Configuration Protocol) — 動的ホスト構成プロトコル。5. ゲートウェイの外部および内部IP:<br>

ip route | grep default<br>

静的設定（/etc/netplan/00-netplan-config.yaml の例）:<br>

network:<br>
  version: 2<br>
  renderer: networkd<br>
  ethernets:<br>
    enp0s3:<br>
      addresses: [192.168.1.100/24]<br>
      gateway4: 192.168.1.1<br>
      nameservers:<br>
        addresses: [1.1.1.1, 8.8.8.8]<br>

適用:<br>

sudo netplan apply<br>

再起動と確認:<br>

sudo reboot<br>
# 起動後<br>
ip addr show enp0s3<br>
ip route<br>
cat /etc/resolv.conf<br>

リモートホストのping確認:<br>

ping -c 4 1.1.1.1<br>
ping -c 4 ya.ru<br>
<img src="img/ping_ya_ru.png"><br>

出力には「0% packet loss」が含まれている必要があります。<br>
<img src="img/ping_1_1_1_1.png"><br>

## Part 4. OSの更新<br>

タスク: パッケージを最新バージョンに更新する。<br>

sudo apt update && sudo apt upgrade -y<br>
sudo apt update<br>
<img src="img/Part4_1.png"><br>

再度のupdate実行時、更新がないことを示すメッセージが表示される。<br>
<img src="img/Part4_2.png"><br>

## Part 5. sudoコマンドの使用<br>

タスク: newuserにsudoを許可し、そのユーザー権限でホスト名を変更する。<br>

sudoersへの追加:<br>

sudo usermod -aG sudo newuser<br>

newuserでのホスト名変更:<br>

sudo hostnamectl set-hostname new-hostname<br>

コメント: sudoコマンドは、ユーザーアカウントを保持したまま、スーパーユーザー権限で特権操作を実行できるようにします。<br>
<img src="img/Part5.png"><br>

## Part 6. 時刻同期サービスのインストールと設定<br>

タスク: 自動時刻同期を設定する。<br>

sudo apt install -y chrony<br>
sudo systemctl enable chrony --now<br>

確認:<br>

timedatectl show | grep NTPSynchronized<br>
timedatectl<br>

出力にはNTPSynchronized=yesが含まれている必要があります。<br>
<img src="img/Part6.png"><br>

## Part 7. テキストエディタのインストールと使用<br>

タスク: VIM, NANO, JOEをインストールする。<br>

sudo apt install -y vim nano joe<br>

エディタでのファイルの作成と保存:<br>

VIM: vim test_vim.txt → ニックネームの入力 → :wq<br>
<img src="img/Part7_vim.png"><br>

NANO: nano test_nano.txt → ニックネームの入力 → Ctrl+X, Enter<br>
<img src="img/Part7_nano.png"><br>

JOE: joe test_joe.txt → ニックネームの入力 → Ctrl+K, X<br>
<img src="img/Part7_joe.png"><br>


## Part 8. SSHDサービスのインストールと基本設定<br>

タスク: SSHのインストール、自動起動、ポートを2022に再設定。<br>

sudo apt install -y openssh-server - 基本のインストール = openSSH server<br>
sudo systemctl enable ssh --now - sshサービスの有効化<br>
sudo sed -i 's/#Port 22/Port 2022/' /etc/ssh/sshd_config - ポートを2022に変更<br>
sudo systemctl restart ssh - 設定を適用するためのサービスの再起動<br>

プロセスの確認:<br>

ps aux | grep sshd<br>  - grepコマンドはサービスのデータを抽出する

ポートの確認:<br>

sudo netstat -tan | grep 2022<br>

出力には 0.0.0.0:2022 0.0.0.0:* LISTEN が含まれている必要があります。<br>
<img src="img/Part8.png"><br>

## Part 9. top, htopユーティリティの使用<br>

タスク: top, htopをインストールして実行し、指標を記録する。<br>

sudo apt install -y htop<br>
top -n 1 > top_output.txt<br>
htop
<img src="img/Part9_htop.png"><br>

データ: アップタイム: 23分、ユーザー: 1、ロードアベレージ: 0.0、プロセス: 26、CPU: 0.3、MEM: 0.0、pid-max: 637。<br>
<img src="img/Part9_top.png"><br>

フィルタリングされたデータ:<br>
PID, PERCENT_CPU:<br>
<img src="img/Part9_percent_cpu.png"><br>

PERCENT_MEM:<br>
<img src="img/Part9_mem.png"><br>

TIME:<br>
<img src="img/Part9_time.png"><br>

sshdプロセスでフィルタリング:<br>
<img src="img/Part9_sshd.png"><br>

検索で見つかったsyslogプロセス:<br>
<img src="img/Part9_syslog.png"><br>

ホスト名、時計、アップタイムの出力を追加:<br>
<img src="img/Part9_done.png"><br>


## Part 10. fdiskユーティリティの使用<br>

タスク: fdisk -lを実行する。<br>

sudo fdisk -l<br>

レポート内: ディスク名: /dev/sda、ディスクサイズ: 25.00Gb、セクター数: 3、スワップサイズ: 2Gb。<br>
<img src="img/Part10.png"><br>

## Part 11. dfユーティリティの使用<br>

タスク: dfとdf -Thを実行する。<br>

df /<br>
df -Th /<br>
<img src="img/Part11_df.png"><br>

レポート内 (/): サイズ: 11670060、使用中: 0、空き: 11670060、使用率: 0%<br>
df -Th内: ファイルシステム(FS)のタイプにext4を追加する。<br>
<img src="img/Part11_dfTh.png"><br>

## Part 12. duユーティリティの使用<br>

タスク: /home, /var, /var/logのdu、さらに/var/logの各要素に対するdu。<br>

du -b /home /var /var/log<br>
du -h /var/log/*<br>
<img src="img/Part12.png"><br>

/homeのサイズ: 118.8Kb<br>
/varのサイズ: 936.7Kb<br>
/var/logのサイズ: 37.3Kb<br>

## Part 13. ncduユーティリティのインストールと使用<br>

タスク: ncduをインストールし、サイズを出力する。<br>

sudo apt install -y ncdu<br>
ncdu /home /var /var/log<br>
<img src="img/Part13.png"><br>

/homeのサイズ: 148.0 Kb<br>
/varのサイズ: 909Mb<br>
/var/logのサイズ: 42Mb<br>

## Part 14. システムログの操作<br>

タスク: /var/log/dmesg, /var/log/syslog, /var/log/auth.logを表示する。<br>

sudo tail -n 50 /var/log/dmesg<br>
<img src="img/Part14_dmesg.png"><br>
tail -n 50 /var/log/syslog<br>
tail -n 50 /var/log/auth.log<br>
<img src="img/Part14_authlog.png"><br>

レポート内: 最後に成功した認証の時刻、名前、メソッド。<br>

sudo systemctl restart ssh<br>
grep sshd /var/log/syslog | tail -n 10<br>
<img src="img/Part14_main.png"><br>

## Part 15. CRONタスクスケジューラの使用<br>

タスク: crontabを使用して、2分ごとにuptimeを実行する。<br>

(crontab -l 2>/dev/null; echo "*/2 * * * * uptime >> /var/log/cron_uptime.log") | crontab <br>
<img src="img/Part15_main.png"><br>

ログでの確認:<br>
<img src="img/Part15_logs.png"><br>

grep CRON /var/log/syslog | grep uptime<br>
crontab -l<br>

タスクの削除:<br>

crontab -r<br>
crontab -l<br>
<img src="img/Part15_done.png"><br>
