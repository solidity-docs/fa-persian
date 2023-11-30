.. index:: ! constant

.. _constants:

**************************************
 متغیر های وضعیت ثابت و تغییر ناپذیر
**************************************


متغیر های وضعیت می توانند به عنوان ثابت ``constant`` یا تغییرناپذیر ``immutable`` تعریف شوند. در هر دو حالت ، متغیر
ها بعد از ساخته شدن قرارداد غیر قابل دستکاری هستند. برای متغیر های ثابت  ``constant`` ، مقدار دهی
باید زمان کامپایل انجام شده باشد ، در صورتی که برای متغیر های تغییر ناپذیر ``immutable`` حین ساخته
شدن قرارداد می توان مقدار دهی کرد.

همچنین ممکن است متغیر های ثابت ``constant`` در سطح فایل تعریف شوند.

کامپایلر برای این نوع متغیر ها، شکاف(slot) ذخیره سازی رزرو نمی کند و هر مورد با مقدار
مربوطه خود جایگزین می شود.

در مقایسه با متغیر های وضعیت معمولی ، هزینه های گاز(gas) متغیر های ثابت و تغییرناپذیر
خیلی خیلی پایینتر است. برای یک متغیر ثابت عبارتی که به آن اختصاص داده شده است(مقدار
دهی شده است) در همه جاهایی که به آن دسترسی وجود دارد کپی شده و همچنین در هر بار
بازبینی می شود. این بهینه سازی محلی را ممکن می کند. متغیر های تغییر ناپذیر یک بار هنگام
ساخت بررسی می شوند و مقدار آنها در هر جایی که دسترسی به آن وجود دارد کپی می شود.
برای این نوع مقادیر، 32 بایت رزرو شده است حتی اگر این مقادیر در بایت کمتری هم جا
شوند. بنابراین ، مقادیر ثابت گاها ارزانتر از مقادیر تغییرناپذیر هستند.

تمامی نوع های متغیر فعلا برای ثابت و تغییر ناپذیر پیاشده سازی نشده اند . فقط نوع :ref:`strings <strings>`
(فقط برای ثابت) و نوعهای مقداری :ref:`value types <value-types>`. 

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.21;

    uint constant X = 32**22 + 8;

    contract C {
        string constant TEXT = "abc";
        bytes32 constant MY_HASH = keccak256("abc");
        uint immutable decimals = 18;
        uint immutable maxBalance;
        address immutable owner = msg.sender;

        constructor(uint decimals_, address ref) {
            if (decimals_ != 0)
                // Immutables are only immutable when deployed.
                // At construction time they can be assigned to any number of times.
                decimals = decimals_;

            // Assignments to immutables can even access the environment.
            maxBalance = ref.balance;
        }

        function isBalanceTooHigh(address other) public view returns (bool) {
            return other.balance > maxBalance;
        }
    }


ثابت 
========

برای متغیر های ثابت ``constant`` ، در زمان کامپایل مقدار باید ثابت باشد و در جایی که متغیر تعریف می
شود مقدار دهی می شود. هر عبارتی که به ذخیره سازی، داده های بلاکچین(مثل زمان بلوک ``block.timestamp`` ، 
آدرس(این[بلوک] ``address(this).balance`` ) میزان حساب ``address(this).balance`` یا شماره بوک ``block.number`` ) یا اجراییه های داده ای( ``msg.value`` یا
``()gasleft``) یا فراخوانی قراردادهای خارجی ممنوع است. عباراتی که ممکن است در تخصیص
حافظه اثر جانبی داشته باشند مجاز هستند، اما آنهایی که ممکن است بر سایر اجزای حافظه
تاثیر جانبی داشته باشند، مجاز نیستند. توابع داخلی ``keccak256`` ، ``sha256`` ، ``ripemd160`` ، 
``ecrecover`` ، ``addmod`` و ``mulmod`` مجاز هستند ( حتی به همراه عبارت ``keccak256`` ، می
توان قراردادهای خارجی نیز فراخوانی کرد).

دلیل اجازه به تخصیص عوارض جانبی بر روی حافظه برای امکانسازی ساخت اشایه پیچیده مثل
جدول-جستجو است. این ویژگی هنوز به طور کامل قابل استفاده نیست.

تغییر ناپذیر
=============

<<<<<<< HEAD
متغیر های تعریف شده به عنوان تغییرناپذیر ``immutable`` محدودیت کمتری نسبت به متغیر های ثابت ``constant`` دارند :
به متغیر های تغییر ناپذیر می توان یک مقدار دلخواه در سازنده قرارداد یا در زمان تعریف آنها
اختصاص داد. انها نمی توانند در زمان ساخت خوانده شوند و فقط یک بار مقدار دهی می شوند.

کد ساخت قرارداد که توسط کامپایلر تولید می شود قبل از بازگشت کد حین اجرایی قرارداد
تمامی مراجعی که به متغیر تغییر ناپذیر اشاره می کند را مقداردهی خواهد کرد. این زمانی
اهمیت دارد که می خواهید کد تولید شده توسط کامپایلر را با کد در حال حاضر ذخیره شده در
بلاکچین مقایسه کنید.

.. note::
  Immutables that are assigned at their declaration are only considered
  initialized once the constructor of the contract is executing.
  This means you cannot initialize immutables inline with a value
  that depends on another immutable. You can do this, however,
  inside the constructor of the contract.

  This is a safeguard against different interpretations about the order
  of state variable initialization and constructor execution, especially
  with regards to inheritance.
=======
Variables declared as ``immutable`` are a bit less restricted than those
declared as ``constant``: Immutable variables can be assigned a
value at construction time.
The value can be changed at any time before deployment and then it becomes permanent.

One additional restriction is that immutables can only be assigned to inside expressions for which
there is no possibility of being executed after creation.
This excludes all modifier definitions and functions other than constructors.

There are no restrictions on reading immutable variables.
The read is even allowed to happen before the variable is written to for the first time because variables in
Solidity always have a well-defined initial value.
For this reason it is also allowed to never explicitly assign a value to an immutable.

.. warning::
    When accessing immutables at construction time, please keep the :ref:`initialization order
    <state-variable-initialization-order>` in mind.
    Even if you provide an explicit initializer, some expressions may end up being evaluated before
    that initializer, especially when they are at a different level in inheritance hierarchy.

.. note::
    Before Solidity 0.8.21 initialization of immutable variables was more restrictive.
    Such variables had to be initialized exactly once at construction time and could not be read
    before then.

The contract creation code generated by the compiler will modify the
contract's runtime code before it is returned by replacing all references
to immutables with the values assigned to them. This is important if
you are comparing the
runtime code generated by the compiler with the one actually stored in the
blockchain. The compiler outputs where these immutables are located in the deployed bytecode
in the ``immutableReferences`` field of the :ref:`compiler JSON standard output <compiler-api>`.
>>>>>>> english/develop
