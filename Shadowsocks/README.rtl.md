<div dir=rtl>

# نصب shadowsocks

برای نصب ابتدا وارد پوشه `shadowsocks` شوید

```bash
cd /opt/freedom/shadowsocks
```

## تنظیمات مربوط به فایل کانفیگ

با دستور زیر یک کپی از فایل `config-sample.json` ایجاد میکنیم

```bash
cp config-sample.json config.json
```

با دستور زیر فایل `config.json` را باز کنید و پس از انجام تغییرات لازم با زدن کلیدهای `ctrl + x` و `y` و `enter` تغییرات را ذخیره کنید.

```bash
nano config.json
```

محتوای فایل مذکور به شکل زیر است:

```json
{
  "server": "0.0.0.0",
  "server_port": 7070,
  "password": "password",
  "timeout": 300,
  "method": "aes-128-gcm",
  "nameserver": "8.8.8.8",
  "mode": "tcp_and_udp",
  "plugin": "xray-plugin",
  "plugin_opts": "server;mode=grpc;serviceName=grpc7070"
}
```
