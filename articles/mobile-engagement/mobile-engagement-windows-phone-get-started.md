<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement для приложений Windows Phone Silverlight"
	description="Узнайте, как использовать Azure Mobile Engagement для аналитики и отправки push-уведомлений для приложений Windows Phone Silverlight."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/30/2015"
	ms.author="piyushjo"/>

# Приступая к работе с Azure Mobile Engagement для приложений Windows Phone Silverlight

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этом разделе показано, как использовать Azure Mobile Engagement, чтобы понять направление использования приложения и отправлять push-уведомления сегментированным пользователям приложения Windows Phone Silverlight. В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. Здесь вы создадите пустое приложение Windows Phone Silverlight, которое будет собирать основные данные и получать push-уведомления, используя службу push-уведомлений (Майкрософт) (MPNS). Ознакомившись с этим руководством, вы сможете рассылать push-уведомления на все устройства или конкретным пользователям в зависимости от свойств их устройств (с использованием MPNS). Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Mobile Engagement для охвата определенных пользователей и групп устройств.

> [AZURE.NOTE]Если вы ориентируетесь на Windows Phone 8.1 (без Silverlight), см. [руководство по универсальным приложениям для Windows](mobile-engagement-windows-store-dotnet-get-started.md).

Для работы с данным учебником требуется следующее:

+ Visual Studio 2013
+ [пакет SDK для Windows Phone для Mobile Engagement].

> [AZURE.IMPORTANT]Изучение этого учебника является необходимым для перехода к другим учебникам по Mobile Engagement для приложений Windows Phone Silverlight, а для его прохождения у вас должна быть активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a id="setup-azme"></a>Настройка Mobile Engagement для приложения Windows Phone Silverlight

1. Войдите на портал Azure, затем нажмите **+СОЗДАТЬ** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем — **Создать**.

    ![][7]

3. Во всплывающем окне введите следующие сведения:

    ![][8]

  - **Имя приложения**. Введите имя своего приложения. Вы можете использовать любые символы.
  - **Платформа**. Выберите целевую платформу для этого приложения (**Windows Phone Silverlight**) (если приложение предназначено для нескольких платформ, повторите операции из этого учебника для каждой из них).
 - **Имя ресурса приложения**. Это имя, по которому приложение можно будет открывать через API и по URL-адресам. Необходимо использовать только традиционные символы для URL-адреса. Автоматически созданное имя должно быть основой имени. Также рекомендуется добавить имя платформы, чтобы избежать конфликта имен, так как это имя должно быть уникальным.
 - **Расположение**. Выберите центр данных, в котором будет размещено это приложение и его коллекция.
 - **Коллекция**. Если вы уже создали приложение, выберите созданную ранее коллекцию, а если нет, щелкните **Новая коллекция**.
 - **Имя коллекции**. Указывает на выбранную группу приложений. Если задать это имя, все приложения будут помещены в одну группу, что позволит выполнять сводные вычисления метрик. Здесь следует указать имя вашей компании или отдела, если применимо.

4. На вкладке **Приложения** выберите только что созданное приложение.

5. Щелкните **Сведения о подключении**, чтобы отобразить параметры подключения, которые следует использовать при интеграции пакета SDK в вашем мобильном приложении.

    ![][10]

6. Скопируйте **строку подключения**. Это необходимо, чтобы обозначить данное приложение в коде приложения и подключиться к Mobile Engagement из вашего приложения Windows Phone.

    ![][11]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Сведения о полной интеграции см. в документации о [пакете SDK для Windows Phone для Mobile Engagement].

Мы создадим базовое приложение в Visual Studio, чтобы продемонстрировать интеграцию.

###Создание проекта для Windows Phone Silverlight

1. Запустите Visual Studio и на **начальном** экране выберите **Создать проект**.

2. Во всплывающем окне выберите **Приложения Магазина** -> **Приложения Windows Phone** -> **Пустое приложение (Windows Phone Silverlight)**. Заполните поля `Name` и `Solution name` для приложения и нажмите кнопку **ОК**.

    ![][13]

3. Вы можете выбрать версию **Windows Phone 8.0** или **Windows Phone 8.1**.

Вы создали приложение Windows Phone Silverlight, в которое нам предстоит интегрировать пакет SDK для Azure Mobile Engagement.

###Подключение приложения к серверной части Mobile Engagement

1. Установите пакет Nuget [пакет SDK для Windows Phone для Mobile Engagement] в проект.

2. Откройте `WMAppManifest.xml` (в папке «Свойства») и убедитесь, что в теге `<Capabilities />` есть следующие объявления (в противном случае добавьте их):

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][20]

3. Теперь вставьте строку подключения, которую вы скопировали ранее для приложения Mobile Engagement, в файл `Resources\EngagementConfiguration.xml` между тегами `<connectionString>` и `</connectionString>`:

    ![][21]

4. В файле `App.xaml.cs`:

	а. Добавьте оператор `using`:

			using Microsoft.Azure.Engagement;

	b. Инициализируйте пакет SDK в методе `Application_Launching`:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Вставьте следующее в `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Включение мониторинга в реальном времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement. Для этого можно создать подкласс `MainPage` с помощью `EngagementPage` в пакете SDK для Mobile Engagement.

1. Добавьте оператор `using`:

       using Microsoft.Azure.Engagement;

2. Замените суперкласс **MainPage**, который расположен перед **PhoneApplicationPage**, на **EngagementPage**, как показано ниже:

	![][22]

3. В файле `MainPage.xml`:

	а. Добавьте в объявления пространств имен:

	   	 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. В имени тега XML замените `phone:PhoneApplicationPage` на `engagement:EngagementPage`.

###Подключение приложения с помощью мониторинга в реальном времени

В этом разделе показано, как настроить приложение, чтобы оно подключалось к серверной части Mobile Engagement с помощью функции мониторинга в реальном времени.

1. Перейдите на портал Mobile Engagement.

	С портала Azure войдите в приложение, которое используется для этого проекта, и нажмите кнопку **Выполнить охват** внизу страницы.

    ![][26]

2. Вы перейдете на страницу **параметров** вашего приложения на портале Engagement. На этой странице щелкните вкладку **Монитор**, как показано ниже. ![][30]

3. Здесь будут показаны все устройства в режиме реального времени, на которых будет запущено приложение.

4. Вернитесь в Visual Studio и запустите приложение в эмуляторе или на подключенном устройстве.

5. Если все сделано правильно, в разделе "Монитор" отобразится один сеанс.![][33]

**Поздравляем!** Вы успешно выполнили первый шаг этого руководства, суть которого заключалась в подключении приложения к серверной части Mobile Engagement, которая уже отправляет данные.

##<a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

###Настройка приложения для приема push-уведомлений MPNS

Добавьте новые возможности в файл `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

   ![][34]

###Запуск пакета SDK для REACH

1. В `App.xaml.cs` вызовите `EngagementReach.Instance.Init();` в функции **Application\_Launching** сразу после инициализации агента:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. В `App.xaml.cs` вызовите `EngagementReach.Instance.OnActivated(e);` в функции **Application\_Activated** сразу после инициализации агента:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Все готово. Теперь убедимся, что базовая интеграция выполнена правильно.

##<a id="send"></a>Отправка уведомления в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомления в приложение.

1. Перейдите на вкладку **РЕКЛАМНАЯ КАМПАНИЯ** на портале Mobile Engagement.

2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений. ![][35]

3. Выполните следующие шаги, чтобы настроить первое поле кампании: ![][36]

	1. Присвойте кампании любое имя.
	2. Выберите для параметра **Время доставки** значение *Любое время*, чтобы приложение могло принимать уведомления независимо от того, запущено оно или нет.
	3. В тексте уведомления введите заголовок, который будет отображаться полужирным шрифтом в push-уведомлении.
	4. Затем введите текст сообщения.

4. Прокрутите окно вниз и в разделе **Содержимое** выберите **Только уведомления**. ![][37]

5. Вы настроили простейшую базовую кампанию. Теперь прокрутите окно вниз и нажмите кнопку **Создать**, чтобы сохранить кампанию.

6. Напоследок щелкните **Активировать**, чтобы активировать кампанию и начать отправку push-уведомлений. ![][39]

7. На вашем устройстве отобразится уведомление **Поздравляем!**: ![][40]

<!-- URLs. -->
[Пакет SDK для Windows Phone для Mobile Engagement]: http://go.microsoft.com/?linkid=9874664[Mobile документация по пакету SDK для Windows Phone для Engagement]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!---HONumber=August15_HO8-->