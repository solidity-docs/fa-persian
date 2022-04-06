.. _metadata:

#################
فراداده قرارداد (Contract Metadata)
#################

.. index:: metadata, contract verification

کامپایلر سالیدیتی به طور خودکار یک فایل JSON، فراداده قرارداد، که حاوی اطلاعاتی درباره قرارداد کامپایل 
شده است، تولید می کند. می توانید از این فایل برای query یا جستجو از نسخه کامپایلر، منابع استفاده 
شده، اسناد ABI و NatSpec برای تعامل ایمن تر با قرارداد و تأیید کد منبع آن استفاده کنید.

کامپایلر به طور پیش‌فرض هش IPFS فایل فراداده را به انتهای بایت کد (برای جزئیات، در زیر ببینید) هر 
قرارداد اضافه می‌کند، به طوری که می‌توانید فایل را به روشی احراز هویت شده و بدون نیاز به اتصال به یک 
ارائه‌دهنده داده متمرکز بازیابی کنید. گزینه های دیگر موجود عبارتند از هش Swarm و عدم الحاق هش 
ابرداده به بایت کد. این میتواند با :ref:`Standard JSON Interface<compiler-api>` پیکربندی شود.


شما باید فایل فراداده را در IPFS، سوارم(Swarm) یا سرویس دیگری منتشر کنید تا دیگران بتوانند به آن 
دسترسی داشته باشند. شما فایل را با استفاده از دستور ``solc --metadata`` ایجاد می کنید که فایلی به نام 
``ContractName_meta.json`` تولید می کند. این شامل IPFS و ارجاعات Swarm به کد منبع است، 
بنابراین شما باید تمام فایل های منبع و فایل فراداده را آپلود کنید.



فایل فراداده دارای فرمت زیر است. مثال زیر به روشی قابل خواندن برای انسان ارائه شده است. فراداده هایی 
که به درستی قالب بندی شده اند باید از نقل قول ها به درستی استفاده کنند، فضای خالی را به حداقل 
برسانند و کلیدهای همه اشیاء را مرتب کنند تا به یک قالب بندی منحصر به فرد برسند. نظرات در اینجا فقط 
برای اهداف توضیحی مجاز نیست و استفاده می شود.


.. code-block:: javascript

    {
      // Required: The version of the metadata format
      "version": "1",
      // Required: Source code language, basically selects a "sub-version"
      // of the specification
      "language": "Solidity",
      // Required: Details about the compiler, contents are specific
      // to the language.
      "compiler": {
        // Required for Solidity: Version of the compiler
        "version": "0.4.6+commit.2dabbdf0.Emscripten.clang",
        // Optional: Hash of the compiler binary which produced this output
        "keccak256": "0x123..."
      },
      // Required: Compilation source files/source units, keys are file names
      "sources":
      {
        "myFile.sol": {
          // Required: keccak256 hash of the source file
          "keccak256": "0x123...",
          // Required (unless "content" is used, see below): Sorted URL(s)
          // to the source file, protocol is more or less arbitrary, but a
          // Swarm URL is recommended
          "urls": [ "bzzr://56ab..." ],
          // Optional: SPDX license identifier as given in the source file
          "license": "MIT"
        },
        "destructible": {
          // Required: keccak256 hash of the source file
          "keccak256": "0x234...",
          // Required (unless "url" is used): literal contents of the source file
          "content": "contract destructible is owned { function destroy() { if (msg.sender == owner) selfdestruct(owner); } }"
        }
      },
      // Required: Compiler settings
      "settings":
      {
        // Required for Solidity: Sorted list of remappings
        "remappings": [ ":g=/dir" ],
        // Optional: Optimizer settings. The fields "enabled" and "runs" are deprecated
        // and are only given for backwards-compatibility.
        "optimizer": {
          "enabled": true,
          "runs": 500,
          "details": {
            // peephole defaults to "true"
            "peephole": true,
            // inliner defaults to "true"
            "inliner": true,
            // jumpdestRemover defaults to "true"
            "jumpdestRemover": true,
            "orderLiterals": false,
            "deduplicate": false,
            "cse": false,
            "constantOptimizer": false,
            "yul": true,
            // Optional: Only present if "yul" is "true"
            "yulDetails": {
              "stackAllocation": false,
              "optimizerSteps": "dhfoDgvulfnTUtnIf..."
            }
          }
        },
        "metadata": {
          // Reflects the setting used in the input json, defaults to false
          "useLiteralContent": true,
          // Reflects the setting used in the input json, defaults to "ipfs"
          "bytecodeHash": "ipfs"
        },
        // Required for Solidity: File and name of the contract or library this
        // metadata is created for.
        "compilationTarget": {
          "myFile.sol": "MyContract"
        },
        // Required for Solidity: Addresses for libraries used
        "libraries": {
          "MyLib": "0x123123..."
        }
      },
      // Required: Generated information about the contract.
      "output":
      {
        // Required: ABI definition of the contract
        "abi": [/* ... */],
        // Required: NatSpec user documentation of the contract
        "userdoc": [/* ... */],
        // Required: NatSpec developer documentation of the contract
        "devdoc": [/* ... */]
      }
    }

.. warning::

      از آنجایی که بایت کد قرارداد حاصل به طور پیش‌فرض حاوی هش ابرداده است، هر تغییری در ابرداده ممکن 
      است منجر به تغییر بایت کد شود که شامل تغییرات در نام فایل یا مسیر است و از آنجایی که فراداده شامل
      هش از تمام منابع استفاده شده است، یک تغییر فضای خالی منجر به ابرداده های مختلف و کد بایت متفاوت 
      می شود.


.. note::
      
          تعریف ABI در بالا ترتیب ثابتی ندارد. می تواند با نسخه های کامپایلر تغییر کند. با شروع از سالیدیتی نسخه  0.5.12، هر چند، آرایه نظم خاصی را حفظ می کند.

      

.. _encoding-of-the-metadata-hash-in-the-bytecode:

رمزگذاری هش فراداده در بایت کد
=============================================
از آنجایی که ممکن است در آینده از روش‌های دیگری برای بازیابی فایل فراداده پشتیبانی کنیم، مپینگ 
``{"ipfs": <IPFS hash>, "solc": <compiler version>}`` به‌صورت کدگذاری `CBOR <https://tools.ietf.org/html/rfc7049>`_ ذخیره می‌شود. 
از آنجایی که مپینگ ممکن است حاوی کلیدهای بیشتری باشد (به زیر مراجعه کنید) و پیدا کردن ابتدای آن 
رمزگذاری آسان نیست، طول آن در یک رمزگذاری دو بایتی بیگ اندیان(big-endian) اضافه می شود.
نسخه فعلی کامپایلر سالیدیتی معمولا موارد زیر را به انتهای بایت کد مستقر شده اضافه می کند، بنابراین 
برای بازیابی داده ها، می توان انتهای بایت کد مستقر شده را بررسی کرد تا با آن الگو مطابقت داشته باشد و 
از هش IPFS برای بازیابی فایل استفاده کرد.



.. code-block:: text

    0xa2
    0x64 'i' 'p' 'f' 's' 0x58 0x22 <34 bytes IPFS hash>
    0x64 's' 'o' 'l' 'c' 0x43 <3 byte version encoding>
    0x00 0x33

در حالی که بیلدهای انتشار solc از کدگذاری 3 بایتی نسخه همانطور که در بالا نشان داده شده است (هر 
کدام یک بایت برای شماره نسخه اصلی، فرعی و وصله) استفاده می کنند، نسخه های پیش از انتشار از یک 
رشته نسخه کامل شامل هش commit و تاریخ ساخت استفاده می کنند.


.. note::

    مپینگ CBOR می‌تواند حاوی کلیدهای دیگری نیز باشد، بنابراین بهتر است به جای اینکه با ``0xa264`` 
      شروع کنید، داده‌ها را به طور کامل رمزگشایی کنید. به عنوان مثال، اگر از هر ویژگی آزمایشی که بر تولید 
      کد تأثیر می گذارد استفاده شود، مپینگ نیز حاوی ``"experimental": true`` . می باشد.


.. note::

    توجه: کامپایلر در حال حاضر از هش IPFS فراداده استفاده می کند، اما ممکن است در آینده از هش bzzr1   یا هش دیگری نیز استفاده کند، بنابراین برای شروع با ``0xa2 0x64 'i' 'p' 'f' 's'`` به این دنباله اعتماد نکنید . ما همچنین ممکن است داده های اضافی را به این ساختار CBOR اضافه کنیم، بنابراین بهترین گزینه استفادهاز تجزیه کننده CBOR مناسب است.


استفاده برای تولید رابط خودکار و NatSpec
====================================================

فراداده به روش زیر استفاده می شود: مؤلفه ای که می خواهد با یک قرارداد تعامل داشته باشد (مثلاً Mist 
یا هر کیف پول) کد قرارداد را بازیابی می کند، از آن هش IPFS/Swarm یک فایل که سپس بازیابی می 
شود. آن فایل با JSON در ساختاری مانند بالا رمزگشایی می شود.


سپس این مؤلفه می تواند از ABI برای ایجاد خودکار یک رابط کاربری ابتدایی برای قرارداد استفاده کند.

علاوه بر این، کیف پول می‌تواند از اسناد کاربر theNatSpec برای نمایش یک پیام تأیید برای کاربر در زمان 
تعامل با قرارداد، همراه با درخواست مجوز برای امضای تراکنش استفاده کند. برای اطلاعات بیشتر، فرمت 
مشخصات  :doc:`Ethereum Natural Language Specification (NatSpec) format <natspec-format>` را بخوانید.



استفاده برای تأیید کد منبع
==================================
به منظور تأیید کامپایل، منابع را می توان از IPFS/Swarm از طریق پیوند موجود در فایل فراداده بازیابی 
کرد. کامپایلر نسخه صحیح (که به عنوان بخشی از کامپایلرهای "رسمی" بررسی شده است) با تنظیمات 
مشخص شده روی آن ورودی فراخوانی می شود. بایت کد به دست آمده با داده های تراکنش ایجاد یا داده 
های آپکد ``CREATE`` مقایسه می شود که به طور خودکار ابرداده را تأیید می کند زیرا هش آن بخشی از بایت 
کد است. داده های اضافی مربوط به داده های ورودی سازنده است که باید با توجه به رابط رمزگشایی شده و 
به کاربر ارائه شود. در مخزن `sourcify <https://github.com/ethereum/sourcify>`_
(`npm package <https://www.npmjs.com/package/source-verify>`_)  می توانید کد نمونه ای را مشاهده کنید که نحوه 
استفاده از این ویژگی را نشان می دهد.


