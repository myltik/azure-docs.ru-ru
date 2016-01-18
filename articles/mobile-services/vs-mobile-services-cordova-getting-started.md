<properties
	pageTitle="Приступая к работе с проектом мобильных служб Cordova (подключенные службы Visual Studio) | Microsoft Azure"
	description="Описывает первые шаги, которые можно выполнить после подключения проекта Cordova к мобильным службам Azure с помощью подключенных служб Visual Studio."
	services="mobile-services"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="tarcher"/>

# Приступая к работе с мобильными службами (проекты Cordova)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

##Первые шаги
Первое действие, необходимое для использования этих примеров кода, зависит от типа мобильной службы, к которой вы подключились.

- Для серверной мобильной службы JavaScript создайте таблицу под названием TodoItem. Чтобы создать таблицу, найдите мобильную службу в узле Azure (в обозревателе сервера), щелкните узел этой службы правой кнопкой мыши, чтобы открыть контекстное меню, и выберите команду **Создать таблицу**. Введите имя таблицы TodoItem.

- Если у вас есть серверная мобильная служба .NET, то в среде Visual Studio уже создана в шаблоне проекта по умолчанию таблица TodoItem, но ее нужно опубликовать в Azure. Чтобы опубликовать таблицу, откройте контекстное меню проекта мобильной службы в обозревателе решений и выберите команду **Опубликовать веб-сайт**. Примите предлагаемые по умолчанию параметры и нажмите кнопку **Опубликовать**.

##Создание ссылки на таблицу

Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления таблицы данных. Таблица TodoItem создается автоматически при создании мобильной службы.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Чтобы эти примеры работали, необходимо установить уровень разрешений таблицы на **Кто угодно с ключом приложения**. Позднее можно настроить проверку подлинности. См. [Начало работы с проверкой подлинности](mobile-services-html-get-started-users.md).

##Добавление элемента в таблицу

Вставьте новый элемент в таблицу данных. Автоматически создается идентификатор (GUID строки типа) в качестве основного ключа для новой строки. Вызовите метод **done** для возвращенного объекта [Promise](https://msdn.microsoft.com/library/dn802826.aspx), чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    var insertTodoItem = function (todoItem) {
        todoTable.insert(todoItem).done(function (item) {
            items.push(item)
        });
    };

##Чтение таблицы и отправка к ней запросов

Следующий код выполняет запрос ко всем элементам таблицы, отсортированным по текстовому полю. Можно добавлять код для обработки результатов запроса в обработчике success. В этом случае обновляется локальный массив элементов.

    todoTable.orderBy('text')
        .read().done(function (results) {
            items = results.slice();
        });

Можно использовать метод where для изменения запроса. Вот пример кода, который отфильтровывает завершенные элементы.

    todoTable.where(function () {
            return (this.complete === false);
        })
        .read().done(function (results) {
            items = results.slice();
        });

Дополнительные примеры запросов см. в разделе, посвященному объекту [query](http://msdn.microsoft.com/library/azure/jj613353.aspx).

##Обновление элемента таблицы

Обновите какую-либо строку в таблице данных. Пример кода, когда при ответе мобильной службы из списка удаляется элемент. Вызовите метод **done** для возвращенного объекта [Promise](https://msdn.microsoft.com/library/dn802826.aspx), чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

##Удаление элемента таблицы

Для удаления строки из таблицы данных используется метод **del**. Вызовите метод **done** для возвращенного объекта [Promise](https://msdn.microsoft.com/library/dn802826.aspx), чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0107_2016-->