.. index:: ! visibility, external, public, private, internal

.. _visibility-and-getters:

**********************
Visibility and Getters
**********************

Solidity knows two kinds of function calls: internal
ones that do not create an actual EVM call (also called
a "message call") and external
ones that do. Because of that, there are four types of visibility for
functions and state variables.

Functions have to be specified as being ``external``,
``public``, ``internal`` or ``private``.
For state variables, ``external`` is not possible.

``external``
    External functions are part of the contract interface,
    which means they can be called from other contracts and
    via transactions. An external function ``f`` cannot be called
    internally (i.e. ``f()`` does not work, but ``this.f()`` works).

``public``
    Public functions are part of the contract interface
    and can be either called internally or via
    messages. For public state variables, an automatic getter
    function (see below) is generated.

``internal``
    Those functions and state variables can only be
    accessed internally (i.e. from within the current contract
    or contracts deriving from it), without using ``this``.
    This is the default visibility level for state variables.

``private``
    Private functions and state variables are only
    visible for the contract they are defined in and not in
    derived contracts.

.. note::
    Everything that is inside a contract is visible to
    all observers external to the blockchain. Making something ``private``
    only prevents other contracts from reading or modifying
    the information, but it will still be visible to the
    whole world outside of the blockchain.

مشخص کننده میدان دید پس از نوع متغیر وضعیت و در توابع بین لیست پارامتر ها و لیست
پارامتر های بازگشتی می آید.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        function f(uint a) private pure returns (uint b) { return a + 1; }
        function setData(uint a) internal { data = a; }
        uint public data;
    }

در مثال زیر، ``D`` ، می تواند ``()c.getData`` برای گرفتن مقدار ``data`` در وضعیت ذخیره سازی
فراخوانی کند، اما نمی تواند ``f``  را فراخوانی کند. قرارداد ``E`` از ``C`` گرفته شده است پس می تواند
``compute`` را فراخوانی کند.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        uint private data;

        function f(uint a) private pure returns(uint b) { return a + 1; }
        function setData(uint a) public { data = a; }
        function getData() public view returns(uint) { return data; }
        function compute(uint a, uint b) internal pure returns (uint) { return a + b; }
    }

    // This will not compile
    contract D {
        function readData() public {
            C c = new C();
            uint local = c.f(7); // error: member `f` is not visible
            c.setData(3);
            local = c.getData();
            local = c.compute(3, 5); // error: member `compute` is not visible
        }
    }

    contract E is C {
        function g() public {
            C c = new C();
            uint val = compute(3, 5); // access to internal member (from derived to parent contract)
        }
    }

.. index:: ! getter;function, ! function;getter
.. _getter-functions:

توابع گیرنده
=============

کامپایلر بصورت اتوماتیک توابع گیرنده برای تمامی متغیر های وضعیت **عمومی** می سازد. در
قرارداد ارائه شده زیر، کامپایلر یک تابعی بنام ``data`` تولید خواهد کرد که پارامتر ورودی نخواهد
داشت و یک ``uint`` ، مقدار متغیر وضعیت ``data`` را بر خواهد گردانید. متغیر های وضعیت می
توانند هنگام تعریف مقدار دهی اولیه نیز شوند.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        uint public data = 42;
    }

    contract Caller {
        C c = new C();
        function f() public view returns (uint) {
            return c.data();
        }
    }

توابع گیرنده دید خارجی دارند. اگر به نماد(symbol) به طور داخلی دسترسی پیدا شود( مثل :
بدون استفاده از ``.this`` ) آن را به عنوان یک متغیر وضعیت می پندارد. اگر به طور خارجی
دسترسی پیدا شود آن را به عنوان یک تابع می پندارد.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract C {
        uint public data;
        function x() public returns (uint) {
            data = 3; // internal access
            return this.data(); // external access
        }
    }

اگر شما آرایه ای از متغیر های وضعیت از نوع ``public`` دارید، شما قادر خواهید بود که فقط یک
عنصر از آرایه را توسط تابع گیرنده تولید شده بر گردانید. این مکانیزم(سازوکار) بوجود آمده تا
از هزینه بالای گاز هنگام برگرداندن کل آرایه جلوگیری کند. شما می توانید با مشخص کردن
ورودی  عنصر مورد نیاز خود از آرایه بازگردانید، برای مثال ``myArray(0)`` . اگر شما می خواهید کل
آرایه را در یک فراخوانی بازگردانید نیاز مند نوشتن یک تابع هستید به عنوان مثال:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract arrayExample {
        // public state variable
        uint[] public myArray;

        // Getter function generated by the compiler
        /*
        function myArray(uint i) public view returns (uint) {
            return myArray[i];
        }
        */

        // function that returns entire array
        function getArray() public view returns (uint[] memory) {
            return myArray;
        }
    }

حالا شما می توانید از ``()getArray`` جهت گرفتن کل آرایه، بجای استفاده از ``myArray(i)`` که یک
عنصر به ازای هر فراخوانی باز می گرداند ، استفاده کنید. 

مثال بعدی پیچیدگی بیشتری دارد:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract Complex {
        struct Data {
            uint a;
            bytes3 b;
            mapping (uint => uint) map;
            uint[3] c;
            uint[] d;
            bytes e;
        }
        mapping (uint => mapping(bool => Data[])) public data;
    }

این تابعی به شکل زیر ایجاد می کند.  نگاشت در ساختار حذف شده است زیرا راه خوبی جهت
فراهم کردن کلید نگاشت وجود ندارد:

.. code-block:: solidity

    function data(uint arg1, bool arg2, uint arg3)
        public
        returns (uint a, bytes3 b, bytes memory e)
    {
        a = data[arg1][arg2][arg3].a;
        b = data[arg1][arg2][arg3].b;
        e = data[arg1][arg2][arg3].e;
    }
