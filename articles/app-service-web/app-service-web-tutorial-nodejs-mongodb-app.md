---
title: "Разработка веб-приложения на основе Node.js и MongoDB в Azure | Документы Майкрософт"
description: "Узнайте, как разработать приложение Node.js с подключением к базе данных DocumentDB и строкой подключения MongoDB в Azure."
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
ms.date: 03/30/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 6db07ed122d8dbd9edaa3b4d25680863778a6adf
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Разработка веб-приложения на основе Node.js и MongoDB в Azure
В этом учебнике показано, как создать веб-приложение Node.js в Azure и подключить его к базе данных MongoDB. После выполнения этого учебника у вас появится приложение MEAN (MongoDB, Express, AngularJS и Node.js), которое будет запущено в [веб-приложениях службы приложений Azure](app-service-web-overview.md).

![Приложение MEAN.js, которое запущено в службе приложений Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

## <a name="before-you-begin"></a>Перед началом работы

Перед запуском этого примера необходимо скачать и установить следующие компоненты в локальной среде:

1. [Git.](https://git-scm.com/)
1. [Node.js и NPM.](https://nodejs.org/)
1. [Установка Gulp.js](http://gulpjs.com/)
1. [Скачивание, установка и запуск MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). 
1. [Скачивание и установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb-database"></a>Проверка локальной базы данных MongoDB
На этом шаге нужно убедиться, что локальная база данных MongoDB запущена.

Откройте окно терминала и `cd` в каталог установки MongoDB `bin`. 

Выполните команду `mongo` в окне терминала для подключения к локальному серверу MongoDB.

```bash
mongo
```

Если подключение успешно установлено, это означает, что локальная база данных MongoDB запущена. Если это не так, запустите локальную базу данных MongoDB, выполнив инструкции, описанные в разделе [Скачивание, установка и запуск MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/).

Закончив проверку базы данных MongoDB, введите `Ctrl`+`C` в окне терминала. 

<a name="step2"></a>

## <a name="create-local-nodejs-application"></a>Создание локального приложения Node.js
На этом шаге вы настроите локальный проект Node.js.

### <a name="clone-the-sample-application"></a>Клонирование примера приложения

Откройте окно терминала и `cd` в рабочий каталог.  

Затем выполните следующие команды, чтобы клонировать репозиторий с примером. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Этот репозиторий с примером содержит приложение [MEAN.js](http://meanjs.org/). 

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

## <a name="create-a-production-mongodb-database"></a>Создание рабочей базы данных MongoDB

На этом шаге вы создадите базу данных MongoDB в Azure. При развертывании приложения в Azure эта база данных используется в качестве рабочей нагрузки приложения.

Для MongoDB в этом руководстве используется [Azure DocumentDB](/azure/documentdb/). Azure DocumentDB поддерживает клиентские подключения MongoDB. Другими словами, приложение Node.js знает только то, что оно подключается к базе данных MongoDB. Тот факт, что подключение обеспечивается базой данных DocumentDB, является прозрачным для приложения.

### <a name="log-in-to-azure"></a>Вход в Azure

Теперь воспользуемся Azure CLI 2.0 в окне терминала, чтобы создать ресурсы, необходимые для размещения приложения Node.js в службе приложений Azure.  Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими. 

В следующем примере создается группа ресурсов в Западной Европе:

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

Чтобы увидеть доступные значения для `--location`, используйте команду `az appservice list-locations` Azure CLI.

### <a name="create-a-documentdb-account"></a>Создание учетной записи DocumentDB

Создайте учетную запись DocumentDB с помощью команды [az documentdb create](/cli/azure/documentdb#create).

В следующей команде замените `<documentdb_name>` на уникальное имя своей базы данных DocumentDB везде, где встречается этот заполнитель. Это уникальное имя будет использоваться как часть конечной точки DocumentDB (`https://<documentdb_name>.documents.azure.com/`), поэтому оно должно быть уникальным для всех учетных записей DocumentDB в Azure. 

```azurecli
az documentdb create --name <documentdb_name> --resource-group myResourceGroup --kind MongoDB
```

Параметр `--kind MongoDB` разрешает клиентские подключения MongoDB.

После создания учетной записи DocumentDB в Azure CLI отображаются следующие сведения:

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<documentdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<documentdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<documentdb_name>",
  "readLocations": [
    ...
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    ...
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Подключение приложения Node.js к базе данных

На этом шаге вы подключите пример приложения MEAN.js к только что созданной базе данных DocumentDB с использованием строки подключения MongoDB. 

### <a name="retrieve-the-database-key"></a>Получение ключа базы данных

Для подключения к базе данных DocumentDB потребуется ключ базы данных. Чтобы получить первичный ключ, выполните команду [az documentdb list-keys](/cli/azure/documentdb#list-keys).

```azurecli
az documentdb list-keys --name <documentdb_name> --resource-group myResourceGroup
```

В Azure CLI отображаются следующие сведения:

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

Скопируйте значение `primaryMasterKey` в текстовый редактор. Эти сведения потребуются на следующем шаге.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Настройка строки подключения в приложении Node.js

В репозитории MEAN.js откройте `config/env/production.js`.

В объекте `db` замените значение `uri`, как показано в следующем примере. Также замените два заполнителя `<documentdb_name>` на имя базы данных DocumentDB, а заполнитель `<primary_master_key>` — на ключ, скопированный на предыдущем шаге.

```javascript
db: {
  uri: 'mongodb://<documentdb_name>:<primary_master_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

> [!NOTE] 
> Важно указать параметр `ssl=true`, так как [для использования Azure DocumentDB требуется SSL](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
>
>

Сохраните изменения.

### <a name="test-the-application-in-production-mode"></a>Тестирование приложения в рабочем режиме 

Как и в некоторых других веб-платформах Node.js, в MEAN.js используется `gulp prod` для уменьшения размера и объединения скриптов в пакет для рабочей среды. Это средство создает файлы, необходимые для рабочей среды. 

Запустите средство `gulp prod`.

```bash
gulp prod
```

Затем выполните следующую команду, чтобы использовать строку подключения, которая была настроена в `config/env/production.js`.

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
Database:        mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Откройте браузер и перейдите по адресу `http://localhost:8443`. Щелкните **Зарегистрироваться** в верхнем меню и попробуйте создать фиктивного пользователя, как в прошлый раз. Если все получилось, это означает, что приложение записывает данные в базу данных DocumentDB в Azure. 

Чтобы остановить Node.js, введите `Ctrl`+`C` в окне терминала. 

## <a name="deploy-the-nodejs-application-to-azure"></a>Развертывание приложения Node.js в Azure
На этом шаге вы развернете приложение Node.js, подключенное к MongoDB, в службе приложений Azure.

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create). 

> [!NOTE] 
> План службы приложений представляет собой коллекцию физических ресурсов, используемых для размещения приложений. Все приложения, назначенные плану службы приложений, совместно используют ресурсы, определенные в нем. Поэтому, разместив несколько приложений, вы сможете сэкономить. 
> 
> Планы службы приложений определяют такие компоненты: 
> 
> * регион (Северная Европа, восточная часть США, Юго-Восточная Азия); 
> * размер экземпляра (небольшой, средний, крупный); 
> * число масштабируемых элементов (один, два, три экземпляра и т. д.); 
> * SKU ("Бесплатный", "Общий", "Базовый", "Стандартный", "Премиум"). 
> 

В следующем примере создается план службы приложений с именем `myAppServicePlan` и ценовой категорией **Бесплатный**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

После создания плана службы приложений в Azure CLI отображаются следующие сведения:

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 0, 
    "family": "F", 
    "name": "F1", 
    "tier": "Free" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

### <a name="create-a-web-app"></a>Создание веб-приложения

После создания плана службы приложений `myAppServicePlan` создайте веб-приложение в рамках этого плана. Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Выполните команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение. 

В следующей команде замените `<app_name>` на уникальное имя своего приложения. Это уникальное имя будет использоваться в доменном имени веб-приложения по умолчанию, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

После создания веб-приложения в Azure CLI отображаются следующие сведения: 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-an-environment-variable"></a>Настройка переменной среды

Ранее в этом учебнике вы жестко закодировали строку подключения в `config/env/production.js`. По соображениям безопасности конфиденциальные данные не следует хранить в репозитории Git. При запуске приложения в Azure строка подключения будет храниться в переменной среды.

В службе приложений переменные среды устанавливаются как _параметры приложения_ с помощью команды [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

В следующем примере настраивается параметр приложения `MONGODB_URI` в веб-приложении Azure. Обязательно замените заполнители на фактические значения, как и в прошлый раз.

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<documentdb_name>:<primary_maste_key>@<documentdb_name>.documents.azure.com:10250/mean?ssl=true"
```

В коде Node.js для доступа к этому параметру приложения, как и к любой переменной среды, используется `process.env.MONGODB_URI`. 

Теперь отмените изменения в `config/env/production.js`, выполнив следующую команду:

```bash
git checkout -- .
```

Снова откройте `config/env/production.js`. Обратите внимание, что приложение MEAN.js по умолчанию уже настроено для использования переменной среды `MONGODB_URI`, которую вы только что создали.

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
az appservice web deployment user set --user-name <specify-a-username> --password <minimum-8-char-capital-lowercase-number>
```

Чтобы настроить доступ локального репозитория Git к веб-приложению Azure, выполните команду [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git). 

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup
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
> - `.deployment` — этот файл означает, что служба приложений должна запустить `bash deploy.sh` как пользовательский скрипт развертывания.
> - `deploy.sh` — пользовательский скрипт развертывания. Если открыть этот файл, вы увидите, что он запускает `gulp prod` после `npm install` и `bower install`. 
>
> Точно так же можно добавить любые действия в развертывание на основе Git.
>
> Обратите внимание, что при перезапуске веб-приложения Azure в любой момент времени служба приложений не перезапускает эти задачи автоматизации.
>
>

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure 
Откройте развертываемое веб-приложение в веб-браузере. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Щелкните **Зарегистрироваться** в верхнем меню и попробуйте создать фиктивного пользователя. 

Если все прошло успешно и вы автоматически вошли в приложение с созданным пользователем, это означает, что приложение MEAN.js в Azure подключено к базе данных MongoDB (DocumentDB). 

![Приложение MEAN.js, которое запущено в службе приложений Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Попробуйте добавить несколько статей, выбрав **Администрирование** > **Управление статьями**. 

**Поздравляем!** Вы запустили управляемое данными приложение Node.js в службе приложений Azure.

## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге вы внесете некоторые изменения в модель данных `article` и опубликуете свои изменения в Azure.

### <a name="update-the-data-model"></a>Обновление модели данных

Откройте `modules/articles/server/models/article.server.model.js`.

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

Сначала откройте `modules/articles/server/controllers/articles.server.controller.js`.

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

Затем откройте `modules/articles/client/views/view-article.client.view.html`.

Прямо перед закрывающим тегом `</section>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Затем откройте `modules/articles/client/views/list-articles.client.view.html`.

Прямо перед закрывающим тегом `</a>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Затем откройте `modules/articles/client/views/admin/list-articles.client.view.html`.

В теге `<div class="list-group">` и прямо перед закрывающим тегом `</a>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Наконец, откройте `modules/articles/client/views/admin/form-article.client.view.html`.

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
> Помните, что ваши изменения для `config/env/production.js` были отменены, и переменная среды `MONGODB_URI` устанавливается только в веб-приложении, а не на локальном компьютере. Если взглянуть на файл конфигурации, вы увидите, что по умолчанию в рабочей конфигурации используется локальная база данных MongoDB. Это гарантирует, что при локальном тестировании изменений кода рабочие данные не будут затронуты.
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
> Если вы добавляли статьи ранее, то вы увидите, что они по-прежнему отображаются. Существующие данные в DocumentDB не теряются. Кроме того, изменения в схеме данных не влияют на существующие данные.
>
>

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики 

При запуске приложения Node.js в службе приложений Azure можно направить журналы консоли прямо в свой терминал. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

Чтобы настроить потоки для журналов, выполните команду [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli 
az appservice web log tail --name <app_name> --resource-group myResourceGroup 
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

<!--

## Step 4 - Download server logs
In this step, you turn on monitoring of your web app with web server logs, and then download these logs. 

### Enable logging
Enable all logging options for your web app.

```azurecli
az appservice web log config --name <app_name> --resource-group myResourceGroup --application-logging true --detailed-error-messages true --failed-request-tracing true --web-server-logging filesystem
```

### Generate errors

To generate some error entries, navigate to a nonexistent page in your web app. For example: `http://<app_name>.azurewebsites.net/404`. 

### Download log files
Download the log files for review.

```azurecli
az appservice web log download --name <app_name> --resource-group myResourceGroup
```

## Step 5 - Scale to another region
In this step, you scale your Node.js app to serve your customers in a new region. That way, you can tailor your web app to customers in different regions, and also put your web app closer to them to improve performance. When you're done with this step, you will have a [Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/) profile with two endpoints, which route traffic to two web apps which reside in different geographical regions.

1. Create a Traffic Manager profile with a unique name and add it to your resource group.

    ```azurecli
    az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name <unique-dns-name>
    ```

    > [!NOTE]
    > `--routing-method Performance` specifies that this profile [routes user traffic to the closest endpoint](../traffic-manager/traffic-manager-routing-methods.md).

2. Get the resource ID of your existing Node.js web app.

    ```azurecli
    az appservice web show --name <app_name> --resource-group myResourceGroup --query id --output tsv
    ```

3. Add an endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-1-resource-id>`:

    ```azurecli
    az network traffic-manager endpoint create --name <app_name>-westeurope --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id <web-app-1-resource-id>
    ```

4. Your Traffic Manager profile now has an endpoint that points to your web app. Query for its URL to try it out.

    ```azurecli
    az network traffic-manager profile show --name cephalin-express --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
    ```

    Copy the output into your browser. You should get the default Express page again, with data from your database.

5. Let's add some identifying characteristic to your West Europe app. Add an environment variable.

    ```azurecli
    az appservice web config appsettings update --settings region="Europe" --name <app_name> --resource-group myResourceGroup    
    ```

6. Open `routes/index.js` and change the `router.get()` to use the environment variable.

    ```javascript
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express ' + process.env.region, data: output });
    });
    ```

7. Save your changes and push them to Azure.

    ```
    git add .
    git commit -m "added region string."
    git push azure master
    ```

8. Refresh your browser on your Traffic Manager profile's URL. You should now see `Express Europe` in the homepage. 

    Since your Traffic Manager profile only has one endpoint which points to your West Europe web app, this is the only page you'll see. Next, you create a new web app in Southeast Asia and add a new endpoint to the profile.

4. Create an App Service plan and web app in the Southeast Asia region, and deploy the same code to it just like you did in [Step 2]<#step2>.

    ```azurecli
    az appservice plan create --name my-expressjs-appservice-plan-asia --resource-group myResourceGroup --location "Southeast Asia" --sku FREE
    az appservice web create --name <app_name>-asia --plan my-expressjs-appservice-plan-asia --resource-group myResourceGroup
    url=$(az appservice web source-control config-local-git --name <app_name>-asia --resource-group myResourceGroup --query url --output tsv)
    git remote add azureasia $url
    git push azureasia master
    ```

5. Add the same application settings to the new web app. Set the region to `"Asia"`.

    ```azurecli
    az appservice web config appsettings update --settings dbconnstring="mongodb://$accountname:$password@$accountname.documents.azure.com:10250/tutorial?ssl=true&sslverifycertificate=false" --name <app_name>-asia --resource-group myResourceGroup    
    az appservice web config appsettings update --settings region="Asia" --name <app_name>-asia --resource-group myResourceGroup    
    ```

    Since DocumentDB is a [geographically distributed](../documentdb/documentdb-distribute-data-globally.md) NoSQL service, you can use the same MongoDB connection string in the Southeast Asia web app. When the MongoDB client driver connects to your DocumentDB account, Azure automatically figures out where is the closest place to route the connection. No code change is necessary. You only need to add the regions you want to support to your DocumentDB account, which you will do next.

6. Add `Southeast Asia` as a region to your DocumentDB account.

    ```azurecli
    az documentdb update --locations "West Europe"=0 "Southeast Asia"=1 --name $accountname --resource-group myResourceGroup
    ```

3. To finish, add a second endpoint to the Traffic Manager profile and put the output of the last command in `<web-app-2-resource-id>`:

    ```azurecli
    resourceid=$(az appservice web show --name <app_name>-asia --resource-group myResourceGroup --query id --output tsv)
    az network traffic-manager endpoint create -n <app_name>-southeastasia --profile-name myTrafficManagerProfile -g myResourceGroup --type azureEndpoints --target-resource-id resourceid
    ```
  
Now, try to access the URL of your Traffic Manager profile. If you access the URL from the Europe region, you should see "Express Europe", but from the Asia region, you should see "Express Asia".

-->
## <a name="more-resources"></a>Дополнительные ресурсы

