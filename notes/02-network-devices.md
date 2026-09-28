# 02 — Network Devices: From NIC to Load Balancer

## Objective

<div dir="rtl">

بعد از این جلسه باید بتوانم هر دستگاه شبکه را ببینم و بلافاصله بگویم در کدام **Layer** کار می‌کند، **بر اساس چه اطلاعاتی** تصمیم می‌گیرد، و **کجای** توپولوژی باید قرار بگیرد.

پیش‌نیاز: جلسه ۱ (انواع شبکه، Topology، Segmentation)

</div>

---

## The Four Questions

<div dir="rtl">

هر دستگاه شبکه را با این چهار سؤال بشناس:

1. **چه چیزی را نگاه می‌کند؟** سیگنال، MAC، IP، Port یا محتوای داده
2. **چه تصمیمی می‌گیرد؟** تکرار، سوییچ، مسیریابی، اجازه یا رد، توزیع بار
3. **مرز چه چیزی است؟** Collision Domain، Broadcast Domain، یا هیچ‌کدام
4. **اگر خراب شود چه کسی قطع می‌شود؟** دامنه خرابی (Failure Domain)

</div>

## Layer Reference

<table>
<tr><th>Layer</th><th dir="auto">تصمیم بر اساس</th></tr>
<tr><td>Layer 1</td><td dir="auto">هیچ تصمیمی نمی‌گیرد؛ فقط سیگنال را منتقل می‌کند</td></tr>
<tr><td>Layer 2</td><td>MAC Address</td></tr>
<tr><td>Layer 3</td><td>IP Address</td></tr>
<tr><td>Layer 4</td><td>Port Number (TCP / UDP)</td></tr>
<tr><td>Layer 7</td><td dir="auto">محتوای داده (URL، نام برنامه، Header)</td></tr>
</table>

---

## 1. Layer 1 Devices

<div dir="rtl">

این دسته **هیچ هوشی ندارند**. نه آدرس می‌فهمند، نه تصمیم می‌گیرند. فقط سیگنال را از یک طرف می‌گیرند و به طرف دیگر تحویل می‌دهند.

</div>

### 1.1 NIC — Network Interface Card

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td dir="auto">چیست</td><td dir="auto">کارت شبکه؛ رابط بین دستگاه و رسانه انتقال</td></tr>
<tr><td>Layer</td><td dir="auto">۱ و ۲ (سیگنال می‌سازد و آدرس MAC دارد)</td></tr>
<tr><td dir="auto">مشکلی که حل می‌کند</td><td dir="auto">تبدیل داده دیجیتال به سیگنال قابل انتقال روی کابل یا هوا</td></tr>
<tr><td dir="auto">نکته کلیدی</td><td dir="auto">آدرس MAC روی سخت‌افزار NIC حک شده (Burned-In Address)، نه روی کامپیوتر</td></tr>
</table>

<div dir="rtl">

**نکات Troubleshooting:**

- &rlm;**Speed و Duplex:** کارت شبکه با دستگاه مقابل مذاکره می‌کند (Auto-Negotiation). اگر یک طرف Auto و طرف دیگر دستی تنظیم شده باشد، **Duplex Mismatch** رخ می‌دهد. علامتش: شبکه کار می‌کند ولی خیلی کند است و شمارنده خطا بالا می‌رود.
- &rlm;**NIC Teaming یا Bonding:** روی سرورها دو کارت شبکه را یکی می‌کنند تا هم Redundancy داشته باشند هم پهنای باند بیشتر. معادل سیسکویی سمت سوییچ همان **EtherChannel** است.

&rlm;**Real-World Extension:** تیم کردن دو NIC روی سرور بدون تنظیم درست EtherChannel سمت سوییچ، یکی از رایج‌ترین دلایل قطعی‌های عجیب در دیتاسنتر است.

</div>

### 1.2 Repeater & Media Converter

<table>
<tr><th dir="auto">دستگاه</th><th dir="auto">کار</th></tr>
<tr><td>Repeater</td><td dir="auto">سیگنال ضعیف‌شده را تقویت و بازسازی می‌کند تا فاصله بیشتری برود</td></tr>
<tr><td>Media Converter</td><td dir="auto">نوع رسانه را عوض می‌کند؛ مثلاً مس (Copper) به فیبر (Fiber)</td></tr>
</table>

<div dir="rtl">

هر دو **کاملاً کور** هستند. اگر بین دو سوییچ یک Media Converter خراب شود، ممکن است سوییچ‌ها لینک را Up ببینند ولی ترافیک عبور نکند. این یکی از سخت‌ترین سناریوهای Troubleshooting لایه فیزیکی است.

</div>

### 1.3 Hub

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td>Layer</td><td dir="auto">۱</td></tr>
<tr><td dir="auto">رفتار</td><td dir="auto">هر سیگنال ورودی را عیناً روی همه پورت‌های دیگر تکرار می‌کند</td></tr>
<tr><
