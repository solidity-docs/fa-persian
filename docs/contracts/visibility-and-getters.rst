.. index:: ! visibility, external, public, private, internal

.. _visibility-and-getters:

**********************
قابلیت مشاهده و گیرنده‌ها (Visibility and Getters)
**********************

سالیدیتی دو نوع فراخوانی تابع را می‌شناسد: فراخوانی‌های داخلی که یک فراخوان EVM واقعی ایجاد نمی‌کنند 
(که به آن "message call" نیز گفته می‌شود) و فراخوانی‌های خارجی که این کار را انجام می‌دهند. به همین 
دلیل، چهار نوع قابلیت مشاهده برای توابع و متغیرهای حالت وجود دارد.



توابع باید به صورت  ``external`` ، ``public`` ، ``internal`` یا  ``private`` مشخص شوند. برای متغیرهای حالت،  ``external`` امکان پذیر نیست.



``external``

    توابع خارجی بخشی از رابط قرارداد هستند، به این معنی که می‌توان آنها را از سایر قراردادها و از طریق تراکنش‌ها 
    فراخوانی کرد. یک تابع خارجی  ``f`` را نمی‌توان به طور داخلی فراخوانی کرد (یعنی  ``()f`` کار نمی‌کند، اما 
    ``()this.f``  کار می‌کند).

 

``public``

    توابع عمومی بخشی از اینترفیس قرارداد هستند و می‌توانند به صورت داخلی یا از طریق پیام فراخوانی شوند. برای 
    متغیرهای حالت عمومی، یک تابع getter خودکار ( قسمت زیر را ببینید) ایجاد می‌شود.

``internal``

    این توابع و متغیرهای حالت را فقط می‌توان به صورت داخلی (یعنی از طریق قرارداد فعلی یا قراردادهای ناشی از 
    آن)، بدون استفاده از  ``this`` ، دسترسی داشت. این سطح دید پیش فرض برای متغیرهای حالت است.



``private``

    توابع خصوصی و متغیرهای حالت فقط برای قراردادی که در آن تعریف شده‌اند قابل مشاهده هستند و در 
    قراردادهای مشتق شده قابل مشاهده نیستن.


.. note::

    هر چیزی که در قرارداد قرار دارد برای همه ناظران خارج از بلاک چین قابل مشاهده است.  ``private`` 
    کردن چیزی فقط مانع از خواندن یا تغییر اطلاعات دیگر قراردادها می شود ، اما همچنان برای خارج از بلاک 
    چین قابل مشاهده است.




The visibility specifier is given after the type for
state variables and between parameter list and
return parameter list for functions.



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        function f(uint a) private pure returns (uint b) { return a + 1; }
        function setData(uint a) internal { data = a; }
        uint public data;
    }


In the following example, ``D``, can call ``c.getData()`` to retrieve the value of
``data`` in state storage, but is not able to call ``f``. Contract ``E`` is derived from
``C`` and, thus, can call ``compute``.


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

توابع گیرنده (Getter Functions)
================

The compiler automatically creates getter functions for
all **public** state variables. For the contract given below, the compiler will
generate a function called ``data`` that does not take any
arguments and returns a ``uint``, the value of the state
variable ``data``. State variables can be initialized
when they are declared.



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

The getter functions have external visibility. If the
symbol is accessed internally (i.e. without ``this.``),
it evaluates to a state variable.  If it is accessed externally
(i.e. with ``this.``), it evaluates to a function.


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

The next example is more complex:

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

این تابعی به شکل زیر ایجاد می کند.
نگاشت و آرایه‌ها (به استثنای آرایه‌های بایت) در struct حذف می‌شوند
، زیرا هیچ راه مناسبی برای انتخاب اعضای struct به صورت جداگانه یا ارائه کلیدی برای نگاشت وجود ندارد:

.. code-block:: solidity

    function data(uint arg1, bool arg2, uint arg3)
        public
        returns (uint a, bytes3 b, bytes memory e)
    {
        a = data[arg1][arg2][arg3].a;
        b = data[arg1][arg2][arg3].b;
        e = data[arg1][arg2][arg3].e;
    }
