.. index:: ! inheritance, ! base class, ! contract;base, ! deriving

***********
وراثت
***********

سالیدتی چندین نوع وراثت را قبول می کند، از جمله چند ریختی را.

چند ریختی یعنی اینکه در فراخوانی تابع(داخلی و خارجی) همیشه تابعی را اجرا می کند که به
همان نام ( و نوع های ورودی ها) در سلسله مراتب وراثتی آن قرارداد باشد.این امر باید به
صراحت در هر تابع در سلسله مراتب با استفاده از کلمه های کلیدی مجازی ``virtual`` و
بازنویسی ``override`` فعال شود. برای جزئیات بیشتر به :ref:`بازنویسی تابع <function-overriding>` تابع مراجعه کنید.

با مشخص کردن ``()ContractName.functionName`` می توانید تابع را در سلسله مراتب
وراثتی بصورت داخلی فراخوانی کنید و یا اگر می خواهید تابع را از یک سطح بالاتر در سلسله
مراتب وراثتی فراخوانی کنید از ``()super.functionName`` استفاده کنید(به زیر توجه کنید).

وقتی قراردادی از یک قراداد دیگر ارث می پذیرد، تنها فقط یک قرارداد در بلاکچین تولید شده
است، و کل کد قرارداد اصلی در قراداد تولید شده بصورت کامپایل شده قرار گرفته است. این
بدان معناست که تمامی فراخوانی داخلی توابع قرارداد اصلی نیز مثل فراخوانی داخلی تابع
خواهد بود( ``(..)super.f`` از JUMP بجای یک فراخوانی پیغامی استفاده خواهد کرد).

 سایه(shadowing) متغیر وضعیت به عنوان خطا در نظر گرفته می شود. یک قرارداد ارث
 گرفته شده فقط می تواند یک متغیر حالت  ``x``  را تعریف کند، در صورتی که هیچ متغیر حالت
 قابل مشاهده ای با همان نام در هیچ یک از قرارداده هالی والد آن وجود نداشته باشد.

سیستم وراثت عمومی بسیار شبیه به `پایتون <https://docs.python.org/3/tutorial/classes.html#inheritance>`_ است، بویژه در موارد تفاوت
وراثت چندگانه، اما :ref:`تفاوت هایی <multi-inheritance>` نیز وجود دارد.

جزئیات در مثال زیر آمده است.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;


    contract Owned {
        constructor() { owner = payable(msg.sender); }
        address payable owner;
    }


    // Use `is` to derive from another contract. Derived
    // contracts can access all non-private members including
    // internal functions and state variables. These cannot be
    // accessed externally via `this`, though.
    contract Destructible is Owned {
        // The keyword `virtual` means that the function can change
        // its behaviour in derived classes ("overriding").
        function destroy() virtual public {
            if (msg.sender == owner) selfdestruct(owner);
        }
    }


    // These abstract contracts are only provided to make the
    // interface known to the compiler. Note the function
    // without body. If a contract does not implement all
    // functions it can only be used as an interface.
    abstract contract Config {
        function lookup(uint id) public virtual returns (address adr);
    }


    abstract contract NameReg {
        function register(bytes32 name) public virtual;
        function unregister() public virtual;
    }


    // Multiple inheritance is possible. Note that `owned` is
    // also a base class of `Destructible`, yet there is only a single
    // instance of `owned` (as for virtual inheritance in C++).
    contract Named is Owned, Destructible {
        constructor(bytes32 name) {
            Config config = Config(0xD5f9D8D94886E70b06E474c3fB14Fd43E2f23970);
            NameReg(config.lookup(1)).register(name);
        }

        // Functions can be overridden by another function with the same name and
        // the same number/types of inputs.  If the overriding function has different
        // types of output parameters, that causes an error.
        // Both local and message-based function calls take these overrides
        // into account.
        // If you want the function to override, you need to use the
        // `override` keyword. You need to specify the `virtual` keyword again
        // if you want this function to be overridden again.
        function destroy() public virtual override {
            if (msg.sender == owner) {
                Config config = Config(0xD5f9D8D94886E70b06E474c3fB14Fd43E2f23970);
                NameReg(config.lookup(1)).unregister();
                // It is still possible to call a specific
                // overridden function.
                Destructible.destroy();
            }
        }
    }


    // If a constructor takes an argument, it needs to be
    // provided in the header or modifier-invocation-style at
    // the constructor of the derived contract (see below).
    contract PriceFeed is Owned, Destructible, Named("GoldFeed") {
        function updateInfo(uint newInfo) public {
            if (msg.sender == owner) info = newInfo;
        }

        // Here, we only specify `override` and not `virtual`.
        // This means that contracts deriving from `PriceFeed`
        // cannot change the behaviour of `destroy` anymore.
        function destroy() public override(Destructible, Named) { Named.destroy(); }
        function get() public view returns(uint r) { return info; }

        uint info;
    }

نکته بالا اینکه، ما ``()Destructible.destroy`` را فراخوانی کرده ایم برای "جلو" بردن درخواست
تخریب. نخوه انجام کار مشکل ساز است ، همانطور که در مثال زیر نشان داده شده است:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract owned {
        constructor() { owner = payable(msg.sender); }
        address payable owner;
    }

    contract Destructible is owned {
        function destroy() public virtual {
            if (msg.sender == owner) selfdestruct(owner);
        }
    }

    contract Base1 is Destructible {
        function destroy() public virtual override { /* do cleanup 1 */ Destructible.destroy(); }
    }

    contract Base2 is Destructible {
        function destroy() public virtual override { /* do cleanup 2 */ Destructible.destroy(); }
    }

    contract Final is Base1, Base2 {
        function destroy() public override(Base1, Base2) { Base2.destroy(); }
    }

فراخوانی ``()Final.destroy`` ، ``Base2.destroy`` را فراخوانی خواهد کرد زیرا ما آن را به طور
صریح در بازنویسی نهایی مشخص کرده ایم، اما تابع ``Base1.destroy`` را دور می زند، راه حل
این است که از ``super`` استفاده کنید:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract owned {
        constructor() { owner = payable(msg.sender); }
        address payable owner;
    }

    contract Destructible is owned {
        function destroy() virtual public {
            if (msg.sender == owner) selfdestruct(owner);
        }
    }

    contract Base1 is Destructible {
        function destroy() public virtual override { /* do cleanup 1 */ super.destroy(); }
    }


    contract Base2 is Destructible {
        function destroy() public virtual override { /* do cleanup 2 */ super.destroy(); }
    }

    contract Final is Base1, Base2 {
        function destroy() public override(Base1, Base2) { super.destroy(); }
    }

اگر ``Base2`` یک تابع از ``super`` را فراخوانی کند، این تابع را به سادگی در یکی از قرارداد های
پایه(پدر) خود فراخوانی نمی کند. بلکه، این تابع را در قرارداد پایه ی بعدی در نمودار وراثت
نهایی فراخوانی می کند، بنابراین ``()Base1.destroy`` (توجه داشته باشید که طبق ترتیب وراثت
نهایی -- بهمراه گرفته شده از آخرین قرارداد  : Final, Base2, Base1, Destructible,
owned) تعلق دارد. تابع واقعی که هنگام استفاده از super فراخوانی می شود، در زمینه
کلاس مورد استفاده مشخص نیست، گرچه نوع آن مشخص است. این مورد متداول مشابه
جستجوی روشهای مجازی است. 

.. index:: ! overriding;function

.. _function-overriding:

بازنویسی تابع 
===================

توابع پایه را می توان با ارث پذیری قرارداد ها، رفتار آنها را باز نویسی کرد در صورتی که بصورت
مجازی ``virtual`` نشان گذاری شده باشند. سپس در تابع اصلی باید از کلمه کلیدی ``override`` استفاده
شده باشد. تابعی که بازنویسی می شود ممکن است از تابعی که بازنویسی شده است فقط
میدان دید خارجی ``external`` یا عمومی ``public`` داشته باشد. تغییر پذیری ممکن است به دنبال دستور العمل دقیق
تر تغییر کند: غیر قابل پرداخت ``nonpayable`` می تواند بصورت ``pure`` یا ``view`` بازنویسی شود. قابل پرداخت ``payable``
یک استثنا است و نمی توان آن را بصورت دیگر حالت های تغییر پذیر عوض کرد.

مثال زیر عوض شدن تغییر پذیری و میدان دید را نشان می دهد:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract Base
    {
        function foo() virtual external view {}
    }

    contract Middle is Base {}

    contract Inherited is Middle
    {
        function foo() override public pure {}
    }

برای وراثت چندگانه، آخزین قراردادهای پایه ای که از آنها گرفته شده است تابع را تعریف می
کند، تابعی که از آن گرفته شده است باید صراحتا بعد از تعریف با کلمه کلیدی ``override`` نشان
گذاری شود. به عبارت دیگر، شما باید قراردادهای پایه ای که همان تابع را تعریف می کنند را
مشخص کنید و نباید آن تابع تا  به حال در جای دیگری از همان قراردادهای پایه بازنویسی شده
باشند( در همان مسیر در نمودار وراثت). علاوه بر این، اگر یک قرارداد تابع یکسانی را از چند جا
پایه های(غیر مرتبط) ارثبری کند، باید آن را صراحتا باز نویسی کند:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    contract Base1
    {
        function foo() virtual public {}
    }

    contract Base2
    {
        function foo() virtual public {}
    }

    contract Inherited is Base1, Base2
    {
        // Derives from multiple bases defining foo(), so we must explicitly
        // override it
        function foo() public override(Base1, Base2) {}
    }

اگر تابع در یک قرارداد پایه مشترک تعریف شده باشد یا اگر یک تابع منحصر به فرد در یک
قرارداد پایه شمترک وجود داشته باشد که قبلا همه توابع دیگر بازنویسی شده گرفته باشد، تعیین
بازنویسی صریح(explicit) نیاز نیست.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    contract A { function f() public pure{} }
    contract B is A {}
    contract C is A {}
    // No explicit override required
    contract D is B, C {}

به طور دقیقتر، اجباری نیست بازنویسی یک تابع که به صورت (مستقیم یا غیر مستقیم) از چند
پایه ارثبری کرده باشد اگر آنجا یک قراردادی باشد که بخشی از تمام مسیر برای امضا
بازنویسی اعلام کند، و (1) پیاده سازی پایه تابع انجام شده و مسیری از قرارداد جاری به پایه
توسط تابعی به همراه امضا آن اشاره می کند  یا (2) پیاده سازی پایه تابع انجام نشده و حداکثر
یک اشاره به تابع مذکور در تمام مسیرها در قرارداد جاری وجود دارد.

معنی اش این است که مسیر بازنویسی یک امضا ، مسیری است معادل یک مسیر در نمودار
وراثتی که از قرارداد مورد نظر شروع و در یک قرارداد اشاره شده یک تابع به همراه امضا آن
که بازنویسی نشده است پایان می یابد.

اگر تابعی را به عنوان ``virtual`` که بازنویسی شده است نشان گذاری نکنید، قرارداد هایی که آن
تابع را به ارث می برند نمی توانند رفتار آن را تغییر دهند.

.. note::

  توابعی که با میدان دید ``private`` هستند نمی توانند ``virtual``  باشند.

.. note::

  توابع بدون پیاده سازی باید در خارج از رابط ها بصورت ``virtual`` علامت گذاری شوند. در
  رابط ها، همه توابع به صورت خودکار ``virtual`` در نظر گرفته می شوند.

.. note::

  Starting from Solidity 0.8.8, the ``override`` keyword is not
  required when overriding an interface function, except for the
  case where the function is defined in multiple bases.


متغیرهای حالت عمومی می توانند توابع خارجی را نادیده بگیرند در صورتی که پارامترها(ورودی
ها) و نوع بازگشتی تابع با تابع گیرنده و متغیر های آن تطابق داشته باشند:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    contract A
    {
        function f() external view virtual returns(uint) { return 5; }
    }

    contract B is A
    {
        uint public override f;
    }

.. note::

  متغیرهای حالت عمومی می توانند توابع خارجی را بازنویسی کنند، در حالی 
  که خود آنها تمی توانند باز نویسی شوند.

.. index:: ! overriding;modifier

.. _modifier-overriding:

بازنویسی اصلاح کننده
====================

اصلاح کننده های تابع می توانند یکدیدگر را بازنویسی کنند. این کار به همان نحوی که :ref:`بازنویسی تابع <function-overriding>`
صورت می گیرد انجام می شود( با این تفاوت که برای اصلاح کننده ها بارگذاری وجود
ندارد). کلمه کلیدی ``virtual`` در اصلاح کننده های باز نویسی شده باید استفاده شود و کلمه
کلیدی ``override`` باید در اصلاح کننده های بازنویسی استفاده شود:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    contract Base
    {
        modifier foo() virtual {_;}
    }

    contract Inherited is Base
    {
        modifier foo() override {_;}
    }


در صورت وراثت متعدد، کلیه قراردادهای پایه مستقیم باید صراحتا مشخص شوند:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    contract Base1
    {
        modifier foo() virtual {_;}
    }

    contract Base2
    {
        modifier foo() virtual {_;}
    }

    contract Inherited is Base1, Base2
    {
        modifier foo() override(Base1, Base2) {_;}
    }



.. index:: ! constructor

.. _constructor:

سازنده ها 
============

سازنده یک تابع اختیاری است که با کلید واژه ``constructor`` اعلام می شود که پس از ایجاد
قرارداد اجرا می شود و در آنجا می توانید کد راه اندازی قرارداد را اجرا کنید.

قبل از اجرای کد سازنده، متغیر های حالت در صورتی که مقدار دهی اولیه شده باشند مقدار
دهی می شوند، یا اگر مقدار دهی نکرده باشید بصورت :ref:`پیش فرض مقدار<default-value>` دهی می شوند

پس از اجرای سازنده، کد نهایی قرارداد در بلاکچین استقرار داده می شود. استقرار کد هزینه
گاز اضافی خطی بر طول کد دارد. این کد شامل همه توابع ای است که بخشی ار رابط عمومی
هستند و بخشی تمامی توابعی که از طریق فراخوانی تابعی قابل دسترس هستند. این شامل کد
سازنده یا توابع داخلی که فقط از سازنده فرا خوانی می شوند

در صورتی که سازنده وجود نداشته باشد، قرارداد سازنده پیش فرض را در نظر می گیرد که
معادل ``{} ()constructor`` می باشد . برای مثال:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    abstract contract A {
        uint public a;

        constructor(uint _a) {
            a = _a;
        }
    }

    contract B is A(1) {
        constructor() {}
    }

شما می توانید از ورودی های داخلی در سازنده( به عنوان مثال اشاره گر های ذخیره سازی)
استفاده کنید. در این حالت، قرارداد باید :ref:`abstract <abstract-contract>` باشد ، زیرا به این ورودی ها نمی توان
مقادیر معتبر از خارج داد، بلکه فقط از طریق سازنده ی قرادادی که از آن اجرا شده است
امکان پذیر می باشد.

.. warning ::
    تا قبل از نسخه 0.4.22، سازنده ها به عنوان تابع هم نام با نام قرارداد تعریف می
    شدند. این نحوه نوشتاری منسوخ شد و دیگر از نسخه 0.5.0 مجاز نیست.

.. warning ::
    تا قبل از نسخه 0.7.0، شما باید محدوده ی دید سازنده ها را توسط ``internal`` یا
    ``public`` مشخص می کردید.


.. index:: ! base;constructor

آرگومانها برای سازنده های پایه 
===============================

سازنده های کلیه قرارداد های پایه طبق قوانین خطی که در زیر توضیح داده شده است،
فراخوانی می شوند. اگر سازنده های پایه ورودی داشته باشند، در قراردادشان باید آنها مشخص
شوند. این کار به دو روش قابل انجام است:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract Base {
        uint x;
        constructor(uint _x) { x = _x; }
    }

    // Either directly specify in the inheritance list...
    contract Derived1 is Base(7) {
        constructor() {}
    }

    // or through a "modifier" of the derived constructor.
    contract Derived2 is Base {
        constructor(uint _y) Base(_y * _y) {}
    }

روش اول مستقیما از طریق لیست ورارثت است (``is Base(7)``). روش دیگر از روش اول به این
صورت است که یک اصلاح کننده به عنوان بخشی از سازنده فراخوانی شده استفاده یم شود
(``Base(_y * _y)``). انجام روش اول راحت تر است اگر ورودی سازنده یک ثابت باید و رفتار
قرارداد را تعریف کند یا آن را توصیف کند. روش دوم زمانی باید مورد استفاده قرار گیرد که
ورودی های تابع سازنده پایه به مقادیر قرادادی که دارد وابسته باشد. ورودی ها ارثی بصورت
لیستی یا به سبک اصلاح کننده در سازنده آن قرارداد باید ارائه شوند. تعیین ورودی ها در هر دو
ی مکانها یک خطا است.

اگر یک قرارداد ارث گرفته شده ورودی های همه سازنده های قرارداد پایه خود را مشخص نکند،
abstract خواهد بود.

.. index:: ! inheritance;multiple, ! linearization, ! C3 linearization

.. _multi-inheritance:

وراثت چندگانه و خطی سازی
======================================

زبانهایی که امکان وراثت متعدد را دارند، با مشکلات متعددی روبرو هستند.
یکی از آنها `مشکل الماس <https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem>`_ است.
سالیدیتی مشابه پایتون است زیرا از "`خطی سازی C3 <https://en.wikipedia.org/wiki/C3_linearization>`_" برای اعمال نظم خاصی
در نمودار غیر چرخشی جهت دار(DAG) کلاسهای پایه استفاده می کند. این منجر به ویژگی
مطلوب یکنواختی می شود اما برخی از نمودار های ارثی را ممنوع می کند. بخصوص، ترتیب
اینکه کدام کلاسهای پایه در درستور ``is`` مهم است: شما باید مستقیما قراردادهای پایه را به
ترتیب "شبیه ترین" تا "مشتق شده ترین" فهرست کنید. توجه داشته باشید که این ترتیب
معکوس فقط در پایتون مورد استفاده قرار می گیرد.

یک روش ساده دیگر برای توضیح این امر این است که وقتی تابعی فراخوانی می شود که
چندین بار در قرارداد های مختلف تعریف شده است، پایه های داده شده از راست به چپ ( چپ
به راست در پایتون) به صورت عمقی-اول جستجو می شوند و در اولین تطابق متوقف می
شوند. اگر قرارداد پایه قبلا جستجو شده باشد، از آن صرفنظر می شود.

در کد زیر ، سالیدیتی خطای "خطی سازی نمودار وراثت غیر ممکن است" خواهد داد.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract X {}
    contract A is X {}
    // This will not compile
    contract C is A, X {}

The reason for this is that ``C`` requests ``X`` to override ``A``
(by specifying ``A, X`` in this order), but ``A`` itself
requests to override ``X``, which is a contradiction that
cannot be resolved.

Due to the fact that you have to explicitly override a function
that is inherited from multiple bases without a unique override,
C3 linearization is not too important in practice.

One area where inheritance linearization is especially important and perhaps not as clear is when there are multiple constructors in the inheritance hierarchy. The constructors will always be executed in the linearized order, regardless of the order in which their arguments are provided in the inheriting contract's constructor.  For example:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract Base1 {
        constructor() {}
    }

    contract Base2 {
        constructor() {}
    }

    // Constructors are executed in the following order:
    //  1 - Base1
    //  2 - Base2
    //  3 - Derived1
    contract Derived1 is Base1, Base2 {
        constructor() Base1() Base2() {}
    }

    // Constructors are executed in the following order:
    //  1 - Base2
    //  2 - Base1
    //  3 - Derived2
    contract Derived2 is Base2, Base1 {
        constructor() Base2() Base1() {}
    }

    // Constructors are still executed in the following order:
    //  1 - Base2
    //  2 - Base1
    //  3 - Derived3
    contract Derived3 is Base2, Base1 {
        constructor() Base1() Base2() {}
    }


Inheriting Different Kinds of Members of the Same Name
======================================================

It is an error when any of the following pairs in a contract have the same name due to inheritance:
  - a function and a modifier
  - a function and an event
  - an event and a modifier

As an exception, a state variable getter can override an external function.
