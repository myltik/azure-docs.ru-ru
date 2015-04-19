<properties 
	pageTitle="Интеграция пакета SDK для Windows Phone для Azure Mobile Engagement" 
	description="Как интегрировать рекламные кампании Engagement в Windows Phone"				
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Как интегрировать пакет SDK для Engagement Reach в проект Windows Phone

Прежде чем приступать к этому руководству, нужно сначала выполнить процедуру интеграции, описанную в документе [Как интегрировать Mobile Engagement в Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md) .

##Внедрение пакета SDK для Engagement Reach в проект Windows Phone

Ничего добавлять не нужно. Ссылки и ресурсы по `EngagementReach` уже включены в проект.

> [AZURE.TIP]  Вы можете настроить изображения, расположенные в папке проекта `Resources`, в частности фирменный значок (по умолчанию используется значок Mobile Engagement).

##Добавление возможностей

Для пакета SDK для Engagement Reach нужны определенные дополнительные возможности.

Откройте файл `WMAppManifest.xml` и убедитесь, что следующие возможности отмечены на панели `Capabilities`:

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

##Включение службы push-уведомлений Майкрософт

Чтобы использовать **службу push-уведомлений Майкрософт** (MPNS), в файле `WMAppManifest.xml` должен быть тег `<App />` с именем проекта в качестве атрибута `Publisher`.

##Запуск пакета SDK для Engagement Reach

### Конфигурация Engagement

Конфигурация Engagement централизована в файле `Resources\EngagementConfiguration.xml` проекта.

Измените этот файл, чтобы задать конфигурацию Reach:

-   *Необязательно*. Укажите, активированы ли системные push-уведомления (MPNS) между тегами `<enableNativePush>` и `</enableNativePush>` (по умолчанию `true`).
-   *Необязательно*. Укажите имя канала push-уведомлений между тегами `<channelName>` и `</channelName>` , задайте то, которое приложение может использовать сейчас, или не указывайте ничего.

Если вместо этого вам необходимо указать ее во время выполнения, можно вызвать следующий метод до инициализации агента Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			engagementConfiguration.Reach.EnableNativePush = true;                  /* [Optional] whether the native push (MPNS) is activated or not. */
			engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   /* [Optional] Provide the same channel name that your application may currently use. */
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Вы можете указать имя канала push-уведомлений MPNS своего приложения. По умолчанию Engagement создает имя на основе appId, так что его не нужно задавать самостоятельно, за исключением случаев, когда вы планируете использовать канал push-уведомлений вне Engagement.

### Инициализация Engagement

Измените `App.xaml.cs`:

-   Добавьте оператор `using`:

			using Microsoft.Azure.Engagement;

-   Вставьте `EngagementReach.Instance.Init` сразу после `EngagementAgent.Instance.Init` в `Application_Launching`:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   EngagementAgent.Instance.Init();
			   EngagementReach.Instance.Init();
			}

-   Вставьте `EngagementReach.Instance.OnActivated` в метод `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			   EngagementReach.Instance.OnActivated(e);
			}

> [AZURE.IMPORTANT]  `EngagementReach.Instance.Init` выполняется в выделенном потоке. Вам не придется делать это самостоятельно.

Отправка
----------

Корпорация Майкрософт ввела ряд правил в отношении использования push-уведомлений.

Вот цитата из раздела 2.9 документации по [политикам в отношении приложений] Майкрософт:

> 2.9.1) Сначала приложение должно показать описание предоставляемых уведомлений и **получить явное разрешение (согласие) пользователя**. В нем также **должен быть предусмотрен механизм, с помощью которого пользователь мог бы отказаться от получения push-уведомления**. Все уведомления, предоставляемые с помощью службы push-уведомлений Майкрософт, должны соответствовать описанию, предоставленному пользователю, и всем применимым [политикам в отношении приложений] [политикам в отношении содержимого] и [дополнительным требованиям к приложениям определенных типов].

**Краткие выводы**. Нужно попросить пользователя дать согласие на получение push-уведомлений. Поэтому добавьте в параметры возможность отключения push-уведомлений.

Объект EngagementReach предоставляет два метода управления включением и отключением: `EnableNativePush()` и `DisableNativePush()`. Вы можете, например, создать в настройках параметр, позволяющий включить или отключить MPNS.

Можно также отключить MPNS с помощью конфигурации Engagement\<windows-phone-sdk-reach-configuration\>.

> 2.9.2) Приложения и предусмотренные в нем механизмы использования службы push-уведомлений Майкрософт не должны слишком интенсивно использовать ресурсы или полосу пропускания сети службы push-уведомлений Майкрософт или создавать любую другую недопустимую нагрузку на устройстве Windows Phone или других устройствах или в других службах Майкрософт вследствие отправки чрезмерного (с точки зрения Майкрософт) количества push-уведомлений. Кроме того, они не должны конфликтовать с сетями или серверами Майкрософт или сторонних поставщиков, подключенными к службам push-уведомлений Майкрософт, либо нарушать их работу.

**Краткие выводы**. Не следует использовать слишком много push-уведомлений. Engagement обрабатывает их автоматически.

> 2.9.3) Службу push-уведомлений Майкрософт нельзя использовать для отправки критически важных уведомлений или уведомлений, от которых зависит жизнь или смерть, в том числе критически важных уведомлений, связанных с медицинскими устройствами или состоянием здоровья. КОРПОРАЦИЯ МАЙКРОСОФТ НЕ ДАЕТ НИКАКИХ ГАРАНТИЙ ТОГО, ЧТО ИСПОЛЬЗОВАНИЕ СЛУЖБЫ PUSH-УВЕДОМЛЕНИЙ МАЙКРОСОФТ И ДОСТАВКА УВЕДОМЛЕНИЙ ЭТОЙ СЛУЖБЫ БУДЕТ ОСУЩЕСТВЛЯТЬСЯ БЕСПЕРЕБОЙНО, БЕЗОШИБОЧНО И В РЕЖИМЕ РЕАЛЬНОГО ВРЕМЕНИ.

**Краткие выводы**. Не следует полагаться на MPNS, если необходимо отправить критически важную информацию. Engagement использует MPNS, поэтому это правило также действует для кампаний, созданных во внешних компонентах Engagement.

**Если вы не будете следовать этим рекомендациям, мы не гарантируем, что приложения пройдут процесс проверки.**

##Обработка отправленных данных (необязательно)

Если вы хотите, чтобы приложения могли получать отправленные данные рекламных кампаний, необходимо реализовать два события класса EngagementReach:

			EngagementReach.Instance.DataPushStringReceived += (body) =>
			{
			   Debug.WriteLine("String data push message received: " + body);
			   return true;
			};
			
			EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
			{
			   Debug.WriteLine("Base64 data push message received: " + encodedBody);
			   // Do something useful with decodedBody like updating an image view
			   return true;
			};

Как можно видеть, обратный вызов каждого метода возвращает логическое значение. После отправки данных Engagement отправляет отзыв серверной части. Если обратный вызов возвращает значение false, будет отправлен отзыв `exit`. В противном случае отправляется `action`. Если для событий не задан обратный вызов, Engagement будет возвращен отзыв `drop`.

> [AZURE.WARNING] Engagement не может получать несколько отзывов для отправленных данных. Не забывайте, что если вы планируете установить для события несколько обработчиков, отзывы будут соответствовать последнему из них. Во избежание путаницы с отзывами на внешних компонентах советуем, чтобы в этом случае возвращаемое значение было всегда одинаковым.

##Настройка пользовательского интерфейса (необязательно)

### Первый шаг

Мы позаботились о том, чтобы вы могли настраивать функциональность пользовательского интерфейса по своему усмотрению.

Для этого необходимо создать подкласс класса `EngagementReachHandler`.

**Пример кода:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			   internal class ExampleReachHandler : EngagementReachHandler
			   {
			      // Override EngagementReachHandler methods depending on your needs
			   }
			}

Затем задайте содержимое поля `EngagementReach.Instance.Handler`, используя настраиваемый объект в классе `App.xaml.cs` метода `Application_Launching`.

**Пример кода:**

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   // your app initialization 
			   EngagementReach.Instance.Handler = new ExampleReachHandler();
			   // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] По умолчанию Engagement использует собственную реализацию `EngagementReachHandler`. Ее не обязательно создавать отдельно, но даже если вы ее создадите, вам не придется переопределять каждый метод. Поведение по умолчанию - выбор базового объекта Engagement.

### Макеты

По умолчанию рекламная кампания будет использовать внедренные ресурсы библиотеки DLL для отображения уведомлений и страниц.

Тем не менее, вы можете применять собственные ресурсы, чтобы использовать в оформлении компонентов свою фирменную символику.

Вы можете переопределить методы `EngagementReachHandler` в подклассе, чтобы в Engagement использовался ваш макет:

**Пример кода:**

			// In your subclass of EngagementReachHandler
			
			public override string GetTextViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetWebViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetPollUri()
			{
			   // return the path of your own xaml
			}
			
			public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
			{
			   // return a new instance of your own notification
			}

> [AZURE.TIP] Метод `CreateNotification` может возвращать значение NULL. Уведомления не будут отображаться и рекламная кампания будет удалена.

Чтобы упростить реализацию макета, мы также предоставляем свой код на языке XAML, который может служить основой для вашего кода. Они находятся в архиве пакета SDK для Engagement (/src/reach/).

> [AZURE.WARNING] Мы предоставляем те же источники, которые используем сами. Поэтому если вы хотите изменить их напрямую, не забудьте изменить пространство имен и имя.

### Расположение уведомлений

По умолчанию уведомление отображается в приложении снизу слева. Это можно изменить, переопределив метод `GetNotificationPosition` объекта `EngagementReachHandler`.

			// In your subclass of EngagementReachHandler
			
			public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
			{
			   // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
			}

Сейчас можно на выбор задать расположение `BOTTOM` (по умолчанию) и `TOP`.

### Запуск сообщения

Если щелкнуть системное уведомление (всплывающее уведомление), Engagement запустит приложение, загрузит содержимое push-сообщений и отобразит страницу для соответствующей кампании.

Между запуском приложения и отображением страницы есть определенная задержка (в зависимости от скорости сети).

Чтобы уведомить пользователя о загрузке, необходимо предоставить визуальную информацию, например индикатор выполнения. Engagement не может обработать ее самостоятельно, но предоставляет несколько обработчиков, которыми вы можете воспользоваться.

Чтобы реализовать обратный вызов, сделайте вот что:

			/* The application has launched and the content is loading.
			 * You should display an indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
			
			/* The application has finished loading the content and the page
			 * is about to be displayed.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
			
			/* The content has been loaded, but an error has occurred.
			 * You can provide an information to the user.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Вы можете задать обратный вызов в методе `Application_Launching` файла `App.xaml.cs` (предпочтительно перед вызовом `EngagementReach.Instance.Init()`).

> [AZURE.TIP] Каждый обработчик вызывается потоком пользовательского интерфейса. При использовании MessageBox или других компонентов, связанных с пользовательским интерфейсом, об этом можно не беспокоится.


[Политики в отношении приложений]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Политики в отношении содержимого]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[Дополнительные требования к приложениям определенных типов]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

<!--HONumber=47-->
