<properties linkid="mobile-services-dotnet-backend-use-non-relational-data-store" urlDisplayName="Build a Service Using a Non-Relational Data Store" pageTitle="Build a Service Using a Non-Relational Data Store - Azure Mobile Services" metaKeywords="" description="Learn how to use a non-relational data store such as MongoDB or Azure Table Storage with your .NET based mobile service" metaCanonical="" services="" documentationCenter="Mobile" title="Build a Service Using a Non-Relational Data Store" authors="yavorg, mahender" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg, mahender"></tags>

# Построение службы с использованием MongoDB в качестве источника данных на внутреннем сервере .NET

В этом разделе показывается использование нереляционного хранилища данных для мобильной службы. В данном учебнике будет выполняться изменение проекта быстрого запуска мобильных служб для использования MongoDB в качестве источника данных.

Для настройки нереляционного хранилища в этом учебнике будут выполнены следующие действия.

1.  [Создание нереляционного хранилища][Создание нереляционного хранилища]
2.  [Изменение данных и контроллеров][Изменение данных и контроллеров]
3.  [Тестирование приложения][Тестирование приложения]

Для данного учебника требуется предварительное выполнение учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными].

## <a name="create-store"></a>Создание нереляционного хранилища

1.  На [портале управления Azure][портале управления Azure] нажмите **Создать** и выберите **Хранилище**.

2.  Выберите надстройку **MongoLab** и пройдите мастер для регистрации учетной записи. Дополнительные сведения о MongoDB см. на [странице надстройки MongoLab][странице надстройки MongoLab].

    ![][]

3.  После настройки учетной записи выберите пункт **Сведения о подключении** и скопируйте строку подключения.

4.  Перейдите в раздел мобильных служб и выберите вкладку **Настройка**.

5.  В разделе **параметров приложения** введите строку подключения с ключом "MongoConnectionString" и нажмите кнопку **Сохранить**.

    ![][1]

6.  В `TodoItemController` добавьте следующий код:

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

    Данный код будет загружать этот параметр приложения и сообщать мобильной службе, что его нужно воспринимать как подключение, которое может использоваться `TableController`. Позднее этот метод будет вызываться при вызове `TodoItemController`.

## <a name="modify-service"></a>Изменение данных и контроллеров

1.  Установите пакет NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2.  Измените `TodoItem`, чтобы он извлекался из `DocumentData` вместо `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3.  В `TodoItemController` замените метод `Initialize` следующим кодом:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4.  В коде для метода `Initialize`, приведенном выше, замените заполнитель **YOUR-DATABASE-NAME** именем, которое было выбрано при подготовке надстройки MongoLab.

## <a name="test-application"></a> Тестирование приложения

1.  Повторно опубликуйте внутренний проект мобильной службы.

2.  Запустите клиентское приложение. Обратите внимание, что не отображаются никакие элементы, сохраненные в базе данных SQL после выполнения учебника по началу работы.

3.  Создайте новый элемент. Приложение должно вести себя как и прежде, за исключением того, что теперь данные будут поступать в нереляционное хранилище.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Создание нереляционного хранилища]: #create-store
  [Изменение данных и контроллеров]: #modify-service
  [Тестирование приложения]: #test-application
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [портале управления Azure]: https://manage.windowsazure.com/
  [странице надстройки MongoLab]: /ru-ru/gallery/store/mongolab/mongolab
  []: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
  [1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png
