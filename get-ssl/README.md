# دریافت گواهی SSL

---

## الگوریتم رمزنگاری

از بین انواع الگوریتم‌های رمزنگاری در گواهی‌های SSL/TLS، نوع `RSA` عمومی‌تر است و اکثرا انتخاب پیشفرض انواع کلاینت‌های دریافت گواهی است. `ECC` نوع دیگری از رمزنگاری استفاده شده در گواهی‌های SSL/TLS هست که از نظر قدرت رمزنگاری بسیار قدرتمند‌تر از نوع `RSA` هست واز نظر حجم بسیار کوچکتر از آن. امنیت بیشتر باعث می‌شود رمان بازگشایی آن کمی افزایش یابد اما به دلیل حجم بسیار پایین آن انتقال سریعتر خواهد بود. در نتیجه با استفاده از نوع `ECC` ما هم امنیت و هم سرعت بیشتری خواهیم داشت و این باعث کاهش لاتنسی در برقراری ارتباط خواهد شد. برای کسب اطلاعات بیشتر می‌توانید تحقیق کنید. در جدول زیر قدرت رمزنگاری و حجم این دو مقایسه شده است.

| Security (In Bits) | RSA Key Length Required | ECC Key Length Required |
| :----------------: | :---------------------: | :---------------------: |
|         80         |          1024           |         160-223         |
|        112         |          2048           |         224-255         |
|        128         |          3072           |         256-383         |
|        192         |          7680           |         384-511         |
|        256         |          15360          |          512+           |

---

## دریافت گواهی

ما اسکریپت `acme.sh` را در مرحله [تنظیمات اولیه سرور](../server-setup/README.md) نصب کرده‌ایم. در اینجا به دو روش گواهی SSL خود را دریافت خواهیم کرد. از آنجایی که قصد داریم چند نوع سرویس راه‌اندازی کنیم که ممکن است نیاز به SSL/TLS داشته باشیم گواهی‌ها را در مسیر زیر نصب می‌کنیم تا در صورت لزوم از آنها استفاده کنیم.

```bash
/opt/cert
```

لازم به ذکر است که در تمامی سرویس‌های این آموزش ما از این مسیر پیشفرض استفاده خواهیم کرد

### با روش DNS Challenge

در صورتی که از `Cloudflare` یا یکی از [پروایدر های موجود در این لیست](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) استفاده می‌کنید، می‌توانید از این روش استفاده کنید. در اینجا ما از توکنی که در آموزش مدیریت دامنه به وسیله کلاودفلر گرفتیم، استفاده می‌کنیم. با استفاده از این روش دیگر نیاز به باز بودن پورت 80 نداریم.

در دستور زیر عبارت `my-API-Token` را با توکن خود جایگزین کرده و اجرا کنید.

```bash
export CF_Token="my-API-Token"
```

در دستور زیر عبارت `sub.test.com` را با دامنه خود جایگزین کرده و اجرا کنید.

```bash
acme.sh --issue --dns dns_cf --keylength ec-256 -d sub.test.com
```

در دستور زیر عبارت `sub.test.com` را با دامنه خود جایگزین کرده و اجرا کنید.

```bash
acme.sh --installcert --ecc -d sub.test.com --key-file /opt/cert/private.key --cert-file /opt/cert/cert.crt --fullchain-file /opt/cert/fullchain.crt --ca-file /opt/cert/ca.crt
```

در صورتی که بیش از یک دامنه دارید برای استفاده از این روش باید مطابق [این لینک](https://github.com/acmesh-official/acme.sh/wiki/Utilize-multiple-DNS-API-keys) عمل کنید.

### با روش standalone

توجه داشته باشید در این روش باید پورت 80 در فایروال باز بوده و آزاد باشد.

در دستور زیر عبارت `sub.test.com` را با دامنه خود جایگزین کرده و اجرا کنید.

```bash
acme.sh --issue --standalone --keylength ec-256 -d sub.test.com
```

در دستور زیر عبارت `sub.test.com` را با دامنه خود جایگزین کرده و اجرا کنید.

```bash
acme.sh --installcert --ecc -d sub.test.com --key-file /opt/cert/private.key --cert-file /opt/cert/cert.crt --fullchain-file /opt/cert/fullchain.crt --ca-file /opt/cert/ca.crt
```

### با روش webroot

از آنجایی که در این روش نیاز به وب سرور داریم، آموزش این قسمت در بخش [nginx](../nginx/README.md) توضیح داده شده است.

---

## آموزش حذف گواهی

در صورت نیاز به حذف گواهی می‌توانید با اجرای دستورات زیر به ترتیب ذکر شده گواهی فعلی خود را حذف کنید.

در دستورات زیر عبارت `sub.test.com` را با دامنه خود جایگزین کرده و اجرا کنید.

```bash
acme.sh --revoke -d sub.test.com
```

```bash
acme.sh --remove -d sub.test.com
```

```bash
rm -rf ~/.acme.sh/sub.test.com_ecc
```

![visitors](https://visitor-badge.glitch.me/badge?page_id=wquc93bgao7guwi1&left_color=black&right_color=blue)
![visitors](https://visitor-badge.glitch.me/badge?page_id=p349hm7zbpijpqan&left_color=black&right_color=blue)
