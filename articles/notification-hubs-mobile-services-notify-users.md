<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Уведомление пользователей" pageTitle="Уведомление пользователей мобильной службы с помощью концентраторов уведомлений" metaKeywords="" description="Пройдите данный учебник, чтобы зарегистрироваться для получения уведомлений от вашей мобильной службы с помощью концентраторов уведомлений" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Уведомление пользователей с помощью концентраторов уведомлений" authors=""  solutions="" writer="glenga" manager="" editor=""  />
# <a name="getting-started"> </a>Уведомление пользователей с помощью концентраторов уведомлений

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/manage/services/notification-hubs/notify-users" title="Мобильные службы" class="current">Мобильные службы</a><a href="/ru-ru/manage/services/notification-hubs/notify-users-aspnet" title="ASP.NET">ASP.NET</a>
</div> 

В этом учебнике показано, как использовать концентраторы уведомлений Azure для отправки push-уведомлений пользователю определенного приложения на конкретном устройстве. Серверная часть мобильных служб Azure используется для проверки подлинности клиентов и для создания уведомлений. Материал этого учебника основан на концентраторе уведомлений, созданном вами в предыдущем учебнике **Приступая к работе с концентраторами уведомлений**. Код регистрации уведомления перемещается из клиента в серверную службу. Это гарантирует выполнение регистрации только после успешной проверки подлинности клиента службой. Это также означает, что учетные данные концентратора уведомлений не распространяются вместе с клиентским приложением. Эта служба также управляет тегами, запрашиваемыми во время регистрации.

В этом учебнике рассматриваются следующие основные действия: 

+ [Обновление вашей мобильной службы для регистрации уведомлений]
+ [Обновление приложения для выполнения входа и запроса регистрации]
+ [Обновление вашей мобильной службы для отправки уведомлений]

## Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники:

+ **Приступая к работе с концентраторами уведомлений** ([Магазин Windows C#][Get started Windows Store]/[iOS][Get started iOS]/[Android][Get started Android]). 

+ **Приступая к работе с проверкой подлинности в мобильных службах** ([Магазин Windows C#][Get started auth Windows Store]/[iOS][Get started auth iOS]/[Android][Get started auth Android])

Материал этого учебника основан на приложении и концентраторе уведомлений, созданных вами в учебнике **Приступая к работе с концентраторами уведомлений**. В нем также используется проверяемая мобильная служба, настроенная в учебнике **Приступая к работе с проверкой подлинности в мобильных службах**. 

<div class="dev-callout"><b>Примечание.</b>
	<p>По умолчанию в учебнике <strong>Приступая к работе с проверкой подлинности в мобильных службах</strong> используется проверка подлинности в Facebook. В этом учебнике нельзя использовать проверку подлинности учетной записи Майкрософт, поскольку два приложения Магазина Windows не могут совместно использовать одну регистрацию Live Connect. Чтобы использовать проверку подлинности учетной записи Майкрософт, мобильная служба и концентратор уведомлений должны быть зарегистрированы с использованием одного приложения в Live Connect.</p>
</div>

<h2><a name="register-notification"></a><span class="short-header">Регистрация для использования уведомлений</span>Обновление вашей мобильной службы для регистрации уведомлений</h2>

Поскольку регистрация уведомлений должна осуществляться только после успешной проверки подлинности клиента службой, для выполнения регистрации служит настраиваемый интерфейс API, определенный в мобильной службе. Этот настраиваемый интерфейс API вызывается проверенным клиентом для запроса регистрации уведомления. В этом разделе вам предстоит обновить проверяемую мобильную службу, определенную в учебнике **Приступая к работе с проверкой подлинности в мобильных службах**. 

1. Войдите на [портал управления Azure][Management Portal], щелкните элемент **Service Bus**, свое пространство имен, элемент **Концентраторы уведомлений**, а затем выберите свой концентратор уведомлений и щелкните элемент **Сведения о подключении**.  

	![][6]

2. Запишите имя концентратора уведомлений и скопируйте строку подключения для **DefaultFullSharedAccessSignature**.

	![][7]

	Эта строка вместе с именем концентратора уведомлений понадобится как при регистрации для использования уведомлений, так и для их отправки.

2. На портале управления щелкните элемент **Мобильные службы** и затем выберите свое приложение.

   	![][0]

2. Откройте вкладку **API**, а затем щелкните элемент **Создать настраиваемый API**.

   	![][1]

   	Откроется диалоговое окно **Создать настраиваемый API**.

3. Введите <em>register_notifications</em> в поле **Имя API**, выберите значение **Только прошедшие проверку пользователи** для параметра **Разрешения POST**, а затем нажмите кнопку проверки.

   	![][2]

  	При этом создается API, который требует от пользователей проходить проверку подлинности перед вызовом с помощью метода HTTP POST. Задать другие методы HTTP не нужно, поскольку мы их не реализуем.

4. Щелкните новую запись **register_notifications** в таблице API.

	![][3]

5. Откройте вкладку **Скрипт** и замените существующий код на приведенный ниже код:

		exports.post = function(request, response) {

			// Create a notification hub instance.
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
				'<FULL_SAS_CONNECTION_STRING>');
		
		    // Get the registration info that we need from the request. 
		    var platform = request.body.platform;
		    var userId = request.user.userId;
		    var installationId = request.header('X-ZUMO-INSTALLATION-ID');
		    
		    // Function called when registration is completed.
		    var registrationComplete = function(error, registration) {
		        if (!error) {
		            // Return the registration.
		            response.send(200, registration);
		        } else {
		            response.send(500, 'Registration failed!');
		        }
		    }
		    // Function called to log errors.
		    var logErrors = function(error) {
		        if (error) {
		            console.error(error)
		        }
		    }
		    // Get existing registrations.
		    hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
		        var firstRegistration = true;
		        if (existingRegs.length > 0) {
		             for (var i = 0; i < existingRegs.length; i++) {
		                if (firstRegistration) {
		                    // Update an existing registration.
		                    if (platform === 'win8') {
		                        existingRegs[i].ChannelUri = request.body.channelUri;                        
		                        hub.updateRegistration(existingRegs[i], registrationComplete);                        
		                    } else if (platform === 'ios') {
		                        existingRegs[i].DeviceToken = request.body.deviceToken;
		                        hub.updateRegistration(existingRegs[i], registrationComplete);
		                    } else {
		                        response.send(500, 'Unknown client.');
		                    }
		                    firstRegistration = false;
		                } else {
		                    // We shouldn't have any extra registrations; delete if we do.
		                    hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
		                }
		            }
                } else {
                    // Create a new registration.
                    if (platform === 'win8') {                
                        hub.wns.createNativeRegistration(request.body.channelUri, 
                        [userId, installationId], registrationComplete);
                    } else if (platform === 'ios') {
                        hub.apns.createNativeRegistration(request.body.deviceToken, 
                        [userId, installationId], registrationComplete);
                    } else {
                        response.send(500, 'Unknown client.');
                    }
                }
            });
        }

	Этот код получает сведения о платформе и deviceID из тела сообщения. Эти данные вместе с идентификатором установки из заголовка запроса и идентификатором вошедшего в систему пользователя используется для обновления существующей или создания новой регистрации. Эта регистрация помечается с использованием идентификатора пользователя и идентификатора установки.

6. Обновите скрипт, чтобы заменить _`<NOTIFICATION_HUB_NAME>`_ и _`<FULL_SAS_CONNECTION_STRING>`_ на значения для своего концентратора уведомлений, а затем нажмите кнопку **Сохранить**.

	<div class="dev-callout"><b>Примечание.</b>
		<p>Обязательно используйте значение <strong>DefaultFullSharedAccessSignature</strong> для параметра <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em> Это утверждение позволяет методу настраиваемого API создавать и обновлять регистрации.</p>
	</div>

<h2><a name="update-app"></a><span class="short-header">Обновление приложения</span>Обновление приложения для выполнения входа и запроса регистрации</h2>

Затем необходимо обновить приложение TodoList для запроса регистрации уведомления, вызвав новый настраиваемый интерфейс API.

1. Выполните следующие действия в одной из следующих версий процедуры **Регистрация текущего пользователя для push-уведомлений с помощью мобильной службы** в зависимости от используемой клиентской платформы:

	+ [Версия C# Магазина Windows][Client topic Windows Store C# version]
	+ [Версия iOS][Client topic iOS version]

2. Запустите обновленное приложение, выполните вход с помощью Facebook и убедитесь, что отображается идентификатор регистрации, назначенный уведомлению.

<h2><a name="send-notifications"></a><span class="short-header">Отправка уведомлений</span>Обновление вашей мобильной службы для отправки уведомлений</h2>

Последним шагом является добавление кода, который отправляет уведомления в мобильную службу. Этот код уведомления добавляется в серверный скрипт, который зарегистрирован в обработчике вставки таблицы **TodoItem**.

1. Вернитесь на портал управления и откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][4]

2. В таблице **todoitem** щелкните вкладку **Скрипт** и выберите **Вставить**.
   
  	![][5]

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом:

		function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Create the payload for a Windows Store app.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}
	
	При этом предпринимается попытка отправить уведомления в тег для текущего пользователя, выполнившего вход в систему, в приложениях Магазина Windows и iOS.
		
4. Обновите скрипт, чтобы заменить _`<NOTIFICATION_HUB_NAME>`_ и _`<FULL_SAS_CONNECTION_STRING>`_ на значения для своего концентратора уведомлений, а затем нажмите кнопку **Сохранить**.

   	При этом регистрируется новый скрипт вставки, в котором используются концентраторы уведомлений для отправки push-уведомления (вставленный текст) в канал, указанный в запросе вставки.

	<div class="dev-callout"><b>Примечание.</b>
		<p>Обязательно используйте значение <strong>DefaultFullSharedAccessSignature</strong> для параметра <em><code>&lt;FULL_SAS_CONNECTION_STRING&gt;</code></em> Это утверждение предоставляет скрипту вставки полный доступ, включая возможность отправлять уведомления зарегистрированным клиентам.</p>
	</div>

<h2><a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении</h2>

Теперь, когда уведомления настроены, настало время протестировать приложение с помощью вставки данных для создания уведомления.

1. Запустите приложение. 

	При запуске снова отображается уведомление о регистрации.

2. Опять введите текст и добавьте новый элемент.

	Обратите внимание, что после завершения вставки приложение получает push-уведомление от концентраторов уведомлений.

	<div class="dev-callout"><b>Примечание.</b>
		<p>Если регистрация для платформы, куда требуется отправить уведомление, отсутствует, на сервере возникает ошибка. В этом случае такую ошибку можно игнорировать. Чтобы узнать, как предотвратить такую ситуацию с помощью шаблонов, см. раздел <a href="/ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/" target="_blank">Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений</a>.</p>
	</div>

3. (Необязательно) Разверните клиентское приложение на втором устройстве, а затем запустите это приложение и вставьте текст. 

	На каждом из устройств отображается уведомление.

## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы завершили работу с данным учебником, рекомендуется ознакомиться со следующими учебниками:

+ **Использование концентраторов уведомлений для передачи экстренных новостей ([Магазин Windows C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>В этом учебнике, ориентированном на конкретную платформу, показано, как с помощью тегов предоставить пользователям возможность для подписки на интересующие их типы уведомлений. 

+ **[Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений]**<br/>Этот учебник представляет собой дополнение к текущему учебнику **Уведомление пользователей с помощью концентраторов уведомлений** и описывает, как можно применять ориентированные на платформу шаблоны в целях регистрации для использования уведомлений. Это позволяет отправлять уведомления из одного метода в серверный код.

Дополнительные сведения о концентраторах уведомлений см. в разделе [Концентраторы уведомлений Azure].


[Обновление вашей мобильной службы для регистрации уведомлений]: #register-notification
[Обновление приложения для выполнения входа и запроса регистрации]: #update-app
[Обновление вашей мобильной службы для отправки уведомлений]: #send-notifications


[0]: ./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png
[2]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png
[3]: ./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png
[4]: ./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png
[5]: ./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png
[6]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png
[7]: ./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png


[Get started Windows Store]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet
[Get started iOS]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-ios
[Get started Android]: /ru-ru/manage/services/notification-hubs/get-started-notification-hubs-android
[Get started auth Windows Store]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started auth iOS]: /ru-ru/develop/mobile/tutorials/get-started-with-users-ios/
[Get started auth Android]: /ru-ru/develop/mobile/tutorials/get-started-with-users-android/
[Client topic Windows Store C# version]: /ru-ru/manage/services/notification-hubs/register-users-mobile-services-dotnet 
[Client topic iOS version]: /ru-ru/manage/services/notification-hubs/register-users-ios 
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Management Portal]: https://manage.windowsazure.com/
[Отправка кроссплатформенных уведомлений пользователям с помощью концентраторов уведомлений]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services
[Breaking news .NET]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet
[Breaking news iOS]: /ru-ru/manage/services/notification-hubs/breaking-news-ios
[Концентраторы уведомлений Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927170.aspx

