<properties 
	pageTitle="Приступая к работе с мобильным приложением Javascript после добавления мобильных служб с помощью подключенных служб Visual Studio | Microsoft Azure" 
	description="Как начать использовать мобильные службы в проекте JavaScript в Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="patshea"/>

# Приступая к работе с мобильным приложением Javascript после добавления мобильных служб Azure с помощью подключенных служб Visual Studio

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-javascript-getting-started.md)
> - [What Happened](vs-mobile-services-javascript-what-happened.md)

Первое действие, необходимое для использования этих примеров кода, зависит от типа мобильной службы, к которой вы подключились.

 - Для серверной мобильной службы JavaScript создайте таблицу под названием TodoItem. Чтобы создать таблицу, найдите мобильную службу в узле Azure (в обозревателе сервера), щелкните узел этой службы правой кнопкой мыши, чтобы открыть контекстное меню, и выберите команду **Создать таблицу**. Введите имя таблицы TodoItem.

 - Если же вы подключились к серверной мобильной службе .NET, среда Visual Studio уже создала в шаблоне проекта по умолчанию таблицу TodoItem, но ее нужно опубликовать в Azure. Чтобы опубликовать таблицу, откройте контекстное меню проекта мобильной службы в обозревателе решений и выберите команду **Опубликовать веб-сайт**. Примите предлагаемые по умолчанию параметры и нажмите кнопку **Опубликовать**.

##Получение ссылки на таблицу

В проект уже добавлен объект клиента. Его имя аналогично имени мобильной службы с добавлением слова "Client". Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления данных таблицы.

	var todoTable = yourMobileServiceClient.getTable('TodoItem');

##Добавление записи 

Вставьте новый элемент в таблицу данных. Автоматически создается идентификатор (GUID строки типа) в качестве основного ключа для новой строки. Не изменяйте тип столбца идентификатора, так как он используется инфраструктурой мобильных служб.

    var todoTable = client.getTable('TodoItem');
    var todoItems = new WinJS.Binding.List();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            todoItems.push(item);
        });
    };

##Чтение таблицы и отправка запросов к ней

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

##Обновление записи

Обновите строку в таблице данных. В приведенном примере *todoItem* — это обновленный *item* (тот самый элемент, который возвращается из мобильной службы). При получении ответа мобильной службы, элемент обновляется в локальном списке todoItems с помощью метода [splice](http://msdn.microsoft.com/library/windows/apps/Hh700810.aspx). Вызовите метод **done** для возвращенного объекта [Promise](https://msdn.microsoft.com/library/dn802826.aspx), чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

        todoTable.update(todoItem).done(function (item) {
            todoItems.splice(todoItems.indexOf(item), 1, item);
        });

#####Удаление записи

Удалите строку из таблицы данных. Вызовите метод [done]() для возвращенного объекта [Promise](https://msdn.microsoft.com/library/dn802826.aspx), чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

	todoTable.del(todoItem).done(function (item) {
	    todoItems.splice(todoItems.indexOf(item), 1);
    }



[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=Oct15_HO3-->