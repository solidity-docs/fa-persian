.. index:: style, coding style

###################
راهنمای سبک نوشتاری
###################

*****
مقدمه
*****

این راهنما در نظر دارد در مورد نحوه ی کد نویسی سالیدیتی صحبت کند. این راهنما همواره در حال تکامل است و با
گذشت زمان امکان دارد قواعد جدید جایگزین قواعد قبلی شود.

بسیاری از پروژه ها سبک نوشتاری خود را پیاده سازی می کنند. درصورتی که تداخلی رخ دهد، سبک نوشتاری خاص
در پروژه الویت پیدا می کند.

ساختار بسیاری از سبک نوشتاری های پیشنهاد شده (recommended) از  `سبک نوشتاری pep8 <https://www.python.org/dev/peps/pep-0008/>`_ بر
گرفته شده است.

هدف این راهنما ارائه روش صحیح و یا بهترین روش برای کد نویسی سالیدیتی نیست. هدف این راهنما یکپارچگی است.
`pep8 <https://www.python.org/dev/peps/pep-0008/#a-foolish-consistency-is-the-hobgoblin-of-little-minds>`_ صراحتا این مفهوم را نشان بیان می کند.

.. note::

    یک سبک نوشتار در مورد یکپارچگی است. سبک نوشتاری به همراه یکپارچگی مهم است. یکپارچگی در یک پروژه بسیار مهم است.یکپارچگی در یک ماژول یا تابع از همه مهمتر است.

    اما مهمتر از همه: **دانستن زمان غیر یکپارچه بودن است** -- بعضی اوغات یکپارچگی در نظر گرفته نمی شود. در صورت شک، به امثال دیگر نگاه کنید و تصمیم بگیرید کدام بهتر است و از پرسیدن سوال دریغ نکنید!


***********
نحوه چیدمان
***********


تو رفتگی
========

برای هر سطح تو رفتگی از 4 عدد دکمه space استفاده کنید.

Tabs یا Spaces
==============

روش دکمه  space برای تو رفتگی مقدم تر است.

از ادغام tab ها و space ها باید جلوگیری شود.

خط های خالی
===========

تعاریف های سطح اول خود را به دو خط خالی در سورس کد(source code) سالیدتی احاطه کنید.

بله:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract A {
        // ...
    }


    contract B {
        // ...
    }


    contract C {
        // ...
    }

خیر:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract A {
        // ...
    }
    contract B {
        // ...
    }

    contract C {
        // ...
    }

داخل قراردادی که تعاریف تابع با یک خط احاطه شده اند.

خط های خالی بین گروه های مرتبط می توانند حذف شوند( مثل تابع stub به عنوان یک قرارداد انتزاعی (abstract contact))

بله:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    abstract contract A {
        function spam() public virtual pure;
        function ham() public virtual pure;
    }


    contract B is A {
        function spam() public pure override {
            // ...
        }

        function ham() public pure override {
            // ...
        }
    }

خیر:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    abstract contract A {
        function spam() virtual pure public;
        function ham() public virtual pure;
    }


    contract B is A {
        function spam() public pure override {
            // ...
        }
        function ham() public pure override {
            // ...
        }
    }

.. _maximum_line_length:

ماکسیموم طول خط
===================

`طبق پیشنهاد PEP8 <https://www.python.org/dev/peps/pep-0008/#maximum-line-length>`_ ماکسیموم طول هرخط 79 (یا 99) کاراکتر باشد به خواننده کمک می کند تا به راحتی کد رابخواند.

خط های پیچیده باید قوانین ذیل را رعایت کنند:

1. ورودی (argument) اول نباید پس از پاراتز باز باشد.
2. یک و فقط یک تورفتگی باید استفاده شود.
3. هر ورودی (argument) باید در خط خودش باشد.
4. نشانگرپایان دهنده :code:`(;)` ، باید به تنهایی درآخرین خط قرار گیرد.

فراخوانی تابع

بله:

.. code-block:: solidity

    thisFunctionCallIsReallyLong(
        longArgument1,
        longArgument2,
        longArgument3
    );

خیر:

.. code-block:: solidity

    thisFunctionCallIsReallyLong(longArgument1,
                                  longArgument2,
                                  longArgument3
    );

    thisFunctionCallIsReallyLong(longArgument1,
        longArgument2,
        longArgument3
    );

    thisFunctionCallIsReallyLong(
        longArgument1, longArgument2,
        longArgument3
    );

    thisFunctionCallIsReallyLong(
    longArgument1,
    longArgument2,
    longArgument3
    );

    thisFunctionCallIsReallyLong(
        longArgument1,
        longArgument2,
        longArgument3);

بیانیه های انتسابات

بله:

.. code-block:: solidity

    thisIsALongNestedMapping[being][set][to_some_value] = someFunction(
        argument1,
        argument2,
        argument3,
        argument4
    );

خیر:

.. code-block:: solidity

    thisIsALongNestedMapping[being][set][to_some_value] = someFunction(argument1,
                                                                       argument2,
                                                                       argument3,
                                                                       argument4);

تعاریف رخداد ها و تولید کنندگان رخداد

بله:

.. code-block:: solidity

    event LongAndLotsOfArgs(
        address sender,
        address recipient,
        uint256 publicKey,
        uint256 amount,
        bytes32[] options
    );

    LongAndLotsOfArgs(
        sender,
        recipient,
        publicKey,
        amount,
        options
    );

خیر:

.. code-block:: solidity

    event LongAndLotsOfArgs(address sender,
                            address recipient,
                            uint256 publicKey,
                            uint256 amount,
                            bytes32[] options);

    LongAndLotsOfArgs(sender,
                      recipient,
                      publicKey,
                      amount,
                      options);

کدگذاری فایل منبع
====================

UTF-8 یا ASCII مقبول تر است.

Imports
=======

بیانیه های import حتما باید در بالای فایل نوشته شوند.

بله:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    import "./Owned.sol";

    contract A {
        // ...
    }

    contract B is Owned {
        // ...
    }

خیر:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract A {
        // ...
    }


    import "./Owned.sol";


    contract B is Owned {
        // ...
    }

ترتیب توابع
===========

ترتیب بندی به خواننده کمک می کند تا بتواند مشخص کند کدام توابع می توانند فراخوانی شوند و پیدا کردن تعاریف توابع سازنده وعقب گرد راحتر می شود.

توابع باید طبق میدان دید و ترتیبشان گروهبندی شوند:

- سازنده
- تابع گیرنده (اگر وجود داشته باشد)
- تابع عقب گرد ( اگر وجود داشته باشد)
- خارجی
- عمومی
- داخلی
- خصوصی

داخل یک گروهبندی ، مکان توابع ``view``  و ``pure`` را در آخر بگذارید.

بله:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;
    contract A {
        constructor() {
            // ...
        }

        receive() external payable {
            // ...
        }

        fallback() external {
            // ...
        }

        // External functions
        // ...

        // External functions that are view
        // ...

        // External functions that are pure
        // ...

        // Public functions
        // ...

        // Internal functions
        // ...

        // Private functions
        // ...
    }

خیر:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;
    contract A {

        // External functions
        // ...

        fallback() external {
            // ...
        }
        receive() external payable {
            // ...
        }

        // Private functions
        // ...

        // Public functions
        // ...

        constructor() {
            // ...
        }

        // Internal functions
        // ...
    }

فضای خالی در اصطلاحات
=====================

در شرایط زیر از فضاهای خالی خودداری کنید: 

بلافاصله داخل پاراتزها، آکولاد ها و کوروشه ها، به جز تعاریف توابع تک خطی.

بله:

.. code-block:: solidity

    spam(ham[1], Coin({name: "ham"}));

خیر:

.. code-block:: solidity

    spam( ham[ 1 ], Coin( { name: "ham" } ) );

استثنا:

.. code-block:: solidity

    function singleLine() public { spam(); }

بلافاصله بعد از نقطه ویرگول (;):

بله:

.. code-block:: solidity

    function spam(uint i, Coin coin) public;

خیر:

.. code-block:: solidity

    function spam(uint i , Coin coin) public ;

بیش از یک space در کنار یک انتساب یا  دیگرعامل ها جهت منظم کردن با یکدیگر.

بله:

.. code-block:: solidity

    x = 1;
    y = 2;
    long_variable = 3;

خیر:

.. code-block:: solidity

    x             = 1;
    y             = 2;
    long_variable = 3;

فضای خالی را در توابع دریافت و عقبگرد قرار ندهید:

بله:

.. code-block:: solidity

    receive() external payable {
        ...
    }

    fallback() external {
        ...
    }

خیر:

.. code-block:: solidity

    receive () external payable {
        ...
    }

    fallback () external {
        ...
    }


ساختار های کنترلی
=================

آکولاد ها بدنه ی یک قرارداد، کتابخانه، توابع نشان را می دهند و ساختارشان باید:

* باید در همان خط تعاریف باز شوند
* باید در خطی که در همان سطح تورفتگی از اول تعریف باز شده اند ، بسته شوند
* یک فضای خالی باید قبل از آکولاد باز باشد

بله:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract Coin {
        struct Bank {
            address owner;
            uint balance;
        }
    }

خیر:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract Coin
    {
        struct Bank {
            address owner;
            uint balance;
        }
    }

همین توصیه ها برای ساختار های کنترلی  ``if`` ، ``else`` ، ``while``  و ``for`` پیشنهاد می شود.

به علاوه یک فضای خالی باید بین ساختار های کنترلی ``if`` ، ``while`` و ``for`` باشد و یک فضای خالی بین بلوک پارانتزی
شرطی  و آکولاد شروع بلوک شرطی باید باشد. 
as well as a single space between the conditional parenthetic
block and the opening brace.

بله:

.. code-block:: solidity

    if (...) {
        ...
    }

    for (...) {
        ...
    }

خیر:

.. code-block:: solidity

    if (...)
    {
        ...
    }

    while(...){
    }

    for (...) {
        ...;}

برای ساختار های کنترلی که بنده ی آنها یک بیانیه دارد ، نداشتن آکولاد مشکلی ایجاد نمی کند اگر بیانیه *if* شامل یک خط باشد.

بله:

.. code-block:: solidity

    if (x < 10)
        x += 1;

خیر:

.. code-block:: solidity

    if (x < 10)
        someArray.push(Coin({
            name: 'spam',
            value: 42
        }));

برای بلوک های ``if`` که دارای یک ``else`` یا ``else if``  هستند ``else`` باید در همان خطی که آکولاد ``if`` تمام می شوند، قرار
گیرد. در مقایسه با قواعد دیگر ساختار های بلوکی-شکل این یک استثنا است.

بله:

.. code-block:: solidity

    if (x < 3) {
        x += 1;
    } else if (x > 7) {
        x -= 1;
    } else {
        x = 5;
    }


    if (x < 3)
        x += 1;
    else
        x -= 1;

خیر:

.. code-block:: solidity

    if (x < 3) {
        x += 1;
    }
    else {
        x -= 1;
    }

تعاریف توابع
=============

برای تعریف توابع کوتاه ، پیشنهاد می شود که آکولاد باز بنده ی تابع در همان خط تعریف تابع بماند.

آکولا بسته باید در همان سطح تورفتگی تعریف تابع بسته شود.

قبل آکولاد باز باید از یک فضای خالی استفاده کرد.

بله:

.. code-block:: solidity

    function increment(uint x) public pure returns (uint) {
        return x + 1;
    }

    function increment(uint x) public pure onlyOwner returns (uint) {
        return x + 1;
    }

خیر:

.. code-block:: solidity

    function increment(uint x) public pure returns (uint)
    {
        return x + 1;
    }

    function increment(uint x) public pure returns (uint){
        return x + 1;
    }

    function increment(uint x) public pure returns (uint) {
        return x + 1;
        }

    function increment(uint x) public pure returns (uint) {
        return x + 1;}

ترتیب اصلاح کنندگان (modifier) یک تابع باید:

1. میدان دید
2. تغییر پذیری (mutability)
3. مجازی
4. بازنویسی تابع (override)
5. اصلاح کنندگان شخصی (custom modifiers)

بله:

.. code-block:: solidity

    function balance(uint from) public view override returns (uint)  {
        return balanceOf[from];
    }

    function shutdown() public onlyOwner {
        selfdestruct(owner);
    }

خیر:

.. code-block:: solidity

    function balance(uint from) public override view returns (uint)  {
        return balanceOf[from];
    }

    function shutdown() onlyOwner public {
        selfdestruct(owner);
    }

برای تعاریف طولانی تابع، توصیه می شود که هر ورودی (argument) را در همان سطح تو
رفتگی بدنه تابع قرار دهید. پرانتز بسته و آکولاد باز باید در خط خود قرار بگیرند و همان سطح
تورفتگی تابع را داشته باشند. 

بله:

.. code-block:: solidity

    function thisFunctionHasLotsOfArguments(
        address a,
        address b,
        address c,
        address d,
        address e,
        address f
    )
        public
    {
        doSomething();
    }

خیر:

.. code-block:: solidity

    function thisFunctionHasLotsOfArguments(address a, address b, address c,
        address d, address e, address f) public {
        doSomething();
    }

    function thisFunctionHasLotsOfArguments(address a,
                                            address b,
                                            address c,
                                            address d,
                                            address e,
                                            address f) public {
        doSomething();
    }

    function thisFunctionHasLotsOfArguments(
        address a,
        address b,
        address c,
        address d,
        address e,
        address f) public {
        doSomething();
    }

اگر یک تابع با تعریف طولانی دارای تابع تغییردهنده(modifier) باشد،  هر تابع تغییر دهنده باید
در خط خود قرار گیرد.

بله:

.. code-block:: solidity

    function thisFunctionNameIsReallyLong(address x, address y, address z)
        public
        onlyOwner
        priced
        returns (address)
    {
        doSomething();
    }

    function thisFunctionNameIsReallyLong(
        address x,
        address y,
        address z,
    )
        public
        onlyOwner
        priced
        returns (address)
    {
        doSomething();
    }

خیر:

.. code-block:: solidity

    function thisFunctionNameIsReallyLong(address x, address y, address z)
                                          public
                                          onlyOwner
                                          priced
                                          returns (address) {
        doSomething();
    }

    function thisFunctionNameIsReallyLong(address x, address y, address z)
        public onlyOwner priced returns (address)
    {
        doSomething();
    }

    function thisFunctionNameIsReallyLong(address x, address y, address z)
        public
        onlyOwner
        priced
        returns (address) {
        doSomething();
    }

پارامتر های خروجی و بیانیه های بازگشتی باید به همان سبک توصیه شده خطوط طولانی در قسمت :ref:`ماکسیموم طول خط <maximum_line_length>` .

بله:

.. code-block:: solidity

    function thisFunctionNameIsReallyLong(
        address a,
        address b,
        address c
    )
        public
        returns (
            address someAddressName,
            uint256 LongArgument,
            uint256 Argument
        )
    {
        doSomething()

        return (
            veryLongReturnArg1,
            veryLongReturnArg2,
            veryLongReturnArg3
        );
    }

خیر:

.. code-block:: solidity

    function thisFunctionNameIsReallyLong(
        address a,
        address b,
        address c
    )
        public
        returns (address someAddressName,
                 uint256 LongArgument,
                 uint256 Argument)
    {
        doSomething()

        return (veryLongReturnArg1,
                veryLongReturnArg1,
                veryLongReturnArg1);
    }

برای توابع سازنده در قراردادهای وراثتی که نیازمند ورودی(argument) است، اگر تعریف تابع
طولانی یا خواندنش سخت باشد، توصیه می شود توابع سازنده های اساسی را به همان شیوه
توابع تغییر دهنده(modifier) روس خط جدید قرار دهید.

بله:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;
    // Base contracts just to make this compile
    contract B {
        constructor(uint) {
        }
    }
    contract C {
        constructor(uint, uint) {
        }
    }
    contract D {
        constructor(uint) {
        }
    }

    contract A is B, C, D {
        uint x;

        constructor(uint param1, uint param2, uint param3, uint param4, uint param5)
            B(param1)
            C(param2, param3)
            D(param4)
        {
            // do something with param5
            x = param5;
        }
    }

خیر:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    // Base contracts just to make this compile
    contract B {
        constructor(uint) {
        }
    }


    contract C {
        constructor(uint, uint) {
        }
    }


    contract D {
        constructor(uint) {
        }
    }


    contract A is B, C, D {
        uint x;

        constructor(uint param1, uint param2, uint param3, uint param4, uint param5)
        B(param1)
        C(param2, param3)
        D(param4) {
            x = param5;
        }
    }


    contract X is B, C, D {
        uint x;

        constructor(uint param1, uint param2, uint param3, uint param4, uint param5)
            B(param1)
            C(param2, param3)
            D(param4) {
                x = param5;
            }
    }


هنگام تعریف توابع کوتاهی که یک عبارت دارند، انجام این کار در یک خط مجاز است.

مجاز:

.. code-block:: solidity

    function shortFunction() public { doSomething(); }

این آموزه ها برای بهبود خوانایی تعریف توابع بودند. نویسندگان باید از بهترین قضاوت خود
استفاده کنند راهنمای وی نمی تواند تمامی جایگزینی های احتمالی تعریف توابع را پوشش دهد.

نگاشت ها
========

در تعاریف متغیر ، از دکمه فاصله(space) جهت جداسازی کلمه کلیدی ``mapping`` با بخش
تعریف-نوع استفاده نکنید. کلمه کلیدی ``mapping`` تو در تو را از طریق دکمه فاصله(space) با
بخش-نوع خودشان جدا نکنید.

بله:

.. code-block:: solidity

    mapping(uint => uint) map;
    mapping(address => bool) registeredAddresses;
    mapping(uint => mapping(bool => Data[])) public data;
    mapping(uint => mapping(uint => s)) data;

خیر:

.. code-block:: solidity

    mapping (uint => uint) map;
    mapping( address => bool ) registeredAddresses;
    mapping (uint => mapping (bool => Data[])) public data;
    mapping(uint => mapping (uint => s)) data;

تعاریف متغیر 
============

در تعریف متغیر های آرایه ای نباید بین بخش نوع و کروشه باز و بسته فاصله باشد.

بله:

.. code-block:: solidity

    uint[] x;

خیر:

.. code-block:: solidity

    uint [] x;


توصیه های دیگر
==============

* رشته ها بجای تک کوتنشن (‘) باید با جفت کوتنشن (“) نمایش داده شوند.

بله:

.. code-block:: solidity

    str = "foo";
    str = "Hamlet says, 'To be or not to be...'";

خیر:

.. code-block:: solidity

    str = 'bar';
    str = '"Be yourself; everyone else is already taken." -Oscar Wilde';

* بین هر دو طرف عملگر ها باید فاصله باشد.

بله:

.. code-block:: solidity
    :force:

    x = 3;
    x = 100 / 10;
    x += 3 + 4;
    x |= y && z;

خیر:

.. code-block:: solidity
    :force:

    x=3;
    x = 100/10;
    x += 3+4;
    x |= y&&z;

* عملگر هایی که دارای الویت بالایی نسبت به دیگر عملگر ها دارند می توانند فضای خالی نداشته
  باشند. برای افزایش خوانایی در عبارات پیچیده مجاز است. فضای خالی در هر دو طرف عملگر
  باید به یک مقدار (یکسان) باشد:

بله:

.. code-block:: solidity

    x = 2**3 + 5;
    x = 2*y + 3*z;
    x = (a+b) * (a-b);

خیر:

.. code-block:: solidity

    x = 2** 3 + 5;
    x = y+z;
    x +=1;

************
ترتیب چیدمان
************

چیدمان عناصر قرارداد به ترتیب ذیل است:

1. بیانیه Pragma
2. بیانیه Import
3. رابط ها (Interfaces)
4. کتابخانه ها (Libraries)
5. قرارداد ها

در داخل هر قرارداد، کتابخانه یا رابط به ترتیب ذیل است:

1. تعاریف نوع (Type declarations)
2. متغیر های وضعیت (State variables)
3. رخداد ها (Events)
4. توابع

.. note::

    امکان دارد تعریف نوع ها نزدیک رخداد ها یا متغیر های وضعیت کد را واضح تر کند.

*************
اصول نامگذاری
*************

اصول نامگذاری زمانی قدرتمند هستند که به صورت سرتاسری وفق داده و استفاده شوند.
استفاده از اصول مختلف اطلاعات *اضافی* منتقل می کند یا به بیان دیگر بلافاصله در دسترس
نخواهد بود.

توصیه های نامگذاری که در اینجا ارائه می شوند برای بهبود دادن به خوانایی است.
پس آنها قواعد نیستند، بلکه دستورالعمل هایی هستند که برای انتقال اطلاعات بیشتر از طریق
نام چیز ها کمک می کنند.

نهایتا، یکپارچگی در داخل یک مقر کد(codebase) همیشه باید جانشین هر نوع اصولیکه در این
سند ذکر شده است، شود


سبک های نامگذاری
================

برای جلوگیری از سردرگمی، از نام های زیر برای اشاره به سبک های مختلف نامگذاری استفاده
خواهد شد.

* ``b`` ( تک کلمه کوچک-چین(lowercase))
* ``B`` ( تک کلمه بزرگ-چین(uppercase))
* ``lowercase`` (کوچک-چین)
* ``lower_case_with_underscores`` (کوچک-چین به همراه خط زیر)
* ``UPPERCASE`` (بزرگ-چین)
* ``UPPER_CASE_WITH_UNDERSCORES`` (بزرگ-چین به همراه خط زیر)
* ``CapitalizedWords`` (or CapWords) ( یا کلمات با حروف بزرگ اول) (CapWord)
* ``mixedCase`` (مثل نوع قبل است با این تفاوت که حرف اول کلمه اول کوچک و باقی حرف اول کلمات دیگر بزرگ)
* ``Capitalized_Words_With_Underscores``  ( مثل نوع ما قبل با این تفاوت که از خط زیر نیر استفاده می شود)

.. note:: هنگام استفاده از CapitalizedWords تمام حروف کلمه اولیه را با حروف بزرگ وارد کنید. بنابراین HTTPServerError بهتر از HttpServerError است. هنگام استفاده از mixedCase تمام حروف کلمه اولیه کوچک و بقیه کلمات را با حرف بزرگ اول بنویسید. بنابراین xmlHTTPRequest بهتر از XMLHTTPRequest است.


نام های اجتنابی
===============

* ``l`` - حرف ال کوچک
* ``O`` - حرف او بزرگ
* ``I`` - حرف آی بزرگ

هرگز از اینها برای متغیر های تک حرفی استفاده نکنید. آنها اغلب با اعداد صفر و یک قاطی می
شوند و غیر قابل تشخیص هستند.


نام های قرار داد و کتابخانه
===========================

* قراردادها و کتابخانه ها باید به سبک CapWord نام گذاری شوند. امثال: ``SimpleToken`` ، ``SmartBank`` ، ``CertificateHashRepository`` ، ``Player`` ،  ``Congress``  ، ``Owned``.
* قرارداد و کتابخانه باید همچنین با نام فایلشان هم نام باشند.
* اگر فایل یک قرارداد شامل چندین قرارداد و/یا کتابخانه باشد، آنگاه نام فایل *قرارداد هسته* با نام قرارداد باید هم نام باشد. این توصیه نمی شود به هر حال در صورت امکان باید اجتناب شود.

همانطور که در مثال زیر نماش داده شده است، اگر نام قرارداد ``Congress`` باشد و نام کتابخانه
اش Owned باشد، آنگاه نام فایل های مربوطه باید ``Congress.sol`` و ``Owned.sol`` باشند.

بله:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    // Owned.sol
    contract Owned {
        address public owner;

        constructor() {
            owner = msg.sender;
        }

        modifier onlyOwner {
            require(msg.sender == owner);
            _;
        }

        function transferOwnership(address newOwner) public onlyOwner {
            owner = newOwner;
        }
    }

و داخل ``Congress.sol``:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    import "./Owned.sol";


    contract Congress is Owned, TokenRecipient {
        //...
    }

خیر:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    // owned.sol
    contract owned {
        address public owner;

        constructor() {
            owner = msg.sender;
        }

        modifier onlyOwner {
            require(msg.sender == owner);
            _;
        }

        function transferOwnership(address newOwner) public onlyOwner {
            owner = newOwner;
        }
    }

و داخل ``Congress.sol``:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.7.0;


    import "./owned.sol";


    contract Congress is owned, tokenRecipient {
        //...
    }

نام های ساختار
==============

ساختار ها باید به سبک CapWords نام گذاری شوند. امثال: ``MyCoin`` ، ``Position`` ،  ``PositionXY``. 


نام های رخداد
=============

رخداد ها باید به سبک CapWords نام گذاری شوند. امثال: ``Deposit`` ، ``Transfer`` ، ``Approval`` ، ``BeforeTransfer`` ، ``AfterTransfer``.


نام های تابع 
==============

توابع باید از سبک mixedCase استفاده کنند. امثال : ``getBalance`` ، ``transfer`` ، ``verifyOwner`` ، ``addMember`` ، ``changeOwner``.


نام های ورودی تابع
==================

ورودی های تابع باید از سبک mixedCase استفاده کنند. امثال : ``initialSupply`` ، ``account`` ، ``recipientAddress`` ، ``senderAddress`` ، ``newOwner``.

زمان نوشتن توابع کتابخانه ای که روی یک ساختار خاص کار می کنند ، ساختار باید اولین ورودی
(argument) باشد و باید همیشه نامش ``self`` باشد.


نام های متغیر های  وضعیت و محلی
===============================

از mixedCase استفاده می شود امثال :  ``totalSupply`` ، ``remainingSupply`` ، ``balancesOf`` ،  ``creatorAddress`` ، ``isPreSale`` ، ``tokenExchangeRate``.


ثابت ها
=========

تمامی ثابت ها باید با حروف بزرگ و جداشده با خط زیر نام گذاری شوند. امثال:
``MAX_BLOCKS`` ،  ``TOKEN_NAME`` ، ``TOKEN_TICKER`` ، ``CONTRACT_VERSION``.


نام های اصلاح کننده
===================

از mixedCase استفاده می شود امثال : ``onlyBy`` ، ``onlyAfter`` ، ``onlyDuringThePreSale``.


Enums
=====

Enums در سبک تعاریف ساده باید از روش نام گذاری CapWords استفاده شود. امثال:  ``TokenGroup`` ، ``Frame`` ، ``HashStyle`` ، ``CharacterLocation``.


اجتناب از تداخل نام گذاری
===========================

* ``single_trailing_underscore_``

این اصول زمانی پیشنهاد می شود که نام مورد نظر با نام داخلی یا نام دیگر رزرو شده ای
تداخل داشته باشد.

.. _style_guide_natspec:

*******
NatSpec
*******

قرارداد های سالیدیتی می توانند شامل توضیحات NatSpec باشند. آنها باید به همراه سه
اسلش (``///``) و یا یک بلوک دو ستاره ای (``/** ... */``)  مستقیما بالای تعاریف تابع یا بیانیه ها
استفاده می شوند.

برای مثال، قراردادی از :ref:`یک قرارداد ساده هوشمند <simple-smart-contract>` به همراه توضیحات اضافه شده شبیه مثال زیر است:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    /// @author The Solidity Team
    /// @title A simple storage example
    contract SimpleStorage {
        uint storedData;

        /// Store `x`.
        /// @param x the new value to store
        /// @dev stores the number in the state variable `storedData`
        function set(uint x) public {
            storedData = x;
        }

        /// Return the stored value.
        /// @dev retrieves the value of the state variable `storedData`
        /// @return the stored value
        function get() public view returns (uint) {
            return storedData;
        }
    }

توصیه می شود که قرارداد های سالیدیتی کلا با :ref:`NatSpec <natspec>` برای تمامی رابط های عمومی (هر چیزی که داخل ABI است) حاشیه نویسی شوند.

لطفا برای توضیحات دقیق به بخش مربوط به :ref:`NatSpec <natspec>` مراجعه کنید.
