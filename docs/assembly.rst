.. _inline-assembly:

###############
مونتاژ داخلی (Inline Assembly)
###############

.. index:: ! assembly, ! asm, ! evmasm


می‌توانید دستورات سالیدیتی را با اسمبلی درون خطی به زبانی نزدیک به ماشین مجازی اتریوم جا دهید. این به 
شما کنترل دقیق‌تری می‌دهد، که به ویژه هنگامی که با نوشتن کتابخانه‌ها زبان را تقویت می‌کنید مفید است.

You can interleave Solidity statements with inline assembly in a language close
to the one of the Ethereum virtual machine. This gives you more fine-grained control,
which is especially useful when you are enhancing the language by writing libraries.

زبانی که برای مونتاژ خطی در سالیدیتی استفاده می‌شود Yul نام دارد و در بخش مخصوص خود مستند شده 
است. این بخش فقط نحوه ارتباط کد مونتاژ درون خطی با کد سالیدیتی را پوشش می‌دهد.

The language used for inline assembly in Solidity is called :ref:`Yul <yul>`
and it is documented in its own section. This section will only cover
how the inline assembly code can interface with the surrounding Solidity code.


.. warning::

    مونتاژ داخلی راهی برای دسترسی به ماشین مجازی اتریوم در سطح پایین است. مونتاژ داخلی چندین ویژگی 
    ایمنی مهم و بررسی سالیدیتی را دور می‌زند. شما فقط باید از آن برای وظایفی که به آن نیاز دارند استفاده کنید 
    و فقط در صورت اطمینان از استفاده از آن.

    Inline assembly is a way to access the Ethereum Virtual Machine
    at a low level. This bypasses several important safety
    features and checks of Solidity. You should only use it for
    tasks that need it, and only if you are confident with using it.

یک بلوک مونتاژ درون خطی با assembly { ... } مشخص می‌شود، جایی که کد داخل آکولاد ها به زبان Yul 
کد است.

An inline assembly block is marked by ``assembly { ... }``, where the code inside
the curly braces is code in the :ref:`Yul <yul>` language.

کد مونتاژ درون خطی می‌تواند به متغیرهای سالیدیتی محلی دسترسی پیدا کند که در زیر توضیح داده شده است.

The inline assembly code can access local Solidity variables as explained below.

بلوک‌های مختلف مونتاژ درون خطی هیچ نامی ندارند، به عنوان مثال نمی‌توان یک تابع Yul را فراخوانی کرد یا 
به یک متغیر Yul که در یک بلوک مونتاژ داخلی متفاوت تعریف شده است دسترسی پیدا کرد.

Different inline assembly blocks share no namespace, i.e. it is not possible
to call a Yul function or access a Yul variable defined in a different inline assembly block.

مثال
-------

مثال زیر کد کتابخانه‌ای را برای دسترسی به کد قرارداد دیگر و بارگذاری آن در متغیر bytes ارائه می‌دهد. این 
امر با " plain Solidity" امکان پذیر نیست و ایده این است که کتابخانه‌های مونتاژ چندبار مصرف می‌توانند زبان 
سالیدیتی را بدون تغییر کامپایلر تقویت کنند.

The following example provides library code to access the code of another contract and
load it into a ``bytes`` variable. This is possible with "plain Solidity" too, by using
``<address>.code``. But the point here is that reusable assembly libraries can enhance the
Solidity language without a compiler change.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    library GetCode {
        function at(address _addr) public view returns (bytes memory o_code) {
            assembly {
                // retrieve the size of the code, this needs assembly
                let size := extcodesize(_addr)
                // allocate output byte array - this could also be done without assembly
                // by using o_code = new bytes(size)
                o_code := mload(0x40)
                // new "memory end" including padding
                mstore(0x40, add(o_code, and(add(add(size, 0x20), 0x1f), not(0x1f))))
                // store length in memory
                mstore(o_code, size)
                // actually retrieve the code, this needs assembly
                extcodecopy(_addr, add(o_code, 0x20), 0, size)
            }
        }
    }

مونتاژ داخلی در مواردی که بهینه ساز قادر به تولید کد کارآمد نباشد، مفید است، به عنوان مثال:

Inline assembly is also beneficial in cases where the optimizer fails to produce
efficient code, for example:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;


    library VectorSum {
        // This function is less efficient because the optimizer currently fails to
        // remove the bounds checks in array access.
        function sumSolidity(uint[] memory _data) public pure returns (uint sum) {
            for (uint i = 0; i < _data.length; ++i)
                sum += _data[i];
        }

        // We know that we only access the array in bounds, so we can avoid the check.
        // 0x20 needs to be added to an array because the first slot contains the
        // array length.
        function sumAsm(uint[] memory _data) public pure returns (uint sum) {
            for (uint i = 0; i < _data.length; ++i) {
                assembly {
                    sum := add(sum, mload(add(add(_data, 0x20), mul(i, 0x20))))
                }
            }
        }

        // Same as above, but accomplish the entire code within inline assembly.
        function sumPureAsm(uint[] memory _data) public pure returns (uint sum) {
            assembly {
                // Load the length (first 32 bytes)
                let len := mload(_data)

                // Skip over the length field.
                //
                // Keep temporary variable so it can be incremented in place.
                //
                // NOTE: incrementing _data would result in an unusable
                //       _data variable after this assembly block
                let data := add(_data, 0x20)

                // Iterate until the bound is not met.
                for
                    { let end := add(data, mul(len, 0x20)) }
                    lt(data, end)
                    { data := add(data, 0x20) }
                {
                    sum := add(sum, mload(data))
                }
            }
        }
    }



دسترسی به متغیرها، توابع و کتابخانه‌های خارجی
-----------------------------------------------------

با استفاده از نام متغیرهای سالیدیتی و سایر identifier ها می‌توانید به آنها دسترسی پیدا کنید.


متغیرهای محلی از نوع ارزش به طور مستقیم در اسمبلی درون خطی قابل استفاده هستند.


Local variables that refer to memory evaluate to the address of the variable in memory not the value itself.
Such variables can also be assigned to, but note that an assignment will only change the pointer and not the data
and that it is your responsibility to respect Solidity's memory management.
See :ref:`Conventions in Solidity <conventions-in-solidity>`.

Similarly, local variables that refer to statically-sized calldata arrays or calldata structs
evaluate to the address of the variable in calldata, not the value itself.
The variable can also be assigned a new offset, but note that no validation to ensure that
the variable will not point beyond ``calldatasize()`` is performed.

For external function pointers the address and the function selector can be
accessed using ``x.address`` and ``x.selector``.
The selector consists of four right-aligned bytes.
Both values are can be assigned to. For example:

.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.8.10 <0.9.0;

    contract C {
        // Assigns a new selector and address to the return variable @fun
        function combineToFunctionPointer(address newAddress, uint newSelector) public pure returns (function() external fun) {
            assembly {
                fun.selector := newSelector
                fun.address  := newAddress
            }
        }
    }


For dynamic calldata arrays, you can access
their calldata offset (in bytes) and length (number of elements) using ``x.offset`` and ``x.length``.
Both expressions can also be assigned to, but as for the static case, no validation will be performed
to ensure that the resulting data area is within the bounds of ``calldatasize()``.

For local storage variables or state variables, a single Yul identifier
is not sufficient, since they do not necessarily occupy a single full storage slot.
Therefore, their "address" is composed of a slot and a byte-offset
inside that slot. To retrieve the slot pointed to by the variable ``x``, you
use ``x.slot``, and to retrieve the byte-offset you use ``x.offset``.
Using ``x`` itself will result in an error.

You can also assign to the ``.slot`` part of a local storage variable pointer.
For these (structs, arrays or mappings), the ``.offset`` part is always zero.
It is not possible to assign to the ``.slot`` or ``.offset`` part of a state variable,
though.

Local Solidity variables are available for assignments, for example:

.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;

    contract C {
        uint b;
        function f(uint x) public view returns (uint r) {
            assembly {
                // We ignore the storage slot offset, we know it is zero
                // in this special case.
                r := mul(x, sload(b.slot))
            }
        }
    }

.. warning::
    If you access variables of a type that spans less than 256 bits
    (for example ``uint64``, ``address``, or ``bytes16``),
    you cannot make any assumptions about bits not part of the
    encoding of the type. Especially, do not assume them to be zero.
    To be safe, always clear the data properly before you use it
    in a context where this is important:
    ``uint32 x = f(); assembly { x := and(x, 0xffffffff) /* now use x */ }``
    To clean signed types, you can use the ``signextend`` opcode:
    ``assembly { signextend(<num_bytes_of_x_minus_one>, x) }``


Since Solidity 0.6.0 the name of a inline assembly variable may not
shadow any declaration visible in the scope of the inline assembly block
(including variable, contract and function declarations).


Since Solidity 0.7.0, variables and functions declared inside the
inline assembly block may not contain ``.``, but using ``.`` is
valid to access Solidity variables from outside the inline assembly block.

مواردی که باید از آنها اجتناب کرد
---------------

اسمبلی داخلی ممکن است ظاهری کاملاً سطح بالا داشته باشد، اما در واقع سطح بسیار پایینی دارد. فراخوانی‌های 
تابع، حلقه‌ها، if ها و سوئیچ ها با قوانین بازنویسی ساده تبدیل می‌شوند و پس از آن، تنها کاری که اسمبلی برای 
شما انجام می‌دهد، مرتب سازی مجدد آپکدهای سبک تابعی، شمارش ارتفاع پشته برای دسترسی متغیرها و حذف 
اسلات‌های پشته برای متغیرهای مونتاژ محلی است. وقتی به انتهای بلوک آنها رسید.

Inline assembly might have a quite high-level look, but it actually is extremely
low-level. Function calls, loops, ifs and switches are converted by simple
rewriting rules and after that, the only thing the assembler does for you is re-arranging
functional-style opcodes, counting stack height for
variable access and removing stack slots for assembly-local variables when the end
of their block is reached.

.. _conventions-in-solidity:

کنوانسیون‌ها در سالیدیتی (Conventions in Solidity)
-----------------------


بر خلاف اسمبلیEVM ، سالیدیتی دارای انواع کوتاه‌تر از 256 بیت است، به عنوان مثال ``uint24`` .برای کارآیی، اکثر عملیات حسابی این واقعیت را نادیده می‌گیرند که نوع آنها می‌تواند کوتاه‌تر از 256 بیت باشد 
 و بیت‌های مرتبه بالاتر در صورت لزوم پاک می‌شوند، یعنی کمی قبل از اینکه در مِمُوری نوشته شوند یا قبل از 
 مقایسه کردن. این بدان معناست که اگر از داخل اسمبلی درون خطی به چنین متغیری دسترسی پیدا کنید، 
 ممکن است لازم باشد ابتدا بیت‌های مرتبه بالاتر را به صورت دستی پاک کنید.



سالیدیتی مِمُوری را به روش زیر مدیریت می‌کند. یک "اشاره‌گر مِمُوری آزاد" در موقعیت ``0x40`` در مِمُوری وجود 
دارد. اگر می‌خواهید مِمُوری را تخصیص دهید، از جایی که این اشاره‌گر به آن اشاره میکند، استفاده کنید و آن را 
به روز کنید. هیچ تضمینی وجود ندارد که حافظه قبلاً استفاده نشده‌است و بنابراین نمی‌توانید فرض کنید که 
محتویات آن صفر بایت است. هیچ مکانیزم داخلی برای آزادسازی یا پاکسازی مِمُوریِ اختصاص داده شده وجود 
ندارد. در اینجا یک قطعه اسمبلی وجود دارد که می‌توانید برای تخصیص مِمُوری از فرایند ذکر شده در بالا استفاده 
کنید:


.. code-block:: yul

    function allocate(length) -> pos {
      pos := mload(0x40)
      mstore(0x40, add(pos, length))
    }

64 بایت اول مِمُوری می‌تواند به عنوان "فضای scratch" برای تخصیص کوتاه مدت استفاده شود. 32 بایت پس 
از اشاره‌گر مِمُوری آزاد (یعنی از ``0x60`` شروع می‌شود) به طور دائم صفر است و به عنوان مقدار اولیه برای آرایه‌های 
مِمُوری پویای خالی استفاده می‌شود. این بدان معناست که حافظه قابل تخصیص از ``0x80`` شروع می‌شود که مقدار 
اولیه نشانگر حافظه آزاد است.

عناصر موجود در آرایه‌های مِمُوری در سالیدیتی همیشه مضرب 32 بایت را اشغال می‌کنند (این حتی برای ``[]byte`` 
صادق است، اما برای ``bytes`` و ``string`` صادق نیست). آرایه‌های مِمُوری چند بعدی اشاره‌گرهایی به آرایه‌های مِمُوری 
هستند. طول یک آرایه پویا در اولین اسلات آرایه ذخیره می‌شود و سپس عناصر آرایه دنبال می‌شود.


.. warning::

    آرایه‌های مِمُوری با اندازه استاتیک دارای length field نیستند، اما ممکن است بعداً اضافه شوند تا قابلیت تبدیل 
    بهتری بین آرایه‌های اندازه استاتیک و پویا ایجاد شود، بنابراین به این موضوع اتکا نکنید.

   