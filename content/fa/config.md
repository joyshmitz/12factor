## III. پیکربندی‌ها
### پیکربندی‌ها را در محیط ذخیره کنید

*پیکربندی* یک برنامه هر چیزی است که احتمالاً بین [استقرار](./codebase) های گوناگون (آزمون، عملیات و محیط‌های توسعه‌دهنده و غیره)، متفاوت است که می‌تواند شامل موارد زیر باشد:

* مشخصات اتصال به پایگاه‌داده، Memcached، و سایر [سرویس‌های پشتیبان](./backing-services)
* موارد محرمانه مربوطه به سرویس‌های بیرونی مانند آمازون S3 یا توییتر
* مقادیر ویژه‌ی هر محیط مانند نام میزبان استاندارد برای استقرار

گاهی اوقات برنامه‌ها پیکربندی را به صورت ثابت در کد ذخیره می‌کنند. این مورد ناقص اصول دوازده-سازه است که مستلزم جداسازی دقیق پیکربندی از کد ** است. پیکربندی به طور قابل توجهی در بین استقرارها متفاوت است، در حالی که در مخزن کد، این‌گونه نیست.
a
یک آزمون تورنسل برای اینکه آیا یک برنامه تمام تنظیمات را به درستی از کد خارج کرده است یا خیر، این است که آیا مخزن کد می تواند در هر لحظه منبع باز شود، بدون اینکه هیچ محرمانگی از برنامه به خطر بیفتد؟

توجه داشته باشید که این تعریف از "پیکربندی" پیکربندی داخلی برنامه را **شامل نمی‌شود**، مانند "config/routes.rb" در Rails، یا نحوه اتصال [ماژول‌های کد](Spring/docs/current/spring-framework-reference/html/beans.html) در [Spring](http://spring.io/). این نوع پیکربندی در بین استقرارها متفاوت نیست، و بنابراین بهتر است در متن کد انجام شود.

روش دیگر برای پیکربندی، استفاده از فایل‌های پیکربندی است که در سیستم کنترل نسخه بررسی نمی‌شوند، مانند «config/database.yml» در Rails. این یک پیشرفت بزرگ نسبت به استفاده از مقادیر ثابتی است که در مخزن کد رهگیری نمی‌شوند، اما همچنان دارای نقاط ضعفی است: ممکن است به اشتباه در کنترل نسخه ثب و رهگیری شود، این تمایل وجود دارد که فایل‌های پیکربندی در مکان‌ها و قالب‌های مختلف پراکنده شوند، که دیدن و مدیریت تمام تنظیمات در یک مکان را دشوار می‌کند. علاوه بر این، این قالب‌ها معمولاً مختص زبان و یا یک چارچوب ویژه هستند.

**پیکربندی برنامه دوازده-سازه در *متغیرهای محیطی*** (اغلب به صورت *env vars* یا *env* کوتاه می‌شود). متغیرهای محیطی به راحتی بین محیط‌ها بدون تغییر هیچ بخشی از کد قابل تغییر هستند. بر خلاف فایل‌های پیکربندی، احتمال کمی وجود دارد که به طور تصادفی در مخزن کد بررسی شوند و برخلاف فایل‌های پیکربندی سفارشی، یا مکانیزم‌های پیکربندی دیگر مانند System Properties در جاوا، یک استاندارد زبان برنامه‌نویسی و سیستم عامل هستند.

یکی دیگر از جنبه های مدیریت پیکربندی، گروه‌بندی است. گاهی اوقات برنامه‌ها پیکربندی دسته‌ای را در گروه‌های نام‌گذاری‌شده (اغلب «environments» نامیده می‌شوند) با نام‌گذاری‌های خاص، مانند محیط‌های `test`، `development` و `production` در Rails ترکیب می‌کنند. این روش‌ها به طور ذاتی مقیاس‌پذیر نیستند و در صورت ایجاد تعداد بیشتری محیط، نام‌های محیط جدیدی مانند `staging` یا `qa` نیاز است. همین‌طور که پروژه بزرگ‌تر می شود، توسعه‌دهندگان ممکن است محیط‌های خاص خود را مانند `joes-staging` اضافه کنند، که منجر به انفجار ترکیبی از پیکربندی‌ها می شود که مدیریت استقرار برنامه را بسیار شکننده می‌کند.

در یک برنامه‌ی کاربردی دوازده-سازه، متغیرهای محیطی، مقادیر ویژه‌ای هستند که هر کدام کاملاً مستقل از سایر متغیرهای محیطی هستند. آنها هرگز به عنوان «محیط» گروه‌بندی نمی شوند، بلکه به طور مستقل برای هر استقرار مدیریت می‌شوند. این مدلی است که اندازه‌ی آن به آرامی افزایش می‌یابد زیرا برنامه به طور طبیعی در چرخه‌ی عمر خود به شکل فزاینده‌ای رشد می‌یابد.