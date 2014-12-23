<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-mobile-services-javascript-getting-started/)
> - [Что произошло?](/documentation/articles/vs-mobile-services-javascript-what-happened/)

## Приступая к работе с мобильными службами

Первое действие, необходимое для использования этих примеров кода, зависит от типа мобильной службы, к которой вы подключились.

Для серверной мобильной службы JavaScript создайте таблицу под названием TodoItem. Чтобы создать таблицу, найдите мобильную службу в узле Azure (в обозревателе сервера), щелкните узел этой службы правой кнопкой мыши, чтобы открыть контекстное меню, и выберите команду **Создать таблицу**. Введите имя таблицы TodoItem.

Если же у вас есть серверная мобильная служба .NET, то среда Visual Studio уже создала в шаблоне проекта по умолчанию таблицу TodoItem, но ее нужно опубликовать в Azure. Чтобы опубликовать таблицу, откройте контекстное меню проекта мобильной службы в обозревателе решений и выберите команду **Опубликовать веб-сайт**. Примите предлагаемые по умолчанию параметры и нажмите кнопку **Опубликовать**.

#####Получение ссылку на таблицу

В проект уже добавлен объект клиента.  Его имя аналогично имени мобильной службы с добавлением слова "Client". Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления таблицы данных.

	var todoTable = yourMobileServiceClient.getTable('TodoItem');

#####Добавление записи 

Вставьте новый элемент в таблицу данных. Автоматически создается идентификатор (GUID строки типа) в качестве основного ключа для новой строки. Не изменяйте тип столбца идентификатора, так как он используется инфраструктурой мобильных служб.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

#####Чтение таблицы и запросы к ней

Следующий код выполняет запрос ко всем элементам таблицы, обновляет локальные коллекции и связывает результаты с элементом пользовательского интерфейса listItems.

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

Дополнительные примеры запросов, которые вы можете использовать, см. в разделе, посвященном [объекту query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

#####Обновление записи

Обновите какую-либо строку в таблице данных. В этом примере todoItem является обновленным элементом, тем самым, который возвращается из мобильной службы. При получении ответа мобильной службы, элемент обновляется в локальном списке todoItems с помощью метода [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Вызовите метод [done]() для возвращенного объекта [Promise](), чтобы получить копию вставленного объекта и обработать все ошибки.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####Удаление записи

Удалите какую-либо строку из таблицы данных. Вызовите метод [done]() для возвращенного объекта [Promise](), чтобы получить копию вставленного объекта и обработать все ошибки.

	todoTable.delete(todoItem).done(function (item) {
	    todoItems.splice(todoItems.indexOf(item), 1);
    }



[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)
