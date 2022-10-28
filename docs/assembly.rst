.. _inline-assembly:

###############
مونتاژ داخلی (Inline Assembly)
###############

.. index:: ! assembly, ! asm, ! evmasm


<<<<<<< HEAD
می‌توانید دستورات سالیدیتی را با اسمبلی درون خطی به زبانی نزدیک به ماشین مجازی اتریوم جا دهید. این به 
شما کنترل دقیق‌تری می‌دهد، که به ویژه هنگامی که با نوشتن کتابخانه‌ها زبان را تقویت می‌کنید مفید است.


زبانی که برای مونتاژ خطی در سالیدیتی استفاده می‌شود :ref:`Yul <yul>` نام دارد و در بخش مخصوص خود مستند شده 
است. این بخش فقط نحوه ارتباط کد مونتاژ درون خطی با کد سالیدیتی را پوشش می‌دهد.

=======
You can interleave Solidity statements with inline assembly in a language close
to the one of the Ethereum Virtual Machine. This gives you more fine-grained control,
which is especially useful when you are enhancing the language by writing libraries.
>>>>>>> 0816b15e757057782d447c6d41513edfa2bec728



.. warning::

    مونتاژ داخلی راهی برای دسترسی به ماشین مجازی اتریوم در سطح پایین است. مونتاژ داخلی چندین ویژگی 
    ایمنی مهم و بررسی سالیدیتی را دور می‌زند. شما فقط باید از آن برای وظایفی که به آن نیاز دارند استفاده کنید 
    و فقط در صورت اطمینان از استفاده از آن.

یک بلوک مونتاژ درون خطی با ``{ ... } assembly`` مشخص می‌شود، جایی که کد داخل آکولاد ها به زبان :ref:`Yul <yul>` 
کد است.



کد مونتاژ درون خطی می‌تواند به متغیرهای سالیدیتی محلی دسترسی پیدا کند که در زیر توضیح داده شده است.



بلوک‌های مختلف مونتاژ درون خطی هیچ نامی ندارند، به عنوان مثال نمی‌توان یک تابع Yul را فراخوانی کرد یا 
به یک متغیر Yul که در یک بلوک مونتاژ داخلی متفاوت تعریف شده است دسترسی پیدا کرد.


مثال
-------

مثال زیر کد کتابخانه‌ای را برای دسترسی به کد قرارداد دیگر و بارگذاری آن در متغیر  ``bytes`` ارائه می‌دهد. این امر با استفاده 
از  ``<address>.code`` با "Plain Solidity" نیز امکان پذیر است. اما نکته اینجاست که کتابخانه‌های اسمبلی قابل استفاده مجدد 
می‌توانند زبان سالیدیتی را بدون تغییر کامپایلر افزایش دهند.


.. code::

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    library GetCode {
        function at(address addr) public view returns (bytes memory code) {
            assembly {
                // retrieve the size of the code, this needs assembly
                let size := extcodesize(addr)
                // allocate output byte array - this could also be done without assembly
                // by using code = new bytes(size)
                code := mload(0x40)
                // new "memory end" including padding
                mstore(0x40, add(code, and(add(add(size, 0x20), 0x1f), not(0x1f))))
                // store length in memory
                mstore(code, size)
                // actually retrieve the code, this needs assembly
                extcodecopy(addr, add(code, 0x20), 0, size)
            }
        }
    }

مونتاژ داخلی در مواردی که بهینه ساز قادر به تولید کد کارآمد نباشد، مفید است، به عنوان مثال:


.. code::

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;


    library VectorSum {
        // This function is less efficient because the optimizer currently fails to
        // remove the bounds checks in array access.
        function sumSolidity(uint[] memory data) public pure returns (uint sum) {
            for (uint i = 0; i < data.length; ++i)
                sum += data[i];
        }

        // We know that we only access the array in bounds, so we can avoid the check.
        // 0x20 needs to be added to an array because the first slot contains the
        // array length.
        function sumAsm(uint[] memory data) public pure returns (uint sum) {
            for (uint i = 0; i < data.length; ++i) {
                assembly {
                    sum := add(sum, mload(add(add(data, 0x20), mul(i, 0x20))))
                }
            }
        }

        // Same as above, but accomplish the entire code within inline assembly.
        function sumPureAsm(uint[] memory data) public pure returns (uint sum) {
            assembly {
                // Load the length (first 32 bytes)
                let len := mload(data)

                // Skip over the length field.
                //
                // Keep temporary variable so it can be incremented in place.
                //
                // NOTE: incrementing data would result in an unusable
                //       data variable after this assembly block
                let dataElementLocation := add(data, 0x20)

                // Iterate until the bound is not met.
                for
                    { let end := add(dataElementLocation, mul(len, 0x20)) }
                    lt(dataElementLocation, end)
                    { dataElementLocation := add(dataElementLocation, 0x20) }
                {
                    sum := add(sum, mload(dataElementLocation))
                }
            }
        }
    }

.. index:: selector; of a function

دسترسی به متغیرها، توابع و کتابخانه‌های خارجی
-----------------------------------------------------

با استفاده از نام متغیرهای سالیدیتی و سایر identifier ها می‌توانید به آنها دسترسی پیدا کنید.


<<<<<<< HEAD
متغیرهای محلی از نوع ارزش به طور مستقیم در اسمبلی درون خطی قابل استفاده هستند.


متغیرهای محلی که به مِمُوری اشاره می‌کنند به آدرس متغیر موجود در مِمُوری ارزیابی می‌شوند نه خود مقدار. چنین متغیرهایی را 
می‌توان به آن‌ها نیز اختصاص داد، اما توجه داشته باشید که یک انتساب فقط نشانگر را تغییر می‌دهد و نه داده‌ها را و این مسئولیت 
شماست که به مدیریت مِمُوری سالیدیتی توجه کنید. قسمت :ref:`Conventions in Solidity <conventions-in-solidity>` را مشاهده کنید.
=======
Local variables that refer to memory evaluate to the address of the variable in memory, not the value itself.
Such variables can also be assigned to, but note that an assignment will only change the pointer and not the data
and that it is your responsibility to respect Solidity's memory management.
See :ref:`Conventions in Solidity <conventions-in-solidity>`.

Similarly, local variables that refer to statically-sized calldata arrays or calldata structs
evaluate to the address of the variable in calldata, not the value itself.
The variable can also be assigned a new offset, but note that no validation is performed to ensure that
the variable will not point beyond ``calldatasize()``.

For external function pointers the address and the function selector can be
accessed using ``x.address`` and ``x.selector``.
The selector consists of four right-aligned bytes.
Both values can be assigned to. For example:
>>>>>>> 0816b15e757057782d447c6d41513edfa2bec728

به طور مشابه، متغیرهای محلی که به آرایه‌های calldata با اندازه استاتیک یا ساختارهای calldata اشاره می‌کنند، به آدرس 
متغیر در calldata، نه خود مقدار، ارزیابی می‌شوند. همچنین می‌توان یک آفست جدید به متغیر اختصاص داد، اما توجه داشته 
باشید که هیچ اعتبارسنجی برای اطمینان از اینکه متغیر فراتر از ``()calldatasize`` قرار نمی‌گیرد، انجام نمی‌شود.

برای آرایه‌های calldata پویا، می‌توانید با استفاده از ``x.offset`` و ``x.length`` به offset call data (بر حسب بایت) و 
طول (تعداد عناصر) آنها دسترسی داشته باشید. هر دو عبارت را نیز می‌توان به آن اختصاص داد، اما در مورد حالت استاتیک، هیچ 
اعتبارسنجی برای اطمینان از اینکه ناحیه داده حاصل در محدوده ``() calldatasize``  است انجام نمی شود.

برای متغیرهای storage  محلی یا متغیرهای حالت، یک شناسه Yul کافی نیست، زیرا آنها لزوماً یک اسلات storage  
کامل را اشغال نمی‌کنند. بنابراین، " address" آنها از یک اسلات و یک بایت آفست در داخل آن اسلات تشکیل شده است. برای 
بازیابی اسلاتی که متغیر ``x`` به آن اشاره می کند، از ``x.slot`` و برای بازیابی بایت آفست از ``x.offset`` استفاده می‌کنید. استفاده از ``x`` 
خود منجر به خطا می‌شود.

شما همچنین می‌توانید به بخش ``slot.`` یک اشاره گر متغیر storage  محلی اختصاص دهید. برای اینها (structها، آرایه‌ها یا 
mappingها)، قسمت ``offset.`` همیشه صفر است. با این حال، نمی‌توان به قسمت ``slot.`` یا ``offset.`` یک متغیر حالت اختصاص داد.

متغیرهای سالیدیتی محلی برای اختصاص دادن (assignments) در دسترس هستند، به عنوان مثال:


.. code::

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

    اگر به متغیرهایی از نوعی دسترسی داشته باشید که کمتر از 256 بیت را شامل می‌شود (به عنوان 
    مثال  ``uint64`` ،  ``address`` یا  ``bytes16`` )، نمی‌توانید هیچ فرضی در مورد بیت‌هایی که بخشی از کدگذاری آن نوع نیستند، داشته 
    باشید. به خصوص، آنها را صفر فرض نکنید. برای ایمن بودن، همیشه قبل از استفاده از آن در زمینه‌ای که مهم است، داده‌ها را به 
    درستی پاک کنید: از  ``uint32 x = f(); assembly { x := and(x, 0xffffffff) /* now use x */ }`` برای پاک 
    کردن signed typeها استفاده کنید،  می‌توانید از آپکد ``signextend``  استفاده کنید: ``assembly { signextend(<num_bytes_of_x_minus_one>, x) }``


<<<<<<< HEAD
از نسخه  0.6.0  سالیدیتی نام یک متغیر اسمبلی درون خطی ممکن است هیچ اعلان قابل مشاهده در محدوده بلوک اسمبلی درون 
خطی (شامل اعلان‌های متغیر، قرارداد و تابع) را تحت الشعاع قرار ندهد.
=======
Since Solidity 0.6.0, the name of a inline assembly variable may not
shadow any declaration visible in the scope of the inline assembly block
(including variable, contract and function declarations).
>>>>>>> 0816b15e757057782d447c6d41513edfa2bec728


  از  نسخه 0.7.0 سالیدیتی، متغیرها و توابع اعلام شده در داخل بلوک اسمبلی درون خطی ممکن است حاوی  ``.`` نباشند، اما 
  از  ``.`` برای دسترسی به متغیرهای سالیدیتی از خارج از بلوک اسمبلی درون خطی معتبر است.



مواردی که باید از آنها اجتناب کرد
---------------

اسمبلی داخلی ممکن است ظاهری کاملاً سطح بالا داشته باشد، اما در واقع سطح بسیار پایینی دارد. فراخوانی‌های 
تابع، حلقه‌ها، if ها و سوئیچ ها با قوانین بازنویسی ساده تبدیل می‌شوند و پس از آن، تنها کاری که اسمبلی برای 
شما انجام می‌دهد، مرتب سازی مجدد آپکدهای سبک تابعی، شمارش ارتفاع پشته برای دسترسی متغیرها و حذف 
اسلات‌های پشته برای متغیرهای مونتاژ محلی است. وقتی به انتهای بلوک آنها رسید.


.. _conventions-in-solidity:

کنوانسیون‌ها در سالیدیتی (Conventions in Solidity)
-----------------------

<<<<<<< HEAD

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

=======
.. _assembly-typed-variables:

Values of Typed Variables
=========================

In contrast to EVM assembly, Solidity has types which are narrower than 256 bits,
e.g. ``uint24``. For efficiency, most arithmetic operations ignore the fact that
types can be shorter than 256
bits, and the higher-order bits are cleaned when necessary,
i.e., shortly before they are written to memory or before comparisons are performed.
This means that if you access such a variable
from within inline assembly, you might have to manually clean the higher-order bits
first.

.. _assembly-memory-management:

Memory Management
=================

Solidity manages memory in the following way. There is a "free memory pointer"
at position ``0x40`` in memory. If you want to allocate memory, use the memory
starting from where this pointer points at and update it.
There is no guarantee that the memory has not been used before and thus
you cannot assume that its contents are zero bytes.
There is no built-in mechanism to release or free allocated memory.
Here is an assembly snippet you can use for allocating memory that follows the process outlined above:
>>>>>>> 0816b15e757057782d447c6d41513edfa2bec728

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
<<<<<<< HEAD

    آرایه‌های مِمُوری با اندازه استاتیک دارای length field نیستند، اما ممکن است بعداً اضافه شوند تا قابلیت تبدیل 
    بهتری بین آرایه‌های اندازه استاتیک و پویا ایجاد شود، بنابراین به این موضوع اتکا نکنید.

   
=======
    Statically-sized memory arrays do not have a length field, but it might be added later
    to allow better convertibility between statically and dynamically-sized arrays; so,
    do not rely on this.

Memory Safety
=============

Without the use of inline assembly, the compiler can rely on memory to remain in a well-defined
state at all times. This is especially relevant for :ref:`the new code generation pipeline via Yul IR <ir-breaking-changes>`:
this code generation path can move local variables from stack to memory to avoid stack-too-deep errors and
perform additional memory optimizations, if it can rely on certain assumptions about memory use.

While we recommend to always respect Solidity's memory model, inline assembly allows you to use memory
in an incompatible way. Therefore, moving stack variables to memory and additional memory optimizations are,
by default, globally disabled in the presence of any inline assembly block that contains a memory operation
or assigns to Solidity variables in memory.

However, you can specifically annotate an assembly block to indicate that it in fact respects Solidity's memory
model as follows:

.. code-block:: solidity

    assembly ("memory-safe") {
        ...
    }

In particular, a memory-safe assembly block may only access the following memory ranges:

- Memory allocated by yourself using a mechanism like the ``allocate`` function described above.
- Memory allocated by Solidity, e.g. memory within the bounds of a memory array you reference.
- The scratch space between memory offset 0 and 64 mentioned above.
- Temporary memory that is located *after* the value of the free memory pointer at the beginning of the assembly block,
  i.e. memory that is "allocated" at the free memory pointer without updating the free memory pointer.

Furthermore, if the assembly block assigns to Solidity variables in memory, you need to assure that accesses to
the Solidity variables only access these memory ranges.

Since this is mainly about the optimizer, these restrictions still need to be followed, even if the assembly block
reverts or terminates. As an example, the following assembly snippet is not memory safe, because the value of
``returndatasize()`` may exceed the 64 byte scratch space:

.. code-block:: solidity

    assembly {
      returndatacopy(0, 0, returndatasize())
      revert(0, returndatasize())
    }

On the other hand, the following code *is* memory safe, because memory beyond the location pointed to by the
free memory pointer can safely be used as temporary scratch space:

.. code-block:: solidity

    assembly ("memory-safe") {
      let p := mload(0x40)
      returndatacopy(p, 0, returndatasize())
      revert(p, returndatasize())
    }

Note that you do not need to update the free memory pointer if there is no following allocation,
but you can only use memory starting from the current offset given by the free memory pointer.

If the memory operations use a length of zero, it is also fine to just use any offset (not only if it falls into the scratch space):

.. code-block:: solidity

    assembly ("memory-safe") {
      revert(0, 0)
    }

Note that not only memory operations in inline assembly itself can be memory-unsafe, but also assignments to
Solidity variables of reference type in memory. For example the following is not memory-safe:

.. code-block:: solidity

    bytes memory x;
    assembly {
      x := 0x40
    }
    x[0x20] = 0x42;

Inline assembly that neither involves any operations that access memory nor assigns to any Solidity variables
in memory is automatically considered memory-safe and does not need to be annotated.

.. warning::
    It is your responsibility to make sure that the assembly actually satisfies the memory model. If you annotate
    an assembly block as memory-safe, but violate one of the memory assumptions, this **will** lead to incorrect and
    undefined behaviour that cannot easily be discovered by testing.

In case you are developing a library that is meant to be compatible across multiple versions
of Solidity, you can use a special comment to annotate an assembly block as memory-safe:

.. code-block:: solidity

    /// @solidity memory-safe-assembly
    assembly {
        ...
    }

Note that we will disallow the annotation via comment in a future breaking release; so, if you are not concerned with
backwards-compatibility with older compiler versions, prefer using the dialect string.
>>>>>>> 0816b15e757057782d447c6d41513edfa2bec728
