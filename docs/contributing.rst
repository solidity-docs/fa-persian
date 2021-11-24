#######
همکاری
#######

همیشه از کمک دیگران استقبال می شود و گزینه های زیادی وجود دارد که چگونه می توانید به سالیدیتی کمک کنید.

به ویژه، از حمایت های شما در زمینه های زیر قدردانی می کنیم:

* گزارش ایراد ها
* رفع و پاسخگویی به `ایراد های سالیدیتی در گیت هاب
  <https://github.com/ethereum/solidity/issues>`_، مخصوصا مواردی که با عنوان "
  `"اولین ایراد خوب" <https://github.com/ethereum/solidity/labels/good%20first%20issue>`_ بر چسب خورده اند مقدمه ای برای همکاری کنندگان خارجی است.
* بهبود اسناد سالیدیتی (documentation).
* ترجمه اسناد به زبان های بیشتر.
* پاسخگویی به سوالات دیگر کاربر ها در `stackExchange <https://ethereum.stackexchange.com>`_ و سالیدیتی  `Solidity Gitter Chat <https://gitter.im/ethereum/solidity>`_.
* با روند طراحی زبان مشغول شدن بوسیله پیشنهاد تغییرات زبانی یا ویژگی های جدید در `فروم سالیدتی <https://forum.soliditylang.org/>`_ با ارائه باز خورد.

در شروع، برای آشنایی با خود اجزا های سالیدیتی و روند ساخت (build) سالیدیتی، می توانید
ساختن از منبع :ref:`building-from-source` را امتحان کنید. همچنین، داشتن مهارت کافی در نوشتن
قرار داد های سالیدیتی ممکن است مفید باشد.

لطفا توجه داشته باشید که این پروژه `یک کد مشارکتی <https://raw.githubusercontent.com/ethereum/solidity/develop/CODE_OF_CONDUCT.md>`_ است.
با شرکت در این پروژه – در ایرادها، درخواست های پول(pull)، یا کانال های gitter – شما قبول می کنید که قوانین آن را رعایت کنید.

تماس های تیمی
=============

اگر مشکلات یا در خواست های پول(pull) دارید برای مطرح کردن، یا علاقمند به شنیدن این
هستید که تیم و مشارکت کنندگان مشغول به چه کار هایی هستند، می توانید به تماس های
تیمی عمومی ما بپیوندید: 

- دوشنبه ها ساعت 3 بعد از ظهر CET/CEST( به وقت مرکزی تابستانی اروپا)
- چهارشنبه ها ساعت 2 بعد از ظهر CET/CEST( به وقت مرکزی تابستانی اروپا)

هر دو وقت تماس در `Jitsi <https://meet.komputing.org/solidity>`_ بر قرار می شود.

چطوری ایراد ها را گزارش دهیم
============================

برای گزارش یک ایراد، از 
`ردیاب ایرادات GitHub <https://github.com/ethereum/solidity/issues>`_  استفاده کنید.
هنگام گزارش ایرادات ، لطفا جزئیات زیر را ذکر کنید:

* نسخه سالیدیتی.
* کد منبع (در صورت امکان).
* سیستم عامل.
* مراحلی که بتوان دوباره همان ایراد رخ دهد.
* رفتار در عمل و رفتار مورد انتظار.

به حداقل رسانی کد منبع که باعث رخداد ایراد می شود همیشه بسیار مفید است و حتی بعضی
اوقات یک سوء تفاهم را روشن می کند.

روند کاری برای درخواست های pull
===============================

برای مشارکت، لطفا از شاخه ``develop`` یک چنگال(fork) (کپی) بگیرید و تغییرات خود را آنجا
انجام دهید. پیام های ارتکابی(commit) شما باید جزئیات اینکه *چه چیزی* در کد و *چرا* تغییر داده
اید را توضیح دهد (مگر اینکه یک تغییر کوچک باشد).

اگر برای عمل pull نیاز به هرنوع تغییری از شاخه ``develop`` برای ساختن (کپی) چنگال خود دارید(
برای مثال، برای برطرف کردن تداخلات هنگام ادغام)، لطفا از ``git merge`` استفاده نکرده و در
عوض از ``git rebase`` برای شاخه خود استفاده کنید. این به ما کمک خواهد کرد تا به راحتی
تغییرات شما را باز بینی کنیم.

علاوه بر این، اگر در حال نوشتن ویژگی جدید هستید، لطفا مطمئن شوید که موارد آزمایشی
مناسب را بخش ``test/`` قرار داده اید/(به زیر توجه کنید) :

با این حال، اگر تغییر بزرگتری ایجاد می کنید، لطفا اول با کانال `Solidity Development Gitter channel
<https://gitter.im/ethereum/solidity-dev>`_ توسعه سالیدیتی مشورت کنید
( متفاوت از یک مورد که در بالا ذکر شد، این مورد بجای کاربرد زبان ، به کامپایلر و توسعه زبان اشاره می کند)

ویژگی های جدید و رفع اشکالات باید به فایل ``Changelog.md`` اضافه شود: لطفا در صورت
نیاز سبک نوشته های قبلی را دنبال کنید

نهایتا، لطفا مطمئن شوید که به `سبک برنامه <https://github.com/ethereum/solidity/blob/develop/CODING_STYLE.md>`_ نویسی این پروژه احترام گذاشته اید. همچنین،
درسته که ما آزمایش CI را انجام می دهیم، لطفا کد خود را آزمایش کرده و قبل از ارسال
درخواست pull اطمینان حاصل کنید که بصورت محلی قابل ساخت (build) است.

از کمک شما متشکریم!

اجرای تست های کامپایلر
======================

پیش نیاز ها
-------------

برخی از تست ها به کتابخانه evmone و برخی دیگر به libz3 نیاز دارند. (`evmone <https://github.com/ethereum/evmone/releases>`_,
`libz3 <https://github.com/Z3Prover/z3>`_, و
`libhera <https://github.com/ewasm/hera>`_).

در سیستم عامل مک برخی اسکریپت های تست انتظار دارند که GNU coreutils (ابزار های
هسته) نصب شده باشند. با استفاده از دستور Homebrew می توان به راحتی ابراز های هسته
را نصب کرد: ``brew install coreutils``.

اجرای تست ها
------------

سالیدیتی شامل انواع مختلفی از تست ها است، بیشتر آنها وصل به بسته `Boost C++ Test Framework <https://www.boost.org/doc/libs/release/libs/test/doc/html/index.html>`_
برنامه ``soltest`` می باشند. اجرای ``build/test/soltest``  یا پوشش دهنده اش ``scripts/soltest.sh`` برای
بیشتر تغییرات کافی است.

اسکریپت ``scripts/test.sh/.`` به صورت اتوماتیک بیشتر تست های سالیدتی را اجرا می کند ، هم بسته های `Boost C++ Test Framework <https://www.boost.org/doc/libs/release/libs/test/doc/html/index.html>`_ برنامه ``soltest`` (یا پوشش دهنده ی ``scripts/soltest.sh``) ، همچنین تست های
تلفیقی و خط فرمان را اجرا می کند.

سیستم تست به صورت اتوماتیک از پوشه جاری سعی می کند مکان کتابخانه `evmone <https://github.com/ethereum/evmone/releases>`_ کشف کند .

The ``evmone`` library must be located in the ``deps`` or ``deps/lib`` directory relative to the
current working directory, to its parent or its parent's parent. Alternatively an explicit location
for the ``evmone`` shared object can be specified via the ``ETH_EVMONE`` environment variable.

``evmone`` is needed mainly for running semantic and gas tests.
If you do not have it installed, you can skip these tests by passing the ``--no-semantic-tests``
flag to ``scripts/soltest.sh``.

Running Ewasm tests is disabled by default and can be explicitly enabled
via ``./scripts/soltest.sh --ewasm`` and requires `hera <https://github.com/ewasm/hera>`_
to be found by ``soltest``.
The mechanism for locating the ``hera`` library is the same as for ``evmone``, except that the
variable for specifying an explicit location is called ``ETH_HERA``.

The ``evmone`` and ``hera`` libraries should both end with the file name
extension ``.so`` on Linux, ``.dll`` on Windows systems and ``.dylib`` on macOS.

For running SMT tests, the ``libz3`` library must be installed and locatable
by ``cmake`` during compiler configure stage.

اگر کتابخانه ``libz3`` در سیستم شما نصب نشده باشد، شما باید تست های SMT را غیر فعال
کنید بوسیله اضافه کردن ``SMT_FLAGS=--no-smt`` قبل از اجرای ``scripts/tests.sh/.`` یا
اجرای ``scripts/soltest.sh --no-smt/.`` این تست ها ``libsolidity/smtCheckerTests``  و
``libsolidity/smtCheckerTestsJSON`` هستند.

.. note ::

    برای گرفتن لیست واحد های تست اجرا شونده توسط soltest ، دستور . ``build/test/soltest --list_content=HRF/.`` اجرا کنید.

برا گرفتن نتیجه سریعتر شما می توانید زیر مجموعه تست ها یا تست های مد نظر خود را اجرا کنید.

برای اجرای زیر مجموعه تست ها، شما می توانید از فیلترهای :
``scripts/soltest.sh -t TestSuite/TestName/.``,
که بجای ``TestName`` از ``*`` نیز می توانید استفاده کنید. 

یا، مثلا ، برای اجرای تمامی تست های ابهام زدایی یول:

``scripts/soltest.sh -t "yulOptimizerTests/disambiguator/*" --no-smt/.`` می توانید استفاده کنید.

``build/test/soltest --help/.``  دارای بخش راهنمای گسترده به همراه تمامی گزینه های موجود است.

به ویژه مشاهده کنید:

- `show_progress (-p) <https://www.boost.org/doc/libs/release/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/show_progress.html>`_ زمان اتمام تست را نشان می دهد,
- `run_test (-t) <https://www.boost.org/doc/libs/release/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/run_test.html>`_ برای اجرای تست های خاص و
- `report-level (-r) <https://www.boost.org/doc/libs/release/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/report_level.html>`_ یک گزارش دقیقتر ارائه می دهد.

.. note ::

    آنهایی که در محیط ویندوزی می خواهند مجموعه های ساده ی بالا را بدون کتابخانه
    libz3 اجرا کنند. از گیت بش(bash) ``build/test/Release/soltest.exe -- --no-smt\.`` استفاده کنند.
    اگر در محیط دستوری اجرا می کنید از ``build\test\Release\soltest.exe -- --no-smt\.`` استفاده کنید.

اگر برای ایرادیابی(debug) از GDB استفاده می کنید، اطمینان حاصل کنید که به غیر از حالت
“usual” عمل ساخت را انجام می دهید. برای مثال، شما می توانید با دستور زیر در پوشه
``build`` عمل کنید:

.. code-block:: bash

   cmake -DCMAKE_BUILD_TYPE=Debug ..
   make

با این کار سمبل هایی ایجاد می شود که زمان ایرادیابی یک تست با نشان ``debug--``  از
آنها استفاده می شوند. شما به توابع و متغیر هایی دسترسی خواهید داشت که زمان ایراد یابی
می توانید آنها را متوقف کرده یا چاپ کنید.

CI تست های اضافه تری را اجرا می کند ( شامل ``solc-js`` و  تست های جداگانه framework
سالیدیتی) که نیازمند کامپایل Emscripten هدف است.

نوشتن و اجرای تست های نوشتاری
-----------------------------

تست های نوشتاری کد خطای صحیح تولید شده توسط کامپایلر را بررسی می کند و کد پیام
های نامعتبر را با کد های معتبر قابل پذیرش تغییر می دهد. آنها در فایل جداگانه ای داخل پوشه
``tests/libsolidity/syntaxTests``  قرار می گیرند. این فایل ها باید حاوی حاشیه نویسی باشند،
طبق نتایج مورد انتظار و تست مربوطه وضعیت دهی شده باشند. مجموعه تست بر عکس
انتظارات دریافتی ، کامپایل و بررسی خود را انجام می دهد.

برای مثال: ``test/libsolidity/syntaxTests/double_stateVariable_declaration.sol/.``

.. code-block:: solidity

    contract test {
        uint256 variable;
        uint128 variable;
    }
    // ----
    // DeclarationError: (36-52): Identifier already declared.

یک تست نوشتاری باید شامل حداقل تست خود قرارداد و به همراه آن جدا کننده ی ``// ----`` باشد.
توضیحاتی که بعد از جداکننده می آید برای توصیف خطاها یا هشدار های کامپایلر مدنظر
استفاده می شود. محدوده اعدادی که نمایش داده می شود محل رخ داد خطا در منبع را نشان
می دهد. اگر می خواهید قرار داد بدون خطا یا هشدار کامپایل شود، می توانید جدا کننده و
توضیحات که در آن است را کنار بگذارید.

در مثال بالا، متغیر وضعیت ``variable`` دو بار تعریف شده است که غیر مجاز است. این منجر به
یک خطای تعریف ``DeclarationError`` میشود که مشخص می کند متغیر قبلا تعریف شده است.

ابزار تست ``isoltest`` برای این کاربرد استفاده می شود و می توانید آن را در ``/build/test/tools/.`` 
بیابید. این یک ابزار تعاملی است که به شما اجازه می دهد قرار داد های ناموفق را با استفاده
از ویراشکر مورد علاقه خود ویرایش کنید. بیایید سعی کنیم این تست را با حذف تعریف دوم متغیر ``variable`` متوقف کنیم:

.. code-block:: solidity

    contract test {
        uint256 variable;
    }
    // ----
    // DeclarationError: (36-52): Identifier already declared.

اجرای ``build/test/isoltest/.`` دوباره منجر به تست ناموفق میشود : 

.. code-block:: text

    syntaxTests/double_stateVariable_declaration.sol: FAIL
        Contract:
            contract test {
                uint256 variable;
            }

        Expected result:
            DeclarationError: (36-52): Identifier already declared.
        Obtained result:
            Success


``isotest`` نتیجه مورد انتظار را کنار نتیجه بدست آمده چاپ می کند، و همچنین روشی برای
ویرایش، بروز رسانی یا رد کردن فایل قرار داد فعلی، یا خروج از برنامه را ارائه می دهد.

چندین گزینه برای تست های ناموفق پیشنهاد می کند:

- ``isoltest`` : ``edit`` تلاش می کند قرار داد را در یک ویراشگر باز کند تا بتوانید آن را ویرایش کنید. با ویرایشگر ارائه شده در خط فرمان ( به عنوان ``isotest --editor /path/to/editor``) در متغیر محیطی ``EDITOR`` یا فقط از مسیر ``/usr/bin/editor`` (به ترتیب) استفاده می کند.
- ``update``:  انتظارات مربوط به قرارداد تحت تست را بروزرسانی می کند. با حذف حاشیه نویسی هایی که با انتظارات مغایرت دارند و اضافه کردن حاشیه نویسی هایی که طبق انتظارات از قلم افتاده اند عمل بروز رسانی را انجام می دهد. سپس تست را مجددا اجرا می کند.
- ``skip``:  اجرای این تست را رد می کند.
- ``quit``: از ``isotest`` خارج می شود.

تمامی این گزینه ها بر قرار داد جاری اعمال می شوند به غیر از گزینه خروج ``quit`` که کل روند تست را متوقف می کند.

به صورت خودکار بروز رسانی تست بالا آن را تغییر می دهد به

.. code-block:: solidity

    contract test {
        uint256 variable;
    }
    // ----

و اجرای دوباره تست . حالا دوباره از تست قبول می شود:

.. code-block:: text

    Re-running test case...
    syntaxTests/double_stateVariable_declaration.sol: OK


.. note::

    نامی برای فایل قرارداد انتخاب کنید که توضیح دهد چه چیزی را تست می کنید، برای
    مثال ``double_variable_declaration.sol``. به هیچ وجه بیش از یک قرار داد داخل یک فایل
    قرار ندهید، مگر اینکه در حال تست فراخوانی های وراثتی بین قرارداد ها باشید. هر فایل باید
    یک جنبه از ویژگی جدید شما را تست کند.


اجرای فازر(Fuzzer) توسط AFL
===========================

فازینگ (Fuzzing) تکنیکی است که برنامه ها را با ورودی های کم و زیاد تصادفی برای یافتن
حالت های استثنایی ( ایراد های تقسیم بندی(segmentation) حافظه، استثناها و غیره) اجرا
می کند. فازر های جدید هوشمند هستند و داخل ورودی از جستجوی مدیریت-شده(directed
search) استفاده می کنند. ما برنامه اختصاصی بنام ``solfuzzer`` داریم که کد منبع(
code) را به عنوان ورودی می گیرید و هر زمان که با خطاهایی مثل یک خطای داخلی کامپایلر،
خطای تقسیم بندی(segmentation) حافظه و یا موارد مشابه، بر خورد کند متوقف نمی شود مثلا اگر کد دارای خطا باشد
به این ترتیب، ابزار های فازینگ می توانند مشکلات داخلی را در کامپایلر پیدا کنند

ما عمدتا از `AFL <https://lcamtuf.coredump.cx/afl/>`_ برای فازینگ استفاده می کنیم. شما باید بسته های نرم
افزاری AFL را دانلود و نصب کنید (بسته afl , afl-clang) یا آنها را بصورت دستی بسازید
(build کنید). سپس سالیدیتی ( یا فقط برنامه ``solfuzzer`` )بسازید و به همراه AFL به عنوان کامپایلر :

.. code-block:: bash

    cd build
    # if needed
    make clean
    cmake .. -DCMAKE_C_COMPILER=path/to/afl-gcc -DCMAKE_CXX_COMPILER=path/to/afl-g++
    make solfuzzer

در این مرحله باید بتوانید پیامی مشابه پیام زیر را مشاهده کنید:

.. code-block:: text

    Scanning dependencies of target solfuzzer
    [ 98%] Building CXX object test/tools/CMakeFiles/solfuzzer.dir/fuzzer.cpp.o
    afl-cc 2.52b by <lcamtuf@google.com>
    afl-as 2.52b by <lcamtuf@google.com>
    [+] Instrumented 1949 locations (64-bit, non-hardened mode, ratio 100%).
    [100%] Linking CXX executable solfuzzer

اگر پیام های ابزار های ساخت ظاهر نشد، پرچم های cmake را که به با برنامه های  AFL clang  ارتباط دارند، تغییر دهید.

.. code-block:: bash

    # if previously failed
    make clean
    cmake .. -DCMAKE_C_COMPILER=path/to/afl-clang -DCMAKE_CXX_COMPILER=path/to/afl-clang++
    make solfuzzer

در غیر این صورت، هنگام اجرای فازر متوقف می شود به همراه خطایی که می گوید برنامه بدرستی ساخته نشده است:

.. code-block:: text

    afl-fuzz 2.52b by <lcamtuf@google.com>
    ... (truncated messages)
    [*] Validating target binary...

    [-] Looks like the target binary is not instrumented! The fuzzer depends on
        compile-time instrumentation to isolate interesting test cases while
        mutating the input data. For more information, and for tips on how to
        instrument binaries, please see /usr/share/doc/afl-doc/docs/README.

        When source code is not available, you may be able to leverage QEMU
        mode support. Consult the README for tips on how to enable this.
        (It is also possible to use afl-fuzz as a traditional, "dumb" fuzzer.
        For that, you can use the -n option - but expect much worse results.)

    [-] PROGRAM ABORT : No instrumentation detected
             Location : check_binary(), afl-fuzz.c:6920


در مرحله بعدی، به برخی از فایل های منبع نیاز دارید. این کار باعث می شود  فازر خطایابی را
خیلی راحت انجام دهد. می توانید فایل ها را از تست های نوشتاری کپی برداری کنید یا فایل
های تستی را از مستندات یا سایر تست ها استخراج کنید :

.. code-block:: bash

    mkdir /tmp/test_cases
    cd /tmp/test_cases
    # extract from tests:
    path/to/solidity/scripts/isolate_tests.py path/to/solidity/test/libsolidity/SolidityEndToEndTest.cpp
    # extract from documentation:
    path/to/solidity/scripts/isolate_tests.py path/to/solidity/docs

در اسناد AFL آمده است که حجم (فایل های ورودی) نباید خیلی بزرگ باشد. خود فایل ها نباید
بزرگتر از 1 کیلو بایت باشند و باید حداکثر یک فایل ورودی در هر عملکرد داشته باشد، بنابراین
بهتر است با تعداد کوچک شروع کرد. همچنین ابزاری بنام ``afl-cm`` وجود دارد که می تواند فایل
های ورودی که منجر به رفتار مشابه برنامه می شوند، اصلاح کند.

حالا فزر را اجرا کنید( گزینه ``-m`` اندازه حافظه را تا 60MB مگابایت افزایش می دهد):

.. code-block:: bash

    afl-fuzz -m 60 -i /tmp/test_cases -o /tmp/fuzzer_reports -- /path/to/solfuzzer

فازر فایل های منبع را که منجر به خرابی می شوند را در ``/tmp/fuzzer_reports`` ایجاد می
کند. اغلب بسیاری از فایل های منبع را پیدا می کند که خطای مشابه دارند. برای فیلتر کردن
خطاهای منحصر به فرد(unique) می توانید از ابزار ``scripts/uniqueErrors.sh`` استفاده کنید.

ریز مو ها
==========

ریزموها یک سیستم الگوی رشته ای شبیه سیبیل `Mustache <https://mustache.github.io>`_ است. در مکان های مختلف
برای کمک به افزایش خوانایی توسط کامپایلر استفاده می شود، و در نتیجه در نگهداری پذیری
و اثبات پذیری کد استفاده می شود.

نحوه ی نوشتاری تفاوت اساسی با سیبیل(Mustache) دارد. نشانگر های الگو ``{{``  و  ``}}``  با
``<`` و ``>`` جهت جلوگیری تداخل حین خواند کد با :ref:`yul` (نماد های ``<`` و ``>`` در کد تو خطی اسمبلی نامعتبر هستند
در صورتی که از ``{`` و ``}`` جهت تعیین محدوده بلوک استفاده می شوند) جایگزین شده اند.
محدودیت دیگر این است که لیست ها در یک عمق مقرر شده اند و باز گشتی نیستند. این محدودیت ممکن در آینده تغییر کند.

جزئیات تقریبی موارد زیر است:

هنگام برخورد با ``<name>`` مقدار-رشته ای متصل به متغیر فراهم شده با ``name`` بدون هیچ کم
و کسری و تکرار جایگزین می شود. یک منطقه را می توان بوسیله ``<#name>...</name>`` 
محدود کرد. به تعداد کاندیدا هایی که در محتوا وجود دارند و مجموعه متغیر هایی
که به سیستم الگو ارائه شده است جایگزینی مقادیری که داخل ``<inner>`` است، صورت
می پذیرد. 

همچنین نوع شرطی ``<?name>...<!name>...</name>`` وجود دارد که بسته به شرط
بخش اول یا دوم را بصورت بازگشتی از الگوی ارائه شده جایگزین می کند. اگر از ``<?+name>...<!+name>...</+name>``
استفاده شود ، بررسی می کند که پارامتر ``name``
خالی نباشد.

.. _documentation-style:

راهنمای سبک مستندات
====================

در بخش زیر شما توصیه های سبک مستندات را پیدا می کنید بخصوص بر روی مشارکت
کنندگان مستندات تمرکز شده است.

زبان انگلیسی 
----------------

از زبان انگلیسی استفاده کنید با لهجه انگلیسی،  مگر اینکه از نام پروژه یا برندی استفاده می
کنید. سعی کنید از زبان عامیانه و اصطلاحات محلی کمتر استفاده کنید، حدالمکان زبان خود را
برای خوانندگان واضح کنید. در زیر برخی از منابع برای کمک وجود دارند: 

* `انگلیسی فنی ساده <https://en.wikipedia.org/wiki/Simplified_Technical_English>`_
* `انگلیسی بین المللی <https://en.wikipedia.org/wiki/International_English>`_
* `لهجه بریتیش انگلیسی <https://en.oxforddictionaries.com/spelling/british-and-spelling>`_


.. note::

    در حالی که مستندات رسمی سالیدیتی به زبان انگلیسی نوشته شده است، توسط
    مشارکت کنندگان جامعه ترجمه هایی :ref:`translations` به زبان های دیگر موجود است.
    
    Please refer to the `translation guide <https://github.com/solidity-docs/translation-guide>`_
    for information on how to contribute to the community translations.

حروف بزرگ برای عناوین 
-----------------------

از موارد `حروف بزرگ <https://titlecase.com>`_ برای عناوین استفاده کنید. یعنی  تمام کلمات اصلی را در عناوین بزرگ
بنویسید ، اما نه در مقالات ، حروف ربط و اضافه، مگر اینکه آنها در ابتدای عنوان باشند.

به عنوان مثال موارد زیر صحیح است: 

* حروف بزرگ برای عناوین.
* برای عناوینی که حرف بزرگ دارند.
* نام متغیر های وضعیت و محلی.
* ترتیب ساختار.

باز کردن مخفف ها
-------------------

از مخفف های باز استفاده کنید، برای مثال:

* "Do not"  بجای "Don't".
* "Can not"  بجای "Can't".

صدای فعال و منفعل 
------------------------

صدای فعال معمولا برای مستندات آموزشی توصیه می شود زیرا به خواننده کمک می کند تا
درک کند چه کسی یا چه کاری را انجام می دهد.با این حال، از آنجایی که مستندات سالیدیتی
مخلوطی از آموزشها و محتوای مرجع است، صدای منفعل گاهی کاربرد بیشتری دارد.

بصورت خلاصه:

* از صدای منفعل برای مرجع فنی استفاده کنید، برای مثال تعاریف زبان و داخلی های ماشین مجازی اتریوم.
* هنگام توصیف توصیه های مر بوز به نحوه ی استفاده از جنبه های سالیدیتی، از صدای فعال استفاده کنید

به عنوان مثال، در زیر صدای منفعل وجود دارد زیرا جنبه ای از سالیدیتی را مشخص می کند:

  توابع ای را می توان  خالص  ``pure`` تعریف کرد که آنها قول می دهند وضعیت را نخوانده و اصلاح نکنند.

به عنوان مثال، در زیر در مورد استفاده از سالیدیتی آمده است، صدای فعال وجود دارد:

  هنگام فراخوانی کامپایلر، می توانید نحوه ی کشف اولبن عنصر در مسیر و همچنین
  نگاشت مجدد پیشوند مسیر را مشخص کنید

اصطلاحات رایج 
--------------

* "پارامتر های تابع" و "متغیر های بازگشتی" ، پارامتر های ورودی و خروجی نیستند.

نمونه های کد
-------------

وقتی که شما یک ارتباط عمومی ایجاد می کنید در فراید CI تمام بلوک های کد قالبندی شده
طبق مثال ها که بهمراه ``pragma solidity``، ``قرارداد``، ``کتابخانه`` یا ``رابط``  هستند توسط 
``test/cmdlineTests.sh/.`` تست می شوند.


اطمینان حاصل کنید که تمامی مثال های کد با نسخه ای از ``pragma`` شروع شود که کستردگی
نسخه آنها در قرار داد معتبر باشد. برای مثال : ``pragma solidity >=0.4.0 <0.9.0;``

اجرای تست های مستندات 
---------------------------

با اجرای ``scripts/docs.sh/.`` مطمئن شوید که مشارکت های شما از تست های مستندات ما با
موفقیت خارج شده است  که وابستگی های مورد نیاز برای اسناد را نصب می کند و هرگونه
مشکل مانند لینک های خراب و ایراد های نوشتاری را بررسی می کند.

طراحی زبان سالیدیتی
========================

برای اینکه فعالانه در گیر فرایند طراحی زبان شوید و ایده های خود را در مورد آینده سالیدیتی
به اشتراک بگذارید، لطفا به `انجمن سالیدتی <https://forum.soliditylang.org/>`_ بپیوندید.

انجمن سالیدیتی به عنوان محلی برای پیشنهاد و بحث در مورد ویژگی های جدید زبان و اجرای
آنها در مراحل اولیه ایده پردازی یا اصلاح ویژگی در دسترس است.

به محض اینکه پیشنهاد ها قابل لمس تر شدند، اجرای آنها نیز در `مخزن گیت هاب سالیدیتی <https://github.com/ethereum/solidity>`_ در
بخش مسائل مورد بحث قرار می گیرد.

علاوه بر بحث های انجمن و بخش مسائل ، ما به طور منظم میزبان  گفتگو های بحث در مورد
طراحی زبان هستیم که در آن مسائل، موضوعات یا پیاده سازی های انتخاب شده، با جزئیات
بحث می شوند. دعوت به این گفتگو ها از طریق انجمن به اشتراک گذاشته می شود.

ما همچنین بررسی باز خورد ها و سایر مطالب مرتبط با طراحی زبان را در انجمن به اشتراک می گذاریم.

اگر می خواهید بدانید تیم از نظر اجرای ویژگی های جدید در کجا قرار دارد، می توانید وضعیت
پیاده سازی را در `گیت هاب پروژه سالیدیتی <https://github.com/ethereum/solidity/projects/43>`_ دنبال کنید. مسائل موجود در پس زمینه طراحی
نیاز به جزئیات بیشتری دارند و در یک گفتگوی طارحی زبان یا در یک گفتگوی معمولی تیمی
مورد بحث قرار می گیرند. با تغییر از شاخه پیش فرض (`توسعه`) به `شاخه جدید <https://github.com/ethereum/solidity/tree/breaking>`_، می توانید
تغییرات بعدی را برای نسخه جدید بعدی مشاهده کنید.

برای موارد و سوالات موقتی می توانید از طریق کانال `Solidity-dev Gitter channel <https://gitter.im/ethereum/solidity-dev>`_ با ما ارتباط
برقرار کنید، یک اتاق گفتگوی اختصاصی برای بحث در مورد کامپایلر سالیدیتی و توسعه زبان ایجاد شده است.

ما نظرات شما را در مورد اینگه چگونه می توانیم روند طراحی زبان را بهبود بخشیم حتی با همکاری بیشتر یا نیمه خوشحال می شویم، بشنویم.
