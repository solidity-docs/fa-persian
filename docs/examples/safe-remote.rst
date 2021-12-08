.. index:: purchase, remote purchase, escrow

********************
خرید امن از راه دور
********************

خرید کالا از راه دور در حال حاضر نیاز به چندین طرف دارد که باید به یکدیگر اعتماد کنند. ساده ترین پیکربندی شامل یک فروشنده و یک خریدار است. خریدار مایل است کالایی را از فروشنده دریافت کند و فروشنده مایل است در ازای آن پول (یا معادل آن) دریافت کند. قسمت مشکل ساز محموله در اینجا است: هیچ راهی برای تعیین اطمینان از رسیدن کالا به خریدار وجود ندارد.

روش‌های مختلفی برای حل این مشکل وجود دارد، اما همه آنها در مقابل یک یا بقیه راه‌ها کم می‌آورند. در مثال زیر، هر دو طرف باید مقدار دو برابر یک قلم کالا را به عنوان ضمانت  در قرارداد قرار دهند. به محض اینکه این اتفاق افتاد، پول در قرارداد قفل شده  خواهد ماند تا زمانی که خریدار تأیید کند که کالا را دریافت کرده‌است. پس از آن، مقدار (نیمی از سپرده خود) به خریدار بازگردانده می‌شود و فروشنده سه برابر مقدار (سپرده خود به علاوه مقدار) دریافت می‌کند. ایده پشت این امر این است که هر دو طرف انگیزه‌ای برای حل اوضاع دارند یا در غیر این صورت پول آنها برای همیشه قفل شده خواهد ماند.

البته این قرارداد مشکلی را حل نمی‌کند‌، اما یک نمای کلی از چگونگی استفاده از ساختار ماشین حالت مانند  در داخل قرارداد ارائه می‌دهد.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;
    contract Purchase {
        uint public value;
        address payable public seller;
        address payable public buyer;

        enum State { Created, Locked, Release, Inactive }
        // The state variable has a default value of the first member, `State.created`
        State public state;

        modifier condition(bool condition_) {
            require(condition_);
            _;
        }

        /// Only the buyer can call this function.
        error OnlyBuyer();
        /// Only the seller can call this function.
        error OnlySeller();
        /// The function cannot be called at the current state.
        error InvalidState();
        /// The provided value has to be even.
        error ValueNotEven();

        modifier onlyBuyer() {
            if (msg.sender != buyer)
                revert OnlyBuyer();
            _;
        }

        modifier onlySeller() {
            if (msg.sender != seller)
                revert OnlySeller();
            _;
        }

        modifier inState(State state_) {
            if (state != state_)
                revert InvalidState();
            _;
        }

        event Aborted();
        event PurchaseConfirmed();
        event ItemReceived();
        event SellerRefunded();

        // Ensure that `msg.value` is an even number.
        // Division will truncate if it is an odd number.
        // Check via multiplication that it wasn't an odd number.
        constructor() payable {
            seller = payable(msg.sender);
            value = msg.value / 2;
            if ((2 * value) != msg.value)
                revert ValueNotEven();
        }

        /// Abort the purchase and reclaim the ether.
        /// Can only be called by the seller before
        /// the contract is locked.
        function abort()
            external
            onlySeller
            inState(State.Created)
        {
            emit Aborted();
            state = State.Inactive;
            // We use transfer here directly. It is
            // reentrancy-safe, because it is the
            // last call in this function and we
            // already changed the state.
            seller.transfer(address(this).balance);
        }

        /// Confirm the purchase as buyer.
        /// Transaction has to include `2 * value` ether.
        /// The ether will be locked until confirmReceived
        /// is called.
        function confirmPurchase()
            external
            inState(State.Created)
            condition(msg.value == (2 * value))
            payable
        {
            emit PurchaseConfirmed();
            buyer = payable(msg.sender);
            state = State.Locked;
        }

        /// Confirm that you (the buyer) received the item.
        /// This will release the locked ether.
        function confirmReceived()
            external
            onlyBuyer
            inState(State.Locked)
        {
            emit ItemReceived();
            // It is important to change the state first because
            // otherwise, the contracts called using `send` below
            // can call in again here.
            state = State.Release;

            buyer.transfer(value);
        }

        /// This function refunds the seller, i.e.
        /// pays back the locked funds of the seller.
        function refundSeller()
            external
            onlySeller
            inState(State.Release)
        {
            emit SellerRefunded();
            // It is important to change the state first because
            // otherwise, the contracts called using `send` below
            // can call in again here.
            state = State.Inactive;

            seller.transfer(3 * value);
        }
    }
.. index:: purchase, remote purchase, escrow

********************
خرید امن از راه دور
********************

خرید کالا از راه دور در حال حاضر نیاز به چندین طرف دارد که باید به یکدیگر اعتماد کنند. ساده ترین پیکربندی شامل یک فروشنده و یک خریدار است. خریدار مایل است کالایی را از فروشنده دریافت کند و فروشنده مایل است در ازای آن پول (یا معادل آن) دریافت کند. قسمت مشکل ساز محموله در اینجا است: هیچ راهی برای تعیین اطمینان از رسیدن کالا به خریدار وجود ندارد.

روش‌های مختلفی برای حل این مشکل وجود دارد، اما همه آنها در مقابل یک یا بقیه راه‌ها کم می‌آورند. در مثال زیر، هر دو طرف باید مقدار دو برابر یک قلم کالا را به عنوان ضمانت  در قرارداد قرار دهند. به محض اینکه این اتفاق افتاد، پول در قرارداد قفل شده  خواهد ماند تا زمانی که خریدار تأیید کند که کالا را دریافت کرده‌است. پس از آن، مقدار (نیمی از سپرده خود) به خریدار بازگردانده می‌شود و فروشنده سه برابر مقدار (سپرده خود به علاوه مقدار) دریافت می‌کند. ایده پشت این امر این است که هر دو طرف انگیزه‌ای برای حل اوضاع دارند یا در غیر این صورت پول آنها برای همیشه قفل شده خواهد ماند.

البته این قرارداد مشکلی را حل نمی‌کند‌، اما یک نمای کلی از چگونگی استفاده از ساختار ماشین حالت مانند  در داخل قرارداد ارائه می‌دهد.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;
    contract Purchase {
        uint public value;
        address payable public seller;
        address payable public buyer;

        enum State { Created, Locked, Release, Inactive }
        // The state variable has a default value of the first member, `State.created`
        State public state;

        modifier condition(bool condition_) {
            require(condition_);
            _;
        }

        /// Only the buyer can call this function.
        error OnlyBuyer();
        /// Only the seller can call this function.
        error OnlySeller();
        /// The function cannot be called at the current state.
        error InvalidState();
        /// The provided value has to be even.
        error ValueNotEven();

        modifier onlyBuyer() {
            if (msg.sender != buyer)
                revert OnlyBuyer();
            _;
        }

        modifier onlySeller() {
            if (msg.sender != seller)
                revert OnlySeller();
            _;
        }

        modifier inState(State state_) {
            if (state != state_)
                revert InvalidState();
            _;
        }

        event Aborted();
        event PurchaseConfirmed();
        event ItemReceived();
        event SellerRefunded();

        // Ensure that `msg.value` is an even number.
        // Division will truncate if it is an odd number.
        // Check via multiplication that it wasn't an odd number.
        constructor() payable {
            seller = payable(msg.sender);
            value = msg.value / 2;
            if ((2 * value) != msg.value)
                revert ValueNotEven();
        }

        /// Abort the purchase and reclaim the ether.
        /// Can only be called by the seller before
        /// the contract is locked.
        function abort()
            external
            onlySeller
            inState(State.Created)
        {
            emit Aborted();
            state = State.Inactive;
            // We use transfer here directly. It is
            // reentrancy-safe, because it is the
            // last call in this function and we
            // already changed the state.
            seller.transfer(address(this).balance);
        }

        /// Confirm the purchase as buyer.
        /// Transaction has to include `2 * value` ether.
        /// The ether will be locked until confirmReceived
        /// is called.
        function confirmPurchase()
            external
            inState(State.Created)
            condition(msg.value == (2 * value))
            payable
        {
            emit PurchaseConfirmed();
            buyer = payable(msg.sender);
            state = State.Locked;
        }

        /// Confirm that you (the buyer) received the item.
        /// This will release the locked ether.
        function confirmReceived()
            external
            onlyBuyer
            inState(State.Locked)
        {
            emit ItemReceived();
            // It is important to change the state first because
            // otherwise, the contracts called using `send` below
            // can call in again here.
            state = State.Release;

            buyer.transfer(value);
        }

        /// This function refunds the seller, i.e.
        /// pays back the locked funds of the seller.
        function refundSeller()
            external
            onlySeller
            inState(State.Release)
        {
            emit SellerRefunded();
            // It is important to change the state first because
            // otherwise, the contracts called using `send` below
            // can call in again here.
            state = State.Inactive;

            seller.transfer(3 * value);
        }
    }
