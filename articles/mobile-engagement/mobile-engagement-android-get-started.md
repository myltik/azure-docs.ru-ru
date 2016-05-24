<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для приложений Android."
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="05/12/2016"
	ms.author="piyushjo;ricksal" />

# Приступая к работе с Azure Mobile Engagement для приложений Android

[AZURE.INCLUDE [Выбор других руководств](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как применять Azure Mobile Engagement для анализа использования приложения и как отправлять push-уведомления сегментированным пользователям приложения Android. В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. В рассматриваемом сценарии создается пустое приложение Android, которое собирает основные данные и получает push-уведомления с помощью службы Google Cloud Messaging (GCM).

## Предварительные требования

Для прохождения этого учебника требуются [Средства разработчика Android](https://developer.android.com/sdk/index.html), которые включают в себя интегрированную среду разработки Android Studio и новейшую платформу Android.

Вам также понадобится [пакет SDK Android для Mobile Engagement](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Для работы с этим руководством требуется активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## Настройка Mobile Engagement для приложения Android

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal.md)]

## Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Документацию о полной интеграции можно найти в статье [Пакет Android SDK для Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md) в разделе об интеграции.

Создадим основное приложение с помощью Android Studio, чтобы продемонстрировать интеграцию.

### Создание нового проекта Android

1. Запустите **Android Studio** и во всплывающем окне выберите элемент **Start a new Android Studio project** (Создать новый проект Android Studio).

    ![][1]

2. Укажите имя приложения и домен компании. Запишите указанные значения. Они понадобятся вам позднее. Нажмите кнопку **Далее**.

    ![][2]

3. Выберите целевой форм-фактор и уровень API, а затем нажмите кнопку **Next** (Далее).

	>[AZURE.NOTE] Для службы Mobile Engagement необходим уровень API минимум 10 (Android 2.3.3).

    ![][3]

4. Выберите **Blank Activity** (Пустое действие), что означает создание пустого экрана для приложения, и нажмите кнопку **Next** (Далее).

    ![][4]

5. В конце оставьте настройки по умолчанию и нажмите кнопку **Finish** (Готово).

    ![][5]

Android Studio создаст демонстрационное приложение, в которое мы интегрируем Mobile Engagement.

### Включение библиотеки пакета SDK в проект

1. Скачайте [пакет SDK Android для Mobile Engagement].
2. Извлеките файл архива в папку на своем компьютере.
3. Определите библиотеку JAR-файлов для текущей версии этого пакета SDK и скопируйте ее в буфер обмена.

	  ![][6]

4. Перейдите к разделу **Project** (Проект), обозначенному на рисунке цифрой 1, и вставьте JAR-файл в папку с библиотеками (обозначена цифрой 2).

	  ![][7]

5. Синхронизируйте проект, чтобы загрузить библиотеку.

	  ![][8]

### Подключение приложения к серверной части Mobile Engagement с помощью строки подключения

1. Скопируйте следующие строки кода в раздел создания действия (эта операция должна выполняться только в одном месте приложения, обычно в разделе основных действий). Для этого примера приложения откройте файл MainActivity в папке src > main -> java и добавьте следующий код.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Добавьте ссылки, нажав сочетание клавиш ALT+ВВОД или добавив следующие операторы импорта.

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Вернитесь на страницу **Сведения о подключении** классического портала Azure для вашего приложения и скопируйте **строку подключения**.

	  ![][9]

4. Вставьте ее в параметр `setConnectionString`, чтобы заменить стандартное значение, как показано ниже.

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### Добавление разрешений и объявления службы

1. Добавьте эти разрешения в Manifest.xml проекта непосредственно перед тегом `<application>` или после него:

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Добавьте следующий код между тегами `<application>` и `</application>`, чтобы объявить службу агента:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. В только что вставленном коде замените `"<Your application name>"` в метке фактическим значением. Это имя появится в меню **Параметры**, где отображены службы, работающие на устройстве пользователя. Вы можете добавить в метку слово, например Service (служба).

### Отправка экрана в Mobile Engagement

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

Откройте файл **MainActivity.java** и добавьте следующий код, чтобы заменить класс **MainActivity** классом **EngagementActivity**:

	public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Если базовый класс не является *Activity*, см. описание наследования из разных классов в разделе, посвященном [расширенной отчетности в Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).


Вы должны закомментировать (исключить) следующую строку этого простого сценария:

    // setSupportActionBar(toolbar);

Если вы хотите сохранить эту строку, см. раздел, посвященный [расширенной отчетности в Android](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## Подключение приложения с возможностью его отслеживания в режиме реального времени

[AZURE.INCLUDE [Подключение приложения с возможностью его отслеживания в режиме реального времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## Включение push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и СВЯЗЫВАТЬСЯ с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### Копирование ресурсов SDK в проект

1. Вернитесь к содержимому скачанного пакета SDK и скопируйте папку **res**.

	![][10]

2. Вернитесь в Android Studio, выберите каталог **main** в файлах проекта и вставьте его, чтобы добавить ресурсы в проект.

	![][11]

[AZURE.INCLUDE [Включение Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Включение обмена сообщениями внутри приложения](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Отправка уведомления с портала](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## Дальнейшие действия

Подробные сведения об интеграции пакета SDK см. в статье [Пакет Android SDK для Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md).

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png

<!---HONumber=AcomDC_0518_2016-->