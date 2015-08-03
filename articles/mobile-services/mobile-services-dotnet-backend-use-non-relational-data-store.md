<properties 
	pageTitle="Создание службы, использующей нереляционное хранилище данных | Мобильные службы Azure" 
	description="Узнайте, как использовать хранилище нереляционных данных, например MongoDB или табличное хранилище Azure, с мобильной службой на основе .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="mahender"/>

# Построение серверной мобильной службы .NET, использующей MongoDB вместо Базы данных SQL

В этом разделе рассматривается использование нереляционного хранилища данных для серверной мобильной службы .NET. В данном учебнике вам предстоит изменить учебный проект мобильных служб таким образом, чтобы в качестве хранилища данных он использовал MongoDB вместо Базы данных SQL Azure (по умолчанию).

Предварительно вам необходимо пройти учебник [Приступая к работе с мобильными службами] или [Добавление мобильных служб в существующее приложение]. Также необходимо будет включить службу MongoLab в подписку.

## <a name="create-store"></a>Создание нереляционного хранилища MongoLab

1. На [портале управления Azure] нажмите **Создать** и выберите **Marketplace**.

2. Щелкните надстройку **MongoLab** и выполните все действия в мастере для регистрации учетной записи MongoLab.

	Дополнительные сведения о MongoDB см. на [странице надстройки MongoLab].

2. После настройки учетной записи щелкните **Сведения о подключении** и скопируйте строку подключения.

3. В вашей мобильной службе выберите вкладку **Настройка**, найдите в ней раздел **Строка подключения** и введите новую строку подключения с **именем** `MongoConnectionString`. В качестве **значения** используйте строку подключения к MongoDB. Затем щелкните **Сохранить**.

	![Добавление строки подключения к MongoDB](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	Строка подключения к учетной записи хранения хранится в зашифрованном виде в параметрах приложения. Вы можете получить доступ к ней во время выполнения, используя любой контроллер таблицы.

8. В обозревателе решений Visual Studio откройте файл Web.config проекта мобильной службы и добавьте следующую строку подключения:

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. Замените `<MONGODB_CONNECTION_STRING>` на строку подключения к MongoDB.

	Мобильная служба будет использовать эту строку подключения при с запуске локального компьютера, что позволит проверить код перед его публикацией. При работе в Azure она использует строку подключения, заданную на портале, и игнорирует строку подключения в проекте.

## <a name="modify-service"></a>Изменение типов данных и контроллеры таблицы

1. Установите пакет NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Измените тип данных **TodoItem**, чтобы он наследовался от **DocumentData**, а не от **EntityData**.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. В **TodoItemController** замените метод **Initialize** следующим кодом:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. В приведенном выше коде для метода **Initialize** замените `<YOUR-DATABASE-NAME>` на имя, которое было выбрано при подготовке надстройки MongoLab.

Теперь все готово для тестирования приложения.

## <a name="test-application"></a>Тестирование приложения

1. (Необязательно) Повторно опубликуйте проект серверной мобильной службы .NET.

	Перед публикацией серверного проекта .NET в Azure вы также можете протестировать мобильную службу локально. Независимо от того, где выполняется тестирование (локально или в Azure), мобильная служба будет использовать MongoDB в качестве хранилища.

4. Нажмите кнопку **Попробовать сейчас** на начальной странице или используйте клиентское приложение, подключенное к вашему мобильному приложению, чтобы выполнить запросы к элементам в базе данных.
 
	Обратите внимание, что не отображаются никакие элементы, сохраненные в базе данных SQL после выполнения учебника по началу работы.

	>[AZURE.NOTE]При вызове страниц API справки с помощью кнопки **Попробовать сейчас** не забудьте указать в качестве пароля (с пустым именем пользователя) свой ключ приложения.

3. Создайте новый элемент.

	Поведение приложения и мобильной службы не должно измениться. Единственная разница заключается в том, что теперь данные сохраняются в нереляционном хранилище, а не в Базе данных SQL.

##Дальнейшие действия

Теперь, когда вы узнали, как легко использовать табличное хранилище с серверной частью .NET, попробуйте поэкспериментировать с другими серверными хранилищами:

+ [Построение серверной мобильной службы .NET с использованием табличного хранилища вместо Базы данных SQL](mobile-services-dotnet-backend-store-data-table-storage.md)</br>Как и в данном учебнике, в этой статье показано, как использовать нереляционное хранилище данных для мобильной службы. В этом учебнике вам предстоит изменить учебный проект мобильных служб таким образом, чтобы в качестве хранилища данных вместо Базы данных SQL использовалось табличное хранилище Azure.
 
+ [Соединение с локальным SQL Server с помощью гибридных подключений](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Гибридные подключения позволяют мобильной службе устанавливать защищенное соединение с локальными ресурсами. Благодаря этому ваши локальные данные будут доступны мобильным клиентам, использующим Azure. К поддерживаемым активам относятся любые ресурсы, работающие на статическом TCP-порту, в том числе Microsoft SQL Server, MySQL, веб-API HTTP и большинство настраиваемых веб-служб.

+ [Загрузка изображений в хранилище Azure с помощью мобильных служб](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>В этой статье показано, как изменить учебный проект TodoList, чтобы добавить возможность передавать изображения из приложения в хранилище BLOB-объектов Azure.


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Добавление мобильных служб в существующее приложение]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[портале управления Azure]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[странице надстройки MongoLab]: /gallery/store/mongolab/mongolab
 

<!---HONumber=July15_HO4-->