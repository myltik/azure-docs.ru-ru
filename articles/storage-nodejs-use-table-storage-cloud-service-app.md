<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="Веб-приложение с хранилищем" pageTitle="Веб-приложение с хранилищем таблиц (Node.js) | Microsoft Azure" metaKeywords="учебник Azure Node.js hello world, Azure Node.js hello world, приступая к работе с Azure Node.js, учебник Azure Node.js, учебник Azure Node.js Express" description="Учебник, который опирается на учебник по веб-приложению с Express и добавляет службы хранилища Azure и модуль Azure." metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="Веб-приложение Node.js, использующее хранилище" authors="" solutions="" manager="" editor="" />





# Веб-приложение Node.js, использующее хранилище

В этом учебнике вы расширите возможности приложения, созданного в
учебнике [Веб-приложение Node.js с использованием Express], с помощью клиентских библиотек Windows
Azure для Node.js, позволяющих работать со службами управления данными. Вы
расширите возможности приложения за счет создания веб-приложения списка задач,
которое можно развернуть в Azure. Список задач позволяет пользователю
извлекать задачи, добавлять новые задачи и помечать задачи как завершенные.

Элементы задач хранятся в хранилище Azure. Хранилище
Azure обеспечивает хранение неструктурированных данных с функциями отказоустойчивости
и высокой доступности. Хранилище Azure включает в себя несколько структур данных, где можно хранить данные и осуществлять доступ к ним, кроме того, вы можете использовать службы хранилища из API-интерфейсов, включенных в состав пакета Azure SDK для Node.js, или через API-интерфейсы REST. Дополнительные сведения см. в разделе [Хранение данных и доступ к ним в Azure].

В этом учебнике предполагается, что вы завершили учебники [Веб-приложение Node.js] и [Node.js с Express][Node.js Web Application using Express].

Вы узнаете:

-   Как работать с подсистемой шаблонов Jade
-   Как работать со службами управления данными Azure

Снимок экрана завершенного приложения приведен ниже:

![Готовая веб-страница в Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Настройка учетных данных хранилища в файле Web.Config

Для доступа к хранилищу Azure необходимо передать
учетные данные хранилища. Для этого используются параметры приложения web.config.
Эти параметры передаются в качестве переменных среды в Node,
которые затем считываются пакетом Azure SDK.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Учетные данные хранилища используются только при
развертывании приложения в Azure. При запуске в эмуляторе приложение будет использовать
эмулятор хранения.</p>
</div>

Выполните следующие действия, чтобы получить учетные данные учетной записи хранения и добавить их в параметры web.config.

1.  Если этот еще не сделано, запустите PowerShell Azure из меню **Пуск**, развернув пункты **Все программы, Azure**, щелкнув правой кнопкой мыши элемент **Azure PowerShell** и выбрав **Запуск от имени администратора**.

2.  Перейдите к папке, содержащей ваше приложение. Например, C:\\node\\tasklist\\WebRole1.

3.  В окне Azure Powershell введите следующий командлет, чтобы получить сведения об учетной записи хранения.

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	При этом извлекается список учетных записей хранилища и ключей учетной записи, связанных с вашей размещенной службой.

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>Поскольку пакет Azure SDK создает учетную запись хранения при развертывании службы, учетная запись хранения должна уже существовать в связи с развертыванием приложения в предыдущих руководствах.</p>
	</div>

4.  Откройте файл web.cloud.config, содержащий параметры среды, которые используются при развертывании приложения в Azure:

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  Вставьте следующий блок под элементом **configuration**, заменив {STORAGE ACCOUNT} и {STORAGE ACCESS KEY} на имя учетной записи и первичный ключ для учетной записи хранения, которые требуется использовать для развертывания:

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

	![Содержимое файла web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Сохраните файл и закройте Блокнот.

## Установка модулей

Для использования служб управления данных Azure необходимо установить
модуль Azure для узла. Необходимо также установить модуль node-uuid, который
будет использоваться для создания глобальных уникальных идентификаторов (UUID). Чтобы
установить эти модули, введите следующую команду:

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

После выполнения команды модули добавляются в
папку **node\_modules**. Выполните следующие действия, чтобы использовать
эти модули в своем приложении:

1.  Откройте файл server.js:

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  После строки, которая заканчивается на express.createServer(), добавьте приведенный ниже ко, чтобы включать модули node-uuid, home и azure. Модуль home еще не существует, но будет создан позже.

	![Код server.js с выделенной строкой line app = modules.exports](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  Добавьте код для создания клиента таблицы хранилища, передав информацию об учетной записи хранения и ключе доступа.

	<div class="dev-callout">
	<strong>Примечание.</strong>
	<p>При работе в эмуляторе пакет SDK автоматически использует эмулятор, даже если сведения об учетной записи хранения были предоставлены через web.config.</p>
	</div>

        var client = azure.createTableService();

4.  Затем создайте в хранилище Azure таблицу с именем tasks. Приведенная ниже логическая схема создает новую таблицу, если она не существует, и заполняет эту таблицу данными по умолчанию.

        //table creation
        client.createTableIfNotExists('tasks', function(error){
            if(error){
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey: 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  Замените существующий код в разделе route на приведенный ниже код, который создает экземпляр контроллера home и направляет на него все запросы для **/** или **/home**.

	![Файл server.js с выделенным разделом routes.](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

	Обратите внимание, что вместо внутренней обработки запроса вы теперь делегируете команду объекту Home. Команда **bind** обеспечивает правильное локальное разрешение этих ссылок в контроллере home.

## Создание контроллера Home

Теперь необходимо создать контроллер home, который обрабатывает все запросы
для сайта списка задач. Выполните следующие действия, чтобы создать этот
контроллер:

1.  Создайте новый файл home.js в Блокноте. Этот файл будет содержать
    код контроллера, реализующий логическую схему для списка задач.

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  Замените содержимое файла на приведенный ниже код и сохраните файл. В следующем
    коде используется шаблон модуля JavaScript. Он экспортирует функцию
    Home. Прототип Home содержит функции для обработки
    фактических запросов.

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
        };

        Home.prototype = {
            showItems: function (req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                    }			
                    self.showResults(res, tasklist);
                });
            },

            getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
        	
                if (!allItems) {
                    query = query.where('completed eq ?', 'false');
                }
                this.client.queryEntities(query, callback);
             },

             showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout: false, 
                    tasklist: tasklist });
             },
        };

	Теперь ваш контроллер home включает в себя три функции:

	-   *showItems* обрабатывает запрос.
	-   *getItems* использует таблицу клиента для получения элементов открытой задачи
    из таблицы задач. Обратите внимание, что запрос может использовать дополнительные
        фильтры. Например, фильтры предыдущего запроса отображают только
        те задачи, у которых значение completed равно false.
	-   *showResults* вызывает функцию визуализации Express для
        отрисовки страницы с помощью представления home, которое предстоит создать в
        следующем разделе.

### Изменение представления Home

Подсистема шаблонов Jade использует синтаксис разметки, который является менее подробным по сравнению с HTML, и является обработчиком по умолчанию для Express. Выполните
следующие действия, чтобы создать представление, которое поддерживает отображение элементов
списка задач:

1.  Из окна команд Windows PowerShell отредактируйте файл home.jade
    с помощью следующей команды:

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  Замените содержимое файла home.jade на код, приведенный ниже, и
    сохраните файл. Приведенная ниже форма содержит функциональные возможности для чтения
    и обновление элементов задачи. (Обратите внимание, что в настоящее время контроллер home
    поддерживает только чтение, позднее вы несете необходимые изменения.) Форма
    содержит подробные сведения для каждого элемента из списка задач.

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

## Запуск приложения в эмуляторе вычислений

1.  В окне Windows PowerShell введите следующий командлет для
    запуска службы в эмуляторе вычислений и отображения веб-страницы,
    которая вызывает службу.

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

	Браузер отображает следующую страницу, показывая элемент задачи, который был извлечен из хранилища Azure:

	![Отображение страницы "Мой список задач" с одним элементом в таблице в Internet Explorer.](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

## Добавление функциональной возможности новой задачи

В этом разделе вы обновите приложение, реализовав поддержку добавления новых элементов
задачи.

### Добавление нового маршрута в Server.js

Добавьте в файл server.js следующую строку после последней записи маршрута
для **/home**, а затем сохраните файл.

![Файл server.js с выделенной строкой, содержащей маршрут для home.](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

	Раздел routes теперь должен выглядеть следующим образом:

       // Routes

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### Добавление модуля Node-UUID

Чтобы использовать модуль node-uuid для создания уникального идентификатора, добавьте
следующую строку в верхнюю часть файла home.js после первой строки,
в которой экспортируется модуль.

![Файл home.js с выделенной строкой module.exports = Home.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### Добавление функции нового элемента в контроллер Home

Для реализации функции нового элемента создайте функцию **newItem**.
Вставьте в файл home.js следующий код после последней функции,
а затем сохраните файл.

![Функция showresults выделена](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function (req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error){  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

Функция **newItem** выполняет следующие задачи:

-   Извлекает переданный элемент из тела.
-   Задает значения **RowKey** и **PartitionKey** для нового элемента.
    Эти параметры необходимы для вставки элемента в таблицу
    Azure. Создается идентификатор UUID для значения **RowKey**.
-   Вставляет элемент в таблицу задач путем вызова
    функции **insertEntity**.
-   Отображает страницу путем вызова функции **getItems**.

### Добавление формы нового элемента в представление Home

Теперь обновите представление путем добавления новой формы, чтобы разрешить пользователю добавить
элемент. Вставьте следующий код в конец файла home.jade
и сохраните его.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>В Jade пробел имеет значение, поэтому не удаляйте
никакие из указанных ниже пробелов.</p>
</div>

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### Запуск приложения в эмуляторе

1.  Поскольку эмулятор Azure уже запущен, можно
    просмотреть обновленное приложение:

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

	Браузер открывается и отображает следующую страницу:

	![Веб-страница под названием "Мой список задач" с таблицей, содержащей задачи, и поля для добавления новой задачи.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  Введите для параметра **Имя элемента:** "New task functionality", для **Категория элемента:** значение "Site work" и для **Дата элемента:** значение "12/02/2011". Нажмите кнопку **Добавить элемент**.

	Элемент добавлен в таблицу задач хранилища Azure задачи и отображается, как показано на следующем снимке экрана.

	![Веб-страница под названием "Мой список задач" с таблицей, содержащей задачи, после добавления задачи в список.](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

## Повторная публикация приложения в Azure

Теперь, когда приложение готово, опубликуйте его в Azure путем
обновления развертывания в существующей размещенной службе.

1.  В окне Windows PowerShell вызовите следующий командлет,
    чтобы повторно развернуть размещенную службу в Azure. Параметры хранилища и
    расположение были сохранены, поэтому их не требуется вводить еще раз.

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

	После завершения развертывания должен появиться ответ, похожий на следующий:

	![сообщения о состоянии, отображаемое во время развертывания.](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

	Как и раньше, благодаря указанию параметра **-launch** после завершения публикации браузер открывает и отображает приложение, запущенное в Azure.

	![В окне браузера отображается страница "Мой список задач". URL-адрес указывает, что теперь страница размещается в Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

## Остановка и удаление приложения

После развертывания приложения может потребоваться отключить его, чтобы
сократить затраты или построить и развернуть другие приложения в течение периода
бесплатного пробного использования.

Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени.
Серверное время используется с момента развертывания приложения,
даже если экземпляры не запущены и находятся в остановленном состоянии.

Ниже показано, как остановить и удалить приложение.

1.  В окне Windows PowerShell остановите развертывание службы,
    созданное в предыдущем разделе, с помощью следующего командлета:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	Остановка службы может занять несколько минут. Если эта служба остановлена, появится сообщение, указывающее на то, что она была остановлена.

	![Сообщения о состоянии, указывающие на остановку службы.](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

3.  Чтобы удалить службу, вызовите следующий командлет:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	При появлении запроса введите **Y**, чтобы удалить службу.

	Удаление службы может занять несколько минут. После удаления службы появится сообщение, указывающее, что служба была удалена.

	![Сообщения о состоянии, указывающие на удаление службы.](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)

  [Веб-приложение Node.js с использованием Express]: http://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/web-app-with-express/
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Веб-приложение Node.js]: http://www.windowsazure.com/ru-ru/develop/nodejs/tutorials/getting-started/
 

