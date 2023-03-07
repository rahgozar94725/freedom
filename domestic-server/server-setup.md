<div dir="auto">

# تنظیمات اولیه سرور

در ابتدا بعد از خریداری سرور باید یک سری کارهای اولیه انجام بگیرد. تمامی آموزش‌ها بر اساس `ubuntu 20.04` و `ubuntu 22.04` است. همچنین در تمام آموزش از یوزر `root` استفاده میکنیم. اگر یوزر شما غیر از این بود به ابتدای تمامی دستورات عبارت `sudo` را اضافه کنید!

---

## تغییر پورت پیشفرض SSH

به دلیل مشکلات اخیر و همچنین برای افزایش امنیت سرور و جلوگیری از کرک شدن آن توصیه می‌شود پورت پیشفرض سرور را تغییر بدهید. برای این منظور دستور زیر را در ترمینال سرور وارد کنید:

```bash
nano /etc/ssh/sshd_config
```

به وسیله دکمه‌های بالا و پایین کیبورد به خط زیر بیاید:

```conf
#Port 22
```

علامت # اول خط را حذف کرده و عدد 22 را به پورت دلخواه خود تغییر دهید. پیشنهاد میکنم یک عدد چهار رقمی انتخاب کنید. به عنوان مثال:

```conf
Port 4256
```

بعد از انجام تغییرات دکمه‌های `ctrl + x` و `y` و `enter` را بزنید تا تغییرات ذخیره شده و فایل بسته شود. سپس دستور زیر را در ترمینال وارد کنید:

```bash
systemctl reload sshd
```

حالا یک ترمینال با پورت تغییر یافته باز کنید، اگر ترمینال باز شد ترمینال قبلی را ببندید و مرحله بعدی را انجام بدهید اما اگر ترمینال جدید باز نشد در ترمینال قبلی بار دیگر مرال بالا را چک کنید.

---

## آپدیت سرور و نصب برنامه‌های مورد نیاز

با دستور زیر سرور را آپدیت کنید.

```bash
sh -c 'apt-get update; apt-get upgrade -y; apt-get dist-upgrade -y; apt-get autoremove -y; apt-get autoclean -y'
```

در طول آپدیت هر سوالی شد با زدن دکمه `enter` حالت پیشفرض را انتخاب کنید.

با زدن دستور زیر در ترمینال برنامه‌های مورد نیاز را نصب کنید:

```bash
apt-get install -y software-properties-common wget curl git socat cron busybox bash-completion locales nano apt-utils iptables
```

---

## تنظیمات فایروال

در سرور داخلی با توجه به اینکه ما از `iptables` به عنوان یکی از روش های فوروارد ترافیک استفاده میکنیم، تنظیمات فایروال را توسط `iptables` انجام میدهیم.

ابتدا با دستور زیر یک فایل را برای ذخیره تنظیمات مربوطه باز کنید.

```bash
nano /opt/iptable.sh
```

محتوای کادر زیر را به درون ویرایشگر نانو کپی کنید

```bash
#!/usr/bin/env bash
# Reset
iptables -t filter -F
iptables -t filter -X
# Incoming and redirected traffic is blocked
iptables -t filter -P INPUT DROP
#iptables -t filter -P FORWARD DROP
# Outgoing traffic is allowed
iptables -t filter -P OUTPUT ACCEPT
# We don't break the established connections
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
# Authorizes the incoming and outgoing traffic on the loopback network interface (IP : 127.0.0.1)
iptables -t filter -A INPUT -i lo -j ACCEPT
iptables -t filter -A OUTPUT -o lo -j ACCEPT
# ICMP (ping)
iptables -t filter -A INPUT -p icmp -j ACCEPT
iptables -t filter -A OUTPUT -p icmp -j ACCEPT
# SSH incoming / outgoing
iptables -t filter -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --sport 22 -j ACCEPT
# DNS incoming / outgoing
# DNS queries to the server (TCP and UDP)
iptables -t filter -A OUTPUT -p tcp --sport 53 -j ACCEPT
iptables -t filter -A OUTPUT -p udp --sport 53 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --dport 53 -j ACCEPT
iptables -t filter -A OUTPUT -p udp --dport 53 -j ACCEPT
# DNS requests sent by clients and therefore received by the server (TCP and UDP)
iptables -t filter -A INPUT -p tcp --dport 53 -j ACCEPT
iptables -t filter -A INPUT -p udp --dport 53 -j ACCEPT
# NTP outgoing (time server)
iptables -t filter -A OUTPUT -p udp --sport 123 -j ACCEPT
# Mail SMTP : 25 (Protocol for sending e-mails)
iptables -t filter -A INPUT -p tcp --dport 25 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --sport 25 -j ACCEPT
# Mail POP3 : 110 (Protocol to access to e-mails)
iptables -t filter -A INPUT -p tcp --dport 110 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --sport 110 -j ACCEPT
# Mail POP3S : 995 (Protocol to access to e-mails)
# POP3S is a secure POP3 over SSL
iptables -t filter -A INPUT -p tcp --dport 995 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --sport 995 -j ACCEPT
# Mail IMAP : 143 (Protocol to read emails by a system of remote folders)
iptables -t filter -A INPUT -p tcp --dport 143 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --sport 143 -j ACCEPT
# FTP outgoing
iptables -t filter -A OUTPUT -p tcp --sport 21 -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --sport 20 -j ACCEPT
# FTP incoming
iptables -t filter -A INPUT -p tcp --dport 20 -j ACCEPT
iptables -t filter -A INPUT -p tcp --dport 21 -j ACCEPT
# FTP Passive
iptables -t filter -A OUTPUT -p tcp --sport 50000:50100 -j ACCEPT
iptables -t filter -A INPUT -p tcp --dport 50000:50100 -j ACCEPT
```

**توجه** داشته باشید در صورتی که در مرحله قبل پورت پیشفرض ssh را تغییر داده اید، حتما در متن بالا پورت 22 را با پورت جدید جایگزین کنید. در غیراینصورت دیگر نمیتوانید وارد سرور بشوید.

بعد از انجام تغییرات دکمه‌های `ctrl + x` و `y` و `enter` را بزنید تا تغییرات ذخیره شده و فایل بسته شود.

برای اینکه دستورات فوق بعد از هر بار ریبوت شدن سرور اجرا شود، دستورات زیر را **یک بار** به ترتیب وارد کنید.

**توجه** داشته باشید که اجرای دستورات زیر، خروجی ندارد. در صورتی که بیش از یک بار وارد کنید دچار مشکل خواهد شد.

```bash
chmod +x /opt/iptable.sh
```

```bash
crontab -l | { cat; echo "@reboot /opt/iptable.sh"; } | crontab -
```

### باز و بسته کردن پورت های مورد نیاز

در صورتی که بخواهید در سرور پورتی را باز کنید، باید طبق فرمت زیر، دستور لازم را به فایل `iaptable.sh` که در مرحله قبل ایجاد کردیم، اضافه کنید. و در صورتی که بخواهید پورت اضافه شده را ببندید، باید دستورات اضافه شده مربوط به آن پورت را از فایل فوق حذف کنید.

ابتدا با دستور زیر فایل مورد نظر را باز کنید.

```bash
nano /opt/iptable.sh
```

طبق فرمت زیر، برای هر پورت خطوط زیر را به انتهای فایل اضافه کنید. در عبارات زیر `YOURPORT` را با پورت مورد نظر خود جایگزین کنید.

- برای tcp
  
```bash
iptables -t filter -A INPUT -p tcp --dport YOURPORT -j ACCEPT
iptables -t filter -A OUTPUT -p tcp --sport YOURPORT -j ACCEPT
```

- برای udp

```bash
iptables -t filter -A INPUT -p udp --dport YOURPORT -j ACCEPT
iptables -t filter -A OUTPUT -p udp --sport YOURPORT -j ACCEPT
```

بعد از انجام تغییرات دکمه‌های `ctrl + x` و `y` و `enter` را بزنید تا تغییرات ذخیره شده و فایل بسته شود.

برای اعمال تغییرات باید یک بار سرور خود را با دستور زیر ریبوت کنید.

```bash
shutdown -r now
```

---

## بهینه‌سازی سرور

دستور زیر را اجرا کنید:

```bash
nano /etc/security/limits.conf
```

دو خط زیر را به انتهای فایل باز شده اضافه کنید.

```conf
* soft nofile 51200
* hard nofile 51200
```

به ترتیب با زدن دکمه‌های `ctrl + x` و `y` و `enter` تغییرات را ذخیره کرده و فایل را ببندید.

دستور زیر را اجرا کنید:

```bash
ulimit -n 51200
```

به جای BBR از Hybla استفاده میکنیم که بازدهی به مراتب بیشتری از BBR دارد.

<p align="center">
    <img
        style=
            "display: block;
            margin-left: auto;
            margin-right: auto;
            width: 50%;"
        src="../src/hybla001.png"
        alt="BBR vs Cubic vs Hybla vs PCC">
    </img>
    <img
        style=
            "display: block;
            margin-left: auto;
            margin-right: auto;
            width: 50%;"
        src="../src/hybla002.png"
        alt="BBR vs Cubic vs Hybla vs PCC">
    </img>
    <img
        style=
            "display: block;
            margin-left: auto;
            margin-right: auto;
            width: 50%;"
        src="../src/hybla003.png"
        alt="BBR vs Cubic vs Hybla vs PCC">
    </img>
</p>
دستور زیر را اجرا کنید:

```bash
nano /etc/sysctl.conf
```

خطوط زیر را در انتهای فایل اضافه کنید:

```conf
fs.file-max = 51200
net.core.netdev_max_backlog = 250000
net.core.rmem_max = 67108864
net.core.somaxconn = 4096
net.core.wmem_max = 67108864
net.ipv4.ip_forward=1
net.ipv4.ip_local_port_range = 10000 65000
net.ipv4.tcp_congestion_control = hybla
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_fin_timeout = 30
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_max_tw_buckets = 5000
net.ipv4.tcp_mem = 25600 51200 102400
net.ipv4.tcp_mtu_probing = 1
net.ipv4.tcp_rmem = 4096 87380 67108864
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_recycle = 0
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_wmem = 4096 65536 67108864

```

---

در نهایت با دستور زیر سرور را ریبوت میکنیم:

```bash
shutdown -r now
```

![visitors](https://visitor-badge.glitch.me/badge?page_id=6zuo3ypx94s2kmrv&left_color=black&right_color=blue)
![visitors](https://visitor-badge.glitch.me/badge?page_id=p349hm7zbpijpqan&left_color=black&right_color=blue)
