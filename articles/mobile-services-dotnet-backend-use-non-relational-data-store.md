<properties 
	pageTitle="Создание службы с помощью нереляционного хранилища данных — мобильных служб Azure" 
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

# Построение службы с использованием MongoDB в качестве источника данных на внутреннем сервере .NET

В этом разделе показывается использование нереляционного хранилища данных для мобильной службы. В данном учебнике будет выполняться изменение проекта быстрого запуска мобильных служб для использования MongoDB в качестве источника данных.

Для настройки нереляционного хранилища в этом учебнике будут выполнены следующие действия.

1. [Создание нереляционного хранилища]
2. [Изменение данных и контроллеров]
3. [Тестирование приложения]

Для данного учебника требуется предварительное выполнение учебника [Приступая к работе с мобильными службами] или [Приступая к работе с данными].

## <a name="create-store"></a>Создание нереляционного хранилища

1. На [портале управления Azure] нажмите **Создать** и выберите **Хранилище**.

2. Выберите надстройку **MongoLab** и пройдите мастер для регистрации учетной записи. Дополнительные сведения о MongoDB см. на [странице надстройки MongoLab].

    ![][0]

2. После настройки учетной записи выберите пункт **Сведения о подключении** и скопируйте строку подключения.

3. Перейдите в раздел мобильных служб и выберите вкладку **Настройка**.

4. В разделе **параметров приложения** введите строку подключения с ключом "MongoConnectionString" и нажмите кнопку **Сохранить**.

    ![][1]

2. В `TodoItemController` добавьте следующий код:

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    Данный код будет загружать параметр приложения и сообщать мобильной службе, что его нужно воспринимать как подключение, которое может использоваться `TableController`. Позднее этот метод будет использоваться при вызове `TodoItemController`.



## <a name="modify-service"></a>Изменение данных и контроллеров

1. Установите пакет NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Измените `TodoItem`, чтобы он извлекался из `DocumentData` вместо `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. В `TodoItemController` замените метод `Initialize` следующим кодом:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. В коде для метода `Initialize`, приведенном выше, замените заполнитель **YOUR-DATABASE-NAME** именем, выбранным при подготовке надстройки MongoLab к работе.


## <a name="test-application"></a>Тестирование приложения

1. Повторно опубликуйте внутренний проект мобильной службы.

2. Запустите клиентское приложение. Обратите внимание, что не отображаются никакие элементы, сохраненные в базе данных SQL после выполнения учебника по началу работы.

3. Создайте новый элемент. Приложение должно вести себя как и прежде, за исключением того, что теперь данные будут поступать в нереляционное хранилище.


<!-- Anchors. -->
[Создание нереляционного хранилища]: #create-store
[Изменение данных и контроллеров]: #modify-service
[Тестирование приложения]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Приступая к работе с мобильными службами]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Приступая к работе с данными]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[портале управления Azure]: https://manage.windowsazure.com/
[What is the Table Service]: storage/storage-dotnet-how-to-use-tables.md#what-is
[странице надстройки MongoLab]: /gallery/store/mongolab/mongolab

<!--HONumber=54-->