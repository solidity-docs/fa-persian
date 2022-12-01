********************************
تغییرات خاص ورژن 0.5.0 سالیدیتی
********************************

این بخش، تغییرات اصلی را که در سالیدیتی نسخه 0.5.0 معرفی شده است، به همراه استدلال پشت این
تغییرات و نحوه به‌روزرسانی کدهای تحت تأثیر، برجسته می‌کند. برای لیست کامل، `تغییرات انتشار <https://github.com/ethereum/solidity/releases/tag/v0.5.0>`_. را بررسی
کنید.


.. note::
   قراردادهای کامپایل شده با سالیدیتی نسخه 0.5.0 همچنان می توانند با قراردادها و حتی کتابخانه هایی
   که با نسخه های قدیمی کامپایل شده اند، بدون کامپایل مجدد یا استقرار مجدد آنها ارتباط برقرار کنند. تغییر
   اینترفیس‌ها برای گنجاندن لوکیشین های داده و مشخص‌کننده‌های قابل مشاهده و تغییرپذیری کافی است.
   بخش :ref:`همکاری با قراردادهای قدیمی <interoperability>` را
   در زیر ببینید.

فقط به صورت معنایی تغییر می کند
===============================

این بخش تغییراتی را لیست می‌کند که فقط معنایی هستند، بنابراین به طور بالقوه رفتارهای جدید و متفاوت را
در کد موجود پنهان می‌کنند.

* شیفت به راست امضا شده اکنون به جای گرد کردن به سمت صفر، از شیفت حسابی مناسب استفاده می
  کند، یعنی گرد کردن به سمت بی نهایت منفی.  شیفت امضا یا ساین شده و بدون امضا دارای کدهای
  عملیاتی اختصاصی خواهند بود و در حال حاضر توسط سالیدیتی تقلید می شوند.

* دستور ``continue`` در یک حلقه ``do...while`` اکنون به شرط می رود، که رفتار رایج در چنین مواردی
  است.بنابراین، اگر شرط نادرست باشد، حلقه خاتمه می یابد.

* توابع ``.call()``، ``.delegatecall()`` و ``.staticcall()`` دیگر هنگامی که یک پارامتر بایتی به آن داده می شود، پد
  نمی شوند.

* اگر نسخه EVM بیزانتیوم یا جدیدتر باشد، اکنون توابع خالص و view با استفاده از کد opcode
  ``STATICCALL`` به جای ``CALL`` فراخوانی می شوند. این تغییر حالت را در سطح EVM مجاز نمی‌داند.

* رمزگذار ABI اکنون به درستی آرایه ها و رشته های بایت را از داده های فراخوانی (``msg.data`` و پارامترهای
  تابع خارجی) هنگامی که در فراخوانی های تابع خارجی و در ``abi.encode`` استفاده می شود، پاک می کند. برای
  رمزگذاری بدون پد، از ``abi.encodePacked`` استفاده کنید.

* رمزگشای ABI در ابتدای توابع و در ``abi.decode()`` در صورتی که داده فراخوانی ارسال شده خیلی کوتاه
  باشد یا خارج از محدوده باشد، برمی گردد. توجه داشته باشید که بیت های مرتبه بالاتر هنوز نادیده گرفته می شوند.

* همه گس های موجود را با فراخوانی تابع خارجی با شروع از Tangerine Whistle هدایت کنید.

تغییرات معنایی و نحوی
==============================

این بخش تغییراتی را که بر نحو و معنایی تأثیر می گذارد برجسته می کند.

* توابع ``.call()``، ``.delegatecall()``، ``staticcall()``، ``keccak256()``، ``sha256()`` و ``ripemd160()`` اکنون فقط
  یک آرگومان بایتی را می پذیرند. این تغییر واضح‌تر و روشن‌تر می باشد که چگونه استدلال‌ها به هم پیوسته‌اند.
  هر .``.call()`` (و خانواده) به یک ``.call("")`` و هر ``.call(signature, a,
  b, c)`` برای استفاده از ``.call(abi.encodeWithSignature(signature, a, b, c))`` (آخرین مورد فقط برای انواع مقدار کار می کند). هر
  ``keccak256(a, b, c)`` را به  ``keccak256(abi.encodePacked(a, b, c))`` تغییر دهید. حتی اگر این یک تغییر
  قطعی نیست، پیشنهاد می شود که توسعه دهندگان ``x.call(bytes4(keccak256("f(uint256)")), a, b)`` را به
  ``x.call(abi.encodeWithSignature("f(uint256)", a, b))`` تغییر دهند.

* توابع ``.call()``، ``.delegatecall()`` و ``.staticcall()`` اکنون برمی گردند ``(bool, bytes memory)`` تا دسترسی به
  داده های برگشتی را فراهم کنند. ``bool success = otherContract.call("f")`` را به ``(bool success, bytes memory
  data) = otherContract.call("f")`` تغییر دهید.

* سالیدیتی اکنون قوانین محدوده بندی به سبک C99 را برای متغیرهای محلی تابع پیاده سازی می کند، یعنی
  متغیرها را فقط می توان پس از اعلام و فقط در محدوده های مشابه یا تودرتو مورد استفاده قرار داد.
  متغیرهای اعلام شده در بلوک اولیه یک حلقه ``for`` در هر نقطه از حلقه معتبر هستند.

الزامات صراحت
=========================

این بخش تغییراتی را لیست می‌کند که اکنون کد باید واضح‌تر باشد. برای بیشتر موضوعات، کامپایلر پیشنهاداتی
را ارائه می دهد.

* اکنون مشاهده عملکرد صریح اجباری است. عمومی ``public`` را به هر تابع و سازنده اضافه کنید، و به هر
  توابع بازگشتی یا رابطی که نمایان بودن آن را قبلاً مشخص نکرده است، خارجی ``external`` اضافه کنید.

<<<<<<< HEAD
* مکان داده صریح برای همه متغیرهای ساختار، آرایه یا انواع نگاشت اکنون اجباری است. این همچنین برای
  پارامترهای تابع و متغیرهای بازگشتی اعمال می شود. برای مثال، ``uint[] x = m_x`` را به ``uint[]`` ذخیره سازی
  x = m_x، و تابع ``function f(uint[][] x)`` را به تابع ``function f(uint[][] memory x)`` تغییر دهید که در آن حافظه ``memory`` مکان داده است و
  ممکن است بر این اساس با ذخیره سازی یا ``calldata`` جایگزین شود. توجه داشته باشید که توابع خارجی به
  پارامترهایی با مکان داده ``calldata`` نیاز دارند.
=======
* Explicit data location for all variables of struct, array or mapping types is
  now mandatory. This is also applied to function parameters and return
  variables.  For example, change ``uint[] x = z`` to ``uint[] storage x =
  z``, and ``function f(uint[][] x)`` to ``function f(uint[][] memory x)``
  where ``memory`` is the data location and might be replaced by ``storage`` or
  ``calldata`` accordingly.  Note that ``external`` functions require
  parameters with a data location of ``calldata``.
>>>>>>> c6ee18a5072641f42d5514d9c4523e4708b9b2c7

* انواع قراردادها دیگر شامل اعضای آدرس ``address``نمی شوند تا فضاهای نام جدا شوند. بنابراین، اکنون لازم است که
  قبل از استفاده از یک عضو آدرس، مقادیر نوع قرارداد را به صراحت به آدرس تبدیل کنیم.
  مثال: اگر ``c`` یک قرارداد است،  ``c.transfer(...)`` را به ``address(c).transfer(...)`` و ``c.balance`` را به ``address(c).balance`` تغییر دهید.


* تبدیل صریح بین انواع قراردادهای نامرتبط اکنون مجاز نیست. شما فقط می توانید از نوع قراردادی به یکی
  از انواع پایه یا اجداد آن تبدیل کنید. اگر مطمئن هستید که یک قرارداد با نوع قراردادی که می‌خواهید به آن
  تبدیل کنید، سازگار است، اگرچه از آن ارث نمی‌برد، می‌توانید ابتدا با تبدیل به آدرس ``address``، این مشکل را برطرف کنید.
  مثال: اگر ``A` و ``B`` از انواع قرارداد هستند، ``B`` از ``A`` ارث نمی برد و ``b`` قراردادی از نوع ``B`` است، همچنان می توانید
  با استفاده از ``A(address(b))`` ``b`` را به نوع ``A`` تبدیل کنید. توجه داشته باشید که همانطور که در زیر توضیح داده
  شده است، همچنان باید مراقب تطبیق عملکردهای بازگشتی قابل پرداخت باشید.

* نوع آدرس به آدرس و آدرس قابل پرداخت تقسیم شد که در آن فقط آدرس قابل پرداخت عملکرد انتقال را
  ارائه می دهد. آدرس قابل پرداخت را می توان مستقیماً به آدرس تبدیل کرد، اما برعکس آن مجاز نیست.
  تبدیل آدرس به آدرس قابل پرداخت از طریق تبدیل از طریق ``uint160`` امکان پذیر است. اگر c یک قرارداد
  باشد، آدرس(c) تنها در صورتی منجر به آدرس قابل پرداخت می شود که ``c`` تابع بازگشتی قابل پرداخت داشته
  باشد. اگر از :ref:`الگوی برداشت<withdrawal_pattern>` استفاده می کنید، به احتمال زیاد مجبور نیستید کد خود را تغییر دهید زیرا انتقال به
  جای آدرس های ذخیره شده فقط در ``msg.sender`` استفاده می شود و ``msg.sender`` یک آدرس قابل
  پرداخت است.

* تبدیل بین ``bytesX`` و ``uintY`` با اندازه های مختلف اکنون به دلیل وجود لایه بایت ایکس در سمت راست و پد 
  ``uintY`` در سمت چپ که ممکن است منجر به نتایج غیرمنتظره تبدیل شود، مجاز نیست. اکنون اندازه باید در
  نوع قبل از تبدیل تنظیم شود. به عنوان مثال، با تبدیل متغیر ``bytes4`` به ``bytes8`` و سپس به ``uint64``، می
  توانید بایت ``bytes4`` (4 بایت) را به ``uint64`` (8 بایت) تبدیل کنید. هنگام تبدیل از طریق ``uint32``، padding مخالف
  دریافت می کنید. قبل از نسخه 0.5.0 هر تبدیل بین ``bytesX`` و ``uintY`` از طریق ``uint8X`` انجام می شد.
  به عنوان مثال ``uint8(bytes3(0x291807))`` به ``uint8(uint24(bytes3(0x291807)))`` تبدیل می شود (نتیجه ``0x07`` است).

* استفاده از ``msg.value`` در توابع غیر قابل پرداخت (یا معرفی آن از طریق یک modifier) به عنوان یک
  ویژگی امنیتی مجاز نیست.
  تابع را به قابل پرداخت ``payable`` تبدیل کنید یا یک تابع داخلی جدید برای منطق برنامه ایجاد کنید که از ``msg.value``
  استفاده می کند.

* به دلایل وضوح، رابط خط فرمان اکنون نیاز دارد، ``-`` اگر ورودی استاندارد به عنوان منبع استفاده شود.

عناصر منسوخ شده
===================

این بخش تغییراتی را لیست می کند که ویژگی ها یا نحو قبلی را منسوخ می کند. توجه داشته باشید که بسیاری
از این تغییرات قبلاً در حالت آزمایشی نسخه ``v0.5.0`` فعال شده بودند.

خط فرمان(کامند لاین) و رابط های JSON
-------------------------------------

* گزینه خط فرمان  ``--formal`` (برای تولید خروجی Why3 برای تأیید رسمی بیشتر استفاده می شود) منسوخ
  شده و اکنون حذف شده است. یک ماژول تأیید رسمی جدید، SMTCchecker که 
  از طریق ``pragma experimental SMTChecker;`` آزمایشی پراگما فعال می شود.

* گزینه خط فرمان ``--julia`` به دلیل تغییر نام زبان میانی ``Julia`` به ``Yul`` به ``--yul`` تغییر نام داد.

* گزینه های خط فرمان ``--clone-bin`` و ``--combined-json clone-bin`` حذف شدند.

* مپینگ مجدد با پیشوند خالی مجاز نیست.

* فیلدهای JSON AST ثابت ``constant`` و قابل پرداخت ``payable`` حذف شدند. اطلاعات اکنون در قسمت ``stateMutability`` وجود دارد. 

* فیلد JSON AST سازنده نود ``FunctionDefinition`` با فیلدی به نام kind است که می تواند دارای مقدار
  ``"constructor"``، ``"fallback"`` یا ``"function"`` باشد.

* در فایل‌های هگز باینری غیرپیونده، متغیرهای آدرس کتابخانه اکنون 36 کاراکتر هگز اول هش keccak256
  نام کتابخانه کاملاً واجد شرایط هستند که با ``$...$`` احاطه شده‌اند. قبلاً فقط از نام کتابخانه کاملاً واجد شرایط
  استفاده می شد. این امر احتمال برخورد را کاهش می دهد، به خصوص زمانی که از مسیرهای طولانی
  استفاده می شود. فایل‌های باینری اکنون حاوی فهرستی از مپینگ ها از این مکان‌ها به نام‌های کاملا واجد
  شرایط هستند.

سازنده ها(Constructors)
------------------------

* اکنون سازنده ها باید با استفاده از کلمه کلیدی ``constructor`` تعریف شوند.

* فراخوانی سازنده های پایه بدون پرانتز اکنون مجاز نیست.

* تعیین آرگومان های سازنده پایه چندین بار در یک سلسله مراتب ارثی اکنون مجاز نیست.

* فراخوانی سازنده با آرگومان اما با تعداد آرگومان اشتباه اکنون مجاز نیست. اگر فقط می خواهید یک رابطه
  ارثی را بدون ارائه آرگومان مشخص کنید، اصلاً پرانتز ارائه نکنید.

توابع(Functions)
-----------------

* کد تماس تابع اکنون غیرمجاز است (به نفع تماس نمایندگی یا همان ``delegatecall``). هنوز هم امکان
  استفاده از آن از طریق اسمبلی درون خطی وجود دارد.

*  خودکشی(``suicide``) اکنون ممنوع است (به نفع خود تخریبی ``selfdestruct`` ).

* ``sha3`` اکنون غیرمجاز است (به نفع ``keccak256``).

* ``throw`` اکنون مجاز نیست (به نفع  ``revert``, ``require`` و ``assert``).

تبدیل ها
-----------

* تبدیل صریح و ضمنی از حروف اعشاری به انواع ``bytesXX`` اکنون مجاز نیست.

* تبدیل صریح و ضمنی از hex literals به انواع ``bytesXX`` با اندازه های مختلف اکنون مجاز نیست.

لفظ و پسوند
--------------

* به دلیل پیچیدگی ها و سردرگمی ها ،اکنون سال های ``years`` واحد مجاز نیست.

* نقطه های دنباله ای که عددی دنبال نمی شوند اکنون غیرمجاز هستند.

* ترکیب اعداد هگز با نام واحد مثلاً ``0x1e wei``) اکنون مجاز نیست.

* پیشوند ``0X`` برای اعداد هگز غیر مجاز است و فقط ``0x`` امکان پذیر است.

متغیرها
---------

* اکنون برای وضوح، اعلام ساختارهای ``constant`` خالی مجاز نیست.

* کلمه کلیدی ``var`` در حال حاضر غیرمجاز است تا صریح باشد.

* انتساب بین تاپل ها(tuple) با تعداد مؤلفه های مختلف اکنون مجاز نیست.

* مقادیر ثابت هایی که ثابت زمان کامپایل نیستند مجاز نیستند.

* اعلان های چند متغیره با تعداد مقادیر نامتناسب اکنون مجاز نیستند.

* متغیرهای ذخیره سازی اولیه غیر مجاز هستند.

* اجزای چندگانه خالی اکنون غیرمجاز هستند.

* تشخیص وابستگی های چرخه ای در متغیرها و ساختارها در بازگشت به 256 محدود می شود.

* آرایه های با اندازه ثابت با طول صفر اکنون مجاز نیستند.

سینتکس
--------

* استفاده از تغییرپذیری حالت ثابت به عنوان تابع اکنون مجاز نیست.

* عبارات بولین نمی توانند از عملیات حسابی استفاده کنند.

* اپراتور unary ``+`` اکنون غیرمجاز است.

* دیگر نمی توان از Literals با ``abi.encodePacked`` بدون تبدیل قبلی به یک نوع صریح استفاده کرد.

* عبارات بازگشتی خالی برای توابع با یک یا چند مقدار بازگشتی اکنون غیرمجاز هستند.

* سینتکس "loose assembly" در حال حاضر به طور کامل غیر مجاز است، یعنی، jump labels، جهش ها
  و دستورالعمل های غیر کاربردی دیگر نمی توانند استفاده شوند. به جای آن از ``while``، ``switch`` و ``if``
  استفاده کنید.

* توابع بدون پیاده سازی دیگر نمی توانند از modifier استفاده کنند.

* انواع تابع با مقادیر بازگشتی نامگذاری شده اکنون غیرمجاز هستند.

* اعلان‌های متغیر منفرد در داخل if/while/for بدنه‌هایی که بلوک نیستند اکنون غیرمجاز هستند.

* کلمات کلیدی جدید: ``calldata`` و ``constructor``.

* کلمات کلیدی رزرو شده جدید: ``alias``, ``apply``, ``auto``, ``copyof``,
  ``define``, ``immutable``, ``implements``, ``macro``, ``mutable``,
  ``override``, ``partial``, ``promise``, ``reference``, ``sealed``,
  ``sizeof``, ``supports``, ``typedef`` و ``unchecked``.

.. _interoperability:

قابلیت همکاری با قراردادهای قدیمی تر
=====================================

هنوز هم می‌توان با قراردادهایی که برای نسخه‌های سالیدیتی قبل از نسخه 5.5.0 (یا برعکس) نوشته شده‌اند،
با تعریف واسط‌هایی برای آنها ارتباط برقرار کرد. در نظر بگیرید که قرارداد زیر قبل از 0.5.0 را قبلاً مستقر
کرده اید:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.4.25;
    // This will report a warning until version 0.4.25 of the compiler
    // This will not compile after 0.5.0
    contract OldContract {
        function someOldFunction(uint8 a) {
            //...
        }
        function anotherOldFunction() constant returns (bool) {
            //...
        }
        // ...
    }

که با سالیدیتی نسخه 0.5.0 کامپایل نخواهد شد. با این حال، می توانید یک رابط سازگار برای آن تعریف کنید:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.5.0 <0.9.0;
    interface OldContract {
        function someOldFunction(uint8 a) external;
        function anotherOldFunction() external returns (bool);
    }

توجه داشته باشید که ما یک تابع ``OldFunction`` را به عنوان ``view`` اعلام نکردیم، علیرغم اینکه در قرارداد
اصلی ثابت ``constant`` اعلام شد که به این دلیل است که شروع با Solidity v0.5.0 ``staticcall`` برای فراخوانی توابع
``view`` استفاده می شود. قبل از نسخه 0.5.0، کلمه کلیدی ثابت اعمال نمی شد، بنابراین فراخوانی تابعی که با
``staticcall`` ثابت ``constant`` اعلام شده است، ممکن است بازگردانده شود، زیرا تابع ثابت ممکن است همچنان سعی کند
فضای ذخیره سازی را تغییر دهد. در نتیجه، هنگام تعریف یک رابط برای قراردادهای قدیمی، فقط باید از view
به جای ``constant`` استفاده کنید در صورتی که کاملاً مطمئن باشید که عملکرد با ``staticcall`` کار می کند.

با توجه به رابط تعریف شده در بالا، اکنون می توانید به راحتی از قرارداد پیش از 0.5.0 مستقر شده استفاده کنید:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.5.0 <0.9.0;

    interface OldContract {
        function someOldFunction(uint8 a) external;
        function anotherOldFunction() external returns (bool);
    }

    contract NewContract {
        function doSomething(OldContract a) public returns (bool) {
            a.someOldFunction(0x42);
            return a.anotherOldFunction();
        }
    }

به طور مشابه، کتابخانه‌های pre-0.5.0 را می‌توان با تعریف عملکردهای کتابخانه بدون پیاده‌سازی و ارائه
آدرس کتابخانه pre-0.5.0 در حین پیوند استفاده کرد (برای نحوه استفاده از کامپایلر خط فرمان برای پیوند، به
استفاده از :ref:`commandline-compiler` مراجعه کنید). :

.. code-block:: solidity

    // This will not compile after 0.6.0
    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.5.0;

    library OldLibrary {
        function someFunction(uint8 a) public returns(bool);
    }

    contract NewContract {
        function f(uint8 a) public returns (bool) {
            return OldLibrary.someFunction(a);
        }
    }


مثال
=======

مثال زیر یک قرارداد و نسخه به روز شده آن را برای سالیدیتی v0.5.0 با برخی از تغییرات ذکر شده در این
بخش نشان می دهد.

نسخه قدیمی:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.4.25;
    // This will not compile after 0.5.0

    contract OtherContract {
        uint x;
        function f(uint y) external {
            x = y;
        }
        function() payable external {}
    }

    contract Old {
        OtherContract other;
        uint myNumber;

        // Function mutability not provided, not an error.
        function someInteger() internal returns (uint) { return 2; }

        // Function visibility not provided, not an error.
        // Function mutability not provided, not an error.
        function f(uint x) returns (bytes) {
            // Var is fine in this version.
            var z = someInteger();
            x += z;
            // Throw is fine in this version.
            if (x > 100)
                throw;
            bytes memory b = new bytes(x);
            y = -3 >> 1;
            // y == -1 (wrong, should be -2)
            do {
                x += 1;
                if (x > 10) continue;
                // 'Continue' causes an infinite loop.
            } while (x < 11);
            // Call returns only a Bool.
            bool success = address(other).call("f");
            if (!success)
                revert();
            else {
                // Local variables could be declared after their use.
                int y;
            }
            return b;
        }

        // No need for an explicit data location for 'arr'
        function g(uint[] arr, bytes8 x, OtherContract otherContract) public {
            otherContract.transfer(1 ether);

            // Since uint32 (4 bytes) is smaller than bytes8 (8 bytes),
            // the first 4 bytes of x will be lost. This might lead to
            // unexpected behavior since bytesX are right padded.
            uint32 y = uint32(x);
            myNumber += y + msg.value;
        }
    }

نسخه جدید:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.5.0;
    // This will not compile after 0.6.0

    contract OtherContract {
        uint x;
        function f(uint y) external {
            x = y;
        }
        function() payable external {}
    }

    contract New {
        OtherContract other;
        uint myNumber;

        // Function mutability must be specified.
        function someInteger() internal pure returns (uint) { return 2; }

        // Function visibility must be specified.
        // Function mutability must be specified.
        function f(uint x) public returns (bytes memory) {
            // The type must now be explicitly given.
            uint z = someInteger();
            x += z;
            // Throw is now disallowed.
            require(x <= 100);
            int y = -3 >> 1;
            require(y == -2);
            do {
                x += 1;
                if (x > 10) continue;
                // 'Continue' jumps to the condition below.
            } while (x < 11);

            // Call returns (bool, bytes).
            // Data location must be specified.
            (bool success, bytes memory data) = address(other).call("f");
            if (!success)
                revert();
            return data;
        }

        using AddressMakePayable for address;
        // Data location for 'arr' must be specified
        function g(uint[] memory /* arr */, bytes8 x, OtherContract otherContract, address unknownContract) public payable {
            // 'otherContract.transfer' is not provided.
            // Since the code of 'OtherContract' is known and has the fallback
            // function, address(otherContract) has type 'address payable'.
            address(otherContract).transfer(1 ether);

            // 'unknownContract.transfer' is not provided.
            // 'address(unknownContract).transfer' is not provided
            // since 'address(unknownContract)' is not 'address payable'.
            // If the function takes an 'address' which you want to send
            // funds to, you can convert it to 'address payable' via 'uint160'.
            // Note: This is not recommended and the explicit type
            // 'address payable' should be used whenever possible.
            // To increase clarity, we suggest the use of a library for
            // the conversion (provided after the contract in this example).
            address payable addr = unknownContract.makePayable();
            require(addr.send(1 ether));

            // Since uint32 (4 bytes) is smaller than bytes8 (8 bytes),
            // the conversion is not allowed.
            // We need to convert to a common size first:
            bytes4 x4 = bytes4(x); // Padding happens on the right
            uint32 y = uint32(x4); // Conversion is consistent
            // 'msg.value' cannot be used in a 'non-payable' function.
            // We need to make the function payable
            myNumber += y + msg.value;
        }
    }

    // We can define a library for explicitly converting ``address``
    // to ``address payable`` as a workaround.
    library AddressMakePayable {
        function makePayable(address x) internal pure returns (address payable) {
            return address(uint160(x));
        }
    }

    