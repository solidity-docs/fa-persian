********************
کانال پرداخت خرد
********************

در این بخش نحوه ساختن نمونه پیاده سازی کانال پرداخت  را خواهیم آموخت. کانال پرداخت از امضاهای رمزنگاری شده برای انتقال مکرر اتر بین طرف‌های مشابه به صورت ایمن، آنی و بدون کارمزد استفاده می‌کند. برای مثال، باید نحوه امضا و تأیید امضاها و راه اندازی کانال پرداخت را درک کنیم.

ایجاد و تأیید امضا
=================================

  تصور کنید آلیس می‌خواهد مقداری اتر برای باب ارسال کند، یعنی آلیس فرستنده است و باب گیرنده آن می‌باشد.

آلیس فقط باید پیام های خارج از زنجیرهِ امضا شده با رمزنگاری (مثلاً از طریق ایمیل) را به باب بفرستد و این شبیه چک نوشتن است.

 آلیس و باب برای تأیید تراکنش‌ها از امضاها استفاده می‌کنند که با قراردادهای هوشمند در اتریوم امکان پذیر است. آلیس یک قرارداد هوشمند ساده خواهد ساخت که به او امکان می‌دهد اتر را منتقل کند، اما به جای اینکه خودش یک تابع را برای شروع پرداخت فراخوانی کند، به باب اجازه این کار را می‌دهد و بنابراین هزینه تراکنش را پرداخت می‌کند.

قرارداد به شرح زیر کار می‌کند:

    1.	آلیس قرارداد ``ReceiverPays``  را دیپلوی می‌کند، به اندازه کافی اتر را برای پوشش پرداخت‌هایی که انجام خواهد شد، پیوست می‌کند.
    2.	آلیس با امضای پیام با کلید خصوصی خود اجازه پرداخت را می‌دهد.
    3.	آلیس پیام امضا شده با رمزنگاری را برای باب می‌فرستد. نیازی به مخفی نگه داشتن پیام نیست (بعداً توضیح داده خواهد شد) و سازوکار ارسال آن اهمیتی ندارد.
    4.	باب با ارائه پیام امضا شده به قرارداد هوشمند، پرداخت خود را مدعی می‌شود. قرارداد صحت پیام را تأیید می‌کند و سپس وجوه را آزاد می‌کند.


ایجاد امضا
----------------------


آلیس برای امضای تراکنش نیازی به تعامل با شبکه اتریوم ندارد، روند کار کاملا آفلاین است. در این آموزش، ما با استفاده از روش توصیف شده در `EIP-762 <https://github.com/ethereum/EIPs/pull/712>`_  پیام‌ها را در مرورگر با استفاده از  `web3.js <https://github.com/ethereum/web3.js>`_  و `MetaMask <https://metamask.io>`_, امضا خواهیم کرد، زیرا تعدادی از مزایای امنیتی دیگر را فراهم می‌کند.



.. code-block:: javascript

    /// Hashing first makes things easier
    var hash = web3.utils.sha3("message to sign");
    web3.eth.personal.sign(hash, web3.eth.defaultAccount, function () { console.log("Signed"); });

.. note::

``web3.eth.personal.sign`` طول پیام را به داده‌های امضا شده اضافه می‌کند. از آنجا که ما ابتدا هش می‌کنیم، پیام همیشه دقیقاً 32 بایت خواهد بود و بنابراین این پیشوند طول  همیشه یکسان است.
  

چه چیزی برای امضا
------------


برای قراردادی که پرداخت‌ها را انجام می‌دهد، پیام امضا شده باید شامل موارد زیر باشد:

        1.	آدرس گیرنده.
        2.	مبلغی که باید منتقل شود.
        3.	محافظت در برابر حملات مجدد. 

حمله مجدد زمانی رخ می‌دهد که از پیام امضا شده مجدداً برای درخواست مجوز برای اقدام دیگر استفاده می‌شود. برای جلوگیری از حملات مجدد، ما از همان روش تراکنش اتریوم استفاده می‌کنیم، اصطلاحاً نانس  نامیده می‎شود، یعنی تعداد تراکنش‌های ارسال شده توسط یک حساب می‌باشد. قرارداد هوشمند بررسی می‌کند که آیا یک نانس چندین بار استفاده شده‌است.

نوع دیگر حمله مجدد می‌تواند هنگامی رخ دهد که مالکِ قرارداد هوشمند  ``ReceiverPays`` را دیپلوی  کند،  مقداری پرداخت انجام بدهد و سپس قرارداد را از بین ببرد. بعداً، آنها تصمیم می‌گیرند که قرارداد هوشمند ``ReceiverPays``  را دوباره دیپلوی کنند، اما قرارداد جدید، نانس  استفاده شده در دیپلوی قبلی را نمی‌شناسد، بنابراین مهاجم می‌تواند دوباره از پیام‌های قدیمی استفاده کند.

آلیس می‌تواند با درج آدرس قرارداد در پیام در برابر این حمله محافظت کند و فقط پیام‌های حاوی آدرس قرارداد خود پذیرفته می‌شوند. نمونه‌ای از این مورد را می‌توانید در دو خط اول تابع ``()claimPayment``  در قرارداد کامل در انتهای این بخش بیابید.


بسته بندی آرگومان‌ها 
-----------------


حالا که ما مشخص کرده‌ایم که چه اطلاعاتی را باید در پیام امضا شده قرار دهیم، ما آماده هستیم که پیام را کنار هم قرار دهیم و آن را هش و امضا کنیم.
برای سادگی، داده‌ها را بهم پیوند می‌دهیم. کتابخانه `ethereumjs-abi <https://github.com/ethereumjs/ethereumjs-abi>`_ تابعی به نام ``soliditySHA3``  را فراهم می‌کند که رفتار ``keccak256``  سالیدیتی را که برای آرگومان‌های رمزگذاری شده با استفاده از  ``abi.encodePacked`` اعمال می‌شود، را تقلید می‌کند. در اینجا یک تابع جاوا اسکریپت وجود دارد که امضای مناسب را برای مثال ``ReceiverPays``  ایجاد می‌کند:


.. code-block:: javascript

    // recipient is the address that should be paid.
    // amount, in wei, specifies how much ether should be sent.
    // nonce can be any unique number to prevent replay attacks
    // contractAddress is used to prevent cross-contract replay attacks
    function signPayment(recipient, amount, nonce, contractAddress, callback) {
        var hash = "0x" + abi.soliditySHA3(
            ["address", "uint256", "uint256", "address"],
            [recipient, amount, nonce, contractAddress]
        ).toString("hex");

        web3.eth.personal.sign(hash, web3.eth.defaultAccount, callback);
    }

بازیابی امضای پیام در سالیدیتی
-----------------------------------------



به طور کلی، امضاهای ECDSA از دو پارامتر  ``r`` و  ``s`` تشکیل شده‌است. امضاها در اتریوم شامل یک پارامتر سوم به نام  ``v``  هستند که می‌توانید برای تایید اینکه کدام کلیدخصوصیِ حساب، برای امضای پیام و تراکنش فرستنده بکار رفته‌است، استفاده کنید. سالیدیتی یک تابع داخلی :ref:`ecrecover <mathematical-and-cryptographic-functions>`  را ارائه می‌دهد که با استفاده از پارامترهای  ``r``  ،  ``s``  و  ``v``  یک پیام را می‌پذیرد و آدرسی را که برای امضای پیام استفاده شده بود، برمی‌گرداند.


استخراج پارامترهای امضا
-----------------------------------

امضاهای تولید شده توسط web3.js بهم پیوستن  ``r``  ، ``s``  و  ``v``  هستند، بنابراین اولین قدم جدا کردن این پارامترها از یکدیگر است. شما می‌توانید این کار را در سمت کلاینت انجام دهید، اما انجام آن در داخل قرارداد هوشمند به این معنی است که شما فقط باید یک پارامتر امضا را بجای سه پارامتر ارسال کنید. جداسازی آرایه بایت  به قسمت‌های تشکیل دهنده آن یک خرابکاری است، بنابراین ما برای انجام این کار در تابع ``splitSignature``  از :doc:`اسمبلی درون خطی<assembly>`  استفاده می‌کنیم (سومین تابع در قرارداد کامل در انتهای این بخش).

محاسبه پیام هش
--------------------------

قرارداد هوشمند باید دقیقاً بداند چه پارامترهایی امضا شده‌اند، بنابراین باید پیام را از طریق پارامترها دوباره ایجاد کند و از آن برای تأیید امضا استفاده کند. توابع ``prefixed``   و  ``recoverSigner`` این کار را در تابع  ``claimPayment`` انجام می‌دهند.

قرارداد کامل
-----------------

.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;
    contract ReceiverPays {
        address owner = msg.sender;

        mapping(uint256 => bool) usedNonces;

        constructor() payable {}

        function claimPayment(uint256 amount, uint256 nonce, bytes memory signature) external {
            require(!usedNonces[nonce]);
            usedNonces[nonce] = true;

            // this recreates the message that was signed on the client
            bytes32 message = prefixed(keccak256(abi.encodePacked(msg.sender, amount, nonce, this)));

            require(recoverSigner(message, signature) == owner);

            payable(msg.sender).transfer(amount);
        }

        /// destroy the contract and reclaim the leftover funds.
        function shutdown() external {
            require(msg.sender == owner);
            selfdestruct(payable(msg.sender));
        }

        /// signature methods.
        function splitSignature(bytes memory sig)
            internal
            pure
            returns (uint8 v, bytes32 r, bytes32 s)
        {
            require(sig.length == 65);

            assembly {
                // first 32 bytes, after the length prefix.
                r := mload(add(sig, 32))
                // second 32 bytes.
                s := mload(add(sig, 64))
                // final byte (first byte of the next 32 bytes).
                v := byte(0, mload(add(sig, 96)))
            }

            return (v, r, s);
        }

        function recoverSigner(bytes32 message, bytes memory sig)
            internal
            pure
            returns (address)
        {
            (uint8 v, bytes32 r, bytes32 s) = splitSignature(sig);

            return ecrecover(message, v, r, s);
        }

        /// builds a prefixed hash to mimic the behavior of eth_sign.
        function prefixed(bytes32 hash) internal pure returns (bytes32) {
            return keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", hash));
        }
    }


نوشتن یک کانال پرداخت  ساده
================================

اکنون آلیس یک پیاده سازی ساده اما کامل از یک کانال پرداخت ایجاد کرده‌است. کانال‌های پرداخت از امضاهای رمزنگاری شده برای انتقال مکرر اتر به صورت ایمن، فوری و تراکنش بدون کارمزد استفاده می‌کنند.


کانال پرداخت چیست؟
--------------------------


کانال‌های پرداخت به شرکت کنندگان این امکان را می‌دهد تا انتقال‌های مکرر اتر را بدون استفاده از تراکنش انجام دهند. این بدان معنی است که شما می‌توانید از تأخیر و هزینه‌های مرتبط با تراکنش‌ها جلوگیری کنید. ما می‌خواهیم یک کانال پرداخت یک طرفه ساده بین دو طرف (آلیس و باب) را بررسی کنیم. شامل سه مرحله است:


    1.	آلیس قرارداد هوشمند همراه با اتر را تأمین می‌کند. این کانال پرداخت را "باز" می‌کند.
    2.	آلیس پیام‌هایی را امضا می‌کند که مشخص می‌کند چه مقدار از آن اتر به گیرنده بدهکار است. این مرحله برای هر پرداخت تکرار می‌شود.
    3.	باب کانال پرداخت را "می بندد"، سهم خود را از اتر پس می‌گیرد و باقیمانده را به فرستنده ارسال می‌کند.



.. note::
  فقط مراحل 1 و 3 به تراکنش‌های اتریوم نیاز دارند، مرحله 2 به این معنی است که فرستنده از طریق روش‌های غیر زنجیره‌ای (به عنوان مثال ایمیل) پیام امضا شده رمزنگاری شده را به گیرنده منتقل می‌کند. این بدان معناست که برای پشتیبانی از هر تعداد تراکنش  فقط دو تراکنش لازم است.

تضمین شده که باب وجوه  خود را دریافت می‌کند زیرا قرارداد هوشمند اتر را اسکو  می‌کند (اسکو  به معنی اینکه طرفین معامله بر یک سری شرایط توافق میکنند که اگر این شرایط توسط دو طرفین انجام شوند طرف ثالث مانند قرارداد هوشمند امکان برداشت یا پرداخت وجوه را امکان پذیر میکند.) و قول یک پیام معتبر امضا شده را می‌دهد. قرارداد هوشمند همچنین مهلت زمانی را اعمال می‌کند، بنابراین آلیس تضمین می‌کند که سرانجام وجوه خود را بازیابی می‌کند حتی اگر گیرنده از بستن کانال خودداری کند. شرکت کنندگان در یک کانال پرداخت تصمیم میگیرند که چه مدت آن را باز نگه دارند. برای یک معامله کوتاه مدت، مانند پرداخت کافی نت  به ازای هر دقیقه دسترسی به شبکه، کانال پرداخت ممکن است به مدت محدودی باز نگه داشته شود. از طرف دیگر، برای پرداخت مکرر، مانند پرداخت دستمزد ساعتی به یک کارمند، کانال پرداخت ممکن است برای چندین ماه یا سال باز نگه داشته شود.

باز کردن کانال پرداخت
---------------------------

برای باز کردن کانال پرداخت، آلیس قرارداد هوشمند را دیپلوی می‌کند، اتر را برای تضمین  ضمیمه می‌کند و دریافت کننده مورد نظر و حداکثر مدت زمان  وجود کانال را مشخص می‌کند. در انتهای این بخش این تابع ``SimplePaymentChannel``  در قرارداد است.


ایجاد پرداخت ها
---------------

   آلیس با ارسال پیام های امضا شده به باب، پرداخت‌ها را انجام می‌دهد. این مرحله کاملاً خارج از شبکه اتریوم انجام می‌شود. پیام‌ها به صورت رمزنگاری شده توسط فرستنده امضا می‌شوند و سپس مستقیماً به گیرنده ارسال می‌شوند. 

هر پیام شامل اطلاعات زیر است:

    *	آدرسِ قراردادِ هوشمند استفاده شده برای جلوگیری از حملات مجدد  بین قراردادی.
    *	مقدارِ کلِ اتری که تاکنون به گیرنده بدهکار است.

در پایان یک سری انتقال‌ها، فقط یک بار کانال پرداخت بسته می‌شود. به همین دلیل، فقط یکی از پیام‌های ارسالی استفاده می‌شود. به همین دلیل است که هر پیام مجموع مقدار کل اتر بدهکار  را  به جای مقدار جداگانه کانال پرداخت  مشخص می‌کند.گیرنده به طور طبیعی آخرین پیام را بخاطر اینکه بالاترین جمع کل  را دارد، برای بازخرید   انتخاب خواهد کرد. دیگر به نانس  برای هر پیام نیاز نمی‌باشد زیرا قرارداد هوشمند فقط به یک پیام پایبند‌است. برای جلوگیری از استفاده پیامی که در نظر گرفته شده برای یک کانال پرداخت در کانال دیگر، از آدرس قرارداد هوشمند همچنان استفاده می‌شود.



 در اینجا کد جاوا اسکریپت ویرایش شده برای امضای یک پیام به صورت رمزنگاری از بخش قبلی وجود دارد:

.. code-block:: javascript

    function constructPaymentMessage(contractAddress, amount) {
        return abi.soliditySHA3(
            ["address", "uint256"],
            [contractAddress, amount]
        );
    }

    function signMessage(message, callback) {
        web3.eth.personal.sign(
            "0x" + message.toString("hex"),
            web3.eth.defaultAccount,
            callback
        );
    }

    // contractAddress is used to prevent cross-contract replay attacks.
    // amount, in wei, specifies how much Ether should be sent.

    function signPayment(contractAddress, amount, callback) {
        var message = constructPaymentMessage(contractAddress, amount);
        signMessage(message, callback);
    }


بستن کانال پرداخت 
---------------------------

   هنگامی که باب آماده دریافت وجوه  خود باشد، وقت آن است که با فراخوانی تابع ``close``  در قرارداد هوشمند کانال پرداخت را ببندید. بستن کانال به گیرنده اتری که بدهکار است را پرداخت می‌کند و قرارداد را از بین می‌برد و اتر باقی مانده را برای آلیس می‌فرستد. برای بستن کانال، باب باید پیامی را امضا کند که توسط آلیس امضا شده باشد.

 قرارداد هوشمند باید تأیید کند که پیام حاوی یک امضای معتبر از طرف فرستنده است. روند انجام این تأیید همان روندی است که گیرنده از آن استفاده می‌کند. توابع سالیدیتی ``isValidSignature``  و ``recoverSigner``  درست همانند رونوشت‌های جاوا اسکریپت  در بخش قبلی با تابع آخری که از قرارداد ``ReceiverPays``   گرفته شده کار می‌کند.

فقط گیرنده کانال پرداخت می‌تواند تابع ``close``  را فراخوانی کند، که به طور طبیعی جدیدترین پیام پرداخت را ارسال می‌کند زیرا این پیام بیشترین مجموع بدهی  را دارد. اگر فرستنده اجازه فراخوانی این تابع را داشته باشد، می‌تواند پیامی با مقدار کمتری ارائه دهد و گیرنده را از آنچه طلبکار است، فریب دهد.

تابع تأیید می‌کند که پیام امضا شده با پارامترهای داده شده مطابقت دارد. اگر همه چیز بررسی شود، به گیرنده بخشی از اتر ارسال می شود، و بقیه را از طریق  ``selfdestruct`` برای فرستنده ارسال می‌کند. تابع  ``close``  را می‌توانید در قراردادِ کامل مشاهده کنید.

انقضا کانال 
-------------------

  باب می‌تواند در هر زمان کانال پرداخت را ببندد، اما اگر آنها موفق به انجام این کار نشوند، آلیس به راهی برای بازیابی وجوه پس انداز شده  خود نیاز دارد. زمان انقضا در زمان استقرار قرارداد تعیین می‌شود. پس از رسیدن به این زمان، آلیس می‌تواند با فراخوانی تابع ``claimTimeout`` وجوه خود پس بگیرد. تابع  ``claimTimeout``  را می‌توانید در قرارداد کامل مشاهده کنید. 
  بعد از فراخوانی این تابع، باب دیگر نمی‌تواند هیچ اتری دریافت کند، بنابراین مهم است که باب قبل از رسیدن به زمان انقضا، کانال را ببندد.



قرارداد کامل
-----------------

.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.7.0 <0.9.0;
    contract SimplePaymentChannel {
        address payable public sender;      // The account sending payments.
        address payable public recipient;   // The account receiving the payments.
        uint256 public expiration;  // Timeout in case the recipient never closes.

        constructor (address payable recipientAddress, uint256 duration)
            payable
        {
            sender = payable(msg.sender);
            recipient = recipientAddress;
            expiration = block.timestamp + duration;
        }

        /// the recipient can close the channel at any time by presenting a
        /// signed amount from the sender. the recipient will be sent that amount,
        /// and the remainder will go back to the sender
        function close(uint256 amount, bytes memory signature) external {
            require(msg.sender == recipient);
            require(isValidSignature(amount, signature));

            recipient.transfer(amount);
            selfdestruct(sender);
        }

        /// the sender can extend the expiration at any time
        function extend(uint256 newExpiration) external {
            require(msg.sender == sender);
            require(newExpiration > expiration);

            expiration = newExpiration;
        }

        /// if the timeout is reached without the recipient closing the channel,
        /// then the Ether is released back to the sender.
        function claimTimeout() external {
            require(block.timestamp >= expiration);
            selfdestruct(sender);
        }

        function isValidSignature(uint256 amount, bytes memory signature)
            internal
            view
            returns (bool)
        {
            bytes32 message = prefixed(keccak256(abi.encodePacked(this, amount)));

            // check that the signature is from the payment sender
            return recoverSigner(message, signature) == sender;
        }

        /// All functions below this are just taken from the chapter
        /// 'creating and verifying signatures' chapter.

        function splitSignature(bytes memory sig)
            internal
            pure
            returns (uint8 v, bytes32 r, bytes32 s)
        {
            require(sig.length == 65);

            assembly {
                // first 32 bytes, after the length prefix
                r := mload(add(sig, 32))
                // second 32 bytes
                s := mload(add(sig, 64))
                // final byte (first byte of the next 32 bytes)
                v := byte(0, mload(add(sig, 96)))
            }

            return (v, r, s);
        }

        function recoverSigner(bytes32 message, bytes memory sig)
            internal
            pure
            returns (address)
        {
            (uint8 v, bytes32 r, bytes32 s) = splitSignature(sig);

            return ecrecover(message, v, r, s);
        }

        /// builds a prefixed hash to mimic the behavior of eth_sign.
        function prefixed(bytes32 hash) internal pure returns (bytes32) {
            return keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n32", hash));
        }
    }


.. note::

  تابع ``splitSignature`` از همه بررسی‌های امنیتی  استفاده نمی‌کند. برای پیاده سازی واقعی باید از یک کتابخانه تست شده با دقت بیشتری استفاده کرد،  مانند `نسخه  <https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol>`_  openzepplin از این کد.
  

تأیید پرداخت‌ها
------------------

برخلاف بخش قبلی، پیام‌های موجود در یک کانال پرداخت  بلافاصله استفاده نمی‌شوند. گیرنده آخرین پیام را پیگیری می‌کند و هنگامی که زمان بستن کانال پرداخت باشد،  آن را استفاده می‌کند. این به این معنی است که بسیار مهم می‌باشد که گیرنده تأیید خود را برای هر پیام انجام دهد. در غیر این صورت هیچ تضمینی وجود ندارد که در پایان گیرنده بتواند وجوه را دریافت کند. گیرنده باید هر پیام را با استفاده از روند زیر تأیید کند:

گیرنده باید هر پیام را با استفاده از روند زیر تأیید کند:

    1. تأیید کند که آدرس قرارداد در پیام با کانال پرداخت مطابقت دارد.
    2. تأیید کند که کل جدید ، مقدار مورد انتظار است. 
    3. تأیید کند که کل جدید  از مقدار اتر پس انداز شده  بیشتر نیست.
    4. تأیید کند که امضا معتبر است و از طرف فرستنده کانال پرداخت می‌باشد.


برای نوشتن این تأیید از کتابخانه `ethereumjs-util <https://github.com/ethereumjs/ethereumjs-util>`_ استفاده خواهیم کرد. مرحله آخر را می‌توان به روش‌های مختلفی انجام داد، و ما از **جاوا اسکریپت** استفاده می‌کنیم. کد زیر تابع ``constructPaymentMessage``  را از امضای کد جاوا اسکریپت در بالا گرفتیم: 

.. code-block:: javascript

    // this mimics the prefixing behavior of the eth_sign JSON-RPC method.
    function prefixed(hash) {
        return ethereumjs.ABI.soliditySHA3(
            ["string", "bytes32"],
            ["\x19Ethereum Signed Message:\n32", hash]
        );
    }

    function recoverSigner(message, signature) {
        var split = ethereumjs.Util.fromRpcSig(signature);
        var publicKey = ethereumjs.Util.ecrecover(message, split.v, split.r, split.s);
        var signer = ethereumjs.Util.pubToAddress(publicKey).toString("hex");
        return signer;
    }

    function isValidSignature(contractAddress, amount, signature, expectedSigner) {
        var message = prefixed(constructPaymentMessage(contractAddress, amount));
        var signer = recoverSigner(message, signature);
        return signer.toLowerCase() ==
            ethereumjs.Util.stripHexPrefix(expectedSigner).toLowerCase();
    }
