---
title: "Создание, настройка и развертывание веб-приложения PHP в Azure | Документация Майкрософт"
description: "В этом руководстве описывается, как запустить веб-приложение PHP (Laravel) в службе приложений Azure. Узнайте, как настроить службу приложений Azure в соответствии с требованиями выбранной платформы PHP."
services: app-service\web
documentationcenter: php
author: cephalin
manager: wpickett
editor: 
tags: mysql
ms.assetid: cb73859d-48aa-470a-b486-d984746d6d26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: f595be46983bf07783b529de885d889c18fdb61a
ms.openlocfilehash: 6bbbf253b9735695e7ace0c27e4bc96df7a0c779


---
# <a name="create-configure-and-deploy-a-php-web-app-to-azure"></a>Создание, настройка и развертывание веб-приложения PHP в Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

В этом руководстве описывается, как создать, настроить и развернуть веб-приложение PHP в Azure, а также как настроить службу приложений Azure в соответствии с требованиями веб-приложения PHP. По окончании изучения этого руководства у вас будет работающее веб-приложение [Laravel](https://www.laravel.com/), запущенное в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) в режиме реального времени.

Как разработчик PHP вы можете перенести предпочитаемую среду PHP в Azure. В этом руководстве в качестве примера используется приложение Laravel. Вы узнаете: 

* как выполнить развертывание с помощью Git;
* как установить версию PHP;
* как использовать файл запуска, который не находится в корневом каталоге приложения;
* как получить доступ к переменным конкретной среды;
* как обновить приложение в Azure.

Вы сможете применить полученные здесь знания для других веб-приложений PHP, развертываемых в Azure.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](app-service-web-php-get-started-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0 (предварительная версия)](app-service-web-php-get-started.md) — интерфейс командной строки нового поколения для модели развертывания Resource Manager.

## <a name="prerequisites"></a>Предварительные требования
* [PHP 7.0](http://php.net/downloads.php)
* [Редактор](https://getcomposer.org/download/)
* [Предварительная версия Azure CLI 2.0](/cli/azure/install-az-cli2)
* [Git.](http://www.git-scm.com/downloads)
* Учетная запись Microsoft Azure. Если у вас нет учетной записи, [подпишитесь на бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) или [активируйте преимущества для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> Веб-приложение можно увидеть в действии. [Попробуйте службу приложений](http://go.microsoft.com/fwlink/?LinkId=523751) — в ней можно быстро создать кратковременное приложение начального уровня. Для этого не потребуется ни кредитная карта, ни какие-либо обязательства.
> 
> 

## <a name="create-a-php-laravel-app-on-your-dev-machine"></a>Создание приложения PHP (Laravel) на компьютере разработки
1. Откройте новое окно командной строки Windows, окно PowerShell, оболочку Linux или терминал OS X. Чтобы проверить, правильно ли установлены требуемые инструменты на компьютере, выполните следующие команды. 
   
        php --version
        composer --version
        az --version
        git --version
   
    Если вы еще не установили необходимые инструменты, то ссылки для их скачивания доступны в разделе [Предварительные требования](#Prerequisites).

2. Установите приложение Laravel следующим образом:
   
        composer global require "laravel/installer"

3. Перейдите в рабочий каталог, используя команду `CD`, и создайте приложение Laravel следующим образом:
   
        cd <working_directory>
        laravel new <app_name>
4. Перейдите в только что созданный каталог `<app_name>`, используя команду `CD`, и протестируйте приложение следующим образом:
   
        cd <app_name>
        php artisan serve
   
    Теперь после перехода по ссылке http://localhost:8000 в браузере отобразится экран-заставка Laravel.
   
    ![Протестируйте приложение PHP (Laravel) локально перед его развертыванием в Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)

1. Инициализируйте репозиторий Git и сохраните весь код:

        git init
        git add .
        git commit -m "Hurray! My first commit for my Azure web app!"

Это обычный рабочий процесс Laravel и Git, но мы здесь не для <a href="https://laravel.com/docs/5.3" rel="nofollow">изучения работы с Laravel</a>. поэтому давайте пойдем дальше.

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>Создание веб-приложения Azure и настройка развертывания Git
> [!NOTE]
> Если вы хотите выполнить развертывание с помощью FTP, см. сведения в [руководстве по FTP](web-sites-php-mysql-deploy-use-ftp.md). 
> 
> 

С помощью интерфейса командной строки Azure можно создать веб-приложение в службе приложений Azure и настроить для него развертывание Git, используя всего одну командную строку. Давайте приступим.

1. Войдите в Azure:
   
        az login
   
    Следуйте инструкциям из справочного сообщения, чтобы продолжить процесс входа в систему.
   
3. Укажите пользователя развертывания для службы приложений. Вы развернете код позже с помощью этих учетных данных.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md). В рамках этой версии руководства для PHP вам не обязательно знать, что это такое.

        az group create --location "<location>" --name my-php-app-group

    Чтобы увидеть доступные значения для `<location>`, используйте команду `az appservice list-locations` интерфейса командной строки.

3. Создайте [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) уровня "Бесплатный". В рамках этой версии руководства для PHP вам достаточно знать, что этот план не предусматривает выставление счетов за веб-приложения.

        az appservice plan create --name my-php-appservice-plan --resource-group my-php-app-group --sku FREE

4. Создайте веб-приложение с уникальным именем в `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-php-app-group --plan my-php-appservice-plan

5. Настройте локальное развертывание Git для веб-приложения с помощью следующей команды:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-php-app-group

    Если вы получите приблизительно следующие выходные данные JSON, это значит, что удаленный репозиторий Git настроен:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Добавьте URL-адрес в JSON в качестве удаленного репозитория Git для локального репозитория (для простоты называется `azure`).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
<a name="configure"/>

## <a name="configure-the-azure-web-app"></a>Настройка веб-приложения Azure
Чтобы приложение Laravel работало в Azure, необходимо обратить внимание на несколько моментов. Эти же действия можно выполнить для выбранной платформы PHP.

* Настройте PHP версии 5.6.4 или более поздней. Полный список требований к серверу см. в разделе [Требования к серверу](https://laravel.com/docs/5.3#server-requirements). В остальной части списка содержатся расширения, которые включены при установке PHP в Azure. 
* Укажите переменные среды, необходимые для приложения. Чтобы легко задать переменные среды, в приложении Laravel используется файл `.env`. Но так как он не должен фиксироваться в системе управления версиями (см. раздел о [настройке среды для приложения Laravel](https://laravel.com/docs/5.3/configuration#environment-configuration)), вместо этого вы можете настроить параметры для веб-приложения Azure.
* Сначала загрузите точку входа приложения Laravel `public/index.php`. Ознакомьтесь с [обзором жизненного цикла Laravel](https://laravel.com/docs/5.3/lifecycle#lifecycle-overview). Другими словами, необходимо задать корневой URL-адрес веб-приложения, указывающий на каталог `public`.
* Включите расширение Composer в Azure при наличии файла composer.json. Таким образом, во время развертывания с помощью `git push`Composer будет контролировать получение требуемых пакетов. Так удобнее. 
  Если автоматизация Composer не включена, необходимо просто удалить `/vendor` из файла `.gitignore`, чтобы Git включал ("не игнорировал") все содержимое каталога `vendor` во время выполнения и развертывания кода.

Давайте последовательно выполним эти задачи.

1. Установите версию PHP, которая требуется для приложения Laravel.
   
        az appservice web config update --php-version 7.0 --name <app_name> --resource-group my-php-app-group
   
    Теперь версия PHP установлена. 

2. Создайте новый ключ `APP_KEY` для веб-приложения Azure и установите его в качестве параметра приложения.
   
        php artisan key:generate --show
        az appservice web config appsettings update --settings APP_KEY="<output_of_php_artisan_key:generate_--show>" --name <app_name> --resource-group my-php-app-group

3. Включите отладку Laravel, чтобы устранять все зашифрованные страницы `Whoops, looks like something went wrong.` .
   
        az appservice web config appsettings update --settings APP_DEBUG=true --name <app_name> --resource-group my-php-app-group
   
    Теперь переменные среды указаны.
   
   > [!NOTE]
   > Давайте не будем торопиться и разберемся, что конкретно выполняется в Laravel и Azure. Приложение Laravel использует файл `.env` в корневом каталоге, чтобы предоставить приложению переменные среды. В этом файле содержится строка `APP_DEBUG=true` (а также `APP_KEY=...`). К этой переменной в `config/app.php` обращается код `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.3/helpers#method-env) — это вспомогательный метод Laravel, в котором используется [getenv()](http://php.net/manual/en/function.getenv.php) PHP.
   > 
   > Но `.env` игнорируется Git, так как он вызывается с помощью файла `.gitignore` в корневом каталоге. Проще говоря, `.env` в локальном репозитории Git не отправляется в Azure вместе с остальными файлами. Конечно, можно просто удалить эту строку из `.gitignore`, но мы уже выяснили, что этот файл не должен фиксироваться в системе управления версиями. Тем не менее по-прежнему нужен метод, чтобы задать эти переменные среды в Azure. 
   > 
   > Хорошо, что параметры приложения в службе приложений Azure поддерживают [getenv()](http://php.net/manual/en/function.getenv.php) в PHP. Чтобы вручную отправить файл `.env` в Azure, можно использовать протокол FTP или другие средства. Вы все также можете просто указать необходимые переменные для параметров приложения Azure, не используя файл `.env` в Azure. Кроме того, если переменная указана и в файле `.env`, и в параметрах приложения Azure, параметры приложения Azure являются приоритетными.     
   > 
   > 
4. Для последних двух заданий (настройка виртуального каталога и включение Composer) нужно войти на [портал Azure](https://portal.azure.com) с помощью учетной записи [портала](https://portal.azure.com) Azure.
5. В меню слева щелкните **Службы приложений** > **&lt;имя_приложения>** > **Расширения**.
   
    ![Включите Composer для приложения PHP (Laravel) в Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
   
6. Щелкните **Добавить**, чтобы добавить расширение.
7. Выберите **Composer** в [колонке](../azure-resource-manager/resource-group-portal.md#manage-resources) **Выберите расширение** (*колонка* — это страница на портале, которая открывается горизонтально).
8. Нажмите кнопку **ОК** в [колонке](../azure-resource-manager/resource-group-portal.md#manage-resources) **Принять юридические условия**. 
9. Нажмите кнопку **ОК** в [колонке](../azure-resource-manager/resource-group-portal.md#manage-resources) **Добавить расширение**.
   
    После добавления расширений в Azure в углу отобразится всплывающее сообщение и вы увидите, что в [колонке](../azure-resource-manager/resource-group-portal.md#manage-resources) **Расширения** добавлено расширение  **Composer**.
   
    ![Колонка расширений после включения расширения Composer для приложения PHP (Laravel) в Azure](./media/app-service-web-php-get-started/configure-composer-end.png)
   
    Теперь расширение Composer включено.
10. Вернитесь в [колонку ресурсов](../azure-resource-manager/resource-group-portal.md#manage-resources) веб-приложения и щелкните **Параметры приложения**.
    
     ![Откройте колонку параметров для настройки виртуального каталога для приложения PHP (Laravel) в Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)
    
     В [колонке](../azure-resource-manager/resource-group-portal.md#manage-resources) **Параметры приложения** обратите внимание на ранее заданную версию PHP:
    
     ![Версия PHP в колонке параметров для приложения PHP (Laravel) в Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a-cli2.png)
    
     Просмотрите также добавленные параметры приложения:
    
     ![Параметры приложения в колонке параметров для приложения PHP (Laravel) в Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)
11. Перейдите к нижней части [колонки](../azure-resource-manager/resource-group-portal.md#manage-resources) и измените корневой виртуальный каталог, указав **site\wwwroot\public** вместо **site\wwwroot**.
    
     ![Настройте виртуальный каталог для приложения PHP (Laravel) в Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)
12. Щелкните **Сохранить** в верхней части [колонки](../azure-resource-manager/resource-group-portal.md#manage-resources).
    
     Теперь виртуальный каталог настроен. 

## <a name="deploy-your-web-app-with-git-and-setting-environment-variables"></a>Развертывание веб-приложения с помощью Git (и указание переменных среды)
Теперь все готово к развертыванию кода. Для этого необходимо снова открыть командную строку или терминал.

1. Передайте код для веб-приложения Azure так же, как и для любого репозитория Git:
   
        git push azure master 
   
    При появлении запроса укажите учетные данные, созданные ранее.

2. Давайте запустим веб-приложение в браузере с помощью следующей команды:
   
        az appservice web browse --name <app_name> --resource-group my-php-app-group
   
    В браузере должен отобразиться экран-заставка приложения Laravel.
   
    ![Экран-заставка приложения Laravel после развертывания веб-приложения в Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
   
    Теперь веб-приложение Laravel выполняется в Azure.

## <a name="troubleshoot-common-errors"></a>Устранение распространенных ошибок
Ниже приведены некоторые ошибки, с которыми можно столкнуться при работе с этим руководством.

* [В интерфейсе командной строки Azure отображается сообщение 'site' is not an azure command](#clierror)
* [В веб-приложении отображается ошибка HTTP 403](#http403)
* [В веб-приложении отображается сообщение Whoops, looks like something went wrong](#whoops)
* [В веб-приложении отображается сообщение No supported encryptor found](#encryptor)

<a name="clierror"></a>

### <a name="azure-cli-shows-site-is-not-an-azure-command"></a>В интерфейсе командной строки Azure отображается сообщение 'site' is not an azure command
При выполнении `azure site *` в терминале командной строки отобразится ошибка `error:   'site' is not an azure command. See 'azure help'.` 

Обычно это является результатом переключения в режим Azure Resource Manager (ARM). Чтобы устранить эту проблему, переключитесь обратно в режим управления службами Azure (ASM), выполнив команду `azure config mode asm`.

<a name="http403"></a>

### <a name="web-app-shows-http-403-error"></a>В веб-приложении отображается ошибка HTTP 403
Вы успешно развернули веб-приложение в Azure, но при его просмотре появляется сообщение `HTTP 403` или `You do not have permission to view this directory or page.`

Скорее всего, это связано с тем, что веб-приложению не удается найти точку входа в приложение Laravel. Для корневого виртуального каталога необходимо задать `site\wwwroot\public`, где находится `index.php` Laravel (см. раздел [Настройка веб-приложения Azure](#configure)).

<a name="whoops"></a>

### <a name="web-app-shows-whoops-looks-like-something-went-wrong"></a>В веб-приложении отображается сообщение Whoops, looks like something went wrong
Вы успешно развернули веб-приложение в Azure, но при его просмотре появляется зашифрованное сообщение `Whoops, looks like something went wrong.`

Чтобы получить сообщение об ошибке с подробным описанием, включите отладку Laravel, установив для переменной среды `APP_DEBUG` значение `true` (см. раздел [Настройка веб-приложения Azure](#configure)).

<a name="encryptor"></a>

### <a name="web-app-shows-no-supported-encryptor-found"></a>В веб-приложении отображается сообщение No supported encryptor found
Вы успешно развернули веб-приложение в Azure, но при его просмотре появляется следующее сообщение об ошибке:

![Параметр APP_KEY отсутствует в приложении PHP (Laravel) в Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)

Это опасная ошибка, но по крайней мере она не зашифрована, так как включена отладка Laravel. Выполнив поверхностный поиск строки ошибки на форумах Laravel, вы узнаете, что эта ошибка возникла, потому что в `.env` не задан параметр APP_KEY или, как в вашем случае, потому что `.env` вообще не используется в Azure. Чтобы исправить эту ошибку, добавьте `APP_KEY` в качестве параметра приложения Azure (см. раздел [Настройка веб-приложения](#configure)).

## <a name="next-steps"></a>Дальнейшие действия
Узнайте, как добавить данные в приложение в разделе [Создание базы данных MySQL в Azure](../store-php-create-mysql-database.md). Кроме того, ознакомьтесь с полезными ресурсами для PHP в Azure, ссылки на которые приведены ниже.

* [Центр разработчиков PHP](/develop/php/)
* [Создание веб-приложения из Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
* [Настройка PHP в веб-приложениях службы приложений Azure](web-sites-php-configure.md)
* [Преобразование сайта WordPress в мультисайт с помощью службы приложений Azure](web-sites-php-convert-wordpress-multisite.md)
* [Сайт WordPress корпоративного класса в службе приложений Azure](web-sites-php-enterprise-wordpress.md)




<!--HONumber=Dec16_HO3-->


