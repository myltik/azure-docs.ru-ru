<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Приступая к работе](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [Что произошло?](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

## Приступая к работе с мобильными службами (проекты .NET)

Первое действие, необходимое для использования этих примеров кода, зависит от типа мобильной службы, к которой вы подключились.

Для серверной мобильной службы JavaScript создайте таблицу под названием TodoItem.  Чтобы создать таблицу, найдите мобильную службу в узле Azure (в обозревателе сервера), щелкните узел этой службы правой кнопкой мыши, чтобы открыть контекстное меню, и выберите команду **Создать таблицу**. Введите имя таблицы TodoItem.

Если же у вас есть серверная мобильная служба .NET, то среда Visual Studio уже создала в шаблоне проекта по умолчанию таблицу TodoItem, но ее нужно опубликовать в Azure. Чтобы опубликовать таблицу, откройте контекстное меню проекта мобильной службы в обозревателе решений и выберите команду **Опубликовать веб-сайт**. Примите предлагаемые по умолчанию параметры и нажмите кнопку **Опубликовать**.

#####Получение ссылку на таблицу

Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления этой таблицы данных. Вам потребуется класс TodoItem с атрибутами, настроенными на интерпретацию JSON, который мобильная служба отправляет в ответ на ваши запросы.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Чтобы эти примеры работали, необходимо установить для таблицы разрешение **Кто угодно с ключом приложения**. Если изменить разрешения для повышения безопасности мобильного устройства, необходимо будет добавить поддержку проверки подлинности пользователей. См. статью [Начало работы с проверкой подлинности](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/).

#####Добавление записи 

Вставьте новый элемент в таблицу данных.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

#####Чтение таблицы и запросы к ней 

Следующий код позволяет выполнить запрос ко всем элементам таблицы. Обратите внимание, возвращается только первая страница данных, которая по умолчанию содержит 50 элементов. Вы можете передать любой размер страницы, так как это дополнительный параметр.

    List<TodoItem> items;
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToListAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }


#####Обновление записи

Обновите какую-либо строку в таблице данных. Этот элемент параметра является обновляемым объектом TodoItem.

	await todoTable.UpdateAsync(item);

#####Удаление записи

Удалите какую-либо строку из базы данных. Этот элемент параметра является удаляемым объектом TodoItem.

	await todoTable.DeleteAsync(item);


[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)
