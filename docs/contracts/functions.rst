.. index:: ! functions

.. _functions:

*********
توابع
*********

توابع می توانند داخل و خارج قراردادها تعریف شوند.

توابعی که خارج از یک قرارداد هستند "توابع آزاد" نیر نامیده می شوند، همیشه دارای :ref:`میدان دید<visibility-and-getters>` 
داخلی ``internal`` مطلق هستند. مثل توابع کتابخانه ای داخلی کد این توابع در قرارداد هایی که آنها را
فراخوانی می کنند گنجانده می شوند.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.1 <0.9.0;

    function sum(uint[] memory _arr) pure returns (uint s) {
        for (uint i = 0; i < _arr.length; i++)
            s += _arr[i];
    }

    contract ArrayExample {
        bool found;
        function f(uint[] memory _arr) public {
            // This calls the free function internally.
            // The compiler will add its code to the contract.
            uint s = sum(_arr);
            require(s >= 10);
            found = true;
        }
    }

.. note::
    توابعی که خارج از یک قرارداد تعریف شده اند همیشه در متن قرارداد اجرا می شوند.
    همچنان آنها به متغیر ``this`` دسترسی دارند، می توانند قراداد های دیگر را فراخوانی کنند، می
    توانند به قرارداد ها اتر ارسال و قرادادهایی که آنها را فراخوانی می کنند را نابود کنند، در میان
    چیز های دیگر. تفاوت اصلی بین توابع تعریف شده در داخل یک قرارداد با توابع آزاد این است
    که توابع آزاد دسترسی مستقیم به متغیر های ذخیره سازی و توابع دیگر ندارند و در میدان دید
    آنها نیست.

.. _function-parameters-return-variables:

پارامتر های(مولفه های) توابع و متغیر های بازگشتی
=================================================

توابع پارامتر هایی با نوع مشخص شده به عنوان ورودی دریافت می کنند و شاید برخلاف دیگر
زبان های برنامه نویسی، همچنین تعداد دلخواهی از مقادیر را به عنوان خروجی برگردانند.

پارامتر های تابع 
-------------------

پارامتر های تابع به همان روش تعریف متغیر های تعریف می شوند و پارامتر های بلا استفاده را
نیز می توان حذف کرد.

برای مثال، اگر شما می خواهید قرار داد شما یک نوع از فراخوانی
خارجی را به همراه دو مقدار عددی قبول کند، شما می توانید از چیزی شبیه مثال زیر استفاده کنید:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract Simple {
        uint sum;
        function taker(uint _a, uint _b) public {
            sum = _a + _b;
        }
    }

پارامتر های تابع می توانند به عنوان متغیر های محلی استفاده شده و مقدار دهی شوند.

.. note::

  یک :ref:`تابع خارجی<external-function-calls>` نمی تواند یک آرایه چند بعدی را به عنوان یک ورودی قبول کند. این قابلیت
  زمانی امکانپذیر است که ABI کدر نسخه v2 را توسط  ``pragma abicoder v2;`` به فایل کد
  منبع خود اضافه کنید.
  
  یک :ref:`تابع داخلی<external-function-calls>` می تواند یک آرایه چند بعدی را بدون اضافه کردن این قابلیت به عنوان ورودی
  قبول کند.

.. index:: return array, return string, array, string, array of strings, dynamic array, variably sized array, return struct, struct

متغیر های بازگشتی 
------------------

متغیر های بازگشتی تابع به همان روش نوشتاری بعد از کلمه کلیدی ``returns`` تعریف می شوند.

برای مثال، فرض کنید شما می خواهید دو نتیجه را بازگردانید: جمع و ضرب دو مقدار عددی که
به صورت پارامتر به تابع انتقال یافته اند، پس شما کدی شبیه زیر خواهید داشت:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract Simple {
        function arithmetic(uint _a, uint _b)
            public
            pure
            returns (uint o_sum, uint o_product)
        {
            o_sum = _a + _b;
            o_product = _a * _b;
        }
    }

نام متغیر های بازگشتی قابل حذف است. متغیر های بازگشتی می توانند به عنوان متغیر محلی
استفاده شوند و آنها مقدار دهی اولیه می شوند توسط :ref:`مقدار اولیه پیش فرض <default-value>` شان و دارای
مقدار هستند تا زمانی که مقدار دهی دوباره شوند.

شما می توانید مقدار متغیر بازگشتی را واگذار کنید(یعنی فقط متغیر را در بخش returns
بنویسید) و سپس تابع را همانند بالا رها کنید، یا می توانید مقادیر بازگشتی (یک یا :ref:`چند <multi-return>`) را
مستقیما با دستور ``return`` فراهم کنید:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract Simple {
        function arithmetic(uint _a, uint _b)
            public
            pure
            returns (uint o_sum, uint o_product)
        {
            return (_a + _b, _a * _b);
        }
    }

اگر شما از دستور ``return`` جهت خارج شدن زودرس از تابعی که مقادیری را بازمی گرداند
استفاده کرده اید، باید به همراه دستور return  مقادیر بازگشتی را نیز فراهم کنید. 

.. note::
    شما نمی توانید بعضی از نوع های متغیر را از توابع غیر-داخلی باز گردانید، بویژه آرایه های
    چند-بعدی پویا و ساختار های داده ای. اگر شما ABI کدر v2 را توسط  ``;pragma abicoder v2`` 
    فعال کرده باشید، فایل منبع کد شما نوع های بیشتری را قبول می کند، اما نوع های  ``mapping``
    هنوز دارای محدودیت هستند و فقط در داخل یک قرارداد استفاده می شوند و قابل انتقال
    نیستند.

.. _multi-return:

بازگرداندن چندین مقادیر 
-------------------------

وقتی یک تابعی دارای چندین نوع برای بازگرداندن دارد از بیانیه(دستور) ``return (v0, v1, ..., vn)`` 
می توان استفاده کرد برای بازگرداندن چندین مقادیر.  تعداد اجزای دستور باید با تعداد و
نوع بازگشتی متغیر ها تطابق داشته باشد  بویژه پس از تبدیل ضمنی :ref:`implicit conversion <types-conversion-elementary-types>`.

.. _state-mutability:

نغییر پذیری وضعیت
==================

.. index:: ! view function, function;view

.. _view-functions:

توابع رویتی
--------------

توابع می توانند به عنوان رویتی ``view`` تعریف شوند به شرط اینکه قول دهند وضعیت را تغییر ندهند.

.. note::
  اگر کامپایلر EVM بر روی Byzantium یا جدیدتر(پیش فرض) کد ``STATICCALL`` هنگام
  فراخوانی تابع ``view`` استفاده می شود که ملزوم می کند وضعیت غیر قابل تغییر در بخش
  اجرای EVM بماند. برای کتابخانه توابع ``view`` از ``DELEGATECALL`` استفاده می شود، بخاطر
  اینکه چیزی به عنوان مخلوط ``DELEGATECALL``  و ``STATICCALL`` وجود ندارد. به این معنی
  است که کتابخانه توابع ``view`` دارای تصدیق حین-اجرا نیستند که از تغییرات وضعیت جلوگیری
  کنند. این نباید تاثیر منفی امنیتی داشته باشد زیرا کد کتابخانه معمولا شناسایی شده زمان
  کامپایل و چک کننده ایتا نقش چک کننده کامپایلر را ایفا می کند.

عبارات های به عنوان تغییر دهنده وضعیت شناخته می شوند:

#. نوشتن متغیرهای وضعیت
#. :ref:`انتشار رخداد <events>`.
#. :ref:`ایجاد قرارداهای دیگر <creating-contracts>`.
#. استفاده از ``selfdestruct``.
#. ارسال اتر توسط فراخوانی.
#. فراخوانی هر تابعی که از نوع ``view`` یا ``pure`` نباشد.
#. استفاده از فراخوانی های سطح-پایین.
#. استفاده از اسمبلی توخط که دارای کد های خاص باشد.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.5.0 <0.9.0;

    contract C {
        function f(uint a, uint b) public view returns (uint) {
            return a * (b + 42) + block.timestamp;
        }
    }

.. note::
   ``constant`` در توابع برای اشاره به ``view`` استفاده می شود ، اما این در نسخه 0.5.0 از قلم افتاده است.

.. note::
  متد های دریافت کننده بصورت اتوماتیک ``view`` نشان گذاری می شوند.

.. note::
  تا قبل از نسخه 0.5.0 کامپایلر از کد ``STATICCALL`` برای توابع view استفاده نمی کرد.
  این حالت تبادل نوع نا متعبراوسط توابع ``view`` را فعال می کرد. بویسله استفاده ``STATICCALL``
  برای توابع ``view`` ز تغییرات وضعیت دز سطح EVM جلوگیری می شود.

.. index:: ! pure function, function;pure

.. _pure-functions:

توابع خالص
--------------

توابعی خالص ``pure`` نامیده می شوند که قول دهند که از وضعیت نخوانند و تغییرش ندهند.

.. note::
  اگر کامپایلر EVM بر روی Byzantium یا جدیدتر(پیش فرض) کد ``STATICCALL`` استفاده
  شود، این تضمین را نمی دهد که وضعیت را نمی خواند، اما حداقل اجاره تغییر وضعیت را نمی
  دهد.

در ادامه لیست عبارات تغییردهنده وضعیت که در بالا توضیح داده شد، در زیر عباراتی که وضعیت را می خوانند بیان می شود:

#. خواندن از متغیرهای وضعیت.
#. دسترسی به ``address(this).balance`` یا ``<address>.balance``.
#. دسترسی به هر یک از اعضای ``block``, ``tx``, ``msg`` (به غیر از ``msg.sig`` و ``msg.data``).
#. فراخوانی هر تابعی که به عنوان ``pure`` نشان گذاری نشده باشد.
#. استفاده از اسمبلی توخط که دارای کد های خاص باشد.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.5.0 <0.9.0;

    contract C {
        function f(uint a, uint b) public pure returns (uint) {
            return a * (b + 42);
        }
    }

توابع خالص قادر به استفاده از توابع ``()revert`` و ``()require`` برای واکشی ظرفیت تغییرات
وضعیت زمان رخداد خطا هستند.

واکشی یک تغییر وضعیت به معنای "تغییردادن وضعیت" نیست، زیرا فقط تغییراتی که قبلا در کد
ایجاد شده بوده که محدودیت ``view``  یا ``pure`` نداشته اند واکشی شده است و این کد روشی
برای گرفتن واکشی ``revert`` بدون ارسال آن است.

این رفتار همچنین هم سو با کد ``STATICCALL`` است.

.. warning::
  این امکان وجود ندارد که از خواندن وضعیت توسط توابع در سطح EVM جلوگیری کرد
  تنها می توان از نوشتن آنها جلوگیری کرد( مثل توابع ``view`` که می توانند EVM را ملزم کنند اما
  توابع ``pure`` نمی تواند).

.. note::
  تا قبل از نسخه 0.5.0 کامپایلر از کد ``STATICCALL`` برای توابع ``pure`` استفاده نمی کرد.
  این حالت تبادل نوع نا متعبراوسط توابع pure را فعال می کرد. بویسله استفاده ``STATICCALL``
  برای توابع ``pure`` ز تغییرات وضعیت دز سطح EVM جلوگیری می شود.

.. note::
  تا قبل از نسخه 0.4.17 کامپایلر توابع خالص ``pure`` را ملزم به نخواندن وضعیت نمی کرد. این
  یک بررسی زمان- کامپایل است، که می تواند به تبادلات نامعتبر مستقیم بین نوع های قرارداد
  ها دور بزند، زیرا کامپایلر می تواند نوع قرارداد را تصدیق کند نه عملیات تغییر-وضعیت را، اما
  آن نمی تواند بررسی کند که قراردادی که فراخوانی خواهد شد در زمان اجرا در واقع در همان
  نوع است یا خیر.

.. _special-functions:

توابع خاص
=================

.. index:: ! receive ether function, function;receive ! receive

.. _receive-ether-function:

تابع دریافت اتر
----------------------

هر قرارداد نمی تواند بیش از یک تابع دریافت ``receive`` داشته باشد، با استفاده از 
``{ ... } receive() external payable`` (بدون کلمه کلیدی ``function`` ) تعریف می شود.
این تابع نمی تواند ورودی و مقدار بازگشتی داشته باشد، و باید میدان دید ``external``  خارجی و وضعیت ``payable`` تغییرناپذیر داشته باشد.
می تواند مجازی باشد ، می تواند بازنویسی(override) و تغییر دهنده داشته باشد.

تابع دریافت زمان فراخوانی می شود که به قرارداد،
فراخوانی به همراه فراخوانی داده خالی ارسال شود.
این همان تابعی است که در انتقال های اتر اجرا می شود( مثل ``()send.`` یا ``()transfer.`` ).
اگر چنین تابعی وجود نداشته باشد، اما یک :ref:`تابع عقبگرد <fallback-function>` قابل پرداخت وجود داشته
باشد، تابع عقبگرد در انتقال اتر فراخوانی می شود. اگر هر دو تابع وجود نداشته باشد، قرارداد
قادر به دریافت تراکنسهای اتری به روش های عادی نخواهد بود و خطایی استثنا بروز خواهد داد.

در بدترین حالت ، تابع ``receive`` تنها می تواند توسط 2300 گاز در دسترس باشد( برای مثال
زمانی که از ``send`` یا ``transfer`` استفاده شود)، فضای کمی برای انجام عملیات دیگر بجز ورود
به سیستم باقی می ماند. عملیات زیر گاز بیشتری نسبت به هزینه اولیه 2300 گاز مصرف می
کنند:

- نوشتن در محل ذخیره سازی(storage)
- ایجاد یک قرارداد
- فراخوانی یک تابع خارجی که مقدار زیادی گاز مصرف می کند
- ارسال اتر

.. warning::
    قراردادهایی که مستقیما اتر دریافت می کنند (بدون یک فراخوانی تابع، مثل ``send``  یا ``transfer``)
    اما تابع دریافت اتر ندارند یا یک تابع عقبگرد ندارند یک استثنا از خود بروز می دهند،
    و اتر ارسالی را باز می گردانند( این روش تا قبل از سالیدیتی نسخه 0.4.0 متفاوت بود).
    بنابراین اگر می خواهید قرارداد شما اتر دریافت کند ، شما مجبور هستید یک تابع دریافت اتر
    پیاده سازی کنید. (استفاده از تابع عقبگرد قابل پرداخت برای دریافت اتر پیشنهاد نمی شود، زیرا
    امکان دارد در تداخلات رابط خطا ندهد).


.. warning::
    یک قرارداد بدون تابع دریافت اتر می تواند اتر را به عنوان گیرنده یک *تراکنش* *coinbase* ( به عنوان *پاداش* *بلوک* *معدنچی* ) یا به عنوان مقصد ``selfdestruct`` دریافت کند.

    یک قرارداد نمی تواند به چنین انتقال اتر واکنش نشان دهد و بنابراین نمی تواند آنها را رد کند.
    این یک انتخاب طراحی EVM است و solidity نمی تواند دور و بر این مسائل کار کند.

    همچنین به این معنی است که آدرس ``address(this).balance`` می تواند مجموع برخی از حسابداری
    دستی اجرا شده در یک قرارداد ( به عنوان مثال بروزرسانی شمارنده در تابع دریافت اتر) باشد.

در زیر می توانید نمونهای از یک قرارداد بنام Sink که از تابع گیرنده ``receive`` استفاده می کند را مشاهده کنید.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    // This contract keeps all Ether sent to it with no way
    // to get it back.
    contract Sink {
        event Received(address, uint);
        receive() external payable {
            emit Received(msg.sender, msg.value);
        }
    }

.. index:: ! fallback function, function;fallback

.. _fallback-function:

تابع عقبگرد
-----------------

یک قرارداد می تواند حداکثر یک تابع ``fallback`` داشته باشد، با ساتفاده از ``fallback () external [payable]`` 
یا ``fallback (bytes calldata _input) external [payable] returns (bytes memory _output)`` (هر دو بدون استفاده از کلمه کیلیدی ``function`` )
تعریف می شوند. این تابع باید دارای میدان دید خارجی ``external`` باشد. یک تابع عقبگرد می
تواند مجازی در بدترباشد ، می تواند بازنویسی یا دارای تغییردهنده باشد.

تابع عقبگرد هنگام فراخوانی قرارداد اجرا شده و اگر دیگر توابع با امضاء این تابع همخوانی
نداشته باشد یا اگر داده ای درکل فراهم نشده و یا اگر :ref:`تابع گیرنده اتر <receive-ether-function>` تعریف نشده باشد. تابع
عقبگرد همیشه داده دریافت می کند ، اما برای دریافت اتر باید بصورت ``payable`` نشان گذاری شده باشد.

اگر نسخه به همراه پارامترها استفاده شده باشد، ``input_`` شامل کل داده ارسالی توسط قرارداد (برابر
است یا ``msg.data`` ) است و ``output_`` می تواند داده را بازگرداند. داده برگشتی بصورت ABI-
encoded نخواهد بود. در عوض بدون تغییرات برگشت خواهد شد( حتی بدون لایه بندی).

در بدترین حالت، اگر یک تابع عقبگرد قابل پرداخت بجای تابع گیرنده مورد استفاده قرار گیرد،
فقط با توسل به 2300 گاز در دسترس خواهد بود( بخش :ref:`تابع گیرنده اتر <receive-ether-function>` برای توضیحات و
پیاده سازی این مورد مشاهده کنید)

مثل هر تابعی، تابع عقبگرد می تواند عملیات پیچیده ای را تا زمانی که گاز کافی به آن داده
شود،  اجرا کند.

.. warning::
    اگر :ref:`تابع  دریافت کننده اتر <receive-ether-function>` اتر حاضر نباشد، همچنین برای انتقالات ساده اتر از یک تابع
    عقبگرد ``payable`` استفاده می شود. همیشه پیشنهاد شده که یک تابع دریافت کننده اتر تعریف
    شود، اگر شما تابع عقبگرد قابل پرداخت تعریف کرده اید این تابع از تداخلات رابط انتقالات اتر
    جلوگیری می کند. 

.. note::
    اگر شما می خواهید داده ورودی رمزگشایی(decode) کنید، شما می توانید با بررسی
    کردن چهار بایت اول از تابع انتخابگر و سپس با استفاده از ``abi.decode`` به همراه تکه ای از
    آرایه نوشتاری رمزگشایی داده کدشده-ABI استفاده کنید: 
    ``(c, d) = ;abi.decode(_input[4:], (uint256, uint256))``
    نکته اینکه این روش باید به عنوان آخرین راه حل مورد استفاده قرار گیرد و  بجای آن باید از توابع مناسب استفاده کرد.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.2 <0.9.0;

    contract Test {
        uint x;
        // This function is called for all messages sent to
        // this contract (there is no other function).
        // Sending Ether to this contract will cause an exception,
        // because the fallback function does not have the `payable`
        // modifier.
        fallback() external { x = 1; }
    }

    contract TestPayable {
        uint x;
        uint y;
        // This function is called for all messages sent to
        // this contract, except plain Ether transfers
        // (there is no other function except the receive function).
        // Any call with non-empty calldata to this contract will execute
        // the fallback function (even if Ether is sent along with the call).
        fallback() external payable { x = 1; y = msg.value; }

        // This function is called for plain Ether transfers, i.e.
        // for every call with empty calldata.
        receive() external payable { x = 2; y = msg.value; }
    }

    contract Caller {
        function callTest(Test test) public returns (bool) {
            (bool success,) = address(test).call(abi.encodeWithSignature("nonExistingFunction()"));
            require(success);
            // results in test.x becoming == 1.

            // address(test) will not allow to call ``send`` directly, since ``test`` has no payable
            // fallback function.
            // It has to be converted to the ``address payable`` type to even allow calling ``send`` on it.
            address payable testPayable = payable(address(test));

            // If someone sends Ether to that contract,
            // the transfer will fail, i.e. this returns false here.
            return testPayable.send(2 ether);
        }

        function callTestPayable(TestPayable test) public returns (bool) {
            (bool success,) = address(test).call(abi.encodeWithSignature("nonExistingFunction()"));
            require(success);
            // results in test.x becoming == 1 and test.y becoming 0.
            (success,) = address(test).call{value: 1}(abi.encodeWithSignature("nonExistingFunction()"));
            require(success);
            // results in test.x becoming == 1 and test.y becoming 1.

            // If someone sends Ether to that contract, the receive function in TestPayable will be called.
            // Since that function writes to storage, it takes more gas than is available with a
            // simple ``send`` or ``transfer``. Because of that, we have to use a low-level call.
            (success,) = address(test).call{value: 2 ether}("");
            require(success);
            // results in test.x becoming == 2 and test.y becoming 2 ether.

            return true;
        }
    }

.. index:: ! overload

.. _overload-function:

بارگذاری تابع
====================

یک قرارداد می تواند دارای تعداد متنوعی از توابع بصورت هم نام با پارامتر های دریافتی
متفاوت باشد. به این روند (بازگذاری/اضافه بار) “overloading” می گویند که همچنین بروی
توابع وراثتی نیز انجام می شود. مثال زیر بارگذاری تابع ``f`` در محدوده ی قرارداد ``A`` را نشان می
دهد:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract A {
        function f(uint _in) public pure returns (uint out) {
            out = _in;
        }

        function f(uint _in, bool _really) public pure returns (uint out) {
            if (_really)
                out = _in;
        }
    }

توابع بارگذاری شده همچنین در رابط خارجی در دسترس هستند. این منجر به یک خطا می شود
اگر توابعی قابل دید به صورت خارجی باشند و نوع سالیدیتی آنها با نوع خارجی آنها متفاوت باشد.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    // This will not compile
    contract A {
        function f(B _in) public pure returns (B out) {
            out = _in;
        }

        function f(address _in) public pure returns (address out) {
            out = _in;
        }
    }

    contract B {
    }


هر دو تابع ``f`` در بالا بارگذاری شده و برای ABI آدرس پذیری می کنند همچنین در داخل سالیدیتی
این دو تابع متفاوت در نظر گرفته می شوند.

وضوح بارگذاری و تطبیق ورودی ها
-----------------------------------------

توابع بارگذاری شده توسط تطبیق آنها نسبت به میدان دید تعاریف های از قبل نوشته شده
ورودی های آنها انتخاب و فراخوانی می شوند. توابع های منتخب اگر تمامی ورودی های آنها
بتوانند به نوع های تعریف شده تبدیل شوند انتخاب می شود. اگر هیچ تطابقی با تابع منتخب رخ
ندهد، عمل انتخاب تابع با خطا مواجه می شود.

.. note::
    در انتخاب تابع بارگذاری شده پارامتر های بازگشتی مد نظر قرار نمی گیرند.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract A {
        function f(uint8 _in) public pure returns (uint8 out) {
            out = _in;
        }

        function f(uint256 _in) public pure returns (uint256 out) {
            out = _in;
        }
    }

Calling ``f(50)`` would create a type error since ``50`` can be implicitly converted both to ``uint8``
and ``uint256`` types. On another hand ``f(256)`` would resolve to ``f(uint256)`` overload as ``256`` cannot be implicitly
converted to ``uint8``.
