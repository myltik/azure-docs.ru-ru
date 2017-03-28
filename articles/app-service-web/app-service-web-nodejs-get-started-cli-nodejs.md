---
title: "Начало работы с веб-приложениями Node.js в службе приложений Azure | Документация Майкрософт"
description: "Сведения о развертывании приложения Node.js в веб-приложение в службе приложений Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9be7aec6d66c625be83b93bb840ad137d3c786d2
ms.lasthandoff: 03/21/2017


---
# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Начало работы с веб-приложениями Node.js в службе приложений Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

В этом руководстве показано, как создать простое приложение [Node.js] и развернуть его в [службе приложений Azure] из командной строки, например cmd.exe или Bash. Инструкции, приведенные в этом руководстве, применимы к любой операционной системе, в которой может работать Node.js.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи

Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0](app-service-web-nodejs-get-started-cli-nodejs.md) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.
- [Azure CLI 2.0](app-service-web-nodejs-get-started.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

## <a name="prerequisites"></a>Предварительные требования
* [Node.js]
* [Bower]
* [Yeoman]
* [Git.]
* [Интерфейс командной строки Azure]
* Учетная запись Microsoft Azure. Если у вас нет учетной записи, [подпишитесь на бесплатную пробную версию] или [активируйте преимущества для подписчиков Visual Studio].

> [!NOTE]
> [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/) возможно даже без учетной записи Azure. Вы можете создать приложение начального уровня и экспериментировать с ним в течение часа. Для этого вам не нужно указывать данные кредитной карты или брать на себя какие-либо обязательства.
> 
> 

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Создание и развертывание простого веб-приложения Node.js
1. Откройте любой терминал командной строки и установите [генератор Express для Yeоman].
   
        npm install -g generator-express
2. `CD` в рабочий каталог и создайте приложение Еxpress с использованием следующего синтаксиса:
   
        yo express
   
    Выберите следующие параметры при появлении запроса:  
   
    `? Would you like to create a new directory for your project?` **Yes**  
    `? Enter directory name` **{имя_приложения}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **None**  
    `? Select a database to use:` **None**  
    `? Select a build tool to use:` **Grunt**
3. Перейдите с помощью команды `CD` в корневой каталог нового приложения и запустите его, чтобы проверить его работоспособность в среде разработки.
   
        npm start
   
    В браузере перейдите по адресу <http://localhost:3000>, чтобы проверить доступность домашней страницы Express. Убедившись в работоспособности приложения, остановите его с помощью команды `Ctrl-C` .
4. Перейдите в режим ASM и войдите в Azure (с помощью [интерфейса командной строки Azure](#prereq)):
   
        azure config mode asm
        azure login
   
    Следуйте указаниям, чтобы продолжить вход в браузере с помощью учетной записи Майкрософт в рамках своей подписки Azure.


3. Укажите пользователя развертывания для службы приложений. Вы развернете код позже с помощью учетных данных.
   
        azure site deployment user set --username <username> --pass <password>

5. Убедитесь, что вы по-прежнему находитесь в корневом каталоге приложения, а затем создайте ресурс приложения службы приложений в Azure с уникальным именем приложения при помощи следующей команды. Например: http://{имя_приложения}.azurewebsites.net
   
        azure site create --git {appname}
   
    Следуйте указаниям, чтобы выбрать регион Azure для развертывания. 
6. Откройте файл ./config/config.js в корневом каталоге приложения и измените рабочий порт на `process.env.port`. Свойство `production` объекта `config` должно выглядеть, как в следующем примере.
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > По умолчанию служба приложений Azure запускает приложения Node.js с переменными среды `production` (`process.env.NODE_ENV="production"`).
    > Такая конфигурация позволяет приложению Node.js в Azure отвечать на веб-запросы с использованием порта по умолчанию, который прослушивается iisnode.
    >
    >

7. Откройте файл ./package.json и добавьте свойство `engines` , чтобы [указать нужную версию Node.js](#version).
   
        "engines": {
            "node": "6.9.1"
        }, 
8. Сохраните изменения, а затем разверните приложение в Azure с помощью команды git. При появлении запроса укажите учетные данные пользователя, созданные ранее.
   
        git add .
        git add -f config
        git commit -m "{your commit message}"
        git push azure master
   
    В генератор Express уже входит GITIGNORE-файл, поэтому команда `git push` не будет использовать пропускную способность при попытках отправки данных в каталог node_modules/.
9. Наконец, запустите живое приложение Azure в браузере.
   
        azure site browse
   
    Теперь вы увидите веб-приложение Node.js, запущенное в службе приложений Azure в режиме реального времени.
   
    ![Пример перехода к развернутому приложению.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Обновление веб-приложения Node.js
Чтобы обновить веб-приложение Node.js, запущенное в службе приложений, просто запустите `git add`, `git commit` и `git push` так же, как и при первом развертывании веб-приложения.

## <a name="how-app-service-deploys-your-nodejs-app"></a>Как служба приложений развертывает приложение Node.js
Для запуска приложений Node.js служба приложений Azure использует [iisnode] . Совместное использование Azure CLI и ядра Kudu (развертывание Git) упрощает разработку и развертывание приложений Node.js из командной строки. 

* Команда `azure site create --git` распознает общий шаблон Node.js (server.js или app.js) и создает файл iisnode.yml в корневом каталоге. Этот файл можно использовать для настройки iisnode.
* С помощью команды `git push azure master` Kudu автоматизирует следующие задачи развертывания:
  
  * Если файл package.json находится в корневой папке репозитория, выполните команду `npm install --production`.
  * Создайте файл Web.config для iisnode, который указывает на ваш сценарий запуска в файле package.json (например, server.js или app.js).
  * Настройте файл Web.config для подготовки приложения к отладке с помощью Node-Inspector.

## <a name="use-a-nodejs-framework"></a>Использование платформы Node.js
Если для разработки приложений вы используете популярную платформу Node.js (например, [Sails.js][SAILSJS] или [MEAN.js][MEANJS]), вы можете развернуть их в службе приложений. Популярные платформы Node.js имеют свои особенности. Их связанные зависимости пакетов регулярно обновляются. Так как при этом служба приложений предоставляет доступ к журналам stdout и stderr, вы точно будете знать, что происходит с вашим приложением, чтобы вносить соответствующие изменения. Дополнительные сведения см. в разделе [Получение журналов stdout и stderr из iisnode](#iisnodelog).

В следующих руководствах показано, как работать с конкретной платформой в службе приложений:

* [Развертывание веб-приложения Sails.js в службе приложений Azure]
* [Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure]
* [Как использовать io.js с веб-приложениями службы приложений Azure]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>Использование определенного модуля Node.js
В рамках обычного рабочего процесса вы настраиваете службу приложений для использования определенного модуля Node.js, как и в файле package.json.
Например:

    "engines": {
        "node": "6.9.1"
    }, 

Подсистема развертывания Kudu определяет, какой модуль Node.js будет использоваться, в следующем порядке:

* Сначала проверьте, указан ли в файле iisnode.yml элемент `nodeProcessCommandLine`. Если элемент указан, используйте его.
* Затем проверьте, указан ли в файле package.json элемент `"node": "..."` в объекте `engines`. Если элемент указан, используйте его.
* Выберите версию Node.js по умолчанию.

Обновленный список всех поддерживаемых версий Node.js (NPM) в службе приложений Azure доступен по следующему URL-адресу:

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> Рекомендуется явно определить нужный модуль Node.js. Версия Node.js по умолчанию может измениться, и в веб-приложении могут возникнуть ошибки, так как версия Node.js по умолчанию не подходит для вашего приложения.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Получение журналов stdout и stderr из iisnode
Для чтения журналов iisnode выполните следующие действия.

> [!NOTE]
> Не исключено, что после выполнения этих действий файлы журналов появятся, только когда произойдет ошибка.
> 
> 

1. Откройте файл iisnode.yml, который предоставляет инфраструктура Azure CLI.
2. Укажите следующие параметры: 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Эти два параметра указывают iisnode в службе приложений поместить выходные данные stdout и stderror в каталог D:\home\site\wwwroot\**iisnode**.
3. Сохраните изменения, а затем примените изменения в Azure с помощью следующих команд Git:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    Вы настроили iisnode. Далее показано, как получить доступ к этим журналам.
4. В браузере откройте консоль отладки Kudu для своего приложения по адресу:
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    Этот URL-адрес отличается от URL-адреса веб-приложения тем, что к имени DNS добавляется *расширение SCM* . Если это не сделано, вы получите сообщение об ошибке 404.
5. Перейдите в каталог D:\home\site\wwwroot\iisnode.
   
    ![Переход в расположение файлов журнала iisnode.][iislog-kudu-console-find]
6. Щелкните значок **Изменить** для журнала, который вы хотите прочитать. При необходимости также можно щелкнуть **Скачать** или **Удалить**.
   
    ![Открытие файла журнала iisnode.][iislog-kudu-console-open]
   
    Теперь вы можете просмотреть журнал для отладки развертывания службы приложений.
   
    ![Анализ файла журнала iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Отладка приложения с помощью Node-Inspector
Если вы выполняете отладку приложений Node.js с помощью Node-Inspector, это средство можно использовать также для живого приложения службы приложений. Средство Node-Inspector предустановлено в установке iisnode для службы приложений. Если развертывание выполняется через Git, автоматически созданный в Kudu файл Web.config уже содержит все настройки, необходимые для включения Node-Inspector.

Включить Node-Inspector можно так.

1. Откройте файл iisnode.yml в корневой папке репозитория и укажите следующие параметры: 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. Сохраните изменения, а затем примените изменения в Azure с помощью следующих команд Git:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. Теперь просто перейдите к файлу запуска приложения, добавив /debug к URL-адресу, указанному в скрипте запуска в вашем файле package.json. Например,
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    или
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Дополнительные ресурсы
* [Указание версии Node.js в приложении Azure](../nodejs-specify-node-version-azure-apps.md)
* [Рекомендации и руководство по устранению неполадок приложений Node.js в Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Отладка веб-приложения Node.js в службе приложений Azure](web-sites-nodejs-debug.md)
* [Использование модулей Node.js с приложениями Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js (Веб-приложения службы приложений Azure: Node.js)](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Центр разработчиков Node.js.](/develop/nodejs/)
* [Начало работы с веб-приложениями в службе приложений Azure](app-service-web-get-started.md)
* [Exploring the Super Secret Kudu Debug Console (Обзор сверхсекретной консоли для отладки Kudu)]

<!-- URL List -->

[Интерфейс командной строки Azure]:../cli-install-nodejs.md
[службе приложений Azure]: ../app-service/app-service-value-prop-what-is.md
[активируйте преимущества для подписчиков Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Создание приложения для разговоров на Node.js с использованием Socket.IO в службе приложений Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Развертывание веб-приложения Sails.js в службе приложений Azure]: ./app-service-web-nodejs-sails.md
[Exploring the Super Secret Kudu Debug Console (Обзор сверхсекретной консоли для отладки Kudu)]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[генератор Express для Yeоman]: https://github.com/petecoop/generator-express
[Git.]: http://www.git-scm.com/downloads
[Как использовать io.js с веб-приложениями службы приложений Azure]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[подпишитесь на бесплатную пробную версию]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png

