.. index:: ! contract;abstract, ! abstract contract

.. _abstract-contract:

******************
قراردادهای انتزاعی (Abstract Contracts)
******************

زمانی که حداقل یکی از توابع قرارداد‌ها اجرا نشود، باید قراردادها به صورت انتزاعی علامت گذاری شوند. ممکن 
است قراردادها بصورت انتزاعی علامت گذاری شوند حتی اگر همه توابع اجرا شوند.


این را می‌توان با استفاده از کلمه کلیدی ``abstract`` همانطور که در مثال زیر نشان داده شده است، انجام داد. 
توجه داشته باشید که این قرارداد باید بصورت انتزاعی تعریف شود، زیرا تابع ``()utterance`` تعریف شده 
است، اما پیاده سازی ارائه نشده است (هیچ پیاده سازی ``{ }``   body داده نشده است):


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    abstract contract Feline {
        function utterance() public virtual returns (bytes32);
    }

چنین قراردادهای انتزاعی را نمی‌توان مستقیماً به عنوان نمونه معرفی کرد. اگر یک قرارداد انتزاعی خود تمام توابع 
تعریف شده را اجرا کند، این نیز صادق است. در مثال زیر استفاده از یک قرارداد انتزاعی به عنوان کلاس پایه 
نشان داده شده است:


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.6.0 <0.9.0;

    abstract contract Feline {
        function utterance() public pure virtual returns (bytes32);
    }

    contract Cat is Feline {
        function utterance() public pure override returns (bytes32) { return "miaow"; }
    }

اگر یک قرارداد از یک قرارداد انتزاعی ارث برده‌ باشد و تمام توابع اجرا نشده را با overriding اجرا ‌نکند، باید به 
عنوان انتزاعی نیز مشخص شود.


توجه داشته باشید که یک تابع بدون اجرا با :ref:`Function Type <function_types>` متفاوت است، اگرچه سینتکس آنها بسیار شبیه به هم باشد.





مثال تابع بدون اجرا (اعلان تابع):



.. code-block:: solidity

    function foo(address) external returns (address);

مثال اعلان متغیری که نوع آن تابع است:


.. code-block:: solidity

    function(address) external returns (address) foo;

قراردادهای انتزاعی تعریف یک قرارداد را از اجرای آن جدا می‌کنند که باعث توسعه و مستندسازی بهتر و تسهیل 
الگوهایی مانند  `Template method <https://en.wikipedia.org/wiki/Template_method_pattern>`_  و حذف موارد تکراری می‌شود. قراردادهای انتزاعی به همان شیوه‌ای 
مفید هستند که تعریف method‌ها در یک رابط مفید است. این شیوه‌ای است که طراحِ قراردادِ انتزاعی می‌گوید 
"هر فرزند من باید این روش را اجرا کند".



.. note::

قراردادهای انتزاعی نمی‌توانند یک تابع مجازی پیاده سازی شده را با یک تابع پیاده سازی نشده override کنند.

