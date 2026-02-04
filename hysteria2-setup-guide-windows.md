&#x202b;
# راهنمای نصب Reality VPN با Hysteria2 (برای کاربران ویندوز)

&#x202b;
## پیش‌نیازها

&#x202b;
- یک سرور لینوکس (Ubuntu/Debian/CentOS)
- یک ساب‌دامین که به آی‌پی سرور اشاره می‌کند
- ویندوز ۱۰ یا ۱۱

---
&#x202b;
## مرحله ۱: ساخت کلید SSH

&#x202b;
### روش اول: استفاده از PowerShell (ویندوز ۱۰/۱۱)

&#x202b;
۱. PowerShell را به عنوان Administrator باز کنید

&#x202b;
۲. بررسی کنید که OpenSSH نصب است:

```powershell
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'
```

&#x202b;
۳. اگر نصب نیست، نصب کنید:

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```

&#x202b;
۴. کلید SSH بسازید:

```powershell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

&#x202b;
۵. از شما مسیر ذخیره کلید پرسیده می‌شود. برای استفاده از مسیر پیش‌فرض، Enter بزنید:

```
Enter file in which to save the key (C:\Users\YourName\.ssh\id_ed25519):
```
&#x202b;
۶. یک رمز عبور (passphrase) وارد کنید یا برای بدون رمز، Enter بزنید

&#x202b;
۷. دو فایل ساخته می‌شود:
   - `C:\Users\YourName\.ssh\id_ed25519` — کلید خصوصی (این را هرگز به اشتراک نگذارید!)
   - `C:\Users\YourName\.ssh\id_ed25519.pub` — کلید عمومی

&#x202b;
۸. کلید عمومی را کپی کنید:

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

&#x202b;
۹. این کلید را هنگام ساخت سرور (مثلا در Hetzner) در قسمت SSH Keys اضافه کنید.

---
&#x202b;
### روش دوم: استفاده از PuTTYgen

&#x202b;
۱. [PuTTY](https://www.putty.org/) را دانلود و نصب کنید

&#x202b;
۲. برنامه **PuTTYgen** را باز کنید

&#x202b;
۳. در پایین پنجره، **Ed25519** را انتخاب کنید

&#x202b;
۴. روی **Generate** کلیک کنید و موس را در پنجره حرکت دهید

&#x202b;
۵. پس از ساخت کلید:
   - کلید عمومی را از کادر بالا کپی کنید (برای Hetzner)
   - روی **Save private key** کلیک کنید و فایل `.ppk` را ذخیره کنید

---
&#x202b;
## مرحله ۲: تنظیم DNS

&#x202b;
قبل از نصب، ساب‌دامین خود را به آی‌پی سرور متصل کنید:

&#x202b;
۱. وارد پنل مدیریت دامنه شوید

&#x202b;
۲. یک رکورد A بسازید:
   - **Name:** نام ساب‌دامین (مثلاً `vpn`)
   - **Type:** A
   - **Value:** آی‌پی سرور (مثلاً `221.222.223.224`)

&#x202b;
۳. صبر کنید تا DNS پراپاگیت شود (معمولاً چند دقیقه تا چند ساعت)

&#x202b;
۴. برای بررسی در PowerShell:

```powershell
nslookup your.subdomain.com
```
&#x202b;
یا در Command Prompt:

```cmd
nslookup your.subdomain.com
```
&#x202b;
باید آی‌پی سرور را برگرداند.

---
&#x202b;
## مرحله ۳: اتصال به سرور از طریق SSH

&#x202b;
### روش اول: استفاده از PowerShell یا Command Prompt

```powershell
ssh root@YOUR_SERVER_IP
```

&#x202b;
اگر کلید SSH شما در مسیر پیش‌فرض نیست:

```powershell
ssh -i C:\Users\YourName\.ssh\your_key_name root@YOUR_SERVER_IP
```

---
&#x202b;
### روش دوم: استفاده از PuTTY

&#x202b;
۱. برنامه **PuTTY** را باز کنید

&#x202b;
۲. در قسمت **Host Name** آی‌پی سرور را وارد کنید

&#x202b;
۳. پورت را روی **22** بگذارید

&#x202b;
۴. در منوی سمت چپ به **Connection > SSH > Auth > Credentials** بروید

&#x202b;
۵. در قسمت **Private key file for authentication** فایل `.ppk` خود را انتخاب کنید

&#x202b;
۶. به **Session** برگردید و روی **Open** کلیک کنید

&#x202b;
۷. در پنجره ترمینال، نام کاربری `root` را وارد کنید

---

&#x202b;
## مرحله ۴: آپدیت سرور

&#x202b;
پس از اتصال به سرور:

```bash
apt update && apt upgrade -y
```

---
&#x202b;
## مرحله ۵: نصب Hysteria2 با Let's Encrypt

```bash
bash <(curl -sL https://bit.ly/realityez) --transport hysteria2 --security letsencrypt --server your.subdomain.com
```
&#x202b;
> **نکته:** جایگزین `your.subdomain.com` با ساب‌دامین واقعی خود کنید.

&#x202b;
پس از اتمام نصب، کانفیگ و QR کد نمایش داده می‌شود. آن را ذخیره کنید.

---
&#x202b;
## مرحله ۶: ری‌استارت سرویس‌ها

```bash
bash <(curl -sL https://bit.ly/realityez) -r
```

---
&#x202b;
## مدیریت کاربران

&#x202b;
### لیست کاربران

```bash
bash <(curl -sL https://bit.ly/realityez) --list-users
```

&#x202b;
### اضافه کردن کاربر جدید

```bash
bash <(curl -sL https://bit.ly/realityez) --add-user USERNAME
```

&#x202b;
### نمایش کانفیگ و QR کد کاربر

```bash
bash <(curl -sL https://bit.ly/realityez) --show-user USERNAME
```

&#x202b;
### حذف کاربر

```bash
bash <(curl -sL https://bit.ly/realityez) --delete-user USERNAME
```

---
&#x202b;
## دستورات مفید

| عملیات | دستور |
|--------|-------|
| منوی تنظیمات | `bash <(curl -sL https://bit.ly/realityez) -m` |
| ری‌استارت سرویس | `bash <(curl -sL https://bit.ly/realityez) -r` |
| نمایش کانفیگ سرور | `bash <(curl -sL https://bit.ly/realityez) --show-server-config` |
| بکاپ گرفتن | `bash <(curl -sL https://bit.ly/realityez) --backup` |
| بازیابی بکاپ | `bash <(curl -sL https://bit.ly/realityez) --restore URL_OR_FILE` |
| حذف کامل | `bash <(curl -sL https://bit.ly/realityez) -u` |

---
&#x202b;
## اپلیکیشن‌های کلاینت

&#x202b;
کانفیگ یا QR کد را در یکی از اپلیکیشن‌های زیر وارد کنید:

| پلتفرم | اپلیکیشن‌های پیشنهادی |
|--------|----------------------|
| **iOS** | Hiddify, Shadowrocket, FoXray, Stash |
| **Android** | Hiddify, v2rayNG, NekoBox |
| **Windows** | Hiddify, v2rayN, NekoRay, Furious |
| **macOS** | Hiddify, NekoRay, Furious |
| **Linux** | NekoRay, Furious |

---
&#x202b;
## عیب‌یابی

&#x202b;
### سرویس کار نمی‌کند

&#x202b;
بررسی وضعیت کانتینرها:

```bash
docker ps
```

&#x202b;
بررسی لاگ‌ها:

```bash
docker logs reality-ezpz-engine-1 --tail 50
docker logs reality-ezpz-certbot-1 --tail 30
```

&#x202b;
### مشکل گواهی SSL

&#x202b;
مطمئن شوید ساب‌دامین به آی‌پی سرور اشاره می‌کند.

&#x202b;
در PowerShell:

```powershell
nslookup your.subdomain.com
```

&#x202b;
سپس در سرور، سرویس را ری‌استارت کنید:

```bash
bash <(curl -sL https://bit.ly/realityez) -r
```

&#x202b;
### پورت UDP باز نیست

&#x202b;
بررسی کنید که سرویس روی پورت ۴۴۳ گوش می‌دهد:

```bash
ss -ulnp | grep 443
```

&#x202b;
اگر فایروال فعال است:

```bash
ufw allow 443/udp
```

&#x202b;
### بررسی فایروال Hetzner

&#x202b;
اگر در Hetzner فایروال فعال کرده‌اید:

&#x202b;
۱. وارد [Hetzner Cloud Console](https://console.hetzner.cloud/) شوید
۲. به قسمت **Firewalls** بروید
۳. یک قانون جدید اضافه کنید:
   - **Direction:** Inbound
   - **Protocol:** UDP
   - **Port:** 443
   - **Source:** Any (0.0.0.0/0)

---
&#x202b;
## منابع

- [GitHub پروژه اصلی](https://github.com/aleskxyz/reality-ezpz)
- [دانلود PuTTY](https://www.putty.org/)
- [بررسی پراپاگیت شدن DNS](https://dnschecker.org)

---
&#x202b;
موفق باشید! 🎉
