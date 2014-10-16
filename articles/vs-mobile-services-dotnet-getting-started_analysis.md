<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### Что произошло?

###### Добавлены ссылки

В проект добавлен пакет NuGet мобильных служб Azure. В результате в проект добавлены следующие ссылки на .NET: Microsoft.WindowsAzure.Mobile, Microsoft.WindowsAzure.Mobile.Ext, Newtonsoft.Json, System.Net.Http.Extensions, System.Net.Http.Primitives

###### Значения строк подключения для мобильных служб

В файле App.xaml.cs был создан объект MobileServiceClient, содержащий ключ и URL-адрес выбранного приложения мобильной службы.

### Приступая к работе с мобильными службами

###### Получение ссылку на таблицу

Следующий код получает ссылку на таблицу, содержащую данные для TodoItem, которые можно использовать в последующих операциях чтения и обновления данных таблицы. Необходим класс TodoItem с атрибутами, настроенными на интерпретацию JSON, который мобильный службы отправляют в ответ на запросы.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

    IMobileServiceTable<TodoItem> todoTable = App.<yourClient>.GetTable<TodoItem>();

Чтобы эти примеры работали, необходимо установить уровень разрешений таблицы на **Кто угодно с ключом приложения**. Если изменить разрешения для повышения безопасности мобильного устройства, необходимо будет добавить поддержку проверки подлинности пользователей. См. [Начало работы с проверкой подлинности][Начало работы с проверкой подлинности].

###### Добавление записи

Вставьте новый элемент в таблицу данных.

    TodoItem todoItem = new TodoItem() { Text = "My first to do item", Complete = false };
    await todoTable.InsertAsync(todoItem);

###### Чтение таблицы и запросы к ней

Следующий код позволяет выполнить запрос ко всем элементам таблицы. Обратите внимание, возвращается только первая страница данных, которая по умолчанию содержит 50 элементов. Можно указать необходимый размер, так как это дополнительный параметр.

    List<TodoItem> items = await todoTable.ToListAsync();
    try
    {
        // Query that returns all items.   
        items = await todoTable.ToCollectionAsync();             
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // handle exception
    }

###### Обновление записи

Обновите строку в таблице данных. Элемент параметра является обновляемым объектом TodoItem.

    await todoTable.UpdateAsync(item);

###### Удаление записи

Удалите строку из базы данных. Элемент параметра является удаляемым объектом TodoItem.

    await todoTable.DeleteAsync(item);

  [Начало работы с проверкой подлинности]: http://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
