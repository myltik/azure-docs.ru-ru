---
title: "Настройка PHP в веб-приложениях службы приложений Azure | Документация Майкрософт"
description: "Узнайте, как настроить установку PHP по умолчанию или добавить пользовательскую установку PHP для веб-приложений в службе приложений Azure."
services: app-service
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 0467707a46709674d3f5de3346ad242af5c9dcb8
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Настройка PHP в веб-приложениях службы приложений Azure
## <a name="introduction"></a>Введение
В этом руководстве показано, как настроить встроенную среду выполнения PHP в веб-приложениях [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714), предоставить настраиваемую среду выполнения PHP и включить расширения. Чтобы использовать службу приложений, подпишитесь на [бесплатную пробную версию]. Чтобы это руководство было для вас максимально эффективным, необходимо сначала создать веб-приложение PHP в службе приложений.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Практическое руководство. Изменение встроенной версии PHP
По умолчанию при создании веб-приложения службы приложений устанавливается среда PHP 5.6, которая будет сразу готова для использования. Оптимальный способ увидеть доступные версии, конфигурацию по умолчанию и поддерживаемые расширения заключается в развертывании сценария, который вызывает функцию [phpinfo()] .

Версии PHP 5.6 и PHP 7.0 также доступны, но не включены по умолчанию. Чтобы обновить версию PHP, выполните следующие действия.

### <a name="azure-portal"></a>Портал Azure
1. Перейдите к своему веб-приложению на [портале Azure](https://portal.azure.com) и нажмите кнопку **Параметры** .
   
    ![Параметры веб-приложения][settings-button]
2. В колонке **Параметры** откройте **Параметры приложения** и выберите новую версию PHP.
   
    ![Параметры приложения][application-settings]
3. Нажмите кнопку **Сохранить** в верхней части колонки **Параметры веб-приложения**.
   
    ![Сохранение параметров конфигурации][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell (только для Windows)
1. Запустите Azure PowerShell и войдите в свою учетную запись.
   
        PS C:\> Login-AzureRmAccount
2. Укажите версию PHP для веб-приложения.
   
        PS C:\> Set-AzureWebsite -PhpVersion {5.5 | 5.6 | 7.0} -Name {app-name}
3. После этого версия PHP будет установлена. Можно подтвердить эти параметры.
   
        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-command-line-interface-linux-mac-windows"></a>Интерфейс командной строки Azure для Mac, Linux и Windows
Для использования интерфейса командной строки Azure требуется установить на компьютер **Node.js** .

1. Откройте терминал и войдите в свою учетную запись.
   
        azure login
2. Укажите версию PHP для веб-приложения.
   
        azure site set --php-version {5.5 | 5.6 | 7.0} {app-name}

3. После этого версия PHP будет установлена. Можно подтвердить эти параметры.
   
        azure site show {app-name}

> [!NOTE] 
> Ниже указаны команды [Azure CLI 2.0](https://github.com/Azure/azure-cli), которые эквивалентны приведенным выше.
>
>

    az login
    az appservice web config update --php-version {5.5 | 5.6 | 7.0} -g {resource-group-name} -n {app-name}
    az appservice web config show -g {resource-group-name} -n {app-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Практическое руководство. Изменение встроенной конфигурации PHP
Для любой встроенной среды выполнения PHP можно изменить параметры конфигурации, для чего выполните следующие действия. (Дополнительные сведения о директивах php.ini см. в разделе [Список директив php.ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Изменение параметров конфигурации PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL
1. Добавьте файл [.user.ini] в корневой каталог.
2. Добавьте параметры конфигурации в файл `.user.ini`, используя тот же синтаксис, что и для файла `php.ini`. Например, чтобы включить параметр `display_errors` и установить для параметра `upload_max_filesize` значение 10 М, в файле `.user.ini` будет указан следующий текст:
   
        ; Example Settings
        display_errors=On
        upload_max_filesize=10M
   
        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
3. Разверните веб-приложение.
4. Перезапустите веб-приложение. (Перезапуск необходим, так как частота считывания PHP файлов `.user.ini` регулируется параметром `user_ini.cache_ttl`, который является параметром уровня системы; по умолчанию значение этого параметра составляет 300 секунд (5 минут). Перезапуск веб-приложения заставляет PHP считать новые параметры из файла `.user.ini` .)

В качестве альтернативы использованию файла `.user.ini` можно использовать функцию [ini_set()] в скрипте для установки параметров конфигурации, не являющихся директивами системного уровня.

### <a name="changing-phpinisystem-configuration-settings"></a>Изменение параметров конфигурации PHP\_INI\_SYSTEM
1. Добавьте параметр приложения для веб-приложения с помощью ключа `PHP_INI_SCAN_DIR` и значения `d:\home\site\ini`
2. Создание файл `settings.ini` с помощью консоли Kudu (http://&lt;имя_узла&gt;.scm.azurewebsite.net) в каталоге `d:\home\site\ini`.
3. Добавьте параметры конфигурации в файл `settings.ini` , используя тот же синтаксис, что и для файла php.ini. Например, если необходимо, чтобы параметр `curl.cainfo` указывал на файл `*.crt`, а параметр wincache.maxfilesize имел значение 512K, файл `settings.ini` должен содержать следующий текст:
   
        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
4. Перезапустите веб-приложение для загрузки изменений.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Практическое руководство. Включение расширений в среде выполнения PHP по умолчанию
Как было отмечено в предыдущем разделе, оптимальный способ увидеть версию PHP по умолчанию, конфигурацию по умолчанию и поддерживаемые расширения заключается в развертывании сценария, который вызывает функцию [phpinfo()]. Чтобы включить дополнительные расширения, выполните следующие действия.

### <a name="configure-via-ini-settings"></a>Настройка с помощью параметров ini
1. Добавьте каталог `ext` в каталог `d:\home\site`.
2. Поместите файлы с расширением `.dll` в каталог `ext` (например, `php_xdebug.dll`). Убедитесь, что расширения совместимы с версией PHP по умолчанию, а также совместимы с VC9 и непотокобезопасной технологией (nts).
3. Добавьте параметр приложения для веб-приложения с помощью ключа `PHP_INI_SCAN_DIR` и значения `d:\home\site\ini`
4. Создайте файл `ini` в `d:\home\site\ini` с именем `extensions.ini`.
5. Добавьте параметры конфигурации в файл `extensions.ini` , используя тот же синтаксис, что и для файла php.ini. Например, чтобы включить расширения MongoDB и XDebug, файл `extensions.ini` должен содержать следующий текст:
   
        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
6. Перезапустите веб-приложение для загрузки изменений.

### <a name="configure-via-app-setting"></a>Настройка с помощью параметров приложения
1. Добавьте каталог `bin` в корневой каталог.
2. Поместите файлы с расширением `.dll` в каталог `bin` (например, `php_xdebug.dll`). Убедитесь, что расширения совместимы с версией PHP по умолчанию, а также совместимы с VC9 и непотокобезопасной технологией (nts).
3. Разверните веб-приложение.
4. Перейдите к своему веб-приложению на портале Azure и нажмите кнопку **Параметры** .
   
    ![Параметры веб-приложения][settings-button]
5. В колонке **Параметры** откройте **Параметры приложения** и прокрутите содержимое окна до раздела **Параметры приложения**.
6. В разделе **Параметры приложения** создайте ключ **PHP_EXTENSIONS**. Значением для этого ключа будет путь относительно корневого веб-сайта: **bin\ваш-внешний-файл**.
   
    ![Включение расширения в параметрах приложения][php-extensions]
7. Нажмите кнопку **Сохранить** в верхней части колонки **Параметры веб-приложения**.
   
    ![Сохранение параметров конфигурации][save-button]

Расширения Zend также поддерживаются с помощью ключа **PHP_ZENDEXTENSIONS**. Чтобы включить несколько расширений, включите список разделенных запятыми файлов `.dll` для значения параметра приложения.

## <a name="how-to-use-a-custom-php-runtime"></a>Практическое руководство. Настраиваемая среда выполнения PHP
Вместо среды выполнения PHP по умолчанию веб-приложения службы приложений могут использовать среду выполнения PHP, предоставляемую для выполнения скриптов PHP. Предоставляемую среду выполнения можно настроить с помощью файла `php.ini`, также предоставляемого пользователем. Чтобы использовать настраиваемую среду выполнения PHP с веб-приложениями, выполните следующие действия.

1. Получите версию PHP для Windows, совместимую с VC9 или VC11 и непотокобезопасной технологией (nts). Последние версии PHP для Windows можно найти здесь: [http://windows.php.net/download/]. Более старые версии можно найти в архиве здесь: [http://windows.php.net/downloads/releases/archives/].
2. Измените файл `php.ini` для среды выполнения. Обратите внимание, что все параметры конфигурации, являющиеся директивами system-level-only, не будут учитываться веб-приложениями. (Дополнительные сведения о директивах только системного уровня см. в разделе [Список директив php.ini].)
3. При необходимости добавьте расширения для вашей среды выполнения PHP и включите их в файл `php.ini` .
4. Добавьте каталог `bin` в корневой каталог и поместите в него каталог, в котором содержится ваша среда выполнения PHP (например, `bin\php`).
5. Разверните веб-приложение.
6. Перейдите к своему веб-приложению на портале Azure и нажмите кнопку **Параметры** .
   
    ![Параметры веб-приложения][settings-button]
7. В колонке **Параметры** откройте **Параметры приложения** и прокрутите содержимое окна до раздела **Сопоставление обработчиков**. Добавьте расширение `*.php` в поле "Расширение" и путь к исполняемому файлу `php-cgi.exe`. Если поместить среду выполнения PHP в каталог `bin` в корневом каталоге приложения, путь будет выглядеть так: `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
   
    ![Укажите обработчик в сопоставлении обработчика][handler-mappings]
8. Нажмите кнопку **Сохранить** в верхней части колонки **Параметры веб-приложения**.
   
    ![Сохранение параметров конфигурации][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Практическое руководство. Включение автоматизации Composer в Azure
По умолчанию служба приложений не выполняет никаких действий с файлом composer.json, если он есть в проекте PHP. Если используется [развертывание Git](app-service-deploy-local-git.md), можно включить обработку composer.json во время операции `git push`, для этого активируйте расширение Composer.

> [!NOTE]
> [Здесь](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)вы можете отдать свой голос за первоклассную поддержку Composer в службе приложений!
> 
> 

1. На [портале Azure](https://portal.azure.com) в колонке вашего веб-приложения PHP щелкните **Инструменты** > **Расширения**.
   
    ![Колонка параметров портала Azure для включения автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Щелкните **Добавить**, а затем — **Composer**.
   
    ![Добавление расширения Composer для включения автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Нажмите кнопку **ОК** , чтобы принять условия использования. Нажмите кнопку **ОК** еще раз, чтобы добавить расширение.
   
    В колонке **Установленные расширения** появится расширение Composer.  
    ![Примите условия использования для включения автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Теперь выполните операции `git add`, `git commit` и `git push`, как в предыдущем разделе. Вы увидите, что расширение Composer устанавливает зависимости, определенные в файле composer.json.
   
    ![Развертывание Git с помощью автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Дополнительная информация
Дополнительную информацию можно найти в [Центре разработчика PHP](/develop/php/).

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

[бесплатную пробную версию]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Список директив php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png

