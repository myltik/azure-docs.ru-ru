---
title: "Разработка веб-приложения на основе Node.js и MongoDB в Azure | Документы Майкрософт"
description: "Узнайте, как разработать приложение Node.js с подключением к базе данных Cosmos DB и строкой подключения MongoDB в Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 2a3d63b3829e750b62658d720522ae1abf89cd86
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Разработка веб-приложения на основе Node.js и MongoDB в Azure
В этом учебнике показано, как создать веб-приложение Node.js в Azure и подключить его к базе данных MongoDB. После выполнения этого учебника у вас появится приложение MEAN (MongoDB, Express, AngularJS и Node.js), которое будет запущено в [веб-приложениях службы приложений Azure](app-service-web-overview.md). Для простоты в примере приложения используется [веб-платформа MEAN.js](http://meanjs.org/).

![Приложение MEAN.js, которое запущено в службе приложений Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MongoDB в Azure.
> * Подключение приложения Node.js к базе данных MongoDB.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

## <a name="prerequisites"></a>Предварительные требования

Перед запуском этого примера необходимо скачать и установить следующие компоненты в локальной среде:

1. [Git.](https://git-scm.com/)
1. [Node.js и NPM.](https://nodejs.org/)
1. [Gulp.js](http://gulpjs.com/) (требуется для [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started));
1. [MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (этот компонент потребуется запустить); 
1. [Скачивание и установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>Проверка локальной базы данных MongoDB
На этом шаге нужно убедиться, что локальная база данных MongoDB запущена.

Откройте окно терминала и `cd` в каталог установки MongoDB `bin`. 

Выполните команду `mongo` в окне терминала для подключения к локальному серверу MongoDB.

```bash
mongo
```

Если подключение успешно установлено, это означает, что локальная база данных MongoDB запущена. Если это не так, запустите локальную базу данных MongoDB, выполнив инструкции, описанные в разделе [Скачивание, установка и запуск MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). Часто база данных MongoDB установлена, но ее необходимо запустить, выполнив команду `mongod`. 

Закончив проверку базы данных MongoDB, введите `Ctrl`+`C` в окне терминала. 

<a name="step2"></a>

## <a name="create-local-nodejs-app"></a>Создание локального приложения Node.js
На этом шаге вы настроите локальный проект Node.js.

### <a name="clone-the-sample-application"></a>Клонирование примера приложения

Откройте окно терминала и `cd` в рабочий каталог.  

Затем выполните следующие команды, чтобы клонировать репозиторий с примером. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Этот пример репозитория содержит копию [репозитория MEAN.js](https://github.com/meanjs/mean). В него внесены минимальные изменения для запуска службы приложений (дополнительные сведения см. в [файле сведений](https://github.com/Azure-Samples/meanjs/blob/master/README.md)).

### <a name="run-the-application"></a>Выполнение приложения

Установите необходимые пакеты и запустите приложение.

```bash
cd meanjs
npm install
npm start
```

После полной загрузки приложения вы увидите следующее сообщение:

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Откройте браузер и перейдите по адресу `http://localhost:3000`. Щелкните **Зарегистрироваться** в верхнем меню и попробуйте создать фиктивного пользователя. 

В примере приложения MEAN.js данные пользователя хранятся в базе данных. Если все получилось и вы вошли в приложение MEAN.js под созданным пользователем, это означает, что приложение записывает данные в локальную базу данных MongoDB.

![MEAN.js успешно подключается к базе данных MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Попробуйте добавить несколько статей, выбрав **Администрирование** > **Управление статьями**.

Чтобы остановить Node.js в любое время, введите `Ctrl`+`C` в окне терминала. 

## <a name="create-production-mongodb"></a>Создание рабочей базы данных MongoDB

На этом шаге вы создадите базу данных MongoDB в Azure. При развертывании приложения в Azure эта база данных используется в качестве рабочей нагрузки приложения.

В качестве MongoDB в этом руководстве используется [Azure Cosmos DB](/azure/documentdb/). Cosmos DB поддерживает подключения клиентов MongoDB.

### <a name="log-in-to-azure"></a>Вход в Azure

Теперь воспользуемся Azure CLI 2.0 в окне терминала, чтобы создать ресурсы, необходимые для размещения приложения Node.js в службе приложений Azure.  Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими. 

В следующем примере создается группа ресурсов в регионе "Западная Европа".

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

Чтобы увидеть доступные значения для `--location`, используйте команду `az appservice list-locations` Azure CLI.

### <a name="create-a-cosmos-db-account"></a>Создание учетной записи Cosmos DB

Создайте учетную запись Cosmos DB командой [az cosmosdb create](/cli/azure/cosmosdb#create).

В следующей команде замените _&lt;cosmosdb_name>_ уникальным именем своей базы данных Cosmos DB везде, где встречается этот заполнитель. Это уникальное имя используется как часть конечной точки Cosmos DB (`https://<cosmosdb_name>.documents.azure.com/`), поэтому оно должно быть уникальным для всех учетных записей Cosmos DB в Azure. 

```azurecli
az cosmosdb create \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup \
    --kind MongoDB
```

Параметр `--kind MongoDB` разрешает клиентские подключения MongoDB.

> [!NOTE]
> В имени _&lt;cosmosdb_name>_ могут использоваться только строчные буквы, цифры и знак _-_. Его длина должна быть от 3 до 50 знаков.
>
>

После создания учетной записи Cosmos DB в Azure CLI отображаются следующие сведения.

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output has been truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Подключение приложения к рабочей базе данных MongoDB

На этом шаге вы подключите пример приложения MEAN.js к только что созданной базе данных Cosmos DB с помощью строки подключения MongoDB. 

### <a name="retrieve-the-database-key"></a>Получение ключа базы данных

Для подключения к базе данных Cosmos DB потребуется ключ базы данных. Чтобы получить первичный ключ, выполните команду [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys).

```azurecli
az cosmosdb list-keys \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup
```

В Azure CLI отображаются следующие сведения:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Скопируйте значение `primaryMasterKey` в текстовый редактор. Эти сведения потребуются на следующем шаге.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Настройка строки подключения в приложении Node.js

В репозитории MEAN.js откройте файл _config/env/production.js_.

В объекте `db` замените значение `uri`, как показано в следующем примере. Также замените два заполнителя _&lt;cosmosdb_name>_ именем базы данных Cosmos DB, а заполнитель _&lt;primary_master_key>_ — ключом, скопированным на предыдущем шаге.

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> Параметр `ssl=true` важен, так как для [Cosmos DB требуется протокол SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 
>
>

Сохраните изменения.

### <a name="test-the-application-in-production-mode"></a>Тестирование приложения в рабочем режиме 

Как и в некоторых других веб-платформах Node.js, в MEAN.js используется `gulp` для уменьшения размера и объединения скриптов в пакет для рабочей среды. Это средство создает файлы, необходимые для рабочей среды. 

Запустите средство `gulp prod`.

```bash
gulp prod
```

Затем выполните следующую команду, чтобы использовать строку подключения, которая была настроена в _config/env/production.js_.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` устанавливает переменную среды, которая указывает, что Node.js необходимо запустить в рабочей среде. `node server.js` запускает сервер Node.js с `server.js` в корневой папке репозитория. Так приложение Node.js загружается в Azure. 

Когда приложение будет загружено, убедитесь, что оно запущено в рабочей среде:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Откройте браузер и перейдите по адресу `http://localhost:8443`. Щелкните **Зарегистрироваться** в верхнем меню и попробуйте создать фиктивного пользователя, как в прошлый раз. Если все получилось, это означает, что приложение записывает данные в базу данных Cosmos DB в Azure. 

Чтобы остановить Node.js, введите `Ctrl`+`C` в окне терминала. 

## <a name="deploy-app-to-azure"></a>Развертывание приложения в Azure
На этом шаге вы развернете приложение Node.js, подключенное к MongoDB, в службе приложений Azure.

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

В следующем примере создается план службы приложений _myAppServicePlan_ в ценовой категории **Бесплатный**.

```azurecli
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

После создания плана службы приложений в Azure CLI отображаются следующие сведения:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>Создание веб-приложения

После создания плана службы приложений _myAppServicePlan_ создайте в нем веб-приложение. Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Выполните команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение. 

В следующей команде замените заполнитель _&lt;app_name>_ уникальным именем своего приложения. Это уникальное имя используется в доменном имени по умолчанию для веб-приложения, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям. 

```azurecli
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

После создания веб-приложения в Azure CLI отображаются следующие сведения: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-an-environment-variable"></a>Настройка переменной среды

Ранее в этом руководстве вы жестко запрограммировали строку подключения в файле _config/env/production.js_. По соображениям безопасности конфиденциальные данные не следует хранить в репозитории Git. При запуске приложения в Azure строка подключения будет храниться в переменной среды.

В службе приложений переменные среды устанавливаются как _параметры приложения_ с помощью команды [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

В следующем примере настраивается параметр приложения `MONGODB_URI` в веб-приложении Azure. Обязательно замените заполнители на фактические значения, как и в прошлый раз.

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

В коде Node.js для доступа к этому параметру приложения, как и к любой переменной среды, используется `process.env.MONGODB_URI`. 

Теперь отмените изменения в файле _config/env/production.js_, выполнив следующую команду.

```bash
git checkout -- .
```

Повторно откройте файл _config/env/production.js_. Обратите внимание, что приложение MEAN.js по умолчанию уже настроено для использования переменной среды `MONGODB_URI`, которую вы только что создали.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>Настройка локального развертывания Git 

Для развертывания веб-приложения в службе приложений Azure можно использовать FTP, локальный репозиторий Git, GitHub, Visual Studio Team Services и BitBucket. Для использования FTP и локального репозитория Git на сервере необходимо настроить пользователя развертывания, который потребуется, чтобы выполнить проверку подлинности развертывания. 

Для их создания используйте команду [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set). 

> [!NOTE] 
> Пользователь развертывания необходим для развертывания FTP и локального репозитория Git в службе приложений. Это пользователь уровня учетной записи. Поэтому этот пользователь развертывания отличается от учетной записи подписки Azure. Пользователя развертывания нужно настроить только один раз.

```azurecli
az appservice web deployment user set \
    --user-name <specify-a-username> \
    --password <minimum-8-char-capital-lowercase-number>
```

Чтобы настроить доступ локального репозитория Git к веб-приложению Azure, выполните команду [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git). 

```azurecli
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

После настройки пользователя развертывания в Azure CLI будет показан URL-адрес развертывания для веб-приложения Azure в следующем формате:

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Скопируйте выходные данные из терминала, так как они понадобятся на следующем этапе. 

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

Добавьте удаленную службу приложений Azure в локальный репозиторий Git. 

```bash
git remote add azure <paste_copied_url_here> 
```

Чтобы развернуть приложение Node.js, опубликуйте его в удаленную службу приложений Azure. После этого введите пароль, указанный ранее в процессе создания пользователя развертывания. 

```bash
git push azure master
```

Во время развертывания служба приложений Azure будет взаимодействовать с Git.

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

> [!NOTE]
> Вы можете заметить, что в ходе развертывания после `npm install` запускается [Gulp](http://gulpjs.com/). Служба приложений не запускает задачи Gulp или Grunt во время развертывания, поэтому для запуска скрипта в этом примере в корневом каталоге репозитория расположены два дополнительных файла: 
>
> - _.deployment_ — этот файл указывает службе приложений запустить `bash deploy.sh` как пользовательский сценарий развертывания.
> - _deploy.sh_ — пользовательский сценарий развертывания. Если открыть этот файл, вы увидите, что он запускает `gulp prod` после `npm install` и `bower install`. 
>
> Точно так же можно добавить любые действия в развертывание на основе Git.
>
> При перезапуске веб-приложения Azure в любой момент времени служба приложений не перезапускает эти задачи автоматизации.
>
>

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure 
Откройте развертываемое веб-приложение в веб-браузере. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Щелкните **Зарегистрироваться** в верхнем меню и попробуйте создать фиктивного пользователя. 

Если все прошло успешно и вы автоматически вошли в приложение с созданным пользователем, это означает, что приложение MEAN.js в Azure подключено к базе данных MongoDB (Cosmos DB). 

![Приложение MEAN.js, которое запущено в службе приложений Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Попробуйте добавить несколько статей, выбрав **Администрирование** > **Управление статьями**. 

**Поздравляем!** Вы запустили управляемое данными приложение Node.js в службе приложений Azure.

## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге вы внесете некоторые изменения в модель данных `article` и опубликуете свои изменения в Azure.

### <a name="update-the-data-model"></a>Обновление модели данных

Откройте файл _modules/articles/server/models/article.server.model.js_.

В `ArticleSchema` добавьте тип `String` с именем `comment`. Когда все будет готово, код схемы должен выглядеть следующим образом:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

На этом изменения модели закончены. 

### <a name="update-the-articles-code"></a>Обновление кода статей

Затем обновите остальную часть кода `articles`, чтобы в ней использовался `comment`.

В общей сложности потребуется изменить пять файлов: файл сервера контроллера и четыре файла представлений клиентов. 

Сначала откройте файл _modules/articles/server/controllers/articles.server.controller.js_.

Добавьте в функцию `update` присваивание для `article.comment`. Когда все будет готово, функция `update` должна выглядеть следующим образом:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Затем откройте файл _modules/articles/client/views/view-article.client.view.html_.

Прямо перед закрывающим тегом `</section>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Теперь откройте файл _modules/articles/client/views/list-articles.client.view.html_.

Прямо перед закрывающим тегом `</a>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Откройте файл _modules/articles/client/views/admin/list-articles.client.view.html_.

В теге `<div class="list-group">` и прямо перед закрывающим тегом `</a>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Наконец, откройте файл _modules/articles/client/views/admin/form-article.client.view.html_.

Найдите тег `<div class="form-group">`, который содержит кнопку "Отправить" и выглядит следующим образом:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Прямо перед этим тегом добавьте еще один тег `<div class="form-group">`, который позволяет пользователям изменять поле `comment`. Новый тег должен выглядеть следующим образом:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

Сохраните изменения.

Проверьте изменения в рабочем режиме.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Помните, что ваши изменения в _config/env/production.js_ были отменены, и переменная среды `MONGODB_URI` задается только в веб-приложении, а не на локальном компьютере. Если взглянуть на файл конфигурации, вы увидите, что по умолчанию в рабочей конфигурации используется локальная база данных MongoDB. Это гарантирует, что при локальном тестировании изменений кода рабочие данные не будут затронуты.
>
>

Откройте в браузере адрес `http://localhost:8443` и убедитесь, что вход выполнен.

Щелкните **Администрирование** > **Управление статьями** и добавьте статью, нажав кнопку **+**.

Должно появиться новое текстовое поле `Comment`.

![В статьи добавлено поле комментария](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

Чтобы остановить Node.js, введите `Ctrl`+`C` в окне терминала. 

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

Зафиксируйте изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git commit -am "added article comment"
git push azure master
```

После окончания `git push` перейдите в веб-приложение Azure и еще раз проверьте новые функции.

![Изменения модели и базы данных, опубликованные в Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

> [!NOTE]
> Если вы добавляли статьи ранее, то вы увидите, что они по-прежнему отображаются. Существующие данные в Cosmos DB не теряются. Кроме того, изменения в схеме данных не влияют на существующие данные.
>
>

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики 

При запуске приложения Node.js в службе приложений Azure можно направить журналы консоли прямо в свой терминал. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

Чтобы настроить потоки для журналов, выполните команду [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

После настройки потоков обновите веб-приложение Azure в браузере, чтобы получить немного трафика. Вы должны увидеть, что журналы консоли теперь направляются в терминал.

Чтобы в любое время отменить потоковую передачу журналов, введите `Ctrl`+`C`. 

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейти на портал Azure, чтобы увидеть созданное веб-приложение.

Для этого войдите на портал [https://portal.azure.com](https://portal.azure.com).

В меню слева выберите **Служба приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Вы попадете в _колонку_ веб-приложения (страница портала, открывшаяся горизонтально).

По умолчанию колонка веб-приложения отображает страницу **обзора**. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части колонки отображаются различные страницы конфигурации, которые можно открыть.

![Колонка службы приложений на портале Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

На вкладках в колонке содержится множество полезных функций, которые вы можете добавить в веб-приложение. Ниже представлены лишь некоторые из возможностей:

* сопоставление настраиваемого DNS-имени;
* привязка настраиваемого SSL-сертификата;
* настройка непрерывного развертывания;
* вертикальное и горизонтальное масштабирование;
* добавление аутентификации пользователей.

## <a name="clean-up-resources"></a>Очистка ресурсов
 
Если эти ресурсы не требуются для изучения другого руководства (см. раздел [Дальнейшие действия](#next)), их можно удалить, выполнив следующие команды: 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MongoDB в Azure.
> * Подключение приложения Node.js к базе данных MongoDB.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов из Azure в окно терминала.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена.

> [!div class="nextstepaction"] 
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md)

