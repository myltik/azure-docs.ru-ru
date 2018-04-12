---
title: 'Хранилище таблиц Azure: создание веб-приложения Node.js | Документация Майкрософт'
description: В этом коротком уроке вы научитесь создавать веб-приложение посредством добавления служб хранилища Azure и модуля Azure.
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2018
ms.author: mimig
ms.openlocfilehash: 3708c4a1bae93682f81d8aad0f3649f6b2381ff5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-table-storage-nodejs-web-application"></a>Хранилище таблиц Azure: веб-приложение Node.js
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Обзор
В этом руководстве возможности приложения, создание которого описано в статье [Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure], расширяются с помощью клиентских библиотек Microsoft Azure для Node.js, позволяя работать со службами управления данными. Возможности приложения расширяются за счет создания веб-приложения списка задач, которое можно развернуть в Azure. Список задач позволяет пользователю извлекать задачи, добавлять новые задачи и помечать задачи как завершенные.

Элементы задач хранятся в службе хранилища Azure или Azure Cosmos DB. Служба хранилища Azure и Azure Cosmos DB обеспечивают хранение неструктурированных данных с функциями отказоустойчивости и высокой доступности. Служба хранилища Azure и Azure Cosmos DB включают в себя несколько структур данных, где можно хранить данные и получать к ним доступ. Службы хранилища и Azure Cosmos DB можно использовать из интерфейсов API, включенных в пакет Azure SDK для Node.js, или через интерфейсы REST API. Дополнительные сведения см. в статье [Хранилище Azure].

Прежде чем приступать к работе с этим руководством, необходимо ознакомиться со статьями [Построение и развертывание приложения Node.js в облачной службе Azure], [Node.js с Express] и [Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure].

В нем содержится следующая информация:

* Как работать с подсистемой шаблонов Jade
* Как работать со службами управления данными Azure

На снимке экрана ниже показано готовое приложение:

![Готовая веб-страница в Internet Explorer](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Настройка учетных данных хранилища в файле Web.Config
Для доступа к службе хранилища Azure или Azure Cosmos DB необходимо передать учетные данные хранилища. Для этого используются параметры приложения web.config.
Параметры web.config передаются в качестве переменных среды в Node, которые затем считываются пакетом SDK для Azure.

> [!NOTE]
> Учетные данные хранилища используются только при развертывании приложения в Azure. При запуске в эмуляторе приложение использует эмулятор хранения.
>
>

Выполните следующие действия, чтобы получить учетные данные учетной записи хранения и добавить их в параметры web.config:

1. Если это еще не сделано, запустите Azure PowerShell из меню **Пуск**. Для этого разверните пункты **Все программы, Azure**, щелкните правой кнопкой мыши **Azure PowerShell** и выберите **Запуск от имени администратора**.
2. Перейдите к папке, содержащей ваше приложение. Например, C:\\node\\tasklist\\WebRole1.
3. В окне Azure Powershell введите следующий командлет, чтобы получить сведения об учетной записи хранения:

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccount
    ```

   Этот командлет извлекает список учетных записей хранения и ключей учетной записи, связанных с вашей размещенной службой.

   > [!NOTE]
   > Поскольку пакет Azure SDK создает учетную запись хранения при развертывании службы, учетная запись хранения должна уже существовать в связи с развертыванием приложения в предыдущих руководствах.
   >
   >
4. Откройте файл **ServiceDefinition.csdef**, содержащий параметры среды, которые используются при развертывании приложения в Azure:

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. Вставьте следующий блок под элементом **Environment**, заменив {STORAGE ACCOUNT} и {STORAGE ACCESS KEY} именем учетной записи и первичным ключом для учетной записи хранения, которые будут использоваться для развертывания:

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![Содержимое файла web.cloud.config](./media/table-storage-cloud-service-nodejs/node37.png)

6. Сохраните файл и закройте Блокнот.

### <a name="install-additional-modules"></a>Установка дополнительных модулей
1. Используйте следующую команду, чтобы установить модули [azure], [node-uuid], [nconf] и [async] локально, а также чтобы сохранить запись для них в файле **package.json**:

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  Результат этой команды должен выглядеть аналогично следующему:

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>Использование службы таблиц в приложении Node
В этом разделе базовое приложение, созданное командой **express**, расширяется при добавлении файла **task.js**, который содержит модель для ваших задач. Измените существующий файл **app.js** и создайте файл **tasklist.js**, который использует эту модель.

### <a name="create-the-model"></a>Создание модели
1. В каталоге **WebRole1** создайте каталог с именем **models**.
2. В каталоге **models** создайте файл с именем **task.js**. Этот файл содержит модель для задач, создаваемых приложением.
3. В начале файла **task.js** добавьте следующий код для ссылки на необходимые библиотеки:

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. Затем добавьте код для определения и экспорта объекта Task. Объект Task отвечает за подключение к таблице.

    ```nodejs
    module.exports = Task;

    function Task(storageClient, tableName, partitionKey) {
      this.storageClient = storageClient;
      this.tableName = tableName;
      this.partitionKey = partitionKey;
      this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
        if(error) {
          throw error;
        }
      });
    };
    ```

5. Затем добавьте следующий код, чтобы определить дополнительные методы для объекта Task, обеспечивающего взаимодействие с данными, хранящимися в таблице:

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(this.tablename, query, null, null, function entitiesQueried(error, result) {
          if(error) {
            callback(error);
          } else {
            callback(null, result.entries);
          }
        });
      },

      addItem: function(item, callback) {
        self = this;
        // use entityGenerator to set types
        // NOTE: RowKey must be a string type, even though
        // it contains a GUID in this example.
        var itemDescriptor = {
          PartitionKey: entityGen.String(self.partitionKey),
          RowKey: entityGen.String(uuid()),
          name: entityGen.String(item.name),
          category: entityGen.String(item.category),
          completed: entityGen.Boolean(false)
        };

        self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
          if(error){
            callback(error);
          }
          callback(null);
        });
      },

      updateItem: function(rKey, callback) {
        self = this;
        self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
          if(error) {
            callback(error);
          }
          entity.completed._ = true;
          self.storageClient.replaceEntity(self.tableName, entity, function entityUpdated(error) {
            if(error) {
              callback(error);
            }
            callback(null);
          });
        });
      }
    }
    ```

6. Сохраните и закройте файл **task.js** .

### <a name="create-the-controller"></a>Создание контроллера
1. В каталоге **WebRole1/routes** создайте файл с именем **tasklist.js** и откройте его в текстовом редакторе.
2. Добавьте в **tasklist.js**следующий код. Этот код загружает модули azure и async, которые используются файлом **tasklist.js**, и определяет функцию **TaskList**, передающую экземпляр объекта **Task**, определенного ранее:

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. Продолжайте добавление в файл **tasklist.js** методов, используемых для **showTasks**, **addTask** и **completeTasks**:

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = new azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
        var item = {
            name: req.body.name, 
            category: req.body.category
        };
        self.task.addItem(item, function itemAdded(error) {
          if(error) {
            throw error;
          }
          res.redirect('/');
        });
      },

      completeTask: function(req,res) {
        var self = this;
        var completedTasks = Object.keys(req.body);
        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
          self.task.updateItem(completedTask, function itemsUpdated(error) {
            if(error){
              callback(error);
            } else {
              callback(null);
            }
          });
        }, function goHome(error){
          if(error) {
            throw error;
          } else {
            res.redirect('/');
          }
        });
      }
    }
    ```

4. Сохраните файл **tasklist.js**.

### <a name="modify-appjs"></a>Изменение app.js
1. В каталоге **WebRole1** откройте файл **app.js** в текстовом редакторе.
2. В начале файла добавьте следующий код для загрузки модуля Аzure, а также задания имение таблицы и ключа раздела (partitionKey):

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. Прокрутите файл app.js вниз до появления следующей строки:

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    Замените приведенные выше строки следующим кодом. Этот код инициализирует экземпляр <strong>Task</strong>, используя подключение к вашей учетной записи хранения. <strong>Task</strong> передается в <strong>TaskList</strong>, где используется для обмена данными со службой таблиц:

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. Сохраните файл **app.js** .

### <a name="modify-the-index-view"></a>Изменение представления индекса
1. Измените каталоги на каталог **views** и откройте файл **index.jade** в текстовом редакторе.
2. Замените содержимое файла **index.jade** следующим кодом. Этот код определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

    ```
    extends layout

    block content
      h1= title
      br

      form(action="/completetask", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Date
            td Complete
          if tasks == []
            tr
              td
          else
            each task in tasks
              tr
                td #{task.name._}
                td #{task.category._}
                - var day   = task.Timestamp._.getDate();
                - var month = task.Timestamp._.getMonth() + 1;
                - var year  = task.Timestamp._.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
        button.btn(type="submit") Update tasks
      hr
      form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
    ```

3. Сохраните и закройте файл **index.jade** .

### <a name="modify-the-global-layout"></a>Изменение глобального макета
Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге измените файл **layout.jade** для использования [Twitter Bootstrap](https://github.com/twbs/bootstrap) — набора средств, упрощающего разработку привлекательного веб-сайта.

1. Загрузите и извлеките файлы [Twitter Bootstrap](http://getbootstrap.com/). Скопируйте файл **bootstrap.min.css** из папки **bootstrap\\dist\\css** в каталог **public\\stylesheets** своего приложения tasklist.
2. В папке **views** откройте файл **layout.jade** в текстовом редакторе и замените его содержимое следующим:
 
```jade
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body.app
        nav.navbar.navbar-default
          div.navbar-header
            a.navbar-brand(href='/') My Tasks
        block content
```
3. Сохраните файл **layout.jade**.

### <a name="running-the-application-in-the-emulator"></a>Запуск приложения в эмуляторе
Выполните следующую команду, чтобы проверить приложение в эмуляторе.

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

Браузер открывается и отображает следующую страницу:

![Веб-страница под названием My Task List ("Мой список задач") с таблицей, содержащей задачи, и поля для добавления новой задачи](./media/table-storage-cloud-service-nodejs/node44.png)

Используйте форму для добавления или удаления существующих элементом, помечая их как завершенные.

## <a name="publishing-the-application-to-azure"></a>Публикация приложения в Azure
В окне Windows PowerShell вызовите следующий командлет, чтобы повторно развернуть размещенную службу в Azure.

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

Замените **myuniquename** уникальным именем приложения. Замените **datacentername** именем центра обработки данных Azure, например **West US**.

После завершения развертывания должен появиться ответ, похожий на следующий:

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

Благодаря указанию параметра **-launch** в приведенном выше командлете после завершения публикации браузер открывает и отображает приложение, запущенное в Azure.

![В окне браузера отображается страница «Мой список задач». URL-адрес указывает, что теперь страница размещается в Azure.](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Остановка и удаление приложения
После развертывания приложения может потребоваться отключить его, чтобы сократить затраты или построить и развернуть другие приложения в течение периода бесплатного пробного использования.

Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени.
Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии.

Ниже показано, как остановить и удалить приложение.

1. В окне Windows PowerShell остановите развертывание службы, созданное в предыдущем разделе со следующего командлета:

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.

2. Чтобы удалить службу, вызовите следующий командлет:

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   При появлении запроса введите **Y** , чтобы удалить службу.

   Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.

[Создание веб-приложения Node.js с использованием модуля Express в облачной службе Azure]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[Хранилище Azure]: https://docs.microsoft.com/azure/storage/
[Построение и развертывание приложения Node.js в облачной службе Azure]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/


