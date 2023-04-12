# نصب و راه اندازی warp در x-ui

## نکات
برای انجام این آموزش باید با فرمت و سینتکس json آشنایی حداقلی داشته باشید

## نصب warp

### نصب نرمال
نکته: اگر در این روش به خطا خوردید روش بعد را انجام دهید

#### نصب در سیستم عامل های حاوی apt (مانند `Ubuntu` و `Debian`)
```bash
sudo apt install cloudflare-warp
```
#### نصب در سیستم عامل های حاوی yum (مانند `CentOS` و `RHEL`)
```bash
sudo yum install cloudflare-warp
```


### نصب آخرین نسخه
برای استفاده از warp در x-ui اول به [قسمت دانلود warp](https://pkg.cloudflareclient.com/packages/cloudflare-warp) رفته و لینک پکج مورد نظر خود را کپی کنید.
به طور مثال فرض میکنیم `Ubuntu Jammy (22.04)` سیستم عامل ما هست.

به سرور خود رفته و دستور زیر را اجرا میکنیم

```bash
wget _PACKAGE_LINK_
```

که برای مثال ما میشود

```bash
wget https://pkg.cloudflareclient.com/uploads/cloudflare_warp_2023_3_398_1_amd64_ddd2a223f7.deb
```

پس از آن پکج را نصب میکنیم، که در اوبونتو به این صورت خواهد بود

```bash
sudo apt update
sudo dpkg -i cloudflare_warp_*.deb
sudo apt -f install # Fix missing packages
```

## راه اندازی warp
لطفا دستورات را به ترتیب زیر انجام دهید، در غیر این صورت امکان از دست رفتن دسترسی به سرور شما وجود دارد


```bash
warp-cli register
warp-cli set-mode proxy
warp-cli set-proxy-port 10864
warp-cli connect
```

## تنظیمات x-ui

به `Settings > Xray Config` رفته و محتوای json را به شکل زیر تغییر بدهید.

### نسخه آماده
اگر تنها با گوگل مشکل دارید میتوانید از [کانفیگ آماده](config_kafka_warp_block-ir.json) استفاده کنید تنها کافیست کانفیگ جدید را جایگزین کرد و `save` سپس `restart` را بزنید. در این صورت آموزش در اینجا تمام خواهد شد.

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
