<properties title="Build a Node.js web application using DocumentDB" pageTitle="Build a Node.js web application using DocumentDB | Azure" description="Learn how to use Azure DocumentDB to store and access data from a Node.js application hosted on Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="ryancraw"></tags>

# <a name="_Toc395783175">Создание веб-приложения Node.js с использованием DocumentDB</a>

<a name="_Toc395783175">

В этом учебном курсе показано, как использовать службу Azure DocumentDB для хранения данных и обеспечения доступа к ним из приложения
Node.js Express, размещенного на веб-сайтах Azure.

Этот учебный курс разработан для читателей, обладающих определенным опытом использования Node.js.

Вы узнаете:

Как использовать средства Node.js для Visual Studio

Как работать со службой Azure DocumentDB, используя модуль documentdb из npm

Как развернуть веб-приложение на веб-сайтах Azure

В ходе учебного курса вы создадите простое веб-приложение
 для управления задачами, позволяющее создавать, извлекать и
 выполнять задачи. Задачи будут храниться в виде документов JSON в Azure
DocumentDB.

![Альтернативный текст][]

## <a name="_Toc395783176">Предварительные требования</a>

Перед выполнением инструкций, приведенных в этой статье, следует убедиться,
 что установлены следующие компоненты:

[Node.js][] версии v0.10.29 или более поздней версии

[Git][]

[Visual Studio 2013][] с обновлением 3

[Средства Node.js для Visual Studio][]

**Примечание.** Несмотря на то, что мы используем Visual Studio для создания, отладки и развертывания нашего проекта Node.js
 в этом учебнике, вы можете использовать любой предпочтительный редактор
 и запускать Node.js непосредственно на выбранной вами платформе,
 как это обычно происходит. Для развертывания приложения на веб-сайтах Azure можно использовать [средства командной строки][] Azure

# <a name="_Toc395637761">Создание учетной записи базы данных DocumentDB</a>

Для подготовки учетной записи базы данных DocumentDB в Azure, откройте[портал управления Azure][]
 и, либо щелкните плитку «Коллекция Azure» на домашней странице, либо нажмите «+» в левом нижнем углу экрана.

![Альтернативный текст][1]

Откроется коллекция Azure, где можно будет выбрать из множества
 доступных служб Azure. В коллекции выберите «Данные, хранение и
 резервное копирование» из списка категорий.

![Альтернативный текст][2]

Здесь выберите параметр для Azure DocumentDB

![Альтернативный текст][3]

Затем нажмите «Создать» в нижней части экрана

![Альтернативный текст][4]

Откроется выноска «Новая DocumentDB», где можно будет задать
 имя, регион, масштаб, группу ресурсов и прочие настройки новой
 учетной записи.

![Альтернативный текст][5]

Как только вы закончите ввод значений для вашей учетной записи, нажмите кнопку «Создать»
и начнется процесс подготовки создания учетной записи базы данных.
 Когда процесс подготовки будет завершен, вы уведите уведомление,
 которое появится в области уведомлений портала, а плитка на
 экране запуска (если вы выбрали ее для создания) изменится, чтобы отобразить
 выполненное действие.

![Альтернативный текст][6]

После завершения подготовки при нажатии плитки DocumentDB на
 экране запуска появится главная выноска для этой вновь созданной
DocumentDB.

![Альтернативный текст ][]
![Альтернативный текст ][7]

При помощи кнопки «Ключи», получите URL-адрес вашей конечной точки и первичный ключ,
 скопируйте их в буфер обмена и держите их под рукой, так как мы будем использовать
 эти значения в веб-приложении, которое создадим в последующем.

## <a name="_Toc395783178">Создание нового приложения Node.js</a>

В Visual Studio выберите Файл – Новый проект и выберите вариант создания нового приложения «Базовое приложение Microsoft Azure Express».

![Альтернативный текст][8]

Будет создано базовое приложение Express. Если появится запрос
 «установить зависимости?», выберите ответ «Да». При этом будут установлены все пакеты
, которые требуются для нового приложения Express.

После установки обозреватель решений должен иметь
следующий вид;

![Альтернативный текст][9]

Видно, что установлены Express, Jade и Stylus.

Если нажать клавишу F5 в Visual Studio, то произойдет сборка проекта, запуск
 Node.js и откроется веб-браузер с эквивалентом Express приложения «Hello
 World».

![Альтернативный текст][10]

## <a name="_Toc395783179">Установка дополнительных модулей</a>

Файл **package.json** является одним из файлов, созданных в корневой папке
 проекта. Этот файл содержит список дополнительных модулей,
 необходимых для приложения Express. Позднее, при развертывании этого
 приложения на веб-сайте Azure, этот файл будет использоваться, чтобы определить,
 какие модули должны быть установлены в Azure для поддержки приложения.

![Альтернативный текст][11]

Для этого учебника нужны два дополнительных модуля.

Щелкните правой кнопкой мыши на «npm» в обозревателе решений и выберите пункт «Установить пакеты
 npm»

![Альтернативный текст][12]

В диалоговом окне «Установка новых пакетов npm» введите **nconf** для поиска
 модуля. Этот модуль будет использоваться приложением для считывания настроек подключения
 к базе данных из файла конфигурации.

![Альтернативный текст][13]

Наконец, установите модуль Azure DocumentDB таким же образом,
найдя строку **documentdb**. В этом модуле заключена вся «магия»
DocumentDB.

После установки этих двух дополнительных модулей и зависимостей,
 они должны появиться в обозревателе решений в группе модулей **npm**
.

![Альтернативный текст][14]

В файле приложения **package.json** должны быть
указаны дополнительные модули. Этот файл позже будет использоваться Azure для загрузки и установки
пакетов, необходимых при выполнении
 приложения.

При необходимости для воссоздания примера отредактируйте файл package.json.

![Альтернативный текст][15]

Это сообщает Node (и позже Azure), что ваше приложение зависит от этих дополнительных
 модулей.

# <a name="_Toc395783180">Использование службы DocumentDB в приложении Node</a>

После завершения начальной установки и настройки, давайте перейдем к, собственно, написанию кода приложения, использующего Azure DocumentDB. Для начала отредактируйте файл **app.js**, расположенный в корневой папке только что созданного приложения Express. Найдите в файле следующие строки:

    app.get('/', routes.index);
    app.get('/users', user.list);

И замените их этими двумя строками:

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

Эти строки сообщат программе, что делать по умолчанию с методами GET и POST на форме, которую мы создадим в последующем.

Найдите файл **index.js** в папке **routes**. Откройте его в редакторе и удалите из него весь код.

Добавьте следующий код в начало файла;

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

Тут определяются модули, которые мы собираемся использовать, это **documentdb** и **nconf**.

**nconf** — это модуль, который позволяет загружать значения конфигурации, такие как настройки подключения базы данных, из внешних файлов, а не устанавливать эти значения в коде. nconf будет искать конфигурацию по умолчанию в файле **config.json**.

Далее, создадим пустой текстовый файл **config.json** в корневой папке проекта (там же, где находится файл app.js)

Откройте созданный **config.json** и введите следующие значения, в зависимости от конкретной конечной точки DocumentDB. Убедитесь в правильности введенных значений HOST и MASTER\_KEY.

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

Вернитесь к файлу **index.js**, добавьте следующие строки после последних строк для того, чтобы реализовать чтение конфигурационного файла и хранение значений в переменных уровня страницы.

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

После того, как это будет сделано, добавьте следующий код в **index.js**

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

Это и есть две функции, о которые мы говорили ранее, что они нужны для использования в приложении в **app.js**, когда мы определили маршруты. При получении представлением индекса команды GET запустится функция **exports.index**, аналогично при получении команды POST запустится функция **exports.createOfUpdateItem**.

Эти две функции реализуют всю функциональность приложения, которое мы разрабатываем, однако они обращаются к другим функциям, только для того, чтобы сделать код более читаемым и отслеживаемым. Добавьте в файл **index.js** следующий код: Он содержит все методы, используемые двумя вышеуказанными функциями выше и все вызовы DocumentDB. Мы более подробно ознакомимся с каждой функцией позже.

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** — обновляет документ в базе данных на основе идентификатора элемента, переданного из формы. Этот идентификатор используется для запуска метода ***readDocument*** в DocumentDB, чтобы прочитать определенный документ, сохраненный ранее. Затем он изменяет значение атрибута документа «завершен» в true, указав, что он уже завершен, а затем переходит к замене документа в базе данных.

**getItem —** использует ***queryDocuments*** ,чтобы получить одиночный элемент из базы данных, через свойство идентификатора элемента.

**createItem** — использует метод ***createDocument*** для создания нового документа в базе данных на основе введенных в форме имени элемента и его категории. Он также устанавливает флаг завершения в false, чтобы указать, что этот элемент еще не завершен.

**listItems** — использует ***queryDocuments*** для поиска всех документов в коллекции, которые еще не завершены, т.е. где completed = false. Он использует грамматику запросов DocumentDB на базе ANSI-SQL для демонстрации широко известной и мощной возможности.

**readOrCreateDatabase** — использует ***queryDatabases*** для проверки, существует ли база данных с указанным именем. Если такая база данных не найдена, мы продолжим и используем ***createDatabase*** чтобы создать новую базу данных с заданным идентификатором (из нашего конфигурационного файла) на указанной конечной точке (также из конфигурационного файла).

**readOrCreateCollection** — как и readOrCreateDatabase, этот метод сначала попытается найти коллекцию с заданным идентификатором, если она существует, то он завершается, если не существует, то коллекция создается.

Вот и весь код, который необходим для этого образца приложения, чтобы взаимодействовать с DocumentDB.

Теперь давайте обратим наше внимание на создание пользовательского интерфейса, чтобы пользователь смог взаимодействовать с нашим приложением. Созданное нами приложение Express использует в качестве обработчика представлений **Jade**. Дополнительные сведения о Jade см. на сайте <http://jade-lang.com/>

Откройте файл **layout.jade** , расположенный в папке **views**, и замените его содержимое следующим;

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

Этот код указывает обработчику **Jade** создать HTML для нашего приложения, а также «**блок**» под названием «**контент**», в котором можно поместить макет наших страниц.

Откройте файл **index.jade** представления, связанного с index.js, и замените его содержимое следующим;

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

Этот код дополнит макет и добавит контент для заполнителя «блок контента»
 для файла layout.jade.

В макете мы создали две формы HTML. В первой форме имеется таблица
 для наших данных и кнопка, позволяющая обновлять элементы. Вторая форма содержит
 несколько полей ввода и кнопку, позволяющую создавать новые элементы.

При нажатии любой из этих кнопок вызывается функция **createOrUpdateItem**
 (т. е. в форме POST), созданная нами в файле **index.js**
. Если страница была загружена непосредственно (т. е. выполнен GET
), то запускается функция **index**.

Этого должно быть достаточно для работы нашего приложения.

</h1>
# <a name="_Toc395783181">Локальный запуск приложения</a>

Чтобы проверить приложение на локальном компьютере, нажмите клавишу F5 в Visual Studio:
должна произойти сборка приложения, запуск Node.js и откроется веб-браузер
со страницей, похожей на приведенную ниже:

![Альтернативный текст][16]

1. Используйте предоставленные поля Имя элемента и Имя категории для ввода
данных, а затем нажмите кнопку **Добавить элемент**.

2. Страница должна обновиться, чтобы показать элемент в таблице списка задач
.

 	![Альтернативный текст][17]

3. Чтобы завершить задачу, просто установите флажок в столбце «Завершено»
 и нажмите кнопку **Обновить задачи.**

</h1>
# <a name="_Toc395783182">Разверните приложение на веб-сайтах Azure</a>

После установки средств Node.js для Visual Studio развертывание на веб-сайтах Azure
 выполняется легко выполнением нескольких шагов.

Щелкните правой кнопкой проект и выберите «Опубликовать».

![Альтернативный текст][18]

Затем следуйте указаниям мастера публикации, чтобы задать необходимую конфигурацию для
 вашего веб-сайта Azure. Мастер предложит либо выбрать обновление
 существующего веб-сайта, либо создание нового.

После задания необходимой конфигурации, нажмите кнопку «Опубликовать».

![Альтернативный текст][19]

Затем следуйте указаниям мастера публикации, чтобы задать необходимую конфигурацию для
вашего веб-сайта Azure. Мастер предложит либо выбрать обновление
существующего веб-сайта, либо создание нового.

После задания необходимой конфигурации, нажмите кнопку «Опубликовать».

После этого Visual Studio подключится к вашей подписке Azure и опубликует
 это приложение Node.js.

Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения
 и запустит браузер, где можно увидеть ваше творение,
 запущенное в Azure!

# <a name="_Toc395783183"></a> <a name="_Toc395637775">Заключение</a>

Поздравляем! Вы только что создали свое первое веб-приложение Node.js Express с использованием
 Azure DocumentDB и опубликовали его на веб-сайтах Azure.

Исходный код завершенного учебного приложения можно загрузить [здесь][].

</h1>

  [Альтернативный текст]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/en-us/vstudio/cc136611.aspx
  [Средства Node.js для Visual Studio]: https://nodejstools.codeplex.com/
  [средства командной строки]: http://azure.microsoft.com/ru-ru/documentation/articles/xplat-cli/
  [портал управления Azure]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [Альтернативный текст ]: ./media/documentdb-nodejs-application/image8.png
  [7]: ./media/documentdb-nodejs-application/image9.png
  [8]: ./media/documentdb-nodejs-application/image10.png
  [9]: ./media/documentdb-nodejs-application/image11.png
  [10]: ./media/documentdb-nodejs-application/image12.png
  [11]: ./media/documentdb-nodejs-application/image13.png
  [12]: ./media/documentdb-nodejs-application/image14.png
  [13]: ./media/documentdb-nodejs-application/image15.png
  [14]: ./media/documentdb-nodejs-application/image16.png
  [15]: ./media/documentdb-nodejs-application/image17.png
  [16]: ./media/documentdb-nodejs-application/image18.png
  [17]: ./media/documentdb-nodejs-application/image19.png
  [18]: ./media/documentdb-nodejs-application/image20.png
  [19]: ./media/documentdb-nodejs-application/image21.png
  [здесь]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409
