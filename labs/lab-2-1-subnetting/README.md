# Lab 2-1: Subnetting Basics

## Scenario

<div dir="rtl">

یک شرکت کوچک دو بخش فعال دارد: **FANNI** (فنی، ۲۰ دستگاه) و **EDARI** (اداری، ۱۰ دستگاه). یک بخش سوم به نام **MALI** (مالی) برای آینده رزرو شده است. فضای آدرس کل `192.168.1.0/24` است و باید بین بخش‌ها تقسیم شود، به‌طوری که هر بخش Subnet جداگانه‌ی خودش را داشته باشد و آدرس هدر نرود.

همه‌ی PCها فعلاً به یک Switch وصل هستند و **هیچ Router و هیچ VLANی** در این لب وجود ندارد.

</div>

## Goal

<div dir="rtl">

- طراحی IP Plan با روش «بزرگ‌ترین بخش اول»
- پیاده‌سازی آدرس‌ها روی PCها در PNETLab
- بررسی ارتباط داخل یک Subnet و بین دو Subnet متفاوت
- فهمیدن این‌که **چرا** دو دستگاه روی یک Switch ممکن است نتوانند با هم ارتباط بگیرند

</div>

## Topology

![Topology](topology.drawio.svg)

## Devices

<table>
<tr><th>Device</th><th>Type</th><th dir="auto">نقش</th></tr>
<tr><td>SW1</td><td>Cisco vIOS-L2</td><td dir="auto">Switch لایه ۲، بدون تنظیمات خاص</td></tr>
<tr><td>PC-FANNI-1</td><td>VPCS</td><td dir="auto">کلاینت بخش فنی</td></tr>
<tr><td>PC-FANNI-2</td><td>VPCS</td><td dir="auto">کلاینت بخش فنی</td></tr>
<tr><td>PC-EDARI-1</td><td>VPCS</td><td dir="auto">کلاینت بخش اداری</td></tr>
<tr><td>PC-EDARI-2</td><td>VPCS</td><td dir="auto">کلاینت بخش اداری</td></tr>
</table>

## Cabling

<table>
<tr><th>SW1 Port</th><th>Connected To</th></tr>
<tr><td>Gi0/0</td><td>PC-FANNI-1 eth0</td></tr>
<tr><td>Gi0/1</td><td>PC-FANNI-2 eth0</td></tr>
<tr><td>Gi0/2</td><td>PC-EDARI-1 eth0</td></tr>
<tr><td>Gi0/3</td><td>PC-EDARI-2 eth0</td></tr>
</table>

## IP Addressing

<div dir="rtl">

جدول کامل Subnetها و آدرس هاست‌ها در فایل [ip-plan.md](ip-plan.md) است.

</div>

## Tasks

<div dir="rtl">

1. توپولوژی را در PNETLab مطابق نقشه بساز و کابل‌کشی کن.
2. روی هر PC آدرس IP و Mask را طبق `ip-plan.md` تنظیم کن (فعلاً بدون Gateway).
3. **قبل از Ping:** جدول Tests پایین را پر کن و نتیجه‌ی هر تست را پیش‌بینی کن (Expected).
4. تست‌ها را اجرا کن و نتیجه‌ی واقعی (Actual) را بنویس.
5. اگر نتیجه با پیش‌بینی فرق داشت، علتش را در بخش Lessons Learned بنویس.

</div>

## Tests

<table>
<tr><th>#</th><th>Source</th><th>Destination</th><th>Expected</th><th>Actual</th></tr>
<tr><td>1</td><td>PC-FANNI-1</td><td>PC-FANNI-2 (192.168.1.2)</td><td></td><td></td></tr>
<tr><td>2</td><td>PC-EDARI-1</td><td>PC-EDARI-2 (192.168.1.34)</td><td></td><td></td></tr>
<tr><td>3</td><td>PC-FANNI-1</td><td>PC-EDARI-1 (192.168.1.33)</td><td></td><td></td></tr>
<tr><td>4</td><td>PC-EDARI-2</td><td>PC-FANNI-2 (192.168.1.2)</td><td></td><td></td></tr>
</table>

## Verification Commands

VPCS:

```
show ip
ping 192.168.1.2
show arp
```

SW1:

```
show mac address-table
show interfaces status
```

## Troubleshooting Objectives

<div dir="rtl">

- از روی خروجی `show ip` اشتباه در IP یا Mask را پیدا کنی.
- از روی `show mac address-table` بفهمی Switch کدام PCها را دیده است.
- تفاوت «Switch فریم را رساند» با «Host اصلاً فریم را نفرستاد» را تشخیص بدهی.

</div>

## Review Questions

<div dir="rtl">

1. چرا FANNI به `/27` و EDARI به `/28` نیاز دارد؟
2. چرا بخش بزرگ‌تر را اول تخصیص دادیم؟ اگر EDARI اول تخصیص داده می‌شد چه مشکلی پیش می‌آمد؟
3. همه‌ی PCها روی یک Switch هستند. در این لب چند **Broadcast Domain** و چند **Subnet** داریم؟
4. وقتی PC-FANNI-1 می‌خواهد به `192.168.1.33` پینگ کند، قبل از ارسال هر بسته‌ای چه تصمیمی می‌گیرد؟
5. برای این‌که FANNI و EDARI با هم ارتباط بگیرند، چه دستگاه یا تنظیمی لازم است؟

</div>

## Lessons Learned

<div dir="rtl">

_(بعد از انجام لب پر شود)_

</div>
