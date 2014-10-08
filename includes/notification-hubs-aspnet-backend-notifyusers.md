## Создание проекта веб-интерфейса API

Первый шаг — это создание проекта веб-интерфейса API ASP.NET. Это сервер, который используется для проверки подлинности клиентов и создания уведомлений.

> [AZURE.NOTE] **Важно!** Перед тем как начать этот учебник, убедитесь, что у вас установлена последняя версия диспетчера пакетов NuGet. Чтобы проверить, запустите Visual Studio. В меню **Средства** щелкните пункт **Расширения и обновления**. Найдите **Диспетчер пакетов NuGet для Visual Studio 2013** и убедитесь, что у вас версия 2.8.50313.46 или более поздняя. Если это не так, переустановите диспетчер пакетов NuGet.
>
> ![][]

1.  Запустите Visual Studio с повышенным уровнем привилегий (от имени администратора).
2.  В Visual Studio или Visual Studio Express щелкните пункт **Файл**, затем **Создать**, **Проект**, откройте **Шаблоны**, **Visual C\#**, выберите **Веб-приложение** и **Веб-приложение ASP.NET**, введите имя **AppBackend** и нажмите кнопку **ОК**.

    ![][1]

3.  В диалоговом окне **Новый проект ASP.NET** выберите **Веб-интерфейс API** и нажмите кнопку **ОК**.

    ![][2]

4.  В диалоговом окне **Настройка сайта Azure** выберите подписку, регион и базу данных для этого проекта. Затем нажмите кнопку **ОК**, чтобы создать проект.

    ![][3]

5.  В обозревателе решений щелкните правой кнопкой мыши проект **AppBackend** и выберите **Управление пакетами NuGet**.

6.  В левой части окна выберите **В сети**.

7.  В поле **Поиск** введите **servicebus**.

8.  В списке результатов щелкните **Служебная шина Windows Azure**, а затем нажмите кнопку **Установить**. Завершите установку и закройте окно диспетчера пакетов NuGet.

    ![][4]

9.  В обозревателе решений щелкните правой кнопкой мыши папку **Модели**, нажмите кнопку **Добавить**, затем **Класс**. Назовите новый класс **Notifications.cs**. Щелкните кнопку **Добавить**, чтобы создать класс. В этом модуле представлены разные уведомления безопасности, которые будут отправлены. В полной реализации уведомления хранятся в базе данных. В этом случае для упрощения мы сохраняем их в память.

    ![][5]

10. Добавьте код в файл Notifications.cs, заменив определение класса `Notifications` на следующее:

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Добавьте следующий оператор `using` в верхнюю часть файла:

        using Microsoft.ServiceBus.Notifications;

12. В методе `Notifications()` замените два заполнителя в следующей строке кода на строку подключения (с полным доступом) для концентратора уведомлений и имени концентратора. Эти значения можно получить на [портале управления Azure][]:

        Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");

13. В обозревателе решений щелкните правой кнопкой мыши проект **AppBackend**, нажмите кнопку **Добавить**, затем **Класс**. Назовите новый класс **AuthenticationTestHandler.cs**. Щелкните кнопку **Добавить**, чтобы создать класс. Этот класс используется для проверки подлинности пользователе с помощью *Базовой проверки подлинности*. Обратите внимание, что приложение может использовать любую схему проверки подлинности.

14. Добавьте код в файл AuthenticationTestHandler.cs, заменив определение класса `AuthenticationTestHandler` на следующее:

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

    > [AZURE.NOTE] **Примечание о безопасности.** Класс `AuthenticationTestHandler` не обеспечивает настоящую проверку подлинности. Он используется лишь для имитации базовой проверки подлинности и возврата принципа. Имя пользователя необходимо для создания регистраций концентраторов уведомлений. Приведенная ранее реализация не является безопасной. В реальных приложениях и службах необходимо реализовать безопасный механизм проверки подлинности.

15. Добавьте следующие операторы `using` в верхнюю часть файла AuthenticationTestHandler.cs:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;               

16. Добавьте следующий код в конец метода `Register` в классе **App\_Start/WebApiConfig.cs**:

        config.MessageHandlers.Add(new AuthenticationTestHandler());

17. В обозревателе решений щелкните правой кнопкой мыши папку **Контроллеры**, нажмите кнопку **Добавить**, затем **Контроллер**. Щелкните элемент **Контроллер веб-интерфейса API 2 — пустой** и нажмите кнопку **Добавить**.

    ![][6]

18. Назовите новый класс **RegisterController** и снова щелкните **Добавить**, чтобы создать контроллер.

    ![][7]

19. Добавьте следующий код в определение класса `RegisterController`:

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

20. Добавьте следующий оператор `using` в верхнюю часть файла RegisterController.cs:

        using Microsoft.ServiceBus.Notifications;
        using SecurePush.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

21. Обратите внимание, что в этом коде мы добавляем тег пользователя для пользователя, проверка подлинности которого была выполнена обработчиком. Вы можете также добавить дополнительные проверки, чтобы убедиться, что у пользователя есть право регистрации запрошенных тегов.

22. В обозревателе решений щелкните правой кнопкой мыши папку **Контроллеры**, нажмите кнопку **Добавить**, затем **Контроллер**. Щелкните элемент **Контроллер веб-интерфейса API 2 — пустой** и нажмите кнопку **Добавить**. Назовите новый класс **NotificationsController** и снова щелкните **Добавить**, чтобы создать контроллер. Этот компонент предоставляет способ безопасного получения уведомления устройством, а также способ (в обучающих целях) активации безопасного push-уведомления для устройств пользователя. Обратите внимание, что при отправке уведомления в концентратор уведомлений мы отправляем только необработанное уведомление с идентификатором уведомления (без фактического сообщения).

23. Добавьте следующий код в определение класса **NotificationsController**:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);

        }

24. Добавьте следующие операторы `using` в верхнюю часть файла NotificationsController.cs:

        using SecurePush.Models;
        using System.Threading.Tasks;
        using System.Web;

25. Нажмите клавишу **F5**, чтобы запустить приложение и убедиться в правильности действий на данный момент. Приложение должно запустить браузер и отобразить домашнюю страницу ASP.NET.

26. После этого развернем это приложение на веб-сайте Azure, чтобы сделать его доступным для всех устройств. Щелкните правой кнопкой мыши проект **AppBackend** и нажмите кнопку **Опубликовать**.

27. Выберите в качестве цели публикации веб-сайт Azure.

    ![][8]

28. Войдите в учетную запись Azure и выберите существующий или новый веб-сайт.

    ![][9]

29. Запишите свойство **URL-адрес назначения** во вкладке **Подключение**. Далее в учебнике этот URL-адрес будет называться *конечная точка сервера*. Щелкните **Опубликовать**.

    ![][10]

  []: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
  [1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
  [2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
  [3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
  [4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
  [5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
  [портале управления Azure]: http://manage.windowsazure.com
  [6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
  [7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
  [8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
  [9]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
  [10]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
