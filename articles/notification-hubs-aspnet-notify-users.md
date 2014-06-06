<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="Уведомление пользователей" pageTitle="Уведомление пользователей службы ASP.NET с помощью концентраторов уведомлений" metaKeywords="" description="Следуйте инструкциям этого учебника, чтобы зарегистрироваться для получения уведомлений от вашей службы ASP.NET с помощью концентраторов уведомлений" metaCanonical="" services="notification-hubs" documentationCenter="" title="Уведомление пользователей с помощью концентраторов уведомлений" authors=""  solutions="" writer="glenga" manager="" editor=""  />

# Уведомление пользователей с помощью концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/manage/services/notification-hubs/notify-users" title="Мобильные службы">Мобильные службы</a><a href="/ru-ru/manage/services/notification-hubs/notify-users-aspnet" title="ASP.NET" class="current">ASP.NET</a>
</div> 

В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть веб-API ASP.NET используется для проверки подлинности клиентов и для создания уведомлений. Материал этого учебника основан на концентраторе уведомлений, созданном вами в предыдущем учебнике **Приступая к работе с концентраторами уведомлений**. Код регистрации уведомления перемещается из клиента в серверную службу. Это гарантирует выполнение регистрации только после успешной проверки подлинности клиента службой. Это также означает, что учетные данные концентратора уведомлений не распространяются вместе с клиентским приложением. Эта служба также управляет тегами, запрашиваемыми во время регистрации.

В этом учебнике рассматриваются следующие основные действия: 

+ [Создание приложения ASP.NET с проверкой подлинности]
+ [Обновление вашего приложения ASP.NET для регистрации уведомлений]
+ [Обновление приложения для выполнения входа и запроса регистрации]

## Предварительные требования

+ Visual Studio 2012. Можно также использовать Visual Studio Express 2012 for Web и Visual Studio Express 2012 for Windows 8 для создания приложения ASP.NET и приложения Магазина Windows, соответственно. 
+ Материал этого учебника основан на приложении и концентраторе уведомлений, созданных вами в учебнике **Приступая к работе с концентраторами уведомлений**.  Перед началом работы с учебником необходимо изучить этот учебник **Приступая к работе с концентраторами уведомлений** ([Магазин Windows C#][Get started Windows Store]/[iOS][Get started iOS]/[Android][Get started Android]). 

<div class="dev-callout"><b>Примечание.</b>
	<p>Создаваемый в этом учебнике проект веб-API ASP.NET выполняется на локальном компьютере. Можно также опубликовать проект веб-API ASP.NET в Azure. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/net/tutorials/rest-service-using-web-api/" target="_blank">Создание службы REST, адаптированной для мобильных устройств, с помощью веб-API ASP.NET и базы данных SQL</a>.</p>
</div>

<h2><a name="create-application"></a><span class="short-header">Создание приложения ASP.NET</span>Создание приложения ASP.NET с проверкой подлинности</h2>

Сначала создается приложение веб-API ASP.NET. Эта серверная служба будет проверять подлинность клиентов, осуществлять регистрацию для использования push-уведомлений от имени пользователя, прошедшего проверку, а также рассылать уведомления.

1. В Visual Studio или Visual Studio Express 2012 for Web щелкните элемент **Файл**, выберите **Новый проект в меню "Файл", разверните узел **Шаблоны**, **Visual C#**, выберите **Веб-сайт** и **Веб-приложение ASP.NET MVC 4**, введите имя _NotificationService_ и нажмите кнопку **ОК**.

	![][0]

2. В диалоговом окне **Новый проект ASP.NET MVC** щелкните **Пусто** и нажмите кнопку **ОК**.

	При этом создается проект ASP.NET MVC.

3. В обозревателе решений щелкните правой кнопкой мыши проект, выберите **Добавить**, **Класс**, введите `AuthenticationTestHandler` и нажмите кнопку **Добавить**.

	![][1] 

	При этом в проект добавляется файл кода для класса **AuthenticationTestHandler**.

4. Откройте новый файл проекта AuthenticationTestHandler.cs и замените определение класса на следующий код:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Security.Principal;
		using System.Text;
		using System.Web;
		
		namespace NotificationService
		{
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
		            } else return Unauthorised();
		
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
		} 

	<div class="dev-callout"><b>Примечание о безопасности</b>
		<p>Класс <strong>AuthenticationTestHandler</strong> не обеспечивает настоящую проверку подлинности. Он используется лишь для имитации базовой проверки подлинности и возврата принципа. Имя пользователя необходимо для создания регистраций концентраторов уведомлений. Приведенная выше реализация не является безопасной. В реальных приложениях и службах необходимо реализовать безопасный механизм проверки подлинности.</p>
	</div>

5. Разверните папку **App_Start**, откройте файл WebApiConfig.cs и добавьте следующую строку кода в конец метода **Register**:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

	Для этого требуется, чтобы запросы в приложение ASP.NET содержали заголовок "Authorization".

Теперь мы создали базовое приложение со схемой фиктивной проверки подлинности для получения имени пользователя. 

<h2><a name="register-notification"></a><span class="short-header">Регистрация для использования уведомлений</span>Обновление вашего приложения ASP.NET для регистрации уведомлений</h2>

Следующий шаг заключается в добавлении логической схемы регистрации для концентраторов уведомлений в приложение ASP.NET путем создания нового контроллера **Registration**. 

1. Войдите на [портал управления Azure][Management Portal], щелкните элемент **Service Bus**, свое пространство имен, элемент **Концентраторы уведомлений**, а затем выберите свой концентратор уведомлений и щелкните элемент **Сведения о подключении**.  

	![][6]

2. Запишите имя концентратора уведомлений и скопируйте строку подключения для **DefaultFullSharedAccessSignature**.

	![][7]

	Эта строка вместе с именем концентратора уведомлений понадобится как при регистрации для использования уведомлений, так и для их отправки.

3. В **обозревателе решений** щелкните правой кнопкой мыши имя проекта и выберите **Управление пакетами NuGet**.

4. В левой области выберите категорию **Интернет**, выполните поиск `WindowsAzure.ServiceBus`, щелкните пункт **Установить** в пакете **Azure Service Bus**, а затем примите условия лицензионного соглашения. 

  	![][2]

  	При этом в проект добавляется сборка Microsoft.ServiceBus.dll.

5. В обозревателе решений щелкните папку **Контроллеры** правой кнопкой мыши, выберите **Добавить**, **Контроллер...**, введите `RegisterController` в поле **Имя контроллера**, выберите **Пустой контроллер API** и нажмите кнопку **Добавить**.

	![][3]

	при этом в проект добавляется класс контроллера "controller". При вызове данный контроллер выполняет работу по регистрации устройства для концентраторов уведомлений.

6. Откройте файл нового проекта RegisterController.cs и добавьте следующие инструкции **using**:

		using Microsoft.ServiceBus.Notifications;
		using Newtonsoft.Json.Linq;
		using System.Threading.Tasks;
		using System.Web;

7. Добавьте следующий код в новый класс RegisterController: 

		// Define the Notification Hubs client.
		private NotificationHubClient hubClient;

		// Create the client in the constructor.
        public RegisterController()
        {
            var cn = "<FULL_SAS_CONNECTION_STRING>";
            hubClient = NotificationHubClient
				.CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
        }

8. Обновите код в конструкторе, чтобы заменить _`<NOTIFICATION_HUB_NAME>`_ и _`<FULL_SAS_CONNECTION_STRING>`_ на значения для своего концентратора уведомлений, а затем нажмите кнопку **Сохранить**.

	<div class="dev-callout"><b>Примечание.</b>
		<p>Обязательно используйте <strong>DefaultFullSharedAccessSignature</strong> для <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em>. Это утверждение позволяет веб-API создавать и обновлять регистрации.</p>
	</div>

9. Добавьте в класс RegisterController следующий код метода Post:

        public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
        {
            // Get the registration info that we need from the request. 
            var platform = registrationCall["platform"].ToString();
            var installationId = registrationCall["instId"].ToString();
            var channelUri = registrationCall["channelUri"] != null ? 
                registrationCall["channelUri"].ToString() : null;
            var deviceToken = registrationCall["deviceToken"] != null ? 
                registrationCall["deviceToken"].ToString() : null;       
            var userName = HttpContext.Current.User.Identity.Name;

            // Get registrations for the current installation ID.
            var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

            bool updated = false;
            bool firstRegistration = true;
            RegistrationDescription registration = null;

            // Check for existing registrations.
            foreach (var registrationDescription in regsForInstId)
            {
                if (firstRegistration)
                {
 					// Update the tags.
                    registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                    // We need to handle each platform separately.
                    switch (platform)
                    {
                        case "windows":
                            var winReg = registrationDescription as WindowsRegistrationDescription;
                            winReg.ChannelUri = new Uri(channelUri);
                            registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                            break;
                        case "ios":
                            var iosReg = registrationDescription as AppleRegistrationDescription;
                            iosReg.DeviceToken = deviceToken;
                            registration = await hubClient.UpdateRegistrationAsync(iosReg);
                            break;
                    }
                    updated = true;
                    firstRegistration = false;
                }
                else
                {
                    // We shouldn't have any extra registrations; delete if we do.
                    await hubClient.DeleteRegistrationAsync(registrationDescription);
                }
            }

            // Create a new registration.
            if (!updated)
            {
                switch (platform)
                {
                    case "windows":
                        registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                            new string[] { installationId, userName });
                        break;
                    case "ios":
                        registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                            new string[] { installationId, userName });
                        break;
                }
            }

            // Send out a test notification.
            sendNotification(string.Format("Test notification for {0}", userName), userName);

            return registration;
        }

	Этот код вызывается POST-запросом и возвращает информацию о платформе и deviceID из тела сообщения. Эти данные вместе с идентификатором установки из заголовка запроса и идентификатором вошедшего в систему пользователя используется для обновления существующей регистрации. Если регистрация отсутствует, создается новая регистрация. Эта регистрация помечается с использованием идентификатора пользователя и идентификатора установки.

10. Добавьте следующий метод sendNotification:

        // Basic implementation that sends a not ification to Windows Store and iOS app clients.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Create notifications for both Windows Store and iOS platforms.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Send a notification to the logged-in user on both platforms.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // This is expected when an APNS registration doesn't exist.
                Console.WriteLine(ex.Message);
            }
        }

	Этот метод вызывается для отправки уведомления сразу же после завершения регистрации.

Далее мы обновим клиентское приложение, созданное вами при прохождении учебника **Приступая к работе с концентраторами уведомлений**. 

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для выполнения входа и запроса регистрации</h2>

Приложению, созданному после завершения учебника **Приступая к работе с концентраторами уведомлений**, необходима регистрация непосредственно из концентратора уведомлений. Мы удалим этот код регистрации из клиентского приложения и заменим его на вызов нового API регистрации в приложении веб-API ASP.NET.

1. Нажмите клавишу F5 для запуска приложения ASP.NET и попытайтесь загрузить страницу по умолчанию. 

	При отображении обозревателя запишите имя узла запрошенного сайта. Этот корневой URL-адрес потребуется при обновлении клиентского приложения.

	<div class="dev-callout"><b>Примечание.</b>
		<p>При использовании локального веб-сервера служб IIS или сервера разработки Visual Studio необходимо также указать номер порта. Обратите внимание на появление ошибки 404 в связи с тем, что мы не реализовали в этом приложении страницу по умолчанию.</p>
	</div>

2. Выполните следующие действия в одной из следующих версий процедуры **Регистрация текущего пользователя для push-уведомлений с помощью веб-API ASP.NET** в зависимости от используемой клиентской платформы:

	+ [Версия C# Магазина Windows][Client topic Windows Store C# version]
	+ [Версия iOS][Client topic iOS version]

3. Запустите обновленное приложение, войдите в службу, используя одну и ту же строку как для имени пользователя, так и для пароля, и затем убедитесь, что отображается назначенный уведомлению идентификатор регистрации.

	Вы также получите push-уведомление.

	<div class="dev-callout"><b>Примечание.</b>
		<p>Если регистрация для платформы, куда требуется отправить уведомление, отсутствует, на сервере возникает ошибка. В этом случае такую ошибку можно игнорировать. Чтобы узнать, как предотвратить такую ситуацию с помощью шаблонов, см. раздел <a href="/ru-ru/manage/services/notification-hubs/notify-users-xplat-aspnet" target="_blank">Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений</a>.</p>
	</div>

4. (Необязательно) Разверните клиентское приложение на втором устройстве, а затем запустите это приложение и вставьте текст. 

	На каждом из устройств отображается уведомление.

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы завершили работу с данным учебником, рекомендуется ознакомиться со следующими учебниками:

+ **Использование концентраторов уведомлений для передачи экстренных новостей ([Магазин Windows C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>В этом учебнике, ориентированном на конкретную платформу, показано, как с помощью тегов предоставить пользователям возможность для подписки на интересующие их типы уведомлений. 

+ **[Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений]**<br/>Этот учебник представляет собой дополнение к текущему учебнику **Уведомление пользователей с помощью концентраторов уведомлений** и описывает, как можно применять ориентированные на платформу шаблоны в целях регистрации для использования уведомлений. Это позволяет отправлять уведомления из одного метода в серверный код.

Дополнительные сведения о концентраторах уведомлений см. в разделе [Концентраторы уведомлений Azure].

<!-- Anchors. -->
[Создание приложения ASP.NET с проверкой подлинности]: #create-application
[Обновление вашего приложения ASP.NET для регистрации уведомлений]: #register-notification
[Обновление приложения для выполнения входа и запроса регистрации]: #update-app
[Обновление вашего приложения ASP.NET отправки уведомлений]: #send-notifications

<!-- Images. -->
[0]: ./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png
[1]: ./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png
[2]: ./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png
[3]: ./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png
[6]: ./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png
[7]: ./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png


<!-- URLs. -->
[Приступая к работе с Магазином Windows]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet
[Приступая к работе с iOS]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios
[Приступая к работе с Android]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-android
[Приступая к работе с проверкой подлинности для Магазина Windows]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Приступая к работе с проверкой подлинности для iOS]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios/
[Приступая к работе с проверкой подлинности для Android]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android/
[Клиент для версии C# Магазина Windows]: /ru-ru/manage/services/notification-hubs/register-users-aspnet-dotnet 
[Клиент для версии iOS]: /ru-ru/manage/services/notification-hubs/howto-register-user-with-aspnet-ios 
[Visual Studio 2012 Express для Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Портал управления]: https://manage.windowsazure.com/

[Создание службы REST, адаптированной для мобильных устройств, с помощью веб-API ASP.NET и базы данных SQL]: /ru-ru/develop/net/tutorials/rest-service-using-web-api/
[Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-aspnet
[Экстренные новости в .NET]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[Экстренные новости в iOS]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[Концентраторы уведомлений Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927170.aspx

