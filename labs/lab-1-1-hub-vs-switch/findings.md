# Lab 1-1 — Findings: Hub vs Switch

<div dir="rtl">

**تاریخ:** ۲۹ سپتامبر ۲۰۲۶ &nbsp;|&nbsp; **ابزار:** Packet Tracer — حالت Simulation و Realtime &nbsp;|&nbsp; **وضعیت:** انجام شد ✅

</div>

---

## 1. Summary — خلاصه

<div dir="rtl">

در این لب یک ping ساده را یک بار روی Hub و یک بار روی Switch، قدم‌به‌قدم در حالت Simulation دنبال کردم تا فرق رفتار این دو دستگاه را با چشم خودم ببینم.

**نتیجه‌ی اصلی در یک جمله:** Hub هیچ‌وقت یاد نمی‌گیرد و همیشه به همه می‌فرستد؛ Switch فقط بار اول مجبور به پخش است، ولی از همان ترافیک یاد می‌گیرد و از آن به بعد فقط به مقصد می‌فرستد.

</div>

## 2. Topology Recap

```
Part A (Hub)                      Part B (Switch)
PC0 192.168.1.11 ─┐               PC3 192.168.1.21 ── Fa0/1 ─┐
PC1 192.168.1.12 ─┼─ Hub0         PC4 192.168.1.22 ── Fa0/2 ─┼─ Switch0 (2960)
PC2 192.168.1.13 ─┘               PC5 192.168.1.23 ── Fa0/3 ─┘

All /24, no gateway. Part A and Part B are NOT connected.
```

---

## 3. Part A — Hub: Step by Step

<div dir="rtl">

سناریو: PC0 به PC1 ping زد. قبل از شروع، جدول ARP کامپیوتر PC0 با `arp -d` پاک شد تا ARP مجبور به ظاهر شدن باشد.

</div>

<table>
<tr><th>#</th><th>Packet</th><th>From</th><th dir="auto">&rlm;Hub به کجا فرستاد</th><th dir="auto">توضیح</th></tr>
<tr><td>1</td><td>ARP Request</td><td>PC0</td><td>PC1 + PC2</td><td dir="auto">&rlm;PC0 پرسید «صاحب 192.168.1.12 کیست؟» — مقصد FFFF.FFFF.FFFF یعنی Broadcast</td></tr>
<tr><td>2</td><td>ARP Reply</td><td>PC1</td><td>PC0 + PC2</td><td dir="auto">&rlm;PC1 جواب را فقط برای PC0 نوشت (Unicast)، ولی Hub باز به همه فرستاد</td></tr>
<tr><td>3</td><td>ICMP Echo Request</td><td>PC0</td><td>PC1 + PC2</td><td dir="auto">همان ping اصلی که پشت ARP در صف منتظر بود</td></tr>
<tr><td>4</td><td>ICMP Echo Reply</td><td>PC1</td><td>PC0 + PC2</td><td dir="auto">جواب ping، باز هم پخش شد</td></tr>
</table>

<div dir="rtl">

**نتیجه:** PC2 در یک ping ساده **چهار بار** frame گرفت که هیچ‌کدام به او ربطی نداشت.

**ضربدر قرمز روی PC2** یعنی بسته را گرفت، بررسی کرد، دید مال او نیست و Drop کرد. ولی مجبور شد بگیرد و بررسی کند؛ وقت و پهنای باند هدر رفت.

**چرا Hub این‌طور است؟** چون Hub دستگاه لایه ۱ است. اصلاً «frame» و «آدرس» نمی‌بیند؛ فقط سیگنال الکتریکی را از یک پورت می‌گیرد و روی همه‌ی پورت‌های دیگر تکرار می‌کند. توانایی تصمیم‌گیری ندارد.

</div>

### Real-World Extension — Drop در کدام لایه؟

<div dir="rtl">

ضربدر در دو حالت در لایه‌های مختلف اتفاق می‌افتد. در ARP Request چون مقصد Broadcast است، کارت شبکه‌ی PC2 آن را قبول می‌کند و بعد پردازش ARP می‌بیند Target IP مال او نیست. ولی در بسته‌های Unicast، خود کارت شبکه چون MAC مقصد مال خودش نیست، همان در لایه ۲ آن را دور می‌ریزد.

</div>

### Why Hubs Are Obsolete

<div dir="rtl">

- **محیط مشترک:** همه‌ی دستگاه‌ها روی یک «سیم منطقی» هستند ← یک **Collision Domain**
- اگر دو دستگاه هم‌زمان بفرستند ← **Collision** (همان که صبح در بخش A دیدیم)
- چون هر لحظه فقط یکی می‌تواند حرف بزند ← اجباراً **Half-Duplex** و استفاده از **CSMA/CD**
- &rlm;Switch هر پورت را جدا کرد ← هر پورت یک Collision Domain مستقل ← **Full-Duplex**

**فرمول به خاطر سپردن:** Hub یعنی محیط مشترک · Switch یعنی محیط جدا

</div>

---

## 4. Part B — Switch: Step by Step

<div dir="rtl">

سناریو: PC3 به PC4 ping زد. قبل از شروع، جدول MAC سوییچ با `clear mac address-table dynamic` پاک شد.

</div>

<table>
<tr><th>#</th><th>Packet</th><th>From</th><th dir="auto">&rlm;Switch به کجا فرستاد</th><th dir="auto">&rlm;Switch چه یاد گرفت</th></tr>
<tr><td>1</td><td>ARP Request</td><td>PC3</td><td>PC4 + PC5 (Flood)</td><td dir="auto">&rlm;MAC کامپیوتر PC3 پشت پورت Fa0/1 است</td></tr>
<tr><td>2</td><td>ARP Reply</td><td>PC4</td><td dir="auto"><b>فقط PC3</b></td><td dir="auto">&rlm;MAC کامپیوتر PC4 پشت پورت Fa0/2 است</td></tr>
<tr><td>3</td><td>ICMP Echo Request</td><td>PC3</td><td dir="auto"><b>فقط PC4</b></td><td dir="auto">—</td></tr>
<tr><td>4</td><td>ICMP Echo Reply</td><td>PC4</td><td dir="auto"><b>فقط PC3</b></td><td dir="auto">—</td></tr>
</table>

<div dir="rtl">

**نتیجه:** PC5 فقط یک بار درگیر شد (ARP Request اول). بعد از آن هیچ ترافیکی به او نرسید.

</div>

### چرا اولین ARP روی Switch هم پخش شد؟

<div dir="rtl">

دو دلیل:

1. **مقصد Broadcast بود** (FFFF.FFFF.FFFF). سوییچ Broadcast را طبق طراحی به همه‌ی پورت‌ها به جز پورت ورودی می‌فرستد. این باگ نیست، قاعده است.
2. **جدول MAC خالی بود.** حتی اگر بسته Unicast بود، سوییچ نمی‌دانست PC4 کجاست. به این حالت **Unknown Unicast Flooding** می‌گویند.

</div>

### چرا جواب PC4 فقط به PC3 رفت؟

<div dir="rtl">

چون وقتی ARP Request از PC3 وارد پورت Fa0/1 شد، سوییچ **Source MAC** آن را خواند و ثبت کرد. پس وقتی جواب Unicast با مقصد MAC کامپیوتر PC3 آمد، سوییچ می‌دانست فقط باید به Fa0/1 بفرستد.

**قانون:** سوییچ از **Source MAC** یاد می‌گیرد، یعنی از فرستادن؛ نه از دریافت کردن.

</div>

### کدام Domain تفکیک شد؟

<div dir="rtl">

- &rlm;Switch **Collision Domain** را تفکیک می‌کند ← هر پورت یک Collision Domain
- &rlm;Switch **Broadcast Domain** را تفکیک **نمی‌کند** ← هر سه کامپیوتر هنوز در یک Broadcast Domain هستند
- برای تفکیک Broadcast Domain به **Router** یا **VLAN** نیاز است (جلسات بعدی)

**جمله‌ی کلیدی:** Hub **نمی‌تواند** یاد بگیرد · Switch **موقتاً نمی‌داند**

</div>

---

## 5. Hub vs Switch — Comparison

<table>
<tr><th dir="auto">ویژگی</th><th>Hub</th><th>Switch</th></tr>
<tr><td dir="auto">لایه‌ی OSI</td><td dir="auto">لایه ۱ (Physical)</td><td dir="auto">لایه ۲ (Data Link)</td></tr>
<tr><td dir="auto">خواندن آدرس</td><td dir="auto">نمی‌خواند</td><td dir="auto">&rlm;MAC را می‌خواند</td></tr>
<tr><td dir="auto">رفتار با Broadcast</td><td dir="auto">به همه</td><td dir="auto">به همه (Flood)</td></tr>
<tr><td dir="auto">رفتار با Unicast شناخته‌شده</td><td dir="auto">به همه</td><td dir="auto"><b>فقط به پورت مقصد</b></td></tr>
<tr><td dir="auto">رفتار با Unknown Unicast</td><td dir="auto">به همه</td><td dir="auto">به همه، تا وقتی یاد بگیرد</td></tr>
<tr><td dir="auto">یادگیری</td><td dir="auto">ندارد</td><td dir="auto">&rlm;MAC Table از روی Source MAC</td></tr>
<tr><td dir="auto">تعداد Collision Domain</td><td dir="auto">یکی برای کل Hub</td><td dir="auto">یکی برای هر پورت</td></tr>
<tr><td dir="auto">تعداد Broadcast Domain</td><td dir="auto">یکی</td><td dir="auto">یکی (بدون VLAN)</td></tr>
<tr><td>Duplex</td><td>Half</td><td>Full</td></tr>
</table>

---

## 6. ARP Table vs MAC Table

<div dir="rtl">

این دو جدول را امروز چند بار با هم قاطی کردم. اسمشان شبیه است ولی دو کار کاملاً متفاوت دارند.

</div>

<table>
<tr><th></th><th>ARP Table</th><th>MAC Address Table</th></tr>
<tr><td dir="auto">کجاست</td><td dir="auto">روی کامپیوتر (و Router)</td><td dir="auto">روی Switch</td></tr>
<tr><td dir="auto">چه می‌گوید</td><td dir="auto">&rlm;<b>این IP</b> مال <b>این MAC</b> است</td><td dir="auto">&rlm;<b>این MAC</b> پشت <b>این پورت</b> است</td></tr>
<tr><td dir="auto">کارش</td><td dir="auto">ترجمه‌ی آدرس منطقی به آدرس فیزیکی</td><td dir="auto">دانستن مسیر</td></tr>
<tr><td dir="auto">&rlm;IP می‌بیند؟</td><td dir="auto">بله</td><td dir="auto">نه</td></tr>
<tr><td dir="auto">چطور پر می‌شود</td><td dir="auto">&rlm;با ARP Request و ARP Reply</td><td dir="auto">&rlm;از Source MAC فریم‌های ورودی</td></tr>
<tr><td dir="auto">دستور دیدن</td><td><code>arp -a</code></td><td><code>show mac address-table</code></td></tr>
<tr><td dir="auto">دستور پاک کردن</td><td><code>arp -d</code></td><td><code>clear mac address-table dynamic</code></td></tr>
</table>

<div dir="rtl">

**یادآوری:** هر کامپیوتر MAC **خودش** را همیشه می‌داند، چون روی NIC حک شده. چیزی که نمی‌داند، MAC **دستگاه مقابل** است. ARP برای همین است.

**هدف ARP کمک به کامپیوتر است، نه سوییچ.** سوییچ فقط در حاشیه‌ی این رفت‌وبرگشت یاد می‌گیرد.

</div>

---

## 7. The ARP Rule — قانون ARP

```
PC needs to send to 192.168.1.22
        │
        ▼
Is 192.168.1.22 in my ARP Table?
        │
        ├── YES → build frame with that MAC → send directly
        │
        └── NO  → send ARP Request (Broadcast)
                  → wait for ARP Reply (Unicast)
                  → save in ARP Table
                  → build frame → send
```

<div dir="rtl">

**به زبان ساده:** اول جدول خودم را نگاه می‌کنم ← اگر آدرس بود، مستقیم می‌فرستم ← اگر نبود، ARP می‌زنم.

**تست:** بعد از یک ping موفق، اگر بلافاصله دوباره ping بزنی، هیچ ARP‌ای نمی‌بینی؛ چون آدرس در ARP Table هست.

**ترتیب واقعی یک ping وقتی MAC مقصد را نداریم:**

1. ping ساخته می‌شود
2. سیستم می‌بیند MAC مقصد را ندارد و ping در صف می‌ماند
3. &rlm;ARP Request فرستاده می‌شود
4. &rlm;ARP Reply می‌رسد و در ARP Table ذخیره می‌شود
5. &rlm;frame ساخته می‌شود و ping ارسال می‌شود

**پیش‌نمایش جلسات بعد:** اگر مقصد در Subnet دیگری باشد، کامپیوتر برای MAC خود مقصد ARP نمی‌زند؛ برای MAC **Default Gateway** ARP می‌زند.

</div>

---

## 8. ARP Request vs Gratuitous ARP

<div dir="rtl">

معمای صبح (جدول MAC قبل از هر ping پر بود) با Gratuitous ARP حل شد. امروز یک بار این دو را قاطی کردم.

</div>

<table>
<tr><th></th><th>ARP Request</th><th>Gratuitous ARP (GARP)</th></tr>
<tr><td dir="auto">نوع پیام</td><td dir="auto"><b>سؤال:</b> «صاحب این IP کیست؟»</td><td dir="auto"><b>اعلام:</b> «من این‌جا هستم»</td></tr>
<tr><td>Sender IP</td><td dir="auto">&rlm;IP خودم</td><td dir="auto">&rlm;IP خودم</td></tr>
<tr><td>Target IP</td><td dir="auto">&rlm;IP <b>دستگاه دیگر</b></td><td dir="auto">&rlm;IP <b>خودم</b>، یعنی Sender و Target یکی هستند</td></tr>
<tr><td dir="auto">کی فرستاده می‌شود</td><td dir="auto">وقتی MAC مقصد را ندارم</td><td dir="auto">وقتی IP عوض می‌شود یا Interface بالا می‌آید</td></tr>
<tr><td dir="auto">هدف</td><td dir="auto">پیدا کردن MAC</td><td dir="auto">&rlm;Duplicate IP Detection و Announcement</td></tr>
</table>

---

## 9. MAC Aging — جدول MAC موقت است

<div dir="rtl">

**اتفاقی که افتاد:** وسط Simulation جدول MAC سه ورودی داشت، ولی بعد از اینکه Play تا آخر رفت، `show mac address-table` خالی بود.

**علت:** جدول MAC حافظه‌ی **موقت** است. هر ورودی به طور پیش‌فرض حدود **۳۰۰ ثانیه** می‌ماند و اگر در این مدت ترافیکی از آن MAC نیاید، خودش پاک می‌شود. به این **Age Out** می‌گویند. در Simulation زمان مجازی زیادی گذشته بود.

**تأیید:** در حالت Realtime به هر سه کامپیوتر ping زدم و بلافاصله جدول را دیدم؛ هر سه MAC ثبت شده بودند ✅

**درس برای کار NOC:** جدول MAC **عکس لحظه** است، نه سند دائمی. موقع عیب‌یابی، اول ترافیک بساز و **بلافاصله** جدول را ببین. خالی بودن جدول به‌تنهایی نشانه‌ی خرابی نیست.

</div>

```
Switch# show mac address-table                 ! full table
Switch# show mac address-table dynamic         ! learned entries only
Switch# show mac address-table aging-time      ! default 300 sec
Switch# clear mac address-table dynamic        ! clear learned entries
```

---

## 10. Packet Tracer Tips — نکات محیط

<div dir="rtl">

- **پاکت بسته** در نوار بالا یعنی Add Simple PDU · **پاکت باز** یعنی Create Complex PDU
- اول روی **فرستنده** کلیک کن، بعد روی **گیرنده**
- **قبل از اجرا** در لیست پایین، ستون‌های Source و Destination را چک کن
- دکمه‌ی **Capture/Forward** فقط یک قدم جلو می‌رود · دکمه‌ی **Play** یک‌جا تا آخر می‌رود
- دکمه‌ی **Reset Simulation** فقط زمان را صفر می‌کند؛ PDU‌های قبلی را **پاک نمی‌کند** و باید دستی حذف شوند
- گزینه‌ی **Edit Filters** فقط **نمایش** را فیلتر می‌کند، نه ضبط را
- ضربدر قرمز روی دستگاه یعنی بسته Drop شد
- بخش A و بخش B به هم وصل نیستند؛ ping از PC1 به PC4 جواب نمی‌دهد
- برای بزرگ کردن صفحه: کلید Ctrl همراه با اسکرول ماوس

**شروع تمیز یک تست:**

1. حذف PDU‌های قبلی از لیست پایین
2. دستور `arp -d` روی کامپیوترها
3. دستور `clear mac address-table dynamic` روی سوییچ
4. زدن Reset Simulation

</div>

---

## 11. Mistakes I Made — اشتباهات من

<table>
<tr><th>#</th><th dir="auto">اشتباه من</th><th dir="auto">درست</th></tr>
<tr><td>1</td><td dir="auto">&rlm;NIC لایه ۱ است پس سیگنالش به همه می‌رسد و سوییچ از همین یاد می‌گیرد</td><td dir="auto">پخش به همه رفتار Hub است؛ سیگنال لایه ۱ آدرس MAC ندارد؛ سوییچ از Source MAC فریم لایه ۲ یاد می‌گیرد</td></tr>
<tr><td>2</td><td dir="auto">&rlm;Collision را Mismatch نامیدم</td><td dir="auto">&rlm;Collision یعنی رویداد برخورد سیگنال · Duplex Mismatch یعنی خطای تنظیم که <b>باعث</b> Collision می‌شود</td></tr>
<tr><td>3</td><td dir="auto">«چون Hub به PC0 فرستاد، Collision شد»</td><td dir="auto">ترتیب برعکس است: اول تصادم (علت)، بعد پخش سیگنال خراب (نتیجه). ستون Time ترتیب را نشان می‌دهد</td></tr>
<tr><td>4</td><td dir="auto">&rlm;ARP برای این است که سوییچ MAC را در جدولش ذخیره کند</td><td dir="auto">&rlm;ARP برای کامپیوتر است تا MAC مقصد را پیدا کند؛ سوییچ فقط در حاشیه یاد می‌گیرد</td></tr>
<tr><td>5</td><td dir="auto">&rlm;ARP Request را با Gratuitous ARP قاطی کردم («اعلام می‌کنم IP و MAC من این است»)</td><td dir="auto">&rlm;ARP Request یک <b>سؤال</b> است؛ GARP یک <b>اعلام</b> است</td></tr>
<tr><td>6</td><td dir="auto">پیش‌بینی کردم سوییچ اولین ARP را فقط به PC4 می‌دهد</td><td dir="auto">&rlm;ARP Request از نوع Broadcast است و سوییچ آن را Flood می‌کند؛ ضمناً جدولش خالی بود</td></tr>
<tr><td>7</td><td dir="auto">گفتم سوییچ بر اساس Broadcast Domain تفکیک می‌کند</td><td dir="auto">سوییچ Collision Domain را تفکیک می‌کند، نه Broadcast Domain</td></tr>
<tr><td>8</td><td dir="auto">&rlm;MAC Table و ARP Table را جابه‌جا گفتم</td><td dir="auto">&rlm;ARP Table روی کامپیوتر است و IP را به MAC ربط می‌دهد · MAC Table روی سوییچ است و MAC را به پورت ربط می‌دهد</td></tr>
<tr><td>9</td><td dir="auto">«کامپیوتر همیشه اول ARP می‌فرستد»</td><td dir="auto">فقط وقتی MAC مقصد در ARP Table نباشد</td></tr>
<tr><td>10</td><td dir="auto">اتفاقی را که هنوز ندیده بودم، مثل اتفاق‌افتاده تعریف کردم</td><td dir="auto">اول <b>پیش‌بینی</b>، بعد <b>مشاهده</b>، بعد مقایسه. این عادت اصلی کار NOC است</td></tr>
<tr><td>11</td><td dir="auto">گفتم از PC1 به PC4 ping بزنم</td><td dir="auto">این دو در دو بخش جدا هستند که به هم وصل نیستند</td></tr>
<tr><td>12</td><td dir="auto">گفتم پاکت بسته یک frame می‌فرستد</td><td dir="auto">پاکت بسته یک ping از نوع ICMP در لایه ۳ می‌سازد؛ frame بسته‌بندی لایه ۲ آن است</td></tr>
</table>

---

## 12. Self-Test — خودآزمایی

<div dir="rtl">

اول خودت جواب بده، بعد روی سؤال کلیک کن.

<details>
<summary>۱. چرا Hub جواب ARP را که Unicast بود، به PC2 هم فرستاد؟</summary>

چون Hub لایه ۱ است و اصلاً آدرس نمی‌خواند. هر سیگنالی را روی همه‌ی پورت‌ها به جز پورت ورودی تکرار می‌کند.

</details>

<details>
<summary>۲. اولین ARP Request روی Switch چرا به PC5 هم رسید؟ دو دلیل.</summary>

یک: مقصدش Broadcast بود و سوییچ Broadcast را Flood می‌کند. دو: جدول MAC سوییچ خالی بود.

</details>

<details>
<summary>۳. سوییچ از کدام آدرس یاد می‌گیرد؟</summary>

از Source MAC فریمی که روی یک پورت وارد می‌شود.

</details>

<details>
<summary>۴. بلافاصله بعد از یک ping موفق، دوباره ping می‌زنیم. ARP می‌بینیم؟</summary>

نه. آدرس در ARP Table هست، پس مستقیم frame ساخته و فرستاده می‌شود.

</details>

<details>
<summary>۵. جدول MAC سوییچ خالی است. یعنی سوییچ خراب است؟</summary>

نه لزوماً. ممکن است ورودی‌ها Age Out شده باشند؛ پیش‌فرض ۳۰۰ ثانیه است. اول ترافیک بساز و بلافاصله دوباره نگاه کن.

</details>

<details>
<summary>۶. سوییچ کدام Domain را تفکیک می‌کند و کدام را نه؟</summary>

&rlm;Collision Domain را تفکیک می‌کند، یعنی هر پورت یکی. Broadcast Domain را نه؛ برای آن Router یا VLAN لازم است.

</details>

**سؤال باز برای جلسه‌ی بعد (بدون جواب؛ اول خودم تلاش کنم):**

- &rlm;Switch0 آدرس MAC کامپیوتر PC3 را می‌شناسد، ولی ARP Table خود PC3 خالی است. آیا این تناقض است؟ چرا؟

</div>

---

## 13. Next Steps

<div dir="rtl">

- [ ] جواب سؤال باز بالا
- [ ] &rlm;**Real-World Extension:** تکرار همین سناریو در PNETLab با سوییچ واقعی IOS و دیدن ARP با Wireshark، با فیلترهای `arp` و `arp.isgratuitous`
- [ ] &rlm;Quiz جلسه‌ی ۲ و سؤال مصاحبه‌ی آن
- [ ] جلسه‌ی ۳ — OSI و TCP/IP

</div>
