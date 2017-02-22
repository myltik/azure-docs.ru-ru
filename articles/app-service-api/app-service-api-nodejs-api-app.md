---
title: "Приложение API Node.js в службе приложений Azure | Документация Майкрософт"
description: "Узнайте, как создать API RESTful Node.js и развернуть его в приложении API с помощью службы приложений Azure."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: wpickett
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: get-started-article
ms.date: 05/26/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 633caca05835aa204d4fec5fe216043a50520000
ms.openlocfilehash: 87d2792d4f7610c62ed96cb522958844d4dc982b


---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Создание API RESTful Node.js и его развертывание в приложении API в Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

В этом руководстве описано, как создать простой API-интерфейс [Node.js](http://nodejs.org) и развернуть его в [приложении API](app-service-api-apps-why-best-platform.md) с помощью [службы приложений Azure](../app-service/app-service-value-prop-what-is.md) и [GIT](http://git-scm.com). Для этой процедуры можно использовать любую операционную систему, поддерживающую Node.js, а также такие программы командной строки, как cmd.exe или Bash.

## <a name="prerequisites"></a>Предварительные требования
1. Учетная запись Microsoft Azure. ([Создайте бесплатную учетную запись здесь](https://azure.microsoft.com/pricing/free-trial/).)
2. Установленная платформа [Node.js](http://nodejs.org). (В этом примере используется Node.js версии 4.2.2.)
3. [GIT](https://git-scm.com/) .
4. [GitHub](https://github.com/) .

Хотя служба приложений и поддерживает множество способов развертывания кода в приложении API, в этом руководстве показан метод, предусматривающий использование Git. Предполагается, что вы располагаете достаточными знаниями о работе с ним. Дополнительные сведения о методах развертывания см. в разделе [Развертывание приложения в службе приложений Azure](../app-service-web/web-sites-deploy.md).

## <a name="get-the-sample-code"></a>Получение кода примера
1. Откройте интерфейс командной строки, где можно выполнять команды Node.js и Git.
2. Перейдите к папке, которую можно использовать в качестве локального репозитория Git, и клонируйте [репозиторий GitHub, содержащий пример кода](https://github.com/Azure-Samples/app-service-api-node-contact-list).
   
        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git
   
    В примере API предоставляются две конечные точки: запрос Get к `/contacts` возвращает список имен и адресов электронной почты в формате JSON, а `/contacts/{id}` — только выбранный контакт.

## <a name="scaffold-auto-generate-nodejs-code-based-on-swagger-metadata"></a>Формирование (автоматическое) шаблона кода Node.js на основе метаданных Swagger
[Swagger](http://swagger.io/) — это формат файла для метаданных, описывающих API RESTful. В службу приложений Azure [встроена поддержка метаданных Swagger](app-service-api-metadata.md). В этом разделе руководства моделируется рабочий процесс разработки API, в котором сначала создаются метаданные Swagger, а затем они используются при формировании (автоматическом создании) серверного кода для API. 

> [!NOTE]
> Этот раздел можно пропустить, если вас не интересует формирование кода Node.js на основе файла метаданных Swagger. Если нужно только развернуть пример кода в новом приложении API, перейдите прямо к разделу [Создание приложения API в Azure](#createapiapp) .
> 
> 

### <a name="install-and-execute-swaggerize"></a>Установка и выполнение Swaggerize
1. Выполните следующие команды, чтобы установить модули NPM **yo** и **generator-swaggerize** на глобальном уровне.
   
        npm install -g yo
        npm install -g generator-swaggerize
   
    Swaggerize — это средство, которое позволяет создать серверный код для API, описываемого с помощью файла метаданных Swagger. Файл Swagger, который будет использоваться, называется *api.json*. Он расположен в клонированном репозитории в папке *start*.
2. Перейдите к папке *start*, а затем выполните команду `yo swaggerize`. Swaggerize выдаст запрос на ряд параметров.  Для параметра **what to call this project** введите contactlist, для **path to swagger document** — api.json, а для **Express, Hapi, or Restify** — express.
   
        yo swaggerize
   
    ![Командная строка swaggerize](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
   
    **Примечание**. На этом этапе у вас может возникнуть ошибка. Способ ее устранения описан на следующем этапе.
   
    Swaggerize создает папку приложения, обработчики, файлы конфигурации, а также файл **package.json** . Чтобы создать страницу справки по Swagger, необходимо использовать обработчик представлений express.  
3. Если выполнение команды `swaggerize` завершится ошибкой "непредвиденный маркер" или "недопустимая escape-последовательность", устранить причину ошибки можно, изменив созданный файл *package.json* . В строке `regenerate` раздела `scripts` замените обратную косую черту перед *api.json* косой чертой, чтобы строка выглядела так:
   
         "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"
4. Перейдите к папке, в которой содержится сформированный код (в этом примере — в подпапку */start/ContactList*).
5. Запустите `npm install`.
   
        npm install
6. Затем установите модуль NPM **jsonpath** . 
   
        npm install --save jsonpath
   
    ![Установка jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)
7. Установите модуль NPM **swaggerize-ui** . 
   
        npm install --save swaggerize-ui
   
    ![Установка пользовательского интерфейса swaggerize](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### <a name="customize-the-scaffolded-code"></a>Настройка сформированного шаблона кода
1. Скопируйте папку **lib** из папки **start** в папку **ContactList**, созданную с помощью компонента формирования на основе скаффолдинга. 
2. Замените код в файле **handlers/contacts.js** следующим кодом. 
   
    Этот код использует данные JSON, хранящиеся в файле **lib/contacts.json**, который обрабатывается с помощью **lib/contactRepository.js**. Новый код contacts.js отвечает на HTTP-запросы, чтобы получить все контакты и вернуть их в виде полезных данных JSON. 
   
        'use strict';
   
        var repository = require('../lib/contactRepository');
   
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };
3. Замените код в файле **handlers/contacts/{идентификатор}.js** следующим кодом. 
   
        'use strict';
   
        var repository = require('../../lib/contactRepository');
   
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };
4. Замените код в файле **server.js** следующим кодом. 
   
    Изменения, внесенные в файл server.js, выделены с помощью комментариев, чтобы их можно было увидеть. 
   
        'use strict';
   
        var port = process.env.PORT || 8000; // first change
   
        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');
   
        var app = express();
   
        var server = http.createServer(app);
   
        app.use(bodyParser.json());
   
        app.use(swaggerize({
            api: path.resolve('./config/swagger.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));
   
        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));
   
        server.listen(port, function () { // fifth and final change
        });

### <a name="test-with-the-api-running-locally"></a>Тестирование API в локальном режиме работы
1. Активируйте сервер с помощью исполняемого файла командной строки Node.js. 
   
        node server.js
2. Если перейти по ссылке **http://localhost:8000/contacts**, можно увидеть выходные данные списка контактов в формате JSON (или появится запрос на скачивание в зависимости от браузера). 
   
    ![Вызов API всех контактов](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)
3. Если перейти по ссылке **http://localhost:8000/contacts/2**, можно увидеть контакт, представленный значением идентификатора.
   
    ![Вызов API определенного контакта](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)
4. Данные JSON Swagger обрабатываются с помощью конечной точки **/swagger** :
   
    ![JSON-файл Swagger контактов](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)
5. Пользовательский интерфейс Swagger обрабатывается с помощью конечной точки **/docs** . Для тестирования интерфейса API в пользовательском интерфейсе Swagger можно использовать расширенные возможности клиента HTML.
   
    ![Пользовательский интерфейс Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a name="a-idcreateapiappa-create-a-new-api-app"></a><a id="createapiapp"></a> Создание нового приложения API
В этом разделе рассматривается использование портала Azure для создания приложения API. Это приложение API представляет вычислительные ресурсы, предоставляемые Azure для выполнения кода. В следующих разделах описано, как развернуть свой код в новом приложении API.

1. Перейти на [портал Azure](https://portal.azure.com/). 
2. Щелкните **Создать > Интернет + мобильные устройства > Приложение API**. 
   
    ![Новое приложение API на портале](media/app-service-api-nodejs-api-app/new-api-app-portal.png)
3. В поле **Имя приложения** введите имя, которое будет уникальным в домене *azurewebsites.net*. Например, можно ввести имя NodejsAPIApp и номер, который сделает имя уникальным. 
   
    Например, если имя — `NodejsAPIApp`, URL-адрес будет `nodejsapiapp.azurewebsites.net`.
   
    Если ввести имя, которое уже используется, справа появится красный восклицательный знак.
4. В раскрывающемся меню **Группа ресурсов** щелкните **Создать**, а затем в поле **Новое имя группы ресурсов** введите имя NodejsAPIAppGroup или любое другое. 
   
    [Группа ресурсов](../azure-resource-manager/resource-group-overview.md) — это коллекция ресурсов Azure, таких как приложения API, базы данных и виртуальные машины. Мы рекомендуем создать новую группу ресурсов, так как это позволит быстро удалить все ресурсы Azure, созданные для работы с этим руководством.
5. Щелкните вкладку **Расположение или план службы приложений**, а затем нажмите кнопку **Создать**.
   
    ![Создание плана службы приложений](./media/app-service-api-nodejs-api-app/newappserviceplan.png)
   
    Далее мы создадим план службы приложений для новой группы ресурсов. План службы приложений определяет вычислительные ресурсы, на которых будет работать ваше приложение API. Например, если выбрать уровень "Бесплатный", ваше приложение будет работает на общих виртуальных машинах, тогда как при выборе некоторых платных уровней приложение будет работать на выделенных виртуальных машинах. Дополнительные сведения см. в статье [Подробный обзор планов службы приложений Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
6. В колонке **План службы приложений** введите NodejsAPIAppPlan или другое имя.
7. В раскрывающемся списке **Расположение** выберите ближайшее расположение.
   
    Этот параметр определяет, в каком центре обработки данных Azure будет выполняться приложение. При работе с учебником вы можете выбрать любой регион, и это не будет оказывать существенного влияния. Тем не менее для рабочего приложения необходимо, чтобы сервер располагался как можно ближе к клиентам, которые осуществляют к нему доступ. Это позволит минимизировать [задержки](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).
8. Последовательно выберите **Ценовая категория > Просмотреть все > F1 Free** (Бесплатный F1).
   
    Эта ценовая категория обеспечит достаточную производительность в рамках заданий этого руководства.
   
    ![Выбор бесплатной ценовой категории](./media/app-service-api-nodejs-api-app/selectfreetier.png)
9. В колонке **План службы приложений** нажмите кнопку **ОК**.
10. В колонке **Приложение API** щелкните **Создать**.

## <a name="set-up-your-new-api-app-for-git-deployment"></a>Настройка нового приложения API для развертывания Git
Вы развернете код в приложении API, отправив фиксации в репозиторий Git в службе приложений Azure. Этот раздел руководства предусматривает создание учетных данных, а также репозитория Git в Azure, которые вы будете использовать для развертывания.  

1. Создав приложение API, на домашней странице портала последовательно выберите **Службы приложений > {свое приложение API}**. 
   
    На портале отобразятся колонки **Приложение API** и **Параметры**.
   
    ![Приложение API и колонка "Параметры" на портале](media/app-service-api-nodejs-api-app/portalapiappblade.png)
2. В колонке **Параметры** прокрутите список вниз до раздела **Публикация**, а затем щелкните **Учетные данные развертывания**.
3. В колонке **Установка учетных данных развертывания** введите имя пользователя и пароль, а затем нажмите кнопку **Сохранить**.
   
    Эти учетные данные будут использоваться для публикации кода Node.js в приложении API. 
   
    ![Учетные данные развертывания](media/app-service-api-nodejs-api-app/deployment-credentials.png)
4. В колонке **Параметры** последовательно выберите **Источник развертывания > Выбор источника > Локальный репозиторий Git**, а затем нажмите кнопку **ОК**.
   
    ![Создание репозитория Git](media/app-service-api-nodejs-api-app/create-git-repo.png)
5. Когда вы создадите репозиторий GIT, вид колонки изменится для отображения активных развертываний. Так как репозиторий новый, в списке не будет активных развертываний. 
   
    ![Нет активных развертываний](media/app-service-api-nodejs-api-app/no-active-deployments.png)
6. Скопируйте URL-адрес репозитория Git. Чтобы сделать это, перейдите к колонке нового приложения API и просмотрите раздел **Основные компоненты** . В разделе **Основные компоненты** вы увидите **URL-адрес клона Git**. Если навести указатель мыши на этот URL-адрес, справа отобразится значок, который позволяет скопировать URL-адрес в буфер обмена. Щелкните этот значок, чтобы скопировать URL-адрес.
   
    ![Получите URL-адрес Git из портала](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)
   
    **Примечание**. Для выполнения следующего шага необходим URL-адрес клона Git, поэтому его нужно обязательно сохранить.

Теперь, когда у вас есть приложение API с репозиторием GIT для резервного копирования, вы можете передать код в репозиторий, чтобы развернуть код в приложении API. 

## <a name="deploy-your-api-code-to-azure"></a>Развертывание кода API в Azure
В этом разделе вы создадите локальный репозиторий Git, в котором содержится код сервера для API, а затем передадите код из этого репозитория в созданную ранее среду Azure.

1. Скопируйте папку `ContactList` в расположение, которое можно использовать для нового локального репозитория Git. Если вы выполнили шаги в первой части руководства, скопируйте `ContactList` из папки `start` или `ContactList` из папки `end`.
2. В программе командной строки перейдите в новую папку, а затем выполните следующую команду, чтобы создать локальный репозиторий. 
   
        git init
   
     ![Новый локальный репозиторий Git](media/app-service-api-nodejs-api-app/new-local-git-repo.png)
3. Если вы ознакомились с первой частью этого руководства и скопировали папку `ContactList`, скорее вы также скопировали и папку `node_modules`. Не следует включать папку `node_modules` в системе управления версиями, так как эта папка будет создана при развертывании с использованием файла `package.json` и `npm install`. Следовательно, добавьте файл `.gitignore`, выполнив в корневом каталоге проекта следующую команду.

         touch .gitignore
      
   Откройте GITIGNORE-файл и добавьте в первую строку `node_modules`. Можно подтвердить запрос на игнорирование папки `node_modules` системой управления версиями, выполнив `git status` и убедившись, что каталог не отображается в списке. Если вы хотите добавить дополнительные правила, в проекте GitHub [https://github.com/github/gitignore/blob/master/Node.gitignore] содержатся файлы, которые рекомендуется игнорировать в проекте NodeJS.
 
4. Выполните следующую команду, чтобы добавить удаленный репозиторий Git для своего репозитория приложения API. 
   
        git remote add azure YOUR_GIT_CLONE_URL_HERE
   
    **Примечание**. Замените строку YOUR_GIT_CLONE_URL_HERE URL-адресом клона Git, скопированным ранее. 
5. Выполните следующие команды, чтобы создать фиксацию, которая будет содержать весь код. 
   
        git add .
        git commit -m "initial revision"
   
    ![Фиксировать выходные данные в Git](media/app-service-api-nodejs-api-app/git-commit-output.png)
6. Выполните команду для передачи кода в Azure. При появлении соответствующего запроса введите пароль, созданный ранее на портале Azure.
   
        git push azure master
   
    Это активирует развертывание в приложение API.  
7. В браузере вернитесь к колонке **Развертывания** своего приложения API, и вы увидите, что выполняется развертывание. 
   
    ![Процесс развертывания](media/app-service-api-nodejs-api-app/deployment-happening.png)
   
    В это же время в интерфейсе командной строки будет отображаться состояние этого процесса. 
   
    ![Процесс развертывания Node.js](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)
   
    Когда развертывание будет завершено, в колонке **Развертывания** отобразится сообщение об успешном развертывании изменений кода в приложении API. 

## <a name="test-with-the-api-running-in-azure"></a>Тестирование API, работающего в Azure
1. Скопируйте **URL-адрес** в раздел **Основные компоненты** колонки вашего приложения API. 
   
    ![Развертывание завершено](media/app-service-api-nodejs-api-app/deployment-completed.png)
2. Используя клиент REST API, например Postman или Fiddler (или веб-браузер), укажите URL-адрес вызова API контактов, который должен быть конечной точкой `/contacts` приложения API. URL-адрес будет выглядеть следующим образом: `https://{your API app name}.azurewebsites.net/contacts`
   
    При отправке запроса GET в эту конечную точку вы получите выходные данные JSON приложения API.
   
    ![Postman отправляет вызовы API](media/app-service-api-nodejs-api-app/postman-hitting-api.png)
3. В браузере перейдите к конечной точке `/docs` , чтобы протестировать, как пользовательский интерфейс Swagger работает в Azure.

Теперь, когда обеспечена непрерывная доставка, в код можно внести изменения и развернуть его в Azure, отправив фиксации в репозиторий Azure Git.

## <a name="next-steps"></a>Дальнейшие действия
Теперь вы успешно создали и развернули приложение API, а в нем развернули API Node.js. Из следующего руководства вы узнаете, как [использовать приложения API в клиентах JavaScript с помощью CORS](app-service-api-cors-consume-javascript.md).




<!--HONumber=Jan17_HO3-->


