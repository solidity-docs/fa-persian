********************************
سالیدیتی v0.6.0 و تغییرات
********************************

این بخش، تغییرات اصلی را که در سالیدیتی نسخه 0.6.0 معرفی شده است، به همراه استدلال پشت این
تغییرات و نحوه به‌روزرسانی کدهای تحت تأثیر، برجسته می‌کند. برای لیست کامل، `تغییرات انتشار <https://github.com/ethereum/solidity/releases/tag/v0.6.0>`_ را بررسی
کنید.


تغییراتی که کامپایلر ممکن است در مورد آن هشدار ندهد
====================================================

این بخش تغییراتی را لیست می کند که در آن رفتار کد شما ممکن است تغییر کند بدون اینکه کامپایلر در مورد
آن به شما بگوید.

*  نوع حاصل از یک توان، نوع پایه است. قبلاً کوچکترین نوع بود که می تواند هم نوع پایه و هم نوع توان را
  مانند عملیات متقارن نگه دارد. علاوه بر این، انواع علامت‌دار برای پایه قدرت مجاز هستند.

الزامات صراحت
==============

این بخش تغییراتی را لیست می‌کند که کد اکنون باید واضح‌تر باشد، اما معنایی تغییر نمی‌کند. برای بیشتر
موضوعات، کامپایلر پیشنهاداتی را ارائه می دهد.

* اکنون فقط زمانی می توان توابع را لغو کرد که با کلمه کلیدی ``virtual`` علامت گذاری شده باشند یا در یک
  رابط تعریف شده باشند. توابع بدون پیاده سازی خارج از رابط باید ``virtual`` علامت گذاری شوند. هنگام نادیده
  گرفتن یک تابع یا اصلاح کننده، باید از کلمه کلیدی ``override`` استفاده شود. هنگام لغو یک تابع یا modifier
  تعریف شده در چندین پایه موازی، همه پایه ها باید در داخل پرانتز پس از کلمه کلیدی مانند این لیست شوند: ``override(Base1, Base2)``.

* دسترسی اعضا به طول ``length`` آرایه ها اکنون همیشه فقط خواندنی است، حتی برای آرایه های ذخیره سازی. دیگر
  امکان تغییر اندازه آرایه های ذخیره سازی با اختصاص یک مقدار جدید به طول آنها وجود ندارد. به جای آن از
   ``push()``، ``push(value)`` یا ``pop()`` استفاده کنید یا یک آرایه کامل را اختصاص دهید که البته محتوای موجود را
  بازنویسی می کند. دلیل این امر جلوگیری از برخورد آرایه های ذخیره سازی بزرگ است.

* برای علامت گذاری قراردادها به عنوان ``abstract`` می توان از کلمه کلیدی جدید abstract استفاده کرد. اگر
  قراردادی تمام وظایف خود را اجرا نکند، باید از آن استفاده شود. قراردادهای انتزاعی را نمی توان با استفاده
  از عملگر جدید ``new`` ایجاد کرد و امکان تولید بایت کد برای آنها در حین کامپایل وجود ندارد.

* کتابخانه ها باید تمام وظایف خود را اجرا کنند، نه فقط کارهای داخلی.

* نام متغیرهای اعلام شده در اسمبلی درون خطی ممکن است دیگر به ``_slot`` یا ``_offset`` ختم نشود.

* اعلامیه های متغیر در assembly درون خطی ممکن است دیگر هیچ اعلان خارج از بلوک assembly درون 
  خطی را تحت الشعاع قرار ندهند.
   اگر نام حاوی یک نقطه باشد، پیشوند آن تا نقطه ممکن است با هیچ اعلان
   خارج از بلوک اسمبلی درون خطی مغایرت نداشته باشد.

* یک قرارداد مشتق شده فقط می تواند یک متغیر حالت ``x`` را اعلام کند، در صورتی که متغیر حالت قابل
  مشاهده با همان نام در هیچ یک از پایه های آن وجود نداشته باشد.


<<<<<<< HEAD
تغییرات معنایی و نحوی
=======
Explicitness Requirements
=========================

This section lists changes where the code now needs to be more explicit,
but the semantics do not change.
For most of the topics the compiler will provide suggestions.

* Functions can now only be overridden when they are either marked with the
  ``virtual`` keyword or defined in an interface. Functions without
  implementation outside an interface have to be marked ``virtual``.
  When overriding a function or modifier, the new keyword ``override``
  must be used. When overriding a function or modifier defined in multiple
  parallel bases, all bases must be listed in parentheses after the keyword
  like so: ``override(Base1, Base2)``.

* Member-access to ``length`` of arrays is now always read-only, even for storage arrays. It is no
  longer possible to resize storage arrays by assigning a new value to their length. Use ``push()``,
  ``push(value)`` or ``pop()`` instead, or assign a full array, which will of course overwrite the existing content.
  The reason behind this is to prevent storage collisions of gigantic
  storage arrays.

* The new keyword ``abstract`` can be used to mark contracts as abstract. It has to be used
  if a contract does not implement all its functions. Abstract contracts cannot be created using the ``new`` operator,
  and it is not possible to generate bytecode for them during compilation.

* Libraries have to implement all their functions, not only the internal ones.

* The names of variables declared in inline assembly may no longer end in ``_slot`` or ``_offset``.

* Variable declarations in inline assembly may no longer shadow any declaration outside the inline assembly block.
  If the name contains a dot, its prefix up to the dot may not conflict with any declaration outside the inline
  assembly block.

* In inline assembly, opcodes that do not take arguments are now represented as "built-in functions" instead of standalone identifiers. So ``gas`` is now ``gas()``.

* State variable shadowing is now disallowed.  A derived contract can only
  declare a state variable ``x``, if there is no visible state variable with
  the same name in any of its bases.


Semantic and Syntactic Changes
>>>>>>> 73fcf69188fed78c3ad91f81ce7d6ed7c6ee79c6
==============================

این بخش تغییراتی را لیست می کند که در آن باید کد خود را تغییر دهید و پس از آن کار دیگری انجام می دهد.

* تبدیل از انواع تابع خارجی به ``address``  اکنون مجاز نیست. در عوض انواع تابع خارجی دارای عضوی به نام ``address`` هستند که مشابه عضو انتخابگر ``selector``  موجود، است.

* تابع ``push(value)`` برای آرایه های ذخیره سازی پویا دیگر طول جدید را بر نمی گرداند (هیچ چیز را برمی گرداند).

* تابع بدون نام که معمولاً به عنوان "تابع فالبک" شناخته می شود به یک تابع بازگشتی جدید که با استفاده از کلمه کلیدی فالبک ``fallback`` و یک تابع دریافت اتر با استفاده از کلمه کلیدی دریافت ``receive`` تعریف می شود، تقسیم شد.

* در صورت وجود، تابع دریافت اتر هر زمان که داده تماس(call) خالی باشد (چه اتر دریافت شود یا نه) فراخوانی می شود. این تابع به طور ضمنی قابل پرداخت ``payable`` است.

* تابع فالبک یا همان فالبک فانکشن جدید زمانی فراخوانی می شود که هیچ تابع دیگری مطابقت نداشته باشد (اگر تابع دریافت اتر وجود نداشته باشد، این شامل تماس هایی با داده تماس خالی می شود). شما می توانید این تابع را قابل پرداخت یا همان ``payable`` کنید یا خیر. اگر ``payable`` نباشد، تراکنش هایی که با هیچ تابع دیگری که ارزشی ارسال نمی کند مطابقت ندارد، برگردانده می شود. اگر از الگوی ارتقا یا پروکسی پیروی می کنید، فقط باید تابع جدید بازگشتی یا همان فالبک را پیاده سازی کنید.

ویژگی‌های جدید
============

این بخش مواردی را لیست می کند که قبل از سالیدیتی0.6.0 امکان پذیر نبودند یا دستیابی به آنها دشوارتر بود.

* عبارت :ref:`try/catch <try-catch>` به شما امکان می دهد در تماس های خارجی ناموفق واکنش نشان دهید.

* ``struct`` and ``enum`` types can be declared at file level.

* انواع ``struct`` و ``enum`` را می توان در سطح فایل اعلام کرد.

* برش های آرایه را می توان برای آرایه های داده فراخوانی استفاده کرد، برای مثال ``abi.decode(msg.data[4:], (uint, uint))`` یک روش سطح پایین برای رمزگشایی بار فراخوانی تابع است.

* Natspec از پارامترهای بازگشتی متعدد در اسناد توسعه دهنده پشتیبانی می کند و همان بررسی نامگذاری را به عنوان ``@param`` اعمال می کند.

* Yul و Inline Assembly یک عبارت جدید به نام ترک ``leave`` دارند که از تابع فعلی خارج می شود.

  * تبدیل از ``address`` به ``address payable`` اکنون از طریق ``payable(x)`` امکان پذیر است، جایی که ``x`` باید از نوع ``address`` باشد.


تغییرات رابط یا همان اینترفیس
=================

این بخش تغییراتی را لیست می‌کند که به خود زبان ارتباطی ندارند، اما روی رابط‌های کامپایلر تأثیر دارند. اینها ممکن است نحوه استفاده از کامپایلر را 
در خط فرمان یا همان کامند لاین، نحوه استفاده از رابط قابل برنامه ریزی آن، یا نحوه تجزیه و تحلیل خروجی تولید شده توسط آن را تغییر دهند.


گزارشگر خطای جدید
~~~~~~~~~~~~~~~~~~

گزارشگر خطای جدیدی معرفی شد که هدف آن تولید پیام های خطا در دسترس تر در خط فرمان است. به طور پیش فرض فعال است، اما ارسال ``--old-reporter`` به گزارشگر خطای قدیمی منسوخ برمی گردد.

گزینه های هش متادیتا
~~~~~~~~~~~~~~~~~~~~~

کامپایلر اکنون هش `IPFS <https://ipfs.io/>`_  فایل فراداده را به‌طور پیش‌فرض به انتهای بایت کد اضافه می‌کند (برای جزئیات، مستندات مربوط به :doc:`contract metadata <metadata>` را ببینید). قبل از ورژن 0.6.0، کامپایلر هش سوارم `Swarm <https://ethersphere.github.io/swarm-home/>`_ را به طور پیش‌فرض اضافه می‌کرد و برای اینکه همچنان از این رفتار پشتیبانی 
کند، گزینه جدید خط فرمان ``--metadata-hash`` معرفی شد که به شما اجازه می دهد تا با ارسال ``ipfs`` یا ``swarm`` به عنوان مقدار به گزینه خط 
فرمان ``--metadata-hash`` هش مورد نظر برای تولید و الحاق را انتخاب کنید. 
ارسال مقدار ``none`` به طور کامل هش را حذف می کند.


این تغییرات همچنین می تواند از طریق رابط :ref:`Standard JSON Interface<compiler-api>` مورد استفاده قرار گیرد و فراداده JSON تولید شده توسط کامپایلر را تحت تأثیر قرار 
دهد.



روش توصیه شده برای خواندن ابرداده خواندن دو بایت آخر برای تعیین طول رمزگذاری CBOR و انجام رمزگشایی مناسب روی آن بلوک داده همانطور که در بخش :ref:`metadata section<encoding-of-the-metadata-hash-in-the-bytecode>` توضیح داده شده است.


Yul Optimizer
~~~~~~~~~~~~~

همراه با بهینه ساز بایت کد قدیمی، زمانی که کامپایلر را با ``--optimize`` فراخوانی می کنید، بهینه ساز :doc:`Yul <yul>` به طور پیش فرض فعال می شود. با 
فراخوانی کامپایلر با ``--no-optimize-yul`` می توان آن را غیرفعال کرد. این حرکت بیشتر روی کدهایی که از کد ABI v2 استفاده می کنند تأثیر می 
گذارد.


C API تغییرات
~~~~~~~~~~~~~

کد کلاینت که از C API ``libsolc`` استفاده می کند، اکنون کنترل حافظه مورد استفاده توسط کامپایلر را در دست دارد. برای تطبیق این تغییر، 
``solidity_free`` به solidity_reset تغییر نام داد، توابع ``solidity_alloc`` و ``solidity_free`` اضافه شدند و ``solidity_compile`` اکنون رشته‌ یا 
استرینگی را برمی‌گرداند که باید صریحاً از طریق ``solidity_free()`` آزاد شود.

The client code that uses the C API of ``libsolc`` is now in control of the memory used by the compiler. To make
this change consistent, ``solidity_free`` was renamed to ``solidity_reset``, the functions ``solidity_alloc`` and
``solidity_free`` were added and ``solidity_compile`` now returns a string that must be explicitly freed via
``solidity_free()``.


چگونه کد خود را به روز کنیم
=======================

این بخش دستورالعمل های دقیقی در مورد نحوه به روز رسانی کد قبلی برای هر تغییر شکسته ارائه می دهد.

* ``address(f)`` را به ``f.address`` تغییر دهید زیرا ``f`` از نوع تابع خارجی است.

* ``function () external [payable] { ... }`` را با ``receive() external payable { ... }`` ، ``fallback() external [payable] { ... }`` یا هر دو جایگزین کنید. در صورت امکان، فقط از یک تابع دریافت استفاده کنید.

* تغییر ``uint length = array.push(value)`` به  ``;array.push(value)`` . طول جدید از طریق ``array.length`` قابل دسترسی است.

* برای افزایش ``array.length++`` به ``array.push()`` و برای کاهش طول آرایه ذخیره سازی از ``pop()`` استفاده کنید.

* برای هر پارامتر بازگشتی نامگذاری شده در اسناد  ``@dev`` یک تابع، یک ورودی ``@return`` تعریف کنید که حاوی نام پارامتر به عنوان اولین کلمه است. به عنوان مثال. اگر تابع ``f()`` تعریف شده است مانند ``function f() public returns (uint value)`` و یک  ``@dev`` که آن را حاشیه نویسی می کند، پارامترهای بازگشتی آن را به این صورت مستند کنید: ``@return value The return value.`` . می توانید پارامترهای بازگشتی نامدار و نامگذاری نشده را ترکیب کنید. مستندات تا زمانی که اعلامیه ها به ترتیبی باشند که در نوع بازگشتی تاپلی ظاهر می شوند.

* شناسه‌های منحصربه‌فرد را برای اعلان‌های متغیر در مجموعه درون خطی انتخاب کنید که با اعلان‌های خارج از بلوک اسمبلی درون خطی تضاد ندارند.

<<<<<<< HEAD
* مجازی ``virtual`` را به هر تابع غیر رابطی که قصد لغو آن را دارید اضافه کنید. ``virtual`` را به همه توابع بدون اجرای رابط های خارجی اضافه کنید. برای وراثت تکی، به هر تابع نادیده ``override`` اضافه کنید. برای وراثت چندگانه، ``override(A, B, ..)`` را اضافه کنید، جایی که تمام قراردادهایی را که تابع لغو را در پرانتز تعریف می کنند، لیست می کنید. هنگامی که چندین پایه یک تابع را تعریف می کنند، قرارداد ارثی باید همه عملکردهای متضاد را لغو کند.
=======
* Add ``virtual`` to every non-interface function you intend to override. Add ``virtual``
  to all functions without implementation outside interfaces. For single inheritance, add
  ``override`` to every overriding function. For multiple inheritance, add ``override(A, B, ..)``,
  where you list all contracts that define the overridden function in the parentheses. When
  multiple bases define the same function, the inheriting contract must override all conflicting functions.

* In inline assembly, add ``()`` to all opcodes that do not otherwise accept an argument.
  For example, change ``pc`` to ``pc()``, and ``gas`` to ``gas()``.
>>>>>>> 73fcf69188fed78c3ad91f81ce7d6ed7c6ee79c6
