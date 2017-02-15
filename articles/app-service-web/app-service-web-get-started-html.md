---
title: "Развертывание первого веб-приложения в Azure за пять минут (предварительная версия CLI 2.0) | Документация Майкрософт"
description: "Узнайте, как можно быстро запускать веб-приложения в службе приложений, развернув пример приложения. Начните с разработки настоящего приложения — и сразу же получите результаты."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 05e61d2fc751c4239aef4b10ad897765c59fe928
ms.openlocfilehash: 42f3f17366a37e10d746fe177a93a2432dbdcca2


---
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes-cli-20-preview"></a>Развертывание первого веб-приложения в Azure за пять минут (предварительная версия CLI 2.0)

> [!div class="op_single_selector"]
> * [Первый HTML-сайт](app-service-web-get-started-html.md)
> * [Первое приложение .NET](app-service-web-get-started-dotnet.md)
> * [Первое приложение PHP](app-service-web-get-started-php.md)
> * [Первое приложение Node.js](app-service-web-get-started-nodejs.md)
> * [Первое приложение Python](app-service-web-get-started-python.md)
> * [Первое приложение Java](app-service-web-get-started-java.md)
> 
> 

Это руководство поможет вам развернуть простое веб-приложение HTML+CSS в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md).
В службе приложений можно создавать веб-приложения, [серверные части мобильных приложений](/documentation/learning-paths/appservice-mobileapps/) и [приложения API](../app-service-api/app-service-api-apps-why-best-platform.md).

Вы сможете выполнять следующие задачи: 

* создавать веб-приложения в службе приложений Azure;
* Развертывать в них код HTML и CSS.
* Просматривать страницы, выполняющиеся в рабочей среде в реальном времени.
* Обновлять содержимое так же, как вы [отправляете фиксации Git](https://git-scm.com/docs/git-push).

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](app-service-web-get-started-html-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0 (предварительная версия)](app-service-web-get-started-html.md) — интерфейс командной строки нового поколения для модели развертывания Resource Manager.

## <a name="prerequisites"></a>Предварительные требования
* [Git](http://www.git-scm.com/downloads).
* [Azure CLI 2.0 (предварительная версия)](/cli/azure/install-az-cli2).
* Учетная запись Microsoft Azure. Если у вас нет учетной записи, [подпишитесь на бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) или [активируйте преимущества для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751) возможно даже без учетной записи Azure. Вы можете создать приложение начального уровня и экспериментировать с ним в течение часа. Для этого вам не нужно указывать данные кредитной карты или брать на себя какие-либо обязательства.
> 
> 

## <a name="deploy-a-simple-html-site"></a>Развертывание простого HTML-сайта
1. Откройте новое окно командной строки Windows, окно PowerShell, оболочку Linux или терминал OS X. Выполните команды `git --version` и `azure --version`, чтобы убедиться, что система GIT и интерфейс командной строки Azure установлены на компьютере.
   
    ![Тестирование установки средств интерфейса командной строки для первого веб-приложения в Azure](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    Если вы еще не установили необходимые инструменты, то ссылки для их скачивания доступны в разделе [Предварительные требования](#Prerequisites).
2. Войдите в Azure:
   
        az login
   
    Следуйте инструкциям из справочного сообщения, чтобы продолжить процесс входа в систему.
   
    ![Вход в Azure для создания первого веб-приложения](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. Укажите пользователя развертывания для службы приложений. Вы развернете код позже с помощью этих учетных данных.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md). В рамках этого руководства по использованию службы приложений вам не обязательно знать, что это такое.

        az group create --location "<location>" --name my-first-app-group

    Чтобы увидеть доступные значения для `<location>`, используйте команду `az appservice list-locations` интерфейса командной строки.

3. Создайте [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) уровня "Бесплатный". В рамках этого руководства по использованию службы приложений вам достаточно знать, что это план не предусматривает выставление счетов за веб-приложения.

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. Создайте веб-приложение с уникальным именем в `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. Затем получите пример HTML-сайта, который необходимо развернуть. Перейдите в рабочий каталог (`CD`) и клонируйте пример приложения следующим образом:
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git

5. Перейдите в репозиторий примера приложения. 
   
        cd app-service-web-html-get-started
5. Настройте локальное развертывание Git для веб-приложения службы приложений с помощью следующей команды:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    Если вы получите приблизительно следующие выходные данные JSON, это значит, что удаленный репозиторий Git настроен:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Добавьте URL-адрес в JSON в качестве удаленного репозитория Git для локального репозитория (для простоты называется `azure`).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Разверните пример кода в приложении Azure так же, как вы отправляете любой код с помощью Git. При появлении запроса введите пароль, который вы настроили ранее.
   
        git push azure master
   
    ![Отправка кода в первое веб-приложение в Azure](./media/app-service-web-get-started/5-push-code.png)
   
    Если используется одна из языковых платформ, выходные данные будут отличаться. Это происходит потому, что команда `git push` не только помещает код в Azure, но и активирует задачи развертывания в подсистеме развертывания. Если в корневом каталоге проекта (репозитория) содержится файл package.json (Node.js) или файл requirements.txt (Python) либо если в проекте ASP.NET содержится файл packages.config, скрипт развертывания сможет автоматически восстановить необходимые пакеты. Вы также можете [включить расширение Composer](web-sites-php-mysql-deploy-use-git.md#composer) для автоматической обработки файлов composer.json в приложении PHP.

Поздравляем, вы развернули свое приложение в службе приложений Azure.

## <a name="see-your-app-running-live"></a>Наблюдение за работой приложения в реальном времени
Чтобы увидеть работу приложения в Azure в реальном времени, выполните в любом каталоге репозитория следующую команду:

    azure site browse

## <a name="make-updates-to-your-app"></a>Обновление приложения
Теперь с помощью Git можно в любой момент передать на рабочий сайт изменения из корневого каталога проекта (репозитория). Для этого нужно выполнить те же действия, что и при первом развертывании кода. Например, каждый раз, когда вам нужно отправить новое изменение, протестированное локально, просто выполните следующие команды из корневого каталога проекта (репозитория):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Дальнейшие действия
Определите предпочтительный способ разработки и этапы развертывания приложений в соответствии с используемой языковой платформой:

* [.NET](web-sites-dotnet-get-started.md)
* [PHP](app-service-web-php-get-started.md)
* [Node.js](app-service-web-nodejs-get-started.md)
* [Python](web-sites-python-ptvs-django-mysql.md)
* [Java](web-sites-java-get-started.md)

Вы также можете продолжить работу над своим первым веб-приложением. Например:

* Попробуйте [другие способы развертывания кода в Azure](web-sites-deploy.md). Например, чтобы развернуть приложение из какого-либо репозитория на GitHub, в разделе **Параметры развертывания** нужно просто указать **GitHub**, а не **Локальный репозиторий Git**.
* Выведите приложение Azure на следующий уровень. Проверяйте подлинность пользователей. Масштабируйте приложение в зависимости от потребностей. Настраивайте оповещения производительности. И все это — с помощью нескольких действий. См. статью [Добавление функциональных возможностей в первое веб-приложение](app-service-web-get-started-2.md).




<!--HONumber=Jan17_HO1-->


