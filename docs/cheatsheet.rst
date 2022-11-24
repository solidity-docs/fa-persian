**********
برگه تقلب (Cheatsheet)
**********

.. index:: operator; precedence

ترتیب اولویت اپراتورها (Order of Precedence of Operators)
================================
<<<<<<< HEAD

در زیر ترتیب اولویت عملگرها آمده است که به ترتیب ارزیابی لیست شده‌اند.

+------------+-------------------------------------+--------------------------------------------+
| Precedence | Description                         | Operator                                   |
+============+=====================================+============================================+
| *1*        | Postfix increment and decrement     | ``++``, ``--``                             |
+            +-------------------------------------+--------------------------------------------+
|            | New expression                      | ``new <typename>``                         |
+            +-------------------------------------+--------------------------------------------+
|            | Array subscripting                  | ``<array>[<index>]``                       |
+            +-------------------------------------+--------------------------------------------+
|            | Member access                       | ``<object>.<member>``                      |
+            +-------------------------------------+--------------------------------------------+
|            | Function-like call                  | ``<func>(<args...>)``                      |
+            +-------------------------------------+--------------------------------------------+
|            | Parentheses                         | ``(<statement>)``                          |
+------------+-------------------------------------+--------------------------------------------+
| *2*        | Prefix increment and decrement      | ``++``, ``--``                             |
+            +-------------------------------------+--------------------------------------------+
|            | Unary minus                         | ``-``                                      |
+            +-------------------------------------+--------------------------------------------+
|            | Unary operations                    | ``delete``                                 |
+            +-------------------------------------+--------------------------------------------+
|            | Logical NOT                         | ``!``                                      |
+            +-------------------------------------+--------------------------------------------+
|            | Bitwise NOT                         | ``~``                                      |
+------------+-------------------------------------+--------------------------------------------+
| *3*        | Exponentiation                      | ``**``                                     |
+------------+-------------------------------------+--------------------------------------------+
| *4*        | Multiplication, division and modulo | ``*``, ``/``, ``%``                        |
+------------+-------------------------------------+--------------------------------------------+
| *5*        | Addition and subtraction            | ``+``, ``-``                               |
+------------+-------------------------------------+--------------------------------------------+
| *6*        | Bitwise shift operators             | ``<<``, ``>>``                             |
+------------+-------------------------------------+--------------------------------------------+
| *7*        | Bitwise AND                         | ``&``                                      |
+------------+-------------------------------------+--------------------------------------------+
| *8*        | Bitwise XOR                         | ``^``                                      |
+------------+-------------------------------------+--------------------------------------------+
| *9*        | Bitwise OR                          | ``|``                                      |
+------------+-------------------------------------+--------------------------------------------+
| *10*       | Inequality operators                | ``<``, ``>``, ``<=``, ``>=``               |
+------------+-------------------------------------+--------------------------------------------+
| *11*       | Equality operators                  | ``==``, ``!=``                             |
+------------+-------------------------------------+--------------------------------------------+
| *12*       | Logical AND                         | ``&&``                                     |
+------------+-------------------------------------+--------------------------------------------+
| *13*       | Logical OR                          | ``||``                                     |
+------------+-------------------------------------+--------------------------------------------+
| *14*       | Ternary operator                    | ``<conditional> ? <if-true> : <if-false>`` |
+            +-------------------------------------+--------------------------------------------+
|            | Assignment operators                | ``=``, ``|=``, ``^=``, ``&=``, ``<<=``,    |
|            |                                     | ``>>=``, ``+=``, ``-=``, ``*=``, ``/=``,   |
|            |                                     | ``%=``                                     |
+------------+-------------------------------------+--------------------------------------------+
| *15*       | Comma operator                      | ``,``                                      |
+------------+-------------------------------------+--------------------------------------------+
=======
.. include:: types/operator-precedence-table.rst
>>>>>>> eb2f874eac0aa871236bf5ff04b7937c49809c33

.. index:: assert, block, coinbase, difficulty, number, block;number, timestamp, block;timestamp, msg, data, gas, sender, value, gas price, origin, revert, require, keccak256, ripemd160, sha256, ecrecover, addmod, mulmod, cryptography, this, super, selfdestruct, balance, codehash, send

متغیرهای جهانی
================

<<<<<<< HEAD
-	``abi.decode(bytes memory encodedData, (...)) returns (...)`` : آ :ref:`ABI <ABI>` داده‌های ارائه شده را رمزگشایی می‌کند. انواع آن در پرانتز به عنوان آرگومان دوم آورده شده است. مثال: ``(uint a, uint[2] memory b, bytes memory c) = abi.decode(data, (uint, uint[2], bytes))`` 
-	``abi.encode(...) returns (bytes memory)``: آ :ref:`ABI <ABI>` آرگومان‌های داده شده را کد می‌کند
-	``abi.encodePacked(...) returns (bytes memory)``:آ :ref:`packed encoding <abi_packed_mode>` از آرگومان‌های داده شده را انجام می‌دهد. توجه داشته باشید که این رمزگذاری می‌تواند مبهم باشد!
-	``abi.encodeWithSelector(bytes4 selector, ...) returns (bytes memory)``: آ :ref:`ABI <ABI>` آرگومان‌های داده شده را از دوم شروع می‌کند و selector چهار بایت داده شده را اضافه می‌کند.
-	``abi.encodeWithSignature(string memory signature, ...) returns (bytes memory)``: معادل ```abi.encodeWithSelector(bytes4(keccak256(bytes(signature)), ...)`` می‌باشد.
-	``bytes.concat(...) returns (bytes memory)``: :ref:`تعداد متغیر آرگومان‌ها را به یک آرایه بایت متصل می‌کند.<bytes-concat>`
- ``block.basefee`` (``uint``): هزینه پایه (base fee ) بلاک فعلی (`EIP-3198 <https://eips.ethereum.org/EIPS/eip-3198>`_ و `EIP-1559 <https://eips.ethereum.org/EIPS/eip-1559>`_)
-	``block.chainid`` (``uint``) : شناسه (id) زنجیره فعلی
-	``block.coinbase`` (``address payable``): آدرس فعلی استخراج کننده بلاک
-	``block.difficulty`` (``uint``): سختی بلاک فعلی
-	``block.gaslimit`` (``uint``): محدودیت گاز بلاک فعلی
-	``block.number`` (``uint``): شماره بلاک فعلی
-	``block.timestamp`` (``uint``): برچسب زمانی بلوک فعلی
-	``gasleft() returns (uint256)``: گاز باقی مانده
-	``msg.data`` (``bytes``): آ calldata کامل
- ``msg.sender`` (``address``): فرستنده پیام (فراخوانی فعلی)
-	``msg.value`` (``uint``): تعداد wei ارسال شده با پیام
-	``tx.gasprice`` (``uint``): قیمت گاز تراکنش
-	``tx.origin`` (``address``): فرستنده تراکنش÷ (زنجیره فراخوانی کامل)
-	``assert(bool condition)``: اجرا را لغو کرده و در صورتی که شرط ``false`` باشد، تغییرات حالت را برگردانید (برای خطای داخلی استفاده کنید)
-	``require(bool condition)``: لغو اجرا و برگرداندن تغییرات حالت در صورت ``false`` بودن شرایط (استفاده برای ورودی نادرست یا خطا در جزء خارجی)
-	``require(bool condition, string memory message)``: اجرا را لغو کرده و در صورت ``false`` بودن تغییرات، وضعیت را برگردانید (برای ورودی نادرست یا خطا در کامپوننت خارجی استفاده کنید). همچنین پیغام خطا را ارائه دهید.
-	``()revert``: اجرا را لغو کرده و تغییرات حالت را برگردانید
-	``revert(string memory message)``: اجرا را لغو کرده و با ارائه یک رشته توضیحی، تغییرات حالت را برگردانید
-	``blockhash(uint blockNumber) returns (bytes32)``: هش بلاک داده شده - فقط برای 256 بلاک اخیر کار می‌کند
-	``keccak256(bytes memory) returns (bytes32)``: محاسبه هش Keccak-256 ورودی
-	``sha256(bytes memory) returns (bytes32)``: محاسبه هش SHA-256 ورودی
-	``ripemd160(bytes memory) returns (bytes20)``: محاسبه هش RIPEMD-160 ورودی
-	``ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)``: بازیابی آدرس مرتبط با کلید عمومی از امضای منحنی بیضوی، بازگشت صفر در خطا
-	``addmod(uint x, uint y, uint k) returns (uint)``: محاسبه ``(x + y) % k``   که در آن جمع با دقت دلخواه انجام می‌شود و در ``256**2`` جمع نمی‌شود. تأیید کنید که ``k != 0`` از نسخه 0.5.0 شروع می‌شود.
-	``mulmod(uint x, uint y, uint k) returns (uint)``: محاسبه ``(x * y) % k`` که در آن ضرب با دقت دلخواه انجام می‌شود و در ``256**2`` جمع نمی‌شود. تأیید کنید که ``k != 0`` از نسخه 0.5.0 شروع می‌شود.
-	``this (current contract’s type)``: قرارداد فعلی، به صراحت قابل تبدیل به ``address`` یا ``address payable`` است
-	``super``: قرارداد یک سطح بالاتر در سلسله مراتب وراثت است
-	``selfdestruct(address payable recipient)``: قرارداد فعلی را تخریب کنید، وجوه آن را به آدرس داده شده ارسال کنید
-	``<address>.balance`` (``uint256``): موجودی :ref:`address` در Wei
-	``<address>.code`` (``bytes memory``): کد در :ref:`address` (می‌تواند خالی باشد)
-	``<address>.codehash`` (``bytes32``): کد هش :ref:`address`
-	``<address payable>.send(uint256 amount) returns (bool)``: ارسال مقدار داده شده از Wei به :ref:`address`، در صورت عدم موفقیت false باز می‌گردد
-	``<address payable>.transfer(uint256 amount)``: ارسال مقدار مشخص شده از Wei به :ref:`address`، عدم موفقیت
-	``type(C).name`` (``string``): نام قرارداد
-	``type(C).creationCode`` (``bytes memory``): ایجاد بایت کد قرارداد مشخص شده، :ref:`اطلاعات نوع<meta-type>` را ببینید.
-	``type(C).runtimeCode`` (``bytes memory``): بایت کد زمان اجرا قرارداد، :ref:`اطلاعات نوع<meta-type>` را ببینید.
-	``type(I).interfaceId`` (``bytes4``) : مقدار حاوی رابط identifier  EIP-165 رابط داده شده، :ref:`اطلاعات نوع<meta-type>` را ببینید.
-	``type(T).min`` (``T``) : حداقل مقدار قابل نمایش توسط نوع صحیح T ، :ref:`اطلاعات نوع<meta-type>` را ببینید.
-	``type(T).max`` (``T``) : حداکثر مقدار قابل نمایش توسط نوع صحیح T ، :ref:`اطلاعات نوع<meta-type>` را ببینید.


.. note::
    When contracts are evaluated off-chain rather than in context of a transaction included in a
    block, you should not assume that ``block.*`` and ``tx.*`` refer to values from any specific
    block or transaction. These values are provided by the EVM implementation that executes the
    contract and can be arbitrary.

.. note::

    به ``block.timestamp`` یا ``blockhash`` به عنوان منبع تصادفی اعتماد نکنید، مگر اینکه بدانید در حال انجام چه کاری هستید.

 
    برچسب زمان و بلاک هش تا حدی می‌توانند تحت تأثیر ماینرها قرار بگیرند. بازیگران بد در جامعه ماینینگ 
    می‌توانند به عنوان مثال یک تابع بازپرداخت کازینو را روی یک هش انتخاب شده اجرا کنند و در صورت عدم 
    دریافت هیچ پولی، یک هش دیگر را دوباره امتحان کنند.

    

    برچسب زمانی بلوک کنونی باید بسیار بزرگتر از زمانبندی آخرین بلوک باشد، اما تنها تضمین این است که در 
    جایی بین برچسب زمانی دو بلوک متوالی در زنجیره متعارف قرار گیرد.



.. note::

    هش بلاک به دلایل مقیاس پذیری برای همه بلاک‌ها در دسترس نیست. فقط می‌توانید به هش های 256 
    بلوک جدید دسترسی پیدا کنید، همه مقادیر دیگر صفر خواهند بود.

 

.. note::

    در نسخه 0.5.0 ، نام مستعار زیر حذف شد: ``suicide`` به عنوان نام مستعار برای ``selfdestruct`` ، ``msg.gas`` به 
    عنوان نام مستعار برای ``gasleft`` ، ``block.blockhash`` به عنوان نام مستعار برای ``blockhash`` و ``sha3`` به عنوان نام 
    مستعار برای ``keccak256``.


.. note::

    در نسخه 0.7.0 ، نام مستعار ``now`` (برای ``block.timestamp``) حذف شده‌است.
=======
- ``abi.decode(bytes memory encodedData, (...)) returns (...)``: :ref:`ABI <ABI>`-decodes
  the provided data. The types are given in parentheses as second argument.
  Example: ``(uint a, uint[2] memory b, bytes memory c) = abi.decode(data, (uint, uint[2], bytes))``
- ``abi.encode(...) returns (bytes memory)``: :ref:`ABI <ABI>`-encodes the given arguments
- ``abi.encodePacked(...) returns (bytes memory)``: Performs :ref:`packed encoding <abi_packed_mode>` of
  the given arguments. Note that this encoding can be ambiguous!
- ``abi.encodeWithSelector(bytes4 selector, ...) returns (bytes memory)``: :ref:`ABI <ABI>`-encodes
  the given arguments starting from the second and prepends the given four-byte selector
- ``abi.encodeCall(function functionPointer, (...)) returns (bytes memory)``: ABI-encodes a call to ``functionPointer`` with the arguments found in the
  tuple. Performs a full type-check, ensuring the types match the function signature. Result equals ``abi.encodeWithSelector(functionPointer.selector, (...))``
- ``abi.encodeWithSignature(string memory signature, ...) returns (bytes memory)``: Equivalent
  to ``abi.encodeWithSelector(bytes4(keccak256(bytes(signature)), ...)``
- ``bytes.concat(...) returns (bytes memory)``: :ref:`Concatenates variable number of
  arguments to one byte array<bytes-concat>`
- ``string.concat(...) returns (string memory)``: :ref:`Concatenates variable number of
  arguments to one string array<string-concat>`
- ``block.basefee`` (``uint``): current block's base fee (`EIP-3198 <https://eips.ethereum.org/EIPS/eip-3198>`_ and `EIP-1559 <https://eips.ethereum.org/EIPS/eip-1559>`_)
- ``block.chainid`` (``uint``): current chain id
- ``block.coinbase`` (``address payable``): current block miner's address
- ``block.difficulty`` (``uint``): current block difficulty
- ``block.gaslimit`` (``uint``): current block gaslimit
- ``block.number`` (``uint``): current block number
- ``block.timestamp`` (``uint``): current block timestamp in seconds since Unix epoch
- ``gasleft() returns (uint256)``: remaining gas
- ``msg.data`` (``bytes``): complete calldata
- ``msg.sender`` (``address``): sender of the message (current call)
- ``msg.sig`` (``bytes4``): first four bytes of the calldata (i.e. function identifier)
- ``msg.value`` (``uint``): number of wei sent with the message
- ``tx.gasprice`` (``uint``): gas price of the transaction
- ``tx.origin`` (``address``): sender of the transaction (full call chain)
- ``assert(bool condition)``: abort execution and revert state changes if condition is ``false`` (use for internal error)
- ``require(bool condition)``: abort execution and revert state changes if condition is ``false`` (use
  for malformed input or error in external component)
- ``require(bool condition, string memory message)``: abort execution and revert state changes if
  condition is ``false`` (use for malformed input or error in external component). Also provide error message.
- ``revert()``: abort execution and revert state changes
- ``revert(string memory message)``: abort execution and revert state changes providing an explanatory string
- ``blockhash(uint blockNumber) returns (bytes32)``: hash of the given block - only works for 256 most recent blocks
- ``keccak256(bytes memory) returns (bytes32)``: compute the Keccak-256 hash of the input
- ``sha256(bytes memory) returns (bytes32)``: compute the SHA-256 hash of the input
- ``ripemd160(bytes memory) returns (bytes20)``: compute the RIPEMD-160 hash of the input
- ``ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address)``: recover address associated with
  the public key from elliptic curve signature, return zero on error
- ``addmod(uint x, uint y, uint k) returns (uint)``: compute ``(x + y) % k`` where the addition is performed with
  arbitrary precision and does not wrap around at ``2**256``. Assert that ``k != 0`` starting from version 0.5.0.
- ``mulmod(uint x, uint y, uint k) returns (uint)``: compute ``(x * y) % k`` where the multiplication is performed
  with arbitrary precision and does not wrap around at ``2**256``. Assert that ``k != 0`` starting from version 0.5.0.
- ``this`` (current contract's type): the current contract, explicitly convertible to ``address`` or ``address payable``
- ``super``: the contract one level higher in the inheritance hierarchy
- ``selfdestruct(address payable recipient)``: destroy the current contract, sending its funds to the given address
- ``<address>.balance`` (``uint256``): balance of the :ref:`address` in Wei
- ``<address>.code`` (``bytes memory``): code at the :ref:`address` (can be empty)
- ``<address>.codehash`` (``bytes32``): the codehash of the :ref:`address`
- ``<address payable>.send(uint256 amount) returns (bool)``: send given amount of Wei to :ref:`address`,
  returns ``false`` on failure
- ``<address payable>.transfer(uint256 amount)``: send given amount of Wei to :ref:`address`, throws on failure
- ``type(C).name`` (``string``): the name of the contract
- ``type(C).creationCode`` (``bytes memory``): creation bytecode of the given contract, see :ref:`Type Information<meta-type>`.
- ``type(C).runtimeCode`` (``bytes memory``): runtime bytecode of the given contract, see :ref:`Type Information<meta-type>`.
- ``type(I).interfaceId`` (``bytes4``): value containing the EIP-165 interface identifier of the given interface, see :ref:`Type Information<meta-type>`.
- ``type(T).min`` (``T``): the minimum value representable by the integer type ``T``, see :ref:`Type Information<meta-type>`.
- ``type(T).max`` (``T``): the maximum value representable by the integer type ``T``, see :ref:`Type Information<meta-type>`.

>>>>>>> eb2f874eac0aa871236bf5ff04b7937c49809c33

.. index:: visibility, public, private, external, internal

مشخص کننده‌های قابلیت دیدن تابع (Function Visibility Specifiers)
==============================

.. code-block:: solidity
    :force:

    function myFunction() <visibility specifier> returns (bool) {
        return true;
    }

- ``public``: قابل مشاهده در خارج و داخل (ایجاد یک تابع گیرنده برای ذخیره سازی/متغیرهای حالت)
- ``private``: فقط در قرارداد فعلی قابل مشاهده است
- ``external``: فقط از خارج قابل مشاهده است (فقط برای عملکردها) - یعنی فقط می توان پیام (از طریق ``this.func``) فراخوانی کرد
- ``internal``: فقط در داخل قابل مشاهده است




.. index:: modifiers, pure, view, payable, constant, anonymous, indexed

اصلاح کننده ها
=========

- ``pure`` برای توابع: اجازه تغییر یا دسترسی به حالت را نمی دهد.
- ``view`` برای توابع: تغییر حالت را ممنوع می کند.
- ``payable`` برای توابع: به آنها اجازه می دهد تا اتر را به همراه یک call دریافت کنند.
- ``constant`` برای متغیرهای حالت: تخصیص را غیر مجاز می کند (بجز مقداردهی اولیه) ، اسلات storage  را اشغال نمی کند.
- ``immutable`` برای متغیرهای حالت : دقیقاً یک انتساب را در زمان ساخت اجازه می دهد و پس از آن ثابت است. در کد ذخیره می شود
- ``anonymous`` برای رویدادها: امضای رویداد را به عنوان topic ذخیره نمی کند.
- ``indexed`` برای پارامترهای رویداد: پارامتر را به عنوان topic ذخیره می کند.
- ``virtual`` برای توابع و اصلاح کننده ها: اجازه می دهد رفتار تابع یا اصلاح کننده در قراردادهای مشتق شده تغییر کند.
- ``override``: بیان می کند که این تابع ، اصلاح کننده یا متغیر حالت عمومی رفتار یک تابع یا اصلاح کننده را در یک قرارداد پایه تغییر می دهد.

<<<<<<< HEAD


کلمات کلیدی رزرو شده
=================

این کلمات کلیدی در سالیدیتی محفوظ است. آنها ممکن است در آینده بخشی از نحو شوند:


``after``, ``alias``, ``apply``, ``auto``, ``byte``, ``case``, ``copyof``, ``default``,
``define``, ``final``, ``implements``, ``in``, ``inline``, ``let``, ``macro``, ``match``,
``mutable``, ``null``, ``of``, ``partial``, ``promise``, ``reference``, ``relocatable``,
``sealed``, ``sizeof``, ``static``, ``supports``, ``switch``, ``typedef``, ``typeof``,
``var``.
=======
>>>>>>> eb2f874eac0aa871236bf5ff04b7937c49809c33
