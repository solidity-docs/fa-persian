.. index:: ! function;modifier

.. _modifiers:

********************
اصلاح کننده های تابع
********************

اصلاح کننده ها جهت تغییر رفتار توابع به روش تعریف وار استفاده می شوند. برای مثال، شما
می توانید از یک اصلاح کننده جهت اعمال پیش شرط به صورت اتوماتیک برای اجرای آن تابع
استفاده کنید.

اصلاح کننده ها ویژگی های وراثتی قرارداد ها هستند و ممکن است توسط قرارداد هایی که
نشات گرفته شده اند باز نویسی شوند. اما فقط زمانی که بصورت ``virtual`` نشانه گذاری شده
باشند. برای جزئیات، لطفا از بخش :ref:`Modifier Overriding <modifier-overriding>` دیدن کنید.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.1 <0.9.0;

    contract owned {
        constructor() { owner = payable(msg.sender); }
        address payable owner;

        // This contract only defines a modifier but does not use
        // it: it will be used in derived contracts.
        // The function body is inserted where the special symbol
        // `_;` in the definition of a modifier appears.
        // This means that if the owner calls this function, the
        // function is executed and otherwise, an exception is
        // thrown.
        modifier onlyOwner {
            require(
                msg.sender == owner,
                "Only owner can call this function."
            );
            _;
        }
    }

    contract destructible is owned {
        // This contract inherits the `onlyOwner` modifier from
        // `owned` and applies it to the `destroy` function, which
        // causes that calls to `destroy` only have an effect if
        // they are made by the stored owner.
        function destroy() public onlyOwner {
            selfdestruct(owner);
        }
    }

    contract priced {
        // Modifiers can receive arguments:
        modifier costs(uint price) {
            if (msg.value >= price) {
                _;
            }
        }
    }

    contract Register is priced, destructible {
        mapping (address => bool) registeredAddresses;
        uint price;

        constructor(uint initialPrice) { price = initialPrice; }

        // It is important to also provide the
        // `payable` keyword here, otherwise the function will
        // automatically reject all Ether sent to it.
        function register() public payable costs(price) {
            registeredAddresses[msg.sender] = true;
        }

        function changePrice(uint _price) public onlyOwner {
            price = _price;
        }
    }

    contract Mutex {
        bool locked;
        modifier noReentrancy() {
            require(
                !locked,
                "Reentrant call."
            );
            locked = true;
            _;
            locked = false;
        }

        /// This function is protected by a mutex, which means that
        /// reentrant calls from within `msg.sender.call` cannot call `f` again.
        /// The `return 7` statement assigns 7 to the return value but still
        /// executes the statement `locked = false` in the modifier.
        function f() public noReentrancy returns (uint) {
            (bool success,) = msg.sender.call("");
            require(success);
            return 7;
        }
    }

اگر می خواهید به یک اصلاح کننده ی ``m`` تعریف شده در یک قرارداد ``C`` دسترسی پیدا کنید، می
توانید از ``C.m`` برای ارجاع دادن آن بدون بازپرسی مجازی استفاده کنید. این فقط در اصلاح
کننده های تعریف شده در قرارداد جاری یا قراردادهای اصلی(base) قابل استفاده است. اصلاح
کننده ها را می توان در کتابخانه ها نیز تعریف کرد اما محدود به توابع داخل همان کتابخانه می
ماند.

اصلاح کننده های متعدد بر روی یک تابع با مشخص کردن آنها در یک فضای خالی-جدا شده
اعمال می شوند و به ترتیب نماش داده شده ارزیابی می شوند.

اصلاح کننده ها نمی توانند به طور ضمنی به ورودی های تابع دسترسی داشته یا آنها را تغییر
داده و یا مقادیر بازگشتی را برگردانند. مقادیر آنها فقط به صراحت هنگام فراخوانی به آنها
منتقل می شود.

بازگشت واضح(Explicit) از یک اصلاح کننده یا بدنه تابع فقط از اصلاح کننده جاری و یا بنده تابع
خارج می شود. متغیر های اختصاص داده شده باز می گردند و کنترل جریان ``_`` از اصلاح کننده
قبلی ادامه می یابد.

.. warning::
   در نسخه قدیمی سالیدیتی، دستورات ``return`` در توابع اصلاح کننده ها با رفتار متفاوت
   عمل می کنند.

یک بازگشت واضح از یک اصلاح کننده با ``;return`` روی مقادیر بازگشتی توسط تابع تاثیر نمی
گذارد. با این حال، اصلاح کننده می تواند بدنه تابع را به طور کلی اجرا نکند و در این صورت
متغیر های باز گشتی بر روی :ref:`مقادیر پیش فرض<default-value>` خود تنظیم می شوند درست مانند اینکه تابع
دارای یک بدن خالی باشد.

نماد ``_`` می تواند چندین بار در اصلاح کننده ظاهر شود. هر رخداد با بدنه تابع جایگزین می شود.

عبارت های دلخواه برای ورودی (آرگومان) های اصلاح کننده مجاز هستند و در این مورد ، همه
نماد های قابل مشاهده در تابع در اصلاح کننده نیز قابل مشاهده هستند.
نماد های معرفی شده در اصلاح کننده در تابع قابل مشاهده نیستند(زیرا ممکن است توسط
بازنویسی(overriding) در آن تغییر کنند)
