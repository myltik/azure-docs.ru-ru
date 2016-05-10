<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement для универсальных приложений для Windows"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для универсальных приложений для Windows."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="piyushjo" />

# Приступая к работе с Azure Mobile Engagement для универсальных приложений для Windows

[AZURE.INCLUDE [Выбор других руководств](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этом разделе показано, как применять Azure Mobile Engagement для анализа использования приложений и отправки push-уведомлений сегментированным пользователям универсального приложения для Windows. В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. Вы создадите пустое универсальное приложение для Windows, которое будет собирать основные данные об использовании приложений и получать push-уведомления, используя службу push-уведомлений Windows (WPNS).

Для работы с данным учебником требуется следующее:

+ Visual Studio 2013
+ Пакет NuGet [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-store-dotnet-get-started).

##<a id="setup-azme"></a>Настройка Mobile Engagement для универсального приложения для Windows

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции см. в статье [Интеграция пакета Mobile Engagement Windows Universal SDK](mobile-engagement-windows-store-sdk-overview.md)

Мы создадим базовое приложение в Visual Studio, чтобы продемонстрировать интеграцию.

###Создание проекта универсального приложения для Windows

Указанные ниже действия предполагают использование Visual Studio 2015, хотя они и аналогичны действиям с использованием более ранних версий Visual Studio.

1. Запустите Visual Studio и на **начальном** экране выберите пункт **Создать проект**.

2. Во всплывающем окне последовательно выберите пункты **Windows 8**, **Универсальное** и **Пустое приложение (универсальное для Windows 8.1)**. Введите необходимые значения в поля **Имя** и **Имя решения**, а затем нажмите кнопку **ОК**.

    ![][1]

Вы создали универсальное приложение для Windows, в которое мы интегрируем пакет SDK для Azure Mobile Engagement.

###Подключите приложение к серверной части Mobile Engagement.

1. Установите пакет NuGet [MicrosoftAzure.MobileEngagement] в проект. Если приложение предназначено как для платформы Windows, так и для Windows Phone, это понадобится сделать для обоих проектов. Для Windows 8.x и Windows Phone 8.1 один и тот же пакет NuGet добавляет нужные двоичные файлы для определенной платформы в каждый проект.

2. Откройте файл **Package.appxmanifest** и добавьте в него указанную ниже возможность.

		Internet (Client)

	![][2]

3. Теперь вставьте строку подключения (которую вы скопировали ранее для приложения Mobile Engagement) в файл `Resources\EngagementConfiguration.xml` между тегами `<connectionString>` и `</connectionString>`.

	![][3]

	>[AZURE.TIP] Если приложение будет рассчитано как на платформу Windows, так и на Windows Phone, то следует создать два приложения Mobile Engagement (по одному для каждой из поддерживаемых платформ). Это позволит правильно сегментировать аудиторию и отправлять целевые уведомления для каждой платформы.

4. В файле `App.xaml.cs`:

	а. Добавьте оператор `using`:

			using Microsoft.Azure.Engagement;

	b. Добавьте метод для инициализации и настройки Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

			 //... rest of the code
           }

    c. Инициализируйте пакет SDK в методе **OnLaunched**:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  InitEngagement(e);

			  //... rest of the code
			}

	c. Вставьте указанный ниже код в метод **OnActivated** и добавьте метод, если его еще нет.

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  InitEngagement(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Включение мониторинга в режиме реального времени

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

1. 	В файле **MainPage.xaml.cs** добавьте следующий оператор `using`:

		using Microsoft.Azure.Engagement.Overlay;

2. Замените на базовый класс **MainPage** из **Page** для **EngagementPage**.

		class MainPage : EngagementPageOverlay

3. В файле `MainPage.xaml`:

	а. Добавьте в объявления пространств имен:

		xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

	b. В имени XML-тега измените **Page** на **engagement:EngagementPageOverlay**.
	
> [AZURE.IMPORTANT] Если страница переопределяет метод `OnNavigatedTo`, необходимо обязательно вызвать `base.OnNavigatedTo(e)`. В противном случае о действии не будет сообщено (`EngagementPage` вызывает `StartActivity` внутри метода `OnNavigatedTo`). Это особенно важно в проекте Windows Phone, где шаблон по умолчанию включает метод `OnNavigatedTo`.

##<a id="monitor"></a>Подключение приложения с мониторингом в реальном времени

[AZURE.INCLUDE [Подключение приложения с мониторингом в реальном времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями при помощи push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

###Настройка приложения для приема push-уведомлений WNS

1. В файле `Package.appxmanifest` откройте вкладку **Приложение** и в разделе **Уведомления** для параметра **Всплывающие уведомления** укажите значение **Да**.

	![][5]

###Запуск пакета SDK для REACH

В файле `App.xaml.cs` вызовите **EngagementReach.Instance.Init(e);** в функции **InitEngagement** сразу после инициализации агента.

        private void InitEngagement(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Все готово к отправке всплывающего уведомления. Теперь убедимся, что базовая интеграция выполнена правильно.

###Предоставление доступа к Mobile Engagement для отправки уведомлений

1. Откройте в веб-браузере [Центр разработки Магазина Windows], войдите и при необходимости создайте учетную запись.
2. Щелкните **Панель мониторинга** в верхнем правом углу, а затем нажмите кнопку **Создать приложение** в меню на панели слева. 

	![][9]

2. Создайте приложение, резервируя его имя.

	![][10]

3. Создав приложение, выберите в меню слева пункты **Службы -> Push-уведомления**.

	![][11]

4. В разделе рush-уведомлений перейдите по ссылке **Сайт служб Live**.

	![][12]

5. Вы перейдете в раздел учетных данных рush-уведомлений. В разделе **Параметры приложения** скопируйте **идентификатор безопасности пакета** и **секрет клиента**.

	![][13]

6. Перейдите в область **Параметры** портала Mobile Engagement и щелкните раздел **Системное push-уведомление** слева. Нажмите кнопку **Изменить**, чтобы ввести данные в поля **Идентификатор безопасности пакета (SID)** и **Секретный ключ**, как показано ниже.

	![][6]

8. Наконец вам нужно связать свое приложение Visual Studio и созданное приложение в магазине приложений. Для этого щелкните в Visual Studio **Связать приложение с магазином**.

	![][7]

##<a id="send"></a>Отправка уведомления в приложение

[AZURE.INCLUDE [Создание кампании push-уведомлений Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Если приложение запущено, вы увидите соответствующее уведомление, а если приложение закрыто, то всплывающее уведомление. Если отображается уведомление в приложении, а не всплывающее уведомление, а само приложение выполняется в Visual Studio в режиме отладки, на панели инструментов щелкните **События жизненного цикла -> Приостановить**. Так вы убедитесь в том, что приложение действительно приостановлено. Просто нажатие кнопки домашней страницы при отладке приложения в Visual Studio не всегда означает приостановку, и, пока уведомление выводится как уведомление в приложении, оно не отображается как всплывающее уведомление.

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Центр разработки Магазина Windows]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png

<!---HONumber=AcomDC_0504_2016-->