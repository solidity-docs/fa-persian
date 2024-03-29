.. index:: Bugs

.. _known_bugs:

#######################
لیست باگهای شناخته شده
#######################

در زیر ، می توانید لیستی از برخی اشکالات شناخته شده مربوط به امنیت در کامپایلر سالیدیتی
را در قالب جیسون پیدا کنید. خود فایل در `مخزن گیت هاب <https://github.com/ethereum/solidity/blob/develop/docs/bugs.json>`_ . میزبانی می شود.
این لیست تا نسخه 0.3.0 امتداد دارد، اشکالات قبلی حاضر هستند و اشکالاتی که بعد از ارائه شدن نسخه
نبودند در این لیست وجود دارد.

فایل دیگری بنام `bugs_by_version.json <https://github.com/ethereum/solidity/blob/develop/docs/bugs_by_version.json>`_ وجود دارد ، که می تواند برای بررسی اشکالات
موجود بر نسخه خاصی از کامپایلر مورد استفاده قرار گیرد.

ابزار های تصدیق منبع قرارداد و همچنین سایر ابزار های تعاملی با قرارداد ها طبق این لیست
معیارها باید شرایط ذیل را داشته باشند:

- اگر قراردادی بجای نسخه کامپایلر منتشر شده با نسخه کامپایلرشبانه، کامپایل شود ،
  کمی مشکوک است. این لیست نسخه های منتشر نشده با شبانه را دنبال نمی کند.
- همچنین اگر قراردادی با آخرین نسخه منتشر شده کامپایلر ، کامپایل نشده باشد، کمی
  مشکوک است. برای قراردادهایی که با استفاده از سایر قراردادها ایجاد شده اند، شما
  باید زنجیره ایجاد یک تراکنش را دنبال کنید و از تاریخ آن تراکنش به عنوان تاریخ ایجاد
  قرارداد استفاده کنید.
- اگر قراردادی با کامپایلری که حاوی اشکال شناخته شده است ، کامپایل شده باشد و
  زمان ایجاد قرارداد بعد از تاریخ انتشار نسخه جدید کامپایلر با رفع اشکال مذکور باشد.
  آن قرارداد بسیار مشکوک است.

فایل json اشکالات شناخته شده زیر ، یک آرایه ای از شی ها برای هر اشکال(bug) است ، به
همراه کلید های زیر:

uid
    Unique identifier given to the bug in the form of ``SOL-<year>-<number>``.
    It is possible that multiple entries exists with the same uid. This means
    multiple version ranges are affected by the same bug.
نام
    نام اختصاری داده شده به اشکال(bug)
خلاصه
    توضیح کوتاه در مورد اشکال
شرح
    شرح کامل از اشکال
پیوند(link)
    URL وب سایت به همراه اطلاعات دقیقتر ، اختیاری
معرفی شده
    اولین نسخه کامپایلر منتشر شده که دارای اشکال است، اختیاری
تعمیر شده
    اولین نسخه کامپایلر منتشر شده که دیگر دارای آن اشکال نیست
انتشار
    تاریخی که اشکال برای عموم شناخته شده است ، اختیاری
شدت
    شدت(سختی) اشکال : خیلی کم ، کم ، متوسط ، زیاد. میزان قابل کشف بودن در تست
    های قرار داد در نظر گرفته می شود. احتمال افتادن اتفاق ، خسارت و سوء استفاده توسط
    exploit ها (exploit: تکه کد، برنامه نرم افزاری ، ترتیب انجام چند رفتار با قرارداد و ... که از
    اشکال موجود در قرارداد برای انجام اهداف مخرب بهره می برد).
شرایط
    شرایطی که اشکال را تحریک می کنند . نام کلیدی ذیل قابل استفاده است :
    ``optimizer`` ، مقداری بولی(true/false) که مشخص می کند اگر ``optimizer`` فعال شود
    احتمال وقوع اشکال است. ``evmVersion`` ، رشته ای که مشخص می کند کدام تنظیمات
    کامپایلر نسخه EVM اشکال را تحریک می کنند. این رشته می تواند شامل عملگر های مقایسه
    ای نیز باشد. برای مثال ، ``">=constantinople"``  به این معنی است که اگر از EVM خود
    نسخه ``constantinople`` و یا به بعد استفاده شود، اشکال رخ می دهد . در صورت عدم ارائه
    شرایط ، فرض بر این است که اشکال در تمامی شرایط رخ می دهد.
بررسی
    این بخش شامل چک-لیست هایی است که مشخص می کند قراداد های هوشمند
    اشکال دارند یا نه. اولین نوع بررسی عبارات منظم جاوا اسکریپت است که در صورت وجود
    اشکال باید کد منبع با ("source-regex") مطابقت داشته باشد. اگر مطابقتی وجود نداشته باشد،
    پس به اختمال زیاد اشکال وجود ندارد. اگر مطابقت وجود داشته باشد، احتمال وجود اشکال
    است. برای دقت بیشتر ، بررسی ها باید پس از حذف حاشیه نویسی ها(comments) در کد
    منبع (source code) انجام شود. دومین نوع بررسی الگوهایی است که باید بر روی AST
    فشرده برنامه سالیدتی اعمال کرد.(“ast-compact-json-path”). عبارت مشخص شده
    جستجو عبارتی از نوع `JsonPath <https://github.com/json-path/JsonPath>`_  است. اگر حداقل یک مسیر از AST سالیدتی با عبارت
    جستجو شده تطابق کند، اشکال به احتمال زیاد وجود دارد.

.. literalinclude:: bugs.json
   :language: js
