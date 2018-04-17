---
title: Разработка веб-приложения на основе Node.js и MongoDB в Azure | Документы Майкрософт
description: Узнайте, как разработать приложение Node.js с подключением к базе данных Cosmos DB и строкой подключения MongoDB в Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8fdad8d8e62365c33b47e67b483c929aaab0083e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-build-a-nodejs-and-mongodb-web-app-in-azure"></a>Руководство. Разработка веб-приложения на основе Node.js и MongoDB в Azure

> [!NOTE]
> В этой статье мы развернем приложение в службе приложений на платформе Windows. Чтобы развернуть приложение в службе приложений на платформе _Linux_, см. в руководство по [созданию веб-приложений Node.js с MongoDB в службе приложений Azure в Linux](./containers/tutorial-nodejs-mongodb-app.md).
>

Веб-приложения Azure — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом руководстве показано, как создать веб-приложение Node.js в Azure и подключить его к базе данных MongoDB. После выполнения действий, описанных в этом руководстве, у вас появится приложение MEAN (MongoDB, Express, AngularJS и Node.js), выполняющееся в [службе приложений Azure](app-service-web-overview.md). Для простоты в примере приложения используется [веб-платформа MEAN.js](http://meanjs.org/).

![Приложение MEAN.js, которое запущено в службе приложений Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание базы данных MongoDB в Azure.
> * Подключение приложения Node.js к базе данных MongoDB.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

1. [установите Git](https://git-scm.com/);
1. [установите Node.j и NPM](https://nodejs.org/).
1. [установите Bower](https://bower.io/) (требуется для [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started));
1. [Gulp.js](http://gulpjs.com/) (требуется для [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started));
1. [Установите и запустите MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/). 

## <a name="test-local-mongodb"></a>Проверка локальной базы данных MongoDB

Откройте окно терминала и `cd` в каталог установки MongoDB `bin`. Используйте это окно терминала для выполнения всех команд в рамках этого руководства.

Выполните команду `mongo` в окне терминала для подключения к локальному серверу MongoDB.

```bash
mongo
```

Если подключение успешно установлено, это означает, что локальная база данных MongoDB запущена. Если это не так, запустите локальную базу данных MongoDB, выполнив инструкции, описанные в статье [Install MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (Установка MongoDB Community Edition). Часто база данных MongoDB установлена, но ее необходимо запустить, выполнив команду `mongod`. 

Закончив проверку базы данных MongoDB, введите команду `Ctrl+C` в окне терминала. 

## <a name="create-local-nodejs-app"></a>Создание локального приложения Node.js

На этом шаге вы настроите локальный проект Node.js.

### <a name="clone-the-sample-application"></a>Клонирование примера приложения

Откройте окно терминала и c помощью команды `cd` перейдите в рабочий каталог.  

Выполните команду ниже, чтобы клонировать репозиторий с примером. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Этот пример репозитория содержит копию [репозитория MEAN.js](https://github.com/meanjs/mean). В него внесены изменения для запуска службы приложений. Дополнительные сведения см. в [файле сведений](https://github.com/Azure-Samples/meanjs/blob/master/README.md) в репозитории MEAN.js.

### <a name="run-the-application"></a>Выполнение приложения

Выполните команды ниже, чтобы установить необходимые пакеты и запустить приложение.

```bash
cd meanjs
npm install
npm start
```

После полной загрузки приложения вы увидите следующее сообщение:

```console
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Откройте браузер и перейдите по адресу `http://localhost:3000`. В верхнем меню щелкните **Зарегистрироваться** и создайте тестового пользователя. 

В примере приложения MEAN.js данные пользователя хранятся в базе данных. Если вы создали пользователя и вошли в приложение, это означает, что приложение записывает данные в локальную базу данных MongoDB.

![MEAN.js успешно подключается к базе данных MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Чтобы добавить несколько статей, выберите **Администрирование > Manage Articles** (Управление статьями).

Чтобы остановить Node.js в любое время, введите `Ctrl+C` в окне терминала. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Создание рабочей базы данных MongoDB

На этом шаге вы создадите базу данных MongoDB в Azure. При развертывании приложения в Azure используется эта облачная база данных.

В качестве MongoDB в этом руководстве используется [Azure Cosmos DB](/azure/documentdb/). Cosmos DB поддерживает подключения клиентов MongoDB.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Создание учетной записи Cosmos DB

В Cloud Shell создайте учетную запись Cosmos DB при помощи команды [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_create).

В следующей команде замените заполнитель *\<cosmosdb_name>* уникальным именем базы данных Cosmos DB. Это имя используется как часть конечной точки Cosmos DB (`https://<cosmosdb_name>.documents.azure.com/`), поэтому оно должно быть уникальным для всех учетных записей Cosmos DB в Azure. В нем могут использоваться только строчные буквы, цифры и дефис (-). Его длина должна быть от 3 до 50 знаков.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

Параметр *--kind MongoDB* разрешает клиентские подключения MongoDB.

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
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Подключение приложения к рабочей базе данных MongoDB

На этом шаге вы подключите пример приложения MEAN.js к только что созданной базе данных Cosmos DB с помощью строки подключения MongoDB. 

### <a name="retrieve-the-database-key"></a>Получение ключа базы данных

Для подключения к базе данных Cosmos DB потребуется ключ базы данных. Чтобы получить первичный ключ, выполните в Cloud Shell команду [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_list_keys).

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
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

Скопируйте значение `primaryMasterKey`. Эти сведения потребуются на следующем шаге.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Настройка строки подключения в приложении Node.js

В локальном репозитории MEAN.js создайте файл с именем _local-production.js_ в папке _config/env/_. По умолчанию, чтобы этот файл хранился вне репозитория, настраивается _.gitignore_. 

Скопируйте в него следующий код: Замените два заполнителя *\<cosmosdb_name>* именем базы данных Cosmos DB, а заполнитель *\<primary_master_key>* — ключом, скопированным на предыдущем шаге.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

Параметр `ssl=true` обязателен, так как для [Cosmos DB требуется протокол SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Сохраните изменения.

### <a name="test-the-application-in-production-mode"></a>Тестирование приложения в рабочем режиме 

Выполните команду ниже, чтобы уменьшить размер скриптов и объединить их в пакет для рабочей среды. При этом создаются файлы, необходимые для рабочей среды.

```bash
gulp prod
```

Выполните следующую команду, чтобы использовать строку подключения, которая была настроена в _config/env/local-production.js_.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` устанавливает переменную среды, указывающую, что Node.js необходимо запустить в рабочей среде.  `node server.js` запускает сервер Node.js с `server.js` в корневой папке репозитория. Так приложение Node.js загружается в Azure. 

Когда приложение будет загружено, убедитесь, что оно запущено в рабочей среде:

```console
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Откройте браузер и перейдите по адресу `http://localhost:8443`. В верхнем меню щелкните **Зарегистрироваться** и создайте тестового пользователя. Если вы создали пользователя и вошли в приложение, это означает, что приложение записывает данные в локальную базу данных Cosmos DB в Azure. 

Чтобы остановить Node.js, введите `Ctrl+C` в окне терминала. 

## <a name="deploy-app-to-azure"></a>Развертывание приложения в Azure

На этом шаге вы развернете приложение Node.js, подключенное к MongoDB, в службе приложений Azure.

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Настройка переменной среды

По умолчанию _config/env/local-production.js_ хранится в проекте MEAN.js вне репозитория Git. Поэтому для веб-приложения Azure вам нужно использовать параметры приложения, чтобы определить строку подключения MongoDB.

Чтобы задать параметры приложения, выполните команду [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) в Cloud Shell. 

В следующем примере настраивается параметр приложения `MONGODB_URI` в веб-приложении Azure. Замените заполнители *\<app_name>*, *\<cosmosdb_name>* и *\<primary_master_key>*.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

В коде Node.js для доступа к этому параметру приложения, как и к любой переменной среды, используется `process.env.MONGODB_URI`. 

В локальном репозитории MEAN.js откройте файл _config/env/production.js_ (не _config/env/local-production.js_), который содержит конфигурацию для конкретной рабочей среды. Приложение MEAN.js по умолчанию уже настроено для использования созданной переменной среды `MONGODB_URI`.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

Вы можете заметить, что в ходе развертывания после `npm install` запускается [Gulp](http://gulpjs.com/). Служба приложений не запускает задачи Gulp или Grunt во время развертывания, поэтому для запуска скрипта в этом примере в корневом каталоге репозитория расположены два дополнительных файла: 

- _.deployment_ — этот файл указывает службе приложений запустить `bash deploy.sh` как пользовательский сценарий развертывания.
- _deploy.sh_ — пользовательский сценарий развертывания. Если открыть этот файл, вы увидите, что он запускает `gulp prod` после `npm install` и `bower install`. 

Точно так же можно добавить любые действия в развертывание на основе Git. При перезапуске веб-приложения Azure в любой момент времени служба приложений не перезапускает эти задачи автоматизации.

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure 

Откройте развертываемое веб-приложение в веб-браузере. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Щелкните **Зарегистрироваться** в верхнем меню и создайте фиктивного пользователя. 

Если все прошло успешно и вы автоматически вошли в приложение с созданным пользователем, это означает, что приложение MEAN.js в Azure подключено к базе данных MongoDB (Cosmos DB). 

![Приложение MEAN.js, которое запущено в службе приложений Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Чтобы добавить несколько статей, выберите **Администрирование > Manage Articles** (Управление статьями). 

**Поздравляем!** Вы запустили управляемое данными приложение Node.js в службе приложений Azure.

## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге вы внесете изменения в модель данных `article` и опубликуете свои изменения в Azure.

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

### <a name="update-the-articles-code"></a>Обновление кода статей

Обновите остальную часть кода `articles`, чтобы в ней использовался `comment`.

Вам потребуется изменить пять файлов: файл сервера контроллера и четыре файла представлений клиентов. 

Откройте файл _modules/articles/server/controllers/articles.server.controller.js_.

Добавьте в функцию `update` присваивание для `article.comment`. В следующем коде приведена готовая функция `update`:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Откройте файл _modules/articles/client/views/view-article.client.view.html_.

Прямо перед закрывающим тегом `</section>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Откройте файл _modules/articles/client/views/list-articles.client.view.html_.

Прямо перед закрывающим тегом `</a>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Откройте файл _modules/articles/client/views/admin/list-articles.client.view.html_.

В элементе `<div class="list-group">` и прямо перед закрывающим тегом `</a>` добавьте следующую строку для отображения `comment` вместе с остальными данными статьи:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Откройте файл _modules/articles/client/views/admin/form-article.client.view.html_.

Найдите элемент `<div class="form-group">`, который содержит кнопку "Отправить" и выглядит следующим образом:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Прямо перед этим тегом добавьте еще один элемент `<div class="form-group">`, который позволяет пользователям изменять поле `comment`. Новый элемент должен выглядеть следующим образом:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

Сохраните изменения.

В окне терминала на локальном компьютере снова проверьте изменения в рабочем режиме.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Откройте в браузере адрес `http://localhost:8443` и убедитесь, что вход выполнен.

Выберите **Администрирование > Управление статьями** и добавьте статью, нажав кнопку **+**.

Должно появиться новое текстовое поле `Comment`.

![В статьи добавлено поле комментария](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

Чтобы остановить Node.js, введите `Ctrl+C` в окне терминала. 

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала на локальном компьютере зафиксируйте изменения в Git, а затем отправьте изменение кода в Azure.

```bash
git commit -am "added article comment"
git push azure master
```

После окончания `git push` перейдите в веб-приложение Azure и проверьте новые функции.

![Изменения модели и базы данных, опубликованные в Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Если вы добавляли статьи ранее, то вы увидите, что они по-прежнему отображаются. Существующие данные в Cosmos DB не теряются. Кроме того, изменения в схеме данных не влияют на существующие данные.

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики 

При запуске приложения Node.js в службе приложений Azure можно направить журналы консоли в свой терминал. Таким образом, вы будете получать те же диагностические сообщения, которые помогут устранить ошибки приложения.

Чтобы настроить потоки для журналов, выполните команду [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) в Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

После настройки потоков обновите веб-приложение Azure в браузере, чтобы получить немного трафика. Вы должны увидеть, что журналы консоли теперь направляются в терминал.

Чтобы в любое время отменить потоковую передачу журналов, введите `Ctrl+C`. 

## <a name="manage-your-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное веб-приложение.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

По умолчанию на портале отображается страница **Обзор** веб-приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Дополнительная информация

Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание базы данных MongoDB в Azure.
> * Подключение приложения Node.js к базе данных MongoDB.
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов из Azure в окно терминала.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"] 
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](app-service-web-tutorial-custom-domain.md)
