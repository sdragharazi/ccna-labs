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

- **Speed و Duplex:** کارت شبکه با دستگاه مقابل مذاکره می‌کند (Auto-Negotiation). اگر یک طرف Auto و طرف دیگر دستی تنظیم شده باشد، **Duplex Mismatch** رخ می‌دهد. علامتش: شبکه کار می‌کند ولی خیلی کند است و شمارنده خطا بالا می‌رود.
- **NIC Teaming یا Bonding:** روی سرورها دو کارت شبکه را یکی می‌کنند تا هم Redundancy داشته باشند هم پهنای باند بیشتر. معادل سیسکویی سمت سوییچ همان **EtherChannel** است.

**Real-World Extension:** تیم کردن دو NIC روی سرور بدون تنظیم درست EtherChannel سمت سوییچ، یکی از رایج‌ترین دلایل قطعی‌های عجیب در دیتاسنتر است.

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
<tr><td>Collision Domain</td><td dir="auto">فقط یک دامنه برای کل دستگاه</td></tr>
<tr><td>Duplex</td><td dir="auto">اجباراً Half-Duplex و نیاز به CSMA/CD</td></tr>
<tr><td dir="auto">وضعیت امروز</td><td dir="auto">منسوخ (Obsolete)</td></tr>
</table>

<div dir="rtl">

یادآوری از جلسه ۱: توپولوژی فیزیکی Star، توپولوژی منطقی **Bus**.

**Real-World Extension:** امروز به جای Hub برای شنود ترافیک و تحلیل با Wireshark، از **SPAN Port** یا **Network TAP** استفاده می‌شود.

</div>

### 1.4 Modem

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td dir="auto">نام کامل</td><td>Modulator / Demodulator</td></tr>
<tr><td dir="auto">کار</td><td dir="auto">تبدیل سیگنال دیجیتال به سیگنال قابل حمل روی خط ISP و برعکس</td></tr>
<tr><td dir="auto">انواع</td><td>DSL Modem, Cable Modem, ONT (Fiber)</td></tr>
</table>

<div dir="rtl">

⚠️ **تله رایج:** جعبه‌ای که در خانه «مودم» صدایش می‌کنیم، در واقع دستگاه ترکیبی است: Modem و Router و Switch و Access Point در یک قوطی. اگر در مصاحبه بگویی «مودم به من IP می‌دهد» اشتباه گفته‌ای؛ **بخش Router** آن دستگاه است که DHCP و NAT انجام می‌دهد.

</div>

---

## 2. Layer 2 Devices

<div dir="rtl">

این دسته **MAC Address** را می‌خوانند و بر اساس آن تصمیم می‌گیرند.

</div>

### 2.1 Bridge

<div dir="rtl">

جد سوییچ است. دو یا چند Segment را به هم وصل می‌کرد، ولی برخلاف Hub **یاد می‌گرفت** کدام MAC در کدام طرف است و ترافیک محلی را بیخود به طرف دیگر نمی‌فرستاد.

</div>

<table>
<tr><th dir="auto">ویژگی</th><th>Bridge</th><th>Switch</th></tr>
<tr><td dir="auto">تعداد پورت</td><td dir="auto">۲ تا ۴</td><td dir="auto">۸ تا ۴۸ و بیشتر</td></tr>
<tr><td dir="auto">روش پردازش</td><td dir="auto">نرم‌افزاری (Software)</td><td dir="auto">سخت‌افزاری با تراشه ASIC</td></tr>
<tr><td dir="auto">سرعت</td><td dir="auto">کند</td><td>Wire-Speed</td></tr>
</table>

<div dir="rtl">

**Bridge امروز کجاست؟** در Linux، ماشین‌های مجازی، و مهم‌تر از همه **MikroTik**. در RouterOS برای اینکه چند پورت مثل سوییچ رفتار کنند، باید آن‌ها را عضو یک `bridge` کنی.

</div>

### 2.2 Switch

<div dir="rtl">

قلب شبکه LAN. سه کار انجام می‌دهد:

</div>

<table>
<tr><th dir="auto">عملیات</th><th dir="auto">توضیح</th></tr>
<tr><td>Learning</td><td dir="auto">MAC مبدأ هر فریم ورودی را همراه با شماره پورت در MAC Address Table ثبت می‌کند</td></tr>
<tr><td>Forwarding / Filtering</td><td dir="auto">اگر MAC مقصد در جدول باشد، فریم را فقط به همان یک پورت می‌فرستد</td></tr>
<tr><td>Flooding</td><td dir="auto">اگر MAC مقصد در جدول نباشد (Unknown Unicast) یا فریم Broadcast باشد، به همه پورت‌ها به‌جز پورت ورودی می‌فرستد</td></tr>
</table>

<table>
<tr><th dir="auto">مفهوم</th><th>Hub</th><th>Switch</th></tr>
<tr><td>Collision Domain</td><td dir="auto">یکی برای کل دستگاه</td><td dir="auto">هر پورت یکی</td></tr>
<tr><td>Broadcast Domain</td><td dir="auto">یکی</td><td dir="auto">یکی (مگر با VLAN تفکیک شود)</td></tr>
<tr><td>Duplex</td><td>Half</td><td>Full</td></tr>
<tr><td dir="auto">پهنای باند</td><td dir="auto">تقسیم‌شده (Shared)</td><td dir="auto">اختصاصی برای هر پورت (Dedicated)</td></tr>
</table>

<div dir="rtl">

⚠️ **جمله‌ای که باید حفظ باشی:** سوییچ Collision Domain را می‌شکند، ولی Broadcast Domain را **نمی‌شکند**. تنها دو چیز Broadcast Domain را می‌شکند: **VLAN** و **Router**.

</div>

### 2.3 Layer 3 Switch (Multilayer Switch)

<div dir="rtl">

سوییچی که علاوه بر کار لایه ۲، می‌تواند **بین VLANها مسیریابی** کند. با فعال کردن `ip routing` و ساختن **SVI** این کار انجام می‌شود.

**چرا وجود دارد؟** در روش Router-on-a-Stick تمام ترافیک بین VLANها باید روی یک لینک برود و برگردد و گلوگاه ایجاد می‌شود. سوییچ لایه ۳ این کار را داخل ASIC خودش با سرعت Wire-Speed انجام می‌دهد.

</div>

```
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
!
ip routing
```

<table>
<tr><th dir="auto">قابلیت</th><th>L3 Switch</th><th>Router</th></tr>
<tr><td dir="auto">مسیریابی داخلی سریع</td><td dir="auto">✅ عالی</td><td dir="auto">متوسط</td></tr>
<tr><td dir="auto">پورت WAN</td><td>❌</td><td>✅</td></tr>
<tr><td>NAT / PAT</td><td dir="auto">معمولاً ❌</td><td>✅</td></tr>
<tr><td dir="auto">VPN و Tunneling</td><td>❌</td><td>✅</td></tr>
<tr><td dir="auto">QoS پیشرفته و Shaping</td><td dir="auto">محدود</td><td>✅</td></tr>
<tr><td dir="auto">تعداد پورت</td><td dir="auto">زیاد (۲۴ تا ۴۸)</td><td dir="auto">کم (۲ تا ۴)</td></tr>
</table>

<div dir="rtl">

**قانون خلاصه:** سوییچ لایه ۳ برای داخل شبکه، روتر برای لبه شبکه.

⚠️ **مخصوص تجهیزات خودم:**

- **Catalyst 3750G** سوییچ لایه ۳ است، ولی قابلیت مسیریابی‌اش به **IOS Image و License** بستگی دارد. نسخه IP Base مسیریابی محدود دارد و برای OSPF کامل معمولاً IP Services لازم است. با `show version` بررسی می‌شود.
- **Catalyst 2960X** کاملاً لایه ۲ است و OSPF ندارد.

</div>

---

## 3. Layer 3 Devices

### 3.1 Router

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td dir="auto">چه چیزی را می‌بیند</td><td dir="auto">آدرس IP مقصد</td></tr>
<tr><td dir="auto">بر اساس چه تصمیم می‌گیرد</td><td dir="auto">Routing Table و قانون Longest Prefix Match</td></tr>
<tr><td dir="auto">مرز چیست</td><td dir="auto">مرز Broadcast Domain</td></tr>
</table>

<div dir="rtl">

**سه کار حیاتی روتر:**

1. **Path Selection:** از بین چند مسیر ممکن، بهترین را انتخاب می‌کند.
2. **توقف Broadcast:** روتر به صورت پیش‌فرض Broadcast را عبور نمی‌دهد. به همین دلیل برای DHCP بین دو شبکه به `ip helper-address` نیاز است.
3. **اتصال تکنولوژی‌های متفاوت:** یک طرف Ethernet، طرف دیگر فیبر مخابراتی یا MPLS.

**نکته مهم:** وقتی بسته از روتر عبور می‌کند، **IP مبدأ و مقصد تغییر نمی‌کند** (مگر NAT فعال باشد)، ولی **MAC مبدأ و مقصد در هر Hop عوض می‌شود**.

</div>

### 3.2 Gateway — Interview Trap

<div dir="rtl">

این کلمه **دو معنی کاملاً متفاوت** دارد:

</div>

<table>
<tr><th dir="auto">معنی</th><th dir="auto">توضیح</th><th dir="auto">مثال</th></tr>
<tr><td>Default Gateway</td><td dir="auto">آدرس IP روتری که خروجی شبکه است</td><td>192.168.1.1</td></tr>
<tr><td>Protocol Gateway</td><td dir="auto">دستگاهی که یک پروتکل را به پروتکل دیگر ترجمه می‌کند</td><td dir="auto">VoIP Gateway، Email Gateway، IoT Gateway</td></tr>
</table>

<div dir="rtl">

اگر در مصاحبه پرسیدند «Gateway چیست؟»، اول بپرس منظورشان کدام است، یا هر دو را توضیح بده.

</div>

---

## 4. Layer 4–7 Devices

### 4.1 Firewall

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td>Layer</td><td dir="auto">۳ و ۴ (سنتی)، تا ۷ (نسل جدید)</td></tr>
<tr><td dir="auto">تصمیم بر اساس</td><td>Source IP, Destination IP, Port, Protocol, State</td></tr>
<tr><td dir="auto">مفهوم کلیدی</td><td>Stateful Inspection</td></tr>
</table>

<div dir="rtl">

**Stateful یعنی چه؟** فایروال یک جدول به نام **State Table** دارد. وقتی کاربر داخلی به یک سایت وصل می‌شود، فایروال این ارتباط را ثبت می‌کند و **فقط جواب همان ارتباط** را اجازه ورود می‌دهد. لازم نیست Rule برگشت را دستی بنویسی.

</div>

<table>
<tr><th dir="auto">ویژگی</th><th dir="auto">ACL روی Router</th><th>Firewall</th></tr>
<tr><td dir="auto">حافظه ارتباط</td><td>❌ Stateless</td><td>✅ Stateful</td></tr>
<tr><td dir="auto">نیاز به Rule برگشت</td><td dir="auto">دارد</td><td dir="auto">ندارد</td></tr>
<tr><td dir="auto">مفهوم Zone (Inside / Outside / DMZ)</td><td dir="auto">ندارد</td><td dir="auto">دارد</td></tr>
<tr><td dir="auto">بازرسی محتوا (Layer 7)</td><td dir="auto">ندارد</td><td dir="auto">در NGFW دارد</td></tr>
<tr><td dir="auto">Logging و گزارش‌گیری</td><td dir="auto">ابتدایی</td><td dir="auto">پیشرفته</td></tr>
</table>

<div dir="rtl">

**Real-World Extension:** فایروال نسل جدید (NGFW) مثل Palo Alto، FortiGate یا Cisco Firepower، خود برنامه را تشخیص می‌دهد؛ مثلاً می‌فهمد ترافیک روی پورت 443 در واقع تلگرام است نه وب معمولی.

</div>

### 4.2 IDS vs IPS

<table>
<tr><th dir="auto">ویژگی</th><th>IDS</th><th>IPS</th></tr>
<tr><td dir="auto">نام کامل</td><td>Intrusion Detection System</td><td>Intrusion Prevention System</td></tr>
<tr><td dir="auto">محل قرارگیری</td><td dir="auto">Out-of-Band (کنار مسیر، روی SPAN Port)</td><td dir="auto">Inline (وسط مسیر ترافیک)</td></tr>
<tr><td dir="auto">واکنش</td><td dir="auto">فقط هشدار می‌دهد</td><td dir="auto">جلوی حمله را می‌گیرد</td></tr>
<tr><td dir="auto">اثر روی ترافیک</td><td dir="auto">صفر</td><td dir="auto">تأخیر ایجاد می‌کند</td></tr>
<tr><td dir="auto">ریسک</td><td dir="auto">حمله انجام می‌شود و بعد متوجه می‌شوی</td><td dir="auto">False Positive می‌تواند ترافیک سالم را قطع کند</td></tr>
</table>

<div dir="rtl">

**استعاره مصاحبه:** IDS مثل **دوربین مداربسته** است؛ ضبط می‌کند و آژیر می‌زند. IPS مثل **نگهبان جلوی در** است؛ جلوی ورود را می‌گیرد. و چون IPS وسط مسیر است، اگر خراب شود یا اشتباه کند، کل شبکه را می‌خواباند.

</div>

### 4.3 Proxy

<table>
<tr><th dir="auto">نوع</th><th dir="auto">جهت</th><th dir="auto">کاربرد</th></tr>
<tr><td>Forward Proxy</td><td dir="auto">از داخل به بیرون</td><td dir="auto">کنترل دسترسی کارمندان، فیلتر محتوا، Cache، مخفی کردن IP کاربران</td></tr>
<tr><td>Reverse Proxy</td><td dir="auto">از بیرون به داخل</td><td dir="auto">محافظت از سرورها، SSL Offload، توزیع بار، مخفی کردن ساختار داخلی</td></tr>
</table>

<div dir="rtl">

- **مثال Forward Proxy:** کارمندان مستقیم به اینترنت وصل نیستند؛ همه از Proxy رد می‌شوند و Log ثبت می‌شود.
- **مثال Reverse Proxy:** نرم‌افزار Nginx یا HAProxy جلوی چند وب‌سرور.

</div>

### 4.4 Load Balancer

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td dir="auto">مشکلی که حل می‌کند</td><td dir="auto">یک سرور جواب‌گوی بار نیست و خودش هم Single Point of Failure است</td></tr>
<tr><td dir="auto">روش کار</td><td dir="auto">یک IP مجازی (VIP) منتشر می‌کند و درخواست‌ها را بین چند سرور واقعی پخش می‌کند</td></tr>
<tr><td>Health Check</td><td dir="auto">مدام سلامت سرورها را چک می‌کند و سرور خراب را از چرخه خارج می‌کند</td></tr>
<tr><td dir="auto">الگوریتم‌ها</td><td>Round Robin, Least Connection, Weighted, IP Hash</td></tr>
</table>

<table>
<tr><th></th><th>L4 Load Balancer</th><th>L7 Load Balancer</th></tr>
<tr><td dir="auto">تصمیم بر اساس</td><td>IP + Port</td><td>URL, Cookie, Header</td></tr>
<tr><td dir="auto">مثال</td><td dir="auto">پخش ترافیک TCP 443</td><td dir="auto">ارسال مسیر /api به سرور A و /images به سرور B</td></tr>
<tr><td dir="auto">ویژگی</td><td dir="auto">سریع‌تر</td><td dir="auto">هوشمندتر</td></tr>
</table>

<div dir="rtl">

**نکته:** Reverse Proxy و L7 Load Balancer هم‌پوشانی زیادی دارند؛ خیلی وقت‌ها یک نرم‌افزار مثل Nginx هر دو نقش را بازی می‌کند.

</div>

---

## 5. Wireless Devices

### 5.1 Access Point (AP)

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td>Layer</td><td dir="auto">۲</td></tr>
<tr><td dir="auto">کار واقعی</td><td dir="auto">پل (Bridge) بین دنیای بی‌سیم 802.11 و دنیای سیمی 802.3</td></tr>
<tr><td dir="auto">نکته حیاتی</td><td dir="auto">مثل Hub رفتار می‌کند نه Switch؛ رسانه مشترک و Half-Duplex</td></tr>
<tr><td dir="auto">مکانیزم دسترسی</td><td dir="auto">CSMA/CA (پیشگیری از تصادم، نه تشخیص آن)</td></tr>
</table>

<table>
<tr><th dir="auto">نوع AP</th><th dir="auto">توضیح</th></tr>
<tr><td>Autonomous AP</td><td dir="auto">مستقل؛ تنظیمات روی خودش است. مناسب ۱ تا ۵ عدد</td></tr>
<tr><td>Lightweight AP</td><td dir="auto">وابسته؛ تنظیمات از Controller می‌آید. مناسب شبکه‌های بزرگ</td></tr>
</table>

### 5.2 Wireless LAN Controller (WLC)

<table>
<tr><th dir="auto">مورد</th><th dir="auto">توضیح</th></tr>
<tr><td dir="auto">مشکلی که حل می‌کند</td><td dir="auto">مدیریت تک‌تک ۲۰۰ عدد AP غیرممکن است</td></tr>
<tr><td dir="auto">پروتکل ارتباطی</td><td dir="auto">CAPWAP (تونل بین AP و Controller)</td></tr>
<tr><td dir="auto">معماری</td><td dir="auto">Split-MAC: کارهای لحظه‌ای روی AP، کارهای مدیریتی روی Controller</td></tr>
</table>

<div dir="rtl">

**سه کار کلیدی WLC:**

1. **تنظیم متمرکز:** یک بار SSID می‌سازی، روی همه APها اعمال می‌شود.
2. **RRM (Radio Resource Management):** تنظیم خودکار کانال و توان هر AP برای کاهش تداخل.
3. **Roaming نرم:** جابه‌جایی کاربر بین APها بدون قطع ارتباط.

</div>

---

## 6. Collision & Broadcast Domains

<table>
<tr><th dir="auto">دستگاه</th><th>Collision Domain</th><th>Broadcast Domain</th></tr>
<tr><td dir="auto">Hub با ۸ پورت</td><td>1</td><td>1</td></tr>
<tr><td dir="auto">Bridge با ۴ پورت</td><td>4</td><td>1</td></tr>
<tr><td dir="auto">Switch با ۲۴ پورت، بدون VLAN</td><td>24</td><td>1</td></tr>
<tr><td dir="auto">Switch با ۲۴ پورت، ۳ عدد VLAN</td><td>24</td><td>3</td></tr>
<tr><td dir="auto">Router با ۴ پورت فعال</td><td>4</td><td>4</td></tr>
<tr><td>Access Point</td><td dir="auto">1 (به ازای هر کانال)</td><td>1</td></tr>
</table>

<div dir="rtl">

**قانون خلاصه:**

- هر پورت **Switch** ← یک Collision Domain
- هر پورت **Router** یا هر **VLAN** ← یک Broadcast Domain

</div>

---

## 7. Summary Table

<table>
<tr><th dir="auto">دستگاه</th><th>Layer</th><th dir="auto">تصمیم بر اساس</th><th dir="auto">کاربرد اصلی</th></tr>
<tr><td>NIC</td><td>1-2</td><td>—</td><td dir="auto">رابط دستگاه با شبکه</td></tr>
<tr><td>Repeater</td><td>1</td><td>—</td><td dir="auto">افزایش فاصله</td></tr>
<tr><td>Media Converter</td><td>1</td><td>—</td><td dir="auto">تغییر نوع رسانه</td></tr>
<tr><td>Modem / ONT</td><td>1</td><td>—</td><td dir="auto">اتصال به ISP</td></tr>
<tr><td>Hub</td><td>1</td><td>—</td><td dir="auto">منسوخ</td></tr>
<tr><td>Bridge</td><td>2</td><td>MAC</td><td dir="auto">اتصال دو Segment</td></tr>
<tr><td>Switch</td><td>2</td><td>MAC</td><td dir="auto">هسته LAN</td></tr>
<tr><td>L3 Switch</td><td>2-3</td><td>MAC + IP</td><td dir="auto">مسیریابی سریع بین VLANها</td></tr>
<tr><td>Router</td><td>3</td><td>IP</td><td dir="auto">اتصال شبکه‌ها، مرز Broadcast</td></tr>
<tr><td>Firewall</td><td>3-7</td><td>IP + Port + State</td><td dir="auto">امنیت محیطی</td></tr>
<tr><td>IDS</td><td>3-7</td><td dir="auto">الگوی حمله</td><td dir="auto">تشخیص</td></tr>
<tr><td>IPS</td><td>3-7</td><td dir="auto">الگوی حمله</td><td dir="auto">جلوگیری</td></tr>
<tr><td>Proxy</td><td>7</td><td dir="auto">محتوا</td><td dir="auto">واسطه درخواست</td></tr>
<tr><td>Load Balancer</td><td>4-7</td><td dir="auto">IP/Port یا URL</td><td dir="auto">توزیع بار</td></tr>
<tr><td>Access Point</td><td>2</td><td>MAC</td><td dir="auto">پل بی‌سیم به سیمی</td></tr>
<tr><td>WLC</td><td>2-3</td><td>—</td><td dir="auto">مدیریت متمرکز AP</td></tr>
</table>

---

## 8. Cisco vs MikroTik

<table>
<tr><th dir="auto">مفهوم</th><th>Cisco IOS</th><th>MikroTik RouterOS</th></tr>
<tr><td dir="auto">فلسفه پورت</td><td dir="auto">پورت سوییچ به صورت پیش‌فرض عضو VLAN 1 و آماده کار</td><td dir="auto">هر پورت به صورت پیش‌فرض مستقل و ایزوله</td></tr>
<tr><td dir="auto">ساختن رفتار سوییچ</td><td dir="auto">خودکار</td><td dir="auto">باید دستی bridge بسازی و پورت‌ها را عضو کنی</td></tr>
<tr><td dir="auto">دیدن پورت‌ها</td><td><code>show ip interface brief</code></td><td><code>/interface print</code></td></tr>
<tr><td dir="auto">دیدن جدول MAC</td><td><code>show mac address-table</code></td><td><code>/interface bridge host print</code></td></tr>
<tr><td dir="auto">مسیریابی</td><td dir="auto">روی Router و L3 Switch</td><td dir="auto">همه دستگاه‌ها روتر هستند</td></tr>
</table>

<div dir="rtl">

**جمله کلیدی:** سیسکو بین «سوییچ» و «روتر» مرز سخت‌افزاری قائل است. میکروتیک همه چیز را روتر می‌بیند و رفتار سوییچ را باید با Bridge **بسازی**.

</div>

---

## 9. Common Mistakes

<table>
<tr><th dir="auto">اشتباه</th><th dir="auto">واقعیت</th></tr>
<tr><td dir="auto">«مودم به من IP می‌دهد»</td><td dir="auto">بخش Router دستگاه ترکیبی این کار را می‌کند</td></tr>
<tr><td dir="auto">«سوییچ Broadcast را متوقف می‌کند»</td><td dir="auto">فقط Collision را می‌شکند. Broadcast فقط با VLAN یا Router متوقف می‌شود</td></tr>
<tr><td dir="auto">«AP مثل سوییچ بی‌سیم است»</td><td dir="auto">مثل Hub است؛ رسانه مشترک و Half-Duplex</td></tr>
<tr><td dir="auto">«فایروال و ACL یکی هستند»</td><td dir="auto">ACL از نوع Stateless است، فایروال Stateful</td></tr>
<tr><td dir="auto">«IPS بهتر از IDS است، پس همیشه IPS»</td><td dir="auto">IPS اگر اشتباه کند، ترافیک سالم را قطع می‌کند</td></tr>
<tr><td dir="auto">«L3 Switch جای روتر را می‌گیرد»</td><td dir="auto">نه در لبه شبکه؛ WAN و NAT و VPN ندارد</td></tr>
</table>

---

## 10. Quiz

<div dir="rtl">

⚠️ **اول خودت جواب بده، بعد جواب را باز کن.** روی «جواب» کلیک کن تا باز شود.

</div>

### Q1

<div dir="rtl">

یک سوییچ ۲۴ پورته داریم که روی آن **۴ عدد VLAN** تعریف شده و **۲۰ پورت فعال** است. چند Collision Domain و چند Broadcast Domain داریم؟

</div>

<details>
<summary>جواب</summary>

<div dir="rtl">

- **Collision Domain: 20** ← هر پورت فعال سوییچ یک Collision Domain جداست.
- **Broadcast Domain: 4** ← هر VLAN یک Broadcast Domain جداست (به شرط اینکه هر ۴ VLAN پورت فعال داشته باشند).

**نکته آزمون:** بعضی منابع تعداد کل پورت‌ها (۲۴) را می‌شمارند. اگر سؤال صریحاً گفت «پورت فعال» یا «دستگاه متصل»، همان عدد را بشمار.

</div>

</details>

### Q2

<div dir="rtl">

تفاوت Forward Proxy و Reverse Proxy را با یک مثال واقعی توضیح بده.

</div>

<details>
<summary>جواب</summary>

<div dir="rtl">

- **Forward Proxy** سمت **کاربران** قرار دارد و از طرف آن‌ها به اینترنت درخواست می‌دهد. مثال: پراکسی شرکت که دسترسی کارمندان به سایت‌ها را فیلتر و Log می‌کند.
- **Reverse Proxy** سمت **سرورها** قرار دارد و از طرف آن‌ها به درخواست‌های بیرونی جواب می‌دهد. مثال: Nginx جلوی سه وب‌سرور یک فروشگاه اینترنتی.

**جمله کلیدی:** Forward از کاربر محافظت و کنترل می‌کند، Reverse از سرور.

</div>

</details>

### Q3

<div dir="rtl">

چرا می‌گوییم Access Point بیشتر شبیه Hub است تا Switch؟

</div>

<details>
<summary>جواب</summary>

<div dir="rtl">

چون همه کلاینت‌های وصل به یک کانال AP، **رسانه مشترک** (هوا) دارند:

- در هر لحظه فقط یک دستگاه می‌تواند ارسال کند ← **Half-Duplex**
- پهنای باند بین همه **تقسیم** می‌شود
- همه یک Collision Domain هستند و از **CSMA/CA** برای پیشگیری از تصادم استفاده می‌کنند

به همین دلیل با زیاد شدن کاربر روی یک AP، سرعت هر نفر افت می‌کند.

</div>

</details>

### Q4

<div dir="rtl">

یک شرکت ۳ وب‌سرور یکسان دارد. می‌خواهد بار بینشان پخش شود و اگر یکی خراب شد، کاربر متوجه نشود. چه دستگاهی نیاز دارد و آن دستگاه چطور می‌فهمد یک سرور خراب شده؟

</div>

<details>
<summary>جواب</summary>

<div dir="rtl">

**Load Balancer.**

- کاربران به یک **VIP** (Virtual IP) وصل می‌شوند، نه مستقیم به سرورها.
- Load Balancer با **Health Check** مدام سرورها را چک می‌کند؛ مثلاً Ping، باز بودن پورت TCP، یا درخواست HTTP و بررسی کد 200.
- اگر یک سرور جواب نداد، از چرخه خارج می‌شود و ترافیک بین دو سرور سالم پخش می‌شود.

</div>

</details>

### Q5

<div dir="rtl">

روی لینک بین سوییچ و سرور، Ping جواب می‌دهد ولی سرعت انتقال فایل به شدت پایین است و شمارنده خطا بالا می‌رود. محتمل‌ترین مشکل لایه ۱ و ۲ چیست؟

</div>

<details>
<summary>جواب</summary>

<div dir="rtl">

**Duplex Mismatch.**

یک طرف روی Full-Duplex و طرف دیگر روی Half-Duplex است (معمولاً چون یک طرف دستی تنظیم شده و طرف دیگر Auto).

- Ping کار می‌کند چون ترافیکش کم است.
- با ترافیک سنگین، طرف Half-Duplex مدام Collision می‌بیند.
- علائم در خروجی `show interfaces`:
  - سمت Half: شمارنده **Late Collision** بالا می‌رود
  - سمت Full: شمارنده **CRC / FCS Error** و **Runts** بالا می‌رود

**راه‌حل:** هر دو طرف یکسان تنظیم شود؛ یا هر دو Auto، یا هر دو دستی با مقدار برابر.

</div>

</details>

### Q6 — Review from Session 01

<div dir="rtl">

یک شرکت ۴ شعبه دارد که همه با **خط اختصاصی مخابرات** به هم وصل‌اند. این شبکه LAN است، WAN یا MAN؟ معیار تصمیمت چه بود؟

</div>

<details>
<summary>جواب</summary>

<div dir="rtl">

**WAN.**

کلمه کلیدی: «خط اختصاصی **مخابرات**». یعنی زیرساخت از Service Provider اجاره شده است. «اختصاصی» بودن خط یعنی پهنای باندش فقط برای ماست، **نه** اینکه مالکش ما هستیم.

**تله سؤال:** کلمه «اختصاصی» ممکن است تو را به سمت «مالکیت خودمان» ببرد. اختصاصی ≠ مالکیت.

</div>

</details>

---

## 11. Interview Question

<div dir="rtl">

**سطح: Junior / NOC**

تفاوت Switch و Router را توضیح بده. اگر یک شبکه فقط Switch داشته باشد و هیچ Router نداشته باشد، چه اتفاقی می‌افتد؟

</div>

<details>
<summary>جواب حرفه‌ای</summary>

<div dir="rtl">

**بخش اول — تفاوت:**

- **Switch** در لایه ۲ کار می‌کند، بر اساس **MAC Address** تصمیم می‌گیرد و دستگاه‌های **داخل یک شبکه** را به هم وصل می‌کند. هر پورتش یک Collision Domain است ولی Broadcast Domain را نمی‌شکند.
- **Router** در لایه ۳ کار می‌کند، بر اساس **IP Address** و Routing Table تصمیم می‌گیرد و **شبکه‌های مختلف** را به هم وصل می‌کند. هر پورتش مرز یک Broadcast Domain است.

**بخش دوم — شبکه بدون Router (بخش جداکننده کاندیدای خوب):**

- دستگاه‌هایی که **در یک Subnet** هستند، بدون مشکل با هم ارتباط دارند.
- هیچ ارتباطی بین **Subnetهای مختلف** یا **VLANهای مختلف** برقرار نمی‌شود.
- **دسترسی به اینترنت** وجود ندارد، چون Default Gateway وجود ندارد.
- کل شبکه یک Broadcast Domain بزرگ است؛ با رشد شبکه، ترافیک Broadcast زیاد می‌شود و ریسک **Broadcast Storm** بالا می‌رود.
- اگر VLAN تعریف شده باشد، هر VLAN یک جزیره ایزوله می‌شود.

**Follow-up احتمالی:** «اگر به جای Router یک سوییچ لایه ۳ بگذاریم، کدام مشکلات حل می‌شود و کدام نه؟»
جواب: ارتباط بین VLANها حل می‌شود؛ اما اتصال WAN، NAT و VPN همچنان به Router یا Firewall نیاز دارد.

</div>

</details>

---

## 12. Mistakes I Made

<div dir="rtl">

(بعد از حل Quiz، اشتباهاتم را اینجا ثبت می‌کنم.)

</div>

<table>
<tr><th>#</th><th dir="auto">اشتباه</th><th dir="auto">درست</th></tr>
<tr><td>1</td><td></td><td></td></tr>
<tr><td>2</td><td></td><td></td></tr>
</table>

---

## 13. Glossary

<table>
<tr><th>English</th><th dir="auto">فارسی</th></tr>
<tr><td>NIC</td><td dir="auto">کارت شبکه</td></tr>
<tr><td>Burned-In Address</td><td dir="auto">آدرس حک‌شده روی سخت‌افزار</td></tr>
<tr><td>Auto-Negotiation</td><td dir="auto">مذاکره خودکار سرعت و Duplex</td></tr>
<tr><td>Duplex Mismatch</td><td dir="auto">ناهماهنگی حالت ارسال و دریافت دو طرف لینک</td></tr>
<tr><td>ASIC</td><td dir="auto">تراشه سخت‌افزاری اختصاصی برای پردازش سریع</td></tr>
<tr><td>Flooding</td><td dir="auto">ارسال فریم به همه پورت‌ها</td></tr>
<tr><td>Unknown Unicast</td><td dir="auto">فریم تک‌مقصدی که مقصدش در جدول MAC نیست</td></tr>
<tr><td>SVI</td><td dir="auto">اینترفیس مجازی VLAN روی سوییچ لایه ۳</td></tr>
<tr><td>Longest Prefix Match</td><td dir="auto">انتخاب دقیق‌ترین مسیر در جدول مسیریابی</td></tr>
<tr><td>Stateful / Stateless</td><td dir="auto">با حافظه ارتباط / بدون حافظه ارتباط</td></tr>
<tr><td>False Positive</td><td dir="auto">تشخیص اشتباه ترافیک سالم به عنوان حمله</td></tr>
<tr><td>VIP (Virtual IP)</td><td dir="auto">آدرس IP مجازی Load Balancer</td></tr>
<tr><td>Health Check</td><td dir="auto">بررسی مداوم سلامت سرورها</td></tr>
<tr><td>CAPWAP</td><td dir="auto">پروتکل تونل بین AP و Controller</td></tr>
<tr><td>RRM</td><td dir="auto">مدیریت خودکار منابع رادیویی</td></tr>
<tr><td>SPAN Port</td><td dir="auto">پورت کپی ترافیک برای شنود و تحلیل</td></tr>
</table>
