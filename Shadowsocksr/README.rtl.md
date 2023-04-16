<div dir=auto>

# نصب shadowsocksr

برای نصب ابتدا وارد پوشه `shadowsocksr` شوید:

```bash
cd /opt/freedom/Shadowsocksr
```

## تنظیمات مربوط به فایل کانفیگ

ابتدا با دستور زیر فایل `config.json` را از روی فایل نمونه ایجاد میکنیم.

```bash
cp config-sample.json config.json
```

با دستور زیر فایل `config.json` را باز کنید و پس از انجام تغییرات لازم با زدن کلیدهای `ctrl + x` و `y` و `enter` تغییرات را ذخیره کنید.

```bash
nano config_sample.json
```

محتوای فایل مذکور به شکل زیر است:

```json
{
  "server": "0.0.0.0",
  "server_ipv6": "::",
  "server_port": 8080,
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "password": "password",
  "timeout": 120,
  "udp_timeout": 60,
  "method": "chacha20-ietf",
  "protocol": "auth_aes128_md5",
  "protocol_param": "",
  "obfs": "http_simple",
  "obfs_param": "",
  "redirect": "",
  "dns_ipv6": true,
  "fast_open": true,
  "workers": 1
}
```

### تغییر `port`

مقدار `server_port` را به مقدار دلخواه خود تغییر دهید. توصیه می‌شودعددی چهار رقمی انتخاب کنید.

### تغییر `password`

مقدار `password` را به پسورد دلخواه تغییر دهید. توصیه می‌شود از پسورد پیشفرض استفاده نکنید

### تغییر `method`

برای `method` می‌توانید یکی از مقادیر زیر را انتخاب کنید:

```txt
none
aes-256-cfb
aes-192-cfb
aes-128-cfb
aes-256-cfb8
aes-192-cfb8
aes-128-cfb8
aes-256-ctr
aes-192-ctr
aes-128-ctr
chacha20-ietf
chacha20
salsa20
xchacha20
xsalsa20
rc4-md5
```

متود پیشنهادی ما `chacha20-ietf` هست.

### تغییر `protocol`

برای `protocol` می‌توانید یکی از مقادیر زیر را انتخاب کنید:

```txt
origin
verify_deflate
auth_sha1_v4
auth_sha1_v4_compatible
auth_aes128_md5
auth_aes128_sha1
auth_chain_a
auth_chain_b
auth_chain_c
auth_chain_d
auth_chain_e
auth_chain_f
```

پروتکل پیشنهادی ما `auth_aes128_md5` هست.

### تغییر `obfs`

برای `obfs` می‌توانید یکی از مقادیر زیر را انتخاب کنید:

```txt
plain
http_simple
http_post
tls1.2_ticket_auth
tls1.2_ticket_fastauth
```

مبهم‌ساز پیشنهادی ما `http_simple` هست.

## تنظیمات مربوط به فایل داکرکمپوز

ستور زیر فایل `docker-compose.yml` را باز کنید و پس از انجام تغییرات لازم با زدن کلیدهای `ctrl + x` و `y` و `enter` تغییرات را ذخیره کنید.

```bash
nano docker-compose.yml
```

محتوای فایل مذکور به شکل زیر است:

```yml
version: "3"

services:
  ssr:
    image: rahgozar94725/ssr
    container_name: ssr
    restart: always
    dns:
      - 8.8.8.8
      - 8.8.4.4
    volumes:
      - ./config_sample.json:/etc/shadowsocksr/config.json:ro
```

با دستور زیر پورت انتخابی در مرحله قبل رادر فایروال باز کنید. اگر پورت را تغییر داده اید آن را با `8080` تعویض کنید
README.rtl.md

```bash
ufw allow 8080 && ufw allow 8080/udp
```

## راه‌اندازی

با دستور زیر کانتینر را اجرا کنید:

```bash
docker-compose up -d
```

با دستور زیر می‌توانید لاگ سرور را ببینید:

```bash
docker-compose logs -f
```

با دستور زیر می توانید سرور را خاموش کنید:

```bash
docker-compose down
```

با دستور زیر می توانید کانتینر خود را به جدیدترین ورژن منتشر شده آپدیت کنید.

```bash
docker-compose down && docker-compose up --pull always -d
```

## کلاینت

- [android](https://github.com/shadowsocksrr/shadowsocksr-android/releases/download/3.5.4/shadowsocksr-android-3.5.4.apk)
- [windows](https://github.com/shadowsocksrr/shadowsocksr-csharp/releases/download/4.9.2/ShadowsocksR-win-4.9.2.zip)

![visitors](https://visitor-badge.glitch.me/badge?page_id=hznzzt0bezuzut4g&left_color=black&right_color=blue)
![visitors](https://visitor-badge.glitch.me/badge?page_id=p349hm7zbpijpqan&left_color=black&right_color=blue)

</div>
