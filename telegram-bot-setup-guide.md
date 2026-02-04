&#x202b;
# راهنمای فعال‌سازی ربات تلگرام برای مدیریت کاربران VPN

&#x202b;
با استفاده از ربات تلگرام می‌توانید کاربران VPN را به راحتی مدیریت کنید.

---

&#x202b;
## مرحله ۱: ساخت ربات در تلگرام

&#x202b;
۱. در تلگرام، به اکانت [@BotFather](https://t.me/BotFather) پیام دهید

&#x202b;
۲. دستور `/newbot` را ارسال کنید

&#x202b;
۳. یک نام برای ربات انتخاب کنید (مثلاً `My VPN Bot`)

&#x202b;
۴. یک یوزرنیم برای ربات انتخاب کنید (باید به `bot` ختم شود، مثلاً `myvpn_bot`)

&#x202b;
۵. پس از ساخت ربات، BotFather یک **توکن** به شما می‌دهد. آن را کپی کنید:

```
123456789:ABCdefGHIjklMNOpqrsTUVwxyz
```

&#x202b;
> **نکته:** این توکن را محرمانه نگه دارید!

---

&#x202b;
## مرحله ۲: فعال‌سازی ربات در سرور

&#x202b;
به سرور SSH بزنید و دستور زیر را اجرا کنید:

```bash
bash <(curl -sL https://bit.ly/realityez) --enable-tgbot true --tgbot-token <telegram-bot-token> --tgbot-admins=<your-telegram-username>
```

&#x202b;
### مثال:

```bash
bash <(curl -sL https://bit.ly/realityez) --enable-tgbot true --tgbot-token 123456789:ABCdefGHIjklMNOpqrsTUVwxyz --tgbot-admins=kian
```

&#x202b;
### چند ادمین:

&#x202b;
اگر می‌خواهید چند نفر به ربات دسترسی داشته باشند، یوزرنیم‌ها را با کاما جدا کنید:

```bash
bash <(curl -sL https://bit.ly/realityez) --enable-tgbot true --tgbot-token 123456789:ABCdefGHIjklMNOpqrsTUVwxyz --tgbot-admins=kian,arash,pooneh
```

&#x202b;
> **نکته:** یوزرنیم‌ها را بدون علامت `@` وارد کنید.

---
&#x202b;
## مرحله ۳: استفاده از ربات

&#x202b;
۱. در تلگرام، ربات خود را پیدا کنید (با یوزرنیمی که در مرحله ۱ ساختید)

&#x202b;
۲. روی **Start** کلیک کنید

&#x202b;
۳. حالا می‌توانید کاربران را از طریق ربات مدیریت کنید:
   - اضافه کردن کاربر جدید
   - مشاهده لیست کاربران
   - دریافت کانفیگ و QR کد
   - حذف کاربر

---

&#x202b;
## غیرفعال کردن ربات

&#x202b;
اگر می‌خواهید ربات را غیرفعال کنید:

```bash
bash <(curl -sL https://bit.ly/realityez) --enable-tgbot false
```

---

&#x202b;
## عیب‌یابی

&#x202b;
### ربات جواب نمی‌دهد

&#x202b;
۱. مطمئن شوید توکن درست وارد شده

&#x202b;
۲. مطمئن شوید یوزرنیم شما در لیست ادمین‌ها هست

&#x202b;
۳. سرویس را ری‌استارت کنید:

&#x202b;
```bash
bash <(curl -sL https://bit.ly/realityez) -r
```

&#x202b;
۴. لاگ‌ها را بررسی کنید:

```bash
docker logs reality-ezpz-tgbot-1 --tail 50
```

---
&#x202b;
## منابع

- [GitHub پروژه اصلی](https://github.com/aleskxyz/reality-ezpz)

---
&#x202b;
موفق باشید! 🎉
