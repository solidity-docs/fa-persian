.. index:: ! value type, ! type;value
.. _value-types:

انواع مقدار 
===========

انواع زیر را نیز انواع مقدار می‌نامند زیرا متغیرهای این نوع‌ها همیشه از نظر مقدار منتقل می‌شوند، یعنی وقتی که به عنوان آرگومان تابع یا در "انتساب‌ها " استفاده میشوند، همیشه کپی می‌شوند.

.. index:: ! bool, ! true, ! false

بولین
--------
  
``bool``: مقادیر ممکن ثابت‌های ``true``  و  ``false`` هستند.

عملگر‌ها:

*  ``!`` (logical negation)
*  ``&&`` (logical conjunction, "and")
*  ``||`` (logical disjunction, "or")
*  ``==`` (equality)
*  ``!=`` (inequality)

اپراتورها ``||``  و  ``&&``  قوانین متداول اتصال کوتاه  را اعمال می‌کنند. این بدان معنی است که در عبارت ``f(x) || g(y)`` ، اگر ``f(x)``  به صورت  ``true``  ارزیابی شود،  ``g(y)`` حتی اگر دارای عوارض جانبی باشد نیز ارزیابی نخواهد شد.
 


.. index:: ! uint, ! int, ! integer
.. _integers:

عدد صحیح یا اینتیجر
--------

``int`` / ``uint``:  عددهای صحیح با علامت و بدون علامت در اندازه های مختلف. کلمات کلیدی  ``uint8`` تا  ``uint256``  در گام‌های  ``8`` (بدون علامت 8 تا 256 بیت) و ``int8`` تا  ``int256`` . ``uint`` و  ``int`` به ترتیب نام مستعار برای  ``uint256`` و  ``int256`` هستند.


عملگرها:

*	مقایسه‌گرها :  ``<=`` ، ``<`` ، ``==`` ، ``!=`` ، ``>=`` ، ``>`` (ارزیابی به  ``bool`` )
*	عملگرهای بیت :  ``&`` ، ``|`` ، ``^`` (bitwise exclusive یا) ،  ``~`` (bitwise negation)
*	عملگرهای شیفت کردن :  ``>>`` (شیفت چپ) ، ``<<`` (شیفت راست)
*	عملگرهای حسابی :  ``+`` ,``-`` ، unary ``-``  (فقط برای اعداد صحیح با علامت) ، ``*`` ، ``/`` ، ``%``  (باقیمانده) ،  ``**`` (توان)

برای یک عدد صحیح نوع  ``X`` ، می‌توانید از  ``type(X).min`` و ``type(X).max``  برای دستیابی به حداقل و حداکثر مقدار قابل نمایش توسط نوع استفاده کنید.

 
.. warning::

  

  Integers in Solidity are restricted to a certain range. For example, with ``uint32``, this is ``0`` up to ``2**32 - 1``.
  There are two modes in which arithmetic is performed on these types: The "wrapping" or "unchecked" mode and the "checked" mode.
  By default, arithmetic is always "checked", which mean that if the result of an operation falls outside the value range
  of the type, the call is reverted through a :ref:`failing assertion<assert-and-require>`. You can switch to "unchecked" mode
  using ``unchecked { ... }``. More details can be found in the section about :ref:`unchecked <unchecked>`.

مقایسه‌گر‌ها
^^^^^^^^^^^

مقدار مقایسه‌گر، مقداری است که با مقایسه مقدار عدد صحیح  بدست می‌آید.

عملیات‌های بیتی (Bit operations)
^^^^^^^^^^^^^^

عملیات بیت بر روی نمایش مکمل دو انجام می‌شود. این بدان معنی است که به عنوان 
مثال  ``int256(0) == int256(-1)~`` .



شیفت‌ها 
^^^^^^

شیفت‌ها نتیجه یک عمل جابجایی دارای نوع عملوند  سمت چپ می‌باشند و نتیجه را متناسب با نوع آن کوتاه 
می‌کنند. عملوند سمت راست باید از نوع بدون علامت  باشد. تلاش برای جابجایی با نوع با علامت  خطای کامپایل 
ایجاد می‌کند.


Shifts can be "simulated" using multiplication by powers of two in the following way. Note that the truncation
to the type of the left operand is always performed at the end, but not mentioned explicitly.

- ``x << y`` is equivalent to the mathematical expression ``x * 2**y``.
- ``x >> y`` is equivalent to the mathematical expression ``x / 2**y``, rounded towards negative infinity.

.. warning::

    قبل از نسخه  ``0.5.0``  شیفت راست  ``x >> y``   برای منفی  ``x``  معادل ``x / 2**y`` بود، یعنی 
    از شیفت‌های راست به جای گرد کردن (به سمت بی نهایت منفی) از گرد کردن (به سمت صفر) استفاده 
    می‌شد.



.. note::
    بررسی‌های سرریز همانطور که برای عملیات حسابی انجام می‌شود هرگز برای عملیات شیفت انجام نمی‌شود. در عوض، نتیجه همیشه کوتاه می‌شود.

جمع، تفریق و ضرب
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

جمع، تفریق و ضرب سمنتیک معمول را دارند، با توجه به دو حالت مختلف از نظر سرریز  و زیرریز   :

به طور پیش فرض، تمام محاسبات زیرریز  و سریز بررسی می‌شود، اما این می‌تواند با استفاده از :ref:`unchecked block<unchecked>`
غیرفعال شود، در نتیجه محاسبات پیچیده می‌شود. جزئیات بیشتر را می‌توان در آن بخش یافت.


عبارت  ``x-`` برابر است با  ``(T(0) - x)`` که  ``T``  نوع  ``x`` است. فقط در انواع امضا شده قابل استفاده است. 
اگر  ``x`` منفی باشد مقدار   ``x-`` می‌تواند مثبت باشد. اخطار دیگری نیز وجود دارد که ناشی از نمایش مکمل دو  است:



اگر  ``;int x = type(int).min`` داشته باشید،  ``x-``  با رنج مثبت متناسب نیست. این به این 
معنی است که  ``unchecked { assert(-x == x); }``  کار می‌کند، و عبارت  ``x-``  هنگامی 
که در حالت checked استفاده ‌شود، منجر به اعلان شکست  می‌شود.



تقسیم  
^^^^^^^^

از آنجا که نوع نتیجه یک عملیات همیشه نوع یکی از عملوندها  است، تقسیم بر اعداد صحیح همیشه منجر به 
یک عدد صحیح می‌شود. در سالیدیتی، تقسیم به سمت صفر گرد می‌شود. این بدان معنی است که
``int256(-5) / int256(2) == int256(-2)``.

 توجه داشته باشید که در مقابل، تقسیم بر روی :ref:`لیترال‌ها<rational_literals>`  منجر به مقادیر کسری دلخواه می‌شود.


.. note::
  تقسیم بر صفر باعث :ref:`Panic error<assert-and-require>`  می‌شود. این بررسی از طریق  ``{ ... }unchecked``  غیرفعال  **نمی‌شود** .


.. note::

  عبارت  ``type(int).min / (-1)``  موردی است که تقسیم باعث سرریز  می‌شود. در حالت 
  حسابی بررسی شده ، باعث اعلان شکست می‌شود، در حالی که در حالت wrapping  ، 
  مقدار  ``type(int).min`` خواهد بود.
  
  

باقیمانده  (Modulo)
^^^^^^

عملیات باقیمانده ``a % n``  پس از تقسیم عملوند   ``a`` توسط عملوند  ``n`` ، باقی مانده  ``r`` را حاصل می‌شود، 
جایی که  ``q = int(a / n)`` و  ``r = a - (n * q)`` . این بدان معناست که باقیمانده همان 
علامت عملوند سمت چپ (یا صفر) خود را نشان می‌دهد و ``a % n == -(-a % n)``  برای منفی  ``a`` 
نگه می‌دارد:

* ``int256(5) % int256(2) == int256(1)``
* ``int256(5) % int256(-2) == int256(1)``
* ``int256(-5) % int256(2) == int256(-1)``
* ``int256(-5) % int256(-2) == int256(-1)``

.. note::

    باقیمانده با صفر باعث :ref:`Panic error<assert-and-require>`  می‌شود. این بررسی از طریق ``{ ... }unchecked`` غیرفعال **نمی‌شود**.


به توان رساندن (Exponentiation)
^^^^^^^^^^^^^^

به توان رساندن فقط برای انواع بدون علامت  در توان  در دسترس است. نوع توان در نتیجه همیشه با نوع پایه 
برابر است. لطفاً توجه داشته باشید که به اندازه کافی بزرگ باشد تا بتواند نتیجه را حفظ کند و برای اعلان شکست 
احتمالی یا رفتار پیچیده آماده شود.


.. note::

    در حالت بررسی شده ، توان فقط از آپکد  ``exp`` نسبتاً ارزان برای پایه‌های کوچک استفاده می‎کند. برای 
    موارد   ``x**3`` ، ممکن است عبارت  ``x*x*x`` ارزان تر باشد. در هر صورت، تست هزینه گاز و استفاده از 
    بهینه ساز توصیه می‌شود.


.. note::
    توجه داشته باشید که ``0**0``  توسط EVM به صورت  ``1`` تعریف می‌شود.
  

.. index:: ! ufixed, ! fixed, ! fixed point number

عدد ممیز ثابت 
-------------------

.. warning::

    عدد ممیز ثابت هنوز توسط سالیدیتی کاملاً پشتیبانی نمی‌شوند. می‌توان آن‌ها را مشخص کرد، اما نمی‌توان 
    آنها را به چیزی یا از چیزی اختصاص داد.
    

``fixed`` / ``ufixed`` : اعداد  ثابت بدون علامت و باعلامت دراندازه‌های مختلف. کلمات  کلیدی  ``ufixedMxN`` و  ``fixedMxN`` ، جایی که  ``M`` تعداد بیت‌های گرفته شده توسط نوع را نشان  می‌دهد و ``N``  نشان دهنده تعداد اعشار در دسترس است. ``M``  باید بر 8 قابل تقسیم باشد و از 8 به 256 بیت  تبدیل شود. ``N`` باید شامل 0 تا 80 باشد. ``ufixed`` و ``fixed`` به ترتیب نام‌های مستعار برای  ``ufixed128x18`` و  ``fixed128x18`` هستند.



عملگرها:

*	مقایسه ها ``<=`` ، ``<`` ، ``==`` ، ``!=`` ، ``>=`` ، ``>`` (ارزیابی به  bool)
*	عملگرهای حسابی: ``+`` ، ``-`` ، ``-`` unary  ، ``*`` ، ``/`` ، ``%`` (باقیمانده)



.. note::

    تفاوت اصلی بین اعداد float ( ``float`` و ``double`` در بسیاری از زبانها، به طور دقیق‌تر اعداد IEEE 754) و عدد ممیز ثابت در این است که تعداد بیت‌های مورد استفاده برای عدد صحیح  و قسمت کسری  (قسمت بعد از نقطه اعشاری ) در قبل انعطاف پذیر است، در حالی که در  دومی به طور دقیقاً تعریف شده‌است. به طور کلی، در اعداد float تقریباً از کل فضا برای نشان دادن عدد  استفاده می‌شود، در حالی که فقط تعداد کمی بیت مکان نقطه اعشار را تعریف می‌کنند.

    

.. index:: address, balance, send, call, delegatecall, staticcall, transfer

.. _address:

آدرس
-------

نوع آدرس به دو صورت وجود دارد که تا حد زیادی یکسان هستند:

-	``address``: دارای مقدار 20 بایت (اندازه آدرس اتریوم) است.
-	``address payable``: همان  ``address`` است، اما با اعضای اضافی  ``transfer`` و   ``send`` .

ایده پشت این تمایز این است که  ``address payable`` آدرسی است که می‌توانید اتر را به آن بفرستید، در حالی که نمی‌توان با یک  ``address`` ساده اتر ارسال کرد.


تبدیل‌های نوع::

تبدیل‌های ضمنی از  ``address payable`` به ``address`` مجاز است، در حالی که تبدیل از  ``address`` به  ``address payable`` باید از طریق ``payable(<address>)`` صریح باشد.


تبدیل صریح به و از  ``address`` برای آدرس‌های  ``uint160``، لیترال‌های عدد صحیح ، ``bytes20`` و انواع قرارداد مجاز است.

فقط عبارات نوع  ``address`` و نوع قرارداد را می توان از طریق تبدیل 
صریح  ``(...)payable`` به  ``address payable`` تبدیل کرد. برای نوع قرارداد، این تبدیل فقط در 
صورتی مجاز است که قرارداد بتواند اتر را دریافت کند، به عنوان مثال، قرارداد تابع دریافت  یا  برگشتی قابل 
پرداخت  داشته باشد. توجه داشته باشید که  ``payable(0)`` معتبر است و از این قاعده مستثنی است.


.. note::

    اگر به متغیر نوع  ``address`` نیاز دارید و قصد دارید اتر را برای آن ارسال کنید، نوع آن را به عنوان 
    آدرس  ``address payable`` مشخص کنید تا این نیاز قابل مشاهده باشد. همچنین، سعی کنید این 
    تمایز یا تغییر را در اسرع وقت انجام دهید.

  
عملگرها:

*   ``<=``, ``<``, ``==``, ``!=``, ``>=`` و ``>``


.. warning::

    اگر نوعی را که از اندازه بایت بزرگتری استفاده می‌کند به  ``address`` تبدیل کنید، به عنوان مثال 
    ``bytes32``  ، سپس به  ``address`` کوتاه می‌شود. برای کاهش ابهام تبدیل ورژن 0.4.24 و بالاتر 
    کامپایلر شما را مجبور به کوتاه کردن صریح در تبدیل می‌کند. به عنوان مثال مقدار 32 بایت 
    ``0x111122223333444455556666777788889999AAAABBBBCCCCDDDDEEEEFFFFCCCC`` را در نظر بگیرید.

    می توانید از آدرس  ``address(uint160(bytes20(b)))``  استفاده کنید که نتیجه آن 
    ``0x111122223333444455556666777788889999aAaa`` است، یا می‌توانید از آدرس 
    ``address(uint160(uint256(b)))`` استفاده کنید، که منجر به 
     ``0x777788889999AaAAbBbbCcccddDdeeeEfFFfCcCc`` می‌شود .


.. note::

    تمایز بین ``address`` و ``address payable`` با ورژن 0.5.0 معرفی شده‌است. همچنین از آن 
    ورژن، قراردادها از نوع آدرس مشتق گرفته نمی‌شوند، اما اگر تابع  payable fallback یا receive 
    داشته باشند، هنوز میتوان به صورت صریح به ``address`` و ``address payable`` تبدیل شوند. 


.. _members-of-addresses:

اعضای آدرس‌ها
^^^^^^^^^^^^^^^^^^^^

برای مراجعه سریع به کلیه اعضای آدرس، به :ref:`اعضای انواع آدرس<address_related>` مراجعه کنید.


* ``balance`` و  ``transfer``

می‌توان با استفاده از ویژگی ``balance`` ، بالانس یک آدرس را جستوجو کرد و با استفاده از تابع  ``transfer`` اتر (در واحدهای وی ) را به یک آدرس قابل پرداخت  ارسال کرد:


.. code-block:: solidity
    :force:

    address payable x = address(0x123);
    address myAddress = address(this);
    if (x.balance < 10 && myAddress.balance >= 10) x.transfer(10);


اگر بالانس قرارداد فعلی به اندازه کافی بزرگ نباشد یا انتقال اتر توسط حساب دریافت کننده رد شود، تابع 
``transfer`` از کار می‌افتد. تابع ``transfer`` در صورت شکست برمی‌گردد.


.. note::

    اگر ``x``  یک آدرس قرارداد باشد، کد آن (به طور خاص تر: تابع :ref:`Receive Ether<receive-ether-function>` در صورت وجود، یا در غیر 
    این صورت :ref:`تابع Fallback<fallback-function>` در صورت وجود) همراه با فراخوانی ``transfer`` اجرا می‌شود (این ویژگی 
    EVM است و نمی‌توان جلوی آن را گرفت ) اگر گاز آن اجرا تمام شود یا به هر صورتی از کار بیفتد، انتقال اتر 
    برگردانده می‌شود و قرارداد جاری با استثنا متوقف می‌شود.

   
* ``send``
Send نقطه مقابل سطح پایین  ``transfer`` است. در صورت عدم اجرا، قرارداد فعلی با استثنا متوقف نخواهد شد، اما ``send``  مقدار  ``false`` را برمیگرداند.

.. warning::

    ستفاده از ``send`` خطرات زیادی دارد: اگر فراخوانی پشته عمق 1024 باشد (که همیشه می‌تواند توسط 
    فراخوانی کننده مجبور شود) انتقال شکست میخورد و اگر گاز گیرنده شما تمام شود نیز از کار می‌افتد. بنابراین 
    برای انجام مطمئن انتقال اتر، همیشه مقدار برگشتی ``send`` ، را با استفاده از   ``transfer`` کنید 
    یا حتی بهتراست که: از الگویی استفاده کنید که گیرنده پول را برداشت کند.



* 	``call`` ، ``delegatecall`` و  ``staticcall``

برای برقراری ارتباط با قراردادهایی که به ABI پایبند نیستند، یا برای گرفتن کنترل مستقیم‌تری بر روی رمزگذاری 
، توابع  ``call`` ، ``delegatecall`` و  ``staticcall`` ارائه شده‌اند. همه آنها یک 
پارامتر  ``bytes memory`` را می‌گیرند و شرایط موفقیت (به عنوان  ``bool`` ) و داده های برگشتی 
(  ``bytes memory`` ) را برمی‌گردانند. از توابع ``abi.encode`` ،  ``abi.encodePacked`` ، 
``abi.encodeWithSelector`` و  ``abi.encodeWithSignature`` می‌توان برای 
رمزگذاری داده‌های ساختار یافته  استفاده کرد.

مثال:

.. code-block:: solidity

    bytes memory payload = abi.encodeWithSignature("register(string)", "MyName");
    (bool success, bytes memory returnData) = address(nameReg).call(payload);
    require(success);

.. warning::

    همه این توابع، توابع سطح پایینی هستند و باید با احتیاط استفاده شوند. به طور خاص، هر قرارداد ناشناخته‌ای ممکن است مخرب باشد و در صورت تماس با آن، کنترل آن قرارداد را به شما واگذار می‌کند که می‌تواند به نوبه خود به قرارداد شما بازگردد، بنابراین در زمان بازگشت فراخوانی‌ها خود را برای تغییراتی که روی متغیرهای حالت شما اتفاق می‌افتد آماده کنید. روش متداول برای برقراری ارتباط با سایر قراردادها، فراخوانی یک تابع در یک شی قرارداد (``()x.f``) است.
    

.. note::

    ورژن های قبلی سالیدیتی به این توابع اجازه می‌دهد آرگومان‌های دلخواه را دریافت کنند و همچنین اولین آرگومان از نوع  ``bytes4`` را به گونه دیگری مدیریت کنند. این موارد در نسخه 0.5.0 حذف شده‌اند.

تنظیم گاز تامین شده با اصلاح کننده   ``gas``  امکان پذیر است:


.. code-block:: solidity

    address(nameReg).call{gas: 1000000}(abi.encodeWithSignature("register(string)", "MyName"));

به طور مشابه، مقدار اتر عرضه شده نیز می‌تواند کنترل شود:

.. code-block:: solidity

    address(nameReg).call{value: 1 ether}(abi.encodeWithSignature("register(string)", "MyName"));

سرانجام، این اصلاح کننده‌ها می‌توانند ترکیب شوند. ترتیب آنها مهم نیست:

.. code-block:: solidity

    address(nameReg).call{gas: 1000000, value: 1 ether}(abi.encodeWithSignature("register(string)", "MyName"));

به روشی مشابه می‌توان از تابع  ``delegatecall`` استفاده کرد: تفاوت در این است که فقط از کد آدرس 
داده شده استفاده می‌شود، تمام ‌جنبه‌های دیگر (storage ، balance ، ...) از قرارداد فعلی گرفته شده‌اند. 
هدف از فراخوانی  ``delegatecall`` استفاده از کد کتابخانه است که در قرارداد دیگری ذخیره شده‌است. 
کاربر باید اطمینان حاصل کند که ساختار storage در هر دو قرارداد برای استفاده از delegatecall  مناسب است.


.. note::

    قبل از homestead، فقط یک نوع محدود به نام ``callcode`` در دسترس بود که دسترسی به مقادیر 
    اولیه  ``msg.sender`` و  ``msg.value`` را فراهم نمی‌کرد. این تابع در نسخه 0.5.0 حذف شد.

از آنجا که  بیزانس ``staticcall``  نیز می‌تواند مورد استفاده قرار گیرد. این اساساً همان ``call`` است، اما اگر تابع فراخوانی شده به هر طریقی حالت را تغییر دهد، برمی‌گردد.

هر سه تابع  ``call`` ،  ``delegatecall`` و ``staticcall``  تابع‌های سطح پایینی هستند و فقط به عنوان *آخرین راه حل* باید از آنها استفاده شود زیرا باعث از بین رفتن ایمنی بودن نوع سالیدیتی می‌شوند.

گزینه  ``gas`` در هر سه روش موجود است، در حالی که گزینه  ``value`` برای  ``delegatecall`` پشتیبانی نمی‌شود.


.. note::
   بهتر است بدون توجه به اینکه آیا حالت از آن خوانده می شود یا روی آن نوشته شده است، از تکیه بر مقادیر گاز سخت رمزگذاری شده در کد قرارداد هوشمند خود جلوگیری کنید، زیرا این امر می تواند مشکلات زیادی را به همراه داشته باشد. همچنین، دسترسی به گاز ممکن است در آینده تغییر کند.


.. note::
   
    کلیه قراردادها را می‌توان به نوع  ``address`` تبدیل کرد، بنابراین می‌توان بالانس قرارداد فعلی را با استفاده از  ``address(this).balance`` جستوجو کرد.

.. index:: ! contract type, ! type; contract

.. _contract_types:

انواع قرارداد
--------------



هر :ref:`قراردادی<contracts>` نوع خاص خود را مشخص می‌کند. به طور ضمنی می‌توانید قراردادها را به قراردادهایی که از آنها به ارث می‌برند تبدیل کنید. قراردادها را می‌توان به طور صریح به نوع ``address``   تبدیل و از آنها تغییر داد.

تبدیل صریح به نوع  ``address payable`` فقط از آنجا امکان پذیر است که نوع قرارداد تابع برگشتی قابل دریافت یا پرداخت داشته باشد. تبدیل هنوز با استفاده از  ``address(x)`` انجام می‌شود. اگر نوع قرارداد تابع برگشت پذیر یا قابل پرداخت نباشد، تبدیل به  ``address payable`` را می‌توان با استفاده از ``payable(address(x))`` انجام داد. در بخش مربوط به نوع :ref:`آدرس<address>` می‌توانید اطلاعات بیشتری کسب کنید.



.. note::
    
    قبل از ورژن 0.5.0، قراردادها مستقیماً از نوع آدرس نشأت می‌گرفتند و هیچ تفاوتی بین ``address``  و  ``address payable`` وجود نداشت.


اگر متغیر محلی را از نوع قرارداد  (``MyContract c``) مشخص کنید، می‌توانید توابع مربوط به آن قرارداد را فراخوانی کنید. مراقب باشید که آن را از جایی اختصاص دهید که همان نوع قرارداد باشد.

شما همچنین می‎توانید قراردادها را فوری (یعنی آنهایی که تازه ایجاد شده‌اند) قرار دهید. جزئیات بیشتر را می‌توانید در بخش :ref:`"قرارداد از طریق new"<creating-contracts>` پیدا کنید.

نمایش داده‌های یک قرارداد با نوع  ``address`` یکسان است و از این نوع در :ref:`ABI<ABI>` نیز استفاده می‌شود.
قراردادها از هیچ عملگری پشتیبانی نمی‌کنند.

اعضای انواع قرارداد، توابع خارجی قرارداد شامل هر متغیر حالت است که به عنوان  ``public`` مشخص شده‌است.

برای قرارداد  ``C`` می‌توانید از ``type(C)``  برای دسترسی به :ref:`اطلاعات مربوط به تایپ<meta-type>` استفاده کنید.

آرایه‌های بایت با اندازه ثابت 


.. index:: byte array, bytes32

آرایه‌های بایت با اندازه ثابت 
----------------------

مقدارهای مختلف ``bytes32`` ، ... ، ``bytes3`` ، ``bytes2`` ، ``bytes1`` توالی بایت را از یک تا 32 نگه می‌دارد.


عملگرها:

* Comparisons: ``<=``, ``<``, ``==``, ``!=``, ``>=``, ``>`` (evaluate to ``bool``)
* Bit operators: ``&``, ``|``, ``^`` (bitwise exclusive or), ``~`` (bitwise negation)
* Shift operators: ``<<`` (left shift), ``>>`` (right shift)
* Index access: If ``x`` is of type ``bytesI``, then ``x[k]`` for ``0 <= k < I`` returns the ``k`` th byte (read-only).


عملگر شیفت با نوع عدد صحیح بدون علامت به عنوان عملوند راست کار می‌کند (اما نوع عملوند سمت چپ را برمی‌گرداند)، که تعداد بیت های شیفت را نشان می‌دهد. جابجایی با نوع با علامت ، خطای کامپایل ایجاد می‌کند.


اعضا (Members):

* ``length.``  طول ثابت آرایه بایت را ارائه می‌دهد (فقط برای خواندن).

.. note::
    
    نوع  ``bytes1[]`` ، آرایه‌ای از بایت است. اما به دلیل قوانین لایه گذاری، 31 بایت فضا را برای هر عنصر هدر می‌دهد (به جز در storage). بهتر است به جای آن از نوع  ``bytes`` استفاده کنید.

.. note::

    قبل از ورژن 0.8.0 ،  ``byte`` یک نام مستعار برای ``bytes1`` بود.



آرایه بایت با اندازه پویا 
----------------------------

``bytes``:

    آرایه بایت در اندازه پویا، از نوع مقدار  نیست. به قسمت  :ref:`آرایه‌ها<arrays>` مراجعه کنید !

   
``string``:

    رشته‌ای با کد UTF-8 به صورت پویا، به قسمت  :ref:`آرایه‌ها<arrays>` کنید. از نوع مقدار نیست!

.. index:: address, literal;address

.. _address_literals:

لیترال‌های Address
----------------

لیترال‌های هگزادسیمال که از تست checksum آدرس استفاده می‌کنند، به عنوان مثال 
``0xdCad3a6d3569DF655070DEd06cb7A1b2Ccd1D3AF``  از نوع  ``address`` هستند. 
لیترال‌های هگزادسیمال که دارای طول 39 تا 41 رقم هستند و از  تست checksum عبور نمی‌کنند، خطایی 
ایجاد می‌کنند. برای حذف خطا می‌توانید (برای انواع عدد صحیح) یا (برای انواع bytesNN) صفرها را ضمیمه 
کنید.



.. note::
    
    قالب  checksum آدرس مختلط در `EIP-55 <https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md>`_ تعریف شده است.

.. index:: literal, literal;rational

.. _rational_literals:

لیترال‌های عدد گویا و صحیح
-----------------------------

لیترال‌های عدد صحیح  از توالی اعداد در محدوده 0-9 تشکیل می شوند. آنها به عنوان دیسیمال تفسیر می 
شوند. به عنوان مثال  ``69`` به معنای شصت و نه است. لیترال‌های Octal در سالیدیتی وجود ندارند و صفرهای 
قبل از عدد نامعتبر هستند.


لیترال‌های کسری دیسیمال توسط یک  ``.`` با حداقل یک عدد در یک طرف تشکیل می‌شوند. مثال‌ها 
شامل ``.1`` ،  ``1.`` و  ``1.3`` است.



نشانه علمی نیز پشتیبانی می‌شود، جایی که پایه می‌تواند کسر داشته باشد و توان ‌تواند. برای مثال از 
جمله  ``2e10`` ،  ``-2e10`` ،  ``2e-10`` ،  ``2.5e1`` .

// @saracodic's comment:   ``(2**800 + 1) - 2**800`` swapping in the above text

زیرخط‌ می‌تواند برای جدا کردن رقم از لیترال‌های عددی برای کمک به خوانایی استفاده شود. به عنوان مثال، 
دسیمال ``000_123`` ، هگزادسیمال  ``0x2eff_abde`` ، نماد علمی دسیمال  ``1_2e34_678`` همه 
معتبر هستند. زیرخط‌ تنها بین دو رقم مجاز است و تنها یک زیرخط متوالی مجاز است. هیچ معنایی سمنتیک 
اضافی به لیترال‌ عددی  حاوی زیرخط اضافه نشده است. 

// @saracodic's comment:   ``(2**800 + 1) - 2**800`` swapping in the above text

عبارات لیترال‌های عددی  دقت دلخواه را حفظ می‌کنند تا زمانی که به یک نوع غیرلیترالی تبدیل شوند (به عنوان 
مثال استفاده از آنها همراه با یکدیگر با یک عبارت غیرلیترالی یا با تبدیل صریح). این بدان معناست که محاسبات 
سرریز نمی‌شود و تقسیمات در عبارات لیترال عددی کوتاه نمی‌شوند.


به عنوان مثال،  ``(2**800 + 1) - 2**800`` منجر به ثابت   ``1``  (از نوع  ``uint8`` ) می‌شود گرچند 
نتایج میانی حتی اندازه کلمه ماشین را فیت نمی‌کند. علاوه بر این،   ``8 * 5.`` منجر به عدد صحیح  4 (گرچند 
در بین آنها غیر عدد صحیح استفاده می‌شود).

 // @saracodic's comment:   ``(2**800 + 1) - 2**800`` swapping in the above text

هر عملگری که می‌تواند به عدد صحیح اعمال شود، تا زمانی که عملوند‌ها عدد صحیح باشند می تواند به لیترال‌های 
عددی نیز اعمال شود. اگر هر یکی از این دو کسری باشند، عملیات بیت امکان پذیر نیست و نیز به توان رساندن 
اگر توان کسری باشد (زیرا ممکن است منجر به یک عدد غیر گویا شوند) امکان پذیر نیست.



تعویض و به توان رساندن با اعداد لیترال  بطوریکه سمت چپ (یا پایه) عملوند و نوع عدد صحیح در سمت راست 
به عنوان عملوند  (توان) همیشه در   ``uint256`` (برای لیترال‌های غیر منفی) یا  ``int256`` (برای لیترال‌های 
منفی)، بدون توجه به نوع سمت راست عملوند (توان)، عمل می‌کند.



.. warning::

    تقسیم بر روی لیترال‌های عدد صحیح برای کوتاه کردن در سالیدیتی نسخه‌های قبلتر از نسخه 0.4.0 استفاده 
    میشد، اما اکنون به یک عدد گویا تبدیل می شود، برای مثال  ``2 / 5``  برابر با ``2`` نیست بلکه برابر با ``2.5`` می‌باشد.
  
.. note::

    سالیدیتی برای هر عدد گویا یک نوع لیترال عددی دارد. لیترال های عدد صحیح و لیترال‌های عدد گویا به انواع 
    لیترال‌های عدد تعلق دارند. علاوه بر این، تمام عبارات لیترال‌های عددی (یعنی عباراتی که فقط شامل 
    لیترال‌های عددی و عملگرها هستند) به انواع لیترال‌های عددی تعلق دارند. بنابراین عبارات لیترال عددی  
    ``1+2``  و ``2+1`` هر دو متعلق به همان نوع لیترال عددی برای عدد گویا سه هستند.

 // @saracodic's comment:   ``2+1`` swapping in the above text


.. note::


    عبارات لیترال عددی به محض استفاده با عبارات غیر لیترال به نوع غیر لیترال تبدیل می‌شوند. با نادیده گرفتن 
    انواع، مقدار عبارتی که به  ``b`` در زیر اختصاص داده شده به عنوان عدد صحیح ارزیابی می‌شود. از آنجا که  ``a`` از 
    نوع ``uint128`` است، عبارت ``2.5 +a`` باید نوع مناسبی داشته باشد. از آنجا که نوع متداولی برای 
    نوع  ``2.5`` و  ``uint128`` وجود ندارد، کامپایلر سالیدیتی این کد را قبول نمی‌کند.

// @saracodic's comment:  2.5+a swapping in the above text

.. code-block:: solidity

    uint128 a = 1;
    uint128 b = 2.5 + a + 0.5;

.. index:: literal, literal;string, string
.. _string_literals:

لیترال‌های string و انواع
-------------------------

 لیترال‌های رشته‌ای با دو نقل قول يا تك نقل قولي نوشته مي‌شوند ( ``"foo"``  يا  ``'bar'`` )، و همچنين 
 مي‌توانند به چند قسمت متوالي تقسيم شوند ( ``"foo" "bar"``  معادل ``"foobar"`` است) که می‌تواند 
 هنگام کار با رشته‌های طولانی مفید باشد. آنها به مفهوم صفر انتهایی در C نیست.  ``"foo"`` نشانگر سه بایت 
 می‌باشد، نه چهار بایت.
 همانند لیترال‌های عدد صحیح، نوع آنها نیز می‌تواند متفاوت باشد، اما در صورت متناسب بودن آنها به بایت‌های 
 ``bytes1, …, bytes32`` تبدیل می‌شوند، اگر متناسب باشند، به ``bytes`` و  ``string`` تبدیل می‌شوند.

به عنوان مثال، با  b``bytes32 samevar = "stringliteral"``  لیترال  رشته‌ای وقتی به نوع ``bytes32`` اختصاص یابد به معنای بایت خام  تفسیر می‌شود.


  لیترال‌های رشته‌ای  فقط می‌توانند حاوی کارکترهای ASCII قابل چاپ باشند، این به معنای کارکترهای شامل و بین 0x1F .. 0x7E می‌باشند.

علاوه بر این، لیترال‌های رشته‌ای  از کارکتر‌های escape زیر نیز پشتیبانی می‌کنند:


- ``\<newline>`` (escapes an actual newline)
- ``\\`` (backslash)
- ``\'`` (single quote)
- ``\"`` (double quote)
- ``\n`` (newline)
- ``\r`` (carriage return)
- ``\t`` (tab)
- ``\xNN`` (hex escape, see below)
- ``\uNNNN`` (unicode escape, see below)

``xNN\`` یک مقدار hex می‌گیرد و بایت مناسب را وارد می‌کند، در حالی که  ``uNNNN\`` یک کد رمز Unicode را می‌گیرد و یک توالی UTF-8 را وارد می‌کند.


.. note::


    Until version 0.8.0 there were three additional escape sequences: ``\b``, ``\f`` and ``\v``.
    They are commonly available in other languages but rarely needed in practice.
    If you do need them, they can still be inserted via hexadecimal escapes, i.e. ``\x08``, ``\x0c``
    and ``\x0b``, respectively, just as any other ASCII character.




رشته در مثال زیر دارای طول ده بایت است. این کار با یک بایت خط جدید و به دنبال آن یک دو نقل قول، یک  تک نقل قول یک کاراکتر بک اسلش و سپس (بدون جدا کننده) توالی کاراکتر ``abcdef`` شروع می‌شود.



.. code-block:: solidity
    :force:

    "\n\"\'\\abc\
    def"

هر خاتمه دهنده خط Unicode که یک خط جدید نباشد (به عنوان مثال LF ، VF ، FF ، CR ، NEL ، LS ، 
PS) برای خاتمه لیترال رشته در نظر گرفته می‌شود. Newline فقط در صورتی لیترال رشته را خاتمه می‌دهد 
که قبل از آن  ``\`` وجود نداشته باشد.

لیترال‌های Unicode
----------------
در حالی که لیترال‌های رشته‌ای منظم فقط می‌توانند حاوی ASCII باشند، لیترال‌های unicode - با پیشوند 
کلمه کلیدی – ``unicode`` می‌توانند حاوی هر توالی معتبر UTF-8  باشند. آنها همچنین از همان توالی‌های 
escape به عنوان لیترال‌های رشته منظم پشتیبانی می‌کنند.


.. code-block:: solidity

    string memory a = unicode"Hello 😃";

.. index:: literal, bytes

لیترال‌های هگزادسیمال
--------------------
لیترال‌های هگزادسیمال با پیشوند کلمه کلیدی  ``hex`` هستند،که با دو نقل قول يا تك نقل قولي محصور شده‌اند 
( ``hex"001122FF"``، ``hex'0011_22_FF'`` ). محتوای آنها باید ارقام هگزادسیمال باشد که به 
صورت اختیاری می‌تواند از یک زیر خط به عنوان جدا کننده بین مرز بایت استفاده کند. مقدار لیترال، نمایش 
دودویی توالی هگزادسیمال خواهد بود.


لیترال‌های مالتی هگزادسیمال جدا شده توسط فضای خالی به یک لیترال متصل 
می‌شوند:  ``hex"00112233" hex"44556677"`` معادل با  ``hex"0011223344556677"`` است.


لیترال‌های هگزادسیمال مانند :ref:`لیترال‌های رشته‌ای<string_literals>` رفتار می‌کنند و محدودیت‌های تبدیل پذیری یکسانی دارند.

.. index:: enum

.. _enums:

Enums
-----
Enums یکی از راه‌های ایجاد یک نوع تعریف شده توسط کاربر در سالیدیتی می‌باشد. آنها به طور صریح قابل 
تبدیل به انواع مختلف عدد صحیح هستند اما تبدیل ضمنی مجاز نیست. تبدیل صریح از عدد صحیح در زمان 
اجرا بررسی می‌کند که مقدار در محدوده enum باشد و در غیر این صورت باعث ایجاد خطای :ref:`Panic error<assert-and-require>` می‌شود. 
Enums حداقل به یک عضو نیاز دارد و مقدار پیش فرض آن هنگام اعلام اولین عضو است. Enums نمی‌تواند بیش از 256 عضو داشته باشد.


   نمایش داده‌ها همانند enum ها در C است: گزینه‌ها با مقادیر صحیح بدون علامت بعدی ارسال می‌شوند که از  ``0`` شروع می‌شوند.


با استفاده از ``type(NameOfEnum).min`` و ``type(NameOfEnum).max`` می‌توانید
کوچکترین و به ترتیب بزرگترین مقدار عدد داده شده enum را دریافت کنید.



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.8;

    contract test {
        enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
        ActionChoices choice;
        ActionChoices constant defaultChoice = ActionChoices.GoStraight;

        function setGoStraight() public {
            choice = ActionChoices.GoStraight;
        }

        // Since enum types are not part of the ABI, the signature of "getChoice"
        // will automatically be changed to "getChoice() returns (uint8)"
        // for all matters external to Solidity.
        function getChoice() public view returns (ActionChoices) {
            return choice;
        }

        function getDefaultChoice() public pure returns (uint) {
            return uint(defaultChoice);
        }

        function getLargestValue() public pure returns (ActionChoices) {
            return type(ActionChoices).max;
        }

        function getSmallestValue() public pure returns (ActionChoices) {
            return type(ActionChoices).min;
        }
    }

.. note::
    Enums همچنین می‌تواند خارج از تعاریف قرارداد یا کتابخانه، در سطح فایل مشخص شوند.
    

.. index:: ! user defined value type, custom type

.. _user-defined-value-types:

User Defined Value Types
------------------------

A user defined value type allows creating a zero cost abstraction over an elementary value type.
This is similar to an alias, but with stricter type requirements.

A user defined value type is defined using ``type C is V``, where ``C`` is the name of the newly
introduced type and ``V`` has to be a built-in value type (the "underlying type"). The function
``C.wrap`` is used to convert from the underlying type to the custom type. Similarly, the
function ``C.unwrap`` is used to convert from the custom type to the underlying type.

The type ``C`` does not have any operators or bound member functions. In particular, even the
operator ``==`` is not defined. Explicit and implicit conversions to and from other types are
disallowed.

The data-representation of values of such types are inherited from the underlying type
and the underlying type is also used in the ABI.

The following example illustrates a custom type ``UFixed256x18`` representing a decimal fixed point
type with 18 decimals and a minimal library to do arithmetic operations on the type.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.8;

    // Represent a 18 decimal, 256 bit wide fixed point type using a user defined value type.
    type UFixed256x18 is uint256;

    /// A minimal library to do fixed point operations on UFixed256x18.
    library FixedMath {
        uint constant multiplier = 10**18;

        /// Adds two UFixed256x18 numbers. Reverts on overflow, relying on checked
        /// arithmetic on uint256.
        function add(UFixed256x18 a, UFixed256x18 b) internal pure returns (UFixed256x18) {
            return UFixed256x18.wrap(UFixed256x18.unwrap(a) + UFixed256x18.unwrap(b));
        }
        /// Multiplies UFixed256x18 and uint256. Reverts on overflow, relying on checked
        /// arithmetic on uint256.
        function mul(UFixed256x18 a, uint256 b) internal pure returns (UFixed256x18) {
            return UFixed256x18.wrap(UFixed256x18.unwrap(a) * b);
        }
        /// Take the floor of a UFixed256x18 number.
        /// @return the largest integer that does not exceed `a`.
        function floor(UFixed256x18 a) internal pure returns (uint256) {
            return UFixed256x18.unwrap(a) / multiplier;
        }
        /// Turns a uint256 into a UFixed256x18 of the same value.
        /// Reverts if the integer is too large.
        function toUFixed256x18(uint256 a) internal pure returns (UFixed256x18) {
            return UFixed256x18.wrap(a * multiplier);
        }
    }

توجه داشته باشید که چگونه ``UFixed256x18.wrap`` و ``FixedMath.toUFixed256x18`` دارای امضای یکسان 
هستند اما دو عملیات بسیار متفاوت را انجام می‌دهند: تابع ``UFixed256x18.wrap`` یک ``UFixed256x18`` را برمی‌گرداند 
که نمایش داده مشابه با ورودی دارد، در حالی که ``toUFixed256x18`` یک ``UFixed256x18`` را برمی‌گرداند که مقدار 
عددی یکسانی دارد.


.. index:: ! function type, ! type; function

.. _function_types:

انواع توابع(Function Types)
--------------

انواع توابع انواعی از توابع هستند. متغیرهای نوع تابع را می‌توان از توابع اختصاص داد و پارامترهای تابع نوع تابع 
را می‌توان برای انتقال توابع به توابع برگشتی و از فراخوانی‌های تابع استفاده کرد. انواع توابع به دو صورت هستند– 
توابع *داخلی* و *خارجی*:


توابع داخلی را فقط می‌توان در داخل قرارداد فعلی فراخوانی کرد (به طور خاص، در داخل واحد کد فعلی، که 
شامل توابع کتابخانه داخلی و توابع وراثتی نیز می‌شود) زیرا نمی‌توانند خارج از متن قرارداد فعلی اجرا شوند. 
فراخوانی یک تابع داخلی با پرش به برچسب ورودی آن انجام می‌شود، دقیقاً مانند هنگام فراخوانی داخلی توابع 
قرارداد فعلی.


  توابع خارجی شامل یک آدرس و یک امضای تابع می‌باشند و می‌توان آنها را از طریق فراخوانی‌های تابع خارجی منتقل کرد و از آنها بازگرداند.


  انواع توابع به شرح زیر ذکر شده‌است:
Function types are notated as follows:

.. code-block:: solidity
    :force:

    function (<parameter types>) {internal|external} [pure|view|payable] [returns (<return types>)]

در مقابل انواع پارامترها، انواع بازگشت نمی‌توانند خالی باشند - اگر نوع عملکرد نباید چیزی را برگرداند، کل 
قسمت  ``returns (<return types>)``  باید حذف شود. به طور پیش فرض، انواع عملکردها داخلی 
هستند، بنابراین می‌توان کلمه کلیدی داخلی را حذف کرد. توجه داشته باشید که این فقط در انواع توابع اعمال 
می‌شود. قابلیت مشاهده به طور صریح برای توابع تعریف شده در قراردادها مشخص می‌شود، آنها پیش فرض ندارند.



به طور پیش فرض، انواع توابع داخلی هستند، بنابراین می‌توان کلمه کلیدی ``internal`` را حذف کرد. توجه 
داشته باشید که این فقط در انواع توابع اعمال می‌شود. قابلیت مشاهده به طور صریح برای توابع تعریف شده در 
قراردادها مشخص می‌شود، آنها پیش فرض ندارند.



تبدیل‌ها:

یک تابع نوع  ``A`` به طور ضمنی قابل تبدیل به یک تابع نوع  ``B`` است اگر و فقط اگر انواع پارامترهای آنها یکسان 
باشد، انواع بازگشت آنها یکسان، ویژگی internal/external آنها یکسان باشد و تغییرپذیری حالت  ``A`` محدود 
کننده‌تر از تغییر پذیری حالت  ``B`` . به خصوص:



-	توابع ``pure`` را می‌توان به  ``view`` و توابع  ``non-payable`` تبدیل کرد
-	توابع  ``view`` را می‌توان به توابع  ``non-payable`` پرداخت تبدیل کرد
-	توابع  ``payable`` را می‌توان به توابع  ``non-payable`` پرداخت تبدیل کرد



هیچ تبدیل دیگری بین انواع توابع امکان پذیر نیست.


  قانون مربوط به  ``payable`` و   ``non-payable`` ممکن است کمی گیج کننده باشد، اما در اصل، اگر 
  تابعی  ``payable`` باشد، این بدان معناست که پرداخت صفر اتر را نیز می‌پذیرد، بنابراین  ``non-payable`` نیز می‌باشد. از طرف دیگر، یک تابع  ``non-payable`` اتر ارسال شده به آن را رد می‌کند، 
  بنابراین توابع  ``non-payable`` نمی‌توانند به توابع  ``payable`` تبدیل شوند.

اگر یک متغیر از نوع تابع مقداردهی اولیه نشده باشد، فراخوانی آن منجر به خطای :ref:`Panic error<assert-and-require>` می‌شود. اگر پس از 
استفاده از  ``delete`` تابع آن را فراخوانی کنید، همین اتفاق می‌افتد.


اگر از انواع توابع external خارج از زمینه سالیدیتی استفاده شود، با آنها به عنوان نوع  ``function`` رفتار 
می‌شود، که آدرس و به دنبال آن شناسه تابع را با هم در یک تک نوع  ``bytes24`` رمزگذاری می‌کند.



توجه داشته باشید که توابع عمومی قرارداد جاری می‌توانند هم به عنوان تابع داخلی و هم به عنوان تابع خارجی 
استفاده شود. برای استفاده از  ``f`` به عنوان یک تابع داخلی ، فقط از  ``f`` استفاده کنید، اگر می‌خواهید از فرم 
خارجی آن استفاده کنید، از  ``this.f`` استفاده کنید.



یک تابع از یک نوع داخلی را می‌توان به یک متغیر از یک نوع تابع داخلی بدون در نظر گرفتن مکان تعریف شده 
اختصاص داد. این شامل توابع خصوصی، داخلی و عمومی قراردادها و کتابخانه‌ها و همچنین توابع رایگان است. از 
طرف دیگر، انواع توابع خارجی فقط با توابع قرارداد عمومی و خارجی سازگار هستند. کتابخانه‌ها از این مستثنی 
هستند چونکه به یک ``delegatecall`` نیاز دارند و :ref:`از یک کنوانسیون  مختلف ABI برای انتخابگرهای خود<library-selectors>` 
استفاده می‌کنند. توابع اعلام شده در رابط‌ها تعریفی ندارند، بنابراین اشاره به آنها نیز معنی ندارد.

اعضا:

توابع خارجی (یا عمومی) اعضای زیر را دارند:

*	``address.`` آدرس قرارداد تابع را برمی‌گرداند.
*	``selector.``  :ref:`انتخابگر تابع ABI<abi_function_selector>` را برمی‌گرداند.


.. note::

  توابع خارجی (یا عمومی) برای داشتن اعضای اضافی  ``gas(uint).`` و  ``value(uint).`` استفاده 
  می‌شود. اینها در سالیدیتی نسخه 0.6.2 منسوخ شده و در سالیدیتی نسخه 0.7.0 حذف شدند. در عوض 
  از  ``{...:gas }`` و  ``{...:value}``  برای تعیین مقدار گاز یا مقدار wei ارسال شده به یک 
  تابع استفاده کنید. برای اطلاعات بیشتر به قسمت :ref:`فراخوانی تابع خارجی<external-function-calls>` مراجعه کنید.  
  

مثالی که نحوه استفاده از اعضا را نشان می‌دهد:


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.4 <0.9.0;

    contract Example {
        function f() public payable returns (bytes4) {
            assert(this.f.address == address(this));
            return this.f.selector;
        }

        function g() public {
            this.f{gas: 10, value: 800}();
        }
    }

مثالی که نحوه استفاده از انواع توابع داخلی را نشان می‌دهد:


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    library ArrayUtils {
        // internal functions can be used in internal library functions because
        // they will be part of the same code context
        function map(uint[] memory self, function (uint) pure returns (uint) f)
            internal
            pure
            returns (uint[] memory r)
        {
            r = new uint[](self.length);
            for (uint i = 0; i < self.length; i++) {
                r[i] = f(self[i]);
            }
        }

        function reduce(
            uint[] memory self,
            function (uint, uint) pure returns (uint) f
        )
            internal
            pure
            returns (uint r)
        {
            r = self[0];
            for (uint i = 1; i < self.length; i++) {
                r = f(r, self[i]);
            }
        }

        function range(uint length) internal pure returns (uint[] memory r) {
            r = new uint[](length);
            for (uint i = 0; i < r.length; i++) {
                r[i] = i;
            }
        }
    }


    contract Pyramid {
        using ArrayUtils for *;

        function pyramid(uint l) public pure returns (uint) {
            return ArrayUtils.range(l).map(square).reduce(sum);
        }

        function square(uint x) internal pure returns (uint) {
            return x * x;
        }

        function sum(uint x, uint y) internal pure returns (uint) {
            return x + y;
        }
    }

مثال دیگری که از انواع توابع خارجی استفاده می‌کند:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.22 <0.9.0;


    contract Oracle {
        struct Request {
            bytes data;
            function(uint) external callback;
        }

        Request[] private requests;
        event NewRequest(uint);

        function query(bytes memory data, function(uint) external callback) public {
            requests.push(Request(data, callback));
            emit NewRequest(requests.length - 1);
        }

        function reply(uint requestID, uint response) public {
            // Here goes the check that the reply comes from a trusted source
            requests[requestID].callback(response);
        }
    }


    contract OracleUser {
        Oracle constant private ORACLE_CONST = Oracle(address(0x00000000219ab540356cBB839Cbe05303d7705Fa)); // known contract
        uint private exchangeRate;

        function buySomething() public {
            ORACLE_CONST.query("USD", this.oracleResponse);
        }

        function oracleResponse(uint response) public {
            require(
                msg.sender == address(ORACLE_CONST),
                "Only oracle can call this."
            );
            exchangeRate = response;
        }
    }

.. note::
  
      توابع Lambda یا inline برنامه ریزی شده‌اند اما هنوز پشتیبانی نمی‌شوند.