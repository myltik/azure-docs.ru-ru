1. В представлении решения (или **обозревателе решений** в Visual Studio) щелкните правой кнопкой мыши **Компоненты**, выберите пункт **Получить дополнительные компоненты...**, найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект. 

2. Откройте файл проекта ToDoActivity.cs и добавьте в класс следующую инструкцию using:

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


4.	После создания **MobileServiceClient** добавьте в метод **OnCreate** следующий код:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Ваш класс **ToDoActivity** теперь готов для добавления push-уведомлений.

<!---HONumber=August15_HO8-->