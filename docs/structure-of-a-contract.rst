.. index:: contract, state variable, function, event, struct, enum, function;modifier

.. _contract_structure:

***********************
ساختار قرارداد
***********************

  قراردادهای سالیدیتی مانند کلاس‌های زبان‌های شی‌گرا هستند. هر قرارداد می‌تواند شامل دستورات   :ref:`structure-state-variables`    ، :ref:`structure-functions` ، :ref:`structure-function-modifiers`    ، :ref:`structure-events`   ،  :ref:`structure-errors`    ، :ref:`structure-struct-types`  و :ref:`structure-enum-types`   باشد. علاوه بر این، قراردادها می‌توانند از سایر قراردادها ارث ببرند.


  قراردادهای دیگری نیز وجود دارد که :ref:`کتابخانه‌ها<libraries>`   و :ref:`رابط‌ها<interfaces>`  نامیده می‌شوند.



بخش مربوط به :ref:`قرارداد‌ها<contracts>`   شامل جزئیات بیشتری نسبت به این بخش است که یک مرور سریع رو ارائه می‌دهد.

.. _structure-state-variables:

متغیرهای حالت
===============

  متغیرهای حالت متغیرهایی هستند که مقادیر آنها به طور دائمی در storage  قرارداد ذخیره می‌شود.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract SimpleStorage {
        uint storedData; // State variable
        // ...
    }


برای دیدن انواع متغیرهای حالت معتبر  و :ref:`قابلیت مشاهده  آن‌ها و گیرنده‌ها<visibility-and-getters>`   برای انتخاب‌های احتمالی برای مشاهده انواع متغیرها، به بخش :ref:`types`  مراجعه کنید.


.. _structure-functions:

توابع
=========

 توابع، واحد اجرایی کد هستند. توابع معمولاً در داخل قرارداد تعریف می‌شوند، اما در خارج از قراردادها نیز می‌توانند تعریف شوند.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.1 <0.9.0;

    contract SimpleAuction {
        function bid() public payable { // Function
            // ...
        }
    }

    // Helper function defined outside of a contract
    function helper(uint x) pure returns (uint) {
        return x * 2;
    }


:ref:`فراخوانی توابع<function-calls>`  می‌توانند به صورت داخلی یا خارجی اتفاق بیفتند و دارای :ref:`قابلیت مشاهده<visibility-and-getters>`  مختلفی نسبت به سایر قراردادها هستند.  :ref:`توابع<functions>`  پارامترها را می‌پذیرند و  :ref:`متغیرها را برمی‌گردانند<function-parameters-return-variables>`  تا پارامترها و مقادیر بین آنها منتقل شود.

.. _structure-function-modifiers:

توابع اصلاح کننده 
==================


از تابع اصلاح کننده‌ها می‌توان برای اصلاح سمنتیک  توابع به روشی اعلانی استفاده کرد (به  :ref:`توابع اصلاح کننده  <modifiers>` در بخش قراردادها مراجعه کنید).


  اضافه بار ، به این معنا که داشتن نام اصلاح کننده یکسان با پارامترهای مختلف، امکان پذیر نیست. مانند توابع، اصلاح کننده‌ها را می‌توان لغو کرد.

مانند توابع، اصلاح کننده‌ها نیز می‌توانند :ref:`نادیده<modifier-overriding>`  گرفته شوند.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.22 <0.9.0;

    contract Purchase {
        address public seller;

        modifier onlySeller() { // Modifier
            require(
                msg.sender == seller,
                "Only seller can call this."
            );
            _;
        }

        function abort() public view onlySeller { // Modifier usage
            // ...
        }
    }

.. _structure-events:

رویدادها 
======


رویدادها رابط‌های راحتی برای ورود به امکانات EVM هستند.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.21 <0.9.0;

    contract SimpleAuction {
        event HighestBidIncreased(address bidder, uint amount); // Event

        function bid() public payable {
            // ...
            emit HighestBidIncreased(msg.sender, msg.value); // Triggering event
        }
    }

برای اطلاع از چگونگی اعلام رویداد‌ها و استفاده از آنها از طریق dapp، به بخش :ref:`رویدادها<events>` در بخش قراردادها مراجعه کنید.


.. _structure-errors:

خطاها 
======

خطاها به شما امکان می‌دهند نام‌ها و داده‌های توصیفی را برای شرایط شکست تعریف کنید. از خطاها می‌توان در :ref:`دستورات revert <revert-statement>`  استفاده کرد. در مقایسه با توضیحات رشته ، خطاها بسیار ارزان‌تر هستند و به شما امکان می‌دهند داده‌های اضافی را رمزگذاری کنید. برای توصیف خطا برای کاربر می‌توانید از NatSpec استفاده کنید.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    /// Not enough funds for transfer. Requested `requested`,
    /// but only `available` available.
    error NotEnoughFunds(uint requested, uint available);

    contract Token {
        mapping(address => uint) balances;
        function transfer(address to, uint amount) public {
            uint balance = balances[msg.sender];
            if (balance < amount)
                revert NotEnoughFunds(amount, balance);
            balances[msg.sender] -= amount;
            balances[to] += amount;
            // ...
        }
    }


برای اطلاعات بیشتر به :ref:`خطاها<errors>` و دستورات Revert در قسمت قراردادها مراجعه کنید.

.. _structure-struct-types:

انواع Struct
=============

 :ref:`structs` انواع تعریف شده سفارشی هستند که می‌توانند متغیرهای مختلفی را گروه بندی کنند (به بخش Structها در بخش انواع مراجعه کنید).



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract Ballot {
        struct Voter { // Struct
            uint weight;
            bool voted;
            address delegate;
            uint vote;
        }
    }

.. _structure-enum-types:

انواع Enum
==========

  از Enums می‌توان برای ایجاد انواع سفارشی با مجموعه محدودی از  "مقادیر ثابت "  استفاده کرد (به قسمت :ref:`enums`  در بخش انواع مراجعه کنید).


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract Purchase {
        enum State { Created, Locked, Inactive } // Enum
    }
