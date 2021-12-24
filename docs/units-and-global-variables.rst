**************************************
واحدها و متغیرهای موجود در سطح جهانی
**************************************

.. index:: wei, finney, szabo, gwei, ether

واحدهای اتر
===========

یک عدد لیترال می‌تواند از پسوند  ``wei`` ، ``gwei`` یا  ``ether`` برای تعیین زیر-واحد  اتر استفاده کند، هنگامی 
که اعداد اتر بدون پسوند باشند،`Wei فرض می‌شوند.

.. code-block:: solidity
    :force:

    assert(1 wei == 1);
    assert(1 gwei == 1e9);
    assert(1 ether == 1e18);

تنها تأثیر پسوند زیر-واحد عبارت است از ضرب در توان ده.


.. note::

    واحدهای ``finney`` و  ``szabo`` در نسخه 0.7.0 حذف شده اند.

.. index:: time, seconds, minutes, hours, days, weeks, years

واحد‌های زمان (Time Units)
==========

پسوندهایی مانند  ``seconds`` ،  ``minutes``،  ``hours`` ،  ``days`` و  ``weeks`` پس از اعداد تحت اللفظی 
می‌توانند برای تعیین واحدهای زمانی که ثانیه واحد اصلی است و واحدها به صورت ساده لوحانه در نظر گرفته 
می شوند، استفاده شوند:

* ``1 == 1 seconds``
* ``1 minutes == 60 seconds``
* ``1 hours == 60 minutes``
* ``1 days == 24 hours``
* ``1 weeks == 7 days``

اگر محاسبات تقویم را با استفاده از این واحدها انجام می‌دهید، مراقب باشید، زیرا هر سال معادل 365 روز نیست 
و حتی هر روز به دلیل `ثانیه‌های کبیسه <https://en.wikipedia.org/wiki/Leap_second>`_  24 ساعت نیست. با توجه به اینکه ثانیه‌های کبیسه را نمی‌توان پیش 
بینی کرد، یک کتابخانه تقویم دقیق باید توسط یک اوراکل خارجی بِروز شود. 


.. note::

    پسوند  ``years`` به دلایل بالا در نسخه 0.5.0 حذف شده است.

   
این پسوندها را نمی‌توان روی متغیرها اعمال کرد. به عنوان مثال، اگر می‌خواهید پارامتر تابع را در روز تفسیر  
کنید، می‌توانید به روش زیر عمل کنید:


.. code-block:: solidity

    function f(uint start, uint daysAfter) public {
        if (block.timestamp >= start + daysAfter * 1 days) {
          // ...
        }
    }

.. _special-variables-functions:

متغیرها و توابع ویژه (Special Variables and Functions)
===============================

متغیرها و توابع خاصی وجود دارند که همیشه در فضای نام جهانی  وجود دارند و عمدتاً برای ارائه اطلاعات در 
مورد بلاکچین یا توابع کاربردی عمومی استفاده می‌شوند.


.. index:: abi, block, coinbase, difficulty, encode, number, block;number, timestamp, block;timestamp, msg, data, gas, sender, value, gas price, origin


بلاک و ویژگی‌های تراکنش
--------------------------------

-	``blockhash(uint blockNumber) returns (bytes32)``  : هش بلاک داده شده زمانی که ``blocknumber`` یکی از 256 بلاک اخیر باشد. در غیر این صورت صفر را برمی گرداند.
-   ``block.basefee`` (``uint``): بِیس فی بلاک فعلی ( `EIP-3198 <https://eips.ethereum.org/EIPS/eip-3198>`_ و `EIP-1559 <https://eips.ethereum.org/EIPS/eip-1559>`_)
-	``block.chainid (uint)`` :  آی دی (id) زنجیره فعلی
-	``block.coinbase (address payable)`` : آدرس فعلی استخراج کننده بلاک
-	``block.difficulty (uint)`` : سختی بلاک فعلی
-	``block.gaslimit (uint)``:   گَس لیمیت (gaslimit) بلاک فعلی  
-	``block.number (uint)`` : شماره بلاک فعلی
-	``block.timestamp (uint)`` : تایم استمپ (timestamp) بلاک فعلی به عنوان ثانیه از دوران یونیکس
-	``gasleft() returns (uint256)`` : گَس باقی مانده
-	``msg.data (bytes calldata)`` : کالدیتا (calldata)  کامل
-	``msg.sender (address)`` : فرستنده پیام (فراخوانی فعلی)
-	``msg.sig (bytes4)`` : چهار بایت اول calldata  ( مانند تابع identifier )
-	``msg.value (uint)`` : تعداد wei ارسال شده با پیام
-	``tx.gasprice (uint)`` : قیمت گَس  تراکنش
-	``tx.origin (address)`` : فرستنده تراکنش (فراخوانی کامل زنجیره)


.. note::

    مقادیر همه اعضای  ``msg`` ، از جمله ``msg.sender``  و  ``msg.value`` می‌تواند برای هر فراخوانی تابع 
    خارجی تغییر کند. این شامل تماس با توابع کتابخانه است.

    The values of all members of ``msg``, including ``msg.sender`` and
    ``msg.value`` can change for every **external** function call.
    This includes calls to library functions.

.. note::

    When contracts are evaluated off-chain rather than in context of a transaction included in a
    block, you should not assume that ``block.*`` and ``tx.*`` refer to values from any specific
    block or transaction. These values are provided by the EVM implementation that executes the
    contract and can be arbitrary.

.. note::

       به  ``block.timestamp`` یا  ``blockhash`` به عنوان منبع تصادفی اعتماد نکنید، مگر اینکه بدانید در حال انجام چه کاری هستید. 

    timestamp و   block hash تا حدی می‌توانند تحت تأثیر ماینرها قرار بگیرند. به عنوان مثال بازیگران 
    بد در جامعه ماینینگ می‌توانند تابع payout  کازینو را روی یک هش انتخاب شده اجرا کرده و در صورت 
    عدم دریافت پول، یک هش متفاوت را دوباره امتحان کنند.

    زمانبندی بلاک فعلی باید بسیار بزرگتر از زمانبندی آخرین بلاک باشد، اما تنها تضمین در جایی است که بین 
    زمانبندی دو بلاک متوالی در زنجیره متعارف  قرار گیرد.


.. note::

    هش بلاک به دلایل مقیاس پذیری برای همه بلاک‌ها در دسترس نیست. فقط می‌توانید به هش‌های 256 
    بلاک جدید دسترسی پیدا کنید، همه مقادیر دیگر صفر خواهند بود.



.. note::

    تابع  ``blockhash`` قبلاً به عنوان  ``block.blockhash`` شناخته میشد، که در نسخه 0.4.22 
    منسوخ شده بود و در نسخه 0.5.0 حذف شد.

 
.. note::

    تابع  ``gasleft`` قبلاً به عنوان  ``msg.gas`` شناخته میشد، که در نسخه 0.4.21 منسوخ شده بود و در 
    نسخه 0.5.0 حذف شده بود.

 
.. note::

    در نسخه 0.7.0 ، نام مستعار  ``now`` (برای  ``block.timestamp`` ) حذف شد.


.. index:: abi, encoding, packed

توابع رمزگذاری و رمزگشایی ABI :
-----------------------------------

// saracodic comment آ for be sorted in text

- ``abi.decode(bytes memory encodedData, (...)) returns (...)`` : آABI داده‌های داده شده را رمزگشایی میکند، در حالی که انواع  آن در پرانتز به عنوان آرگومان دوم آورده شده است. مثال:
 ``(uint a, uint[2] memory b, bytes memory c) = abi.decode(data, (uint, uint[2], bytes))``
- ``abi.encode(...) returns (bytes memory)`` : آ ABI-encodes آرگومان‌های داده شده
- ``abi.encodePacked(...) returns (bytes memory)`` :  آ :ref:`packed encoding <abi_packed_mode>` آرگومان‌های داده شده را انجام می‌دهد. توجه داشته باشید که رمزگذاری بسته بندی شده می‌تواند مبهم باشد !
- ``abi.encodeWithSelector(bytes4 selector, ...) returns (bytes memory)`` : آBI-encodes آرگومان‌های داده شده را از دوم شروع می‌کند و انتخاب کننده چهار بایت داده شده را اضافه می‌کند.
- ``abi.encodeWithSignature(string memory signature, ...) returns (bytes memory)`` : معادل با  ``abi.encodeWithSelector(bytes4(keccak256(bytes(signature))), ...)`` است.

.. note::

    از این توابع رمزگذاری می‌توان برای ایجاد داده برای فراخوانی توابع خارجی بدون فراخوانی یک تابع خارجی 
    استفاده کرد. علاوه بر این، ``keccak256(abi.encodePacked(a, b))``  روشی برای محاسبه 
    هش داده‌های ساختار یافته است (اگرچه توجه داشته باشید که امکان ایجاد "تصادم هش " با استفاده از انواع 
    مختلف پارامترهای تابع وجود دارد).

    
برای اطلاع از جزئیات مربوط به رمزگذاری، مستند مربوط به :ref:`ABI <ABI>` و  :ref:`tightly packed encoding <abi_packed_mode>`  مراجعه کنید.



.. index:: bytes members

اعضای بایت(Members of bytes)
----------------

- ``bytes.concat(...) returns (bytes memory)`` : :ref:`تعداد متغیر bytes و bytes1 ،… ، bytes32 آرگومان را به یک آرایه بایت متصل می‌کند<bytes-concat>`


.. index:: assert, revert, require

مدیریت خطا (Error Handling)
--------------

برای جزئیات بیشتر در مورد مدیریت خطا و زمان استفاده از کدام تابع، به بخش اختصاصی :ref:`assert and require<assert-and-require>` مراجعه کنید.




``assert(bool condition)``

    در صورت برآورده نشدن شرایط برای استفاده از خطاهای داخلی، باعث ایجاد خطای Panic و در نتیجه برگشت تغییر حالت می‌شود.

  

``require(bool condition)``

     اگر شرایط برآورده نشود revert  می‌شود- برای خطاهای ورودی یا کامپوننت‌ های خارجی استفاده می‌شود.



``require(bool condition, string memory message)``

    اگر شرط برآورده نشود برمی‌گردد - برای خطاهای ورودی یا کامپوننت‌های خارجی استفاده می‌شود. همچنین یک پیام خطا ارائه می‌دهد.

 
``revert()``

    اجرا را لغو کرده و تغییرات حالت را برگرداند.


``revert(string memory reason)``

    اجرا را لغو کرده و تغییرات حالت را برمیگرداند، یک رشته  توضیحی ارائه دهید.


.. index:: keccak256, ripemd160, sha256, ecrecover, addmod, mulmod, cryptography,

.. _mathematical-and-cryptographic-functions:

توابع ریاضی و رمزنگاری
----------------------------------------

// comment saracodic  ``(x + y) % k`` 

``addmod(uint x, uint y, uint k) returns (uint)``
    محاسبه  ``(x + y) % k`` که در آن ضرب با دقت دلخواه انجام می‌شود و در  ``2**256`` جمع نمی‌شود. 
    اثبات کنید  که   ``k != 0`` از نسخه 0.5.0 شروع می‌شود.

``mulmod(uint x, uint y, uint k) returns (uint)``


    compute ``(x * y) % k`` where the multiplication is performed with arbitrary precision and does not wrap around at ``2**256``. Assert that ``k != 0`` starting from version 0.5.0.

``keccak256(bytes memory) returns (bytes32)``

    هش Keccak-256 ورودی را محاسبه می‌کند.


.. note::

    قبلاً نام مستعار ``keccak256`` با نام ``sha3`` وجود داشت که در نسخه 0.5.0 حذف شد.



``sha256(bytes memory) returns (bytes32)``

    هش SHA-256 ورودی را محاسبه کنید

    

``ripemd160(bytes memory) returns (bytes20)``

    هش RIPEMD-160 ورودی را محاسبه کنید
    

``ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)``

    آدرس مربوط به کلید عمومی را از امضای منحنی بیضوی  بازیابی کنید یا خطا را صفر کنید. پارامترهای تابع با مقادیر ECDSA امضا مطابقت دارد:

 
    * ``r``  = اولین 32 بایت  امضا 
    * ``s``  = دوم 32 بایت امضا
    * ``v``  =  یک بایت (1 byte) نهایی امضا


    ``ecrecover``  یک  ``address`` را برمی‌گرداند و یک   ``address payable`` نیست. در صورت نیاز 
    به انتقال وجه به آدرس بازیابی شده،  :ref:`address payable<address>` برای تبدیل را ببینید.
 

   
       برای جزئیات بیشتر، `example usage <https://ethereum.stackexchange.com/questions/1777/workflow-on-signing-a-string-with-private-key-followed-by-signature-verificatio>`_ را مطالعه کنید.


.. warning::

    اگر از    ``ecrecover`` استفاده می‌کنید، توجه داشته باشید که بدون نیاز به آگاهی از کلید خصوصی مربوطه، 
    می‌توانید یک امضای معتبر را به امضای معتبر دیگری تبدیل کنید. در هارد فورک Homestead، این مسئله 
    برای امضای _transaction_ ثابت شد ( `EIP-2 <https://eips.ethereum.org/EIPS/eip-2#specification>`_ را مشاهده کنید )، اما تابع ecrecover بدون تغییر باقی ماند.
    این معمولاً مشکلی ایجاد نمی‌کند مگر اینکه شما نیاز به امضا برای منحصر به فرد بودن یا استفاده از آنها برای 

    شناسایی آیتم‌ها داشته باشید. OpenZeppelin `دارای یک کتابخانه کمکی ECDSA <https://docs.openzeppelin.com/contracts/2.x/api/cryptography#ECDSA>`_ است که می‌توانید 
    بدون این مشکل به عنوان یک  wrapper برای   ``ecrecover`` از آن استفاده کنید.
    

.. note::

    هنگام اجرای  ``sha256`` ،  ``ripemd160`` یا  ``ecrecover`` در *بلاکچین خصوصی* ، ممکن است با Out-of-Gas روبرو شوید. 
    این به این دلیل است که این توابع به عنوان "قراردادهای از پیش تدوین شده" اجرا می‌شوند و تنها پس از دریافت اولین پیام (اگرچه کد قرارداد آنها دارای hardcoded است) واقعاً وجود دارند. 
    پیام به قراردادهای غیر موجود  گرانتر است و بنابراین ممکن است اجرا با خطای Out-of-Gas مواجه شود. 
    یک راه حل برای این مشکل این است که قبل از استفاده از Wei  (به عنوان مثال 1) به هر یک از قراردادها، 
    آنها را در قراردادهای واقعی خود بفرستید. این مسئله در شبکه اصلی یا آزمایشی نیست.

.. index:: balance, codehash, send, transfer, call, callcode, delegatecall, staticcall

.. _address_related:

اعضای انواع آدرس Members of Address Types
------------------------

//saracodic swapping text

``<address>.balance`` (``uint256``)

    بالانس :ref:`آدرس<address>` در Wei


    

``<address>.code`` (``bytes memory``)

    کد در :ref:`آدرس<address>` (می‌تواند خالی باشد)

 

``<address>.codehash`` (``bytes32``)

    هش کد  :ref:`آدرس<address>`

    the codehash of the :ref:`address`

``<address payable>.transfer(uint256 amount)``

    ارسال مقدار داده شده از Wei به :ref:`آدرس<address>` ، در صورت شکست  بازگردانده می‌شود، ارسال کارمزد  2300  گَس، غیر قابل قابل تنظیم 



``<address payable>.send(uint256 amount) returns (bool)``

    ارسال مقدار داده شده Wei به :ref:`آدرس<address>` ، در صورت خرابی  ``false`` برمیگرداند، ارسال کارمزد 2300  گَس، غیر قابل تنظیم 


``<address>.call(bytes memory) returns (bool, bytes memory)``
    
    صدور  ``CALL`` سطح پایین با پیلود  داده شده، شرایط موفقیت را برمی‌گرداند و داده‌ها را برمی‌گرداند، ارسال تمام گَس موجود، قابل تنظیم 

 
``<address>.delegatecall(bytes memory) returns (bool, bytes memory)``
   
   صدور ``DELEGATECALL`` سطح پایین  با پیلود داده شده، شرط موفقیت را برمی‌گرداند و داده ها را برمی‌گرداند، ارسال تمام گَس موجود، قابل تنظیم 

 
``<address>.staticcall(bytes memory) returns (bool, bytes memory)``
    
    صدور سطح پایین  ``STATICCALL`` با میزان پیلود داده شده، شرط موفقیت را برمی‌گرداند و داده‌ها را برمی‌گرداند، ارسال تمام گَس موجود، قابل تنظیم 


برای اطلاعات بیشتر به بخش :ref:`آدرس<address>` مراجعه کنید.

.. warning::

    هنگام اجرای تابع قرارداد دیگر، تا جایی که ممکن است از استفاده از  ``()call.`` خودداری کنید زیرا مانع 
    از بررسی نوع، بررسی وجود تابع و بسته بندی  آرگومان می‌شود.

.. warning::

    برخی از خطرات استفاده از  ``send`` وجود دارد: اگر عمق فراخوانی پشته 1024 باشد، انتقال با شکست مواجه 
    می‌شود (این امر همیشه می‌تواند توسط تماس گیرنده اجبار شود) و همچنین در صورت تمام شدن گَس گیرنده 
    با شکست مواجه می‌شود. بنابراین، برای انجام انتقال ایمن اتر، همیشه مقدار بازگشتی  ``send`` را بررسی کنید، 
    یا حتی بهتر است از  ``transfer`` استفاده کنید: از الگویی استفاده کنید که گیرنده پول را برداشت می‌کند.

   

.. warning::

    با توجه به این واقعیت که EVM فراخوانی یک قرارداد غیر موجود را همیشه موفق می‌داند، سالیدیتی شامل 
    یک بررسی اضافی با استفاده از آپکد  ``extcodesize`` هنگام انجام فراخوانی‌های خارجی است. این تضمین 
    می‌کند که قراردادی که قرار است نامیده شود یا واقعاً وجود دارد (حاوی کد است) یا یک استثناء  مطرح 
    می‌شود.

   

//saracodic swapping text

    فراخوانی‌های سطح پایین که بیشتر از آدرسها استفاده می‌کنند تا موارد قرارداد   
    (i.e. ``.call()``, ``.delegatecall()``, ``.staticcall()``, ``.send()`` and ``.transfer()``)
    شامل این بررسی نمی‌شوند، که از نظر گَس آنها را ارزان تر می‌کند اما از ایمنی کمتری برخوردار هستند.

    The low-level calls which operate on addresses rather than contract instances (i.e. ``.call()``,
    ``.delegatecall()``, ``.staticcall()``, ``.send()`` and ``.transfer()``) **do not** include this
    check, which makes them cheaper in terms of gas but also less safe.

.. note::

   قبل از نسخه 0.5.0، سالیدیتی به اعضای  آدرس اجازه می‌داد تا با یک مورد قرارداد، به عنوان 
    مثال  ``this.balance`` ، دسترسی داشته باشند. این در حال حاضر ممنوع است و تبدیل صریح  به آدرس 
    باید انجام شود:  ``address(this).balance`` .

   
.. note::

    اگر به متغیرهای حالت از طریق "delegatecall" سطح پایین دسترسی پیدا کنید، طرح storage دو 
    قرارداد باید همسو باشد تا قرارداد فراخوانی شده به درستی به نام متغیرهای storage قرارداد فراخوانی شده 
    دسترسی پیدا کند. البته اگر اشاره‌گرهای  storage به عنوان آرگومان‌های تابع مانند کتابخانه‌های سطح بالا 
    ارسال شوند، اینطور نیست.

  // saracodic comment: swapping text

.. note::

    قبل از نسخه 0.5.0 ،   ``call.``  ،    ``delegatecall.``  و   ``staticcall.``  فقط وضعیت موفقیت  را برمی‌گرداند و نه داده‌های بازگشتی.

    Prior to version 0.5.0, ``.call``, ``.delegatecall`` and ``.staticcall`` only returned the
    success condition and not the return data.

.. note::

    قبل از نسخه 0.5.0، یکی از اعضا به نام  ``callcode`` با معنایی مشابه اما کمی متفاوت از  ``delegatecall`` وجود داشت.

  


.. index:: this, selfdestruct

مربوط به قرارداد (Contract Related)
----------------

``this`` (نوع قرارداد فعلی)

    قرارداد فعلی، به صراحت قابل تبدیل به :ref:`آدرس<address>` است


``selfdestruct(address payable recipient)``

    قرارداد فعلی را از بین ببرید، وجوه آن را به :ref:`آدرس<address>`  داده شده ارسال کرده و اجرا را پایان دهید. توجه داشته باشید 
    که  ``selfdestruct`` برخی از ویژگی‌های به ارث برده شده از EVM را دارد:



    -	تابع دریافت قراردادِ دریافت کننده اجرا نمی‌شود.
    -	قرارداد واقعاً در پایان تراکنش از بین می‌رود و ``revert``  ممکن است تخریب را "undo" کند.



علاوه بر این، تمام توابع قرارداد فعلی مستقیماً از جمله تابع فعلی قابل فراخوانی هستند.



.. note::

    قبل از نسخه 0.5.0 ، تابعی به نام  ``suicide`` با معنایی  مشابه  ``selfdestruct`` وجود داشت.
    
  
.. index:: type, creationCode, runtimeCode

.. _meta-type:

اطلاعات نوع (Type Information)
----------------

از عبارت  ``type(X)`` می‌توان برای بازیابی اطلاعات مربوط به ``type X`` استفاده کرد. در حال حاضر، پشتیبانی 
محدودی برای این ویژگی وجود دارد ( ``X``  می‌تواند یک قرارداد یا یک نوع صحیح باشد) اما ممکن است در آینده 
گسترش یابد.



ویژگیهای زیر برای قرارداد نوع  ``C`` موجود است:


``type(C).name``

    نام قرارداد.



``type(C).creationCode``

    آرایه بایت مِمٌوری که شامل بایت کد ایجاد قرارداد می‌باشد. این را می‌توان در اسمبلی داخلی برای ایجاد روال‌های 
    build custom، به ویژه با استفاده از آپکد ``create2``  استفاده کرد. این ویژگی در خود قرارداد یا هر 
    قرارداد مشتق شده قابل دسترسی نیست. این باعث می‌شود که بایت‌کد در بایت‌کد سایت فراخوانی قرار گیرد و 
    بنابراین رفرنس‌های circular  مانند آن امکان پذیر نیستند.

 

``type(C).runtimeCode``

    آرایه بایت مِمُوری که شامل بایت کد زمان اجرای قرارداد است. این کدی است که معمولاً توسط کانستراکتور   ``C`` . 
    دیپلوی می‌شود. اگر  ``C`` دارای کانستراکتوری باشد که از اسمبلی درون خطی استفاده می‌کند، ممکن است با 
    بایت‌کد دیپلوی شده متفاوت باشد. همچنین توجه داشته باشید که کتابخانه‌ها برای محافظت در برابر فراخوانی‌های 
    منظم، بایت‌کد زمان اجرا خود را در زمان دیپلوی تغییر می‌دهند. محدودیت‌های مشابه ``creationCode.``  
    نیز برای این ویژگی اعمال می‌شود.
 

علاوه بر ویژگی‌های بالا، ویژگی‌های زیر برای اینترفیس تایپ   ``I``  موجود است:



``type(I).interfaceId``:

    مقدار ``bytes4``  حاوی مشخص کننده   اینترفیس `EIP-165 <https://eips.ethereum.org/EIPS/eip-165>`_ اینترفیس ``I`` داده شده‌است. این مشخص 
    کننده به عنوان ``XOR``  ، همه انتخاب کننده‌های  تابع تعریف شده در داخل اینترفیس تعریف می‌شود - بدون 
    حذف همه توابع به ارث برد شده.


خواص زیر برای یک عدد صحیح تایپ  ``T`` موجود است:


``type(T).min``

    کوچکترین مقدار با تایپ  ``T`` قابل نمایش است.



``type(T).max``

    بزرگترین مقدار با تایپ  ``T`` قابل نمایش است.

    
