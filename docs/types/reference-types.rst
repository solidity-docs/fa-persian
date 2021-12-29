.. index:: ! type;reference, ! reference type, storage, memory, location, array, struct

.. _reference-types:

انواع مرجع (Reference Types)
===============

مقادیر نوع مرجع را می‌توان از طریق چندین نام مختلف اصلاح کرد. هر زمان که متغیر از نوع مقدار  استفاده 
شود، در جایی که یک کپی مستقل دریافت می‌کنید نوع مرجع را با انواع مقدار مقایسه کنید. به همین دلیل، 
انواع مرجع باید با دقت بیشتری از انواع مقادیر رسیدگی شوند. در حال حاضر، انواع مرجع شامل structها، 
آرایه‌ها و mapping ها است. اگر از یک نوع مرجع استفاده می‌کنید، همیشه باید صریحاً منطقه داده‎ای  را که 
نوع در آن ذخیره شده‌است فراهم کنید:  ``memory``  (طول عمر آن فقط به یک فراخوانی کنند تابع خارجی 
محدود می‌شود)،   ``storage`` (مکانی که متغیرهای حالت در آن ذخیره می‌شوند، جایی که طول عمر آنها به 
طول عمر قرارداد محدود می‌شود) یا  ``calldata`` (مکان داده ویژه‌ای که شامل آرگومان‌های تابع است).



یک انتساب یا تبدیل نوع که مکان داده را تغییر می‌دهد، همیشه موجب یک عملیات کپی خودکار خواهد شد، در 
حالی که انتساب در داخل همان مکان داده فقط در برخی موارد برای انواع storage  کپی می‌شوند.

.. _data-location:

مکان داده (Data location)
-------------

  هر نوع مرجع حاوی یادداشت اضافی است، "data location"، در مورد مکانی که ذخیره می‌شود. سه مکان 
  داده وجود دارد:  ``memory``  ،  ``storage`` و  ``calldata`` . 
  ``calldata`` یک منطقه غیرقابل تغییر و غیرقابل ماندگاری است که آرگومان‌های تابع در آن ذخیره می‌شود و 
  بیشتر مانند مِمُوری رفتار می‌کند. برای پارامترهای توابع خارجی لازم است اما می‌تواند برای سایر متغیرها نیز استفاده شود.
  


.. note::
   
     اگر می‌توانید، سعی کنید از  ``calldata`` به عنوان مکان داده استفاده کنید زیرا از کپی جلوگیری می‌کند 
     و همچنین مطمئن می‌شوید که داده‌ها قابل اصلاح نیستند. آرایه‌ها و struct های دارای مکان  داده  ``calldata`` نیز می‌توانند از توابع برگردانده شوند، اما اختصاص چنین نوع‌هایی امکان پذیر نیست.


.. note::


    Prior to version 0.6.9 data location for reference-type arguments was limited to
    ``calldata`` in external functions, ``memory`` in public functions and either
    ``memory`` or ``storage`` in internal and private ones.
    Now ``memory`` and ``calldata`` are allowed in all functions regardless of their visibility.

    
.. note::
    
     قبل از نسخه 0.5.0 ، مکان داده را می‌توان حذف کرد، و بسته به نوع متغیر، نوع تابع و غیره به مکان‌های مختلف پیش فرض می‌رود ، اما اکنون همه انواع پیچیده باید یک مکان داده مشخص داشته باشند.


.. _data-location-assignment:

مکان داده و رفتار انتساب (Data location and assignment behaviour)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

مکان داده  نه تنها برای ماندگاری داده‌ها بلکه برای معنای انتساب‌ها نیز مهم هستند:


*	انتساب‌ها  بین ``storage``  و  ``memory`` (یا از  ``calldata`` ) همیشه یک کپی مستقل ایجاد می‌کنند.
*	انتساب‌ها از  ``memory`` به  ``memory`` فقط مراجع را ایجاد می‌کند. این بدان معناست که تغییرات در یک متغیر مِمُوری در سایر متغیرهای مِمُوری که به داده‌های مشابه ارجاع می‌کنند نیز قابل مشاهده‌است.
*	انتساب‌ها از ``storage``  به یک متغیر  storage  محلی** نیز فقط یک مرجع اختصاص می‌دهند** .
*	سایر انتسابات به ``storage`` همیشه کپی می‌شوند. نمونه‌هایی برای این مورد، انتساب به متغیرهای حالت یا اعضای متغیرهای محلی از نوع  storage struct می‌باشند، حتی اگر متغیر محلی فقط یک مرجع باشد.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.5.0 <0.9.0;

    contract C {
        // The data location of x is storage.
        // This is the only place where the
        // data location can be omitted.
        uint[] x;

        // The data location of memoryArray is memory.
        function f(uint[] memory memoryArray) public {
            x = memoryArray; // works, copies the whole array to storage
            uint[] storage y = x; // works, assigns a pointer, data location of y is storage
            y[7]; // fine, returns the 8th element
            y.pop(); // fine, modifies x through y
            delete x; // fine, clears the array, also modifies y
            // The following does not work; it would need to create a new temporary /
            // unnamed array in storage, but storage is "statically" allocated:
            // y = memoryArray;
            // This does not work either, since it would "reset" the pointer, but there
            // is no sensible location it could point to.
            // delete y;
            g(x); // calls g, handing over a reference to x
            h(x); // calls h and creates an independent, temporary copy in memory
        }

        function g(uint[] storage) internal pure {}
        function h(uint[] memory) public pure {}
    }

.. index:: ! array

.. _arrays:

آرایه‌ها
------

آرایه‌ها می‌توانند اندازه ثابت زمان کامپایل داشته باشند، یا می‌توانند اندازه پویا داشته باشند.


نوع آرایه‌ای با اندازه ثابت ``k`` و نوع عنصر  ``T`` به صورت  ``T[k]`` و آرایه‌ای با اندازه پویا به صورت  ``[]T`` نوشته می‌شود.


به عنوان مثال، آرایه‌ای از 5 آرایه دینامیکی ``uint`` به صورت  ``uint[][5]`` نوشته می‌شود. علامت گذاری 
در مقایسه با برخی از زبان‌های دیگر معکوس می‌شود. در سالیدیتی ،  ``X[3]`` همیشه یک آرایه است که شامل 
سه عنصر از نوع  ``X`` است، حتی اگر  ``X`` خودش یک آرایه باشد. این مورد در زبان‌های دیگر مانند C وجود ندارد.



شاخص‌ها  مبتنی بر صفر هستند و دسترسی در خلاف جهت اعلامیه  است.


به عنوان مثال، اگر یک متغیر   ``uint[][5] memory x`` داشته باشید، با استفاده از ``x[2][6]``  به 
``uint``  هفتم در آرایه پویای سوم دسترسی پیدا می‌کنید و برای دسترسی به آرایه پویای سوم، از ``x[2]`` استفاده 
کنید. باز هم اگر یک آرایه  ``T[5]`` aبرای نوع  ``T`` دارید که می‌تواند یک آرایه نیز باشد،  ``a[2]`` همیشه 
نوع  ``T`` را دارد.


عناصر آرایه می‌توانند از هر نوع شامل mapping یا struct باشند. محدودیت‌های کلی برای انواع اعمال 
می‌شود، به این دلیل که mapping‌ها فقط در محل داده  ``storage`` می‌توانند ذخیره شوند و توابع قابل 
مشاهده به صورت عمومی، نیاز به پارامترهایی دارند که از نوع :ref:`ABI types <ABI>` باشند. 


می‌توان آرایه‌های متغیر حالت را به صورت  ``public`` علامت گذاری کرد و از سالیدیتی برای ایجاد یک 
:ref:`getter <visibility-and-getters>` استفاده کرد. شاخص عددی به یک پارامتر مورد نیاز برای  getter تبدیل می‌شود.



دستیابی به آرایه‌ای که از انتهای آن گذشته است، ادعای ناموفقی را ایجاد می‌کند. از روش های  ``()push.`` 
و  ``push(value).``  می‌توان برای افزودن یک عنصر جدید در انتهای آرایه استفاده کرد، جایی 
که ``()push.`` یک عنصر مقداردهی شده صفر را اضافه می‌کند و مرجعی را به آن برمی‌گرداند.


.. index:: ! string, ! bytes

.. _strings:

.. _bytes:

bytes و string  به عنوان آرایه‌ها
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

  متغیرهای نوع  ``bytes`` و ``string``  آرایه‌های خاصی هستند. ``bytes``  مانند  ``[]bytes1`` است، اما در 
  calldata و مِمُوری کاملاً بسته بندی شده است. ``string`` برابر با ``bytes``  است اما اجازه دسترسی به 
  طول  یا index  را نمی‌دهد.

Variables of type ``bytes`` and ``string`` are special arrays. The ``bytes`` type is similar to ``bytes1[]``,
but it is packed tightly in calldata and memory. ``string`` is equal to ``bytes`` but does not allow
length or index access.

سالیدیتی توابع دستکاری  string  ندارد، اما کتابخانه‌هایstring  طرف سوم وجود دارد. همچنین 
می‌توانید دو  string  را توسط keccak256-hash آنها با استفاده از 
``keccak256(abi.encodePacked(s1)) == keccak256(abi.encodePacked(s2))``  مقایسه کنید و دو رشته را با استفاده از  ``bytes.concat(bytes(s1), bytes(s2))``  بهم پیوست دهید.



از آنجا که  ``[]bytes1`` سی و یک لایه بایت بین عناصر اضافه می‌کند، شما باید از ``bytes``  بیش 
از ``[]bytes1`` استفاده کنید زیرا ارزان‌تر است. به عنوان یک قاعده کلی، برای داده‌های ``bytes``  خام با طول 
دلخواه از bytes  و برای داده‌های  ``string``  با طول دلخواه (UTF-8) از  ``string`` استفاده کنید. اگر 
می‌توانید طول را به تعداد مشخصی از بایت محدود کنید، همیشه از یکی از انواع مقدار ``bytes1``  
تا  ``bytes32`` استفاده کنید زیرا بسیار ارزان‌تر هستند.


.. note::
    If you want to access the byte-representation of a string ``s``, use
    ``bytes(s).length`` / ``bytes(s)[7] = 'x';`` . Keep in mind
    that you are accessing the low-level bytes of the UTF-8 representation,
    and not the individual characters.

    اگر می‌خواهید به نمایش بایت  یک رشته‌ی ``s`` دسترسی پیدا کنید، از  ``bytes(s).length`` / ``bytes(s)[7] = 'x';`` استفاده کنید. بخاطر داشته باشید که شما به بایت‌های سطح پایین، 
    پیش نمایش UTF-8 و نه به کارکترهای جداگانه دسترسی پیدا می‌کنید.

// @saracodic  Isnt shown correctly in text  
``bytes(s).length`` / ``bytes(s)[7] = 'x';``
    

.. index:: ! bytes-concat

.. _bytes-concat:

تابع bytes.concat 
^^^^^^^^^^^^^^^^^^^^^^^^^
با استفاده از ``bytes.concat``  می‌توانید تعدادی متغیر از ``bytes``  
یا  ``bytes1 ... bytes32``  را بهم پیوند دهید. این تابع یک تک آرایه ``bytes memory``  را 
برمی‌گرداند که شامل محتویات آرگومان‌ها بدون padding است. اگر می‌خواهید از پارامترهای رشته‌ای یا انواع 
دیگر استفاده کنید، ابتدا باید آنها را به  ``bytes`` یا ``bytes1``/.../``bytes32`` تبدیل کنید.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    contract C {
        bytes s = "Storage";
        function f(bytes calldata c, string memory m, bytes16 b) public view {
            bytes memory a = bytes.concat(s, c, c[:2], "Literal", bytes(m), b);
            assert((s.length + c.length + 2 + 7 + bytes(m).length + 16) == a.length);
        }
    }

اگر بدون آرگومان  ``bytes.concat``   فراخوانی کنید، آرایه‌ای خالی از ``bytes``  را برمی‌گرداند.



.. index:: ! array;allocating, new

تخصیص آرایه های مِمُوری (Allocating Memory Arrays)
^^^^^^^^^^^^^^^^^^^^^^^^

  آرایه‌های مِمُوری با طول پویا را می‌توان با استفاده از عملگر  ``new`` ایجاد کرد. در مقایسه با آرایه‌های 
  storage، تغییر اندازه آرایه‌های مِمُوری امکان پذیر نیست (به عنوان مثال توابع عضو ``push.``  در دسترس 
  نیستند). یا باید اندازه مورد نیاز را از قبل محاسبه کنید یا یک آرایه مِمُوری جدید ایجاد کنید و هر عنصر را کپی کنید.



مثل همهِ متغیرها در سالیدیتی، عناصر آرایه‌های تازه تخصیص یافته همیشه با :ref:`مقدار پیش فرض<default-value>` مقداردهی می‌شوند.



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        function f(uint len) public pure {
            uint[] memory a = new uint[](7);
            bytes memory b = new bytes(len);
            assert(a.length == 7);
            assert(b.length == len);
            a[6] = 8;
        }
    }

.. index:: ! array;literals, ! inline;arrays

آرایه‌ لیترال‌ها 
^^^^^^^^^^^^^^
  آرایه لیترال لیستی جدا شده با کاما از یک یا چند عبارت است که در بِراکت مربعی محصور شده 
  است  (``[...]``). به عنوان مثال  ``[1, a, f(3)]`` . نوع آرایه به صورت زیر تعیین می‌شود:

// @saracodic  Isnt shown correctly in text 

همیشه یک آرایه مِمُوری با اندازه ایستا است که طول آن تعداد عبارات است.



نوع پایه‌ی آرایه، نوع اولین عبارت در لیست است به طوری که می‌توان بقیه عبارات را به طور ضمنی به آن تبدیل 
کرد. اگر این امکان وجود نداشته باشد خطای نوع است.


  کافی نیست نوعی وجود داشته باشد که همه عناصر بتوانند به آن تبدیل شوند. یکی از عناصر باید از آن نوع باشد.



   در مثال زیر، نوع  ``[3, 2, 1]`` ،  ``uint8[3] memory`` می‌باشد، زیرا نوع هر یک از این 
   ثابت‌ها  ``uint8`` است. اگر می‌خواهید نتیجه از نوع  ``uint8[3] memory`` باشد، باید اولین عنصر را 
   به  ``uint`` تبدیل کنید.



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        function f() public pure {
            g([uint(1), 2, 3]);
        }
        function g(uint[3] memory) public pure {
            // ...
        }
    }

آرایه لیترال  ``[1-, 1]`` نامعتبر است زیرا نوع عبارت اول  ``uint8`` است در حالی که نوع دوم  ``int8`` است 
و نمی‌توان آنها را به طور ضمنی به یکدیگر تبدیل کرد. برای استفاده از آن، می‌توانید از  ``[int8(1), -1]`` استفاده کنید.



   از آنجا که آرایه‌های مِمُوری با اندازه ثابت از انواع مختلف قابل تبدیل به یکدیگر نیستند (حتی اگر انواع پایه بتوانند)، اگر می‌خواهید از لیترال‌های دو بعدی استفاده کنید، باید یک نوع پایه مشترک را به طور صریح مشخص کنید:


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        function f() public pure returns (uint24[2][4] memory) {
            uint24[2][4] memory x = [[uint24(0x1), 1], [0xffffff, 2], [uint24(0xff), 3], [uint24(0xffff), 4]];
            // The following does not work, because some of the inner arrays are not of the right type.
            // uint[2][4] memory x = [[0x1, 1], [0xffffff, 2], [0xff, 3], [0xffff, 4]];
            return x;
        }
    }

آرایه های مِمُوری با اندازه ثابت را نمی‌توان به آرایه‌های مِمُوری با اندازه پویا اختصاص داد، یعنی موارد زیر امکان پذیر نیست:



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    // This will not compile.
    contract C {
        function f() public {
            // The next line creates a type error because uint[3] memory
            // cannot be converted to uint[] memory.
            uint[] memory x = [uint(1), 3, 4];
        }
    }

در آینده برنامه ریزی شده‌است که سالیدیتی این محدودیت را برطرف کند، اما به دلیل نحوهِ انتقال آرایه‌ها در ABI، مشکلاتی ایجاد می‌شود.

   اگر می‌خواهید آرایه‌هایی با اندازه پویا را شروع کنید، باید عناصر جداگانه را اختصاص دهید:



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract C {
        function f() public pure {
            uint[] memory x = new uint[](3);
            x[0] = 1;
            x[1] = 3;
            x[2] = 4;
        }
    }

.. index:: ! array;length, length, push, pop, !array;push, !array;pop

.. _array-members:

اعضای آرایه (Array Members) public
^^^^^^^^^^^^^

**length**:
    آرایه‌ها دارای یک عضو  ``length`` هستند که شامل تعداد عناصر آنها است. طول آرایه‌های مِمُوری پس از ایجاد 
    ثابت است (اما پویا، یعنی می‌تواند به پارامترها در زمان اجرا بستگی داشته باشد).

    Arrays have a ``length`` member that contains their number of elements.
    The length of memory arrays is fixed (but dynamic, i.e. it can depend on
    runtime parameters) once they are created.
**push()**:

    آرایه‌های storage و  bytes پویا (نه  ``string``) دارای یک عضو تابع به نام ``()push`` هستند که 
    می‌توانید از آن برای افزودن یک عنصر مقداردهی شده صفر در انتهای آرایه استفاده کنید. یک ارجاع به عنصر را 
    برمی‌گرداند، بنابراین می‌توان از آن مانند ``x.push().t = 2``  یا  ``x.push() = b`` استفاده کرد. 

     Dynamic storage arrays and ``bytes`` (not ``string``) have a member function
     called ``()push`` that you can use to append a zero-initialised element at the end of the array.
     It returns a reference to the element, so that it can be used like
     ``x.push().t = 2`` or ``x.push() = b``.


**push(x)**:
    
    آرایه‌های storage و  ``bytes`` پویا (نه  `string``) دارای یک عضو تابع به نام ``push(x)``  هستند که 
    می‌توانید از آن برای افزودن یک عنصر مشخص در انتهای آرایه استفاده کنید. تابع هیچ چیزی بر نمی‌گرداند.

     Dynamic storage arrays and ``bytes`` (not ``string``) have a member function
     called ``push(x)`` that you can use to append a given element at the end of the array.
     The function returns nothing.
**pop**:

    آرایه‌های storage و  ``bytes`` پویا (نه  ``string``) دارای یک عضو تابع به نام ``pop``   هستند که می‌توانید 
    برای حذف یک عنصر از انتهای آرایه استفاده کنید. همچنین به طور ضمنی  :ref:`delete<delete>`  را روی عنصر حذف شده فراخوانی می‌کند.

     Dynamic storage arrays and ``bytes`` (not ``string``) have a member
     function called ``pop`` that you can use to remove an element from the
     end of the array. This also implicitly calls :ref:`delete<delete>` on the removed element.

.. note::

    افزایش طول یک آرایه storage با فراخوانی  ``()push`` هزینه گاز ثابت را دارد زیرا مقداردهی اولیه 
    storage صفر می‌باشد، در حالی که کاهش طول با فراخوانی ``()pop``  هزینه‌ای دارد که به "اندازه" عنصر 
    حذف شده بستگی دارد. اگر آن عنصر آرایه‌ای باشد، می‌تواند بسیار پرهزینه باشد، زیرا شامل پاک کردن صریح عناصر حذف شده مشابه  با فراخوانی :ref:`delete<delete>`  روی آنها است.

    Increasing the length of a storage array by calling ``()push``
    has constant gas costs because storage is zero-initialised,
    while decreasing the length by calling ``()pop`` has a
    cost that depends on the "size" of the element being removed.
    If that element is an array, it can be very costly, because
    it includes explicitly clearing the removed
    elements similar to calling :ref:`delete<delete>` on them.

.. note::

    برای استفاده از آرایه های توابع خارجی (به جای عملکرد public) ، باید ABI coder v2 را فعال کنید.

    

.. note::
    در نسخه‌های EVM قبل از  Byzantium، دسترسی به آرایه‌های پویا از برگشتیِ توابعِ فراخوانی‌ امکان 
    پذیر نبود. اگر توابعی را فراخوانی می‌کنید که آرایه‌های پویا را برمی‌گردانند، حتماً از EVMی استفاده کنید که 
    روی حالت Byzantium تنظیم شده‌است.
    
   

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    contract ArrayContract {
        uint[2**20] m_aLotOfIntegers;
        // Note that the following is not a pair of dynamic arrays but a
        // dynamic array of pairs (i.e. of fixed size arrays of length two).
        // Because of that, T[] is always a dynamic array of T, even if T
        // itself is an array.
        // Data location for all state variables is storage.
        bool[2][] m_pairsOfFlags;

        // newPairs is stored in memory - the only possibility
        // for public contract function arguments
        function setAllFlagPairs(bool[2][] memory newPairs) public {
            // assignment to a storage array performs a copy of ``newPairs`` and
            // replaces the complete array ``m_pairsOfFlags``.
            m_pairsOfFlags = newPairs;
        }

        struct StructType {
            uint[] contents;
            uint moreInfo;
        }
        StructType s;

        function f(uint[] memory c) public {
            // stores a reference to ``s`` in ``g``
            StructType storage g = s;
            // also changes ``s.moreInfo``.
            g.moreInfo = 2;
            // assigns a copy because ``g.contents``
            // is not a local variable, but a member of
            // a local variable.
            g.contents = c;
        }

        function setFlagPair(uint index, bool flagA, bool flagB) public {
            // access to a non-existing index will throw an exception
            m_pairsOfFlags[index][0] = flagA;
            m_pairsOfFlags[index][1] = flagB;
        }

        function changeFlagArraySize(uint newSize) public {
            // using push and pop is the only way to change the
            // length of an array
            if (newSize < m_pairsOfFlags.length) {
                while (m_pairsOfFlags.length > newSize)
                    m_pairsOfFlags.pop();
            } else if (newSize > m_pairsOfFlags.length) {
                while (m_pairsOfFlags.length < newSize)
                    m_pairsOfFlags.push();
            }
        }

        function clear() public {
            // these clear the arrays completely
            delete m_pairsOfFlags;
            delete m_aLotOfIntegers;
            // identical effect here
            m_pairsOfFlags = new bool[2][](0);
        }

        bytes m_byteData;

        function byteArrays(bytes memory data) public {
            // byte arrays ("bytes") are different as they are stored without padding,
            // but can be treated identical to "uint8[]"
            m_byteData = data;
            for (uint i = 0; i < 7; i++)
                m_byteData.push();
            m_byteData[3] = 0x08;
            delete m_byteData[2];
        }

        function addFlag(bool[2] memory flag) public returns (uint) {
            m_pairsOfFlags.push(flag);
            return m_pairsOfFlags.length;
        }

        function createMemoryArray(uint size) public pure returns (bytes memory) {
            // Dynamic memory arrays are created using `new`:
            uint[2][] memory arrayOfPairs = new uint[2][](size);

            // Inline arrays are always statically-sized and if you only
            // use literals, you have to provide at least one type.
            arrayOfPairs[0] = [uint(1), 2];

            // Create a dynamic byte array:
            bytes memory b = new bytes(200);
            for (uint i = 0; i < b.length; i++)
                b[i] = bytes1(uint8(i));
            return b;
        }
    }

.. index:: ! array;slice

.. _array-slices:

برش‌های آرایه (Array Slices)
------------

// translate id different from the original
----------
  برش‌های آرایه نمایی از قسمت پیوسته آرایه است. آنها به صورت  x[end - 1] نوشته می‌شوند، جایی 
  که  startو  endعباراتی هستند که منجر به نوع uint256 می‌شوند (یا به طور ضمنی قابل تبدیل به آن 
  هستند). اولین عنصر برش  x[start] و آخرین عنصر  x[end - 1]می‌باشد.

Array slices are a view on a contiguous portion of an array.
They are written as ``x[start:end]``, where ``start`` and
``end`` are expressions resulting in a uint256 type (or
implicitly convertible to it). The first element of the
slice is ``x[start]`` and the last element is ``x[end - 1]``.
-----------



اگر  ``start`` از  ``end`` بیشتر باشد یا اگر  ``end`` از طول آرایه بیشتر باشد، یک استثنا ایجاد می‌شود.


 ``start`` و  ``end`` هر دو اختیاری هستند:  ``start`` به طور پیشفرض ``0``  و  ``end`` به طور پیش فرض به طول آرایه می‌باشد.


برش‌های آرایه هیچ عضوی ندارند. آنها به طور ضمنی قابل تبدیل به آرایه‌هایی از نوع اصلی و دسترسی به index 
را پشتیبانی می‌کنند. دسترسی index در آرایه اصلی قطعی نیست، اما وابسته به شروع برش است.


برش‌های آرایه دارای نام نوع نیستند، به این معنی که هیچ متغیری نمی‌تواند برش‌های آرایه‌ای را به عنوان نوع 
داشته باشد، آنها فقط در عبارات میانی وجود دارند.



.. note::
      از هم اکنون، برش‌های آرایه فقط برای آرایه‌های فراخوانی داده پیاده سازی می‌شوند.


برش‌های آرایه برای رمزگشایی با داده‌های ثانویه ABI که در پارامترهای تابع منتقل می‌شوند مفید هستند:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.8.5 <0.9.0;
    contract Proxy {
        /// @dev Address of the client contract managed by proxy i.e., this contract
        address client;

        constructor(address _client) {
            client = _client;
        }

        /// Forward call to "setOwner(address)" that is implemented by client
        /// after doing basic validation on the address argument.
        function forward(bytes calldata _payload) external {
            bytes4 sig = bytes4(_payload[:4]);
            // Due to truncating behaviour, bytes4(_payload) performs identically.
            // bytes4 sig = bytes4(_payload);
            if (sig == bytes4(keccak256("setOwner(address)"))) {
                address owner = abi.decode(_payload[4:], (address));
                require(owner != address(0), "Address of owner cannot be zero.");
            }
            (bool status,) = client.delegatecall(_payload);
            require(status, "Forwarded call failed.");
        }
    }



.. index:: ! struct, ! type;struct

.. _structs:

Struct‌ها
-------

سالیدیتی راهی برای تعریف نوع‌های جدید به صورت Struct فراهم می‌کند، که در مثال زیر نشان داده شده است:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    // Defines a new type with two fields.
    // Declaring a struct outside of a contract allows
    // it to be shared by multiple contracts.
    // Here, this is not really needed.
    struct Funder {
        address addr;
        uint amount;
    }

    contract CrowdFunding {
        // Structs can also be defined inside contracts, which makes them
        // visible only there and in derived contracts.
        struct Campaign {
            address payable beneficiary;
            uint fundingGoal;
            uint numFunders;
            uint amount;
            mapping (uint => Funder) funders;
        }

        uint numCampaigns;
        mapping (uint => Campaign) campaigns;

        function newCampaign(address payable beneficiary, uint goal) public returns (uint campaignID) {
            campaignID = numCampaigns++; // campaignID is return variable
            // We cannot use "campaigns[campaignID] = Campaign(beneficiary, goal, 0, 0)"
            // because the right hand side creates a memory-struct "Campaign" that contains a mapping.
            Campaign storage c = campaigns[campaignID];
            c.beneficiary = beneficiary;
            c.fundingGoal = goal;
        }

        function contribute(uint campaignID) public payable {
            Campaign storage c = campaigns[campaignID];
            // Creates a new temporary memory struct, initialised with the given values
            // and copies it over to storage.
            // Note that you can also use Funder(msg.sender, msg.value) to initialise.
            c.funders[c.numFunders++] = Funder({addr: msg.sender, amount: msg.value});
            c.amount += msg.value;
        }

        function checkGoalReached(uint campaignID) public returns (bool reached) {
            Campaign storage c = campaigns[campaignID];
            if (c.amount < c.fundingGoal)
                return false;
            uint amount = c.amount;
            c.amount = 0;
            c.beneficiary.transfer(amount);
            return true;
        }
    }

این قرارداد عملکرد کامل قرارداد سرمایه گذاری جمعی را فراهم نمی‌کند، اما شامل مفاهیم پایه‌ای لازم برای درک 
structها است. نوع structها را می‌توان در داخل ن 
mappingها و آرایه‌ها استفاده کرد و خود آنها می‌توانند شامل 
mappingها و آرایه‌ها باشند.


ممکن است یک struct  از یک نوع خود عضو داشته باشد، اگرچه struct  می‌تواند مقدار نوع یک عضو از 
mapping باشد یا می ‌واند شامل یک آرایه به اندازه پویا از نوع خود باشد. این محدودیت لازم است، زیرا اندازه ساختار باید محدود باشد.



توجه داشته باشید که چگونه در همه توابع، یک نوع struct به یک متغیر محلی با  ``storage`` مکان داده 
اختصاص داده می‌شود. این کار struct را کپی نمی‌کند بلکه فقط یک مرجع را ذخیره می‌کند، در حقیقت تا 
انتسابات به اعضای متغیر محلی  در حالت  نوشته شود.



البته می‌توانید بدون اختصاص دادن به متغیر محلی، مستقیماً به اعضای struct دسترسی پیدا کنید، مانند 
در  ``campaigns[campaignID].amount = 0`` .


.. note::


     تا سالیدیتی نسخه 0.7.0 ، memory-structs که شامل اعضای نوع‌های storage-only هستند (به 
    عنوان مثال mappingها)، مجاز بودند و انتساباتی مانند ``campaigns[campaignID] = Campaign(beneficiary, goal, 0, 0)`` )  در مثال بالا کار می‌کند و فقط در 
    silently از آن اعضا صرف نظر می‌کند.
