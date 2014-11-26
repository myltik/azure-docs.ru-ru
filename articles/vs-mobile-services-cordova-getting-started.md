<properties title="Приступая к работе с мобильными службами" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Приступая к работе][Приступая к работе]
> -   [Что произошло?][Что произошло?]

## Приступая к работе с мобильными службами (проекты Cordova)

Первое действие, необходимое для использования этих примеров кода, зависит от типа мобильной службы, к которой вы подключились.

Для серверной мобильной службы JavaScript создайте таблицу под названием TodoItem. Чтобы создать таблицу, найдите мобильную службу в узле Azure (в обозревателе сервера), щелкните узел этой службы правой кнопкой мыши, чтобы открыть контекстное меню, и выберите команду **Создать таблицу**. Введите имя таблицы TodoItem.

Если же вы подключились к серверной мобильной службе .NET, среда Visual Studio уже создала в шаблоне проекта по умолчанию таблицу TodoItem, но ее нужно опубликовать в Azure. Чтобы опубликовать таблицу, откройте контекстное меню проекта мобильной службы в обозревателе решений и выберите команду **Опубликовать веб-сайт**. Примите предлагаемые по умолчанию параметры и нажмите кнопку **Опубликовать**.

##### Получение ссылку на таблицу

Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления данных таблицы. Таблица TodoItem создается автоматически при создании мобильной службы.

    var todoTable = mobileServiceClient.getTable('TodoItem');

Чтобы эти примеры работали, необходимо установить уровень разрешений таблицы на **Кто угодно с ключом приложения**. Позднее можно настроить проверку подлинности. См. [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности].

##### Добавление записи

Вставьте новый элемент в таблицу данных. Автоматически создается идентификатор (GUID строки типа) в качестве основного ключа для новой строки. Вызовите метод [done][done] для возвращенного объекта [Promise][done], чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

    function TodoItem(text) {
        this.text = text;
        this.complete = false;
    }

    var items = new Array();
    todoTable.insert(todoItem).done(function (item) {
        items.push(item)
        });
    };

##### Чтение таблицы и запросы к ней

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

Дополнительные примеры запросов см. в разделе, посвященному объекту [query][query].

##### Обновление записи

Обновите строку в таблице данных. Пример кода, когда при ответе мобильной службы из списка удаляется элемент. Вызовите метод [done][done] для возвращенного объекта [Promise][done], чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

    todoTable.update(todoItem).done(function (item) {
        // Update a local collection of items.
        items.splice(items.indexOf(todoItem), 1, item);
    });

##### Удаление записи

Для удаления строки из таблицы данных используется метод **del**. Вызовите метод [done][done] для возвращенного объекта [Promise][done], чтобы получить копию вставленного объекта и обработать какие-либо ошибки.

    todoTable.del(todoItem).done(function (item) {
        items.splice(items.indexOf(todoItem), 1);
    });

[Дополнительные сведения о мобильных службах][Дополнительные сведения о мобильных службах]

  [Приступая к работе]: /documentation/articles/vs-mobile-services-cordova-getting-started/
  [Что произошло?]: /documentation/articles/vs-mobile-services-cordova-what-happened/
  [Начало работы с проверкой подлинности]: http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-html-get-started-users/
  [done]: 
  [query]: (http://msdn.microsoft.com/library/azure/jj613353.aspx)
  [Дополнительные сведения о мобильных службах]: http://azure.microsoft.com/documentation/services/mobile-services/
