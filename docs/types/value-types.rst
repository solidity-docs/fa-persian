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

Contract Types
--------------


انواع قرارداد
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
•	مقایسه ها: <=، <، ==، !=، >=، > (ارزیابی به  bool)
•	عملگرهای بیت:  &، |، ^ (bitwise exclusive یا) ، ~  (bitwise negation)
•	عملگرهای شیفت :  << (شیفت چپ) ،  >> (شیفت راست)

*    مقایسه ها: ``=>`` ، ``>`` ، ``==`` ، ``=!`` ، ``=<`` ، ``<`` (ارزیابی به  ``bool``)
*     عملگرهای بیت:  ``&`` ، ``|`` ، ``^`` (bitwise exclusive یا) ، ``~``  (bitwise negation)
*     عملگرهای شیفت :  ``>>`` (شیفت چپ) ،  ``<<`` (شیفت راست)
*     دسترسی به Index: اگر  ``x`` از نوع  ``bytesI`` باشد، سپس ``x[k]`` برای  ``0 <= k < I``   بایت  ``k`` را برمی‌گردانم (فقط برای خواندن).
* Shift operators: ``<<`` (left shift), ``>>`` (right shift)
* Index access: If ``x`` is of type ``bytesI``, then ``x[k]`` for ``0 =< k < I`` returns the ``k`` th byte (read-only).

Operators:

* Comparisons: ``<=``, ``<``, ``==``, ``!=``, ``>=``, ``>`` (evaluate to ``bool``)
* Bit operators: ``&``, ``|``, ``^`` (bitwise exclusive or), ``~`` (bitwise negation)
* Shift operators: ``<<`` (left shift), ``>>`` (right shift)
* Index access: If ``x`` is of type ``bytesI``, then ``x[k]`` for ``0 <= k < I`` returns the ``k`` th byte (read-only).

The shifting operator works with unsigned integer type as right operand (but
returns the type of the left operand), which denotes the number of bits to shift by.
Shifting by a signed type will produce a compilation error.

Members:

* ``.length`` yields the fixed length of the byte array (read-only).

.. note::
    The type ``bytes1[]`` is an array of bytes, but due to padding rules, it wastes
    31 bytes of space for each element (except in storage). It is better to use the ``bytes``
    type instead.

.. note::
    Prior to version 0.8.0, ``byte`` used to be an alias for ``bytes1``.

Dynamically-sized byte array
----------------------------

``bytes``:
    Dynamically-sized byte array, see :ref:`arrays`. Not a value-type!
``string``:
    Dynamically-sized UTF-8-encoded string, see :ref:`arrays`. Not a value-type!

.. index:: address, literal;address

.. _address_literals:

Address Literals
----------------

Hexadecimal literals that pass the address checksum test, for example
``0xdCad3a6d3569DF655070DEd06cb7A1b2Ccd1D3AF`` are of ``address`` type.
Hexadecimal literals that are between 39 and 41 digits
long and do not pass the checksum test produce
an error. You can prepend (for integer types) or append (for bytesNN types) zeros to remove the error.

.. note::
    The mixed-case address checksum format is defined in `EIP-55 <https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md>`_.

.. index:: literal, literal;rational

.. _rational_literals:

Rational and Integer Literals
-----------------------------

Integer literals are formed from a sequence of numbers in the range 0-9.
They are interpreted as decimals. For example, ``69`` means sixty nine.
Octal literals do not exist in Solidity and leading zeros are invalid.

Decimal fraction literals are formed by a ``.`` with at least one number on
one side.  Examples include ``1.``, ``.1`` and ``1.3``.

Scientific notation is also supported, where the base can have fractions and the exponent cannot.
Examples include ``2e10``, ``-2e10``, ``2e-10``, ``2.5e1``.

Underscores can be used to separate the digits of a numeric literal to aid readability.
For example, decimal ``123_000``, hexadecimal ``0x2eff_abde``, scientific decimal notation ``1_2e345_678`` are all valid.
Underscores are only allowed between two digits and only one consecutive underscore is allowed.
There is no additional semantic meaning added to a number literal containing underscores,
the underscores are ignored.

Number literal expressions retain arbitrary precision until they are converted to a non-literal type (i.e. by
using them together with a non-literal expression or by explicit conversion).
This means that computations do not overflow and divisions do not truncate
in number literal expressions.

For example, ``(2**800 + 1) - 2**800`` results in the constant ``1`` (of type ``uint8``)
although intermediate results would not even fit the machine word size. Furthermore, ``.5 * 8`` results
in the integer ``4`` (although non-integers were used in between).

Any operator that can be applied to integers can also be applied to number literal expressions as
long as the operands are integers. If any of the two is fractional, bit operations are disallowed
and exponentiation is disallowed if the exponent is fractional (because that might result in
a non-rational number).

Shifts and exponentiation with literal numbers as left (or base) operand and integer types
as the right (exponent) operand are always performed
in the ``uint256`` (for non-negative literals) or ``int256`` (for a negative literals) type,
regardless of the type of the right (exponent) operand.

.. warning::
    Division on integer literals used to truncate in Solidity prior to version 0.4.0, but it now converts into a rational number, i.e. ``5 / 2`` is not equal to ``2``, but to ``2.5``.

.. note::
    Solidity has a number literal type for each rational number.
    Integer literals and rational number literals belong to number literal types.
    Moreover, all number literal expressions (i.e. the expressions that
    contain only number literals and operators) belong to number literal
    types.  So the number literal expressions ``1 + 2`` and ``2 + 1`` both
    belong to the same number literal type for the rational number three.


.. note::
    Number literal expressions are converted into a non-literal type as soon as they are used with non-literal
    expressions. Disregarding types, the value of the expression assigned to ``b``
    below evaluates to an integer. Because ``a`` is of type ``uint128``, the
    expression ``2.5 + a`` has to have a proper type, though. Since there is no common type
    for the type of ``2.5`` and ``uint128``, the Solidity compiler does not accept
    this code.

.. code-block:: solidity

    uint128 a = 1;
    uint128 b = 2.5 + a + 0.5;

.. index:: literal, literal;string, string
.. _string_literals:

String Literals and Types
-------------------------

String literals are written with either double or single-quotes (``"foo"`` or ``'bar'``), and they can also be split into multiple consecutive parts (``"foo" "bar"`` is equivalent to ``"foobar"``) which can be helpful when dealing with long strings.  They do not imply trailing zeroes as in C; ``"foo"`` represents three bytes, not four.  As with integer literals, their type can vary, but they are implicitly convertible to ``bytes1``, ..., ``bytes32``, if they fit, to ``bytes`` and to ``string``.

For example, with ``bytes32 samevar = "stringliteral"`` the string literal is interpreted in its raw byte form when assigned to a ``bytes32`` type.

String literals can only contain printable ASCII characters, which means the characters between and including 0x1F .. 0x7E.

Additionally, string literals also support the following escape characters:

- ``\<newline>`` (escapes an actual newline)
- ``\\`` (backslash)
- ``\'`` (single quote)
- ``\"`` (double quote)
- ``\n`` (newline)
- ``\r`` (carriage return)
- ``\t`` (tab)
- ``\xNN`` (hex escape, see below)
- ``\uNNNN`` (unicode escape, see below)

``\xNN`` takes a hex value and inserts the appropriate byte, while ``\uNNNN`` takes a Unicode codepoint and inserts an UTF-8 sequence.

.. note::

    Until version 0.8.0 there were three additional escape sequences: ``\b``, ``\f`` and ``\v``.
    They are commonly available in other languages but rarely needed in practice.
    If you do need them, they can still be inserted via hexadecimal escapes, i.e. ``\x08``, ``\x0c``
    and ``\x0b``, respectively, just as any other ASCII character.

The string in the following example has a length of ten bytes.
It starts with a newline byte, followed by a double quote, a single
quote a backslash character and then (without separator) the
character sequence ``abcdef``.

.. code-block:: solidity
    :force:

    "\n\"\'\\abc\
    def"

Any Unicode line terminator which is not a newline (i.e. LF, VF, FF, CR, NEL, LS, PS) is considered to
terminate the string literal. Newline only terminates the string literal if it is not preceded by a ``\``.

Unicode Literals
----------------

While regular string literals can only contain ASCII, Unicode literals – prefixed with the keyword ``unicode`` – can contain any valid UTF-8 sequence.
They also support the very same escape sequences as regular string literals.

.. code-block:: solidity

    string memory a = unicode"Hello 😃";

.. index:: literal, bytes

Hexadecimal Literals
--------------------

Hexadecimal literals are prefixed with the keyword ``hex`` and are enclosed in double
or single-quotes (``hex"001122FF"``, ``hex'0011_22_FF'``). Their content must be
hexadecimal digits which can optionally use a single underscore as separator between
byte boundaries. The value of the literal will be the binary representation
of the hexadecimal sequence.

Multiple hexadecimal literals separated by whitespace are concatenated into a single literal:
``hex"00112233" hex"44556677"`` is equivalent to ``hex"0011223344556677"``

Hexadecimal literals behave like :ref:`string literals <string_literals>` and have the same convertibility restrictions.

.. index:: enum

.. _enums:

Enums
-----

Enums are one way to create a user-defined type in Solidity. They are explicitly convertible
to and from all integer types but implicit conversion is not allowed.  The explicit conversion
from integer checks at runtime that the value lies inside the range of the enum and causes a
:ref:`Panic error<assert-and-require>` otherwise.
Enums require at least one member, and its default value when declared is the first member.
Enums cannot have more than 256 members.

The data representation is the same as for enums in C: The options are represented by
subsequent unsigned integer values starting from ``0``.

Using ``type(NameOfEnum).min`` and ``type(NameOfEnum).max`` you can get the
smallest and respectively largest value of the given enum.


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
    Enums can also be declared on the file level, outside of contract or library definitions.

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

Notice how ``UFixed256x18.wrap`` and ``FixedMath.toUFixed256x18`` have the same signature but
perform two very different operations: The ``UFixed256x18.wrap`` function returns a ``UFixed256x18``
that has the same data representation as the input, whereas ``toUFixed256x18`` returns a
``UFixed256x18`` that has the same numerical value.

.. index:: ! function type, ! type; function

.. _function_types:

Function Types
--------------

Function types are the types of functions. Variables of function type
can be assigned from functions and function parameters of function type
can be used to pass functions to and return functions from function calls.
Function types come in two flavours - *internal* and *external* functions:

Internal functions can only be called inside the current contract (more specifically,
inside the current code unit, which also includes internal library functions
and inherited functions) because they cannot be executed outside of the
context of the current contract. Calling an internal function is realized
by jumping to its entry label, just like when calling a function of the current
contract internally.

External functions consist of an address and a function signature and they can
be passed via and returned from external function calls.

Function types are notated as follows:

.. code-block:: solidity
    :force:

    function (<parameter types>) {internal|external} [pure|view|payable] [returns (<return types>)]

In contrast to the parameter types, the return types cannot be empty - if the
function type should not return anything, the whole ``returns (<return types>)``
part has to be omitted.

By default, function types are internal, so the ``internal`` keyword can be
omitted. Note that this only applies to function types. Visibility has
to be specified explicitly for functions defined in contracts, they
do not have a default.

Conversions:

A function type ``A`` is implicitly convertible to a function type ``B`` if and only if
their parameter types are identical, their return types are identical,
their internal/external property is identical and the state mutability of ``A``
is more restrictive than the state mutability of ``B``. In particular:

- ``pure`` functions can be converted to ``view`` and ``non-payable`` functions
- ``view`` functions can be converted to ``non-payable`` functions
- ``payable`` functions can be converted to ``non-payable`` functions

No other conversions between function types are possible.

The rule about ``payable`` and ``non-payable`` might be a little
confusing, but in essence, if a function is ``payable``, this means that it
also accepts a payment of zero Ether, so it also is ``non-payable``.
On the other hand, a ``non-payable`` function will reject Ether sent to it,
so ``non-payable`` functions cannot be converted to ``payable`` functions.

If a function type variable is not initialised, calling it results
in a :ref:`Panic error<assert-and-require>`. The same happens if you call a function after using ``delete``
on it.

If external function types are used outside of the context of Solidity,
they are treated as the ``function`` type, which encodes the address
followed by the function identifier together in a single ``bytes24`` type.

Note that public functions of the current contract can be used both as an
internal and as an external function. To use ``f`` as an internal function,
just use ``f``, if you want to use its external form, use ``this.f``.

A function of an internal type can be assigned to a variable of an internal function type regardless
of where it is defined.
This includes private, internal and public functions of both contracts and libraries as well as free
functions.
External function types, on the other hand, are only compatible with public and external contract
functions.
Libraries are excluded because they require a ``delegatecall`` and use :ref:`a different ABI
convention for their selectors <library-selectors>`.
Functions declared in interfaces do not have definitions so pointing at them does not make sense either.

Members:

External (or public) functions have the following members:

* ``.address`` returns the address of the contract of the function.
* ``.selector`` returns the :ref:`ABI function selector <abi_function_selector>`

.. note::
  External (or public) functions used to have the additional members
  ``.gas(uint)`` and ``.value(uint)``. These were deprecated in Solidity 0.6.2
  and removed in Solidity 0.7.0. Instead use ``{gas: ...}`` and ``{value: ...}``
  to specify the amount of gas or the amount of wei sent to a function,
  respectively. See :ref:`External Function Calls <external-function-calls>` for
  more information.

Example that shows how to use the members:

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

Example that shows how to use internal function types:

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

Another example that uses external function types:

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
    Lambda or inline functions are planned but not yet supported.
