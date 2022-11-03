سالیدیتی
========
سالیدیتی   یک زبان شیء گرا  و سطح بالا  برای پیاده سازی قراردادهای هوشمند  می‌باشد. قرارداد‌های هوشمند، برنامه‌هایی هستند که رفتار حساب‌ها  در داخل حالت اتریوم  را کنترل می‌کنند.


<<<<<<< HEAD
سالیدیتی یک `زبان آکلادی <https://en.wikipedia.org/wiki/List_of_programming_languages_by_type#Curly-bracket_languages>`_ می‌باشد که از زبان‌هایی مانند سی پلاس پلاس ، پایتون و جاوا اسکریپت تأثیر گرفته و برای هدف قراردادن EVM  یا ماشینِ مجازیِ اتریوم  طراحی شده‌است.
=======
Solidity is a `curly-bracket language <https://en.wikipedia.org/wiki/List_of_programming_languages_by_type#Curly-bracket_languages>`_ designed to target the Ethereum Virtual Machine (EVM).
It is influenced by C++, Python and JavaScript. You can find more details about which languages Solidity has been inspired by in the :doc:`language influences <language-influences>` section.
>>>>>>> 46a7ebd5b66c5638427295b9c3dbf15fc48c4ed2


سالیدیتی از نوع استاتیک   می‌باشد. از ویژگی‌های ارث بری ، کتابخانه‌ها  و انواع نوع‌های پیچیده تعریف شده توسط کاربر  پشتیبانی می‌کند.

با سالیدیتی می‌توانید قراردادهایی را برای کاربردهایی از قبیل رأی‌گیری ، سرمایه گذاری جمعی ، مزایده کور  و کیف پول‌ با امضای چندگانه  استفاده کنید.

هنگام استقرار  قرارداد‌ها، باید از آخرین نسخه سالیدیتی منتشر شده استفاده کنید. به این دلیل که تغییرات جدید ، ویژگی‌های جدید  و رفع باگ‌ها  به طور منظم معرفی می‌شوند. ما در حال حاضر از نسخه 0.X برای `نشان دادن این تغییرات سریع <https://semver.org/#spec-item-4>`_ استفاده می‌کنیم.

<<<<<<< HEAD
=======
When deploying contracts, you should use the latest released
version of Solidity. Apart from exceptional cases, only the latest version receives
`security fixes <https://github.com/ethereum/solidity/security/policy#supported-versions>`_.
Furthermore, breaking changes as well as
new features are introduced regularly. We currently use
a 0.y.z version number `to indicate this fast pace of change <https://semver.org/#spec-item-4>`_.
>>>>>>> 46a7ebd5b66c5638427295b9c3dbf15fc48c4ed2

.. warning::

       سالیدیتی به تازگی نسخه 0.8.X را منتشر کرده که تغییرات جدید را معرفی می‌کند. حتماً :doc:`لیست کامل <080-breaking-changes>` را مطالعه کنید.

ایده‌های بهبود سالیدیتی یا این مستند همیشه مورد استقبال قرار میگیرد، برای جزئیات بیشتر :doc:`راهنمای همکاری <contributing>` را مطالعه کنید.

<<<<<<< HEAD

شروع
=======
.. Hint::

  You can download this documentation as PDF, HTML or Epub by clicking on the versions
  flyout menu in the bottom-left corner and selecting the preferred download format.


Getting Started
>>>>>>> 46a7ebd5b66c5638427295b9c3dbf15fc48c4ed2
---------------

**1. درک مبانی قراردادهای هوشمند**

اگر با مفهوم قراردادهای هوشمند آشنا هستید، به شما توصیه می‌کنیم که با جستجوی بخش "معرفی قراردادهای هوشمند" شروع به کار کنید، که شامل موارد زیر است:

* :ref:`یک مثال ساده از قرارداد هوشمند  <simple-smart-contract>` که با سالیدیتی نوشته شده‌است.
* :ref:`مبانی بلاکچین <blockchain-basics>`.
* :ref:`ماشین مجازی اتریوم <the-ethereum-virtual-machine>`.

**2.	آشنایی با سالیدیتی**

هنگامی که با مبانی اولیه آشنا شدید، توصیه می‌کنیم برای درک مفاهیم اصلی زبان، بخش‌های :doc:`"سالیدیتی با مثال" <solidity-by-example>`
و "شرح زبان" را بخوانید.

**3. 	نصب کامپایلر  سالیدیتی**

روش های مختلفی برای نصب کامپایلر سالیدیتی وجود دارد، به سادگی گزینه مورد نظر خود را انتخاب کنید و مراحل ذکر شده در :ref:`صفحه نصب <installing-solidity>` را دنبال کنید.



.. hint::

  می‌توانید نمونه‌های کد را مستقیماً در مرورگر خود با `ویرایشگر متن ریمیکس <https://remix.ethereum.org>`_ امتحان کنید. ریمیکس یک ویرایشگر متن مبتنی بر مرورگر وب است که به شما امکان می‌دهد، بدون نیاز به نصب سالیدیتی به صورت محلی، قراردادهای هوشمند سالیدیتی را بنویسید، دیپلوی و مدیریت کنید.
  
.. warning::
   
    نرم افزار به عنوان نوشته‌ی انسان، می‌تواند باگ داشته باشد. هنگام نوشتن قراردادهای هوشمند خود، باید بهترین شیوه‌های توسعه نرم افزار را دنبال کنید. شیوه‌های توسعه نرم افزار شامل بازبینی ، آزمایش ، حسابرسی  و اثبات صحتِ  کد می‌باشد. کاربران قرارداد هوشمند گاهی اوقات به خودِ کد نسبت به نویسندگان آن‌ها اطمینان بیشتری دارند. بلاکچین‌ها و قراردادهای هوشمند مسائل منحصر به فرد خود را دارند، که باید مراقب آنها باشید. بنابراین قبل از کار بر روی تولید کد، حتماً قسمت  :ref:`ملاحظات امنیتی <security_considerations>` را مطالعه کنید.
 

**4.	یادگیری بیشتر**

اگر می‌خواهید در مورد ساخت برنامه‌های غیرمتمرکز در اتریوم اطلاعات بیشتری کسب کنید، `منابع توسعه دهنده اتریوم <https://ethereum.org/en/developers/>`_ می‌توانند به شما در تهیه مستندِ عمومیِ بیشتر در مورد اتریوم و انتخاب گسترده‌ای از آموزش‌ها، ابزارها و چارچوب‌های توسعه کمک کنند.

 اگر سؤالی دارید، می‌توانید جواب‌ها را جستجو کنید یا از طریق `Ethereum StackExchange <https://ethereum.stackexchange.com/>`_, یا `کانال Gitter <https://gitter.im/ethereum/solidity/>`_  ما بپرسید.

.. _translations:

ترجمه‌ها
------------

<<<<<<< HEAD
داوطلبان جامعه سالیدیتی به ترجمه این مستندات به چندین زبان کمک می‌کنند. این‌ مستندات سطوح مختلفی از کامل و بروز بودن را دارند. نسخه انگلیسی به عنوان مرجع می‌باشد.
=======
Community contributors help translate this documentation into several languages.
Note that they have varying degrees of completeness and up-to-dateness. The English
version stands as a reference.
>>>>>>> 46a7ebd5b66c5638427295b9c3dbf15fc48c4ed2

You can switch between languages by clicking on the flyout menu in the bottom-left corner
and selecting the preferred language.

* `Chinese <https://github.com/solidity-docs/zh-cn-chinese/>`_
* `French <https://docs.soliditylang.org/fr/latest/>`_
* `Indonesian <https://github.com/solidity-docs/id-indonesian>`_
* `Japanese <https://github.com/solidity-docs/ja-japanese>`_
* `Korean <https://github.com/solidity-docs/ko-korean>`_
* `Persian <https://github.com/solidity-docs/fa-persian>`_
* `Russian <https://github.com/solidity-docs/ru-russian>`_
* `Spanish <https://github.com/solidity-docs/es-spanish>`_
* `Turkish <https://github.com/solidity-docs/tr-turkish>`_

.. note::

<<<<<<< HEAD
   
   We recently set up a new GitHub organization and translation workflow to help streamline the
   community efforts. Please refer to the `translation guide <https://github.com/solidity-docs/translation-guide>`_
   for information on how to contribute to the community translations moving forward.

* `فرانسوی <https://solidity-fr.readthedocs.io>`_ (در حال انجام)
* `ایتالیایی <https://github.com/damianoazzolini/solidity>`_ (در حال انجام)
* `ژاپنی <https://solidity-jp.readthedocs.io>`_
* `کره‌ای <https://solidity-kr.readthedocs.io>`_ (در حال انجام)
* `روسی <https://github.com/ethereum/wiki/wiki/%5BRussian%5D-%D0%A0%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE-%D0%BF%D0%BE-Solidity>`_ (نسبتاً قدیمی)
* `چینی <https://learnblockchain.cn/docs/solidity/>`_ (در حال انجام)
* `اسپانیایی <https://solidity-es.readthedocs.io>`_
* `ترکی <https://github.com/denizozzgur/Solidity_TR/blob/master/README.md>`_ (جزئی)
=======
   We set up a GitHub organization and translation workflow to help streamline the
   community efforts. Please refer to the translation guide in the `solidity-docs org <https://github.com/solidity-docs>`_
   for information on how to start a new language or contribute to the community translations.
>>>>>>> 46a7ebd5b66c5638427295b9c3dbf15fc48c4ed2

فهرست
========

:ref:`فهرست کلمات <genindex>`, :ref:`صفحه جستجو <search>`

.. toctree::
   :maxdepth: 2
   :caption: مبانی

   introduction-to-smart-contracts.rst
   installing-solidity.rst
   solidity-by-example.rst

.. toctree::
   :maxdepth: 2
   :caption: توضیحات زبان

   layout-of-source-files.rst
   structure-of-a-contract.rst
   types.rst
   units-and-global-variables.rst
   control-structures.rst
   contracts.rst
   assembly.rst
   cheatsheet.rst
   grammar.rst

.. toctree::
   :maxdepth: 2
   :caption: کامپایلر

   using-the-compiler.rst
   analysing-compilation-output.rst
   ir-breaking-changes.rst

.. toctree::
   :maxdepth: 2
   :caption: Internals

   internals/layout_in_storage.rst
   internals/layout_in_memory.rst
   internals/layout_in_calldata.rst
   internals/variable_cleanup.rst
   internals/source_mappings.rst
   internals/optimizer.rst
   metadata.rst
   abi-spec.rst

.. toctree::
   :maxdepth: 2
   :caption: Additional Material

   050-breaking-changes.rst
   060-breaking-changes.rst
   070-breaking-changes.rst
   080-breaking-changes.rst
   natspec-format.rst
   security-considerations.rst
   smtchecker.rst
   resources.rst
   path-resolution.rst
   yul.rst
   style-guide.rst
   common-patterns.rst
   bugs.rst
   contributing.rst
   brand-guide.rst
   language-influences.rst
