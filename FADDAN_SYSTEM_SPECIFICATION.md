# توثيق ومواصفات نظام فدّان للروبوتات الزراعية الذكية
## Faddan AI Robotics — Autonomous Under-Canopy Spot Spraying & Inspection System
### صوبة فراولة #1 (12 صف × 100 متر) — Strawberry Greenhouse #1 (12 Rows x 100m)

---

## 1. نظرة عامة على النظام والمعمارية الأساسية (System Architecture & Identity)

* **اسم المشروع:** فدّان | Faddan AI Robotics
* **التطبيق الميداني:** صوبة فراولة تجارية رقم 1 بمساحة تشمل 12 خط زراعي بطول 100 متر لكل صف.
* **الهدف التقني:** فحص أسفل عريشة النبات (Under-Canopy Inspection)، واكتشاف الأمراض باستخدام نماذج التقطيع البصري (YOLOv11-seg)، والرش الموجه فائق الدقة بالمللي للبؤر المصابة فقط (Spot Spraying)، وحساب المحصول بالكيلوجرام، وضمان مطابقة معايير التصدير الدولي (GlobalG.A.P) عبر قفل أمان فترة ما قبل الحصاد (PHI).
* **معمارية العمل بدون إنترنت (Offline-First Architecture):**
  1. نقطة اتصال لاسلكية خارجية (2.4GHz Outdoor Access Point) مثبتة بأول الصوبة لتغطية الـ 12 صفاً محلياً دون الحاجة لشريحة SIM أو اتصال بالإنترنت.
  2. ضغط صور الأوراق المصابة المكتشفة تلقائياً إلى حجم تقريبي (~90KB) مع وسم الإحداثيات (رقم الصف والمسافة بالمتر).
  3. تخزين محلي كامل في ذاكرة الروبوت والمحطة الميدانية، مع توفير زر مزامنة سحابية (Cloud Sync) عند عودة المهندس لمكتب المزرعة.
* **التحكم بالواجهة وتجربة المستخدم (UI & UX Settings):**
  * **دعم ثنائي اللغة:** العربية والإنجليزية مع التبديل التلقائي لاتجاه الواجهة (`dir="rtl"` و `dir="ltr"`).
  * **نمطا العرض:**
    * وضع الشمس الساطعة الميداني (Daylight Mode) للاستخدام المباشر تحت الشمس.
    * الوضع الليلي الداكن (Dark Mode) لعروض المستثمرين والتحليل المكتبي.
  * **زر الطوارئ الميداني الدائم (Persistent E-Stop):** متاح في أعلى الواجهة طوال الوقت لإيقاف المواتير وطلمبة الضخ فوراً عند الخطر.

---

## 2. شريط الحالة اللحظي الدائم (Persistent Top Status Telemetry)

يحتوي الشريط العلوي على المؤشرات الحرجة فقط لمنع التشتيت الميداني:
1. **وضع الاتصال (Connection Pill):**
   * متصل محلياً عبر شبكة الصوبة: `Local Wi-Fi 2.4GHz (No Internet)`.
   * متصل بالسحابة بعد المزامنة: `Cloud Synced (Office Network)`.
2. **الموقع اللحظي للروفر (Rover Real-Time Location):**
   * رقم الصف الحالي + المسافة بالمتر: `الصف 4 (عند متر 63.2)`.
3. **مستوى البطارية (Battery Indicator):**
   * بطارية LiFePO4: النسبة المئوية اللحظية (`84%`).
4. **مستوى تانك المبيد (Tank Level):**
   * النسبة المئوية والسعة اللترية: `68% (10.2 لتر)`.
5. **زر إيقاف الطوارئ (Emergency Stop):**
   * تفعيل / إلغاء القفل الحركي والهيدروليكي الكامل للروبوت.

---

## 3. الشاشات الأساسية الأربعة (Core MVP Screens)

### شاشة 1: ملخص الصوبة والعائد المالي (Executive Overview & ROI Summary)
* **المسار:** `mvp-overview`
* **الفئة المستهدفة:** صاحب المزرعة، المدير الزراعي، المستثمر.
* **المكونات والوظائف:**
  1. **حاسبة التوفير الاستثمارية التفاعلية (Interactive ROI Calculator):**
     * مدخلات متغيرة:
       * عدد الصوب الزراعية (1 إلى 20 صوبة — افتراضي: 1).
       * عدد رشات الموسم (6 إلى 30 رشة — افتراضي: 18 رشة).
       * سعر لتر المبيد المركز (افتراضي: 1,850 جنيه مصري).
     * منطق الحساب البرمجي:
       * الرش التقليدي الغمر: 15 لتر محلول مخفف لكل صوبة في الرشة.
       * رش فدّان الذكي: 3.2 لتر محلول مخفف لكل صوبة في الرشة.
       * حجم التوفير اللتري = `(15 - 3.2) × عدد الرشات × عدد الصوب`.
       * التوفير المالي (جنيه مصري) = `حجم التوفير اللتري × (سعر اللتر المركز ÷ 9.2)`.
  2. **البطاقات الرقمية الرئيسية (4 Hero KPI Cards):**
     * **نسبة التوفير الكيميائي والمالي:** 78.6% توفير فعلي مع حساب المبلغ الإجمالي الموفر سنوياً.
     * **مؤشر صحة الصوبة العام:** 93.6% (+4.2% تحسن)، 32 قطاع سليم من أصل 36 قطاعاً.
     * **حالة المسح اليومي:** 12 / 12 صف مكتمل، 13 بؤرة إصابة تم رشها بدقة، زمن المسح 48 دقيقة (بسرعة 0.4 م/ث).
     * **الكمية الفعلية المستهلكة:** 3.2 لتر مقارنة بـ 15.0 لتر بالرش التقليدي (متوسط الرشة 12 مل للورقة المصابة)، مطابقة لمعيار متبقيات المبيدات الصفرية (Zero-Residue).
  3. **الرسم البياني لمقارنة الاستهلاك صَفّاً بصَفّ (Row-by-Row mL Usage Bar Chart):**
     * أعمدة مزدوجة لكل صف من الصفوف الـ 12:
       * العمود الرمادي: الرش التقليدي الثابت (1250 مل لكل صف).
       * العمود الأخضر/البرتقالي: رش فدّان الفعلي بالمللي حسب الإصابة.
     * وسام علوي دائم يوضح نسبة التوفير المئوية (`-Saved%`) والشطب على الكمية القديمة وإظهار الكمية الفعلية بالمللي.
     * نافذة منبثقة تفاعلية عند مرور مؤشر الفأرة (Hover Tooltips).
  4. **مخطط توزيع الأمراض المكتشفة (Detected Diseases Donut Chart):**
     * إجمالي 18 بؤرة إصابة مكتشفة:
       * عفن رمادي (Botrytis Gray Mold): 52% (9 بؤر) — كود لوني: وردي محمر (#f43f5e).
       * بياض دقيقي (Powdery Mildew): 33% (6 بؤر) — كود لوني: كهرماني (#fbbf24).
       * عنكبوت أحمر (Spider Mites): 15% (3 بؤر) — كود لوني: سماوي (#38bdf8).

---

### شاشة 2: مخطط الصوبة الحقيقي وصور الإصابات والحرارة (100m Map, AI Snapshots & Micro-Climate)
* **المسار:** `mvp-rows`
* **المكونات والوظائف:**
  1. **مخطط مسار الصفوف الـ 12 بطول 100 متر (Linear 100m Schematic):**
     * مسارات خطية بمقياس حقيقي من 0 متر إلى 100 متر مع علامات إرشادية عند (20م، 40م، 60م، 80م، 100م).
     * تمثيل موقع الروبوت المباشر: أيقونة `🤖` عند موقعه الفعلي (الصف 4 عند 63.2م).
     * نقاط ملونة تمثل الإصابات المكتشفة حسب الفلترة:
       * أحمر (Critical): إصابة نشطة وبؤرة كثيفة.
       * أصفر (Warning): اشتباه مبكر أو رطوبة عالية.
       * أزرق (Treated): تم الرش بنجاح بمكرو-جرعة محددة.
     * أوسمة التحقق البصري المباشرة فوق كل نقطة على الخريطة:
       * علامة `✓`: تشخيص تم تأكيده من المهندس.
       * علامة `✗`: تشخيص تم تصحيحه من المهندس.
  2. **لوحة فحص الورقة والتحقق من الذكاء الاصطناعي (Pinpoint & AI Inspector):**
     * عرض صورة الورقة المصابة مع قناع التقطيع اللوني (YOLOv11-seg Mask) وإمكانية إخفائه/إظهاره.
     * بطاقة بيانات النقطة: نسبة ثقة الموديل (e.g. 96.4%)، وقت الالتقاط، حجم الصورة المضغوطة (KB)، والجرعة المرشوشة بالمللي.
     * **حلقة تقييم المهندس المباشرة (Inline Agronomist Feedback):**
       * زر "تشخيص صح ✓": يعتمد التشخيص ويضيف علامة `✓` للنقطة على الخريطة لتدريب الموديل.
       * زر "تشخيص غلط ✗": يفتح قائمة التصنيفات البديلة:
         1. ورقة سليمة (انعكاس ضوء)
         2. نقص كالسيوم / احتراق حواف
         3. بياض دقيقي
         4. عنكبوت أحمر
       * خيار تعديل التقييم في أي وقت.
  3. **خرائط المناخ الدقيق التفاعلية (Micro-Climate Heatmaps):**
     * تعمل بنظام تتبع حركة الفأرة اللحظي (`onMouseMove`) وتستخرج الإحداثيات (الصف، المتر، القيمة المحسوبة).
     * **خريطة درجات الحرارة (°C):**
       * تمثيل بياني يُظهر بؤرة حرارية مرتفعة (33.8°C) في أعلى اليسار (الصفوف 1-3 بين 0م و25م).
       * مقياس لوني من 22°C (معتدل) إلى 35°C (إجهاد حراري).
     * **خريطة الرطوبة النسبية (%):**
       * تمثيل بياني يُظهر بؤرة رطوبة حرجة (89.5%) في أسفل اليمين (الصفوف 10-12 بين 75م و100م) محفزة للعفن الرمادي.
       * مقياس لوني من 55% (مثالي) إلى 90%+ (خطر فطري).
     * **التنبيهات الإجرائية المكانية الثلاثة (Spatial Climate Alerts):**
       * تنبيه 1: فتح الستارة الجانبية الشمالية وتشغيل مروحة التبريد #1.
       * تنبيه 2: زيادة التهوية وتقليل الري بالتنقيط بنهاية الصفوف 10-12.
       * تنبيه 3: منتصف الصوبة في حالة مناخية مثالية لعقد ثمار الفراولة.

---

### شاشة 3: التحكم الميداني والمزامنة المحلية (Quick Control & Offline Sync)
* **المسار:** `mvp-control`
* **المكونات والوظائف:**
  1. **أزرار الأوامر الميدانية الكبيرة (Touch-Friendly Mission Controls):**
     * بدء / استئناف المسح (Start / Resume).
     * إيقاف مؤقت للروفر (Pause Rover).
     * العودة التلقائية لقاعدة الشحن والملء (Return to Dock).
  2. **صندوق المزامنة المحلية مع السحابة (Local-to-Cloud Sync Engine):**
     * عداد الصور المعلقة بانتظار الرفع (مثال: 14 صورة).
     * شريط تقدم مرئي لعملية المزامنة (15% -> 100%).
     * زر استخراج التقرير الرسمي بصيغة PDF.
  3. **البث الحي للكاميرا عند الطلب (On-Demand Local Video Stream):**
     * وضع التوفير الافتراضي (Smart Snapshot Mode): الكاميرا تبث صوراً ثابتة عند رصد الإصابة فقط (~90KB) لتوفير البطارية وشبكة الواي فاي.
     * وضع البث المباشر: تشغيل تغذية الفيديو الحية (28 FPS) عبر شبكة الصوبة المحلية بضغطة زر عند الحاجة لمعاينة النبات بالعين المجردة.

---

### شاشة 4: توقع الحصاد وقفل أمان التصدير (Yield Forecast & PHI Safety Lock)
* **المسار:** `mvp-harvest-phi`
* **المكونات والوظائف:**
  1. **مؤشرات الحصاد التراكمية:**
     * الفراولة الحمراء الناضجة الجاهزة للجمع: 486 كجم (~24,300 ثمرة).
     * الثمار الوردية والخضراء (دفعة الأسبوع القادم): 864 كجم (390 كجم وردي + 474 كجم أخضر).
     * الصفوف المغلقة آلياً ضد الرش (PHI Locked): عدد الصفوف المغلقة من أصل 12 صفاً.
     * مطابقة اشتراطات التصدير الدولية (GlobalG.A.P): نسبة أمان 100% لخلو المحصول من متبقيات المبيدات.
  2. **مصفوفة الصفوف الـ 12 الموحدة (Unified 12-Row Ripeness & Safety Grid):**
     * بطاقة مخصصة لكل صف تحتوي على:
       * اسم الصف والعداد التنازلي لموعد الجمع بالأيام (`الجمع بعد X أيام`).
       * إجمالي وزن المحصول المتوقع بالصف.
       * شريط لوني مكدس يوضح نسب: الأحمر (ناضج)، الوردي (قيد النضج)، والأخضر.
       * أوزان كل مرحلة بالكيلوجرام.
       * **قفل أمان الحصاد (Pre-Harvest Interval Lock):**
         * تفعيل القفل التلقائي للرش الكيميائي لأي صف يتبقى على حصاده 3 أيام أو أقل.
         * زر تحكم يدوي للتبديل بين `مغلق PHI` (Lock) و `مفتوح` (Unlock).

---

## 4. شاشات تشخيص الأعطال والإضافات (Diagnostics & Add-ons)

### شاشة 5: التشخيص الذكي لأعطال الهاردوير والحركة (Hardware Fault Center)
* **المسار:** `opt-hardware`
* **المكونات والوظائف:**
  1. **شريط الإنذار الرئيسي للأعطال النشطة:**
     * يوضح عدد الأعطال المكتشفة وموقع العطل (الصف والمتر) والإجراء المطلوب من الفني، مع زر حل العطل (`تم الإصلاح ✓`).
  2. **المخطط اللحظي لشاسيه الروفر رباعي الدفع (4WD Chassis & Sprayer Visualizer):**
     * مقارنة سرعة دوران العجلات (Wheel RPM) بالحركة الفعلية المقاسة عبر الـ Visual Odometry والـ GPS لكشف انزلاق العجلات فوراً.
     * مراقبة المحركات الأربعة المستقلة (FL, RL, FR, RR): سحب التيار بالأمبير (A)، وسرعة الدوران (RPM)، وحالة حساسات القاعة (Hall Sensors).
     * وحدة المعالجة المركزية (Jetson Orin CPU)، ونسبة البطارية، وحالة نظام الضخ.
     * **أزرار اختبار تسليك النوزل بالنبضات (200ms Purge Test):** أزرار لاختبار النوزل 1، 2، و3 بنبضة ضخ قصيرة للتأكد من تدفق السائل.
  3. **حالات الأعطال الخمسة المدعومة برمجياً (5 Real-Time Anomaly Scenarios):**
     1. **انحشار الروفر / دوران العجلات في المكان (Rover Stuck Anomaly):**
        * المنطق: المواتير تدور بسرعة 410 RPM بينما قراءة الموقع ثابتة عند 63.2م لأكثر من 5 ثوانٍ.
        * الاستجابة: فصل المواتير تلقائياً لحماية مشغلات الـ VESC من الاحتراق أو الحفر في التربة.
     2. **بطارية حرجة (12%) مع تعذر العودة لقاعدة الشحن (Critical Battery Trapped):**
        * المنطق: انخفاض البطارية تحت 15% وفشل العودة التلقائية للشاحن لوجود عائق في الصف 4.
     3. **عطل بموتور الدفع الخلفي يمين (Single Motor Failure):**
        * المنطق: سحب تيار 0.0A وتوقف قراءة الـ RPM في الموتور RR مع خطأ Hall Sensor، واستمرار عمل 3 مواتير.
     4. **انسداد النوزل أو عطل البمب بالتحقق البصري (Nozzle Clog via Camera):**
        * المنطق: أمر ضخ صادر لصمام السولينويد #2 لمدة 350ms، لكن كاميرا الفحص لم ترصد رذاذاً أو لمعاناً مائياً على الورقة.
     5. **مراقبة الحرارة وحساس المسافة (Thermal Throttling & Obstacle Watchdog):**
        * المنطق: ارتفاع حرارة معالج الـ Jetson فوق 78°C أو انقطاع إشارة حساس تفادي العوائق الأمامي (ToF Sensor).

---

### شاشة 6: مراجعة الصور المشكوك فيها (AI Low-Confidence Active Learning)
* **المسار:** `opt-ai-hil`
* **المكونات والوظائف:**
  * طابور مخصص لعرض صور الأوراق التي كانت نسبة ثقة الذكاء الاصطناعي بها أقل من 75%.
  * بطاقات تفصيلية توضح: الصف والمتر، تخمين الموديل، نسبة الثقة، وملاحظات بيئة الالتقاط (مثل وهج الشمس أو صغر البقعة).
  * خيارات اتخاذ القرار:
    * زر `تأكيد التشخيص`: لإدراجه ضمن بيانات التدريب المؤكدة.
    * زر `تصحيح (نقص عناصر/كالسيوم)`: لتعديل الفئة وتصحيح انحراف النموذج.

---

## 5. نموذج نافذة تقرير التصدير الموثق (Export Compliance Certificate Modal)

* **المسمى الرسمي:** تقرير الرش الذكي الموثق للتصدير (GlobalG.A.P Compliance Audit Log).
* **بيانات الاعتماد:**
  * رقم الصوبة: صوبة فراولة #1 — إجمالي 12 صفاً.
  * تاريخ المسح وسجل الروبوت.
  * نسبة التخفيض الكيميائي المحققة: 78.6%.
  * إجمالي الكمية المرشوشة: 3.2 لتر.
  * حالة متبقيات المبيدات: آمن للتصدير بنسبة 100% (Zero-Residue Safe).
* **جدول تدقيق المعاملات الفردية:**
  * رقم الصف | موقع القطاع | المسبب المرضي المكتشف ونسبة الثقة | الكمية المصروفة بالمللي.
* **خيارات الإجراء:** إغلاق العرض، أو تحميل ملف الـ PDF الرسمي المعتمد.

---

## 6. قواعد البيانات وهياكل البيانات الحقيقية المضمنة (Mock Data Structures)

### جدول نقاط الإصابة (21 نقطة فحص على خطوط الـ 100 متر):
```javascript
const INITIAL_PINPOINTS = [
  { id: 'pt-1', rowId: 1, meter: 42.5, status: 'treated', diseaseAr: 'بياض دقيقي (تم الرش)', diseaseEn: 'Powdery Mildew (Treated)', confidence: 95.4, sprayed: true, ml: 10, time: '09:14 ص', sizeKb: 91, verified: 'approved', correctedLabel: '' },
  { id: 'pt-2', rowId: 2, meter: 18.0, status: 'warning', diseaseAr: 'اشتباه مبكر: عنكبوت أحمر', diseaseEn: 'Early Suspicion: Spider Mites', confidence: 71.5, sprayed: false, ml: 0, time: '09:24 ص', sizeKb: 95, verified: 'pending', correctedLabel: '' },
  { id: 'pt-3', rowId: 2, meter: 81.5, status: 'treated', diseaseAr: 'عنكبوت أحمر (تم الرش)', diseaseEn: 'Spider Mites (Treated)', confidence: 93.2, sprayed: true, ml: 10, time: '09:29 ص', sizeKb: 88, verified: 'pending', correctedLabel: '' },
  { id: 'pt-4', rowId: 3, meter: 14.2, status: 'treated', diseaseAr: 'عفن رمادي (تم الرش)', diseaseEn: 'Botrytis Gray Mold (Treated)', confidence: 97.2, sprayed: true, ml: 14, time: '09:35 ص', sizeKb: 98, verified: 'approved', correctedLabel: '' },
  { id: 'pt-5', rowId: 3, meter: 88.0, status: 'treated', diseaseAr: 'عفن رمادي (تم الرش)', diseaseEn: 'Botrytis Gray Mold (Treated)', confidence: 94.8, sprayed: true, ml: 14, time: '09:43 ص', sizeKb: 92, verified: 'pending', correctedLabel: '' },
  { id: 'pt-6', rowId: 4, meter: 31.0, status: 'treated', diseaseAr: 'بياض دقيقي (تم الرش)', diseaseEn: 'Powdery Mildew (Treated)', confidence: 94.0, sprayed: true, ml: 12, time: '10:09 ص', sizeKb: 87, verified: 'pending', correctedLabel: '' },
  { id: 'pt-7', rowId: 4, meter: 63.2, status: 'critical', diseaseAr: 'عفن رمادي نشط (بؤرة كثيفة)', diseaseEn: 'Active Botrytis Cinerea Hotspot', confidence: 96.4, sprayed: true, ml: 18, time: '10:14 ص', sizeKb: 94, verified: 'pending', correctedLabel: '' },
  { id: 'pt-8', rowId: 4, meter: 91.5, status: 'warning', diseaseAr: 'اشتباه بياض دقيقي', diseaseEn: 'Suspected Powdery Mildew', confidence: 76.2, sprayed: false, ml: 12, time: '10:17 ص', sizeKb: 89, verified: 'pending', correctedLabel: '' },
  { id: 'pt-9', rowId: 5, meter: 52.0, status: 'treated', diseaseAr: 'بياض دقيقي (تم الرش)', diseaseEn: 'Powdery Mildew (Treated)', confidence: 93.1, sprayed: true, ml: 18, time: '08:20 ص', sizeKb: 90, verified: 'approved', correctedLabel: '' },
  { id: 'pt-10', rowId: 6, meter: 77.4, status: 'treated', diseaseAr: 'عنكبوت أحمر (تم الرش)', diseaseEn: 'Spider Mites (Treated)', confidence: 92.7, sprayed: true, ml: 12, time: '08:39 ص', sizeKb: 88, verified: 'pending', correctedLabel: '' },
  { id: 'pt-11', rowId: 7, meter: 19.5, status: 'warning', diseaseAr: 'اشتباه مبكر: عفن رمادي', diseaseEn: 'Early Botrytis Suspicion', confidence: 74.0, sprayed: false, ml: 0, time: '08:45 ص', sizeKb: 91, verified: 'pending', correctedLabel: '' },
  { id: 'pt-12', rowId: 7, meter: 56.0, status: 'treated', diseaseAr: 'عفن رمادي (تم الرش)', diseaseEn: 'Botrytis (Treated)', confidence: 97.8, sprayed: true, ml: 16, time: '08:50 ص', sizeKb: 96, verified: 'pending', correctedLabel: '' },
  { id: 'pt-13', rowId: 7, meter: 84.0, status: 'critical', diseaseAr: 'بؤرة عفن رمادي شديدة', diseaseEn: 'Severe Botrytis Hotspot', confidence: 98.2, sprayed: true, ml: 22, time: '08:55 ص', sizeKb: 102, verified: 'pending', correctedLabel: '' },
  { id: 'pt-14', rowId: 8, meter: 48.0, status: 'treated', diseaseAr: 'بياض دقيقي (تم الرش)', diseaseEn: 'Powdery Mildew (Treated)', confidence: 95.0, sprayed: true, ml: 15, time: '09:04 ص', sizeKb: 93, verified: 'pending', correctedLabel: '' },
  { id: 'pt-15', rowId: 8, meter: 89.2, status: 'warning', diseaseAr: 'رطوبة عالية + اشتباه فطري', diseaseEn: 'High Humidity Fungal Risk', confidence: 78.4, sprayed: false, ml: 0, time: '09:08 ص', sizeKb: 87, verified: 'pending', correctedLabel: '' },
  { id: 'pt-16', rowId: 9, meter: 73.0, status: 'treated', diseaseAr: 'عفن رمادي (تم الرش)', diseaseEn: 'Botrytis (Treated)', confidence: 96.1, sprayed: true, ml: 16, time: '07:40 ص', sizeKb: 94, verified: 'pending', correctedLabel: '' },
  { id: 'pt-17', rowId: 10, meter: 24.5, status: 'treated', diseaseAr: 'عنكبوت أحمر (تم الرش)', diseaseEn: 'Spider Mites (Treated)', confidence: 91.8, sprayed: true, ml: 12, time: '07:45 ص', sizeKb: 89, verified: 'pending', correctedLabel: '' },
  { id: 'pt-18', rowId: 10, meter: 82.0, status: 'treated', diseaseAr: 'عفن رمادي (تم الرش)', diseaseEn: 'Botrytis (Treated)', confidence: 95.9, sprayed: true, ml: 18, time: '07:55 ص', sizeKb: 95, verified: 'pending', correctedLabel: '' },
  { id: 'pt-19', rowId: 11, meter: 44.0, status: 'warning', diseaseAr: 'اشتباه نقص كالسيوم / حواف', diseaseEn: 'Suspected Tip Burn / Calcium', confidence: 68.5, sprayed: false, ml: 0, time: '08:05 ص', sizeKb: 86, verified: 'pending', correctedLabel: '' },
  { id: 'pt-20', rowId: 11, meter: 93.5, status: 'critical', diseaseAr: 'عفن رمادي (منطقة الرطوبة العالية)', diseaseEn: 'Botrytis (High Humidity Zone)', confidence: 97.4, sprayed: true, ml: 20, time: '08:09 ص', sizeKb: 99, verified: 'pending', correctedLabel: '' },
  { id: 'pt-21', rowId: 12, meter: 79.0, status: 'treated', diseaseAr: 'بياض دقيقي (تم الرش)', diseaseEn: 'Powdery Mildew (Treated)', confidence: 94.2, sprayed: true, ml: 14, time: '08:14 ص', sizeKb: 91, verified: 'approved', correctedLabel: '' }
];
```

### جدول بيانات الصفوف الـ 12 (استهلاك المبيد، أوزان المحصول، وقفل PHI):
```javascript
const INITIAL_ROWS_DATA = [
  { id: 1, nameAr: 'الصف 1', nameEn: 'Row 1', phiLocked: true, harvestDaysLeft: 2, ripeKg: 48, turningKg: 22, greenKg: 35, traditionalLiters: 1.25, faddanLiters: 0.15 },
  { id: 2, nameAr: 'الصف 2', nameEn: 'Row 2', phiLocked: true, harvestDaysLeft: 2, ripeKg: 52, turningKg: 19, greenKg: 30, traditionalLiters: 1.25, faddanLiters: 0.10 },
  { id: 3, nameAr: 'الصف 3', nameEn: 'Row 3', phiLocked: false, harvestDaysLeft: 6, ripeKg: 34, turningKg: 38, greenKg: 42, traditionalLiters: 1.25, faddanLiters: 0.28 },
  { id: 4, nameAr: 'الصف 4', nameEn: 'Row 4', phiLocked: false, harvestDaysLeft: 6, ripeKg: 41, turningKg: 31, greenKg: 39, traditionalLiters: 1.25, faddanLiters: 0.42 },
  { id: 5, nameAr: 'الصف 5', nameEn: 'Row 5', phiLocked: false, harvestDaysLeft: 7, ripeKg: 29, turningKg: 44, greenKg: 48, traditionalLiters: 1.25, faddanLiters: 0.18 },
  { id: 6, nameAr: 'الصف 6', nameEn: 'Row 6', phiLocked: false, harvestDaysLeft: 7, ripeKg: 38, turningKg: 35, greenKg: 40, traditionalLiters: 1.25, faddanLiters: 0.12 },
  { id: 7, nameAr: 'الصف 7', nameEn: 'Row 7', phiLocked: false, harvestDaysLeft: 5, ripeKg: 45, turningKg: 28, greenKg: 33, traditionalLiters: 1.25, faddanLiters: 0.48 },
  { id: 8, nameAr: 'الصف 8', nameEn: 'Row 8', phiLocked: false, harvestDaysLeft: 5, ripeKg: 42, turningKg: 30, greenKg: 36, traditionalLiters: 1.25, faddanLiters: 0.35 },
  { id: 9, nameAr: 'الصف 9', nameEn: 'Row 9', phiLocked: false, harvestDaysLeft: 8, ripeKg: 31, turningKg: 40, greenKg: 51, traditionalLiters: 1.25, faddanLiters: 0.22 },
  { id: 10, nameAr: 'الصف 10', nameEn: 'Row 10', phiLocked: false, harvestDaysLeft: 8, ripeKg: 36, turningKg: 39, greenKg: 45, traditionalLiters: 1.25, faddanLiters: 0.30 },
  { id: 11, nameAr: 'الصف 11', nameEn: 'Row 11', phiLocked: false, harvestDaysLeft: 4, ripeKg: 47, turningKg: 25, greenKg: 31, traditionalLiters: 1.25, faddanLiters: 0.38 },
  { id: 12, nameAr: 'الصف 12', nameEn: 'Row 12', phiLocked: false, harvestDaysLeft: 4, ripeKg: 43, turningKg: 27, greenKg: 34, traditionalLiters: 1.25, faddanLiters: 0.22 }
];
```

### قائمة عناصر المراجعة البشرية المشكوك فيها (Human-in-the-Loop Queue):
```javascript
const INITIAL_HIL_ITEMS = [
  { id: 'hil-101', row: 'الصف 2 - عند متر 18.0', aiGuessAr: 'اشتباه عنكبوت أحمر (Spider Mites)', aiGuessEn: 'Suspected Spider Mites', confidence: 71.5, status: 'pending', noteAr: 'إضاءة شمس قوية على الورقة قد تشبه اصفرار العنكبوت الأحمر' },
  { id: 'hil-102', row: 'الصف 7 - عند متر 19.5', aiGuessAr: 'بداية عفن رمادي (Early Botrytis)', aiGuessEn: 'Early Botrytis Cinerea', confidence: 74.0, status: 'pending', noteAr: 'بقعة بنية صغيرة على حافة الورقة السفلية' },
  { id: 'hil-103', row: 'الصف 11 - عند متر 44.0', aiGuessAr: 'حواف محترقة / نقص كالسيوم', aiGuessEn: 'Leaf Tip Burn / Calcium Deficiency', confidence: 68.5, status: 'pending', noteAr: 'لم يتم الرش لأن الموديل رجح أنه نقص تسميد وليس فطريات' }
];
```
