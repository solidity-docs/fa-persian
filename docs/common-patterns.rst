###############
الگو های مرسوم
###############

.. index:: withdrawal

.. _withdrawal_pattern:

*************************
برداشت از قراردادها
*************************

روش پیشنهادی برای ارسال وجوه پس از یک تاثیر ، استفاده از الگوی برداشت است. اگرچه
روش بصری ارسال اتر، به عنوان نتیجه یک تاثیر، یک فراخوانی ``ارسال`` مستقیم است، اما این
توصیه نمی شود زیرا خطر بالقوه امنیتی ایجاد می کند. ممکن است در مورد این موضوع در
صفحه ی ملاحظات امنیتی :ref:`security_considerations` بیشتر مطالعه کنید.

در زیر نمونه کاربردی از الگوی برداشت در یک قرارداد است که هدف آن ارسال بیشترین مبلغ
به قرارداد جهت تبدیل شدن به "ثروتمندترین"  است.
که از `King of the Ether <https://www.kingoftheether.com/>`_ الهام گرفته شده است.


در قرارداد ذیل ، اگر شما دیگر ثروتمند ترین نباشید، شما بودجه شخصی را دریافت می کنید که
اکنون ثروتمندترین است.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    contract WithdrawalContract {
        address public richest;
        uint public mostSent;

        mapping (address => uint) pendingWithdrawals;

        /// The amount of Ether sent was not higher than
        /// the currently highest amount.
        error NotEnoughEther();

        constructor() payable {
            richest = msg.sender;
            mostSent = msg.value;
        }

        function becomeRichest() public payable {
            if (msg.value <= mostSent) revert NotEnoughEther();
            pendingWithdrawals[richest] += msg.value;
            richest = msg.sender;
            mostSent = msg.value;
        }

        function withdraw() public {
            uint amount = pendingWithdrawals[msg.sender];
            // Remember to zero the pending refund before
            // sending to prevent re-entrancy attacks
            pendingWithdrawals[msg.sender] = 0;
            payable(msg.sender).transfer(amount);
        }
    }

این بر خلاف الگوی بصری ارسال است:

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    contract SendContract {
        address payable public richest;
        uint public mostSent;

        /// The amount of Ether sent was not higher than
        /// the currently highest amount.
        error NotEnoughEther();

        constructor() payable {
            richest = payable(msg.sender);
            mostSent = msg.value;
        }

        function becomeRichest() public payable {
            if (msg.value <= mostSent) revert NotEnoughEther();
            // This line can cause problems (explained below).
            richest.transfer(msg.value);
            richest = payable(msg.sender);
            mostSent = msg.value;
        }
    }

توجه داشته باشید که ، در این مثال ، یک مهاجم می تواند قرارداد را به حالت غیر قابل استفاده
در بیاورد، زیرا ثروتمندترین ``richest`` آدرس ، آردس قرار دادی است که توابع دریافت و عقبگرد دارد و از
کار می افتند ( به عنوان مثال با استفاده از ``()revert`` یا فقط با مصرف بیش از 2300 هزینه
گاز ارسال شده به آنها). به این ترتیب ، هر زمانی که تابع انتقال ``transfer`` برای تحویل وجوه به قرارداد
"سمی شده" فرا خوانی شود ، شکست خواهد خورد و بنابراین ثروتمندترین ``becomeRichest`` نیز شکست خواهد
خورد، این قرداد برای همیشه گیر می کند.


در عوض ، اگر از الگوی "برداشت" از مثال اول استفاده کنید، مهاجم می تواند ، باعث کند
شدن برداشت خود شود و نه با بقیه بخش های قرارداد کار داشته باشد.

.. index:: access;restricting

******************
محدود کردن دسترسی
******************

محدود کردن دسترسی الگوی مرسومی برای قراردادهاست. توجه داشته باشید که شما هرگز
نمی توانید انسانی یا رایانه ای را از خواندن محتوای معاملات یا وضعیت قرار داد خود منع کنید.
با استفاده از رمزگذاری می توانید این کار را کمی سخت کنید، اما اگر قرار  باشد قرارداد های
دیگر قراداد شما را بخوانند ، دیگران نیز این کار را می توانند انجام دهند.

شما می توانید دسترسی خواندن وضعیت قرارداد خود با **سایر قرارداد ها** را محدود کنید. در
واقع این پیش فرض است مگر اینکه متغیر های وضعیت خود را به حالت عمومی ``public`` تعریف کنید.

علاوه بر این ، شما می توانید افرادی را که می توانند وضعیت قرار داد شما را تغییر دهند ی
توابع قرار داد شما را فراخوانی کنند، محدود کنید و این همان چیزی است که این بخش دنبال
می کند.

.. index:: function;modifier

استفاده از توابع **اصلاح کننده** این محدودیت ها را بسیار خوانا می کند.

.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    contract AccessRestriction {
        // These will be assigned at the construction
        // phase, where `msg.sender` is the account
        // creating this contract.
        address public owner = msg.sender;
        uint public creationTime = block.timestamp;

        // Now follows a list of errors that
        // this contract can generate together
        // with a textual explanation in special
        // comments.

        /// Sender not authorized for this
        /// operation.
        error Unauthorized();

        /// Function called too early.
        error TooEarly();

        /// Not enough Ether sent with function call.
        error NotEnoughEther();

        // Modifiers can be used to change
        // the body of a function.
        // If this modifier is used, it will
        // prepend a check that only passes
        // if the function is called from
        // a certain address.
        modifier onlyBy(address _account)
        {
            if (msg.sender != _account)
                revert Unauthorized();
            // Do not forget the "_;"! It will
            // be replaced by the actual function
            // body when the modifier is used.
            _;
        }

        /// Make `_newOwner` the new owner of this
        /// contract.
        function changeOwner(address _newOwner)
            public
            onlyBy(owner)
        {
            owner = _newOwner;
        }

        modifier onlyAfter(uint _time) {
            if (block.timestamp < _time)
                revert TooEarly();
            _;
        }

        /// Erase ownership information.
        /// May only be called 6 weeks after
        /// the contract has been created.
        function disown()
            public
            onlyBy(owner)
            onlyAfter(creationTime + 6 weeks)
        {
            delete owner;
        }

        // This modifier requires a certain
        // fee being associated with a function call.
        // If the caller sent too much, he or she is
        // refunded, but only after the function body.
        // This was dangerous before Solidity version 0.4.0,
        // where it was possible to skip the part after `_;`.
        modifier costs(uint _amount) {
            if (msg.value < _amount)
                revert NotEnoughEther();

            _;
            if (msg.value > _amount)
                payable(msg.sender).transfer(msg.value - _amount);
        }

        function forceOwnerChange(address _newOwner)
            public
            payable
            costs(200 ether)
        {
            owner = _newOwner;
            // just some example condition
            if (uint160(owner) & 0 == 1)
                // This did not refund for Solidity
                // before version 0.4.0.
                return;
            // refund overpaid fees
        }
    }

یک روش تخصصی تر که می تواند دسترسی به فراخوانی توابع را محدود کند ، در مثال بعدی بحث خواهد شد.

.. index:: state machine

*************
ماشین وضعیت
*************

قرارداد ها اغلب به عنوان یک ماشین وضعیت عمل می کنند، به این معنی است که آنها دارای
**مراحل** خاصی هستند که در آنها رفتار متفاوتی دارند که در آن حالت می توان توابع مختلفی را
فرا خوانی کرد. فراخوانی توابع معمولا یک مرحله را به پایان می رساند و قرار داد را به مرحله
بعدی منتقل می کند ( مخصوصا اگر مدل قرار داد از نوع **تعاملی** باشد). همچنین نرمال است ک
برخی از مراحل به صور خودکار در یک بازه **زمانی** مشخص انجام می شوند.

به عنوان مثال این قرار داد یک حراج کور است که از مرحله " پذیرش پیشنهادات کور" شروع
می شود، سپس به مرحله "آشکار کردن پیشنهادات" منتقل می شود و با مرحله "تعیین نتیجه
حراج" به پایان می رسد.


.. index:: function;modifier

در این شرایط می توان از توابع تغییر دهنده برای مدل سازی حالت ها و جلوگیری از استفاده نادرست از قرار داد استفاده کرد.

مثال
=======

در مثال زیر ، تابع تغییر دهنده ``atStage`` اطمینان حاصل می کند که تابع فقط در یک مرحله خاص فراخوانی شود.

 انتقال به موقع بصورت خودکار توسط تابع تغیر دهنده ``timedTransitions`` انجام می شود، که باید در همه توابع استفاده شود.

.. note::
    **ترتیب توابع تغییر دهنده مهم است**.
    اگر atStage با timedTansitions ترکیب
    شده است، مطمئن شوید که بعد از مرحله دومی آن را ذکر کرده اید، تا مرحله جدید محسوب
    شود.

نهایتا، با استفاده از تابع تغییر دهنده ``transitionNext`` می توان به طور خودکار پس از اتمام
روال تابع به مرحله بعد رفت.

.. note::
    **می توان از تابع تغییر دهنده صرف نظر کرد**.
    این مورد فقط در سالیدیتی نسخه 0.4.0 به قبل اعمال می شود:
    از انجایی که تغیردهنده ها فقط با جایگزینی کد اعمال می شوند
    و نه با استفاده از یک فراخوانی تابع، در صورت استفاده در بخش باز گشت تابع ،  از کد تغیر
    دهنده می توان صرف نظر کرد. اگر می خواهید این کار را انجام دهید، مطمئن شوید که
    بصورت دستی تابع NextStage را از آن تابع ها فراخوانی می کنید. با شروع نسخه 0.4.0 کد
    تغیر دهندها حتی اگر تابع صراحا هم برگردد، اجرا می شوند.

.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    contract StateMachine {
        enum Stages {
            AcceptingBlindedBids,
            RevealBids,
            AnotherStage,
            AreWeDoneYet,
            Finished
        }
        /// Function cannot be called at this time.
        error FunctionInvalidAtThisStage();

        // This is the current stage.
        Stages public stage = Stages.AcceptingBlindedBids;

        uint public creationTime = block.timestamp;

        modifier atStage(Stages _stage) {
            if (stage != _stage)
                revert FunctionInvalidAtThisStage();
            _;
        }

        function nextStage() internal {
            stage = Stages(uint(stage) + 1);
        }

        // Perform timed transitions. Be sure to mention
        // this modifier first, otherwise the guards
        // will not take the new stage into account.
        modifier timedTransitions() {
            if (stage == Stages.AcceptingBlindedBids &&
                        block.timestamp >= creationTime + 10 days)
                nextStage();
            if (stage == Stages.RevealBids &&
                    block.timestamp >= creationTime + 12 days)
                nextStage();
            // The other stages transition by transaction
            _;
        }

        // Order of the modifiers matters here!
        function bid()
            public
            payable
            timedTransitions
            atStage(Stages.AcceptingBlindedBids)
        {
            // We will not implement that here
        }

        function reveal()
            public
            timedTransitions
            atStage(Stages.RevealBids)
        {
        }

        // This modifier goes to the next stage
        // after the function is done.
        modifier transitionNext()
        {
            _;
            nextStage();
        }

        function g()
            public
            timedTransitions
            atStage(Stages.AnotherStage)
            transitionNext
        {
        }

        function h()
            public
            timedTransitions
            atStage(Stages.AreWeDoneYet)
            transitionNext
        {
        }

        function i()
            public
            timedTransitions
            atStage(Stages.Finished)
        {
        }
    }
