.. index:: ! contract;creation, constructor

******************
ایجاد قراردادها
******************
قراردادها را می‌توان "از خارج" از طریق تراکنش‌های اتریوم یا از طریق قراردادهای سالیدیتی ایجاد کرد.


IDE هایی، مانند `Remix <https://remix.ethereum.org/>`_ ، فرآیند ایجاد را با استفاده از المان‌های UI انجام می‌دهند.


یکی از راه‌های ایجاد قراردادها به صورت برنامه‌ای در اتریوم، استفاده از API جاوا اسکریپت `web3.js <https://github.com/ethereum/web3.js>`_ است. 
یک تابع به نام  `web3.eth.Contract <https://web3js.readthedocs.io/en/1.0/web3-eth-contract.html#new-contract>`_ برای تسهیل ایجاد قرارداد را دارد.


هنگامی که یک قرارداد ایجاد می‌شود، :ref:`constructor <constructor>` آن (تابع اعلام شده با کلمه کلیدی ``constructor`` ) 
یک بار اجرا می‌شود.


کانستراکتور اختیاری است. فقط یک کانستراکتور مجاز است، به این معنی که overloading پشتیبانی نمی‌شود.



پس از اجرای کانستراکتور، کد نهایی قرارداد بر روی بلاکچین ذخیره می‌شود. این کد شامل تمام توابع عمومی و 
خارجی و همه توابعی است که از طریق فراخوانی تابع از آنجا قابل دسترسی است. کد به کار رفته شامل کد 
کانستراکتور یا توابع داخلی نیست که فقط از سازنده فراخوانی می‌شوند.


.. index:: constructor;arguments

در داخل، آرگومان‌های کانستراکتور :ref:`ABI encoded <ABI>` پس از کد خود قرارداد ارسال می‌شوند، اما اگر از 
``web3.js`` استفاده می‌کنید لازم نیست به این موضوع اهمیت دهید.

اگر یک قرارداد می‌خواهد قرارداد دیگری ایجاد کند، سورس‌کد (و باینری) قرارداد ایجاد شده باید برای کانستراکتور 
شناخته شود. این بدان معنی است که وابستگی ایجاد چرخه‌ای غیرممکن است.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.22 <0.9.0;


    contract OwnedToken {
        // `TokenCreator` is a contract type that is defined below.
        // It is fine to reference it as long as it is not used
        // to create a new contract.
        TokenCreator creator;
        address owner;
        bytes32 name;

        // This is the constructor which registers the
        // creator and the assigned name.
        constructor(bytes32 _name) {
            // State variables are accessed via their name
            // and not via e.g. `this.owner`. Functions can
            // be accessed directly or through `this.f`,
            // but the latter provides an external view
            // to the function. Especially in the constructor,
            // you should not access functions externally,
            // because the function does not exist yet.
            // See the next section for details.
            owner = msg.sender;

            // We perform an explicit type conversion from `address`
            // to `TokenCreator` and assume that the type of
            // the calling contract is `TokenCreator`, there is
            // no real way to verify that.
            // This does not create a new contract.
            creator = TokenCreator(msg.sender);
            name = _name;
        }

        function changeName(bytes32 newName) public {
            // Only the creator can alter the name.
            // We compare the contract based on its
            // address which can be retrieved by
            // explicit conversion to address.
            if (msg.sender == address(creator))
                name = newName;
        }

        function transfer(address newOwner) public {
            // Only the current owner can transfer the token.
            if (msg.sender != owner) return;

            // We ask the creator contract if the transfer
            // should proceed by using a function of the
            // `TokenCreator` contract defined below. If
            // the call fails (e.g. due to out-of-gas),
            // the execution also fails here.
            if (creator.isTokenTransferOK(owner, newOwner))
                owner = newOwner;
        }
    }


    contract TokenCreator {
        function createToken(bytes32 name)
            public
            returns (OwnedToken tokenAddress)
        {
            // Create a new `Token` contract and return its address.
            // From the JavaScript side, the return type
            // of this function is `address`, as this is
            // the closest type available in the ABI.
            return new OwnedToken(name);
        }

        function changeName(OwnedToken tokenAddress, bytes32 name) public {
            // Again, the external type of `tokenAddress` is
            // simply `address`.
            tokenAddress.changeName(name);
        }

        // Perform checks to determine if transferring a token to the
        // `OwnedToken` contract should proceed
        function isTokenTransferOK(address currentOwner, address newOwner)
            public
            pure
            returns (bool ok)
        {
            // Check an arbitrary condition to see if transfer should proceed
            return keccak256(abi.encodePacked(currentOwner, newOwner))[0] == 0x7f;
        }
    }
