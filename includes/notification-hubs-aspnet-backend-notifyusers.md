## Создание проекта веб-интерфейса API

Выполните следующие шаги для создания новой серверной части ASP.NET WebAPI для проверки подлинности клиентов и формирования уведомлений или изменения существующей серверной части из предыдущих проектов или из учебника [Отправка push-уведомлений пользователям, прошедшим проверку подлинности](http://azure.microsoft.com/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/).

> [AZURE.NOTE] **Важно!** Перед тем как начать работу с этим учебником, убедитесь, что у вас установлена последняя версия диспетчера пакетов NuGet. Чтобы проверить, запустите Visual Studio. В меню **Средства** щелкните пункт **Расширения и обновления**. Найдите **Диспетчер пакетов NuGet для Visual Studio 2013** и убедитесь, что у вас версия 2.8.50313.46 или более поздняя. Если это не так, переустановите диспетчер пакетов NuGet.
> 
> ![][4]

> [AZURE.NOTE] Убедитесь, что вы установили Visual Studio [SDK Azure](http://azure.microsoft.com/ru-ru/downloads/) для развертывания веб-сайта.

1. Запустите Visual Studio или Visual Studio Express.
2. В Visual Studio нажмите **Файл**, затем нажмите **Создать**, затем **Проект**, разверните **Шаблоны**, **Visual C#**, затем нажмите **Web** и **Веб-приложение ASP.NET**, введите имя **AppBackend** и затем нажмите **OK**. 
	
	![][1]

3. В диалоговом окне **Новый проект ASP.NET** нажмите **Web API**, затем нажмите **OK**.

	![][2]

4. В диалоговом окне **Настройка сайта Azure** выберите подписку, регион и базу данных для этого проекта. Затем нажмите кнопку **ОК**, чтобы создать проект.

	![][5]

5. В обозревателе решений нажмите правой кнопкой проект **AppBackend**, затем нажмите **Управление пакетами NuGet**.

6. С левой стороны нажмите **В сети** и найдите пакет **servicebus**, задав это значение в поле **Поиск**.

7. В списке результатов нажмите **Служебная шина Windows Azure**, а затем нажмите кнопку **Установить**. Завершите установку и закройте окно диспетчера пакетов NuGet.

	![][14]

8. Теперь мы создадим новый класс **Notifications.cs**. Перейдите в обозреватель решений, нажмите правой кнопкой папку **Модели**, выберите **Добавить**, затем **Класс**. После присвоения имени новому классу **Notifications.cs** нажмите кнопку **Добавить**, чтобы сформировать класс. В этом модуле представлены разные уведомления безопасности, которые будут отправлены. В полной реализации уведомления хранятся в базе данных. Для упрощения пример из этого учебника сохраняет их в памяти.

	![][6]

9. В Notifications.cs добавьте следующую инструкцию using в начало файла:

        using Microsoft.ServiceBus.Notifications;

10. Затем замените определение класса Notifications на следующее и замените два заполнителя на строку соединения (с полным доступом) для вашего концентратора уведомлений и имя концентратора (доступно на [портале управления Azure](http://manage.windowsazure.com)):

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Затем мы создадим новый класс **AuthenticationTestHandler.cs**. В обозревателе решений нажмите правой кнопкой проект **AppBackend**, выберите **Добавить**, затем нажмите **Класс**. Дайте имя новому классу **AuthenticationTestHandler.cs** и нажмите кнопку **Добавить**, чтобы сформировать класс. Этот класс используется для проверки подлинности пользователей с помощью метода Basic Authentication. Обратите внимание, что ваше приложение может использовать любую схему проверки подлинности.

12. В AuthenticationTestHandler.cs добавьте следующие инструкции using:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. В AuthenticationTestHandler.cs замените определение класса AuthenticationTestHandler на следующее:

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Примечание о безопасности**. Класс AuthenticationTestHandler не обеспечивает истинную проверку подлинности. Этот класс используется лишь для имитации базовой проверки подлинности и не является безопасным. В реальных приложениях и службах необходимо реализовать безопасный механизм проверки подлинности.				

14. Добавьте следующий код в конец метода Register в классе **App_Start/WebApiConfig.cs**:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. Далее мы создадим новый контроллер **RegisterController**. В обозревателе решений щелкните правой кнопкой мыши папку **Контроллеры**, нажмите кнопку **Добавить**, затем **Контроллер**. Щелкните элемент **Контроллер веб-интерфейса API 2 - пустой** и нажмите кнопку **Добавить**. Назовите новый класс **RegisterController** и снова нажмите кнопку **Добавить**, чтобы сформировать класс.

	![][7]

	![][8]

16. В RegiterController.cs добавьте следующие инструкции using:

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. Добавьте следующий код в определение класса RegisterController. Обратите внимание, что в этом коде мы добавляем тег пользователя для пользователя, проверка подлинности которого была выполнена обработчиком. Вы можете также добавить дополнительные проверки, чтобы убедиться, что у пользователя есть право регистрации запрошенных тегов.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. Создайте новый контроллер **NotificationsController**, как мы создавали **RegisterController**. Этот компонент предоставляет возможность устройству безопасно получить уведомление и позволяет пользователю инициировать на устройствах безопасную операцию push. Обратите внимание, что при отправке уведомления в концентратор уведомлений мы отправляем только необработанное уведомление с идентификатором уведомления (без фактического сообщения).

19. В NotificationsController.cs добавьте следующие инструкции using:

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. Добавьте следующий код в определение класса **NotificationsController** и убедитесь, что фрагменты для тех платформ, с которыми вы не работаете, закомментированы.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;


            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);


            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. Нажмите клавишу **F5**, чтобы запустить приложение и убедиться в правильности своих проведенных действий. Приложение должно запустить браузер и отобразить домашнюю страницу ASP.NET. 

22. После этого развернем это приложение на веб-сайте Azure, чтобы сделать его доступным для всех устройств. Щелкните правой кнопкой мыши проект **AppBackend** и нажмите кнопку **Опубликовать**.

23. Выберите в качестве цели публикации веб-сайт Azure.

    ![][B15]

24. Войдите в учетную запись Azure и выберите существующий или новый веб-сайт.

    ![][B16]

25. Запишите значение свойства **URL-адрес назначения** во вкладке **Подключение**. Далее в учебнике этот URL-адрес будет называться *внутренняя конечная точка* (backend endpoint). Щелкните **Опубликовать**.

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
