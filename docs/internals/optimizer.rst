.. index:: optimizer, optimiser, common subexpression elimination, constant propagation
.. _optimizer:

*************
بهینه ساز (The Optimizer)
*************

کامپایلر سالیدیتی از دو ماژول بهینه ساز مختلف استفاده می‌کند: بهینه ساز « old» که در سطح آپکد عمل 
می‌کند و بهینه ساز « new» که بر روی کد Yul IR کار می‌کند.

بهینه ساز مبتنی بر آپکد، مجموعه ای از `قوانین ساده سازی <https://github.com/ethereum/solidity/blob/develop/libevmasm/RuleList.h>`_ را برای کدهای عملیاتی اعمال می‌کند. همچنین 
مجموعه کدهای مساوی را ترکیب کرده و کدهای استفاده نشده را حذف می‌کند.

بهینه ساز مبتنی بر Yul بسیار قدرتمندتر است، زیرا می‌تواند در فراخوانی توابع کار کند. به عنوان مثال، 
جامپ‌های دلخواه در Yul امکان پذیر نیست، بنابراین می‌توان اثرات جانبی هر تابع را محاسبه کرد. دو فراخوانی 
تابع را در نظر بگیرید، که در آن اولی storage  را تغییر نمی دهد و دومی storage  را تغییر می‌دهد. اگر 
آرگومان‌ها و مقادیر بازگشتی آنها به یکدیگر وابسته نباشند، می‌توانیم فراخوانی‌های تابع را دوباره انجام بدهیم. 
به طور مشابه، اگر تابعی بدون اثرات جانبی باشد و نتیجه آن در صفر ضرب شود، می‌توانید فراخوانی تابع را به 
طور کامل حذف کنید.

در حال حاضر، پارامتر ``optimize--`` بهینه‌ساز مبتنی بر آپکد را برای بایت‌کد تولید شده و بهینه‌ساز Yul را 
برای کد Yul تولید شده به صورت داخلی فعال می‌کند، به عنوان مثال برای ABI coder v2. می‌توان از 
``solc --ir-optimized --optimize`` برای تولید یک Yul IR آزمایشی بهینه برای سورس سالیدیتی استفاده 
کرد. به طور مشابه، می‌توان از ``solc --strict-assembly --optimize`` برای حالت Yul مستقل استفاده 
کرد.

در زیر می‌توانید جزئیات بیشتری در مورد ماژول‌های بهینه ساز و مراحل بهینه سازی آنها بیابید.


مزایای بهینه سازی کد سالیدیتی
====================================

به طور کلی، بهینه ساز سعی می‌کند عبارات پیچیده را ساده کند، که هم اندازه کد و هم هزینه اجرا را کاهش 
می‌دهد، یعنی می‌تواند گَس مورد نیاز برای دیپلوی قرارداد و همچنین برای فراخوانی‌های خارجی انجام شده به 
قرارداد را کاهش دهد. همچنین توابع را تخصصی یا درون خطی می‌کند. به خصوص توابع درون‌ریزی عملیاتی 
است که می‌تواند باعث ایجاد کد بسیار بزرگ‌تر شود، اما اغلب انجام می‌شود زیرا فرصت‌هایی برای ساده‌سازی 
بیشتر ایجاد می‌کند.


تفاوت کدهای بهینه و غیربهینه
====================================================

به طور کلی، قابل مشاهده ترین تفاوت این است که عبارات ثابت در زمان کامپایل ارزیابی می‌شوند. وقتی صحبت 
از خروجی ASM می‌شود، می‌توان کاهش بلاک‌های کد معادل یا تکراری را نیز مشاهده کرد (خروجی فلگ‌های 
``asm--`` و ``asm --optimize--`` را مقایسه کنید). با این حال، وقتی صحبت از Yul/intermediate-
representation به میان می‌آید، ممکن است تفاوت‌های قابل توجهی وجود داشته باشد، برای مثال، ممکن 
است توابع خطی شوند، ترکیب شوند، یا بازنویسی شوند تا افزونگی‌ها حذف شوند، و غیره. (خروجی را بین فلگ‌های 
``ir--`` و ``--optimize --ir-optimized`` مقایسه کنید).


.. _optimizer-parameter-runs:

اجرای پارامترهای بهینه ساز 
========================

تعداد اجراها (``optimize-runs--``) تقریباً مشخص می‌کند که هر آپکدِ کد دیپلوی شده چند بار در طول عمر 
قرارداد اجرا می‌شود. این به این معنی است که یک پارامتر مبادله‌ای بین اندازه کد (هزینه دیپلوی) و هزینه اجرای 
کد (هزینه پس از دیپلوی) است. یک پارامتر "run" از "1" کد کوتاه اما گران قیمت تولید می‌کند. در مقابل، 
یک پارامتر " runs" بزرگتر کد طولانی‌تر اما کارآمدتر گاز تولید می‌کند. حداکثر مقدار پارامتر ```2**32-1`` است.


.. note::

    یک تصور غلط رایج این است که این پارامتر تعداد تکرارهای بهینه ساز را مشخص می‌کند. این درست نیست: 
    بهینه ساز همیشه هر چند بار که بتواند کد را بهبود ببخشد اجرا می شود.


ماژول بهینه ساز مبتنی بر آپکد
=============================


ماژول بهینه ساز مبتنی بر آپکد بر اساس کد اسمبلی کار می کند. توالی دستورالعمل ها را به بلاک‌های اصلی در 
``JUMPs`` و  ``JUMPDESTs`` تقسیم می‌کند. در داخل این بلوک‌ها، بهینه‌ساز دستورالعمل‌ها را آنالیز می‌کند و هر تغییری 
را در پشته، مِمُوری یا storage به‌عنوان عبارتی ثبت می‌کند که شامل یک دستورالعمل و فهرستی از آرگومان‌ها 
است که نشانگر عبارات دیگر هستند.


علاوه بر این، بهینه‌ساز مبتنی بر آپکد از کامپوننتی به نام «CommonSubexpressionEliminator» 
استفاده می‌کند که در میان سایر تسک‌ها، عباراتی را پیدا می‌کند که همیشه برابر هستند (در هر ورودی) و آنها 
را در یک کلاس عبارت ترکیب می‌کند. ابتدا سعی می‌کند هر عبارت جدید را در لیستی از عبارات از قبل شناخته 
شده پیدا کند. اگر چنین تطابقی یافت نشد، عبارت را با توجه به قوانینی 
مانند  ``constant + constant = sum_of_constants`` یا ``X * 1 = X`` ساده می‌کند. از آنجایی که این یک فرآیند 
بازگشتی است، اگر عامل دوم عبارت پیچیده‌تری باشد، می‌توانیم قانون دوم را نیز اعمال کنیم. که می‌دانیم همیشه 
به "یک" ارزیابی می‌شود.


برخی از مراحل بهینه ساز به طور نمادین مکان های storage  و مِمُوری را ردیابی می‌کنند. به عنوان مثال، این 
اطلاعات برای محاسبه هش Keccak-256 استفاده می‌شود که می‌تواند در طول زمان کامپایل ارزیابی شود. دنباله را در نظر بگیرید:

.. code-block:: none

    PUSH 32
    PUSH 0
    CALLDATALOAD
    PUSH 100
    DUP2
    MSTORE
    KECCAK256

یا معادل Yul

.. code-block:: yul

    let x := calldataload(0)
    mstore(x, 100)
    let value := keccak256(x, 32)

در این مورد، بهینه ساز مقدار را در یک مکان مِمُوری ``calldataload(0)`` ردیابی می‌کند و سپس متوجه می‌شود 
که هش Keccak-256 را می‌توان در زمان کامپایل ارزیابی کرد. این فقط در صورتی کار می‌کند که هیچ دستور 
دیگری وجود نداشته باشد که حافظه را بین ``mstore`` و ``keccak256`` تغییر دهد. بنابراین اگر دستوری وجود 
دارد که در مِمُوری (یا storage) می‌نویسد، باید دانش مِمُوری (یا storage) فعلی را پاک کنیم. با این حال، 
یک استثناء برای این پاک کردن وجود دارد، زمانی که ما به راحتی می‌توانیم ببینیم که دستورالعمل در یک مکان 
خاص نمی‌نویسد.

به عنوان مثال،

.. code-block:: yul

    let x := calldataload(0)
    mstore(x, 100)
    // Current knowledge memory location x -> 100
    let y := add(x, 32)
    // Does not clear the knowledge that x -> 100, since y does not write to [x, x + 32)
    mstore(y, 200)
    // This Keccak-256 can now be evaluated
    let value := keccak256(x, 32)

بنابراین، تغییرات در مکان‌های storage و مِمُوری، مثلاً مکان ``l`` ، باید دانش مربوط به مکان‌های storage یا 
مِمُوری را که ممکن است برابر با ``l`` باشد، پاک کند. به طور خاص، برای storage ، بهینه‌ساز باید تمام دانش 
مکان‌های نمادین را که ممکن است برابر با ``l`` باشد، و برای مِمُوری ، بهینه‌ساز باید تمام اطلاعات مکان‌های نمادین 
را که ممکن است حداقل ۳۲ بایت فاصله نداشته باشند، پاک کند. اگر ``m`` یک مکان دلخواه را نشان می دهد، پس 
این تصمیم در مورد پاک کردن با محاسبه مقدار ``sub(l, m)`` انجام می‌شود. برای storage ، اگر این مقدار به 
یک حرف غیر صفر ارزیابی شود، آنگاه دانش در مورد ``m`` حفظ خواهد شد. برای مِمُوری، اگر مقدار به معنای واقعی 
کلمه بین ``32`` و  ``2**256 - 32`` باشد، آنگاه دانش در مورد ``m`` حفظ خواهد شد. در تمام موارد دیگر، دانش در 
مورد ``m`` پاک خواهد شد.

پس از این فرآیند، ما می‌دانیم که در پایان کدام عبارات باید در پشته قرار گیرند و لیستی از تغییرات در مِمُوری 
و storage داریم. این اطلاعات همراه با بلاک‌های اصلی ذخیره می‌شود و برای پیوند آنها استفاده می‌شود. علاوه 
بر این، دانش در مورد پشته، storage و پیکربندی مِمُوری به بلوک(های) بعدی ارسال می‌شود.

اگر اهداف تمام دستورات  ``JUMP`` و ``JUMPI`` را بدانیم، می‌توانیم یک گراف جریان کنترل کامل برنامه را بسازیم. 
اگر فقط یک هدف وجود داشته باشد که ما نمی‌دانیم (این می‌تواند اتفاق بیفتد، همانطور که در اصل، اهداف 
JUMP را می‌توان از ورودی‌ها محاسبه کرد)، باید تمام اطلاعات مربوط به وضعیت ورودی یک بلاک را پاک کنیم، 
زیرا می‌تواند هدف JUMP ناشناخته باشد. . اگر ماژول بهینه ساز مبتنی بر کد یک ``JUMPI`` را پیدا کند که 
شرایط آن به یک ثابت ارزیابی می‌شود، آن را به یک ``JUMP`` بدون قید و شرط تبدیل می‌کند.


As the last step, the code in each block is re-generated. The optimizer creates
a dependency graph from the expressions on the stack at the end of the block,
and it drops every operation that is not part of this graph. It generates code
that applies the modifications to memory and storage in the order they were
made in the original code (dropping modifications which were found not to be
needed). Finally, it generates all values that are required to be on the
stack in the correct place.

These steps are applied to each basic block and the newly generated code
is used as replacement if it is smaller. If a basic block is split at a
``JUMPI`` and during the analysis, the condition evaluates to a constant,
the ``JUMPI`` is replaced based on the value of the constant. Thus code like

.. code-block:: solidity

    uint x = 7;
    data[7] = 9;
    if (data[x] != x + 2) // this condition is never true
      return 2;
    else
      return 1;

simplifies to this:

.. code-block:: solidity

    data[7] = 9;
    return 1;

Simple Inlining
---------------

Since Solidity version 0.8.2, there is another optimizer step that replaces certain
jumps to blocks containing "simple" instructions ending with a "jump" by a copy of these instructions.
This corresponds to inlining of simple, small Solidity or Yul functions. In particular, the sequence
``PUSHTAG(tag) JUMP`` may be replaced, whenever the ``JUMP`` is marked as jump "into" a
function and behind ``tag`` there is a basic block (as described above for the
"CommonSubexpressionEliminator") that ends in another ``JUMP`` which is marked as a jump
"out of" a function.

In particular, consider the following prototypical example of assembly generated for a
call to an internal Solidity function:

.. code-block:: text

      tag_return
      tag_f
      jump      // in
    tag_return:
      ...opcodes after call to f...

    tag_f:
      ...body of function f...
      jump      // out

As long as the body of the function is a continuous basic block, the "Inliner" can replace ``tag_f jump`` by
the block at ``tag_f`` resulting in:

.. code-block:: text

      tag_return
      ...body of function f...
      jump
    tag_return:
      ...opcodes after call to f...

    tag_f:
      ...body of function f...
      jump      // out

Now ideally, the other optimizer steps described above will result in the return tag push being moved
towards the remaining jump resulting in:

.. code-block:: text

      ...body of function f...
      tag_return
      jump
    tag_return:
      ...opcodes after call to f...

    tag_f:
      ...body of function f...
      jump      // out

In this situation the "PeepholeOptimizer" will remove the return jump. Ideally, all of this can be done
for all references to ``tag_f`` leaving it unused, s.t. it can be removed, yielding:

.. code-block:: text

    ...body of function f...
    ...opcodes after call to f...

So the call to function ``f`` is inlined and the original definition of ``f`` can be removed.

Inlining like this is attempted, whenever a heuristics suggests that inlining is cheaper over the lifetime of a
contract than not inlining. This heuristics depends on the size of the function body, the
number of other references to its tag (approximating the number of calls to the function) and
the expected number of executions of the contract (the global optimizer parameter "runs").


Yul-Based Optimizer Module
==========================

The Yul-based optimizer consists of several stages and components that all transform
the AST in a semantically equivalent way. The goal is to end up either with code
that is shorter or at least only marginally longer but will allow further
optimization steps.

.. warning::

    Since the optimizer is under heavy development, the information here might be outdated.
    If you rely on a certain functionality, please reach out to the team directly.

The optimizer currently follows a purely greedy strategy and does not do any
backtracking.

All components of the Yul-based optimizer module are explained below.
The following transformation steps are the main components:

- SSA Transform
- Common Subexpression Eliminator
- Expression Simplifier
- Redundant Assign Eliminator
- Full Inliner

Optimizer Steps
---------------

This is a list of all steps the Yul-based optimizer sorted alphabetically. You can find more information
on the individual steps and their sequence below.

- :ref:`block-flattener`.
- :ref:`circular-reference-pruner`.
- :ref:`common-subexpression-eliminator`.
- :ref:`conditional-simplifier`.
- :ref:`conditional-unsimplifier`.
- :ref:`control-flow-simplifier`.
- :ref:`dead-code-eliminator`.
- :ref:`equivalent-function-combiner`.
- :ref:`expression-joiner`.
- :ref:`expression-simplifier`.
- :ref:`expression-splitter`.
- :ref:`for-loop-condition-into-body`.
- :ref:`for-loop-condition-out-of-body`.
- :ref:`for-loop-init-rewriter`.
- :ref:`expression-inliner`.
- :ref:`full-inliner`.
- :ref:`function-grouper`.
- :ref:`function-hoister`.
- :ref:`function-specializer`.
- :ref:`literal-rematerialiser`.
- :ref:`load-resolver`.
- :ref:`loop-invariant-code-motion`.
- :ref:`redundant-assign-eliminator`.
- :ref:`reasoning-based-simplifier`.
- :ref:`rematerialiser`.
- :ref:`SSA-reverser`.
- :ref:`SSA-transform`.
- :ref:`structural-simplifier`.
- :ref:`unused-function-parameter-pruner`.
- :ref:`unused-pruner`.
- :ref:`var-decl-initializer`.

Selecting Optimizations
-----------------------

By default the optimizer applies its predefined sequence of optimization steps to
the generated assembly. You can override this sequence and supply your own using
the ``--yul-optimizations`` option:

.. code-block:: bash

    solc --optimize --ir-optimized --yul-optimizations 'dhfoD[xarrscLMcCTU]uljmul'

The sequence inside ``[...]`` will be applied multiple times in a loop until the Yul code
remains unchanged or until the maximum number of rounds (currently 12) has been reached.

Available abbreviations are listed in the `Yul optimizer docs <yul.rst#optimization-step-sequence>`_.

Preprocessing
-------------

The preprocessing components perform transformations to get the program
into a certain normal form that is easier to work with. This normal
form is kept during the rest of the optimization process.

.. _disambiguator:

Disambiguator
^^^^^^^^^^^^^

The disambiguator takes an AST and returns a fresh copy where all identifiers have
unique names in the input AST. This is a prerequisite for all other optimizer stages.
One of the benefits is that identifier lookup does not need to take scopes into account
which simplifies the analysis needed for other steps.

All subsequent stages have the property that all names stay unique. This means if
a new identifier needs to be introduced, a new unique name is generated.

.. _function-hoister:

FunctionHoister
^^^^^^^^^^^^^^^

The function hoister moves all function definitions to the end of the topmost block. This is
a semantically equivalent transformation as long as it is performed after the
disambiguation stage. The reason is that moving a definition to a higher-level block cannot decrease
its visibility and it is impossible to reference variables defined in a different function.

The benefit of this stage is that function definitions can be looked up more easily
and functions can be optimized in isolation without having to traverse the AST completely.

.. _function-grouper:

FunctionGrouper
^^^^^^^^^^^^^^^

The function grouper has to be applied after the disambiguator and the function hoister.
Its effect is that all topmost elements that are not function definitions are moved
into a single block which is the first statement of the root block.

After this step, a program has the following normal form:

.. code-block:: text

    { I F... }

Where ``I`` is a (potentially empty) block that does not contain any function definitions (not even recursively)
and ``F`` is a list of function definitions such that no function contains a function definition.

The benefit of this stage is that we always know where the list of function begins.

.. _for-loop-condition-into-body:

ForLoopConditionIntoBody
^^^^^^^^^^^^^^^^^^^^^^^^

This transformation moves the loop-iteration condition of a for-loop into loop body.
We need this transformation because :ref:`expression-splitter` will not
apply to iteration condition expressions (the ``C`` in the following example).

.. code-block:: text

    for { Init... } C { Post... } {
        Body...
    }

is transformed to

.. code-block:: text

    for { Init... } 1 { Post... } {
        if iszero(C) { break }
        Body...
    }

This transformation can also be useful when paired with ``LoopInvariantCodeMotion``, since
invariants in the loop-invariant conditions can then be taken outside the loop.

.. _for-loop-init-rewriter:

ForLoopInitRewriter
^^^^^^^^^^^^^^^^^^^

This transformation moves the initialization part of a for-loop to before
the loop:

.. code-block:: text

    for { Init... } C { Post... } {
        Body...
    }

is transformed to

.. code-block:: text

    {
        Init...
        for {} C { Post... } {
            Body...
        }
    }

This eases the rest of the optimization process because we can ignore
the complicated scoping rules of the for loop initialisation block.

.. _var-decl-initializer:

VarDeclInitializer
^^^^^^^^^^^^^^^^^^
This step rewrites variable declarations so that all of them are initialized.
Declarations like ``let x, y`` are split into multiple declaration statements.

Only supports initializing with the zero literal for now.

Pseudo-SSA Transformation
-------------------------

The purpose of this components is to get the program into a longer form,
so that other components can more easily work with it. The final representation
will be similar to a static-single-assignment (SSA) form, with the difference
that it does not make use of explicit "phi" functions which combines the values
from different branches of control flow because such a feature does not exist
in the Yul language. Instead, when control flow merges, if a variable is re-assigned
in one of the branches, a new SSA variable is declared to hold its current value,
so that the following expressions still only need to reference SSA variables.

An example transformation is the following:

.. code-block:: yul

    {
        let a := calldataload(0)
        let b := calldataload(0x20)
        if gt(a, 0) {
            b := mul(b, 0x20)
        }
        a := add(a, 1)
        sstore(a, add(b, 0x20))
    }


When all the following transformation steps are applied, the program will look
as follows:

.. code-block:: yul

    {
        let _1 := 0
        let a_9 := calldataload(_1)
        let a := a_9
        let _2 := 0x20
        let b_10 := calldataload(_2)
        let b := b_10
        let _3 := 0
        let _4 := gt(a_9, _3)
        if _4
        {
            let _5 := 0x20
            let b_11 := mul(b_10, _5)
            b := b_11
        }
        let b_12 := b
        let _6 := 1
        let a_13 := add(a_9, _6)
        let _7 := 0x20
        let _8 := add(b_12, _7)
        sstore(a_13, _8)
    }

Note that the only variable that is re-assigned in this snippet is ``b``.
This re-assignment cannot be avoided because ``b`` has different values
depending on the control flow. All other variables never change their
value once they are defined. The advantage of this property is that
variables can be freely moved around and references to them
can be exchanged by their initial value (and vice-versa),
as long as these values are still valid in the new context.

Of course, the code here is far from being optimized. To the contrary, it is much
longer. The hope is that this code will be easier to work with and furthermore,
there are optimizer steps that undo these changes and make the code more
compact again at the end.

.. _expression-splitter:

ExpressionSplitter
^^^^^^^^^^^^^^^^^^

The expression splitter turns expressions like ``add(mload(0x123), mul(mload(0x456), 0x20))``
into a sequence of declarations of unique variables that are assigned sub-expressions
of that expression so that each function call has only variables or literals
as arguments.

The above would be transformed into

.. code-block:: yul

    {
        let _1 := mload(0x123)
        let _2 := mul(_1, 0x20)
        let _3 := mload(0x456)
        let z := add(_3, _2)
    }

Note that this transformation does not change the order of opcodes or function calls.

It is not applied to loop iteration-condition, because the loop control flow does not allow
this "outlining" of the inner expressions in all cases. We can sidestep this limitation by applying
:ref:`for-loop-condition-into-body` to move the iteration condition into loop body.

The final program should be in a form such that (with the exception of loop conditions)
function calls cannot appear nested inside expressions
and all function call arguments have to be literals or variables.

The benefits of this form are that it is much easier to re-order the sequence of opcodes
and it is also easier to perform function call inlining. Furthermore, it is simpler
to replace individual parts of expressions or re-organize the "expression tree".
The drawback is that such code is much harder to read for humans.

.. _SSA-transform:

SSATransform
^^^^^^^^^^^^

This stage tries to replace repeated assignments to
existing variables by declarations of new variables as much as
possible.
The reassignments are still there, but all references to the
reassigned variables are replaced by the newly declared variables.

Example:

.. code-block:: yul

    {
        let a := 1
        mstore(a, 2)
        a := 3
    }

is transformed to

.. code-block:: yul

    {
        let a_1 := 1
        let a := a_1
        mstore(a_1, 2)
        let a_3 := 3
        a := a_3
    }

Exact semantics:

For any variable ``a`` that is assigned to somewhere in the code
(variables that are declared with value and never re-assigned
are not modified) perform the following transforms:

- replace ``let a := v`` by ``let a_i := v   let a := a_i``
- replace ``a := v`` by ``let a_i := v   a := a_i`` where ``i`` is a number such that ``a_i`` is yet unused.

Furthermore, always record the current value of ``i`` used for ``a`` and replace each
reference to ``a`` by ``a_i``.
The current value mapping is cleared for a variable ``a`` at the end of each block
in which it was assigned to and at the end of the for loop init block if it is assigned
inside the for loop body or post block.
If a variable's value is cleared according to the rule above and the variable is declared outside
the block, a new SSA variable will be created at the location where control flow joins,
this includes the beginning of loop post/body block and the location right after
If/Switch/ForLoop/Block statement.

After this stage, the Redundant Assign Eliminator is recommended to remove the unnecessary
intermediate assignments.

This stage provides best results if the Expression Splitter and the Common Subexpression Eliminator
are run right before it, because then it does not generate excessive amounts of variables.
On the other hand, the Common Subexpression Eliminator could be more efficient if run after the
SSA transform.

.. _redundant-assign-eliminator:

RedundantAssignEliminator
^^^^^^^^^^^^^^^^^^^^^^^^^

The SSA transform always generates an assignment of the form ``a := a_i``, even though
these might be unnecessary in many cases, like the following example:

.. code-block:: yul

    {
        let a := 1
        a := mload(a)
        a := sload(a)
        sstore(a, 1)
    }

The SSA transform converts this snippet to the following:

.. code-block:: yul

    {
        let a_1 := 1
        let a := a_1
        let a_2 := mload(a_1)
        a := a_2
        let a_3 := sload(a_2)
        a := a_3
        sstore(a_3, 1)
    }

The Redundant Assign Eliminator removes all the three assignments to ``a``, because
the value of ``a`` is not used and thus turn this
snippet into strict SSA form:

.. code-block:: yul

    {
        let a_1 := 1
        let a_2 := mload(a_1)
        let a_3 := sload(a_2)
        sstore(a_3, 1)
    }

Of course the intricate parts of determining whether an assignment is redundant or not
are connected to joining control flow.

The component works as follows in detail:

The AST is traversed twice: in an information gathering step and in the
actual removal step. During information gathering, we maintain a
mapping from assignment statements to the three states
"unused", "undecided" and "used" which signifies whether the assigned
value will be used later by a reference to the variable.

When an assignment is visited, it is added to the mapping in the "undecided" state
(see remark about for loops below) and every other assignment to the same variable
that is still in the "undecided" state is changed to "unused".
When a variable is referenced, the state of any assignment to that variable still
in the "undecided" state is changed to "used".

At points where control flow splits, a copy
of the mapping is handed over to each branch. At points where control flow
joins, the two mappings coming from the two branches are combined in the following way:
Statements that are only in one mapping or have the same state are used unchanged.
Conflicting values are resolved in the following way:

- "unused", "undecided" -> "undecided"
- "unused", "used" -> "used"
- "undecided, "used" -> "used"

For for-loops, the condition, body and post-part are visited twice, taking
the joining control-flow at the condition into account.
In other words, we create three control flow paths: Zero runs of the loop,
one run and two runs and then combine them at the end.

Simulating a third run or even more is unnecessary, which can be seen as follows:

A state of an assignment at the beginning of the iteration will deterministically
result in a state of that assignment at the end of the iteration. Let this
state mapping function be called ``f``. The combination of the three different
states ``unused``, ``undecided`` and ``used`` as explained above is the ``max``
operation where ``unused = 0``, ``undecided = 1`` and ``used = 2``.

The proper way would be to compute

.. code-block:: none

    max(s, f(s), f(f(s)), f(f(f(s))), ...)

as state after the loop. Since ``f`` just has a range of three different values,
iterating it has to reach a cycle after at most three iterations,
and thus ``f(f(f(s)))`` has to equal one of ``s``, ``f(s)``, or ``f(f(s))``
and thus

.. code-block:: none

    max(s, f(s), f(f(s))) = max(s, f(s), f(f(s)), f(f(f(s))), ...).

In summary, running the loop at most twice is enough because there are only three
different states.

For switch statements that have a "default"-case, there is no control-flow
part that skips the switch.

When a variable goes out of scope, all statements still in the "undecided"
state are changed to "unused", unless the variable is the return
parameter of a function - there, the state changes to "used".

In the second traversal, all assignments that are in the "unused" state are removed.

This step is usually run right after the SSA transform to complete
the generation of the pseudo-SSA.

Tools
-----

Movability
^^^^^^^^^^

Movability is a property of an expression. It roughly means that the expression
is side-effect free and its evaluation only depends on the values of variables
and the call-constant state of the environment. Most expressions are movable.
The following parts make an expression non-movable:

- function calls (might be relaxed in the future if all statements in the function are movable)
- opcodes that (can) have side-effects (like ``call`` or ``selfdestruct``)
- opcodes that read or write memory, storage or external state information
- opcodes that depend on the current PC, memory size or returndata size

DataflowAnalyzer
^^^^^^^^^^^^^^^^

The Dataflow Analyzer is not an optimizer step itself but is used as a tool
by other components. While traversing the AST, it tracks the current value of
each variable, as long as that value is a movable expression.
It records the variables that are part of the expression
that is currently assigned to each other variable. Upon each assignment to
a variable ``a``, the current stored value of ``a`` is updated and
all stored values of all variables ``b`` are cleared whenever ``a`` is part
of the currently stored expression for ``b``.

At control-flow joins, knowledge about variables is cleared if they have or would be assigned
in any of the control-flow paths. For instance, upon entering a
for loop, all variables are cleared that will be assigned during the
body or the post block.

Expression-Scale Simplifications
--------------------------------

These simplification passes change expressions and replace them by equivalent
and hopefully simpler expressions.

.. _common-subexpression-eliminator:

CommonSubexpressionEliminator
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This step uses the Dataflow Analyzer and replaces subexpressions that
syntactically match the current value of a variable by a reference to
that variable. This is an equivalence transform because such subexpressions have
to be movable.

All subexpressions that are identifiers themselves are replaced by their
current value if the value is an identifier.

The combination of the two rules above allow to compute a local value
numbering, which means that if two variables have the same
value, one of them will always be unused. The Unused Pruner or the
Redundant Assign Eliminator will then be able to fully eliminate such
variables.

This step is especially efficient if the expression splitter is run
before. If the code is in pseudo-SSA form,
the values of variables are available for a longer time and thus we
have a higher chance of expressions to be replaceable.

The expression simplifier will be able to perform better replacements
if the common subexpression eliminator was run right before it.

.. _expression-simplifier:

Expression Simplifier
^^^^^^^^^^^^^^^^^^^^^

The Expression Simplifier uses the Dataflow Analyzer and makes use
of a list of equivalence transforms on expressions like ``X + 0 -> X``
to simplify the code.

It tries to match patterns like ``X + 0`` on each subexpression.
During the matching procedure, it resolves variables to their currently
assigned expressions to be able to match more deeply nested patterns
even when the code is in pseudo-SSA form.

Some of the patterns like ``X - X -> 0`` can only be applied as long
as the expression ``X`` is movable, because otherwise it would remove its potential side-effects.
Since variable references are always movable, even if their current
value might not be, the Expression Simplifier is again more powerful
in split or pseudo-SSA form.

.. _literal-rematerialiser:

LiteralRematerialiser
^^^^^^^^^^^^^^^^^^^^^

To be documented.

.. _load-resolver:

LoadResolver
^^^^^^^^^^^^

Optimisation stage that replaces expressions of type ``sload(x)`` and ``mload(x)`` by the value
currently stored in storage resp. memory, if known.

Works best if the code is in SSA form.

Prerequisite: Disambiguator, ForLoopInitRewriter.

.. _reasoning-based-simplifier:

ReasoningBasedSimplifier
^^^^^^^^^^^^^^^^^^^^^^^^

This optimizer uses SMT solvers to check whether ``if`` conditions are constant.

- If ``constraints AND condition`` is UNSAT, the condition is never true and the whole body can be removed.
- If ``constraints AND NOT condition`` is UNSAT, the condition is always true and can be replaced by ``1``.

The simplifications above can only be applied if the condition is movable.

It is only effective on the EVM dialect, but safe to use on other dialects.

Prerequisite: Disambiguator, SSATransform.

Statement-Scale Simplifications
-------------------------------

.. _circular-reference-pruner:

CircularReferencesPruner
^^^^^^^^^^^^^^^^^^^^^^^^

This stage removes functions that call each other but are
neither externally referenced nor referenced from the outermost context.

.. _conditional-simplifier:

ConditionalSimplifier
^^^^^^^^^^^^^^^^^^^^^

The Conditional Simplifier inserts assignments to condition variables if the value can be determined
from the control-flow.

Destroys SSA form.

Currently, this tool is very limited, mostly because we do not yet have support
for boolean types. Since conditions only check for expressions being nonzero,
we cannot assign a specific value.

Current features:

- switch cases: insert "<condition> := <caseLabel>"
- after if statement with terminating control-flow, insert "<condition> := 0"

Future features:

- allow replacements by "1"
- take termination of user-defined functions into account

Works best with SSA form and if dead code removal has run before.

Prerequisite: Disambiguator.

.. _conditional-unsimplifier:

ConditionalUnsimplifier
^^^^^^^^^^^^^^^^^^^^^^^

Reverse of Conditional Simplifier.

.. _control-flow-simplifier:

ControlFlowSimplifier
^^^^^^^^^^^^^^^^^^^^^

Simplifies several control-flow structures:

- replace if with empty body with pop(condition)
- remove empty default switch case
- remove empty switch case if no default case exists
- replace switch with no cases with pop(expression)
- turn switch with single case into if
- replace switch with only default case with pop(expression) and body
- replace switch with const expr with matching case body
- replace ``for`` with terminating control flow and without other break/continue by ``if``
- remove ``leave`` at the end of a function.

None of these operations depend on the data flow. The StructuralSimplifier
performs similar tasks that do depend on data flow.

The ControlFlowSimplifier does record the presence or absence of ``break``
and ``continue`` statements during its traversal.

Prerequisite: Disambiguator, FunctionHoister, ForLoopInitRewriter.
Important: Introduces EVM opcodes and thus can only be used on EVM code for now.

.. _dead-code-eliminator:

DeadCodeEliminator
^^^^^^^^^^^^^^^^^^

This optimization stage removes unreachable code.

Unreachable code is any code within a block which is preceded by a
leave, return, invalid, break, continue, selfdestruct or revert.

Function definitions are retained as they might be called by earlier
code and thus are considered reachable.

Because variables declared in a for loop's init block have their scope extended to the loop body,
we require ForLoopInitRewriter to run before this step.

Prerequisite: ForLoopInitRewriter, Function Hoister, Function Grouper

.. _unused-pruner:

UnusedPruner
^^^^^^^^^^^^

This step removes the definitions of all functions that are never referenced.

It also removes the declaration of variables that are never referenced.
If the declaration assigns a value that is not movable, the expression is retained,
but its value is discarded.

All movable expression statements (expressions that are not assigned) are removed.

.. _structural-simplifier:

StructuralSimplifier
^^^^^^^^^^^^^^^^^^^^

This is a general step that performs various kinds of simplifications on
a structural level:

- replace if statement with empty body by ``pop(condition)``
- replace if statement with true condition by its body
- remove if statement with false condition
- turn switch with single case into if
- replace switch with only default case by ``pop(expression)`` and body
- replace switch with literal expression by matching case body
- replace for loop with false condition by its initialization part

This component uses the Dataflow Analyzer.

.. _block-flattener:

BlockFlattener
^^^^^^^^^^^^^^

This stage eliminates nested blocks by inserting the statement in the
inner block at the appropriate place in the outer block:

.. code-block:: yul

    {
        let x := 2
        {
            let y := 3
            mstore(x, y)
        }
    }

is transformed to

.. code-block:: yul

    {
        let x := 2
        let y := 3
        mstore(x, y)
    }

As long as the code is disambiguated, this does not cause a problem because
the scopes of variables can only grow.

.. _loop-invariant-code-motion:

LoopInvariantCodeMotion
^^^^^^^^^^^^^^^^^^^^^^^
This optimization moves movable SSA variable declarations outside the loop.

Only statements at the top level in a loop's body or post block are considered, i.e variable
declarations inside conditional branches will not be moved out of the loop.

Requirements:

- The Disambiguator, ForLoopInitRewriter and FunctionHoister must be run upfront.
- Expression splitter and SSA transform should be run upfront to obtain better result.


Function-Level Optimizations
----------------------------

.. _function-specializer:

FunctionSpecializer
^^^^^^^^^^^^^^^^^^^

This step specializes the function with its literal arguments.

If a function, say, ``function f(a, b) { sstore (a, b) }``, is called with literal arguments, for
example, ``f(x, 5)``, where ``x`` is an identifier, it could be specialized by creating a new
function ``f_1`` that takes only one argument, i.e.,

.. code-block:: yul

    function f_1(a_1) {
        let b_1 := 5
        sstore(a_1, b_1)
    }

Other optimization steps will be able to make more simplifications to the function. The
optimization step is mainly useful for functions that would not be inlined.

Prerequisites: Disambiguator, FunctionHoister

LiteralRematerialiser is recommended as a prerequisite, even though it's not required for
correctness.

.. _unused-function-parameter-pruner:

UnusedFunctionParameterPruner
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This step removes unused parameters in a function.

If a parameter is unused, like ``c`` and ``y`` in, ``function f(a,b,c) -> x, y { x := div(a,b) }``, we
remove the parameter and create a new "linking" function as follows:

.. code-block:: yul

    function f(a,b) -> x { x := div(a,b) }
    function f2(a,b,c) -> x, y { x := f(a,b) }

and replace all references to ``f`` by ``f2``.
The inliner should be run afterwards to make sure that all references to ``f2`` are replaced by
``f``.

Prerequisites: Disambiguator, FunctionHoister, LiteralRematerialiser.

The step LiteralRematerialiser is not required for correctness. It helps deal with cases such as:
``function f(x) -> y { revert(y, y} }`` where the literal ``y`` will be replaced by its value ``0``,
allowing us to rewrite the function.

.. _equivalent-function-combiner:

EquivalentFunctionCombiner
^^^^^^^^^^^^^^^^^^^^^^^^^^

If two functions are syntactically equivalent, while allowing variable
renaming but not any re-ordering, then any reference to one of the
functions is replaced by the other.

The actual removal of the function is performed by the Unused Pruner.


Function Inlining
-----------------

.. _expression-inliner:

ExpressionInliner
^^^^^^^^^^^^^^^^^

This component of the optimizer performs restricted function inlining by inlining functions that can be
inlined inside functional expressions, i.e. functions that:

- return a single value.
- have a body like ``r := <functional expression>``.
- neither reference themselves nor ``r`` in the right hand side.

Furthermore, for all parameters, all of the following need to be true:

- The argument is movable.
- The parameter is either referenced less than twice in the function body, or the argument is rather cheap
  ("cost" of at most 1, like a constant up to 0xff).

Example: The function to be inlined has the form of ``function f(...) -> r { r := E }`` where
``E`` is an expression that does not reference ``r`` and all arguments in the function call are movable expressions.

The result of this inlining is always a single expression.

This component can only be used on sources with unique names.

.. _full-inliner:

FullInliner
^^^^^^^^^^^

The Full Inliner replaces certain calls of certain functions
by the function's body. This is not very helpful in most cases, because
it just increases the code size but does not have a benefit. Furthermore,
code is usually very expensive and we would often rather have shorter
code than more efficient code. In same cases, though, inlining a function
can have positive effects on subsequent optimizer steps. This is the case
if one of the function arguments is a constant, for example.

During inlining, a heuristic is used to tell if the function call
should be inlined or not.
The current heuristic does not inline into "large" functions unless
the called function is tiny. Functions that are only used once
are inlined, as well as medium-sized functions, while function
calls with constant arguments allow slightly larger functions.


In the future, we may include a backtracking component
that, instead of inlining a function right away, only specializes it,
which means that a copy of the function is generated where
a certain parameter is always replaced by a constant. After that,
we can run the optimizer on this specialized function. If it
results in heavy gains, the specialized function is kept,
otherwise the original function is used instead.

Cleanup
-------

The cleanup is performed at the end of the optimizer run. It tries
to combine split expressions into deeply nested ones again and also
improves the "compilability" for stack machines by eliminating
variables as much as possible.

.. _expression-joiner:

ExpressionJoiner
^^^^^^^^^^^^^^^^

This is the opposite operation of the expression splitter. It turns a sequence of
variable declarations that have exactly one reference into a complex expression.
This stage fully preserves the order of function calls and opcode executions.
It does not make use of any information concerning the commutativity of the opcodes;
if moving the value of a variable to its place of use would change the order
of any function call or opcode execution, the transformation is not performed.

Note that the component will not move the assigned value of a variable assignment
or a variable that is referenced more than once.

The snippet ``let x := add(0, 2) let y := mul(x, mload(2))`` is not transformed,
because it would cause the order of the call to the opcodes ``add`` and
``mload`` to be swapped - even though this would not make a difference
because ``add`` is movable.

When reordering opcodes like that, variable references and literals are ignored.
Because of that, the snippet ``let x := add(0, 2) let y := mul(x, 3)`` is
transformed to ``let y := mul(add(0, 2), 3)``, even though the ``add`` opcode
would be executed after the evaluation of the literal ``3``.

.. _SSA-reverser:

SSAReverser
^^^^^^^^^^^

This is a tiny step that helps in reversing the effects of the SSA transform
if it is combined with the Common Subexpression Eliminator and the
Unused Pruner.

The SSA form we generate is detrimental to code generation on the EVM and
WebAssembly alike because it generates many local variables. It would
be better to just re-use existing variables with assignments instead of
fresh variable declarations.

The SSA transform rewrites

.. code-block:: yul

    let a := calldataload(0)
    mstore(a, 1)

to

.. code-block:: yul

    let a_1 := calldataload(0)
    let a := a_1
    mstore(a_1, 1)
    let a_2 := calldataload(0x20)
    a := a_2

The problem is that instead of ``a``, the variable ``a_1`` is used
whenever ``a`` was referenced. The SSA transform changes statements
of this form by just swapping out the declaration and the assignment. The above
snippet is turned into

.. code-block:: yul

    let a := calldataload(0)
    let a_1 := a
    mstore(a_1, 1)
    a := calldataload(0x20)
    let a_2 := a

This is a very simple equivalence transform, but when we now run the
Common Subexpression Eliminator, it will replace all occurrences of ``a_1``
by ``a`` (until ``a`` is re-assigned). The Unused Pruner will then
eliminate the variable ``a_1`` altogether and thus fully reverse the
SSA transform.

.. _stack-compressor:

StackCompressor
^^^^^^^^^^^^^^^

One problem that makes code generation for the Ethereum Virtual Machine
hard is the fact that there is a hard limit of 16 slots for reaching
down the expression stack. This more or less translates to a limit
of 16 local variables. The stack compressor takes Yul code and
compiles it to EVM bytecode. Whenever the stack difference is too
large, it records the function this happened in.

For each function that caused such a problem, the Rematerialiser
is called with a special request to aggressively eliminate specific
variables sorted by the cost of their values.

On failure, this procedure is repeated multiple times.

.. _rematerialiser:

Rematerialiser
^^^^^^^^^^^^^^

The rematerialisation stage tries to replace variable references by the expression that
was last assigned to the variable. This is of course only beneficial if this expression
is comparatively cheap to evaluate. Furthermore, it is only semantically equivalent if
the value of the expression did not change between the point of assignment and the
point of use. The main benefit of this stage is that it can save stack slots if it
leads to a variable being eliminated completely (see below), but it can also
save a DUP opcode on the EVM if the expression is very cheap.

The Rematerialiser uses the Dataflow Analyzer to track the current values of variables,
which are always movable.
If the value is very cheap or the variable was explicitly requested to be eliminated,
the variable reference is replaced by its current value.

.. _for-loop-condition-out-of-body:

ForLoopConditionOutOfBody
^^^^^^^^^^^^^^^^^^^^^^^^^

Reverses the transformation of ForLoopConditionIntoBody.

For any movable ``c``, it turns

.. code-block:: none

    for { ... } 1 { ... } {
    if iszero(c) { break }
    ...
    }

into

.. code-block:: none

    for { ... } c { ... } {
    ...
    }

and it turns

.. code-block:: none

    for { ... } 1 { ... } {
    if c { break }
    ...
    }

into

.. code-block:: none

    for { ... } iszero(c) { ... } {
    ...
    }

The LiteralRematerialiser should be run before this step.


WebAssembly specific
--------------------

MainFunction
^^^^^^^^^^^^

Changes the topmost block to be a function with a specific name ("main") which has no
inputs nor outputs.

Depends on the Function Grouper.
