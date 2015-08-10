<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement для универсальных приложений для Windows"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для универсальных приложений для Windows."
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
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Приступая к работе с Azure Mobile Engagement для универсальных приложений для Windows

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этом разделе показано, как применять Azure Mobile Engagement для анализа использования приложений и отправки push-уведомлений сегментированным пользователям универсального приложения для Windows. В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. Вы создадите пустое универсальное приложение для Windows, которое будет собирать основные данные об использовании приложений и получать push-уведомления, используя службу push-уведомлений Windows (WPNS). По завершении вы сможете рассылать push-уведомления на все устройства или взять за цель конкретных пользователей на основе свойств их устройств. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Mobile Engagement для охвата определенных пользователей и групп устройств.

Для работы с данным учебником требуется следующее:

+ Visual Studio 2013
+ [Пакет SDK для Mobile Engagement для универсальных приложений для Windows]

> [AZURE.IMPORTANT]Прохождение этого учебника является необходимым условием для изучения всех других учебников, посвященных Mobile Engagement для универсальных приложений для Windows. Для работы с ним необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a id="setup-azme"></a>Настройка Mobile Engagement для универсального приложения для Windows

1. Выполните вход на портал управления Azure, затем нажмите **+NEW** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем **Создать**.

   	![][7]

3. Во всплывающем окне введите следующую информацию:

   	![][8]

	- **Имя приложения** — введите имя своего приложения. Вы можете использовать любые символы.
	- **Платформа**. Выберите целевую платформу для этого приложения (\*\*Универсальная платформа для Windows \*\*) (если приложение предназначено для нескольких платформ, изучите этот учебник для каждой из них).
	- **Имя ресурса приложения**. Это имя, по которому приложение можно будет открывать через API и по URL-адресам. Необходимо использовать только традиционные символы для URL-адреса. Автоматически созданное имя должно обеспечить достаточно прочную основу. Также рекомендуется добавить имя платформы, чтобы избежать конфликта имен, так как это имя должно быть уникальным.
	- **Местоположение**. Выберите центр обработки данных, в котором будет размещено это приложение (и, что еще важнее, его коллекция).
	- **Коллекция**. Если вы уже создали приложение, то выберите созданную ранее коллекцию, а если нет, то выберите "Создать коллекцию".
	- **Имя коллекции**. Указывает на выбранную группу приложений. Оно также обеспечит помещение всех ваших приложений в одну группу, что позволит выполнять сводное вычисление метрик. Здесь следует указать имя вашей компании или отдела, если применимо.

	> [AZURE.TIP]Если универсальное приложение будет рассчитано как на платформу Windows, так и на Windows Phone, то следует создать два приложения Mobile Engagement (по одному для каждой из поддерживаемых платформ). Это позволит правильно сегментировать аудиторию и отправлять целевые уведомления для каждой платформы.

4. На вкладке **Приложения** выберите только что созданное приложение.

5. Щелкните **Информация о подключении**, чтобы отобразить параметры подключения, которые следует использовать при интеграции пакета SDK в вашем мобильном приложении.

   	![][10]

6. Скопируйте **строку подключения**. Это требуется для того, чтобы обозначить данное приложение в коде приложения и подключаться к Mobile Engagement, используя универсальное приложение.

   	![][11]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений. Информацию о полной интеграции см. в [документации на пакет SDK для Mobile Engagement для универсальных приложений для Windows].

Мы создадим базовое приложение в Visual Studio, чтобы продемонстрировать интеграцию.

###Создание проекта универсального приложения для Windows

Если у вас уже есть приложение и вы знакомы с разработкой универсальных приложений для Windows, вы можете пропустить этот шаг.

1. Запустите Visual Studio и на начальном экране выберите **Создать проект...**.

2. Во всплывающем меню выберите `Store Apps` -> `Universal Apps` -> `Blank App (Universal Apps)`. Заполните поля `Name` и `Solution name` для приложения и нажмите кнопку **ОК**.

   	![][13]

Вы создали универсальное приложение для Windows, в которое нужно интегрировать пакет SDK для Azure Mobile Engagement.

###Подключите приложение к серверной части Mobile Engagement.

1. Установите пакет Nuget [Mobile Engagement Windows Universal SDK] в проект. Если приложение предназначено как для платформы Windows, так и для Windows Phone, это будет необходимо сделать для обоих проектов. Один и тот же пакет Nuget добавит нужные двоичные файлы для определенной платформы в каждый проект.

2. Откройте `Package.appxmanifest` и добавьте следующий код, если он не был добавлен автоматически:

		Internet (Client)

	![][20]

3. Теперь вставьте строку подключения, которую вы скопировали ранее для приложения Mobile Engagement, в файл `Resources\EngagementConfiguration.xml` между тегами `<connectionString>` и `</connectionString>`:

	![][22]

	>[AZURE.TIP]Если приложение будет рассчитано как на платформу Windows, так и на Windows Phone, то следует создать два приложения Mobile Engagement (по одному для каждой из поддерживаемых платформ). Это позволит правильно сегментировать аудиторию и отправлять целевые уведомления для каждой платформы.

4. В файле `App.xaml.cs`:

	а. Добавьте оператор `using`:

			using Microsoft.Azure.Engagement;

	b. Инициализируйте пакет SDK в методе `OnLaunched`:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Вставьте следующий код в метод `OnActivated` и добавьте метод, если его пока нет:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Включение мониторинга в реальном времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement. Для этого можно создать подкласс `MainPage` с помощью `EngagementPage` в пакете SDK для Mobile Engagement.

1. 	Добавьте оператор `using`:

		using Microsoft.Azure.Engagement;

2. Замените суперкласс `MainPage`, который находится перед `Page`, на `EngagementPage`:

	![][23]

3. В файле `MainPage.xml`:

	а. Добавьте в объявления пространств имен:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. В имени тега XML замените `Page` на `engagement:EngagementPage`.

###Убедитесь, что приложение подключено с использованием функции мониторинга в реальном времени

В этом разделе показано, как сделать так, чтобы приложение подключалось к серверной части Mobile Engagement с помощью функции мониторинга в реальном времени.

1. Перейдите на портал Mobile Engagement.

	На портале Azure убедитесь, что находитесь в приложении, которое используется для этого проекта, и нажмите кнопку **Использовать** внизу страницы.

	![][26]

2. Вы перейдете на страницу параметров вашего приложения на портале Engagement. На этой странице щелкните вкладку **Монитор**, как показано ниже. ![][30]

3. Здесь в режиме реального времени может отобразиться любое устройство, которое запустит ваше приложение.

4. Вернитесь в Visual Studio и запустите приложение в эмуляторе или на подключенном устройстве.

5. Если все сделано правильно, в разделе "Монитор" будут отображаться данные одного сеанса, получаемые в реальном времени.![][33]

**Поздравляем!** Вы успешно выполнили первый шаг этого учебника, суть которого заключалась в подключении приложения к серверной части Mobile Engagement, которая уже отправляет данные.

##<a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах описаны действия по настройке приложения для приема уведомлений и сообщений.

###Настройка приложения для приема push-уведомлений WNS

1. В файле `Package.appxmanifest` в разделе `Application` в группе `Notifications` выберите значение `Yes` для `Toast capable:`:

	![][35]

###Запуск пакета SDK для REACH

1. В `App.xaml.cs` вызовите `EngagementReach.Instance.Init();` в функции `OnLaunched` сразу после инициализации агента:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. В `App.xaml.cs` вызовите `EngagementReach.Instance.Init(e);` в функции `OnActivated` сразу после инициализации агента:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Все готово к отправке всплывающего уведомления. Осталось только проверить, правильно ли выполнена эта базовая интеграция.

###Предоставление доступа к Mobile Engagement для отправки уведомлений

1. Необходимо связать свое приложение с приложением Магазина Windows, чтобы получить `Package security identifier (SID)` и `Secret Key` (секрет клиента). Вы можете создать приложение в [Центре разработки для Магазина Windows], а затем **связать приложение с Магазином** в Visual Studio.

2. Перейдите к **параметрам** портала Mobile Engagement и щелкните раздел `Native Push` слева.

3. Нажмите кнопку `Edit`, чтобы ввести `Package security identifier (SID)` и `Secret Key`, как показано ниже:

	![][36]

##<a id="send"></a>Отправка уведомления в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомления в приложение.

1. Перейдите на вкладку **РЕКЛАМНАЯ КАМПАНИЯ** на портале Mobile Engagement.

2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений. ![][37]

3. Выполните следующие шаги для настройки первого поля кампании: ![][38]

	а. Присвойте имя кампании.

	b. Выберите для параметра **Время доставки** значение *Любое*, чтобы приложение могло принимать уведомления независимо от того, запущено оно или нет.

	c. В тексте уведомления введите **заголовок**, которой будет отображаться полужирным шрифтом в push-уведомлении.

	г) Затем введите текст сообщения.

4. Прокрутите вниз и в разделе "Содержимое" выберите **Только уведомления**. ![][39]

5. Вы закончили настройку самой простой кампании. Теперь снова прокрутите страницу вниз и нажмите кнопку **Создать**, чтобы сохранить кампанию.

6. Напоследок щелкните **Активировать**, чтобы активировать кампанию и начать отправку push-уведомлений. ![][41]

На устройстве должно появиться всплывающее уведомление от кампании (для этого приложение должно быть закрыто). Если приложение было запущено, закройте его за несколько минут до активации кампании, чтобы получить всплывающее уведомление. Если вы хотите интегрировать уведомление в приложение, чтобы оно выводилось при открытии приложения, см. раздел [Универсальные приложения для Windows — интеграция наложения].

<!-- URLs. -->
[документации на пакет SDK для Mobile Engagement для универсальных приложений для Windows]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement Windows Universal SDK]: http://go.microsoft.com/?linkid=9864592
[Пакет SDK для Mobile Engagement для универсальных приложений для Windows]: http://go.microsoft.com/?linkid=9864592
[Центре разработки для Магазина Windows]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Универсальные приложения для Windows — интеграция наложения]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png
 

<!---HONumber=July15_HO5-->