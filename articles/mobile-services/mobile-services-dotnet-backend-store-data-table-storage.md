<properties 
	pageTitle="Построение серверной мобильной службы .NET, использующей табличное хранилище | Мобильные службы Azure" 
	description="Узнайте, как настроить таблицу Azure в серверной мобильной службе .NET." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/14/2015" 
	ms.author="glenga"/>

# Построение серверной мобильной службы .NET, использующей табличное хранилище

В этом разделе рассматривается использование нереляционного хранилища данных для серверной мобильной службы .NET. В этом учебнике вам предстоит изменить учебный проект мобильных служб Azure таким образом, чтобы в качестве хранилища данных вместо Базы данных SQL Azure (по умолчанию) использовалось табличное хранилище Azure.

Для данного учебника требуется предварительное выполнение учебника [Приступая к работе с мобильными службами]. Вам также потребуется учетная запись хранения Azure.

##Настройка таблицы Azure в серверной мобильной службе .NET

Прежде всего, необходимо настроить мобильную службу и серверный проект .NET для подключения к хранилищу Azure.

1. В **обозревателе решений** в Visual Studio щелкните серверный проект .NET правой кнопкой мыши и выберите **Управление пакетами NuGet**.

2. В левой области выберите категорию **Online**, выберите **Только стабильные**, найдите пункт **MobileServices**, щелкните **Установить** в пакете **Расширение хранилища Azure для серверных мобильных служб Microsoft Azure Mobile Services для .NET**, а затем примите лицензионные соглашения.

  	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

  	В результате в проект серверной мобильной службы .NET будет добавлена поддержка служб хранилища Azure.

3. Если вы еще не создали учетную запись хранения, см. раздел [Создание учетной записи хранения](../storage-create-storage-account.md)

4. В портале управления нажмите **Хранилище**, выберите учетную запись хранения, нажмите **Управление ключами**.

5. Запишите **имя учетной записи хранения** и **ключ доступа к хранилищу**.
 
6. В вашей мобильной службе выберите вкладку **Настройка**, найдите в ней раздел **Строка подключения** и введите новую строку подключения с **именем** `StorageConnectionString`. В качестве **значения** используйте строку подключения к учетной записи хранения в следующем формате.

		DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. В приведенной строке замените значения `<ACCOUNT_NAME>` и `<ACCESS_KEY>` значениями с портала, а затем щелкните **Сохранить**.

	Строка подключения к учетной записи хранения хранится в зашифрованном виде в параметрах приложения. Вы можете получить доступ к ней во время выполнения, используя любой контроллер таблицы.

8. В обозревателе решений Visual Studio откройте файл Web.config проекта мобильной службы и добавьте следующую строку подключения:

		<add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. Замените `<STORAGE_CONNECTION_STRING>` на строку подключения из шага 6.

	Мобильная служба будет использовать эту строку подключения при с запуске локального компьютера, что позволит проверить код перед его публикацией. При работе в Azure она использует строку подключения, заданную на портале, и игнорирует строку подключения в проекте.

## <a name="modify-service"></a>Изменение типов данных и контроллеров таблицы

Поскольку учебный проект TodoList по умолчанию настроен для работы с Базой данных SQL посредством Entity Framework, необходимо обновить его для работы с табличным хранилищем.

1. Измените тип данных **TodoItem**, чтобы он наследовался от **StorageData**, а не от **EntityData**, как показано ниже.

	    public class TodoItem : StorageData
	    {
	        public string Text { get; set; }
	        public bool Complete { get; set; }
	    }

	>[AZURE.NOTE]Тип **StorageData** имеет свойство Id (идентификатор), для которого требуется составной ключ (строка в формате *partitionId*,*rowValue*).

2. Добавьте в **TodoItemController** следующий оператор using:

		using System.Web.Http.OData.Query;
		using System.Collections.Generic;

3. Замените метод **Initialize** в **TodoItemController** следующим кодом:

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored 
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName, 
                tableName, Request, Services);          
        }

	Будет создан новый доменный диспетчер хранилища для запрашиваемого контроллера с помощью строки подключения к учетной записи хранения.

3. Замените метод **GetAllTodoItems** следующим кодом:

		public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        } 

	В отличие от Базы данных SQL эта версия не возвращает IQueryable<TEntity>, поэтому результат может быть связан с запросом, но не изменен в нем.

## Обновление клиентского приложения

Чтобы учебное приложение работало с серверной частью .NET с использованием табличного хранилища, необходимо сделать одно изменение на стороне клиента. Это связано с тем, что поставщик табличного хранилища ожидает сложный ключ.

1. Откройте файл с клиентским кодом, который содержит код доступа к данным, и найдите метод, выполняющий операцию вставки.

2. Обновите добавляемый экземпляр TodoItem таким образом, чтобы поле Id (идентификатор) явным образом задавалось в строковом формате `<partitionID>,<rowValue>`.

	Вот пример того, как этот идентификатор может выглядеть в приложении на C#, где часть partition является фиксированной, а часть row определяется на основе GUID.

		 todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

Теперь все готово для тестирования приложения.

## <a name="test-application"></a>Тестирование приложения

1. (Необязательно) Повторно опубликуйте проект серверной мобильной службы .NET. 
	
	Перед публикацией серверного проекта .NET в Azure вы также можете протестировать мобильную службу локально. Независимо от того, где выполняется тестирование (локально или в Azure), мобильная служба будет использовать табличное хранилище Azure.

4. Запустите учебное клиентское приложение, подключенное к вашей мобильной службе.

	Обратите внимание, что элементы, добавленные ранее в этом учебнике, не отображаются. Это связано с тем, что табличное хранилище пусто.

5. Добавьте новые элементы, чтобы сгенерировать изменения в базе данных.
 
	Поведение приложения и мобильной службы не должно измениться. Единственная разница заключается в том, что теперь данные сохраняются в нереляционном хранилище, а не в Базе данных SQL.

##Дальнейшие действия

Теперь, когда вы узнали, как легко использовать табличное хранилище с серверной частью .NET, попробуйте поэкспериментировать с другими серверными хранилищами:

+ [Соединение с локальным SQL Server с помощью гибридных подключений](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Гибридные подключения позволяют мобильной службе устанавливать защищенное соединение с локальными ресурс-контейнерами. Благодаря этому ваши локальные данные будут доступны мобильным клиентам, использующим Azure. К поддерживаемым активам относятся любые ресурсы, работающие на статическом TCP-порту, в том числе Microsoft SQL Server, MySQL, веб-API HTTP и большинство настраиваемых веб-служб.

+ [Загрузка изображений в хранилище Azure с помощью мобильных служб](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>В этой статье показано, как изменить пример проекта TodoList, чтобы добавить возможность передавать изображения из приложения в хранилище больших двоичных объектов Azure.

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab
 

<!---HONumber=Oct15_HO3-->