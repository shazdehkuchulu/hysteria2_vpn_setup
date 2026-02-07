&#x202b;
# راهنمای نصب و راه‌اندازی Psiphon Conduit روی لینوکس

&#x202b;
Conduit یک نود داوطلبانه است که به کاربران در مناطق تحت سانسور کمک می‌کند تا به شبکه Psiphon متصل شوند. با اجرای Conduit روی سرور لینوکس خود، شما به عنوان یک رله (relay) برای ترافیک رمزنگاری شده عمل می‌کنید.

---

&#x202b;
## پیش‌نیازها

&#x202b;
- یک سرور لینوکس (Ubuntu/Debian/CentOS/Fedora)
- دسترسی root یا sudo
- اتصال اینترنت

---

&#x202b;
## مرحله ۱: دانلود Conduit

&#x202b;
### برای سیستم‌های x86_64 (اکثر سرورها):

```bash
curl -L -o conduit https://github.com/ssmirr/conduit/releases/download/d8522a8/conduit-linux-amd64
```

&#x202b;
### برای سیستم‌های ARM64 (مثل Raspberry Pi):

```bash
curl -L -o conduit https://github.com/ssmirr/conduit/releases/download/d8522a8/conduit-linux-arm64
```

&#x202b;
> **نکته:** برای آخرین نسخه، [صفحه Releases](https://github.com/ssmirr/conduit/releases) را بررسی کنید.

---

&#x202b;
## مرحله ۲: انتقال به مسیر سیستم

```bash
sudo mv conduit /usr/local/bin/conduit
sudo chmod +x /usr/local/bin/conduit
```

&#x202b;
بررسی کنید که درست نصب شده:

```bash
/usr/local/bin/conduit --help
```

---

&#x202b;
## مرحله ۳: ساخت کاربر سیستمی

&#x202b;
برای امنیت بیشتر، یک کاربر جداگانه برای Conduit می‌سازیم:

```bash
sudo useradd --system --home /var/lib/conduit --shell /usr/sbin/nologin conduit
sudo mkdir -p /var/lib/conduit
sudo chown conduit:conduit /var/lib/conduit
```

---

&#x202b;
## مرحله ۴: ساخت سرویس systemd

&#x202b;
فایل سرویس را بسازید:

```bash
sudo nano /etc/systemd/system/conduit.service
```

&#x202b;
محتوای زیر را کپی و پیست کنید:

```ini
[Unit]
Description=Psiphon Conduit Service
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=conduit
Group=conduit
ExecStart=/usr/local/bin/conduit start
WorkingDirectory=/var/lib/conduit
Restart=always
RestartSec=5
LimitNOFILE=1048576

# Security Config
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=strict
ProtectHome=true
ReadWritePaths=/var/lib/conduit

[Install]
WantedBy=multi-user.target
```

&#x202b;
ذخیره و خروج:
- `Ctrl+O` سپس `Enter` برای ذخیره
- `Ctrl+X` برای خروج

---
&#x202b;
## مرحله ۵: فعال‌سازی و اجرای سرویس

&#x202b;
بارگذاری مجدد systemd:

```bash
sudo systemctl daemon-reload
```

&#x202b;
فعال‌سازی برای اجرای خودکار در هنگام بوت:

```bash
sudo systemctl enable conduit
```

&#x202b;
شروع سرویس:

```bash
sudo systemctl start conduit
```

&#x202b;
✅ حالا می‌توانید ترمینال را ببندید — Conduit به کار خود ادامه می‌دهد.

---

&#x202b;
## مرحله ۶: بررسی وضعیت

&#x202b;
### وضعیت سرویس:

```bash
sudo systemctl status conduit
```

&#x202b;
باید ببینید:

```
Active: active (running)
```

&#x202b;
### مشاهده لاگ‌ها به صورت زنده:

```bash
journalctl -u conduit -f
```

&#x202b;
برای خروج از لاگ‌ها `Ctrl+C` بزنید (سرویس متوقف نمی‌شود).

---

&#x202b;
## دستورات مفید

&#x202b;
| عملیات | دستور |
|--------|-------|
| شروع سرویس | `sudo systemctl start conduit` |
| توقف سرویس | `sudo systemctl stop conduit` |
| ری‌استارت سرویس | `sudo systemctl restart conduit` |
| بررسی وضعیت | `sudo systemctl status conduit` |
| مشاهده لاگ‌ها | `journalctl -u conduit -f` |

---

&#x202b;
## تنظیمات اختیاری: محدودیت پهنای باند

&#x202b;
اگر می‌خواهید پهنای باند یا تعداد کاربران را محدود کنید، فایل سرویس را ویرایش کنید:

```bash
sudo nano /etc/systemd/system/conduit.service
```

&#x202b;
خط `ExecStart` را تغییر دهید:

```ini
ExecStart=/usr/local/bin/conduit start --max-clients 50 --bandwidth-limit-mbps 20
```

&#x202b;
سپس:

```bash
sudo systemctl daemon-reload
sudo systemctl restart conduit
```

---
&#x202b;
## روش جایگزین: استفاده از Docker

&#x202b;
اگر ترجیح می‌دهید از Docker استفاده کنید:

&#x202b;
### دانلود ایمیج:

```bash
docker pull ghcr.io/ssmirr/conduit/conduit:latest
```

&#x202b;
### اجرا:

```bash
docker run -d \
  --name conduit \
  -v conduit-data:/home/conduit/data \
  --restart unless-stopped \
  ghcr.io/ssmirr/conduit/conduit:latest
```

&#x202b;
### بررسی وضعیت:

```bash
docker ps
docker logs conduit --tail 50
```

---

&#x202b;
## عیب‌یابی

&#x202b;
### سرویس شروع نمی‌شود

&#x202b;
بررسی لاگ‌ها:

```bash
journalctl -u conduit --no-pager -n 50
```

&#x202b;
### خطای دسترسی

&#x202b;
مطمئن شوید که مالکیت فولدر درست است:

```bash
sudo chown -R conduit:conduit /var/lib/conduit
```

&#x202b;
### بررسی پورت‌ها

&#x202b;
ببینید Conduit روی کدام پورت گوش می‌دهد:

```bash
ss -tlnp | grep conduit
```

---

&#x202b;
## منابع

&#x202b;
- [اصلی GitHub پروژه Conduit](https://github.com/ssmirr/conduit)
- [صفحه Releases](https://github.com/ssmirr/conduit/releases)

---

&#x202b;
## Conduit چیست؟

&#x202b;
Conduit یک نود رله داوطلبانه برای شبکه Psiphon است:

&#x202b;
- ✅ به کاربران در مناطق سانسور شده کمک می‌کند
- ✅ ترافیک رمزنگاری شده را از کلاینت‌ها به شبکه Psiphon منتقل می‌کند
- ❌ یک سرور کامل Psiphon نیست — فقط یک رله واسط است

&#x202b;
با اجرای Conduit، شما به صورت داوطلبانه به کاربرانی که در مناطق تحت سانسور هستند کمک می‌کنید تا به اینترنت آزاد دسترسی پیدا کنند.

---

&#x202b;
موفق باشید! 🎉
