
1. В представлении решения (или **обозревателе решений** в Visual Studio) щелкните правой кнопкой мыши **Компоненты**, выберите пункт **Получить больше компонентов...**, найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект.
2. Откройте файл проекта ToDoActivity.cs и добавьте в класс следующую инструкцию using:
   
        using Gcm.Client;
3. Добавьте в класс **ToDoActivity** следующий новый код: 
   
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
   
    Это позволяет получать доступ к экземпляру мобильного клиента, используя процесс службы обработчика push-действий.
4. После создания **MobileServiceClient** добавьте в метод **OnCreate** следующий код:
   
     // Задание текущего экземпляра TodoActivity.
     instance = this;
   
     // Проверка правильности настройки клиента GCM.
     GcmClient.CheckDevice(this); GcmClient.CheckManifest(this);
   
     // Регистрация приложения для работы с push-уведомлениями.
     GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Ваш класс **ToDoActivity** теперь готов для добавления push-уведомлений.

