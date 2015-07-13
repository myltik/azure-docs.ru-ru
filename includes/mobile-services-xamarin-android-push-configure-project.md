
1. В представлении решений разверните папку **Компоненты** папки в приложении Xamarin.Android и убедитесь, что установлен пакет мобильных служб Azure. 

2. Щелкните правой кнопкой мыши папку **Компоненты**, выберите пункт **Получить дополнительные компоненты...**, найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект.

1. Откройте файл проекта ToDoActivity.cs и добавьте в класс следующую инструкцию using:

		using Gcm.Client;

2. Добавьте в класс **ToDoActivity** следующий новый код:

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	Это позволит получить доступ к экземпляру клиента мобильных служб из процесса службы.

3. Измените существующее объявление клиента мобильных служб на public следующим образом:

		public MobileServiceClient client { get; private set; }

4.	После создания **MobileServiceClient** добавьте в метод **OnCreate** следующий код:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Ваш класс **ToDoActivity** теперь готов для добавления push-уведомлений.

<!---HONumber=62-->