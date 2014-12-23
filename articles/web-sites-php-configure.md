<properties title="How to Configure PHP in Azure Websites" pageTitle="Настройка PHP на веб-сайтах Azure" metaKeywords="Azure, веб-сайты Azure, конфигурация, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Websites." services="Web Sites" documentationCenter="PHP" authors="cephalin" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

#Настройка PHP на веб-сайтах Windows Azure

В этом руководстве показано, как настроить встроенную среду выполнения PHP на веб-сайтах Azure, создать настраиваемую среду выполнения PHP и включить расширения на веб-сайтах Azure. Чтобы использовать веб-сайты Azure, подпишитесь на [бесплатное пробное использование]. Для максимально эффективного использования данного руководства необходимо сначала создать сайт PHP в Azure (см. [Центр разработчиков PHP - учебные курсы]). Общие сведения о настройке сайтов в Azure см. в разделе [Настройка веб-сайтов].

##Оглавление

* [Что такое веб-сайт Azure?](#WhatIs)
* [Практическое руководство. Изменение конфигурации PHP по умолчанию](#ChangeBuiltInPHP)
* [Практическое руководство. Включение расширений во встроенной среде выполнения PHP](#EnableExtDefaultPHP)
* [Практическое руководство. Использование пользовательской среды PHP](#UseCustomPHP)
* [Дальнейшие действия](#NextSteps)

<h2><a name="WhatIs"></a>Что такое веб-сайты Azure?</h2>
Веб-сайты Azure позволяют создавать масштабируемые веб-сайты в Azure. Быстро и легко развертывайте сайты в облачной среде с высокой масштабируемостью, которая позволяет начать с небольшого сайта и масштабировать его по мере роста трафика. Веб-сайты Azure используют языки и приложения с открытым исходным кодом по вашему выбору, а также поддерживают развертывание с помощью Git, FTP и TFS. Вы можете быстро интегрировать другие службы, например MySQL, SQL Database, кэш, CDN и хранилище.

<h2><a name="ChangeBuiltInPHP"></a>Практическое руководство. Изменение встроенной конфигурации PHP</h2>
По умолчанию при создании веб-сайта Windows Azure устанавливается среда PHP 5.4, которая будет сразу готова для использования. Чтобы увидеть текущую версию выпуска, ее конфигурацию по умолчанию и поддерживаемые расширения, разверните скрипт с вызовом функции [phpinfo()].

Также доступна версия PHP 5.5, но по умолчанию не включена. Для ее включения выполните следующие действия:

1. На портале Azure выберите панель мониторинга веб-сайта и щелкните **Настроить**.

	![Configure tab on Web Sites dashboard][configure]

1. Щелкните PHP 5.5.

	![Select PHP version][select-php-version]

1. В нижней части страницы щелкните **Сохранить**.

	![Save configuration settings][save-button]

Для каждой из встроенных сред выполнения PHP можно изменить любые параметры конфигурации, не являющиеся директивами уровня system-level-only, выполнив следующие действия. (Дополнительные сведения о директивах system-level-only см. в разделе [Список директив php.ini]).

1. Добавьте файл [.user.ini] в корневой каталог.
1. Добавьте параметры конфигурации в файл ".user.ini", используя тот же синтаксис, что и для файла "php.ini". Например, если бы вы хотели включить параметр "display_errors" и задали параметр "upload_max_filesize" равным 10 МБ, ваш файл ".user.ini" содержал бы следующий текст:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. Разверните приложение.
1. Перезагрузите ваш веб-сайт. (Перезагрузка необходима, так как частота, с которой PHP считывает файлы ".user.ini", регулируется параметром "user_ini.cache_ttl", который является параметром системного уровня и по умолчанию составляет 300 секунд (5 минут). Перезагрузка сайта принудительно заставляет PHP считывать новые параметры из файла ".user.ini").

В качестве альтернативы использованию файла ".user.ini" можно использовать функцию [ini_set()] в скриптах для установки параметров конфигурации, не являющихся директивами системного уровня.

<h2><a name="EnableExtDefaultPHP"></a>Практическое руководство. Включение расширений в среде выполнения PHP по умолчанию</h2>
Как было отмечено в предыдущем разделе, наилучшим вариантом просмотра версии PHP по умолчанию, ее конфигурации по умолчанию и поддерживаемых расширений является развертывание сценария, вызывающего функцию [phpinfo()]. Чтобы включить дополнительные расширения, выполните следующие действия.

1. Добавьте каталог "bin" в ваш корневой каталог.
1. Поместите файлы с расширением ".dll" в каталог "bin" (например, "php_mongo.dll"). Убедитесь, что расширения совместимы с версией PHP по умолчанию (на момент написания этого учебника в качестве версии по умолчанию используется PHP 5.4), а также совместимы с VC9 и непотокобезопасной технологией (nts).
1. Разверните приложение.
1. На портале Azure перейдите в панель мониторинга вашего веб-сайта и щелкните **Настроить**.

	![Configure tab on Web Sites dashboard][configure]

1. В разделе **Настройки приложений** создайте параметр **PHP_EXTENSIONS** и присвойте ему значение **bin\ваш-ext-файл**. Чтобы включить несколько расширений, используйте разделенный запятыми список файлов ".dll".

	![Enable extension in app settings][app-settings]

1. В нижней части страницы щелкните **Сохранить**.

	![Save configuration settings][save-button]

<h2><a name="UseCustomPHP"></a>Практическое руководство. Использование пользовательской среды PHP</h2>
Вместо среды выполнения PHP по умолчанию веб-сайты Azure могут использовать среду выполнения PHP, предоставляемую для выполнения скриптов PHP. Предоставляемую среду выполнения можно настроить с помощью файла "php.ini", также предоставляемого пользователем. Чтобы использовать настраиваемые среды выполнения PHP для веб-сайтов Azure, выполните следующие действия.

1.  Получите версию PHP для Windows, совместимую с VC9 и непотокобезопасной технологией (nts). Последние версии PHP для Windows можно найти здесь: [http://windows.php.net/download/]. Более старые версии можно найти в архиве по адресу [http://windows.php.net/downloads/releases/archives/].
1. Измените файл "php.ini" для вашей среды выполнения. Обратите внимание, что все параметры конфигурации, являющиеся директивами только системного уровня, будут игнорироваться веб-сайтами Azure. (Дополнительные сведения о директивах только системного уровня см. в разделе [Список директив php.ini]).
1. При необходимости добавьте расширения для вашей среды выполнения PHP и включите их в файле php.ini.
1. Добавьте каталог "bin" в корневой каталог и поместите в него каталог, в котором содержится ваша среда выполнения PHP (например, "bin\php").
1. Разверните приложение.
1. На портале Azure перейдите в панель мониторинга вашего веб-сайта и щелкните **Настроить**.

	![Configure tab on Web Sites dashboard][configure]

1. В разделе **сопоставления обработчика** добавьте к EXTENSION "*.php" и добавьте путь к исполняемому файлу "php-cgi.exe". Если поместить вашу среду выполнения PHP в каталог "bin" корневого каталога вашего приложения, путь будет "D:\home\site\wwwroot\bin\php\php-cgi.exe".

	![Specify handler in hander mappings][handler-mappings]

1. В нижней части страницы щелкните **Сохранить**.

	![Save configuration settings][save-button]

<h2><a name="NextSteps"></a>Дальнейшие действия</h2>
Вы изучили основные сведения о настройке PHP на веб-сайтах Azure. Дополнительные сведения можно найти по следующим ссылкам.

- [Настройка, мониторинг и масштабирование веб-сайтов в Azure]
- [Загрузка пакета Azure SDK для PHP]


[бесплатное пробное использование]: https://www.windowsazure.com/ru-ru/pricing/free-trial/
[Центр разработчиков PHP - учебные курсы]: https://www.windowsazure.com/ru-ru/develop/php/tutorials/
[Настройка веб-сайтов]: https://www.windowsazure.com/ru-ru/manage/services/web-sites/how-to-configure-websites/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[List of php.ini directives]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[configure]: ./media/web-sites-php-configure/configure.png
[app-settings]: ./media/web-sites-php-configure/app-settings.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[Настройка, мониторинг и масштабирование веб-сайтов в Azure]: http://www.windowsazure.com/ru-ru/manage/services/web-sites/
[Загрузка пакета Azure SDK для PHP]: http://www.windowsazure.com/ru-ru/develop/php/common-tasks/download-php-sdk/
