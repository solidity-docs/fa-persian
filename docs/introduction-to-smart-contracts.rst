###############################
مقدمه‌ای بر قراد‌های هوشمند 
###############################

.. _simple-smart-contract:

***********************
یک قرارداد هوشمند ساده 
***********************

بیایید با یک مثال ابتدایی شروع کنیم که مقدار یک متغیر را تعیین می‌کند و آن را در معرض دسترسی سایر قراردادها قرار می‌دهد. اینکه الان شما متوجه چیزی نمی‌شوید طبیعی می‌باشد، بعداً به جزئیات بیشتری خواهیم پرداخت.

مثال ذخیره سازی
===============

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;

    contract SimpleStorage {
        uint storedData;

        function set(uint x) public {
            storedData = x;
        }

        function get() public view returns (uint) {
            return storedData;
        }
    }

خط اول به شما می‌گوید کد منبع ، تحت مجوز GPL نسخه 3.0 می‌باشد. در جایی که انتشار کد منبع به صورت پیشفرض  باشد، مشخص کننده‌هایِ مجوز قابلِ خواندن توسطِ ماشین  مهم هستند.
خط بعدی مشخص می‌کند کد منبع برای سالیدیتی نسخه 0.4.16 یا نسخه جدیدتر زبان نوشته شده است، اما شامل نسخه 0.9.0 نمی‌باشد. بخاطر اینکه قرارداد هوشمند می‌تواند رفتار متفاوتی داشته ‌باشد، به این هدف که اطمینان حاصل شود قرارداد هوشمند نتواند با نسخه جدید کامپایلر، کامپایل شود. :ref:`پراگما <pragma>` (Pragmas) دستورالعمل‌های رایج برای کامپایلرها در مورد نحوه برخورد با کد منبع می‌باشند (به عنوان مثال  `پراگما یکبار  (pragma once) <https://en.wikipedia.org/wiki/Pragma_once>`_ ). 

قراردادها در سالیدتی به معنای مجموعه‌ای از کد (*توابع* آن‌ها) و داده‌ها (*حالت* آن‌ها) است که در یک آدرس خاص در بلاکچین اتریوم قرار دارند. خط ``;uint storedData``  یک متغیر حالت  به نام ``storedData``  از نوع ``uint``  (عددِ صحیحِ بدونِ علامت  256 بیتی) را مشخص می‌کند. می‌توان آن را به عنوان یک اسلات  در پایگاه داده در نظر بگیرید که می‌توانید با فراخوانی توابع کدی که پایگاه داده را مدیریت می‌کند، آن‌ها را جستوجو کرده و ویرایش کنید. در این مثال، قرارداد توابع  ``set`` و ``get``  را تعریف می‌کند که  برای ویرایش  یا بازیابی  مقدار متغیر استفاده شود.

همانند سایر زبان‌‌های رایج، برای دسترسی به یک متغیر حالت، نیازی به پیشوند ``.this``  ندارید.
 این قرارداد جدا از اینکه هنوز کار زیادی انجام نداده‌است (به دلیل زیرساخت‌های ساخته شده توسط اتریوم) اجازه می‌دهد هر شخص یک عدد را ذخیره کند، که این عدد توسط هر شخصی در دنیا بدون هیچ روش امکان پذیر برای جلوگیری از انتشار آن قابل دسترس می‌باشد. هر شخصی می‌تواند مجدداً تابع ``set``   را فراخوانی کند و عدد را رونویسی کند، اما عدد در تاریخچه‌ِ بلاکچین هنوز ذخیره بماند. بعداً خواهید دید که چگونه می‌توانید محدودیت‌های دسترسی را اعمال کنید تا فقط شما بتوانید عدد را تغییر دهید. 


.. warning::
    در هنگام استفاده از متن Unicode مراقب باشید، زیرا نویسه‌های  مشابه (یا حتی یکسان) می‌توانند دارای نکته‌های کدی  متفاوتی باشند و همینطور به عنوان یک آرایه بایت  متفاوت کدگذاری شوند.

.. note::
    همه شناسه‌ها  (نام قرارداد، نام تابع و نام متغیر) به مجموعه کاراکترهای ASCII محدود می‌‌شوند. ذخیره داده‌های رمزگذاری شده UTF-8 در متغیرهای رشته‌ای  امکان پذیر است.

.. index:: ! subcurrency

مثال  ساب کارنسی یا زیر ارز
===================

قرارداد زیر ساده ترین شکل ارز رمزنگاری شده  را پیاده سازی می‌کند. این قرارداد فقط به سازنده آن اجازه می‌دهد سکه‌ های جدید ایجاد کند (طرح‌های مختلف صدور امکان پذیر است). هر کسی می‌تواند بدون نیاز به ثبت نام با نام کاربری و رمز عبور، سکه برای یکدیگر ارسال کنند، تمام آنچه شما نیاز دارید یک جفت کلید اتریوم است.

.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.4;

    contract Coin {
        // The keyword "public" makes variables
        // accessible from other contracts
        address public minter;
        mapping (address => uint) public balances;

        // Events allow clients to react to specific
        // contract changes you declare
        event Sent(address from, address to, uint amount);

        // Constructor code is only run when the contract
        // is created
        constructor() {
            minter = msg.sender;
        }

        // Sends an amount of newly created coins to an address
        // Can only be called by the contract creator
        function mint(address receiver, uint amount) public {
            require(msg.sender == minter);
            balances[receiver] += amount;
        }

        // Errors allow you to provide information about
        // why an operation failed. They are returned
        // to the caller of the function.
        error InsufficientBalance(uint requested, uint available);

        // Sends an amount of existing coins
        // from any caller to an address
        function send(address receiver, uint amount) public {
            if (amount > balances[msg.sender])
                revert InsufficientBalance({
                    requested: amount,
                    available: balances[msg.sender]
                });

            balances[msg.sender] -= amount;
            balances[receiver] += amount;
            emit Sent(msg.sender, receiver, amount);
        }
    }

این قرارداد مفاهیم جدیدی را معرفی می‌کند، اجازه دهید یکی یکی آنها را مرور کنیم.



خط ``;address public minter``  متغیر حالت از نوع  :ref:`address<address>` را مشخص می‌کند. نوع آدرس یک مقدار 160 بیتی است که اجازه هیچ گونه عملیات حسابی را نمی‌دهد. متغییر ``address`` برای ذخیره آدرس‌ قرارداد‌ها یا یک هش از نیمه عمومیِ  یک جفت کلید  متعلق به :ref:`حساب‌های خارجی <accounts>` مناسب است.

کلمه کلیدی ``public`` به طور خودکار تابعی را ایجاد می‌کند که به شما امکان می‌دهد، از خارج از قرارداد به مقدار فعلی متغیر حالت  دسترسی پیدا کنید. بدون این کلمه کلیدی، سایر قراردادها راهی برای دسترسی به متغیر ندارند. کد تابع که توسط کامپایلر تولید می‌شود معادل موارد زیر است (فعلاً از  ``external`` و  ``view`` چشم پوشی کنید):

.. code-block:: solidity

    function minter() external view returns (address) { return minter; }

می‌توانید توابعی مانند موارد فوق را خود اضافه کنید، اما یک متغیرحالت و تابعی با همان نام خواهید داشت. اما نیازی به این کار نیست، کامپایلر آن را برای شما مشخص می‌کند.

.. index:: mapping

خط بعدی، ``;mapping (address => uint) public balances`` یک متغیر ِحالتِ عمومی  ایجاد می‌کند، اما یک نوع داده  پیچیده‌تر است. نوع :ref:`mapping <mapping-types>`  آدرس‌ها را به اعداد صحیح بدون علامت  (:ref:`unsigned integers <integers>`) نگاشت  می‌کند.

Mapping ‌ها را می‌توان به عنوان `جداول هش  <https://en.wikipedia.org/wiki/Hash_table>`_ مشاهده کرد که عملاً مقداردهی اولیه شده‌اند، به طوری که همه کلیدهای ممکن از همان ابتدا وجود داشته و به مقداری که همهِ نمایشِ بایت  آن‌ها صفر است نگاشت شده باشند. با این حال، نه می‌توان لیستی از تمام کلیدهای Mapping و نه لیستی از تمام مقادیر را بدست آورد. آنچه را که به Mapping اضافه کرده‌اید، ثبت کنید یا از آن در زمینه‌ای که نیازی به آن مقدار نیست استفاده کنید. یا حتی بهتر است که یک لیست نگهدارید یا از نوع داده  مناسب استفاده کنید.

:ref:`تابع getter <getter-functions>`  ایجاد شده توسط کلمه کلیدی ``public``  در Mapping پیچیده‌تر است. به شرح زیر است:

.. code-block:: solidity

    function balances(address _account) external view returns (uint) {
        return balances[_account];
    }

شما می‌توانید برای جستجوی بالانس  یک حساب از این تابع استفاده کنید.

.. index:: event

خط ``;event Sent(address from, address to, uint amount)`` یک :ref:`"event" <events>`  را مشخص می‌کند که در آخرین خط با تابع ``send``  منتشر می‌شود. کلاینت اتریوم مانند برنامه‌های کاربردی  وب می‌توانند به این رویداد ها که در بلاکچین منتشر شده‌اند، بدون هزینه زیاد گوش دهند. شنونده به محض انتشار، آرگومان‌های ``from`` ، ``to`` و ``amount`` را دریافت می‌کند، که امکان ردیابی تراکنش‌ها را فراهم می‌کند.

برای گوش دادن به این event، می‌توانید از کد جاوا اسکریپت زیر استفاده کنید که از `web3.js <https://github.com/ethereum/web3.js/>`_  برای ایجاد شیء قرارداد ``Coin``   استفاده می‌کند و هر رابط کاربری تابع ``balances``   که به صورت خودکار ایجاد شده را از بالا فراخوانی می‌کند::

    Coin.Sent().watch({}, '', function(error, result) {
        if (!error) {
            console.log("Coin transfer: " + result.args.amount +
                " coins were sent from " + result.args.from +
                " to " + result.args.to + ".");
            console.log("Balances now:\n" +
                "Sender: " + Coin.balances.call(result.args.from) +
                "Receiver: " + Coin.balances.call(result.args.to));
        }
    })

.. index:: coin

:ref:`constructor<constructor>`  یک تابع خاص است که در هنگام ایجاد قرارداد اجرا می‌شود و پس از آن نمی‌توان آن را فراخوانی کرد. در این مورد، constructor آدرس شخص ایجاد کننده قرارداد را برای همیشه ذخیره می‌کند. متغیر ``msg``   (همراه با  ``tx`` و  ``block``) یک :ref:`متغیر جهانی خاص  <special-variables-functions>`   که شامل خصوصیاتی می‌باشد و امکان دسترسی به بلاکچین را فراهم می‌کند. ``msg.sender``  همیشه آدرسی است که فراخوانی تابع فعلی (خارجی ) از آن گرفته شده‌است.

توابعی که قرارداد را تشکیل می‌دهند و کاربران و قراردادها می‌توانند آنها را فراخوانی کنند، ``mint`` و ``send``   هستند.

The ``mint`` function sends an amount of newly created coins to another address. The :ref:`require
<assert-and-require>` function call defines conditions that reverts all changes if not met. In this
example, ``require(msg.sender == minter);`` ensures that only the creator of the contract can call
``mint``. In general, the creator can mint as many tokens as they like, but at some point, this will
lead to a phenomenon called "overflow". Note that because of the default :ref:`Checked arithmetic
<unchecked>`, the transaction would revert if the expression ``balances[receiver] += amount;``
overflows, i.e., when ``balances[receiver] + amount`` in arbitrary precision arithmetic is larger
than the maximum value of ``uint`` (``2**256 - 1``). This is also true for the statement
``balances[receiver] += amount;`` in the function ``send``.


:ref:`خطاها <errors>`  به شما امکان می‌دهند اطلاعات بیشتری در مورد علت شرایط یا شکست عملیات به فراخوانی کننده  ارائه دهید. خطاها همراه با  :ref:`دستورات revert <revert-statement>`   استفاده می‌شوند. دستورات revert بدون تغییر و بدون قید و شرط، تمام تغییرات مشابه با تابع ``require``  را نابود و برمی‌گردانند ، اما همچنین به شما امکان ارائه نام خطا و داده‌های اضافی را که به فراخوانی کننده (و در نهایت به برنامه سمت کاربر  یا جستوجوگر بلاک ) نشان دهید، را می‌دهند. به طوری که یک شکست  را می‌توان به راحتی عیب یابی  کرد یا به آن واکنش نشان داد.


The ``send`` function can be used by anyone (who already
has some of these coins) to send coins to anyone else. If the sender does not have
enough coins to send, the ``if`` condition evaluates to true. As a result, the ``revert`` will cause the operation to fail
while providing the sender with error details using the ``InsufficientBalance`` error.

.. note::
   اگر از این قرارداد برای ارسال سکه به یک آدرس استفاده کنید، وقتی به آن آدرس در یک مرورگر بلاکچین نگاه کنید، چیزی مشاهده نخواهید کرد. زیرا تاریخچه ارسال سکه و بالانس تغییر یافته و فقط در فضای ذخیره سازی داده  این قراردادِ خاصِ سکه ذخیره می‌شود. با استفاده از رویداد ها، می‌توانید یک "جستجوگر بلاکچینی" ایجاد کنید که تراکنش‌ها و بالانس‌های سکه جدید شما را ردیابی می‌کند، اما باید آدرس قرارداد سکه و نه آدرس صاحبان سکه را بررسی کنید.

.. _blockchain-basics:

*****************
مبانی بلاکچین
*****************
درک بلاکچین به عنوان یک مفهوم برای برنامه نویسان خیلی دشوار نمی‌باشد. به این دلیل که بیشتر پیچیدگی در مفهوم (استخراج ، `هش کردن <https://en.wikipedia.org/wiki/Cryptographic_hash_function>`_ ، `رمزنگاری منحنی بیضوی <https://en.wikipedia.org/wiki/Elliptic_curve_cryptography>`_  ، `شبکه‌های همتا به همتا <https://en.wikipedia.org/wiki/Peer-to-peer>`_  و غیره) می‌باشد که فقط برای ارائه مجموعه خاصی از ویژگی‌ها و وعده‌ها برای پلتفرم می‌باشد. پس از پذیرش این ویژگی‌ها، دیگر لازم نیست نگران فناوری زیر ساخت باشید - یا برای استفاده از آن باید بدانید که AWS آمازون چگونه کار می‌کند؟

.. index:: transaction

تراکنش‌ها 
============

بلاکچین یک پایگاه داده تراکنشی  مشترک جهانی  است. این بدان معناست که هر کس فقط با شرکت در شبکه می‌تواند ورودی‌های پایگاه داده را بخواند. اگر می‌خواهید چیزی را در پایگاه داده تغییر دهید، باید به اصطلاح، تراکنش ایجاد کنید، باید توسط دیگران پذیرفته شود. کلمه تراکنش به تغییری که می‌خواهید ایجاد کنید که یا اصلاً انجام نشده یا کاملاً اعمال شده اشاره دارد (فرض کنید می‌خواهید همزمان دو مقدار را تغییر دهید). علاوه بر این، زمانی که تراکنش شما در پایگاه داده اعمال می‌شود، هیچ تراکنش دیگری نمی‌تواند آن را تغییر دهد.

به عنوان مثال، جدولی را تصور کنید که بالانس تمام حساب‌ها را در یک ارز الکترونیکی  فهرست می‌کند. اگر انتقال از یک حساب به حساب دیگر درخواست شود، ماهیت تراکنشی پایگاه داده تضمین می‌کند که اگر مبلغ از یک حساب کم شود، همیشه به حساب دیگر اضافه می‌شود. اگر به هر دلیلی، افزودن مبلغ به حساب مقصد  امکان پذیر نباشد، حساب مبدأ  نیز ویرایش نمی‌شود.

علاوه بر این، یک تراکنش همیشه به صورت رمزنگاری توسط فرستنده (سازنده ) امضا می‌شود. این امر باعث می‌شود محافظت از دسترسی به تغییرات خاص پایگاه داده آسان باشد. در مثال ارز الکترونیکی ، یک بررسی ساده تضمین می‌کند که فقط شخصی که کلیدهای حساب را دارد می‌تواند از آن پول انتقال بدهد.

.. index:: ! block

بلاک‌ها  
======

یک مانع عمده برای غلبه بر چیزی که (در اصطلاحات بیتکوین) "حمله دو بار خرج کردن  " نامیده می‌شود : اگر دو تراکنش در شبکه وجود داشته باشد که هر دو بخواهند یک حساب را خالی کنند چه اتفاقی می‌افتد؟ فقط یکی از تراکنش‌ها می‌تواند معتبر باشد، به طور معمول تراکنشی که ابتدا پذیرفته می‌شود. مسئله این است که "First" یک اصطلاح  عَملی در یک شبکه همتا به همتا  نیست.

پاسخ خلاصه این است که شما نیاز ندارید مراقبت باشید. یک ترتیب از تراکنش‌های پذیرفته شده به صورت جهانی برای شما انتخاب می‌شود، که اختلافات را حل می‌کند. تراکنش‌ها به صورت چیزی که "بلاک" نام دارد، بسته و سپس اجرا می‌شوند و در بین گره‌های مشارکت کننده توزیع می‌شوند. اگر دو تراکنش با یکدیگر مغایرت داشته باشند، تراکنشی که در نهایت دوم شود رد می‌شود و بخشی از بلاک نمی‌شود.

این بلاک‌ها از نظر زمانی یک توالی خطی  را تشکیل می‌دهند و بخاطر همین است که کلمه "بلاکچین" از آن گرفته می‌شود. بلاک‌ها در فواصل نسبتاً منظمی به زنجیره اضافه می‌شوند - برای اتریوم تقریباً هر 17 ثانیه می‌باشد.

به عنوان بخشی از "مکانیزم انتخاب ترتیبی  " (که "استخراج " نامیده می‌شود) ممکن است فقط در "نوک " زنجیره، برگرداندن بلاک‌ها هرزگاهی اتفاق بیفتد. هرچه تعداد بلاک‌های اضافه شده در بالای یک بلاکِ خاص بیشتر باشد، احتمال برگردانندن آن بلاک کمتر است. بنابراین ممکن است تراکنش شما برگردانده شود و حتی از بلاکچین حذف شود، اما هرچه بیشتر منتظر بمانید، احتمال آن کمتر است.

.. note::
    تضمین نمی‌شود که تراکنش در بلاک بعدی یا هر بلاک مشخص خاص در آینده لحاظ شود، زیرا این کار به عهده ارسال کننده نمی‌باشد، بلکه ماینرها باید تعیین کنند که تراکنش در کدام بلاک لحاظ شود. 
    
    اگر می‌خواهید فراخوانی قراردادتان را در آینده زمان بندی کنید، می توانید از `alarm clock <https://www.ethereum-alarm-clock.com/>`_  یا سرویس اوراکل مشابه استفاده کنید.


.. _the-ethereum-virtual-machine:

.. index:: !evm, ! ethereum virtual machine

****************************
The Ethereum Virtual Machine
****************************

Overview
========

The Ethereum Virtual Machine or EVM is the runtime environment
for smart contracts in Ethereum. It is not only sandboxed but
actually completely isolated, which means that code running
inside the EVM has no access to network, filesystem or other processes.
Smart contracts even have limited access to other smart contracts.

.. index:: ! account, address, storage, balance

.. _accounts:

Accounts
========

There are two kinds of accounts in Ethereum which share the same
address space: **External accounts** that are controlled by
public-private key pairs (i.e. humans) and **contract accounts** which are
controlled by the code stored together with the account.

The address of an external account is determined from
the public key while the address of a contract is
determined at the time the contract is created
(it is derived from the creator address and the number
of transactions sent from that address, the so-called "nonce").

Regardless of whether or not the account stores code, the two types are
treated equally by the EVM.

Every account has a persistent key-value store mapping 256-bit words to 256-bit
words called **storage**.

Furthermore, every account has a **balance** in
Ether (in "Wei" to be exact, ``1 ether`` is ``10**18 wei``) which can be modified by sending transactions that
include Ether.

.. index:: ! transaction

Transactions
============

A transaction is a message that is sent from one account to another
account (which might be the same or empty, see below).
It can include binary data (which is called "payload") and Ether.

If the target account contains code, that code is executed and
the payload is provided as input data.

If the target account is not set (the transaction does not have
a recipient or the recipient is set to ``null``), the transaction
creates a **new contract**.
As already mentioned, the address of that contract is not
the zero address but an address derived from the sender and
its number of transactions sent (the "nonce"). The payload
of such a contract creation transaction is taken to be
EVM bytecode and executed. The output data of this execution is
permanently stored as the code of the contract.
This means that in order to create a contract, you do not
send the actual code of the contract, but in fact code that
returns that code when executed.

.. note::
  While a contract is being created, its code is still empty.
  Because of that, you should not call back into the
  contract under construction until its constructor has
  finished executing.

.. index:: ! gas, ! gas price

Gas
===

Upon creation, each transaction is charged with a certain amount of **gas**,
whose purpose is to limit the amount of work that is needed to execute
the transaction and to pay for this execution at the same time. While the EVM executes the
transaction, the gas is gradually depleted according to specific rules.

The **gas price** is a value set by the creator of the transaction, who
has to pay ``gas_price * gas`` up front from the sending account.
If some gas is left after the execution, it is refunded to the creator in the same way.

If the gas is used up at any point (i.e. it would be negative),
an out-of-gas exception is triggered, which reverts all modifications
made to the state in the current call frame.

.. index:: ! storage, ! memory, ! stack

Storage, Memory and the Stack
=============================

The Ethereum Virtual Machine has three areas where it can store data-
storage, memory and the stack, which are explained in the following
paragraphs.

Each account has a data area called **storage**, which is persistent between function calls
and transactions.
Storage is a key-value store that maps 256-bit words to 256-bit words.
It is not possible to enumerate storage from within a contract, it is
comparatively costly to read, and even more to initialise and modify storage. Because of this cost,
you should minimize what you store in persistent storage to what the contract needs to run.
Store data like derived calculations, caching, and aggregates outside of the contract.
A contract can neither read nor write to any storage apart from its own.

The second data area is called **memory**, of which a contract obtains
a freshly cleared instance for each message call. Memory is linear and can be
addressed at byte level, but reads are limited to a width of 256 bits, while writes
can be either 8 bits or 256 bits wide. Memory is expanded by a word (256-bit), when
accessing (either reading or writing) a previously untouched memory word (i.e. any offset
within a word). At the time of expansion, the cost in gas must be paid. Memory is more
costly the larger it grows (it scales quadratically).

The EVM is not a register machine but a stack machine, so all
computations are performed on a data area called the **stack**. It has a maximum size of
1024 elements and contains words of 256 bits. Access to the stack is
limited to the top end in the following way:
It is possible to copy one of
the topmost 16 elements to the top of the stack or swap the
topmost element with one of the 16 elements below it.
All other operations take the topmost two (or one, or more, depending on
the operation) elements from the stack and push the result onto the stack.
Of course it is possible to move stack elements to storage or memory
in order to get deeper access to the stack,
but it is not possible to just access arbitrary elements deeper in the stack
without first removing the top of the stack.

.. index:: ! instruction

Instruction Set
===============

The instruction set of the EVM is kept minimal in order to avoid
incorrect or inconsistent implementations which could cause consensus problems.
All instructions operate on the basic data type, 256-bit words or on slices of memory
(or other byte arrays).
The usual arithmetic, bit, logical and comparison operations are present.
Conditional and unconditional jumps are possible. Furthermore,
contracts can access relevant properties of the current block
like its number and timestamp.

For a complete list, please see the :ref:`list of opcodes <opcodes>` as part of the inline
assembly documentation.

.. index:: ! message call, function;call

Message Calls
=============

Contracts can call other contracts or send Ether to non-contract
accounts by the means of message calls. Message calls are similar
to transactions, in that they have a source, a target, data payload,
Ether, gas and return data. In fact, every transaction consists of
a top-level message call which in turn can create further message calls.

A contract can decide how much of its remaining **gas** should be sent
with the inner message call and how much it wants to retain.
If an out-of-gas exception happens in the inner call (or any
other exception), this will be signaled by an error value put onto the stack.
In this case, only the gas sent together with the call is used up.
In Solidity, the calling contract causes a manual exception by default in
such situations, so that exceptions "bubble up" the call stack.

As already said, the called contract (which can be the same as the caller)
will receive a freshly cleared instance of memory and has access to the
call payload - which will be provided in a separate area called the **calldata**.
After it has finished execution, it can return data which will be stored at
a location in the caller's memory preallocated by the caller.
All such calls are fully synchronous.

Calls are **limited** to a depth of 1024, which means that for more complex
operations, loops should be preferred over recursive calls. Furthermore,
only 63/64th of the gas can be forwarded in a message call, which causes a
depth limit of a little less than 1000 in practice.

.. index:: delegatecall, callcode, library

Delegatecall / Callcode and Libraries
=====================================

There exists a special variant of a message call, named **delegatecall**
which is identical to a message call apart from the fact that
the code at the target address is executed in the context of the calling
contract and ``msg.sender`` and ``msg.value`` do not change their values.

This means that a contract can dynamically load code from a different
address at runtime. Storage, current address and balance still
refer to the calling contract, only the code is taken from the called address.

This makes it possible to implement the "library" feature in Solidity:
Reusable library code that can be applied to a contract's storage, e.g. in
order to implement a complex data structure.

.. index:: log

Logs
====

It is possible to store data in a specially indexed data structure
that maps all the way up to the block level. This feature called **logs**
is used by Solidity in order to implement :ref:`events <events>`.
Contracts cannot access log data after it has been created, but they
can be efficiently accessed from outside the blockchain.
Since some part of the log data is stored in `bloom filters <https://en.wikipedia.org/wiki/Bloom_filter>`_, it is
possible to search for this data in an efficient and cryptographically
secure way, so network peers that do not download the whole blockchain
(so-called "light clients") can still find these logs.

.. index:: contract creation

Create
======

Contracts can even create other contracts using a special opcode (i.e.
they do not simply call the zero address as a transaction would). The only difference between
these **create calls** and normal message calls is that the payload data is
executed and the result stored as code and the caller / creator
receives the address of the new contract on the stack.

.. index:: selfdestruct, self-destruct, deactivate

Deactivate and Self-destruct
============================

The only way to remove code from the blockchain is when a contract at that
address performs the ``selfdestruct`` operation. The remaining Ether stored
at that address is sent to a designated target and then the storage and code
is removed from the state. Removing the contract in theory sounds like a good
idea, but it is potentially dangerous, as if someone sends Ether to removed
contracts, the Ether is forever lost.

.. warning::
    Even if a contract is removed by ``selfdestruct``, it is still part of the
    history of the blockchain and probably retained by most Ethereum nodes.
    So using ``selfdestruct`` is not the same as deleting data from a hard disk.

.. note::
    Even if a contract's code does not contain a call to ``selfdestruct``,
    it can still perform that operation using ``delegatecall`` or ``callcode``.

If you want to deactivate your contracts, you should instead **disable** them
by changing some internal state which causes all functions to revert. This
makes it impossible to use the contract, as it returns Ether immediately.
