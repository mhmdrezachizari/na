# WebDevHub

سامانه آموزش پروژه‌محور با Next.js و Django. سبد خرید، کد تخفیف، بارگذاری تصویر فیش و تحویل تکلیف در پروژه پیاده‌سازی شده‌اند.

## اجرای Docker

```bash
docker compose up --build
```

- رابط کاربری: http://localhost:3000
- API و مدیریت Django: http://localhost:8000

برای ساخت مدیر:

```bash
docker compose exec backend python manage.py createsuperuser
```

کدهای تخفیف و وضعیت/بازخورد تکلیف‌ها از پنل مدیریت Django قابل کنترل هستند. تنظیمات محیطی نمونه در `.env.example` قرار دارد.

## استقرار روی webdevhub.ir

پیش‌نیازها: یک سرور Ubuntu با Docker و Docker Compose، باز بودن پورت‌های 80 و 443، و اشاره‌کردن رکوردهای DNS دامنه به IP سرور.

1. برای دامنه اصلی یک رکورد `A` با نام `@` و برای `www` نیز یک رکورد `A` بسازید که هر دو به IP عمومی سرور اشاره کنند.
2. پروژه را روی سرور قرار دهید و فایل تنظیمات production را بسازید:

```bash
cp .env.production.example .env
nano .env
```

برای تولید کلید امن Django می‌توانید از این دستور استفاده کنید:

```bash
openssl rand -base64 48
```

3. سرویس‌ها را اجرا کنید:

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

4. مدیر اولیه را بسازید:

```bash
docker compose -f docker-compose.prod.yml exec backend python manage.py createsuperuser
```

Caddy به‌صورت خودکار برای `webdevhub.ir` و `www.webdevhub.ir` گواهی HTTPS می‌گیرد و آدرس `www` را به دامنه اصلی هدایت می‌کند. فایل‌های آپلودی در volume محلی `webdevhub_media_data` روی خود سرور نگهداری می‌شوند؛ بنابراین باید همراه دیتابیس از این volume نیز نسخه پشتیبان تهیه شود. برای مشاهده وضعیت و لاگ‌ها:

```bash
docker compose -f docker-compose.prod.yml ps
docker compose -f docker-compose.prod.yml logs -f --tail=100
```

برای به‌روزرسانی نسخه بعدی پروژه، فایل‌ها را به‌روز کنید و دوباره دستور مرحله 3 را اجرا کنید. داده‌های PostgreSQL، Redis، فایل‌های آپلودی و گواهی‌ها در volumeهای دائمی Docker حفظ می‌شوند.
