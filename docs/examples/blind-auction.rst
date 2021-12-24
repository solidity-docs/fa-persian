.. index:: auction;blind, auction;open, blind auction, open auction

*************
مزایده کور
*************

در این بخش، نشان خواهیم داد که ایجاد یک قرارداد حراجِ کاملا کور  در اتریوم چقدر آسان است. ما با یک مزایده باز  شروع می‌کنیم که در آن همه می‌توانند پیشنهادات  ارائه شده را ببینند و سپس این قرارداد را به حراج کور توسعه می‌دهیم که در آن امکان مشاهده پیشنهاد واقعی تا زمان پایان مناقصه وجود ندارد.


.. _simple_auction:

مزایده باز ساده
===================

ایده‌ی کلی قرارداد مزایده ساده  زیر این است که همه می‌توانند پیشنهادات خود را در طول یک دوره مناقصه  ارسال کنند. پیشنهادات در حال حاضر شامل ارسال پول/ اتر  به منظور متصل کردن مناقصه‌گرانِ  مناقصه به پیشنهاد آنها است. اگر بالاترین پیشنهاد  افزایش یابد، مناقصه‌گران قبلی پول خود را پس می‌گیرد. پس از پایان دوره مناقصه، قرارداد باید به صورت دستی فراخوانده شود تا ذینفع پول خود را دریافت کند- قراردادها نمی‌توانند خودشان فعال شوند.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;
    contract SimpleAuction {
        // Parameters of the auction. Times are either
        // absolute unix timestamps (seconds since 1970-01-01)
        // or time periods in seconds.
        address payable public beneficiary;
        uint public auctionEndTime;

        // Current state of the auction.
        address public highestBidder;
        uint public highestBid;

        // Allowed withdrawals of previous bids
        mapping(address => uint) pendingReturns;

        // Set to true at the end, disallows any change.
        // By default initialized to `false`.
        bool ended;

        // Events that will be emitted on changes.
        event HighestBidIncreased(address bidder, uint amount);
        event AuctionEnded(address winner, uint amount);

        // Errors that describe failures.

        // The triple-slash comments are so-called natspec
        // comments. They will be shown when the user
        // is asked to confirm a transaction or
        // when an error is displayed.

        /// The auction has already ended.
        error AuctionAlreadyEnded();
        /// There is already a higher or equal bid.
        error BidNotHighEnough(uint highestBid);
        /// The auction has not ended yet.
        error AuctionNotYetEnded();
        /// The function auctionEnd has already been called.
        error AuctionEndAlreadyCalled();

        /// Create a simple auction with `biddingTime`
        /// seconds bidding time on behalf of the
        /// beneficiary address `beneficiaryAddress`.
        constructor(
            uint biddingTime,
            address payable beneficiaryAddress
        ) {
            beneficiary = beneficiaryAddress;
            auctionEndTime = block.timestamp + biddingTime;
        }

        /// Bid on the auction with the value sent
        /// together with this transaction.
        /// The value will only be refunded if the
        /// auction is not won.
        function bid() external payable {
            // No arguments are necessary, all
            // information is already part of
            // the transaction. The keyword payable
            // is required for the function to
            // be able to receive Ether.

            // Revert the call if the bidding
            // period is over.
            if (block.timestamp > auctionEndTime)
                revert AuctionAlreadyEnded();

            // If the bid is not higher, send the
            // money back (the revert statement
            // will revert all changes in this
            // function execution including
            // it having received the money).
            if (msg.value <= highestBid)
                revert BidNotHighEnough(highestBid);

            if (highestBid != 0) {
                // Sending back the money by simply using
                // highestBidder.send(highestBid) is a security risk
                // because it could execute an untrusted contract.
                // It is always safer to let the recipients
                // withdraw their money themselves.
                pendingReturns[highestBidder] += highestBid;
            }
            highestBidder = msg.sender;
            highestBid = msg.value;
            emit HighestBidIncreased(msg.sender, msg.value);
        }

        /// Withdraw a bid that was overbid.
        function withdraw() external returns (bool) {
            uint amount = pendingReturns[msg.sender];
            if (amount > 0) {
                // It is important to set this to zero because the recipient
                // can call this function again as part of the receiving call
                // before `send` returns.
                pendingReturns[msg.sender] = 0;

                if (!payable(msg.sender).send(amount)) {
                    // No need to call throw here, just reset the amount owing
                    pendingReturns[msg.sender] = amount;
                    return false;
                }
            }
            return true;
        }

        /// End the auction and send the highest bid
        /// to the beneficiary.
        function auctionEnd() external {
            // It is a good guideline to structure functions that interact
            // with other contracts (i.e. they call functions or send Ether)
            // into three phases:
            // 1. checking conditions
            // 2. performing actions (potentially changing conditions)
            // 3. interacting with other contracts
            // If these phases are mixed up, the other contract could call
            // back into the current contract and modify the state or cause
            // effects (ether payout) to be performed multiple times.
            // If functions called internally include interaction with external
            // contracts, they also have to be considered interaction with
            // external contracts.

            // 1. Conditions
            if (block.timestamp < auctionEndTime)
                revert AuctionNotYetEnded();
            if (ended)
                revert AuctionEndAlreadyCalled();

            // 2. Effects
            ended = true;
            emit AuctionEnded(highestBidder, highestBid);

            // 3. Interaction
            beneficiary.transfer(highestBid);
        }
    }

مزایده کور
=============

مزایده باز  قبلی در ادامه به مزایده کور  توسعه یافته‌است. مزیت مزایده کور این است که هیچ گونه فشار زمانی نسبت به پایان دوره مناقصه  وجود ندارد. ایجاد مزایده کور بر روی یک پلتفرم محاسباتی شفاف ممکن است متناقض به نظر برسد، اما رمزنگاری به کمک شما می‌آید.

در طول دوره مناقصه، **یک پیشنهاد دهنده** در واقع پیشنهاد  خود را ارسال نمی‌کند، بلکه فقط یک نسخه هش شده از آن را ارسال می‌کند. از آنجا که در حال حاضر یافتن دو مقدار (به اندازه کافی طولانی) که مقادیر هش آنها برابر باشد، عملاً غیرممکن تلقی می‌شود، مناقصه‌گر  با این کار متعهد به مناقصه می‌شود. پس از پایان دوره مناقصه، مناقصه‌گران باید پیشنهادات خود را آشکار کنند: آنها مقادیر خود را بدون رمزگذاری ارسال می‌کنند و قرارداد بررسی می‌کند که مقدار هش همان مقدار ارائه شده در دوره مناقصه است. 

چالش دیگر این است که چگونه مزایده را به طور همزمان **اجباری و پنهان یا کور** جلوه دهید: تنها راه جلوگیری از ارسال نکردن مبلغ توسط داوطلب پس از برنده شدن در مزایده، ارسال آنها به همراه پیشنهاد است. از آنجا که انتقال مقدار در اتریوم  پنهان یا کور نمی‌شود، هر کسی می‌تواند مقدار را ببیند.

 قرارداد زیر با قبول هر مقداری که بزرگتر از بالاترین پیشنهاد  باشد، این مشکل را حل می‌کند. از آنجایی که این فقط در مرحله آشکار شدن قابل بررسی است، ممکن است برخی از پیشنهادات نامعتبر باشند، و این هدفمند است (حتی یک فلَگ  صریح برای قرار دادن پیشنهادات **نامعتبر** با انتقال مقدار بالا ارائه می‌دهد): مناقصه‌گران  با قرار دادن چند پیشنهاد زیاد یا کم اعتبار، می‌توانند رقابت را  مختل کنند.


.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;
    contract BlindAuction {
        struct Bid {
            bytes32 blindedBid;
            uint deposit;
        }

        address payable public beneficiary;
        uint public biddingEnd;
        uint public revealEnd;
        bool public ended;

        mapping(address => Bid[]) public bids;

        address public highestBidder;
        uint public highestBid;

        // Allowed withdrawals of previous bids
        mapping(address => uint) pendingReturns;

        event AuctionEnded(address winner, uint highestBid);

        // Errors that describe failures.

        /// The function has been called too early.
        /// Try again at `time`.
        error TooEarly(uint time);
        /// The function has been called too late.
        /// It cannot be called after `time`.
        error TooLate(uint time);
        /// The function auctionEnd has already been called.
        error AuctionEndAlreadyCalled();

        // Modifiers are a convenient way to validate inputs to
        // functions. `onlyBefore` is applied to `bid` below:
        // The new function body is the modifier's body where
        // `_` is replaced by the old function body.
        modifier onlyBefore(uint time) {
            if (block.timestamp >= time) revert TooLate(time);
            _;
        }
        modifier onlyAfter(uint time) {
            if (block.timestamp <= time) revert TooEarly(time);
            _;
        }

        constructor(
            uint biddingTime,
            uint revealTime,
            address payable beneficiaryAddress
        ) {
            beneficiary = beneficiaryAddress;
            biddingEnd = block.timestamp + biddingTime;
            revealEnd = biddingEnd + revealTime;
        }

        /// Place a blinded bid with `blindedBid` =
        /// keccak256(abi.encodePacked(value, fake, secret)).
        /// The sent ether is only refunded if the bid is correctly
        /// revealed in the revealing phase. The bid is valid if the
        /// ether sent together with the bid is at least "value" and
        /// "fake" is not true. Setting "fake" to true and sending
        /// not the exact amount are ways to hide the real bid but
        /// still make the required deposit. The same address can
        /// place multiple bids.
        function bid(bytes32 blindedBid)
            external
            payable
            onlyBefore(biddingEnd)
        {
            bids[msg.sender].push(Bid({
                blindedBid: blindedBid,
                deposit: msg.value
            }));
        }

        /// Reveal your blinded bids. You will get a refund for all
        /// correctly blinded invalid bids and for all bids except for
        /// the totally highest.
        function reveal(
            uint[] calldata values,
            bool[] calldata fakes,
            bytes32[] calldata secrets
        )
            external
            onlyAfter(biddingEnd)
            onlyBefore(revealEnd)
        {
            uint length = bids[msg.sender].length;
            require(values.length == length);
            require(fakes.length == length);
            require(secrets.length == length);

            uint refund;
            for (uint i = 0; i < length; i++) {
                Bid storage bidToCheck = bids[msg.sender][i];
                (uint value, bool fake, bytes32 secret) =
                        (values[i], fakes[i], secrets[i]);
                if (bidToCheck.blindedBid != keccak256(abi.encodePacked(value, fake, secret))) {
                    // Bid was not actually revealed.
                    // Do not refund deposit.
                    continue;
                }
                refund += bidToCheck.deposit;
                if (!fake && bidToCheck.deposit >= value) {
                    if (placeBid(msg.sender, value))
                        refund -= value;
                }
                // Make it impossible for the sender to re-claim
                // the same deposit.
                bidToCheck.blindedBid = bytes32(0);
            }
            payable(msg.sender).transfer(refund);
        }

        /// Withdraw a bid that was overbid.
        function withdraw() external {
            uint amount = pendingReturns[msg.sender];
            if (amount > 0) {
                // It is important to set this to zero because the recipient
                // can call this function again as part of the receiving call
                // before `transfer` returns (see the remark above about
                // conditions -> effects -> interaction).
                pendingReturns[msg.sender] = 0;

                payable(msg.sender).transfer(amount);
            }
        }

        /// End the auction and send the highest bid
        /// to the beneficiary.
        function auctionEnd()
            external
            onlyAfter(revealEnd)
        {
            if (ended) revert AuctionEndAlreadyCalled();
            emit AuctionEnded(highestBidder, highestBid);
            ended = true;
            beneficiary.transfer(highestBid);
        }

        // This is an "internal" function which means that it
        // can only be called from the contract itself (or from
        // derived contracts).
        function placeBid(address bidder, uint value) internal
                returns (bool success)
        {
            if (value <= highestBid) {
                return false;
            }
            if (highestBidder != address(0)) {
                // Refund the previously highest bidder.
                pendingReturns[highestBidder] += highestBid;
            }
            highestBid = value;
            highestBidder = bidder;
            return true;
        }
    }
