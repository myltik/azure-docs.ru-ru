<properties
	pageTitle="Начало работы с Azure Mobile Engagement для Xamarin.Android"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для приложений Xamarin.Android."
	services="mobile-engagement"
	documentationCenter="xamarin"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Начало работы с Azure Mobile Engagement для приложений Xamarin.Android

[AZURE.INCLUDE [Выбор других руководств](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как применять Azure Mobile Engagement для анализа использования приложения и как отправлять push-уведомления сегментированным пользователям приложения Xamarin.Android. В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. В рассматриваемом сценарии создается пустое приложение Xamarin.Android, которое собирает основные данные и получает push-уведомления с помощью службы Google Cloud Messaging (GCM).

Для работы с данным учебником требуется следующее:

+ [Xamarin Studio](http://xamarin.com/studio) (можно также использовать Visual Studio с расширением Xamarin, но в этом руководстве в инструкциях используется Xamarin Studio);
+ [пакет Xamarin SDK для Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/).

> [AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения Android

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений.

Мы создадим базовое приложение в Xamarin Studio, чтобы продемонстрировать интеграцию.

###Создание нового проекта Xamarin.Android

1. Запустите **Xamarin Studio**. Последовательно щелкните **File** -> **New** -> **Solution** (Файл -> Создать -> Решение). 

    ![][1]

2. Выберите **Android App** (Приложение Android), затем выберите язык **C#** и нажмите кнопку **Next** (Далее).

    ![][2]

3. Заполните поля **App Name** (Имя приложения) и **Organization Identifier** (Идентификатор организации). Установите флажок **Google Play Services** (Службы Google Play) и нажмите кнопку **Next** (Далее).

    ![][3]
	
4. Если требуется, обновите значения в полях **Project Name** (Имя проекта), **Solution Name** (Имя решения) и **Location** (Расположение), а затем нажмите кнопку **Create** (Создать).

    ![][4]
 
Xamarin Studio создаст приложение, в которое мы интегрируем Mobile Engagement.

###Подключение приложения к серверной части Mobile Engagement

1. В окне решения правой кнопкой мыши щелкните папку **Packages** (Пакеты) и выберите пункт **Add Packages** (Добавить пакеты).

    ![][5]

2. Найдите пакет **Microsoft Azure Mobile Engagement Xamarin SDK** и добавьте его в свое решение.

    ![][6]
   
3. Откройте файл **MainActivity.cs** и добавьте следующие инструкции using:

		using Microsoft.Azure.Engagement;
		using Microsoft.Azure.Engagement.Activity;

4. В метод `OnCreate` добавьте следующую команду, чтобы инициализировать подключение к внутреннему серверу Mobile Engagement. Обязательно добавьте **строку подключения**.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
		EngagementAgent.Init(engagementConfiguration);

###Добавление разрешений и объявления службы

1. Откройте файл **Manifest.xml** в папке Properties (Свойства). Перейдите на вкладку Source (Источник), чтобы напрямую обновить источник XML.
 
2. Добавьте эти разрешения в файл Manifest.xml (который можно найти в папке **Properties** (Свойства)) проекта непосредственно перед тегом `<application>` или после него:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Добавьте следующий код между тегами `<application>` и `</application>`, чтобы объявить службу агента:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

4. В только что вставленном коде замените `"<Your application name>"` в метке фактическим значением. Это имя появится в меню **Параметры**, где отображены службы, работающие на устройстве пользователя. Вы можете добавить в метку слово, например Service (служба).

###Отправка экрана в Mobile Engagement

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран на внутренний сервер Mobile Engagement. Для этого убедитесь, что `MainActivity` наследуется от `EngagementActivity`, а не от `Activity`.

	public class MainActivity : EngagementActivity

##<a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени

[AZURE.INCLUDE [Подключение приложения с мониторингом в реальном времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и СВЯЗЫВАТЬСЯ с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

[AZURE.INCLUDE [Включение Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Включение обмена сообщениями внутри приложения](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Отправка уведомления с портала](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png

<!---HONumber=AcomDC_0330_2016-->