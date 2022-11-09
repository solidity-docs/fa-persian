.. index:: !mapping
.. _mapping-types:

انواع  نگاشت‌ها (Mapping Types)
=============

<<<<<<< HEAD
نوع‌های نگاشت از سینتکس  ``mapping(_KeyType => _ValueType)`` استفاده می‌کنند و 
متغیرهای نوع نگاشت با استفاده از سینتکس 
``mapping(_KeyType => _ValueType) _VariableName``  مشخص می‌شوند. ``KeyType_`` می‌تواند هر نوع مقدار داخلی،  ``bytes`` ،  ``string`` یا هر نوع قرارداد یا enum 
باشد. سایر نوع‌های پیچیده یا تعریف شده توسط کاربر، مانند نگاشت یا (mapping)‌، struct‌ها یا انواع آرایه مجاز نیستند. ``ValueType_``  می‌تواند هر نوعی باشد، از جمله نگاشت‌ها، آرایه‌ها و struct‌ها.
=======
Mapping types use the syntax ``mapping(KeyType => ValueType)`` and variables
of mapping type are declared using the syntax ``mapping(KeyType => ValueType) VariableName``.
The ``KeyType`` can be any
built-in value type, ``bytes``, ``string``, or any contract or enum type. Other user-defined
or complex types, such as mappings, structs or array types are not allowed.
``ValueType`` can be any type, including mappings, arrays and structs.
>>>>>>> 9db2da0385c5abec0d1c3eab468648ec85fb5caa


می‌‍‌‌‌‌توانید نگاشت‍‌ها را به صورت  `جداول هش <https://en.wikipedia.org/wiki/Hash_table>`_  در نظر بگیرید که عملاً مقداردهی اولیه می‌شوند به گونه ای که هر 
کلید ممکن وجود دارد و به مقداری نگاشت می‌شود که پیش نمایش بایت همه‌ی آن صفر می‌باشند، یک نوع :ref:`مقدار پیش فرض<default-value>` . شباهت در اینجا پایان می‌یابد، داده‌های کلیدی در نگاشت ذخیره نمی‌شوند، فقط از هش  ``keccak256`` برای جستجوی مقدار استفاده می‌شود.


<<<<<<< HEAD
=======
You can mark state variables of mapping type as ``public`` and Solidity creates a
:ref:`getter <visibility-and-getters>` for you. The ``KeyType`` becomes a parameter for the getter.
If ``ValueType`` is a value type or a struct, the getter returns ``ValueType``.
If ``ValueType`` is an array or a mapping, the getter has one parameter for
each ``KeyType``, recursively.
>>>>>>> 9db2da0385c5abec0d1c3eab468648ec85fb5caa

به همین دلیل، نگاشت‌ها طول یا مفهومی از کلید یا مقدار تنظیم شده ندارند و بنابراین بدون اطلاعات اضافی در مورد کلیدهای اختصاص داده شده پاک نمی‌شوند (به قسمت :ref:`clearing-mappings` مراجعه کنید).



نگاشت‌ها فقط می‌توانند یک مکان داده از  ``storage`` را داشته باشند و بنابراین برای متغیرهای حالت، به 
عنوان نوع‌های مرجع storage  در توابع، یا به عنوان پارامترهای توابع کتابخانه مجاز هستند. آنها نمی‌توانند به 
عنوان پارامترها یا پارامترهای بازگشتی توابع قرارداد که در معرض دید عموم قرار دارند، مورد استفاده قرار گیرند. 
این محدودیت‌ها برای آرایه‌ها و struct‌های حاوی نگاشت نیز صادق است.


شما می‌توانید متغیرهای حالت از نوع نگاشت را به صورت  ``public`` علامت گذاری کنید و سالیدیتی یک 
گیرنده (:ref:`getter <visibility-and-getters>`) برای شما ایجاد می‌کند.  ``KeyType_`` به یک پارامتر برای getter  تبدیل می‌شود. 
اگر  ``ValueType_`` یک مقدار نوع یا یک  struct  باشد، گیتر  ``ValueType_`` را برمی‌گرداند. 
اگر  ``ValueType_`` یک آرایه یا نگاشت باشد، getter به صورت بازگشتی برای هر  ``KeyType_`` یک 
پارامتر دارد.


در مثال زیر، قرارداد  ``MappingExample`` یک نگاشت از  ``balances`` عمومی را تعریف می‎کند، با نوع 
کلید یک   ``address`` و یک نوع مقدار یک  ``uint`` ، و یک آدرس اتریوم را به یک مقدار صحیح بدون علامت  
نگاشت می‌کند. از آنجا که  ``uint`` یک نوع مقدار است، گیرنده مقداری را متناسب با نوع آن برمی‌گرداند که 
می‌توانید آن را در قرارداد  ``MappingUser`` مشاهده کنید که مقدار را در آدرس مشخص شده برمی‌گرداند.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.0 <0.9.0;

    contract MappingExample {
        mapping(address => uint) public balances;

        function update(uint newBalance) public {
            balances[msg.sender] = newBalance;
        }
    }

    contract MappingUser {
        function f() public returns (uint) {
            MappingExample m = new MappingExample();
            m.update(100);
            return m.balances(address(this));
        }
    }


مثال زیر یک نسخه ساده از توکن `ERC20 token <https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol>`_ است.  ``_allowances`` نمونه‌ای از نوع نگاشت در داخل نوع 
نگاشت دیگر است. مثال زیر از ``_allowances`` برای ثبت مبلغی که شخص دیگری مجاز به برداشت از 
حساب شما است استفاده می‌کند.


.. code-block:: solidity

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.22 <0.9.0;

    contract MappingExample {

        mapping (address => uint256) private _balances;
        mapping (address => mapping (address => uint256)) private _allowances;

        event Transfer(address indexed from, address indexed to, uint256 value);
        event Approval(address indexed owner, address indexed spender, uint256 value);

        function allowance(address owner, address spender) public view returns (uint256) {
            return _allowances[owner][spender];
        }

        function transferFrom(address sender, address recipient, uint256 amount) public returns (bool) {
            require(_allowances[sender][msg.sender] >= amount, "ERC20: Allowance not high enough.");
            _allowances[sender][msg.sender] -= amount;
            _transfer(sender, recipient, amount);
            return true;
        }

        function approve(address spender, uint256 amount) public returns (bool) {
            require(spender != address(0), "ERC20: approve to the zero address");

            _allowances[msg.sender][spender] = amount;
            emit Approval(msg.sender, spender, amount);
            return true;
        }

        function _transfer(address sender, address recipient, uint256 amount) internal {
            require(sender != address(0), "ERC20: transfer from the zero address");
            require(recipient != address(0), "ERC20: transfer to the zero address");
            require(_balances[sender] >= amount, "ERC20: Not enough funds.");

            _balances[sender] -= amount;
            _balances[recipient] += amount;
            emit Transfer(sender, recipient, amount);
        }
    }


.. index:: !iterable mappings
.. _iterable-mappings:

نگاشت های تکرارپذیر 
-----------------

<<<<<<< HEAD
نمی‌توانید بر روی نگاشت‌ها تکرار کنید، یعنی نمی‌توانید کلیدهای آنها را بشمارید.گرچند امکان اجرای یک 
ساختار داده در بالای آنها و تکرار آن وجود دارد. به عنوان مثال، کد زیر یک 
کتابخانه  ``IterableMapping`` را پیاده سازی می‌کند که قرارداد ``User`` سپس داده‌ها را نیز اضافه 
می‌کند و تابع ``sum`` تکرار می‌شود تا تمام مقادیر را جمع کند.

=======
You cannot iterate over mappings, i.e. you cannot enumerate their keys.
It is possible, though, to implement a data structure on
top of them and iterate over that. For example, the code below implements an
``IterableMapping`` library that the ``User`` contract then adds data to, and
the ``sum`` function iterates over to sum all the values.
>>>>>>> 9db2da0385c5abec0d1c3eab468648ec85fb5caa

.. code-block:: solidity
    :force:

    // SPDX-License-Identifier: GPL-3.0
    pragma solidity ^0.8.8;

    struct IndexValue { uint keyIndex; uint value; }
    struct KeyFlag { uint key; bool deleted; }

    struct itmap {
        mapping(uint => IndexValue) data;
        KeyFlag[] keys;
        uint size;
    }

    type Iterator is uint;

    library IterableMapping {
        function insert(itmap storage self, uint key, uint value) internal returns (bool replaced) {
            uint keyIndex = self.data[key].keyIndex;
            self.data[key].value = value;
            if (keyIndex > 0)
                return true;
            else {
                keyIndex = self.keys.length;
                self.keys.push();
                self.data[key].keyIndex = keyIndex + 1;
                self.keys[keyIndex].key = key;
                self.size++;
                return false;
            }
        }

        function remove(itmap storage self, uint key) internal returns (bool success) {
            uint keyIndex = self.data[key].keyIndex;
            if (keyIndex == 0)
                return false;
            delete self.data[key];
            self.keys[keyIndex - 1].deleted = true;
            self.size --;
        }

        function contains(itmap storage self, uint key) internal view returns (bool) {
            return self.data[key].keyIndex > 0;
        }

        function iterateStart(itmap storage self) internal view returns (Iterator) {
            return iteratorSkipDeleted(self, 0);
        }

        function iterateValid(itmap storage self, Iterator iterator) internal view returns (bool) {
            return Iterator.unwrap(iterator) < self.keys.length;
        }

        function iterateNext(itmap storage self, Iterator iterator) internal view returns (Iterator) {
            return iteratorSkipDeleted(self, Iterator.unwrap(iterator) + 1);
        }

        function iterateGet(itmap storage self, Iterator iterator) internal view returns (uint key, uint value) {
            uint keyIndex = Iterator.unwrap(iterator);
            key = self.keys[keyIndex].key;
            value = self.data[key].value;
        }

        function iteratorSkipDeleted(itmap storage self, uint keyIndex) private view returns (Iterator) {
            while (keyIndex < self.keys.length && self.keys[keyIndex].deleted)
                keyIndex++;
            return Iterator.wrap(keyIndex);
        }
    }

    // How to use it
    contract User {
        // Just a struct holding our data.
        itmap data;
        // Apply library functions to the data type.
        using IterableMapping for itmap;

        // Insert something
        function insert(uint k, uint v) public returns (uint size) {
            // This calls IterableMapping.insert(data, k, v)
            data.insert(k, v);
            // We can still access members of the struct,
            // but we should take care not to mess with them.
            return data.size;
        }

        // Computes the sum of all stored data.
        function sum() public view returns (uint s) {
            for (
                Iterator i = data.iterateStart();
                data.iterateValid(i);
                i = data.iterateNext(i)
            ) {
                (, uint value) = data.iterateGet(i);
                s += value;
            }
        }
    }
