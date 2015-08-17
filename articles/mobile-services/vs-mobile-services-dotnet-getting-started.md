<properties 
	pageTitle="" 
	description="Как приступить к работе с мобильными службами Azure в проекте Visual Studio .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/22/2015" 
	ms.author="patshea123"/>

# Приступая к работе с мобильными службами (проекты .NET)

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

Первое действие, необходимое для использования этих примеров кода, зависит от типа мобильной службы, к которой вы подключились.

- Для серверной мобильной службы JavaScript создайте таблицу под названием TodoItem. Чтобы создать таблицу, найдите мобильную службу в узле Azure (в обозревателе сервера), щелкните узел этой службы правой кнопкой мыши, чтобы открыть контекстное меню, и выберите команду **Создать таблицу**. Введите имя таблицы TodoItem.

- Если у вас есть серверная мобильная служба .NET, то в среде Visual Studio уже создана в шаблоне проекта по умолчанию таблица TodoItem, но ее нужно опубликовать в Azure. Чтобы опубликовать таблицу, откройте контекстное меню проекта мобильной службы в обозревателе решений и выберите команду **Опубликовать веб-сайт**. Примите предлагаемые по умолчанию параметры и нажмите кнопку **Опубликовать**.

#####Получение ссылки на таблицу

Приведенный ниже код создает ссылку на таблицу (`todoTable`), содержащую данные для TodoItemem, которые можно использовать в последующих операциях для чтения и обновления таблицы данных. Вам потребуется класс TodoItem с атрибутами, настроенными на интерпретацию JSON, который мобильная служба отправляет в ответ на ваши запросы.

	public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

	IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Чтобы эти примеры работали, необходимо установить уровень разрешений таблицы на **Кто угодно с ключом приложения**. Если изменить разрешения для повышения безопасности мобильного устройства, необходимо будет добавить поддержку проверки подлинности пользователей. См. [Начало работы с проверкой подлинности](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

#####Добавление элемента таблицы 

Вставьте новый элемент в таблицу данных.

	TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
	await todoTable.InsertAsync(todoItem);

#####Чтение таблицы и отправка к ней запросов 

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


#####Обновление элемента таблицы

Обновите строку в таблице данных. Этот элемент параметра является обновляемым объектом TodoItem.

	await todoTable.UpdateAsync(item);

#####Удаление элемента таблицы

Удалите какую-либо строку из базы данных. Элемент параметра является удаляемым объектом TodoItem.

	await todoTable.DeleteAsync(item);


[Дополнительные сведения о мобильных службах](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=August15_HO6-->