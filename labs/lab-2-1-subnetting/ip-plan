# IP Plan — Lab 2-1

<div dir="rtl">

فضای کل: `192.168.1.0/24`. بخش‌ها به ترتیب اندازه (بزرگ‌تر اول) تخصیص داده شده‌اند.

</div>

## Subnets

<table>
<tr><th dir="auto">بخش</th><th dir="auto">نیاز</th><th>Subnet</th><th>Mask</th><th>Network</th><th>Usable</th><th>Broadcast</th></tr>
<tr><td>FANNI</td><td dir="auto">۲۰ دستگاه</td><td>192.168.1.0/27</td><td>255.255.255.224</td><td>.0</td><td>.1 – .30</td><td>.31</td></tr>
<tr><td>EDARI</td><td dir="auto">۱۰ دستگاه</td><td>192.168.1.32/28</td><td>255.255.255.240</td><td>.32</td><td>.33 – .46</td><td>.47</td></tr>
<tr><td>MALI</td><td dir="auto">رزرو (بلاک ۱۶)</td><td>192.168.1.48/28</td><td>255.255.255.240</td><td>.48</td><td>.49 – .62</td><td>.63</td></tr>
<tr><td>Free</td><td dir="auto">آزاد</td><td>192.168.1.64 – .255</td><td>—</td><td>—</td><td>—</td><td>—</td></tr>
</table>

## Host Addresses

<table>
<tr><th>Device</th><th>IP Address</th><th>Mask</th><th>Gateway</th></tr>
<tr><td>PC-FANNI-1</td><td>192.168.1.1</td><td>/27</td><td>—</td></tr>
<tr><td>PC-FANNI-2</td><td>192.168.1.2</td><td>/27</td><td>—</td></tr>
<tr><td>PC-EDARI-1</td><td>192.168.1.33</td><td>/28</td><td>—</td></tr>
<tr><td>PC-EDARI-2</td><td>192.168.1.34</td><td>/28</td><td>—</td></tr>
</table>

## How the Subnets Were Calculated

<div dir="rtl">

روش سه‌قدمی:

1. تعداد دستگاه + ۲
2. اولین توان دو که بزرگ‌تر یا مساوی آن است ← اندازه‌ی بلاک
3. تعداد بیت هاست را از ۳۲ کم کن ← پریفیکس

</div>

```
FANNI:  20 + 2 = 22  → 32  → 5 host bits → /27
EDARI:  10 + 2 = 12  → 16  → 4 host bits → /28
```

<div dir="rtl">

- &rlm;FANNI از ۰ شروع می‌شود ← ۰ + ۳۲ = ۳۲ ← شروع EDARI
- &rlm;EDARI از ۳۲ شروع می‌شود ← ۳۲ + ۱۶ = ۴۸ ← شروع بخش بعدی

</div>

## Gateway

<div dir="rtl">

در این لب Gateway تنظیم نشده، چون Router وجود ندارد. وقتی در لب‌های بعدی Router اضافه شود، آخرین آدرس قابل استفاده‌ی هر Subnet به Gateway داده می‌شود (`.30` برای FANNI و `.46` برای EDARI) تا آدرس کامپیوترها عوض نشود.

</div>

