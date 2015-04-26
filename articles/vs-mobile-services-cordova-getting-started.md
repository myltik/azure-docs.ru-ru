<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Приступая к работе](vs-mobile-services-cordova-getting-started.md)
> - [Что произошло?](vs-mobile-services-cordova-what-happened.md)

## Приступая к работе с мобильными службами (проекты Cordova)

Первое действие, необходимое для использования этих примеров кода, зависит от типа мобильной службы, к которой вы подключились.

Для серверной мобильной службы JavaScript создайте таблицу под названием TodoItem.  Чтобы создать таблицу, найдите мобильную службу в узле Azure (в обозревателе сервера), щелкните узел этой службы правой кнопкой мыши, чтобы открыть контекстное меню, и выберите команду **Создать таблицу**. Введите имя таблицы TodoItem.

Если же у вас есть серверная мобильная служба .NET, то в среде Visual Studio уже создана в шаблоне проекта по умолчанию таблица TodoItem, но ее нужно опубликовать в Azure. Чтобы опубликовать таблицу, откройте контекстное меню проекта мобильной службы в обозревателе решений и выберите команду **Опубликовать веб-сайт**. Примите предлагаемые по умолчанию параметры и нажмите кнопку **Опубликовать**.
  
>[AZURE.NOTE]**Примените это [временное решение](http://go.microsoft.com/fwlink/?LinkId=518765) для работы с мобильными службами Azure в проектах Cordova, созданных с помощью предварительной версии Visual Studio 2015. Временное решение не требуется для проектов, в которых используются более поздние версии Visual Studio 2015.**

#####Получение ссылку на таблицу

Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления этой таблицы данных. Таблица TodoItem создается автоматически при создании мобильной службы.

	var todoTable = mobileServiceClient.getTable('TodoItem');

Чтобы эти примеры работали, необходимо установить уровень разрешений таблицы на **Кто угодно с ключом приложения**. Позднее можно настроить проверку подлинности. См. статью [Приступая к работе с проверкой подлинности](http://azure.microsoft.com/documentation/articles/mobile-services-html-get-started-users/).

#####Добавление записи 

Вставьте новый элемент в таблицу данных. Автоматически создается идентификатор (GUID строки типа) в качестве основного ключа для новой строки. Вызовите метод [done]() для возвращенного объекта [Promise](), чтобы получить копию вставленного объекта и обработать все ошибки.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

#####Чтение таблицы и запросы к ней 

Следующий код выполняет запрос ко всем элементам таблицы, отсортированным по текстовому полю. Можно добавлять код для обработки результатов запроса в обработчике success. В этом случае обновляется локальный массив элементов.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
            });
        });

Можно использовать метод where для изменения запроса. Вот пример кода, который отфильтровывает завершенные элементы.

	todoTable.where(function () {
                 return (this.complete === false);
              })
             .read().done(function (results) {
                items = results.slice();
             });

Дополнительные примеры запросов см. в разделе, [посвященном объекту query]((http://msdn.microsoft.com/library/azure/jj613353.aspx)).

#####Обновление записи

Обновите какую-либо строку в таблице данных. Пример кода, когда при ответе мобильной службы из списка удаляется элемент. Вызовите метод [done]() для возвращенного объекта [Promise](), чтобы получить копию вставленного объекта и обработать все ошибки.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

#####Удаление записи

Удалите строку в таблице данных с помощью метода **del**. Вызовите метод [done]() для возвращенного объекта [Promise](), чтобы получить копию вставленного объекта и обработать все ошибки.

	todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
	});

[Дополнительную информацию о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->
