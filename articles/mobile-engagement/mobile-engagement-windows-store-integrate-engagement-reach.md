<properties 
	pageTitle="Интеграция пакета SDK Reach для универсальных приложений для Windows" 
	description="Интеграция Azure Mobile Engagement Reach с универсальными приложениями для Windows"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="piyushjo" />

#Интеграция пакета SDK Reach для универсальных приложений для Windows

Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, описанную в документе [Интеграция пакета SDK Engagement для универсальных приложений для Windows](mobile-engagement-windows-store-integrate-engagement.md).

##Внедрение пакета SDK для Engagement Reach в проект универсального приложения для Windows

Ничего добавлять не нужно. Ссылки и ресурсы по `EngagementReach` уже включены в проект.

> [AZURE.TIP]Вы можете изменить изображения, которые находятся в папке `Resources` проекта, в частности фирменный значок (по умолчанию используется значок Engagement). Для универсальных приложений также можно переместить папку `Resources` в общий проект, чтобы сделать его содержимое доступным для нескольких приложений, но необходимо сохранить файл `Resources\EngagementConfiguration.xml` в папке по умолчанию, так как он зависит от платформы.

##Включение службы уведомлений Windows

Чтобы использовать **службу уведомлений Windows** (WNS), в файле `Package.appxmanifest` на вкладке `Application UI` щелкните `All Image Assets` в поле слева. Справа от поля в `Notifications` измените значение `toast capable` с `(not set)` на `Yes`.

Кроме того, необходимо синхронизировать приложение с учетной записью Майкрософт и платформой Engagement. В интерфейсном компоненте Engagement перейдите в параметры приложения и в разделе `native push` вставьте свои учетные данные. После этого щелкните правой кнопкой мыши проект и выберите `store` и `Associate App with the Store...`.

##Запуск пакета SDK для Engagement Reach

Измените `App.xaml.cs`:

-   Добавьте операторы `using`:

		using Microsoft.Azure.Engagement;

-   Вставьте `EngagementReach.Instance.Init` сразу `EngagementAgent.Instance.Init` после в `OnLaunched`:

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

-   Если вы хотите запускать рекламную кампанию Engagement при активации приложения, переопределите метод `OnActivated`:

		protected override void OnActivated(IActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

	`EngagementReach.Instance.Init` выполняется в выделенном потоке. Вам не придется делать это самостоятельно.

> [AZURE.TIP]Вы можете указать имя канала push-уведомлений WNS своего приложения в файле `Resources\EngagementConfiguration.xml` проекта на `<channelName></channelName>`. По умолчанию Engagement создает имя на основе appId, так что его не нужно задавать самостоятельно, за исключением случаев, когда вы планируете использовать канал push-уведомлений вне Engagement.

##Интеграция

В Engagement предусмотрено два способа реализации уведомлений и объявлений о рекламной кампании: интеграция наложения и интеграция веб-представления.

windows-sdk-engagement-overlay-integration не требует добавления в приложение большого объема кода. Необходимо просто пометить страницы (XAML- и CS-файлы) тегом EngagementPageOverlay. Кроме того, если вы измените стандартное представление Engagement, ваши настройки будут использоваться для всех помеченных страниц, так что их достаточно определить один раз. Но если страницы должны наследовать свойства от объекта, отличного от EngagementPageOverlay, этот вариант не подойдет и придется использовать интеграцию веб-представления.

Реализация windows-sdk-engagement-webview-integration более сложна, но если страницы приложения должны наследовать свойства от объекта, отличного от Page, необходимо интегрировать веб-представление и его поведение.

> [AZURE.TIP]Советуем добавить элемент первого уровня `<Grid></Grid>` вокруг всего содержимого страницы. Чтобы интегрировать веб-представление, добавьте его в качестве дочернего элемента в эту сетку. Если необходимо задать компонент Engagement в другом месте, помните, что вам придется самостоятельно управлять размером экрана.

### Интеграции наложения

Engagement обеспечивает наложение при отображении уведомлений и объявлений.

Если вы хотите использовать его, не используйте windows-sdk-engagement-webview-integration.

В XAML-файле измените ссылку EngagementPage на EngagementPageOverlay.

-   Добавьте в объявления пространств имен:

			xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Замените `engagement:EngagementPage` на `engagement:EngagementPageOverlay`:

**EngagementPage:**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		
		    <!-- layout -->
		</engagement:EngagementPage>

**EngagementPageOverlay:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		
		    <!-- layout -->
		</engagement:EngagementPageOverlay>

> **EngagementPageOverlay для 8.1:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		    <Grid>
		      <!-- layout -->
		    </Grid>
		</engagement:EngagementPageOverlay>

Затем в CS-файле пометьте страницу тегом EngagementPageOverlay, а не EngagementPage и импортируйте Microsoft.Azure.Engagement.Overlay.

			using Microsoft.Azure.Engagement.Overlay;

-   Замените `EngagementPage` на `EngagementPageOverlay`:

**EngagementPage:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**EngagementPageOverlay**:

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

Теперь эта страница использует механизм наложения Engagement и вам не нужно вставлять веб-представление.

Механизм наложения Engagement использует первый элемент Grid, найденный в XAML-файле, для добавления двух веб-представлений на страницу. Если вы хотите указать, куда нужно вставить веб-представления, можно определить сетку с именем EngagementGrid:

			<Grid x:Name="EngagementGrid"></Grid>

Уведомление и объявление с наложением можно настроить непосредственно в их XAML- и CS-файлах:

-   `EngagementAnnouncement.html`: HTML-код веб-представления `Announcement`.
-   `EngagementOverlayAnnouncement.xaml` : XAML-код `Announcement`.
-   `EngagementOverlayAnnouncement.xaml.cs`: код, связанный с `EngagementOverlayAnnouncement.xaml`.
-   `EngagementNotification.html`: HTML-код веб-представления `Notification`.
-   `EngagementOverlayNotification.xaml` : XAML-код `Notification`.
-   `EngagementOverlayNotification.xaml.cs`: код, связанный с `EngagementOverlayNotification.xaml`.
-   `EngagementPageOverlay.cs`: код для отображения объявлений и уведомлений `Overlay`.

### Интеграция веб-представления

Если вы хотите использовать веб-представление, не используйте windows-sdk-engagement-overlay-integration.

Для отображения содержимого Engagement необходимо интегрировать два веб-представления XAML в каждую страницу, на которой необходимо отобразить уведомление и объявление. Поэтому добавьте следующий код в XAML-файл.

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **Для интеграции с 8.1:**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPage>

Связанный CS-файл должен выглядеть следующим образом:

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			   /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			
			    Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			  }
			
			  #region to implement
			  /* Allow webview script to notify system */
			  private void scriptEvent(object sender, NotifyEventArgs e)
			  {
			  }
			
			  /* When page is left ensure to detach SizeChanged handler */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			    base.OnNavigatedFrom(e);
			  }
			
			  /* "width" is the current width of your application display */
			  double width = Window.Current.Bounds.Width;
			
			  /* "height" is the current height of your application display */
			  double height = Window.Current.Bounds.Height;
			
			  /// <summary>
			  /// Set your webview elements to the correct size
			  /// </summary>
			  /// <param name="width">The width of your current display</param>
			  /// <param name="height">The height of your current display</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			  /// </summary>
			  /// <param name="sender">Original event trigger</param>
			  /// <param name="e">Window Size Changed Event argument</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			  }
			  #endregion
			}

> Эта реализация с внедренным веб-представлением меняет свой размер при повороте экрана устройства.

##Обработка отправленных данных (необязательно)

Если вы хотите, чтобы приложения могли получать отправленные данные рекламных кампаний, необходимо реализовать два события класса EngagementReach:

В файле App.xaml.cs в Public App(){} добавьте следующее:

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

> [AZURE.WARNING]Engagement не может получать несколько отзывов для отправленных данных. Не забывайте, что если вы планируете установить для события несколько обработчиков, отзывы будут соответствовать последнему из них. Во избежание путаницы с отзывами на внешних компонентах советуем, чтобы в этом случае возвращаемое значение было всегда одинаковым.

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

Затем задайте содержимое поля `EngagementReach.Instance.Handler`, используя настраиваемый объект в классе `App.xaml.cs` метода `App()`.

**Пример кода:**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE]По умолчанию Engagement использует собственную реализацию `EngagementReachHandler`. Ее не обязательно создавать отдельно, но даже если вы ее создадите, вам не придется переопределять каждый метод. Поведение по умолчанию — выбор базового объекта Engagement.

### Веб-представление

По умолчанию рекламная кампания будет использовать внедренные ресурсы библиотеки DLL для отображения уведомлений и страниц.

Чтобы обеспечить все возможности настройки, мы используем только веб-представление. Если вы хотите настроить макеты, переопределите непосредственно файлы ресурсов `EngagementAnnouncement.html` и `EngagementNotification.html`. Для правильной работы Engagement весь код должен быть в тегах `<body></body>`. Однако можно добавить тег снаружи `engagement_webview_area`.

Тем не менее, вы можете использовать собственные ресурсы.

Вы можете переопределить методы `EngagementReachHandler` в подклассе, чтобы в Engagement использовались ваши макеты, но не забудьте внедрить механизм Engagement:

**Пример кода:**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


По умолчанию AnnouncementHTML — это `ms-appx-web:///Resources/EngagementAnnouncement.html`. Представляет HTML-файл, который задает структуру содержимого push-сообщения (текстовое объявление, веб-объявление и объявление опроса). AnnouncementName — `engagement_announcement_content`. Это имя структуры веб-представления на XAML-странице.

NotfificationHTML — `ms-appx-web:///Resources/EngagementNotification.html`. Представляет HTML-файл, который задает структуру уведомления в push-сообщении. NotfificationName —`engagement_notification_content`. Это имя структуры веб-представления на XAML-странице.

### Настройка

Вы можете настроить необходимое веб-представление уведомлений и объявлений, если сохраните объект Engagement. Проследите, чтобы объект веб-представления был описан трижды: первый раз — в XAML-файле, второй раз — в CS-файле в методе setwebview() и третий раз в HTML-файле.

-   В XAML-файле описывается текущий компонент веб-представления графического макета.
-   В CS-файл можно определить setwebview(), в котором задается размер двух веб-представлений (уведомления, объявления). Это очень эффективно при изменении размера приложения.
-   В HTML-файле Engagement описывается содержимое веб-представления, его структура и взаимное расположение элементов.

### Запуск сообщения

Если щелкнуть системное уведомление (всплывающее уведомление), Engagement запустит приложение, загрузит содержимое push-сообщений и отобразит страницу для соответствующей кампании.

Между запуском приложения и отображением страницы есть определенная задержка (в зависимости от скорости сети).

Чтобы уведомить пользователя о загрузке, необходимо предоставить визуальную информацию, например индикатор выполнения. Engagement не может обработать ее самостоятельно, но предоставляет несколько обработчиков, которыми вы можете воспользоваться.

Чтобы реализовать обратный вызов, в файле App.xaml.cs в Public App(){} добавьте следующее:

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

Вы можете задать обратный вызов в методе «Public App(){}» файла `App.xaml.cs` (предпочтительно перед вызовом `EngagementReach.Instance.Init()`).

> [AZURE.TIP]Каждый обработчик вызывается потоком пользовательского интерфейса. При использовании MessageBox или других компонентов, связанных с пользовательским интерфейсом, об этом можно не беспокоится.

##Совет по настраиваемой схеме

Мы предоставляем возможность использования настраиваемой схемы. Вы можете отправлять из внешнего компонента Engagement универсальный код ресурса другого типа. Этот код будет использоваться в работе приложения. Управление схемой по умолчанию, например `http, ftp, ...`, осуществляет операционная система Windows. Если на устройстве не установлено приложение по умолчанию, появится окно с запросом. Можно использовать также другую схему, например схему приложения. Кроме того, для приложения можно использовать настраиваемую схему.

Настраиваемую схему можно с легкостью задать в приложении, открыв `Package.appxmanifest` и панель `Declarations`. Прокрутите ползунок поля «Доступные объявления», выберите `Protocol` и добавьте его. Замените значение в поле `Name` необходимым именем нового протокола.

Чтобы использовать этот протокол, измените файл `App.xaml.cs`, задав в нем метод `OnActivated`, и не забудьте также инициализировать Engagement:

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion

<!--HONumber=54--> 