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
    pragma solidity >=0.7.4;

    uint constant X = 32**22 + 8;

    contract C {
        string constant TEXT = "abc";
        bytes32 constant MY_HASH = keccak256("abc");
        uint immutable decimals;
        uint immutable maxBalance;
        address immutable owner = msg.sender;

        constructor(uint _decimals, address _reference) {
            decimals = _decimals;
            // Assignments to immutables can even access the environment.
            maxBalance = _reference.balance;
        }

        function isBalanceTooHigh(address _other) public view returns (bool) {
            return _other.balance > maxBalance;
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