.. index:: ! contract

.. _contracts:

##########
قراردادها
##########

قراردادهای سالیدیتی مشابه کلاس‌های زبان‌های شیء گرا هستند. آنها حاوی داده‌های ماندگار در متغیرهای حالت 
و توابع هستند که می‌توانند این متغیرها را تغییر دهند. فراخوانی یک تابع در یک قرارداد متفاوت (instance) 
یک فراخوانی تابع EVM را انجام می‌دهد و بنابراین context را طوری تغییر می‌دهد که متغیرهای حالت در 
فراخوانی قرارداد غیرقابل دسترسی باشد. برای اینکه هر تغییری بیفتد، یک قرارداد و توابع آن باید فراخوانی شود. 
هیچ مفهوم "cron" در اتریوم وجود ندارد که بتواند به طور خودکار یک تابع را در یک رویداد خاص فراخوانی 
کند.


.. include:: contracts/creating-contracts.rst

.. include:: contracts/visibility-and-getters.rst

.. include:: contracts/function-modifiers.rst

.. include:: contracts/constant-state-variables.rst
.. include:: contracts/functions.rst

.. include:: contracts/events.rst
.. include:: contracts/errors.rst

.. include:: contracts/inheritance.rst

.. include:: contracts/abstract-contracts.rst
.. include:: contracts/interfaces.rst

.. include:: contracts/libraries.rst

.. include:: contracts/using-for.rst