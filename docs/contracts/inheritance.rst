.. index:: ! inheritance, ! base class, ! contract;base, ! deriving

***********
Inheritance
***********

Solidity supports multiple inheritance including polymorphism.

Polymorphism means that a function call (internal and external)
always executes the function of the same name (and parameter types)
in the most derived contract in the inheritance hierarchy.
This has to be explicitly enabled on each function in the
hierarchy using the ``virtual`` and ``override`` keywords.
See :ref:`Function Overriding <function-overriding>` for more details.

It is possible to call functions further up in the inheritance
hierarchy internally by explicitly specifying the contract
using ``ContractName.functionName()`` or using ``super.functionName()``
if you want to call the function one level higher up in
the flattened inheritance hierarchy (see below).

When a contract inherits from other contracts, only a single
contract is created on the blockchain, and the code from all the base contracts
is compiled into the created contract. This means that all internal calls
to functions of base contracts also just use internal function calls
(``super.f(..)`` will use JUMP and not a message call).

State variable shadowing is considered as an error.  A derived contract can
only declare a state variable ``x``, if there is no visible state variable
with the same name in any of its bases.

The general inheritance system is very similar to
`Python's <https://docs.python.org/3/tutorial/classes.html#inheritance>`_,
especially concerning multiple inheritance, but there are also
some :ref:`differences <multi-inheritance>`.

Details are given in the following example.

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

Note that above, we call ``Destructible.destroy()`` to "forward" the
destruction request. The way this is done is problematic, as
seen in the following example:

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

A call to ``Final.destroy()`` will call ``Base2.destroy`` because we specify it
explicitly in the final override, but this function will bypass
``Base1.destroy``. The way around this is to use ``super``:

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

If ``Base2`` calls a function of ``super``, it does not simply
call this function on one of its base contracts.  Rather, it
calls this function on the next base contract in the final
inheritance graph, so it will call ``Base1.destroy()`` (note that
the final inheritance sequence is -- starting with the most
derived contract: Final, Base2, Base1, Destructible, owned).
The actual function that is called when using super is
not known in the context of the class where it is used,
although its type is known. This is similar for ordinary
virtual method lookup.

.. index:: ! overriding;function

.. _function-overriding:

Function Overriding
===================

Base functions can be overridden by inheriting contracts to change their
behavior if they are marked as ``virtual``. The overriding function must then
use the ``override`` keyword in the function header.
The overriding function may only change the visibility of the overridden function from ``external`` to ``public``.
The mutability may be changed to a more strict one following the order:
``nonpayable`` can be overridden by ``view`` and ``pure``. ``view`` can be overridden by ``pure``.
``payable`` is an exception and cannot be changed to any other mutability.

The following example demonstrates changing mutability and visibility:

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

For multiple inheritance, the most derived base contracts that define the same
function must be specified explicitly after the ``override`` keyword.
In other words, you have to specify all base contracts that define the same function
and have not yet been overridden by another base contract (on some path through the inheritance graph).
Additionally, if a contract inherits the same function from multiple (unrelated)
bases, it has to explicitly override it:

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

An explicit override specifier is not required if
the function is defined in a common base contract
or if there is a unique function in a common base contract
that already overrides all other functions.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    contract A { function f() public pure{} }
    contract B is A {}
    contract C is A {}
    // No explicit override required
    contract D is B, C {}

More formally, it is not required to override a function (directly or
indirectly) inherited from multiple bases if there is a base contract
that is part of all override paths for the signature, and (1) that
base implements the function and no paths from the current contract
to the base mentions a function with that signature or (2) that base
does not implement the function and there is at most one mention of
the function in all paths from the current contract to that base.

In this sense, an override path for a signature is a path through
the inheritance graph that starts at the contract under consideration
and ends at a contract mentioning a function with that signature
that does not override.

If you do not mark a function that overrides as ``virtual``, derived
contracts can no longer change the behaviour of that function.

.. note::

  Functions with the ``private`` visibility cannot be ``virtual``.

.. note::

  Functions without implementation have to be marked ``virtual``
  outside of interfaces. In interfaces, all functions are
  automatically considered ``virtual``.

.. note::

  Starting from Solidity 0.8.8, the ``override`` keyword is not
  required when overriding an interface function, except for the
  case where the function is defined in multiple bases.


Public state variables can override external functions if the
parameter and return types of the function matches the getter function
of the variable:

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

  While public state variables can override external functions, they themselves cannot
  be overridden.

.. index:: ! overriding;modifier

.. _modifier-overriding:

Modifier Overriding
===================

Function modifiers can override each other. This works in the same way as
:ref:`function overriding <function-overriding>` (except that there is no overloading for modifiers). The
``virtual`` keyword must be used on the overridden modifier
and the ``override`` keyword must be used in the overriding modifier:

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


In case of multiple inheritance, all direct base contracts must be specified
explicitly:

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

Constructors
============

A constructor is an optional function declared with the ``constructor`` keyword
which is executed upon contract creation, and where you can run contract
initialisation code.

Before the constructor code is executed, state variables are initialised to
their specified value if you initialise them inline, or their :ref:`default value<default-value>` if you do not.

After the constructor has run, the final code of the contract is deployed
to the blockchain. The deployment of
the code costs additional gas linear to the length of the code.
This code includes all functions that are part of the public interface
and all functions that are reachable from there through function calls.
It does not include the constructor code or internal functions that are
only called from the constructor.

If there is no
constructor, the contract will assume the default constructor, which is
equivalent to ``constructor() {}``. For example:

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

You can use internal parameters in a constructor (for example storage pointers). In this case,
the contract has to be marked :ref:`abstract <abstract-contract>`, because these parameters
cannot be assigned valid values from outside but only through the constructors of derived contracts.

.. warning ::
    Prior to version 0.4.22, constructors were defined as functions with the same name as the contract.
    This syntax was deprecated and is not allowed anymore in version 0.5.0.

.. warning ::

    قبل از نسخه 0.7.0 ، شما باید قابلیت دیدنِ constructorها را به صورت ``internal`` یا ``public`` مشخص می‌کردید.


.. index:: ! base;constructor

آرگومان‌ها برای constructorهای اصلی
===============================

constructorهای کلیه قراردادهای اصلی طبق قوانین خطی سازی که در زیر توضیح داده شده است فراخوانی 
می‌شوند. اگر constructorهای اصلی دارای آرگومان هستند، قراردادهای مشتق شده باید همه آنها را مشخص 
کنند. این می‌تواند با دو راه انجام شود:


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

یک راه به صورت مستقیم در فهرست وراثت وجود دارد (``is Base(7)``). مورد دیگر به این صورت است که 
یک modifier به عنوان بخشی از constructor مشتق شده فراخوانی می‌شود (``Base(_y * _y)``). 
اگر آرگومان‌constructor ثابت باشد و رفتار قرارداد را تعریف کند یا آن را توصیف کند، انجام اولین راه راحت‌تر 
است. اگر آرگومان‌های constructor اصلی به آرگومان‌های قرارداد مشتق شده وابسته باشند، باید از راه دوم 
استفاده کرد. آرگومان‌ها باید در فهرست وراثت یا در سبک  modifier در constructor مشتق شده ارائه 
شوند. تعیین آرگومان‌ها در هر دو مکان خطا است.


اگر یک قرارداد مشتق شده آرگومان‌ها را برای همه constructorهای قرارداد اصلی خود مشخص نکند، انتزاعی خواهد بود.

.. index:: ! inheritance;multiple, ! linearization, ! C3 linearization

.. _multi-inheritance:

وراثت چندگانه و خطی سازی (Multiple Inheritance and Linearization)
======================================

زبان‌هایی که امکان وراثت متعدد را دارند، با مشکلات متعددی روبرو هستند. یکی از این مشکلات،  `مشکل Diamond <https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem>`_  
است. سالیدیتی از این نظر مشابه پایتون است زیرا از " `خطی سازی C3  <https://en.wikipedia.org/wiki/C3_linearization>`_" برای اعمال نظم خاصی در نمودار غیر 
چرخشی جهت دار (DAG) کلاس‌های اصلی استفاده می‌کند. این منجر به ویژگی مطلوب یکنواختی می‌شود اما 
برخی از نمودارهای ارث بری را ممنوع می‌کند. به طور خاص، ترتیب ارائه کلاس‌های اصلی در دستورالعمل ``is`` 
مهم است: شما باید قراردادهای اصلی مستقیم را به ترتیب از "شبیه ترین" تا "مشتق شده ترین" فهرست کنید. 
توجه داشته باشید که این ترتیب معکوس دستور مورد استفاده در پایتون است.



یک روش ساده دیگر برای توضیح این امر این است که وقتی تابعی فراخوانی می‌شود که چندین بار در قراردادهای 
مختلف تعریف شده‌است، پایه‌های داده شده از راست به چپ (چپ به راست در پایتون) به صورت depth-first 
جستجو می‌شوند و در اولین مسابقه متوقف می‌شوند. اگر قرارداد اصلی قبلاً جستجو شده‌است، از آن صرف نظر 
می‌شود.


در کد زیر، سالیدیتی خطای "خطی سازی نمودار وراثت غیرممکن است" را می‌دهد.



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract X {}
    contract A is X {}
    // This will not compile
    contract C is A, X {}

دلیل این امر این است که ``C`` از ``X`` درخواست می‌کند تا ``A`` را نادیده بگیرد (با مشخص کردن ``A , X`` به این ترتیب)، 
اما خود ``A`` درخواست نادیده گرفتن ``X`` را دارد، این تضادی است که قابل حل نیست.



با توجه به این واقعیت که شما باید صریحاً تابعی را که از چندین پایه به ارث رسیده‌است بدون لغو منحصر به فردی لغو کنید، خطی سازی C3 در عمل خیلی مهم نیست.



یکی از زمینه‌هایی که خطی سازی وراثت از اهمیت ویژه ای برخوردار است و شاید چندان واضح نباشد، زمانی 
است که constructorهای متعددی در سلسله مراتب ارث بری وجود داشته باشند. constructorها همیشه 
صرف نظر از ترتیبی که آرگومان‌های آنها در constructor قرارداد ارث بری ارائه شد
اجرا می‌شوند. مثلا:

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


به ارث بردن انواع مختلف اعضای یک نام
======================================================


این خطا زمانی رخ می‌دهد که هر یک از جفت‌های زیر در یک قرارداد به دلیل وراثت، یک نام داشته باشند:

-	یک تابع و یک modifier
-	یک تابع و یک event
-	یک event و modifier

به عنوان یک استثناء، یک  getter متغیر حالت می‌تواند یک تابع خارجی را نادیده بگیرد.
