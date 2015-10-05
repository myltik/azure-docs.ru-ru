<properties
	pageTitle="Приступая к работе с Azure Mobile Engagement"
	description="Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для приложений Android."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Приступая к работе с Azure Mobile Engagement для приложений Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

В этой статье показано, как применять Azure Mobile Engagement для анализа использования приложения и как отправлять push-уведомления сегментированным пользователям приложения Android. В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. В рассматриваемом сценарии создается пустое приложение Android, которое собирает основные данные и получает push-уведомления с помощью службы Google Cloud Messaging (GCM).

Для работы с данным учебником требуется следующее:

+ Пакет Android SDK (предполагается, что вы будете использовать Android Studio), который можно скачать [здесь](http://go.microsoft.com/fwlink/?LinkId=389797).
+ [Пакет SDK Android для Mobile Engagement].

> [AZURE.IMPORTANT]Выполнение этого учебника является необходимым условием для работы со всеми остальными учебниками, посвященными Mobile Engagement для приложений Android. Для его выполнения необходимо иметь активную учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-RU%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения iOS

[AZURE.INCLUDE [Создание приложения Mobile Engagement на портале](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Документацию о полной интеграции можно найти в статье [Пакет Android SDK для Azure Mobile Engagement](../mobile-engagement-android-sdk-overview/) в разделе об интеграции.

Создадим основное приложение с помощью Android Studio, чтобы продемонстрировать интеграцию.

###Создание нового проекта Android

1. Запустите **Android Studio** и во всплывающем окне выберите элемент **Start a new Android Studio project** (Создать новый проект Android Studio).

    ![][1]

2. Укажите имя приложения и домен компании. Запишите указанные значения. Они понадобятся вам позднее. Нажмите кнопку **Далее**.

    ![][2]

3. Выберите целевой форм-фактор и уровень API, а затем нажмите кнопку **Next** (Далее).
	
	>[AZURE.NOTE]Для службы Mobile Engagement необходим уровень API минимум 10 (Android 2.3.3).

    ![][3]

4. Выберите **Blank Activity** (Пустое действие), что означает создание пустого экрана для приложения, и нажмите кнопку **Next** (Далее).

    ![][4]

5. В конце оставьте настройки по умолчанию и нажмите кнопку **Finish** (Готово).

    ![][5]

Android Studio создаст демонстрационное приложение, в которое мы интегрируем Mobile Engagement.

###Включение библиотеки пакета SDK в проект

Скачайте и интегрируйте библиотеку пакета SDK.

1. Скачайте [пакет SDK Android для Mobile Engagement].
2. Извлеките файл архива в папку на своем компьютере.
3. Определите библиотеку JAR-файлов для текущей версии этого пакета SDK и скопируйте ее в буфер обмена.

	  ![][6]

4. Перейдите к разделу **Project** (Проект), обозначенному на рисунке цифрой 1, и вставьте JAR-файл в папку с библиотеками (обозначена цифрой 2).

	  ![][7]

5. Синхронизируйте проект, чтобы загрузить библиотеку.

	  ![][8]

###Подключение приложения к серверной части Mobile Engagement с помощью строки подключения

1. Скопируйте следующие строки кода в раздел создания действия (эта операция должна выполняться только в одном месте приложения, обычно в разделе основных действий). Для этого примера приложения откройте файл MainActivity в папке src > main -> java и добавьте следующий код.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Добавьте ссылки, нажав сочетание клавиш ALT+ВВОД или добавив следующие операторы импорта.

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Вернитесь на страницу **Сведения о подключении** портала Azure для вашего приложения и скопируйте **строку подключения**.

	  ![][9]

4. Вставьте ее в параметр `setConnectionString`, чтобы заменить стандартное значение, как показано ниже.

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###Добавление разрешений и объявления службы

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

###Отправка экрана в Mobile Engagement

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement.

Откройте файл **MainActivity.java** и добавьте следующий код, используя класс **ActionBarActivity** вместо **EngagementActivity** в качестве базового класса для **MainActivity**.

	public class MainActivity extends EngagementActivity {

##<a id="monitor"></a>Подключение приложения с мониторингом в реальном времени

[AZURE.INCLUDE [Подключение приложения с мониторингом в реальном времени](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и СВЯЗЫВАТЬСЯ с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### Включение обмена сообщениями внутри приложения

1. Скопируйте ресурсы обмена сообщениями внутри приложения в файл Manifest.xml между тегами `<application>` и `</application>`.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. Скопируйте ресурсы в проект, выполнив следующие действия.
	1. Вернитесь к содержимому скачанного пакета SDK и скопируйте папку res.

		 ![][13]

	2. Вернитесь в Android Studio, выберите каталог main в файлах проекта и вставьте его, чтобы добавить ресурсы в проект.

		 ![][14]

###Добавление значка для уведомлений

Вставьте следующий фрагмент XML-кода в файл Manifest.xml между тегами `<application>` и `</application>`.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Так вы определите значок, используемый в системных уведомлениях и уведомлениях в приложении. Это необязательно для уведомлений в приложении, но обязательно для системных уведомлений. Система Android отклоняет системные уведомления с недопустимыми значками.

Используйте значок, который хранится в одной из папок **drawable** (например, ``engagement_close.png``). Папка **mipmap** не поддерживается.

>[AZURE.NOTE]Не следует использовать значок **запуска**. Он имеет другое разрешение и, как правило, находится в неподдерживаемых папках MIP-карт.

В реальных приложениях следует использовать значок, который подходит для уведомлений в соответствии с [рекомендациями по разработке для Android](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP]Чтобы правильно выбрать разрешение значка, ознакомьтесь с [этими примерами](https://www.google.com/design/icons). Прокрутите страницу вниз до раздела **Notification** (Уведомления), щелкните значок и выберите команду `PNGS`, чтобы загрузить набор рисунков. Вы увидите, какие папки рисунков и с каким разрешением следует использовать для каждой версии значка.

###Настройка приложения для приема push-уведомлений GCM

1. После замены номера `project number`, полученного из консоли Google Play, вставьте следующий код в файл Manifest.xml между тегами `<application>` и `</application>`. Перенос строки (\\n) указан преднамеренно, поэтому добавьте его в конец номера проекта.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Вставьте приведенный ниже код в Manifest.xml между тегами `<application>` и `</application>`. Замените имя пакета <Your package name>.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Добавьте последний выделенный набор разрешений перед тегом `<application>`. Замените `<Your package name>` фактическим именем пакета вашего приложения.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Предоставление Mobile Engagement доступа к ключу API GCM

Чтобы разрешить службе Mobile Engagement отправлять push-уведомления от вашего имени, необходимо предоставить ей доступ к ключу API. Это можно сделать, настроив и введя ключ в портал Mobile Engagement.

1. Перейдите на портал Mobile Engagement

	На портале Azure войдите в приложение, которое используется для этого проекта, и нажмите кнопку **Выполнить охват** внизу страницы.

	![][15]

2. Затем последовательно выберите **Параметры** и **Системное push-уведомление**, чтобы ввести ключ GCM.
	  
	![][16]

3. В разделе **Параметры GCM** возле элемента **Ключ API** щелкните значок **правки**, как показано ниже:
	  
	![][17]

4. Во всплывающем окне вставьте ключ GCM-сервера, полученный в разделе [Включение Google Cloud Messaging](#register), а затем нажмите кнопку **ОК**.

	![][18]

##<a id="send"></a>Отправка уведомления в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомления в приложение.

1. Перейдите на вкладку **РЕКЛАМНАЯ КАМПАНИЯ** на портале Mobile Engagement.
	 
2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений.
	 
	![][20]

3. Выполните следующие шаги, чтобы настроить первое поле кампании:
	 
	![][21]

	а. Присвойте имя кампании.

	b. Выберите для параметра **Тип доставки** значение *Системное уведомление -> Простое*. Это простой тип push-уведомлений Android, в состав которых входит заголовок и небольшая строка текста.

	c. Выберите для параметра **Время доставки** значение *Любое время*, чтобы приложение могло принимать уведомления независимо от того, запущено оно или нет.

	г) В тексте уведомления введите **заголовок**, который будет отображаться полужирным шрифтом в push-уведомлении.

	д. Затем введите текст **сообщения**.

4. Прокрутите окно вниз и в разделе **Содержимое** выберите **Только уведомления**.

	![][22]

5. Вы настроили простейшую базовую кампанию. Теперь прокрутите окно вниз и нажмите кнопку **Создать**, чтобы сохранить кампанию.

6. Напоследок щелкните **Активировать**, чтобы активировать кампанию и начать отправку push-уведомлений.
    
	![][24]

<!-- URLs. -->
[Пакет SDK Android для Mobile Engagement]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682
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
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=Sept15_HO4-->