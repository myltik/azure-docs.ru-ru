<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Что произошло?

###### Добавленные ссылки

В проект добавлена библиотека мобильной службы Windows Azure в виде файла MobileServices.js.

###### Значения строк подключения для мобильных служб

В папке services\\mobileServices\\settings создан новый файл JavaScript (.js) с клиентом мобильных служб, содержащий ключ и URL-адрес выбранного приложения мобильной службы.

### Приступая к работе с мобильными службами

###### Получение ссылку на таблицу

В проект уже добавлен объект клиента. Его имя аналогично имени мобильной службы с добавлением слова "Client". Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления данных таблицы.

    var todoTable = yourMobileServiceClient.getTable('TodoItem');

###### Добавление записи

Вставьте новый элемент в таблицу данных. Автоматически создается идентификатор (GUID строки типа) в качестве основного ключа для новой строки. Не изменяйте тип столбца идентификатора, так как он используется инфраструктурой мобильных служб.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

###### Чтение таблицы и запросы к ней

Следующий код выполняет запрос ко всем элементам таблица, обновляет локальные коллекции и связывает результаты с элементом пользовательского интерфейса listItems.

        // This code refreshes the entries in the list view 
        // by querying the TodoItems table.
        todoTable.where()
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });

Можно использовать метод where для изменения запроса. Вот пример кода, который отфильтровывает завершенные элементы.

    todoTable.where(function () {
        return (this.complete === false && this.createdAt !== null);
    })
    .read()
    .done(function (results) {
        todoItems = new WinJS.Binding.List(results);
        listItems.winControl.itemDataSource = todoItems.dataSource;
    });

Дополнительные примеры запросов см. в разделе, посвященному [объекту query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

###### Обновление записи

Обновите строку в таблице данных. В этом примере todoItem является обновленным элементом, тем самым, который возвращается из мобильной службы. При получении ответа мобильной службы, элемент обновляется в локальном списке todoItems с помощью метода [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Вызовите метод [done]() для возвращенного объекта [Promise](), чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

###### Удаление записи

Удалите строку из таблицы данных. Вызовите метод [done]() для возвращенного объекта [Promise](), чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

    todoTable.delete(todoItem).done(function (item) {
        todoItems.splice(todoItems.indexOf(item), 1);
    }

   
