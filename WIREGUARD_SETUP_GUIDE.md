# WireGuard Setup Instructions
# برای نصب و استفاده از کانفیگ‌های WireGuard

## نصب WireGuard

### Ubuntu/Debian
```bash
sudo apt-get update
sudo apt-get install wireguard wireguard-tools
```

### CentOS/RHEL
```bash
sudo dnf install wireguard-tools
```

## کانفیگ‌های موجود

### 1. wireguard-us-de-config.conf (ترکیبی)
- 🇺🇸 2 سرور آمریکایی
- 🇩🇪 2 سرور آلمانی
- بر روی یک اتصال

### 2. wireguard-us-dedicated.conf (اختصاصی آمریکا)
- 🇺🇸 آمریکا Primary + Backup
- سرعت بالا و پایدار

### 3. wireguard-de-dedicated.conf (اختصاصی آلمان)
- 🇩🇪 آلمان Primary + Backup
- سرعت بالا و پایدار

## روند نصب و استفاده

### مرحله 1: کپی کردن فایل کانفیگ
```bash
sudo cp wireguard-us-dedicated.conf /etc/wireguard/wg0.conf
sudo chmod 600 /etc/wireguard/wg0.conf
```

### مرحله 2: فعال کردن WireGuard
```bash
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
```

### مرحله 3: بررسی اتصال
```bash
sudo wg show
ip addr show wg0
```

## تغییرات مورد نیاز

### 1. Private Key
```bash
# کلید خصوصی خود را تولید کنید
wg genkey | tee privatekey | wg pubkey > publickey
```

### 2. Public Keys
- `US_SERVER_1_PUBLIC_KEY` - کلید عمومی سرور 1 آمریکا
- `US_SERVER_2_PUBLIC_KEY` - کلید عمومی سرور 2 آمریکا
- `DE_SERVER_1_PUBLIC_KEY` - کلید عمومی سرور 1 آلمان
- `DE_SERVER_2_PUBLIC_KEY` - کلید عمومی سرور 2 آلمان

### 3. Endpoints
جایگزین کنید:
- `us-server-1.example.com` → IP یا دومین واقعی
- `us-server-2.example.com` → IP یا دومین واقعی
- `de-server-1.example.com` → IP یا دومین واقعی
- `de-server-2.example.com` → IP یا دومین واقعی

## ویژگی‌های کانفیگ

✅ **بلند‌مدت پایدار**
- PersistentKeepalive 15 ثانیه
- هیچ قطع اضطراری

✅ **سرعت بالا**
- Dual server failover
- Load balancing خودکار
- MTU بهینه (1420)

✅ **DNS امن**
- Google DNS (8.8.8.8)
- Cloudflare DNS (1.1.1.1)
- OpenDNS (208.67.222.222)

## فرمان‌های مفید

### شروع اتصال
```bash
sudo wg-quick up wg0
```

### متوقف کردن اتصال
```bash
sudo wg-quick down wg0
```

### مشاهده وضعیت
```bash
sudo wg show
sudo wg show interfaces
sudo wg show wg0 peers
```

### مشاهده ترافیک
```bash
sudo wg show wg0 transfer
```

### تغییر کانفیگ
```bash
sudo wg set wg0 peer <pubkey> endpoint <ip:port>
```

## نکات مهم

⚠️ **PersistentKeepalive: 15**
- برای اتصالات بلند‌مدت
- تنها وصل نگه می‌دارد

⚠️ **AllowedIPs**
- تعیین کننده ترافیکی که از peer عبور می‌کند
- `0.0.0.0/0` = تمام ترافیک

⚠️ **DNS**
- برای حل‌کردن دومین‌ها
- ترتیب مهم است

## حل مشاکل

### اتصال قطع می‌شود
```bash
# KeepAlive را افزایش دهید
sudo wg set wg0 peer <pubkey> persistent-keepalive 25
```

### سرعت کم
```bash
# MTU را کاهش دهید
ip link set wg0 mtu 1280
```

### DNS کار نمی‌کند
```bash
# DNS دستی تنظیم کنید
resolvectl query-types A example.com
```
