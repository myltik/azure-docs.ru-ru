---
title: "Создание приложения Ruby с помощью веб-приложения службы приложений Azure на платформе Linux | Документация Майкрософт"
description: "Узнайте, как создавать приложения Ruby с помощью веб-приложения службы приложений Azure на платформе Linux."
keywords: "служба приложений azure, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>Создание приложения Ruby с помощью веб-приложения на платформе Linux (предварительная версия)

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Обзор

В этом руководстве показано, как создать базовое приложение Ruby on Rails в локальной среде и развернуть его в веб-приложении Azure на платформе Linux.

## <a name="prerequisites"></a>Предварительные требования

* На компьютере разработки должна быть установлена среда [Ruby 2.3.3 или более поздней версии](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller).
* На компьютере разработки должен быть установлен компонент [Git](https://git-scm.com/downloads).
* [Активная подписка Azure](https://azure.microsoft.com/pricing/free-trial/).
* Это руководство написано в контексте среды Ubuntu. Все системные команды относятся к bash.


## <a name="create-a-new-local-rails-application"></a>Создание локального приложения Rails

1. Создайте каталог для нового приложения и перейдите в этот каталог.

        mkdir ~/workspace
        cd ~/workspace

2. Инициализируйте Ruby и проверьте версию, выполнив команду `ruby -v`.

    ![Инициализация Ruby](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. Установите Rails с помощью команды `gem install rails`.

    ![Установка Rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. Создайте приложение Rails **hello-world** с помощью следующей команды.

    Если вы используете Rails 5.1.0 или более поздней версии, добавьте параметр `--skip-yarn`, как показано в следующей команде.

        rails new hello-world --skip-yarn

    Для версий Rails, предшествующих версии 5.1.0, используйте приведенную ниже команду.

        rails new hello-world 

    ![Новое приложение Hello World](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![Новое приложение Hello World](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    При использовании Rails 5.1 или более поздней версии создается файл package.json, если параметр `--skip-yarn` не используется. Нам не нужно добавлять его развертывание. В качестве альтернативы можно удалить файл package.json или добавить его в файл *.git-ignore* в каталоге следующим образом. 

        # Ignore package.json
        /package.json

5. Перейдите в каталог *hello-world* и запустите сервер.

        cd hello-world
        rails server

    ![Запуск приложения Hello World](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. С помощью веб-браузера перейдите к `http://localhost:3000`, чтобы протестировать приложение локально.    

    ![Приложение Hello World](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>Подготовка приложения для Azure

По умолчанию образ Ruby запускает сервер с флагом `-e production`. Эта среда требует определенной настройки для веб-приложения Azure на платформе Linux. Контейнер берет на себя часть этой настройки (например, параметр `SECRET_KEY_BASE`). Необходимо настроить маршрут по умолчанию. В противном случае при переходе на сайт появится сообщение об ошибке 404.

Вот как можно настроить маршрут по умолчанию.

1. Откройте файл *~/workspace/hello-world/config/routes.rb* для редактирования. Добавьте в него приведенную строку, как показано на снимке экрана. 

        root 'application#hello'

    ![Файл routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. Откройте файл *~/workspace/hello-world/app/controllers/application_controller.rb* для редактирования. Добавьте в него приведенные ниже строки, как показано на снимке экрана.

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![Файл routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. Теперь приложение настроено. С помощью веб-браузера перейдите к `http://localhost:3000`, чтобы проверить корневую целевую страницу.

    ![Приложение Hello World настроено](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>Создание веб-сайта Ruby в Azure

1. Войдите на [портал Azure](http://portal.azure.com) с использованием своей подписки. Добавьте **веб-приложение на платформе Linux**, как показано на снимке экрана ниже.

    ![Создание веб-приложения на платформе Linux](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. Откроется колонка **Создание**, как показано на следующем снимке экрана.

    ![Колонка "Создание"](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. Присвойте веб-приложению имя.
    2. Выберите имеющуюся группу ресурсов или создайте новую. (Доступные регионы см. в [разделе с ограничениями](app-service-linux-intro.md).)
    3. Выберите имеющийся план службы приложений Azure или создайте новый. (Заметки о плане службы приложений см. в [разделе с ограничениями](app-service-linux-intro.md).)
    4. Выберите встроенный стек времени выполнения **Ruby 2.3** для конфигурации контейнера.
    5. Щелкните **Закрепить на панели мониторинга** для веб-приложения.
    6. Щелкните **Создать**.

3. После создания веб-приложения отобразится колонка **Обзор**. Скопируйте **URL-адрес** нового веб-приложения и откройте его в браузере. Отобразится следующая страница заставки.

    ![Страница заставки](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>Развертывание приложения

В этом руководстве мы используем Git для развертывания локального приложения Ruby в Azure.

1. Для нового веб-сайта Azure уже настроено развертывание Git. URL-адрес развертывания Git можно найти, перейдя по приведенному ниже URL-адресу после вставки имение своего веб-приложения.

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    URL-адрес Git имеет следующий вид в соответствии с именем вашего веб-приложения.

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. Выполните следующие команды, чтобы развернуть локальное приложение на веб-сайте Azure.

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    Убедитесь, что операции удаленного развертывания успешно выполнены.

    ![Развертывание веб-приложения](./media/app-service-linux-ruby-get-started/deployment-success.png)

    Если отображается сообщение о том, что удаленное развертывание перестало отвечать на запросы, вероятнее всего развертывание все еще выполняется. В этом случае перейдите по приведенному ниже URL-адресу в браузере.

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. После завершения развертывания перезапустите веб-приложение, чтобы задействовать развернутую службу. На [портале Azure](http://portal.azure.com) перейдите к колонке **Обзор** веб-приложения.

    Щелкните **Перезапустить** на панели инструментов.

    ![Перезапуск веб-приложения](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. Перейдите на сайт и убедитесь, что изменения вступили в силу. 

    Во время перезапуска приложения попытка просмотреть сайта приведет к ошибке для кода состояния HTTP 503 (сервер недоступен). На полный перезапуск может потребоваться несколько минут.

        http://{your web app name}.azurewebsites.net

    ![Обновленное веб-приложение](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о веб-приложении службы приложений Azure на платформе Linux можно получить, воспользовавшись приведенными ниже ссылками. Если у вас возникли вопросы, опубликуйте их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creating Web Apps in App Service on Linux](app-service-linux-how-to-create-web-app.md) (Создание веб-приложений в службе приложений на платформе Linux)
* [Как применять пользовательские образы Docker в службе приложений на платформе Linux](app-service-linux-using-custom-docker-image.md)
* [Использование конфигурации PM2 для Node.js в веб-приложениях на платформе Linux](app-service-linux-using-nodejs-pm2.md)
* [Использование .NET Core в веб-приложениях службы приложений Azure на платформе Linux](app-service-linux-using-dotnetcore.md)
* [Вопросы и ответы о веб-приложениях службы приложений Azure на платформе Linux](app-service-linux-faq.md)


