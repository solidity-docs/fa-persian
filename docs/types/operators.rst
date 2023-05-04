.. index:: ! operator

<<<<<<< HEAD
اپراتورهای شامل  LValues
===========================
اگر  ``a`` یک LValue باشد (به عنوان مثال یک متغیر یا چیزی که می‌توان به آن اختصاص داد)، عملگرهای زیر به صورت مختصر در دسترس هستند:
=======
Operators
=========

Arithmetic and bit operators can be applied even if the two operands do not have the same type.
For example, you can compute ``y = x + z``, where ``x`` is a ``uint8`` and ``z`` has
the type ``uint32``. In these cases, the following mechanism will be used to determine
the type in which the operation is computed (this is important in case of overflow)
and the type of the operator's result:

1. If the type of the right operand can be implicitly converted to the type of the left
   operand, use the type of the left operand,
2. if the type of the left operand can be implicitly converted to the type of the right
   operand, use the type of the right operand,
3. otherwise, the operation is not allowed.

In case one of the operands is a :ref:`literal number <rational_literals>` it is first converted to its
"mobile type", which is the smallest type that can hold the value
(unsigned types of the same bit-width are considered "smaller" than the signed types).
If both are literal numbers, the operation is computed with effectively unlimited precision in
that the expression is evaluated to whatever precision is necessary so that none is lost
when the result is used with a non-literal type.

The operator's result type is the same as the type the operation is performed in,
except for comparison operators where the result is always ``bool``.

The operators ``**`` (exponentiation), ``<<``  and ``>>`` use the type of the
left operand for the operation and the result.

Ternary Operator
----------------
The ternary operator is used in expressions of the form ``<expression> ? <trueExpression> : <falseExpression>``.
It evaluates one of the latter two given expressions depending upon the result of the evaluation of the main ``<expression>``.
If ``<expression>`` evaluates to ``true``, then ``<trueExpression>`` will be evaluated, otherwise ``<falseExpression>`` is evaluated.

The result of the ternary operator does not have a rational number type, even if all of its operands are rational number literals.
The result type is determined from the types of the two operands in the same way as above, converting to their mobile type first if required.

As a consequence, ``255 + (true ? 1 : 0)`` will revert due to arithmetic overflow.
The reason is that ``(true ? 1 : 0)`` is of ``uint8`` type, which forces the addition to be performed in ``uint8`` as well,
and 256 exceeds the range allowed for this type.

Another consequence is that an expression like ``1.5 + 1.5`` is valid but ``1.5 + (true ? 1.5 : 2.5)`` is not.
This is because the former is a rational expression evaluated in unlimited precision and only its final value matters.
The latter involves a conversion of a fractional rational number to an integer, which is currently disallowed.

.. index:: assignment, lvalue, ! compound operators

Compound and Increment/Decrement Operators
------------------------------------------
>>>>>>> english/develop

``a += e``  معادل  ``a = a + e`` است. عملگرها  ``=-`` ، ``=*`` ، ``=/`` ، ``%=`` ، ``=|`` ، ``=&``  و ``=^``  بر این 
اساس تعریف می‌شوند.  ``++a`` و  ``--a`` معادل  ``a += 1`` / ``a -= 1``  هستند اما این عبارت هنوز مقدار 
قبلی  ``a`` را دارد. در مقابل ،  ``a--`` و  ``a++`` تأثیر یکسانی در  ``a`` دارند اما مقدار را پس از تغییر برمی‌گردانند.


.. index:: !delete

.. _delete:

حذف 
------

``delete a`` یک مقدار اولیه را برای نوع، به  ``a`` اختصاص می‌دهد. یعنی برای اعداد صحیح معادل  ``a = 0``
است، اما همچنین می‌تواند در آرایه ها مورد استفاده قرار گیرد، جایی که یک آرایه پویا از طول صفر یا یک آرایه 
ایستا با همان طول را با تمام عناصر تنظیم شده روی مقدار اولیه خود اختصاص 
می‌دهد.  ``delete a[x]`` مورد را در شاخص  ``x`` آرایه حذف می‌کند و سایر عناصر و طول آرایه را دست 
نخورده باقی می‌گذارد. این کار به طور ویژه به این معنی است که در آرایه شکاف ایجاد می‌کند. اگر قصد حذف 
موارد را دارید، :ref:`mapping <mapping-types>` احتمالاً انتخاب بهتری است.

برای structها، یک struct را با تنظیم مجدد همه اعضا اختصاص می‌دهد. به عبارت دیگر، مقدار ``a``  پس از 
``delete a``  همانی است که اگر ``a``  بدون انتساب اعلام شود، با توجه به هشدار زیر:


``delete``  تأثیری در نگاشت ندارد (زیرا ممکن است کلیدهای نگاشت دلخواه باشند و به طور کلی ناشناخته باشند). 
بنابراین اگر یک struct را حذف کنید، همه اعضا را که نگاشت نباشند مجدد تنظیم می‌کند و همچنین 
به عضوها بازگشت می‌یابد مگر اینکه آنها نگاشت باشند. با این حال، کلیدهای جداگانه و به آنچه نگاشت می‌شوند 
می‌توانند حذف شوند: اگر ``a``  نگاشت باشد، سپس  ``delete a[x]`` مقدار ذخیره شده در  ``x`` را حذف خواهد کرد.


توجه به این نکته مهم است که ``delete a``  واقعاً مانند انتساب به ``a``  رفتار می‌کند، یعنی یک شی جدید 
را در  ``a``  ذخیره می‌کند. این تمایز زمانی قابل مشاهده‌است که ``a``  متغیر مرجع باشد: فقط یک  aرا خود 
مجدد تنظیم می‌کند نه مقداری که قبلاً به آن اشاره کرده بود.



.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract DeleteExample {
        uint data;
        uint[] dataArray;

        function f() public {
            uint x = data;
            delete x; // sets x to 0, does not affect data
            delete data; // sets data to 0, does not affect x
            uint[] storage y = dataArray;
            delete dataArray; // this sets dataArray.length to zero, but as uint[] is a complex object, also
            // y is affected which is an alias to the storage object
            // On the other hand: "delete y" is not valid, as assignments to local variables
            // referencing storage objects can only be made from existing storage objects.
            assert(y.length == 0);
        }
    }

.. index:: ! operator; precedence
.. _order:

Order of Precedence of Operators
--------------------------------

.. include:: types/operator-precedence-table.rst
