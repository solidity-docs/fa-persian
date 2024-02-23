.. index:: ! installing

.. _installing-solidity:

################################
نصب کامپایلر سالیدیتی
################################

نسخه بندی 
==========

<<<<<<< HEAD
نسخه‌های سالیدیتی از `نسخه بندی سمننتیکی <https://semver.org>`_  پیروی می‌کنند و علاوه بر این نسخه‌ها، **نسخه‌های توسعه شبانه**  نیز در دسترس هستند. کارکردن نسخه‌های نسخه شبانه تضمین نمی‌شود و علی‌رغم همه تلاش‌ها ممکن است تغییرات غیرمستند و یا تغییرات جدید  را شامل شوند. توصیه می‌کنیم از آخرین نسخه استفاده کنید. بسته‌های نصبی زیر از آخرین نسخه استفاده خواهند کرد.
=======
Solidity versions follow `Semantic Versioning <https://semver.org>`_. In
addition, patch-level releases with major release 0 (i.e. 0.x.y) will not
contain breaking changes. That means code that compiles with version 0.x.y
can be expected to compile with 0.x.z where z > y.

In addition to releases, we provide **nightly development builds** to make
it easy for developers to try out upcoming features and
provide early feedback. Note, however, that while the nightly builds are usually
very stable, they contain bleeding-edge code from the development branch and are
not guaranteed to be always working. Despite our best efforts, they might
contain undocumented and/or broken changes that will not become a part of an
actual release. They are not meant for production use.

When deploying contracts, you should use the latest released version of Solidity. This
is because breaking changes, as well as new features and bug fixes are introduced regularly.
We currently use a 0.x version number `to indicate this fast pace of change <https://semver.org/#spec-item-4>`_.
>>>>>>> english/develop

ریمیکس
=====

*ریمیکس را برای قراردادهای کوچک و برای یادگیری سریع سالیدیتی توصیه می‌کنیم. *

<<<<<<< HEAD
می‌توانید به صورت آنلاین به `ریمیکس <https://remix.ethereum.org/>`_  دسترسی داشته باشید و نیازی به نصب هیچ چیزی ندارید. اگر می‌خواهید بدون اتصال به اینترنت از آن استفاده کنید، به  https://github.com/ethereum/remix-live/tree/gh-pages مراجعه کنید و فایل  ``zip.``  را همانطور که در آن صفحه توضیح داده شده بارگیری کنید. ریمیکس همچنین یک گزینه مناسب برای تست نسخه شبانه بدون نصب چندین نسخه سالیدیتی است.

این صفحه گزینه‌های بیشتر برای نصب نرم افزار کامپایلر سالیدیتی  روی رایانه شما با خط فرمان را توضیح می‌دهد. اگر در حال کار بر روی قرارداد بزرگتر هستید یا به گزینه‌های کامپایل بیشتری نیاز دارید، یک کامپایلر خط فرمان  انتخاب کنید.
=======
`Access Remix online <https://remix.ethereum.org/>`_, you do not need to install anything.
If you want to use it without connection to the Internet, go to
https://github.com/ethereum/remix-live/tree/gh-pages#readme and follow the instructions on that page.
Remix is also a convenient option for testing nightly builds
without installing multiple Solidity versions.

Further options on this page detail installing command-line Solidity compiler software
on your computer. Choose a command-line compiler if you are working on a larger contract
or if you require more compilation options.
>>>>>>> english/develop

.. _solcjs:

npm / Node.js
=============

برای نصب راحت و قابل حمل ``solcjs`` یک کامپایلر سالیدیتی، از ``npm``  استفاده کنید. برنامه `solcjs`  دارای ویژگی‌های  کمتری نسبت به راه‌های دسترسی به کامپایلر است که در این صفحه توضیح داده شده است. مستندات  :ref:`commandline-compiler`  فرض می‌کند که از کامپایلر کامل  ``solc`` استفاده می‌کنید. استفاده از  ``solcjs``   در `مخزن <https://github.com/ethereum/solc-js>`_ خود ثبت شده است.

<<<<<<< HEAD
توجه داشته باشید: پروژه solc-js از C++ `solc` با استفاده از Emscripten مشتق گرفته شده‌است، به این معنی که هر دو از کد منبع کامپایلر یکسانی استفاده می‌کنند. `solc-js`  را می‌توان مستقیماً در پروژه‌های جاوا اسکریپت (مانند ریمیکس) استفاده کرد. لطفاً به مخزن solc-js برای مشاهده دستورات مراجعه کنید.
=======
Note: The solc-js project is derived from the C++
`solc` by using Emscripten, which means that both use the same compiler source code.
`solc-js` can be used in JavaScript projects directly (such as Remix).
Please refer to the solc-js repository for instructions.
>>>>>>> english/develop

.. code-block:: bash

    npm install -g solc

.. note::

<<<<<<< HEAD
  خط فرمان اجرایی  ``solcjs`` نام دارد.

 گزینه‌های خط فرمان  ``solcjs`` با  ``solc`` سازگار نیستند و ابزارها (مانند  ``geth``) انتظار دارند رفتار  ``solc`` با  ``solcjs`` کار نکند.
=======
    The command-line executable is named ``solcjs``.

    The command-line options of ``solcjs`` are not compatible with ``solc`` and tools (such as ``geth``)
    expecting the behavior of ``solc`` will not work with ``solcjs``.
>>>>>>> english/develop

داکر
======

<<<<<<< HEAD
تصاویر داکر  از نسخه‌های سالیدیتی با استفاده از تصویر ``solc``   از سازمان ``ethereum``  در دسترس است. از برچسب  ``stable``  برای آخرین نسخه منتشر شده و nightly  برای تغییرات احتمالی ناپایدار در شاخه‌ی نسخه استفاده می‌کند.

تصویر داکر کامپایلر اجرایی را اجرا می‌کند، بنابراین می‌توانید تمام آرگومان‌های کامپایلر را به آن منتقل کنید. به عنوان مثال، دستور زیر نسخه پایدار  تصویر  ``solc`` را دریافت می‌کند (اگر قبلاً آن را ندارید)، و آن را در یک محفظه جدید اجرا می‌کند و آرگومان ``help--`` را عبور می‌دهد.
=======
Docker images of Solidity builds are available using the ``solc`` image from the ``ethereum`` organization.
Use the ``stable`` tag for the latest released version, and ``nightly`` for potentially unstable changes in the ``develop`` branch.

The Docker image runs the compiler executable so that you can pass all compiler arguments to it.
For example, the command below pulls the stable version of the ``solc`` image (if you do not have it already),
and runs it in a new container, passing the ``--help`` argument.
>>>>>>> english/develop

.. code-block:: bash

    docker run ethereum/solc:stable --help

<<<<<<< HEAD
همچنین می‌توانید نسخه‌هایِ توسعهِ منتشر شده را با برچسب مشخص کنید، به عنوان مثال، برای نسخه 0.5.4.
=======
For example, You can specify release build versions in the tag for the 0.5.4 release.
>>>>>>> english/develop

.. code-block:: bash

    docker run ethereum/solc:0.5.4 --help

<<<<<<< HEAD
برای استفاده از تصویر داکر برای کامپایل فایل‌های سالیدیتی در دستگاه میزبان، یک پوشه محلی برای ورودی و خروجی نصب کرده و قرارداد کامپایل را مشخص کنید. برای مثال:
=======
To use the Docker image to compile Solidity files on the host machine, mount a
local folder for input and output, and specify the contract to compile. For example.
>>>>>>> english/develop

.. code-block:: bash

    docker run -v /local/path:/sources ethereum/solc:stable -o /sources/output --abi --bin /sources/Contract.sol

You can also use the standard JSON interface (which is recommended when using the compiler with tooling).
When using this interface, it is not necessary to mount any directories as long as the JSON input is
self-contained (i.e. it does not refer to any external files that would have to be
:ref:`loaded by the import callback <initial-vfs-content-standard-json-with-import-callback>`).

.. code-block:: bash

    docker run ethereum/solc:stable --standard-json < input.json > output.json

بسته‌های لینوکس
==============

بسته‌های باینری سالیدیتی در مخزن گیتهاب  `سالیدیتی/ انتشارات   <https://github.com/ethereum/solidity/releases>`_  موجود است.

ما همچنین PPAs برای اوبونتو  داریم، می‌توانید آخرین نسخه پایدار  را با استفاده از دستورات زیر دریافت کنید:

.. code-block:: bash

    sudo add-apt-repository ppa:ethereum/ethereum
    sudo apt-get update
    sudo apt-get install solc

نسخه شبانه را می‌توان با استفاده از این دستورات زیر نصب کرد:

.. code-block:: bash

    sudo add-apt-repository ppa:ethereum/ethereum
    sudo add-apt-repository ppa:ethereum/ethereum-dev
    sudo apt-get update
    sudo apt-get install solc

<<<<<<< HEAD
ما همچنین یک `بسته اسنپ <https://snapcraft.io/>`_  را منتشر می‌کنیم که در `توزیع‌های پشتیبانی شده لینوکس <https://snapcraft.io/docs/core/install>`_  قابل نصب است. برای نصب آخرین نسخه پایدار solc:
=======
Furthermore, some Linux distributions provide their own packages. These packages are not directly
maintained by us but usually kept up-to-date by the respective package maintainers.

For example, Arch Linux has packages for the latest development version as AUR packages: `solidity <https://aur.archlinux.org/packages/solidity>`_
and `solidity-bin <https://aur.archlinux.org/packages/solidity-bin>`_.

.. note::

    Please be aware that `AUR <https://wiki.archlinux.org/title/Arch_User_Repository>`_ packages
    are user-produced content and unofficial packages. Exercise caution when using them.

There is also a `snap package <https://snapcraft.io/solc>`_, however, it is **currently unmaintained**.
It is installable in all the `supported Linux distros <https://snapcraft.io/docs/core/install>`_. To
install the latest stable version of solc:
>>>>>>> english/develop

.. code-block:: bash

    sudo snap install solc

اگر می‌خواهید آخرین نسخه توسعه دهنده سالیدیتی را با جدیدترین تغییرات آزمایش کنید، لطفاً از موارد زیر استفاده کنید:

.. code-block:: bash

    sudo snap install solc --edge

.. note::

   اسنپ  ``solc`` از سختگیری شدید استفاده می‌کند. این حالت امن‌ترین حالت برای بسته‌های فوری است اما با محدودیت‌هایی همراه است، مثلاً شما فقط به فایل‌های موجود در دایرکتوری‌های ``home/``   و  ``media/``  دسترسی خواهید داشت. برای کسب اطلاعات بیشتر، به `Demystifying Snap Confinement <https://snapcraft.io/blog/demystifying-snap-confinement>`_.  مراجعه کنید.

<<<<<<< HEAD
آرک لینوکس  همچنین دارای بسته‌هایی است، البته محدود به آخرین نسخه توسعه می‌باشد:

.. code-block:: bash

    pacman -S solidity

جنتو لینوکس  دارای `همپوشانی اتریوم <https://overlays.gentoo.org/#ethereum>`_   می‌باشد که حاوی بسته سالیدیتی است. پس از راه اندازی این همپوشانی ،   ``solc`` را می‌توان در معماری x86_64 توسط فرمان زیر نصب کرد:

.. code-block:: bash

    emerge dev-lang/solidity
=======
>>>>>>> english/develop

بسته‌های مک‌او اس
==============

ما کامپایلر سالیدیتی را از طریق هوم‌برو  به عنوان نسخهِ ساخته شده از منبع  توزیع می‌کنیم. مخزن‌های از پیش ساخته شده در حال حاضر پشتیبانی نمی‌شوند.

.. code-block:: bash

    brew update
    brew upgrade
    brew tap ethereum/ethereum
    brew install solidity

برای نصب جدیدترین نسخه سالیدیتی 0.4.x / 0.5.x می‌توانید به ترتیب از ``brew install solidity@4`` و  ``brew install solidity@5``  استفاده کنید.

اگر به نسخه خاصی از سالیدیتی نیاز دارید، می‌توانید فرمول هوم‌برو  را مستقیماً از گیتهاب نصب کنید.

لینک  `solidity.rb commits on Github <https://github.com/ethereum/homebrew-ethereum/commits/master/solidity.rb>`_  را مشاهده کنید.

هشِ کامیت‌های  نسخه موردنظر خود را کپی کرده و در دستگاه خود بررسی کنید.

.. code-block:: bash

    git clone https://github.com/ethereum/homebrew-ethereum.git
    cd homebrew-ethereum
    git checkout <your-hash-goes-here>

با استفاده از  ``brew`` آن را نصب کنید:

.. code-block:: bash

    brew unlink solidity
    # eg. Install 0.4.8
    brew install solidity.rb

باینری‌های استاتیک
===============

ما یک مخزن  حاوی نسخه‌های استاتیک  از نسخه‌های کامپایلر قبلی و فعلی را برای همه پلتفرم‌های پشتیبانی شده در `solc-bin`_  نگهداری می‌کنیم. این مکان همچنین مکانی است که می‌توانید نسخه‌های شبانه  را در آن پیدا کنید.

مخزن نه تنها راهی سریع و آسان برای کاربران نهایی است تا باینری‌ها را برای استفاده در خارج از جعبه   آماده کنند، بلکه به معنای مناسب بودن با ابزارهای ثالث است:

<<<<<<< HEAD
- محتوا در https://binaries.soliditylang.org قرارداده شده‌است که در آن می‌توان به راحتی از طریق HTTPS بدون احراز هویت، محدودیت سرعت یا نیاز به استفاده از git بارگیری کرد.
- محتوا با هدرهای صحیح `نوع محتوا`  و  با پیکربندی CORS ارائه می‌شود تا بتواند مستقیماً توسط ابزارهایی که در مرورگر اجرا می‌شوند بارگیری شود.
- فایل‌های باینری نیازی به نصب یا باز کردن بسته بندی ندارند (به استثنای نسخه‌های قدیمی ویندوز که همراه با DLLهای ضروری هستند).
- ما برای سطح بالایی از سازگاری با گذشته  تلاش می‌کنیم. فایل‌ها، پس از اضافه شدن، بدون ارائه پیوند  تغییر مسیر در مکان قدیمی حذف یا منتقل نمی‌شوند. همچنین هرگز در محل خود تغییر داده نمی‌شوند و همیشه باید با چک‌سام  اصلی مطابقت داشته باشند. تنها استثناء‌ می‌تواند فایل‌های شکسته یا غیرقابل استفاده باشد که اگر به همین صورت باقی بمانند، می‌توانند بیشتر از فایده باعث آسیب شوند.
- •	فایل‌ها از طریق HTTP و HTTPS ارائه می‌شوند. تا زمانیکه لیست فایل‌ها را به صورت ایمن (از طریق git ،HTTPS ، IPFS یا به صورت محلی ذخیره کردید) به دست آوردید. و پس از بارگیری، فایل‌های هش باینری را تأیید کنید. لازم نیست از HTTPS برای خود فایل‌های باینری استفاده کنید.
=======
- The content is mirrored to https://binaries.soliditylang.org where it can be easily downloaded over
  HTTPS without any authentication, rate limiting or the need to use git.
- Content is served with correct `Content-Type` headers and lenient CORS configuration so that it
  can be directly loaded by tools running in the browser.
- Binaries do not require installation or unpacking (exception for older Windows builds
  bundled with necessary DLLs).
- We strive for a high level of backward-compatibility. Files, once added, are not removed or moved
  without providing a symlink/redirect at the old location. They are also never modified
  in place and should always match the original checksum. The only exception would be broken or
  unusable files with the potential to cause more harm than good if left as is.
- Files are served over both HTTP and HTTPS. As long as you obtain the file list in a secure way
  (via git, HTTPS, IPFS or just have it cached locally) and verify hashes of the binaries
  after downloading them, you do not have to use HTTPS for the binaries themselves.
>>>>>>> english/develop

همین فایل‌های باینری در بیشتر موارد در `Solidity release page on Github`_  در گیت‌هاب موجود است. تفاوت در این است که ما به طور کلی نسخه‌های قدیمی را در صفحه انتشار گیتهاب به روز نمی‌کنیم. این بدان معناست که در صورت تغییر شرایط نامگذاری، نام آن‌ها را تغییر نمی‌دهیم و برای پلتفرم‌هایی که در زمان انتشار پشتیبانی نمی‌شوند، نسخه‌‌هایی اضافه نمی‌کنیم. این امر فقط در  ``solc-bin`` اتفاق می‌افتد.

<<<<<<< HEAD
مخزن  ``solc-bin`` شامل چندین دایرکتوری سطح بالا است که هر یک نمایانگر یک پلتفرم واحد می‌باشد. هر یک شامل یک فایل  ``list.json``  است که فایل‌های باینری موجود را فهرست می‌کند. برای مثال در  ``emscripten-wasm32/list.json``  اطلاعات زیر را در مورد نسخه 0.7.4 خواهید یافت:
=======
The ``solc-bin`` repository contains several top-level directories, each representing a single platform.
Each one includes a ``list.json`` file listing the available binaries. For example in
``emscripten-wasm32/list.json`` you will find the following information about version 0.7.4:
>>>>>>> english/develop

.. code-block:: json

    {
      "path": "solc-emscripten-wasm32-v0.7.4+commit.3f05b770.js",
      "version": "0.7.4",
      "build": "commit.3f05b770",
      "longVersion": "0.7.4+commit.3f05b770",
      "keccak256": "0x300330ecd127756b824aa13e843cb1f43c473cb22eaf3750d5fb9c99279af8c3",
      "sha256": "0x2b55ed5fec4d9625b6c7b3ab1abd2b7fb7dd2a9c68543bf0323db2c7e2d55af2",
      "urls": [
        "bzzr://16c5f09109c793db99fe35f037c6092b061bd39260ee7a677c8a97f18c955ab1",
        "dweb:/ipfs/QmTLs5MuLEWXQkths41HiACoXDiH8zxyqBHGFDRSzVE5CS"
      ]
    }

این بدان معناست که:

<<<<<<< HEAD
- شما می‌توانید باینری را در همان فهرست با نام   `solc-emscripten-wasm32-v0.7.4+commit.3f05b770.js <https://github.com/ethereum/solc-bin/blob/gh-pages/emscripten-wasm32/solc-emscripten-wasm32-v0.7.4+commit.3f05b770.js>`_  پیدا کنید. توجه داشته باشید که فایل ممکن است یک پیوند  باشد و اگر از گیت  برای بارگیری آن استفاده نمی‌کنید یا سیستم فایل شما از پیوند‌ها  پشتیبانی نمی‌کند، باید خودتان آن را حل کنید.
- باینری نیز در https://binaries.soliditylang.org/emscripten-wasm32/solc-emscripten-wasm32-v0.7.4+commit.3f05b770.js قرار داده شده‌است. در این حالت گیت ضروری نمی‌باشد. و پیوندها، یا با ارائه یک کپی از فایل یا با بازگرداندن یک مسیر HTTP به طور شفاف حل  می‌شوند.
- فایل همچنین در IPFS در `QmTLs5MuLEWXQkths41HiACoXDiH8zxyqBHGFDRSzVE5CS`_ موجود است.
- ممکن است فایل در آینده در Swarm با شماره `16c5f09109c793db99fe35f037c6092b061bd39260ee7a677c8a97f18c955ab1`_ موجود باشد.
- با مقایسه هش keccak256 آن با ``0x300330ecd127756b824aa13e843cb1f43c473cb22eaf3750d5fb9c99279af8c3``  می‌توانید صحت باینری را بررسی کنید. هش را می‌توان در خط فرمان با استفاده از ابزار  ``keccak256sum`` محاسبه کرد که توسط تابع `sha3sum`_  یا  `keccak256() function
  from ethereumjs-util`_ در جاوا اسکریپت ارائه شده‌است.

- همچنین می‌توانید یکپارچگی باینری را با مقایسه هش sha256 آن با ``0x2b55ed5fec4d9625b6c7b3ab1abd2b7fb7dd2a9c68543bf0323db2c7e2d55af2`` تأیید کنید.
=======
- You can find the binary in the same directory under the name
  `solc-emscripten-wasm32-v0.7.4+commit.3f05b770.js <https://github.com/ethereum/solc-bin/blob/gh-pages/emscripten-wasm32/solc-emscripten-wasm32-v0.7.4+commit.3f05b770.js>`_.
  Note that the file might be a symlink, and you will need to resolve it yourself if you are not using
  git to download it or your file system does not support symlinks.
- The binary is also mirrored at https://binaries.soliditylang.org/emscripten-wasm32/solc-emscripten-wasm32-v0.7.4+commit.3f05b770.js.
  In this case git is not necessary and symlinks are resolved transparently, either by serving a copy
  of the file or returning a HTTP redirect.
- The file is also available on IPFS at `QmTLs5MuLEWXQkths41HiACoXDiH8zxyqBHGFDRSzVE5CS`_.
- The file might in future be available on Swarm at `16c5f09109c793db99fe35f037c6092b061bd39260ee7a677c8a97f18c955ab1`_.
- You can verify the integrity of the binary by comparing its keccak256 hash to
  ``0x300330ecd127756b824aa13e843cb1f43c473cb22eaf3750d5fb9c99279af8c3``.  The hash can be computed
  on the command-line using ``keccak256sum`` utility provided by `sha3sum`_ or `keccak256() function
  from ethereumjs-util`_ in JavaScript.
- You can also verify the integrity of the binary by comparing its sha256 hash to
  ``0x2b55ed5fec4d9625b6c7b3ab1abd2b7fb7dd2a9c68543bf0323db2c7e2d55af2``.
>>>>>>> english/develop

.. warning::

  به دلیل نیاز به سازگاری زیاد، مخزن حاوی برخی از عناصر قدیمی می‌باشد، اما هنگام نوشتن ابزارهای جدید نباید از آنها استفاده کنید:

   - اگر می‌خواهید بهترین عملکرد را داشته باشید، از ``/emscripten-wasm32``  (با جایگزینی برای  ``/emscripten-asmjs``) به جای  ``/bin``  استفاده کنید. ما تا نسخه 0.6.1 فقط فایل‌های باینری asm.js را ارائه می‌دادیم. با شروع 0.6.2، ما به  `WebAssembly builds`_  به عملکرد بسیار بهتر روی آوردیم. ما نسخه‌های قدیمی تر را برای wasm بازسازی کرده‌ایم اما فایل‌های اصلی asm.js در ``bin/``  باقی می‌مانند. موارد جدید باید در یک فهرست جداگانه قرار داده شوند تا از تصادم نامی جلوگیری شود.
   - اگر می‌خواهید مطمئن شوید که در حال بارگیری wasm یا باینری asm.js هستید،  از  ``/emscripten-asmjs``  و  ``/emscripten-wasm32``  به جای  ``/bin``  و  ``/wasm``  استفاده کنید.
   - به جای  ``list.js`` و  ``list.txt`` از ``list.json``  استفاده کنید. فرمت لیست JSON شامل تمام اطلاعات قدیمی و بیشتر است.
   - به جای  https://binaries.soliditylang.org از  https://solc-bin.ethereum.org استفاده کنید. برای ساده نگه داشتن مسائل، ما تقریباً همه چیز مربوط به کامپایلر را تحت دامنه جدید   ``soliditylang.org`` منتقل کردیم و این امر در مورد ``solc-bin``  نیز صدق می‌کند. با اینکه دامنه جدید توصیه می‌شود، اما دامنه قبلی هنوز کاملاً پشتیبانی می‌شود و تضمین می‌شود که به همان مکان اشاره می‌کند.

.. warning::

    فایل‌های باینری نیز در https://ethereum.github.io/solc-bin/ در دسترس هستند، اما این صفحه به روز رسانی خود را پس از انتشار نسخه 0.7.2 متوقف کرد، هیچ نسخه جدید یا نسخه شبانه برای هر پلتفرمی دریافت نمی‌کند و ساختار دایرکتوری جدید، از جمله ساختارهای غیر emscripten را ارائه نمی‌دهد.
    
    اگر از آن استفاده می‌کنید، لطفاً به  https://binaries.soliditylang.org مراجعه کنید، که یک جایگزینی رها کردن  است. این به ما امکان می‌دهد تا به طور شفاف در میزبانی اصلی تغییراتی ایجاد کرده و اختلال را به حداقل برسانیم. بر خلاف دامنه  ``ethereum.github.io``  ، که ما هیچ کنترلی بر آن نداریم، کار کردن  ``binaries.soliditylang.org``   تضمین می‌شود و در دراز مدت همان URL را حفظ کند.

.. _IPFS: https://ipfs.io
.. _Swarm: https://swarm-gateways.net/bzz:/swarm.eth
.. _solc-bin: https://github.com/ethereum/solc-bin/
.. _Solidity release page on github: https://github.com/ethereum/solidity/releases
.. _sha3sum: https://github.com/maandree/sha3sum
.. _keccak256() function from ethereumjs-util: https://github.com/ethereumjs/ethereumjs-util/blob/master/docs/modules/_hash_.md#const-keccak256
.. _WebAssembly builds: https://emscripten.org/docs/compiling/WebAssembly.html
.. _QmTLs5MuLEWXQkths41HiACoXDiH8zxyqBHGFDRSzVE5CS: https://gateway.ipfs.io/ipfs/QmTLs5MuLEWXQkths41HiACoXDiH8zxyqBHGFDRSzVE5CS
.. _16c5f09109c793db99fe35f037c6092b061bd39260ee7a677c8a97f18c955ab1: https://swarm-gateways.net/bzz:/16c5f09109c793db99fe35f037c6092b061bd39260ee7a677c8a97f18c955ab1/

.. _building-from-source:

نسخه از منبع
====================
<<<<<<< HEAD

پیش نیازهای – همه‌ی سیستم عامل‌ها
=======
Prerequisites - All Operating Systems
>>>>>>> english/develop
-------------------------------------

موارد زیر وابستگی‌ها  برای همه نسخه‌های سالیدیتی  می‌باشند:

+-----------------------------------+-------------------------------------------------------+
| Software                          | Notes                                                 |
+===================================+=======================================================+
| `CMake`_ (version 3.21.3+ on      | Cross-platform build file generator.                  |
| Windows, 3.13+ otherwise)         |                                                       |
+-----------------------------------+-------------------------------------------------------+
| `Boost`_ (version 1.77+ on        | C++ libraries.                                        |
| Windows, 1.65+ otherwise)         |                                                       |
+-----------------------------------+-------------------------------------------------------+
| `Git`_                            | Command-line tool for retrieving source code.         |
+-----------------------------------+-------------------------------------------------------+
| `z3`_ (version 4.8.16+, Optional) | For use with SMT checker.                             |
+-----------------------------------+-------------------------------------------------------+
| `cvc4`_ (Optional)                | For use with SMT checker.                             |
+-----------------------------------+-------------------------------------------------------+

.. _cvc4: https://cvc4.cs.stanford.edu/web/
.. _Git: https://git-scm.com/download
.. _Boost: https://www.boost.org
.. _CMake: https://cmake.org/download/
.. _z3: https://github.com/Z3Prover/z3

.. note::
<<<<<<< HEAD
    نسخه‌های سالیدیتی قبل از 0.5.10 نمی‌توانند به درستی با نسخه‌های   +Boost 1.70 لینک شوند. یک راه حل ممکن این است که قبل از اجرای دستور cmake برای پیکربندی سالیدیتی، نام  
    ``Boost install path>/lib/cmake/Boost-1.70.0>`` 
    را به طور موقت تغییر نام دهید.

    با شروع از 0.5.10 لینک کردن برخلاف  +Boost 1.70 باید بدون دخالت دستی کار کند.

=======
    Solidity versions prior to 0.5.10 can fail to correctly link against Boost versions 1.70+.
    A possible workaround is to temporarily rename ``<Boost install path>/lib/cmake/Boost-1.70.0``
    prior to running the cmake command to configure Solidity.
>>>>>>> english/develop


.. note::
    The default build configuration requires a specific Z3 version (the latest one at the time the
    code was last updated). Changes introduced between Z3 releases often result in slightly different
    (but still valid) results being returned. Our SMT tests do not account for these differences and
    will likely fail with a different version than the one they were written for. This does not mean
    that a build using a different version is faulty. If you pass ``-DSTRICT_Z3_VERSION=OFF`` option
    to CMake, you can build with any version that satisfies the requirement given in the table above.
    If you do this, however, please remember to pass the ``--no-smt`` option to ``scripts/tests.sh``
    to skip the SMT tests.

<<<<<<< HEAD
حداقل نسخه‌های کامپایلر
=======
.. note::
    By default the build is performed in *pedantic mode*, which enables extra warnings and tells the
    compiler to treat all warnings as errors.
    This forces developers to fix warnings as they arise, so they do not accumulate "to be fixed later".
    If you are only interested in creating a release build and do not intend to modify the source code
    to deal with such warnings, you can pass ``-DPEDANTIC=OFF`` option to CMake to disable this mode.
    Doing this is not recommended for general use but may be necessary when using a toolchain we are
    not testing with or trying to build an older version with newer tools.
    If you encounter such warnings, please consider
    `reporting them <https://github.com/ethereum/solidity/issues/new>`_.

Minimum Compiler Versions
>>>>>>> english/develop
^^^^^^^^^^^^^^^^^^^^^^^^^

کامپایلرهای ++ C زیر و حداقل نسخه‌های آنها می‌توانند پایگاه کد  سالیدیتی را ایجاد کنند:

- `GCC+ <https://gcc.gnu.org>`_,  ، نسخه 8
- `Clang <https://clang.llvm.org/>`_, version 7+

- `MSVC <https://visualstudio.microsoft.com/vs/>`_, version 2019+


پیش نیازها - مک‌او اس 
---------------------

<<<<<<< HEAD
برای نسخه‌های  مک‌او اس، مطمئن شوید که آخرین نسخه `Xcode <https://developer.apple.com/xcode/download/>`_ را نصب کرده‌اید. این شامل کامپایلر `Clang C++ <https://en.wikipedia.org/wiki/Clang>`_  ،  `Xcode IDE <https://en.wikipedia.org/wiki/Xcode>`_  و سایر ابزارهای توسعه اپل می‌باشد که برای ایجاد برنامه‌های ++ C در OS X مورد نیاز است. اگر برای اولین بار Xcode را نصب می‌کنید یا نسخه جدیدی را نصب کرده‌اید، باید قبل از توسعه، با لایسنس موافقت کنید تا بتوانید با خط فرمان، توسعه‌ها را انجام دهید:


=======
For macOS builds, ensure that you have the latest version of
`Xcode installed <https://developer.apple.com/xcode/resources/>`_.
This contains the `Clang C++ compiler <https://en.wikipedia.org/wiki/Clang>`_, the
`Xcode IDE <https://en.wikipedia.org/wiki/Xcode>`_ and other Apple development
tools that are required for building C++ applications on OS X.
If you are installing Xcode for the first time, or have just installed a new
version then you will need to agree to the license before you can do
command-line builds:
>>>>>>> english/develop

.. code-block:: bash

    sudo xcodebuild -license accept


اسکریپت نسخه OS X ما، از مدیریت بسته `هوم‌برو <https://brew.sh>`_  برای نصب نیازمندیهای خارجی استفاده می‌کند. اگر می‌خواهید دوباره از ابتدا شروع کنید، در اینجا نحوه `حذف نصب هوم‌برو 
<https://docs.brew.sh/FAQ#how-do-i-uninstall-homebrew>`_ ذکر شده‌است.

پیش نیازها - ویندوز
-----------------------

شما باید وابستگی‌های زیر را برای نسخه‌های ویندوز برای سالیدیتی نصب کنید:

+-----------------------------------+-------------------------------------------------------+
| Software                          | Notes                                                 |
+===================================+=======================================================+
| `Visual Studio 2019 Build Tools`_ | C++ compiler                                          |
+-----------------------------------+-------------------------------------------------------+
| `Visual Studio 2019`_  (Optional) | C++ compiler and dev environment.                     |
+-----------------------------------+-------------------------------------------------------+
| `Boost`_ (version 1.77+)          | C++ libraries.                                        |
+-----------------------------------+-------------------------------------------------------+

اگر از قبل یک ویرایشگر  دارید و فقط به کامپایلر و کتابخانه نیاز دارید، می‌توانید ابزارهای نسخه 2019 ویژوال استودیو  را نصب کنید.

ویژوال استودیو 2019  هم ویرایشگر و هم کامپایلر و کتابخانه‌های لازم را ارائه می‌دهد. بنابراین اگر ویرایشگر ندارید و ترجیح می‌دهید سالیدیتی را توسعه دهید، ویژوال استودیو 2019 ممکن است یک انتخاب برای شما باشد تا همه چیز را به راحتی راه اندازی کنید.

در اینجا لیستی از اجزایی که باید در ابزارهای نسخه ویژوال استودیو 2019  نصب شود، آورده شده‌است:

* ویژگی های اصلی ++Visual Studio C 
* 	مجموعه ابزارهای VC++   2019 v141 toolset (x86,x64) 
* 	CRT SDK ویندوز یونیورسال
* 	SDK ویندوز 8.1
* 	پشتیبانی از C ++/CLI

.. _Visual Studio 2019: https://www.visualstudio.com/vs/
.. _Visual Studio 2019 Build Tools: https://visualstudio.microsoft.com/vs/older-downloads/#visual-studio-2019-and-other-products

<<<<<<< HEAD
اسکریپت کمکی وابستگی‌ها 
--------------------------

ما یک اسکریپت کمکی داریم که می‌توانید از آن برای نصب تمام وابستگی‌های خارجی مورد نیاز در مک‌او اس  ، ویندوز و چندین توزیع لینوکس استفاده کنید.

.. code-block:: bash

    ./scripts/install_deps.sh

یا در ویندوز:
=======
We have a helper script which you can use to install all required external dependencies:
>>>>>>> english/develop

.. code-block:: bat

    scripts\install_deps.ps1

<<<<<<< HEAD
=======
This will install ``boost`` and ``cmake`` to the ``deps`` subdirectory.
>>>>>>> english/develop

توجه داشته باشید که دستور دوم   ``boost`` و   ``cmake`` را در زیر شاخه  ``deps`` نصب می‌کند، در حالی که دستور قبلی سعی می‌کند وابستگی‌ها را به صورت جهانی نصب کند.


مخزن را کلون کنید
--------------------

برای شبیه سازی کد منبع ، دستور زیر را اجرا کنید:

.. code-block:: bash

    git clone --recursive https://github.com/ethereum/solidity.git
    cd solidity

<<<<<<< HEAD
اگر می‌خواهید به نسخه سالیدیتی کمک کنید، باید سالیدیتی را فورک  کنید و فورک شخصی خود را به عنوان ریموت دوم  اضافه کنید: 
=======
If you want to help develop Solidity,
you should fork Solidity and add your personal fork as a second remote:
>>>>>>> english/develop

.. code-block:: bash

    git remote add personal git@github.com:[username]/solidity.git

.. note::
<<<<<<< HEAD
   این روش منجر به نسخه پیش انتشار  می‌شود که به عنوان مثال یک فَلگ  در هر کد بایتی که توسط چنین کامپایلری تولید می‌شود، تنظیم می‌شود. اگر می‌خواهید کامپایلر سالیدیتی منتشر شده را دوباره توسعه دهید، لطفاً از tarball منبع در صفحه انتشار گیتهاب استفاده کنید:
=======
    This method will result in a pre-release build leading to e.g. a flag
    being set in each bytecode produced by such a compiler.
    If you want to re-build a released Solidity compiler, then
    please use the source tarball on the github release page:
>>>>>>> english/develop

        https://github.com/ethereum/solidity/releases/download/v0.X.Y/solidity_0.X.Y.tar.gz
         (نه "کد منبع" ارائه شده توسط گیتهاب). 


نسخه خط فرمان
------------------


**قبل از توسعه حتماً وابستگی‌های خارجی را نصب کنید (به قسمت بالا مراجعه کنید).**

پروژه سالیدیتی از CMake برای پیکربندی نسخه استفاده می‌کند. ممکن است بخواهید ccache را برای سرعت بخشیدن به نسخه‌های مکرر نصب کنید. CMake آن را به طور خودکار انتخاب می‌کند. نسخه سالیدیتی در لینوکس، مک‌او اس  و سایر یونیکس‌ها کاملاً مشابه است: 


.. _ccache: https://ccache.dev/

.. code-block:: bash

    mkdir build
    cd build
    cmake .. && make

یا حتی در لینوکس و مک‌او اس راحت‌تر، می‌توانید اجرا کنید:

.. code-block:: bash

    #note: this will install binaries solc and soltest at usr/local/bin
    ./scripts/build.sh

.. warning::

    توسعه BSD باید کار کند، اما توسط تیم سالیدیتی آزمایش نشده است.

و برای ویندوز:

.. code-block:: bash

    mkdir build
    cd build
    cmake -G "Visual Studio 16 2019" ..



در صورت تمایل به استفاده از نسخه boost نصب شده توسط اسکریپت  ``scripts\install_deps.ps1`` ، علاوه بر این باید ``*-DBoost_DIR="deps\boost\lib\cmake\Boost-"`` و  ``-DCMAKE_MSVC_RUNTIME_LIBRARY=MultiThreaded``  را به عنوان آرگومان برای فراخوانی ``cmake`` ارسال کنید. 

این عمل باید منجر به ایجاد **solidity.sln** در آن نسخه دایرکتوری ‌شود. دو بار کلیک بر روی آن فایل باعث می‌شود تا ویژوال استودیو روشن شود. ما ساخت پیکربندی **انتشار**  را پیشنهاد می‌کنیم، اما بقیه نیز کار می‌کنند.

از سوی دیگر، می‌توانید برای ویندوز روی خط فرمان  توسعه دهید، مانند این:

.. code-block:: bash

    cmake --build . --config Release


گزینه‌های CMake
=============


اگر علاقه دارید که چه گزینه‌های CMake در دسترس هستند، ``cmake .. -LH`` را اجرا کنید.

.. _smt_solvers_build:

حل کننده‌های  SMT
-----------
<<<<<<< HEAD
سالیدیتی را می‌توان در کنار حل کننده‌های SMT ایجاد کرد و در صورت یافتن آنها در سیستم به طور پیش فرض این کار را انجام می‌دهد. هر حل کننده را می‌توان با گزینه `cmake` غیرفعال کرد.
=======
Solidity can be built against SMT solvers and will do so by default if
they are found in the system. Each solver can be disabled by a ``cmake`` option.
>>>>>>> english/develop

*توجه: در برخی موارد ، این نیز می تواند یک راه حل احتمالی برای خرابی نسخه باشد.*



در داخل پوشه build می‌توانید آنها را غیرفعال کنید، زیرا به طور پیش فرض فعال هستند:

.. code-block:: bash

    # disables only Z3 SMT Solver.
    cmake .. -DUSE_Z3=OFF

    # disables only CVC4 SMT Solver.
    cmake .. -DUSE_CVC4=OFF

    # disables both Z3 and CVC4
    cmake .. -DUSE_CVC4=OFF -DUSE_Z3=OFF

رشته نسخه  با جزئیات
============================

رشته نسخه سالیدیتی شامل چهار قسمت است:

- شماره نسخه

- برچسب پیش از انتشار، معمولاً با  ``develop.YYYY.MM.DD`` یا  ``nightly.YYYY.MM.DD`` تنظیم می‌شود.
- کامیت در قالب  ``commit.GITHASH``
- پلتفرم، که دارای تعداد دلخواه موارد است، حاوی جزئیات مربوط به پلتفرم و کامپایلر

<<<<<<< HEAD
=======
These parts are combined as required by SemVer, where the Solidity pre-release tag equals to the SemVer pre-release
and the Solidity commit and platform combined make up the SemVer build metadata.
>>>>>>> english/develop


اگر تغییرات محلی وجود داشته باشد، کامیت‌ها با ``mod.`` پسوند داده می‌شود.

این قطعات طبق نیاز Semver ترکیب می‌شوند، جایی که برچسب پیش از انتشار سالیدیتی برابر است با پیش از انتشار Semver و کامیت سالیدیتی و پلتفرم ترکیبی فراداده توسعه Semver را تشکیل می‌دهند.


مثال انتشار: ``0.4.8+commit.60cc1668.Emscripten.clang``

یک نمونه پیش از انتشار:  ``0.4.9-nightly.2017.1.17+commit.6ecb4aa3.Emscripten.clang``


اطلاعات مهم در مورد نسخه بندی
======================================

<<<<<<< HEAD
پس از انتشار، سطح نسخه پَچ   بامپ  ‌شده است، زیرا ما فرض می‌کنیم که فقط تغییرات سطح پَچ دنبال می‌شود. وقتی تغییرات ادغام می‌شوند، نسخه باید با توجه به semver و شدت تغییرات بامپ شود. سرانجام، همیشه نسخه‌ای از نسخه فعلی شبانه منتشر می‌شود، اما بدون تعیین ``prerelease`` .
=======
After a release is made, the patch version level is bumped, because we assume that only
patch level changes follow. When changes are merged, the version should be bumped according
to SemVer and the severity of the change. Finally, a release is always made with the version
of the current nightly build, but without the ``prerelease`` specifier.
>>>>>>> english/develop

مثال:

            1.	نسخه شبانه  از این پس نسخه 0.4.1 دارد.

            2.	تغییرات بدون تغییرات جدید  ارائه می شوند-> بدون تغییر در نسخه.

            3.	یک تغییر جدید  معرفی می‌شود -> نسخه به 0.5.0 افزایش می یابد.

            4.	نسخه 0.5.0 ساخته شده است.


این رفتار با :ref:`نسخه پراگما  <version_pragma>`  به خوبی کار می‌کند.

<<<<<<< HEAD

=======
1. The 0.4.0 release is made.
2. The nightly build has a version of 0.4.1 from now on.
3. Non-breaking changes are introduced --> no change in version.
4. A breaking change is introduced --> version is bumped to 0.5.0.
5. The 0.5.0 release is made.

This behavior works well with the  :ref:`version pragma <version_pragma>`.
>>>>>>> english/develop
