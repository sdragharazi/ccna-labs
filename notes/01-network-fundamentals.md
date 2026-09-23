# 01 — Network Fundamentals

## Objective

Understand what a network is, how networks are classified, the difference between
physical and logical topology, and why networks are segmented.

---

## Network Types

<table>
<thead>
<tr><th>Type</th><th>Full Name</th><th>Scope</th><th>Ownership</th><th>Example</th></tr>
</thead>
<tbody>
<tr><td>PAN</td><td>Personal Area Network</td><td dir="auto">چند متر</td><td dir="auto">فرد</td><td dir="auto">گوشی + هدفون بلوتوث</td></tr>
<tr><td>LAN</td><td>Local Area Network</td><td dir="auto">یک ساختمان یا طبقه</td><td dir="auto">سازمان</td><td dir="auto">شبکه سیمی یک دفتر</td></tr>
<tr><td>WLAN</td><td>Wireless LAN</td><td dir="auto">مثل LAN، روی 802.11</td><td dir="auto">سازمان</td><td dir="auto">وای‌فای دفتر</td></tr>
<tr><td>CAN</td><td>Campus Area Network</td><td dir="auto">چند ساختمان مجاور در یک محوطه</td><td dir="auto">سازمان (فیبر اختصاصی خودش)</td><td dir="auto">دانشگاه، بیمارستان</td></tr>
<tr><td>MAN</td><td>Metropolitan Area Network</td><td dir="auto">یک شهر</td><td dir="auto">معمولاً Service Provider، گاهی خود سازمان (Private MAN)</td><td dir="auto">Metro Ethernet بین شعب یک شهر</td></tr>
<tr><td>WAN</td><td>Wide Area Network</td><td dir="auto">بین شهرها یا کشورها</td><td dir="auto">Service Provider</td><td dir="auto">اتصال شعب با MPLS یا اینترنت</td></tr>
</tbody>
</table>

<div dir="rtl">

**Key rule — دو محور جدا:**

1. **مالکیت** ← اگر زیرساخت یا سرویس از Service Provider اجاره شده باشد ← **WAN**
2. **گستره** ← در شبکه‌های خصوصی: یک ساختمان = **LAN**، محوطه = **CAN**، شهر = **MAN**

</div>

<table>
<thead>
<tr><th dir="auto">سناریو</th><th dir="auto">جواب</th><th dir="auto">کلمه کلیدی</th></tr>
</thead>
<tbody>
<tr><td dir="auto">دو ساختمان مجاور + فیبر خود شرکت</td><td>CAN</td><td dir="auto">«خود شرکت» + «مجاور»</td></tr>
<tr><td dir="auto">همان دو ساختمان + خط اجاره‌ای مخابرات</td><td>WAN</td><td dir="auto">«اجاره‌ای»</td></tr>
<tr><td dir="auto">دو ساختمان با ۴۰ km فاصله + فیبر خود شرکت</td><td>Private MAN</td><td dir="auto">«مالک» + «۴۰ km»</td></tr>
</tbody>
</table>

<div dir="rtl">

**روش حل سؤال:** اول کلمه کلیدی صورت سؤال را پیدا کن (مالک کیست؟ فاصله چقدر؟)، بعد قانون را اعمال کن.

**Demarcation Point:**

نقطه‌ای که مسئولیت ISP تمام و مسئولیت مشتری شروع می‌شود.
معمولاً پورت خروجی مودم، ONT یا NTU.
در Troubleshooting اولین سؤال این است: مشکل قبل از Demarc است یا بعد از آن؟

</div>

---

## Physical vs Logical Topology

<table>
<thead>
<tr><th>Device</th><th>Physical</th><th>Logical</th></tr>
</thead>
<tbody>
<tr><td>Hub (8 ports)</td><td>Star</td><td dir="auto">Bus</td></tr>
<tr><td>Switch (8 ports)</td><td>Star</td><td dir="auto">Star (هر پورت ارتباط مستقل)</td></tr>
</tbody>
</table>

<div dir="rtl">

**Why:**

- **Physical** = شکل واقعی کابل‌کشی. در هر دو حالت همه کابل‌ها به یک دستگاه مرکزی می‌روند ← Star.
- **Logical** = مسیر واقعی حرکت داده.
  - Hub سیگنال را روی **همه** پورت‌ها تکرار می‌کند، پس ۸ دستگاه انگار روی یک کابل مشترک‌اند ← **Bus**
    - یک Collision Domain، Half-Duplex، پهنای باند **تقسیم‌شده** بین همه
    - مثال: هاب 100 Mbps با ۴ دستگاه فعال ← هر کدام **کمتر از 25 Mbps** (به‌خاطر Collision و ارسال مجدد)
  - Switch فریم را فقط به پورت مقصد می‌فرستد ← هر پورت یک Collision Domain جدا، Full-Duplex، پهنای باند اختصاصی

**نکته کلیدی:** با عوض کردن Hub به Switch، توپولوژی فیزیکی عوض نمی‌شود؛ فقط توپولوژی منطقی عوض می‌شود.

</div>

---

## Topologies

<table>
<thead>
<tr><th>Topology</th><th dir="auto">Advantage</th><th dir="auto">Disadvantage</th><th dir="auto">Used today</th></tr>
</thead>
<tbody>
<tr><td>Star</td><td dir="auto">عیب‌یابی آسان، خرابی یک کابل فقط یک دستگاه را قطع می‌کند</td><td dir="auto">دستگاه مرکزی SPOF است</td><td dir="auto">✅ لایه Access همه LANها</td></tr>
<tr><td>Bus</td><td dir="auto">ارزان، کابل کم</td><td dir="auto">یک قطعی کل شبکه را می‌خواباند، تصادم زیاد</td><td dir="auto">❌ منسوخ</td></tr>
<tr><td>Ring</td><td dir="auto">ترافیک قابل پیش‌بینی</td><td dir="auto">یک قطعی حلقه را می‌شکند (مگر Dual Ring)</td><td dir="auto">⚠️ در شبکه‌های فیبر مخابراتی و صنعتی</td></tr>
<tr><td>Full Mesh</td><td dir="auto">بیشترین Redundancy</td><td dir="auto">گران، تعداد لینک زیاد</td><td dir="auto">⚠️ فقط بین تعداد کمی Core Router</td></tr>
<tr><td>Partial Mesh</td><td dir="auto">تعادل بین هزینه و Redundancy</td><td dir="auto">طراحی پیچیده‌تر</td><td dir="auto">✅ WAN و اتصال شعب</td></tr>
<tr><td>Hybrid</td><td dir="auto">ترکیب مزایای چند مدل</td><td dir="auto">مستندسازی و مدیریت سخت‌تر</td><td dir="auto">✅ تقریباً همه شبکه‌های واقعی</td></tr>
</tbody>
</table>

**Full Mesh formula:**

```
n(n-1)/2

6 routers = 6 × 5 / 2 = 15 links
```

<div dir="rtl">

(تقسیم بر ۲ چون لینک A→B همان لینک B→A است)

</div>

<table>
<thead>
<tr><th>Routers</th><th>Links</th></tr>
</thead>
<tbody>
<tr><td>4</td><td>6</td></tr>
<tr><td>6</td><td>15</td></tr>
<tr><td>10</td><td>45</td></tr>
<tr><td>20</td><td>190</td></tr>
</tbody>
</table>

---

## Single Point of Failure

<div dir="rtl">

**Definition:**

نقطه‌ای در شبکه (دستگاه، لینک، منبع برق) که اگر از کار بیفتد و جایگزینی نداشته باشد،
کل سرویس یا بخشی از شبکه قطع می‌شود.

**Example in Star topology:**

سوییچ Access مرکزی. اگر بسوزد، همه دستگاه‌های وصل به آن قطع می‌شوند.
همین‌طور یک Uplink تکی بین سوییچ Access و Core.

**Solution:**

- Redundancy: دو Uplink به دو سوییچ Core متفاوت
- EtherChannel برای تجمیع لینک‌ها (نکته: اگر هر دو سر به یک دستگاه برود، آن دستگاه همچنان SPOF است)
- Stack کردن سوییچ‌ها
- منبع تغذیه دوگانه (Dual PSU)

جمله مستندسازی: «این سوییچ Access یک Single Point of Failure است؛ پیشنهاد می‌شود Uplink دوم به سوییچ Core اضافه شود.»

</div>

---

## Network Segmentation

<div dir="rtl">

1. **Performance** — کاهش اندازه Broadcast Domain؛ هر گروه فقط Broadcast خودش را می‌شنود
2. **Security** — جداسازی گروه‌ها؛ مثلاً مهمان‌ها به سرورها دسترسی لایه ۲ ندارند
3. **Management** — مدیریت، عیب‌یابی و اعمال Policy آسان‌تر برای هر گروه
4. **Compliance** — رعایت الزامات قانونی و استانداردها (مثلاً جداسازی شبکه پرداخت در PCI-DSS)

</div>

### Example — 50-user company

<table>
<thead>
<tr><th>VLAN ID</th><th>Name</th><th dir="auto">Purpose</th></tr>
</thead>
<tbody>
<tr><td>10</td><td>USERS</td><td dir="auto">کامپیوترهای کارمندان</td></tr>
<tr><td>20</td><td>VOICE</td><td dir="auto">تلفن‌های IP</td></tr>
<tr><td>30</td><td>SERVERS</td><td dir="auto">سرورهای داخلی</td></tr>
<tr><td>99</td><td>MANAGEMENT</td><td dir="auto">مدیریت تجهیزات شبکه (SSH، SNMP)</td></tr>
<tr><td>100</td><td>GUEST</td><td dir="auto">مهمان‌ها، فقط دسترسی اینترنت</td></tr>
</tbody>
</table>

<div dir="rtl">

**Why a separate Voice VLAN:**

1. **QoS** — صدا به تأخیر (Latency) و نوسان تأخیر (Jitter) حساس است
2. **Security** — جداسازی ترافیک تلفن از کاربران و مهمان‌ها
3. **Management** — IP Plan، DHCP و عیب‌یابی جداگانه برای تلفن‌ها

**زنجیره درست:** VLAN جدا ← سوییچ ترافیک صدا را **شناسایی** می‌کند ← **QoS** به آن اولویت می‌دهد ← Latency و Jitter کمتر

(خودِ جدا کردن Broadcast Domain جلوی تأخیر را نمی‌گیرد؛ QoS می‌گیرد.)

</div>

---

## Mistakes I Made

<table>
<thead>
<tr><th>#</th><th dir="auto">اشتباه</th><th dir="auto">درست</th></tr>
</thead>
<tbody>
<tr><td>1</td><td dir="auto">برای تشخیص CAN از معیار <strong>فاصله</strong> استفاده کردم («چون مجاورند»)</td><td dir="auto">اول <strong>مالکیت</strong> را چک کن: اجاره از ISP = WAN</td></tr>
<tr><td>2</td><td dir="auto">قانون مالکیت را بلد بودم ولی کلمه «<strong>اجاره‌ای</strong>» در صورت سؤال را ندیدم و گفتم CAN</td><td dir="auto">اول کلمه کلیدی صورت سؤال را پیدا کن، بعد قانون را اعمال کن</td></tr>
<tr><td>3</td><td dir="auto">«فیبر نوری» را کلمه کلیدی گرفتم</td><td dir="auto">فیبر فقط <strong>نوع رسانه</strong> است؛ کلمه کلیدی «<strong>مالک</strong>» بود</td></tr>
<tr><td>4</td><td dir="auto">Logical Topology هاب را به «نداشتن مدیریت» ربط دادم</td><td dir="auto">سیگنال به <strong>همه</strong> پورت‌ها می‌رسد ← منطقاً <strong>Bus</strong></td></tr>
<tr><td>5</td><td dir="auto">فکر کردم Hub به همه <strong>همان پهنای باند کامل</strong> را می‌دهد</td><td dir="auto">پهنای باند <strong>تقسیم</strong> می‌شود، و به‌خاطر Collision حتی کمتر از سهم مساوی</td></tr>
<tr><td>6</td><td dir="auto">دلیل اصلی Voice VLAN را امنیت، و مکانیزمش را جدا کردن Broadcast Domain گفتم</td><td dir="auto">دلیل اول <strong>QoS</strong> است؛ VLAN جدا امکان شناسایی و اولویت‌دهی را فراهم می‌کند</td></tr>
</tbody>
</table>

---

## Interview Question

**Q: What is the difference between LAN and WAN?**

<div dir="rtl">

**A:**

تفاوت اصلی مالکیت و مسئولیت زیرساخت است، نه فقط فاصله.
LAN زیرساختی است که سازمان خودش مالک و مدیر آن است، معمولاً در یک ساختمان یا Campus،
با سرعت بالا و هزینه پایین به ازای هر پورت.
WAN شبکه‌ای است که مناطق جغرافیایی دور را به هم وصل می‌کند و زیرساختش از
Service Provider اجاره می‌شود؛ مثل MPLS، خط اختصاصی یا اینترنت.
مرز بین این دو را **Demarcation Point** می‌گوییم.
این تفاوت در Troubleshooting مهم است: مشکل سمت LAN را خودمان حل می‌کنیم،
مشکل بعد از Demarc را باید با ISP پیگیری کنیم.

</div>

**Follow-up: Is a VPN tunnel between two cities a LAN or a WAN?**

<div dir="rtl">

**A:**

از نظر زیرساخت، **WAN** است، چون ترافیک روی شبکه Service Provider یا اینترنت عبور می‌کند.
VPN فقط یک لایه منطقی رمزنگاری‌شده روی آن زیرساخت می‌سازد که دو شبکه را
**منطقاً** به هم وصل می‌کند، طوری که کاربران احساس کنند در یک شبکه خصوصی‌اند.
پس: زیرساخت WAN، ارتباط منطقی خصوصی.

</div>

---

## Glossary

<table>
<thead>
<tr><th>English</th><th dir="auto">فارسی</th></tr>
</thead>
<tbody>
<tr><td>Single Point of Failure</td><td dir="auto">نقطه تکی خرابی</td></tr>
<tr><td>Demarcation Point</td><td dir="auto">نقطه مرز مسئولیت (بین ISP و مشتری)</td></tr>
<tr><td>Broadcast Domain</td><td dir="auto">دامنه همه‌پخشی</td></tr>
<tr><td>Collision Domain</td><td dir="auto">دامنه تصادم</td></tr>
<tr><td>Segmentation</td><td dir="auto">تفکیک یا بخش‌بندی شبکه</td></tr>
<tr><td>Redundancy</td><td dir="auto">افزونگی</td></tr>
<tr><td>Full-Duplex / Half-Duplex</td><td dir="auto">ارسال و دریافت هم‌زمان / غیرهم‌زمان</td></tr>
<tr><td>Latency</td><td dir="auto">تأخیر</td></tr>
<tr><td>Jitter</td><td dir="auto">نوسان تأخیر</td></tr>
<tr><td>QoS (Quality of Service)</td><td dir="auto">کیفیت سرویس، اولویت‌دهی به ترافیک</td></tr>
<tr><td>Private MAN</td><td dir="auto">شبکه شهری با مالکیت خود سازمان</td></tr>
</tbody>
</table>
