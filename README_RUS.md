# DO1_LINUX

## Part 1. Установка ОС
Проверка версии ОС:<br>
<b>cat /etc/issue</b><br>

<img src="img/Part_1.png"><br>

## Part 2. Создание пользователя

Задание: Создать нового пользователя, добавить в группу adm.<br>

Команда создания пользователя:<br>

sudo adduser newuser<br>
Команда для добавления в adm/sudo:<br>

sudo usermod -aG adm newuser<br>
<img src="img/Part_2.png"><br>

Проверка в /etc/passwd:<br>

cat /etc/passwd | grep newuser<br>
<img src="img/Part_2_2.png"><br>


## Part 3. Настройка сети ОС<br>

Задание: Переименовать хост, задать временную зону, вывести интерфейсы и IP, настроить статический IP.<br>

Переименование машины:<br>

sudo hostnamectl set-hostname user-1<br>

Установка временной зоны:<br>

sudo timedatectl set-timezone Asia/Samarkand<br>

Вывод интерфейсов:<br>

ip link show<br>

Объяснение интерфейса lo: это loopback-интерфейс для внутренних коммуникаций ОС.4. Получение IP от DHCP:<br>

ip addr show<br>

DHCP (Dynamic Host Configuration Protocol) — протокол динамической конфигурации хоста.5. Внешний и внутренний IP шлюза:<br>

ip route | grep default<br>

Статическая настройка (пример для /etc/netplan/00-netplan-config.yaml):<br>

network:<br>
  version: 2<br>
  renderer: networkd<br>
  ethernets:<br>
    enp0s3:<br>
      addresses: [192.168.1.100/24]<br>
      gateway4: 192.168.1.1<br>
      nameservers:<br>
        addresses: [1.1.1.1, 8.8.8.8]<br>

Применение:<br>

sudo netplan apply<br>

Перезагрузка и проверка:<br>

sudo reboot<br>
# После загрузки<br>
ip addr show enp0s3<br>
ip route<br>
cat /etc/resolv.conf<br>

Проверка пинга удалённых хостов:<br>

ping -c 4 1.1.1.1<br>
ping -c 4 ya.ru<br>
<img src="img/ping_ya_ru.png"><br>

Вывод должен содержать «0% packet loss».<br>
<img src="img/ping_1_1_1_1.png"><br>

## Part 4. Обновление ОС<br>

Задание: Обновить пакеты до актуальных.<br>

sudo apt update && sudo apt upgrade -y<br>
sudo apt update<br>
<img src="img/Part4_1.png"><br>

При повторном update сообщение об отсутствии обновлений.<br>
<img src="img/Part4_2.png"><br>

## Part 5. Использование команды sudo<br>

Задание: Разрешить sudo пользователю newuser и сменить hostname от его имени.<br>

Добавление в sudoers:<br>

sudo usermod -aG sudo newuser<br>

Смена hostname под newuser:<br>

sudo hostnamectl set-hostname new-hostname<br>

Комментарий: Команда sudo позволяет выполнять привилегированные операции от имени суперпользователя, сохраняя при этом учёт пользователя.<br>
<img src="img/Part5.png"><br>

## Part 6. Установка и настройка службы времени<br>

Задание: Настроить автоматическую синхронизацию времени.<br>

sudo apt install -y chrony<br>
sudo systemctl enable chrony --now<br>

Проверка:<br>

timedatectl show | grep NTPSynchronized<br>
timedatectl<br>

Вывод должен содержать NTPSynchronized=yes.<br>
<img src="img/Part6.png"><br>

## Part 7. Установка и использование текстовых редакторов<br>

Задание: Установить VIM, NANO, JOE.<br>

sudo apt install -y vim nano joe<br>

Создание файлов и сохраенение в редакторах:<br>

VIM: vim test_vim.txt → ввод nickname → :wq<br>
<img src="img/Part7_vim.png"><br>

NANO: nano test_nano.txt → ввод nickname → Ctrl+X, Enter<br>
<img src="img/Part7_nano.png"><br>

JOE: joe test_joe.txt → ввод nickname → Ctrl+K, X<br>
<img src="img/Part7_joe.png"><br>


## Part 8. Установка и базовая настройка сервиса SSHD<br>

Задание: Установить SSH, автозапуск, перенастройка порта на 2022.<br>

sudo apt install -y openssh-server - установка основы = openSSH server<br>
sudo systemctl enable ssh --now - включение ssh службы<br>
sudo sed -i 's/#Port 22/Port 2022/' /etc/ssh/sshd_config - смена порта на 2022<br>
sudo systemctl restart ssh - перезагрузка службы для применения настроек<br>

Проверка процесса:<br>

ps aux | grep sshd<br>  - команда grep достает данные о службе

Проверка порта:<br>

sudo netstat -tan | grep 2022<br>

Вывод должен содержать 0.0.0.0:2022 0.0.0.0:* LISTEN.<br>
<img src="img/Part8.png"><br>

## Part 9. Установка и использование утилит top, htop<br>

Задание: Установить и запустить top, htop; снять показатели.<br>

sudo apt install -y htop<br>
top -n 1 > top_output.txt<br>
htop
<img src="img/Part9_htop.png"><br>

Данные: uptime: 23min, пользователи: 1, load average: 0.0, процессы: 26, CPU: 0.3, MEM: 0.0, pid-max: 637.<br>
<img src="img/Part9_top.png"><br>

Отфильтрованные данные:<br>
PID, PERCENT_CPU:<br>
<img src="img/Part9_percent_cpu.png"><br>

PERCENT_MEM:<br>
<img src="img/Part9_mem.png"><br>

TIME:<br>
<img src="img/Part9_time.png"><br>

Отфильтрованному для процесса sshd:<br>
<img src="img/Part9_sshd.png"><br>

C процессом syslog, найденным, используя поиск<br>
<img src="img/Part9_syslog.png"><br>

C добавленным выводом hostname, clock и uptime<br>
<img src="img/Part9_done.png"><br>


## Part 10. Использование утилиты fdisk<br>

Задание: Запустить fdisk -l.<br>

sudo fdisk -l<br>

В отчёте: Название диск: /dev/sda, размер диска: 25.00Gb, количество секторов: 3, размер swap: 2Gb.<br>
<img src="img/Part10.png"><br>

## Part 11. Использование утилиты df<br>

Задание: Запустить df и df -Th.<br>

df /<br>
df -Th /<br>
<img src="img/Part11_df.png"><br>

В отчёте (/): размер: 11670060, занято: 0, свободно: 11670060, %исп: 0%<br>
В df -Th: добавить тип ФС: ext4.<br>
<img src="img/Part11_dfTh.png"><br>

## Part 12. Использование утилиты du<br>

Задание: du для /home, /var, /var/log, а также du для каждого элемента в /var/log.<br>

du -b /home /var /var/log<br>
du -h /var/log/*<br>
<img src="img/Part12.png"><br>

Размер /home: 118.8Kb<br>
Размер /var: 936.7Kb<br>
Размер /var/log: 37.3Kb<br>

## Part 13. Установка и использование утилиты ncdu<br>

Задание: Установить ncdu, вывести размеры.<br>

sudo apt install -y ncdu<br>
ncdu /home /var /var/log<br>
<img src="img/Part13.png"><br>

Размер /home: 148.0 Kb<br>
Размер /var: 909Mb<br>
Размер /var/log: 42Mb<br>

## Part 14. Работа с системными журналами<br>

Задание: Просмотреть /var/log/dmesg, /var/log/syslog, /var/log/auth.log.<br>

sudo tail -n 50 /var/log/dmesg<br>
<img src="img/Part14_dmesg.png"><br>
tail -n 50 /var/log/syslog<br>
tail -n 50 /var/log/auth.log<br>
<img src="img/Part14_authlog.png"><br>

В отчёте: время последней успешной авторизации, имя, метод.<br>

sudo systemctl restart ssh<br>
grep sshd /var/log/syslog | tail -n 10<br>
<img src="img/Part14_main.png"><br>

## Part 15. Использование планировщика заданий CRON<br>

Задание: Запустить uptime каждые 2 минуты через crontab.<br>

(crontab -l 2>/dev/null; echo "*/2 * * * * uptime >> /var/log/cron_uptime.log") | crontab <br>
<img src="img/Part15_main.png"><br>

Проверка в журналах:<br>
<img src="img/Part15_logs.png"><br>

grep CRON /var/log/syslog | grep uptime<br>
crontab -l<br>

Удаление заданий:<br>

crontab -r<br>
crontab -l<br>
<img src="img/Part15_done.png"><br>


