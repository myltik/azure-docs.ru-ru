<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Web App with Storage" pageTitle="Web app with table storage (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js hello world tutorial, Azure Node.js hello world, Azure Node.js Getting Started tutorial, Azure Node.js tutorial, Azure Node.js Express tutorial" description="A tutorial that builds on the Web App with Express tutorial by adding Azure Storage services and the Azure module." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Node.js Web Application using Storage" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Веб-приложение Node.js, использующее хранилище

В этом учебнике вы расширите возможности приложения, созданного в учебнике [Веб-приложение Node.js с использованием Express][], с помощью клиентских библиотек Windows Azure для Node.js, позволяющих работать со службами управления данными. Вы расширите возможности приложения за счет создания веб-приложения списка задач,которое можно развернуть в Azure. Список задач позволяет пользователю извлекать задачи, добавлять новые задачи и помечать задачи как завершенные.

Элементы задач хранятся в хранилище Azure. Хранилище Azure обеспечивает хранение неструктурированных данных с функциями отказоустойчивости и высокой доступности. Хранилище Azure включает в себя несколько структур данных, где можно хранить данные и осуществлять доступ к ним, кроме того, вы можете использовать службы хранилища из API-интерфейсов, включенных в состав пакета Azure SDK для Node.js или через API-интерфейсы REST. Дополнительные сведения см. в разделе [Хранение данных и доступ к ним в Azure][].

В этом учебнике предполагается, что вы завершили учебники [Веб-приложение Node.js][] и [Node.js с Express][Веб-приложение Node.js с использованием Express].

Вы узнаете:

-   Как работать с подсистемой шаблонов Jade
-   Как работать со службами управления данными Azure

Снимок экрана завершенного приложения приведен ниже:

![Готовая веб-страница в Internet Explorer][]

## Настройка учетных данных хранилища в файле Web.Config

Для доступа к хранилищу Azure необходимо передать
учетные данные хранилища. Для этого используются параметры приложения web.config. Эти параметры передаются в качестве переменных среды в Node, которые затем считываются пакетом Azure SDK.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Учетные данные хранилища используются только при развертывании приложения в Azure При запуске в эмуляторе приложение будет использовать эмулятор хранилища.</p>
</div>

Выполните следующие действия, чтобы получить учетные данные учетной записи хранения и добавить их в параметры web.config:

1.  Если этот еще не сделано, запустите PowerShell Azure из меню **Пуск**, развернув пункты **Все программы, Azure**, щелкнув правой кнопкой мыши элемент **Azure PowerShell** и выбрав **Запуск от имени администратора**.

2.  Перейдите к папке, содержащей ваше приложение. Например, C:\\node\\tasklist\\\\WebRole1.

3.  В окне Azure Powershell введите следующий командлет, чтобы получить сведения об учетной записи хранения:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    При этом извлекается список учетных записей хранилища и ключей учетной записи, связанных с вашей размещенной службой.

    <div class="dev-callout">
<strong>Примечание.</strong>
<p>Поскольку пакет Azure SDK создает учетную запись хранения при развертывании службы, учетная запись хранения должна уже существовать в связи с развертыванием приложения в предыдущих руководствах.</p>
</div>

4.  Откройте файл **ServiceDefinition.csdef**, содержащий параметры среды, которые используются при развертывании приложения в Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Вставьте следующий блок под элементом **Environment**, заменив {STORAGE ACCOUNT} и {STORAGE ACCESS KEY} на имя учетной записи и первичный ключ для учетной записи хранения, которые требуется использовать для развертывания:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![Содержимое файла web.cloud.config][]

6.  Сохраните файл и закройте Блокнот.

### Установка дополнительных модулей

1.  Используйте следующую команду для установки модулей [azure], [node-uuid], [nconf] и [async] локально, а также для сохранения записи о них в файле **package.json**:

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    Результат этой команды должен выглядеть аналогично следующему:

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

## Использование службы таблиц в приложении Node

В этом разделе базовое приложение, созданное командой **express**, будет расширено путем добавления файла **task.js**, содержащего модель для ваших задач. Также будет изменено существующее приложение **app.js** и создан новый файл контроллера **tasklist.js** для использования этой модели.

### Создание модели

1.  В каталоге **WebRole1** создайте новый каталог с именем **models**.

2.  В каталоге **models** создайте новый файл с именем **task.js**. Этот файл будет содержать модель для задач, создаваемых приложением.

3.  В начале файла **task.js** добавьте следующий код для ссылки на необходимые библиотеки:

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4.  Далее будет добавлен код для определения и экспорта объекта Task. Этот объект отвечает за подключение к таблице.

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

5.  Затем добавьте следующий код, чтобы определить дополнительные методы для объекта Task, обеспечивающего взаимодействие с данными, хранящимися в таблице:

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6.  Сохраните и закройте файл **task.js**.

### Создание контроллера

1.  В каталоге **WebRole1/routes** создайте новый файл с именем **tasklist.js** и откройте его в текстовом редакторе.

2.  Добавьте в **tasklist.js** следующий код. Он загружает модули azure и async, используемые **tasklist.js**. Он также определяет функцию **TaskList**, передающую экземпляр объекта **Task**, определенного ранее:

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

3.  Продолжит добавления в файл **tasklist.js**, добавляя методы, используемые для **showTasks**, **addTask** и **completeTasks**:

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
            var item = req.body.item;
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

4.  Сохраните файл **tasklist.js**.

### Изменение app.js

1.  В каталоге **WebRole1** откройте файл **app.js** в текстовом редакторе.

2.  В начале файла добавьте следующий код для загрузки модуля Аzure, а также задания имение таблицы и ключа раздела (partitionKey):

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3.  Прокрутите файл app.js вниз до появления следующей строки:

        app.use('/', routes);
        app.use('/users', users);

    Замените вышеприведенные строки на код, приведенный ниже. Он инициализирует экземпляр **Task**, используя подключение к вашей учетной записи хранения. Он передается в **TaskList**, где будет использоваться для взаимодействия со службой таблиц:

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

4.  Сохраните файл **app.js**.

### Изменение представления индекса

1.  Измените каталоги на каталог **views** и откройте файл **index.jade** в текстовом редакторе.

2.  Заменит содержимое файла **index.jade** на код, приведенный ниже. Он определяет представление для отображения существующих задач, а также форму для добавления новых задач и пометки существующих задач как завершенных.

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
              if tasks != []
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
            input(name="item[name]", type="textbox")
            label Item Category: 
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3.  Сохраните и закройте файл **index.jade**.

### Изменение глобального макета

Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге он будет изменен для использования [Twitter Bootstrap][] — набора средств, упрощающих разработку привлекательного веб-сайта.

1.  Загрузите и извлеките файлы [Twitter Bootstrap][1]. Скопируйте файл **bootstrap.min.css** из папки **bootstrap\\dist\\css** в каталог **public\\stylesheets** своего приложения tasklist.

2.  В папке **views** откройте файл **layout.jade** в текстовом редакторе и замените его содержимое на следующее:

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

3.  Сохраните файл **layout.jade**.

### Запуск приложения в эмуляторе

Выполните следующую команду, чтобы проверить приложение в эмуляторе.

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

Браузер открывается и отображает следующую страницу:

![Веб-страница под названием My Task List ("Мой список задач") с таблицей, содержащей задачи, и поля для добавления новой задачи][]

Используйте форму для добавления или удаления существующих элементом, помечая их как завершенные.

## Публикация приложения в Azure

В окне Windows PowerShell вызовите следующий командлет, чтобы повторно развернуть размещенную службу в Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Замените **myuniquename** на уникальное имя приложения. Замените **datacentername** на имя центра обработки данных Azure, например, **West US** (Запад США).

После завершения развертывания должен появиться ответ, похожий на следующий:

    PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
    WARNING: Publishing tasklist to Windows Azure. This may take several minutes...
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

Как и раньше, благодаря указанию параметра **-launch** после завершения публикации браузер открывает и отображает приложение, запущенное в Azure.

![В окне браузера отображается страница "Мой список задач". URL-адрес указывает, что теперь страница размещается в Azure.][Готовая веб-страница в Internet Explorer]

## Остановка и удаление приложения

После развертывания приложения может потребоваться отключить его, чтобы сократить затраты или построить и развернуть другие приложения в течение периода бесплатного пробного использования.

Azure предусматривает взимание платы за час использованного рабочего времени, а серверное время потребляется, когда приложение развернуто, даже если экземпляры не выполняются и находятся в состоянии остановки.

Ниже показано, как остановить и удалить приложение.

1.  В окне Windows PowerShell остановите развертывание службы, созданной в предыдущем разделе, с помощью следующего командлета:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.

2.  Чтобы удалить службу, вызовите следующий командлет:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    При появлении запроса введите **Y**, чтобы удалить службу.

    Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.

  [Веб-приложение Node.js с использованием Express]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/web-app-with-express/
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Веб-приложение Node.js]: http://www.windowsazure.com/en-us/develop/nodejs/tutorials/getting-started/
  [Готовая веб-страница в Internet Explorer]: ./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png
  [Содержимое файла web.cloud.config]: ./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png
  [Twitter Bootstrap]: https://github.com/twbs/bootstrap
  [1]: http://getbootstrap.com/
  [Веб-страница под названием My Task List ("Мой список задач") с таблицей, содержащей задачи, и поля для добавления новой задачи]: ./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png
