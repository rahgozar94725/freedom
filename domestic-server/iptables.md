<div dir=auto>

# مقدمه

در این بخش به ایجاد یک تونل بین سرور ایران و سرور خارج می پردازیم.برای انجام تونل راهای مختلفی وجود دارد.راهکاری که در این قسمت به آن میپردازیم، روش `iptables` می باشد.
توجه داشته باشید تمام مراحل تنظیم `iptables` بر روی سرور ایران صورت می گیرد.در نتیجه دستورات زیر را بر روی سرور ایران اعمال نمایید.

## فوروارد ترافیک توسط iptables

ابتدا با دستور زیر فایلی را که در مرحله راه اندازی اولیه سرور ایجاد کردیم باز کنید

```bash
nano /opt/iptable.sh
```

طبق فرمت زیر برای هر پورتی که قصد فوروارد آن را دارید، دستورات زیر را بعد از ویرایش به انتهای فایل اضافه کنید.

```bash
iptables -t nat -A PREROUTING -p PROTOCOL -d LISTEN-IP --dport LISTEN-PORT -j DNAT --to-destination DESTINATION-IP:DESTINATION-PORT
iptables -t nat -A POSTROUTING -p PROTOCOL -d DESTINATION-IP --dport DESTINATION-PORT -j SNAT --to-source LISTEN-IP
```

در عبارات فوق مقادیر زیر را تغییر بدهید.

- تمام مقادیر `PROTOCOL` را طبق نیاز به `tcp` و یا `udp` تغییر دهید.
- تمام مقادیر `LISTEN-IP` را به آیپی سرور داخلی خود تغییر دهید.
- تمام مقادیر `LISTEN-PORT` را به پورتی که قصد دارید ترافیک ورودی آن را فوروارد کنید، تغییر دهید.
- تمامی مقادیر `DESTINATION-IP` را به آیپی سرور مقصد خود تغییر دهید.
- تمامی مقادیر `DESTINATION-PORT` را به پورت مورد نظر در سرور مقصد تغییر دهید.

بعد از انجام تغییرات دکمه‌های `ctrl + x` و `y` و `enter` را بزنید تا تغییرات ذخیره شده و فایل بسته شود.

برای اعمال تغییرات باید یک بار سرور خود را با دستور زیر ریبوت کنید.

```bash
shutdown -r now
```

### مثال

فرض میکنیم شما در سرور خارج با آیپی `2.2.2.2` و در پورت `2222` یک فیلتر شکن از نوع xray دارید. آیپی سرور داخلی شما هم `1.1.1.1` میباشد.

- اگر بخواهید همان پورت `2222` را در سرور ایران انتقال دهید:

```bash
iptables -t nat -A PREROUTING -p tcp -d 1.1.1.1 --dport 2222 -j DNAT --to-destination 2.2.2.2:2222
iptables -t nat -A POSTROUTING -p tcp -d 2.2.2.2 --dport 2222 -j SNAT --to-source 1.1.1.1
```

- اگر بخواهید پورت دیگر از سرور ایران انتقال دهید. مثلا پورت `4444`. کاربرد این زمانی هست که شما دو سرور خارج دارید که هردو از پورت `2222` استفاده میکنند.

```bash
iptables -t nat -A PREROUTING -p tcp -d 1.1.1.1 --dport 4444 -j DNAT --to-destination 2.2.2.2:2222
iptables -t nat -A POSTROUTING -p tcp -d 2.2.2.2 --dport 2222 -j SNAT --to-source 1.1.1.1
```

اکنون در لینک فیلترشکن خود باید آیپی و پورت سرور ایران را وارد کنید.

![visitors](https://visitor-badge.glitch.me/badge?page_id=jsce25dxf47gtyin&left_color=black&right_color=blue)
![visitors](https://visitor-badge.glitch.me/badge?page_id=p349hm7zbpijpqan&left_color=black&right_color=blue)

</div>
