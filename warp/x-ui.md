# نصب و راه اندازی warp در x-ui

## نکات

برای انجام این آموزش باید با فرمت و سینتکس json آشنایی حداقلی داشته باشید

## نصب warp

#### نصب در سیستم عامل `Ubuntu`

ورژن های پشتیبانی شده:

```text
 - Jammy (22.04)
 - Focal (20.04)
 - Bionic (18.04)
 - Xenial (16.04)
```

دستورات زیر را به ترتیب اجرا کنید:

```bash
curl https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg
```

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list
```

```bash
sudo apt-get update && sudo apt-get install cloudflare-warp
```

#### نصب در سیستم عامل `Debian`

ورژن های پشتیبانی شده:

```text
 - Bookworm (12)
 - Bullseye (11)
 - Buster (10)
 - Stretch (9)
```

دستورات زیر را به ترتیب اجرا کنید:

```bash
curl https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg
```

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list
```

```bash
sudo apt-get update && sudo apt-get install cloudflare-warp
```

#### نصب در سیستم عامل `CentOS` و `RHEL`

ورژن های پشتیبانی شده:

```text
 - 8
```

دستورات زیر را به ترتیب اجرا کنید:

```bash
curl -fsSl https://pkg.cloudflareclient.com/cloudflare-warp-ascii.repo | sudo tee /etc/yum.repos.d/cloudflare-warp.repo
```

```bash
sudo yum update
```

```bash
sudo yum install cloudflare-warp
```

## راه اندازی warp

لطفا دستورات را به ترتیب زیر انجام دهید، در غیر این صورت امکان از دست رفتن دسترسی به سرور شما وجود دارد

```bash
warp-cli register # press y to agree
warp-cli set-mode proxy
warp-cli set-proxy-port 10864
warp-cli connect
```

## تنظیمات x-ui

به `Settings > Xray Config` رفته و محتوای json را به شکل زیر تغییر بدهید.

### نسخه آماده

اگر تنها با گوگل مشکل دارید میتوانید از [کانفیگ آماده](config_kafka_warp_block-ir.json) استفاده کنید تنها کافیست کانفیگ جدید را جایگزین کرد و `save` سپس `restart` را بزنید. در این صورت آموزش در اینجا تمام خواهد شد.

این تنظیمات آماده برای پنل کافکا آماده شده است.

### قسمت Outbounds

در آرایه `"outbounds"`

تنظیمات warp به این صورت است:

```json
{
  "protocol": "socks",
  "settings": {
    "servers": [
      { 
        "address": "127.0.0.1",
        "port":10864
      }
    ]
  },
  "tag":"warp"
}
```

+ مسیر دهی کلی: اگر میخواهید همه چیز از warp رد شود تنها کافیست تنظیمات warp را در اول آرایه اضافه کنید. (آموزش همینجا تمام می شود، `save` و `restart` را بزنید)
+ مسیر دهی انتخابی: تنظیمات warp المان دوم به بعد آرایه قرار میگیرد (فقط اول نباشد)

### قسمت Routes

این قسمت تنها برای مسیر دهی انتخابی است

در json کلی x-ui به قسمت `"routing" > "rules"` رفته و به این صورت `rule` اضافه کنید

#### مسیر بر اساس دامنه

```json
{
  "domain" : [
    "geosite:google",
    "geosite:instagram",
    .
    .
    .
  ],
  "outboundTag" : "warp",
  "type" : "field"
}
```

#### مسیر بر اساس ip

```json
{
  "ip" : [
    "geoip:IR",
    "geoip:CN",
    .
    .
    .
  ],
  "outboundTag" : "warp",
  "type" : "field"
}
```

#### مسیر دهی حرفه ای

برای یادگیری نحوه مسیر دهی در xray [به این لینک](https://xtls.github.io/config/routing.html) مراجعه
