<properties 
	pageTitle="Приступая к работе с Azure Mobile Engagement" 
	description="Сведения об использовании Azure Mobile Engagement со средствами аналитики и push-уведомлениями." 					services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Приступая к работе с Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Магазин Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android" class="current">Android</a></div>

В этом разделе рассматривается применение Azure Mobile Engagement для получения информации об использовании приложения и отправки push-уведомлений сегментированным пользователям приложения Android. 
В данном учебнике продемонстрирован простой сценарий рассылки с использованием службы Mobile Engagement. В рассматриваемом сценарии создается пустое приложение Android, которое собирает основные данные и получает push-уведомления с помощью службы Google Cloud Messaging (GCM). После завершения учебника вы сможете рассылать push-уведомления всем устройствам или целевым пользователям на основе свойств их устройств. Рекомендуем изучить следующий учебник, в котором рассматривается использование службы Mobile Engagement для охвата определенных пользователей и групп устройств.


Для работы с данным учебником требуется следующее:

+ Пакет Android SDK (предполагается использование Android Studio), который можно загрузить [здесь](http://go.microsoft.com/fwlink/?LinkId=389797)
+ [Пакет Android SDK для Mobile Engagement]

> [AZURE.IMPORTANT] Выполнение этого учебника является необходимым условием для работы со всеми остальными учебниками, посвященными Mobile Engagement для приложений Android. Для его выполнения необходимо иметь активную учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Настройка службы Mobile Engagement для приложения

1. Войдите на [Портал управления Azure], а затем нажмите кнопку **+СОЗДАТЬ** в нижней части экрана.

2. Щелкните **Службы приложений**, далее **Mobile Engagement** и затем **Создать**.

   	![][7]

3. В появившемся всплывающем меню введите следующие сведения.
 
   	![][8]

	1. **Имя приложения**: можно ввести имя приложения. Допускается использование любых символов.
	2. **Платформа**: выберите целевую платформу для этого приложения (если приложение предназначено для нескольких платформ, повторите данный учебник для каждой платформы).
	3. **Имя ресурса приложения**: Это имя, по которому к приложению будет осуществляться доступ через API и URL-адреса. Рекомендуем использовать только обычные символы для URL-адреса. Автоматически созданное имя должно послужить для этого надежной основой. Кроме этого, рекомендуется добавить имя платформы, чтобы избежать конфликта имен, поскольку данное имя должно быть уникальным.
	4. **Расположение**: выберите центр обработки данных, в котором будет размещаться данное приложение (и что важнее, его коллекция - см. ниже).
	5. **Коллекция**: Если приложение уже создано, выберите ранее созданную коллекцию, в противном случае выберите Создать коллекцию.
	6. **Имя коллекции**: Оно представляет группу приложений. Оно также гарантирует нахождение всех приложений в группе, что позволит выполнять общие вычисления. Настоятельно рекомендуется использовать название вашей компании или отдела.


	Закончив, нажмите кнопку "Проверить", чтобы завершить создание приложения.

4. Теперь щелкните или выберите приложение, которое было только что создано, на вкладке **Приложение**.
 
   	![][9]

5. Далее щелкните **Сведения о подключении** для отображения параметров подключения, которые необходимо поместить в интеграцию пакета SDK.
 
   	![][10]

6. И наконец, запишите **строку подключения**, которая понадобится для идентификации данного приложения из кода вашего приложения.

   	![][11]

	>[AZURE.TIP] Чтобы для удобства скопировать его в буфер обмена, можно воспользоваться значком "Копировать", который находится справа от строки подключения.

##<a id="connecting-app"></a>Подключение приложения к внутреннему серверу Mobile Engagement

В этом учебнике приводится базовая интеграция, которая представляет собой минимально необходимый набор для сбора данных о отправки push-уведомлений. Документацию о полной интеграции можно найти в [документации пакета Android SDK для Mobile Engagement].

МЫ создадим основное приложения с помощью Android Studio для демонстрации интеграции.

###Создание нового проекта Android

Можно пропустить этот шаг, если у вас уже есть приложение, и вы знакомы с разработкой Android.

1. Запустите Android Studio и выберите во всплывающем окне **Начать новый проект Android Studio**.

   	![][12]

2. Введите имя приложения и домен компании. Запишите их, так как они понадобятся вам позже, а затем нажмите кнопку **Далее**.

   	![][13]

3. Теперь выберите целевой форм-фактор и уровень API, а затем нажмите кнопку **Далее**. 

	>[AZURE.NOTE] Для службы Mobile Engagement необходим уровень API минимум 10 (Android 2.3.3).

   	![][14]

4. Сейчас добавим действие к нашему простому приложению, которое будет его основным и единственным экраном. Не забудьте выбрать **Пустое действие** и нажмите кнопку **Далее**.

   	![][15]

5. В последнем экране мастера можете оставить все без изменения для целей данного учебник и нажмите кнопку **Готово**.

   	![][16]

Android Studio создаст демонстрационное приложение, к которому будет выполняться интеграция Mobile Engagement.

###Включение библиотеке пакет SDK в проект

Загрузите и интегрируйте библиотеку пакета SDK

1. Загрузите [Пакет Android SDK для Mobile Engagement].
2. Извлеките файл архива в папку на своем компьютере.
3. Определите библиотеку JAR-файлов для текущей версии этого пакета SDK (данная документация была подготовлена для версии 3.0.0) с скопируйте его в буфер обмена.

	![][17]

4. Перейдите к разделу проекта (1) и вставьте JAR-файл в папку библиотек (2).

	![][18]

5. Синхронизируйте проект, чтобы загрузить библиотеку.

	![][19]


###Подключите приложение к внутреннему серверу Mobile Engagement с помощью строки подключения.

1. Скопируйте следующие строки кода в раздел создания приложения (эта операция должна выполняться только в одном месте приложения, обыкновенно в разделе основных действий).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Вернитесь на портал Azure в странице **Сведения о подключении** приложения и скопируйте **строку подключения**.

	![][11]

3. Вставьте ее в параметр `setConnectionString`, чтобы заменить приведенный пример, как показано ниже (AppId и Sdkkey были скрыты ниже).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. Классы EngagementConfiguration и EngagementAgent вероятно будут показаны как неразрешенные (красным цветом в коде). Щелкните каждый из неразрешенных классов, а затем нажмите Alt-Enter, чтобы их автоматически разрешить.

	![][20]

###Добавление разрешений и объявления службы

1. Добавьте эти разрешения в Manifest.xml проекта непосредственно перед тегом `<application>`:
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>


2. Добавьте следующий код между тегами < application > и </application >, чтобы объявить службу агента:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. В только что вставленном коде замените "< Your application name>" в метке. Например:

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

###Отправка экрана в Mobile Engagement

Чтобы начать отправлять данные и обеспечить активность пользователей, необходимо отправить как минимум один экран (действие) внутреннему серверу Mobile Engagement. Мы сделаем это путем создания подклассов действия с помощью EngagementActivity, которая предоставляется пакетом SDK.
Чтобы выполнить это, замените класс super у MainActivity, который располагается перед ActionBarActivity, на EngagementActivity, как показано ниже:

![][22]

>[AZURE.NOTE] Не забудьте разрешить класс, поскольку он отображается красным цветом, щелкнув его и нажав Alt-Enter.

##<a id="monitor"></a>Проверка подключения приложения с помощью мониторинга в реальном времени

В этом разделе показано, как убедиться в том, что приложение подключено в внутреннему серверу Mobile Engagement с помощью компонента мониторинга в реальном времени службы Mobile Engagement.

1. Перейдите на портал Mobile Engagement.

	На портале Azure убедитесь, что вы находитесь в приложении, которое используется для данного проекта, а затем нажмите кнопку **Engage** (Использовать), расположенную в нижней части.

	![][26]

2. Вы попадете на страницу параметров портала Engagement для вашего приложения. На портале щелкните вкладку **Монитор**, как показано ниже.
	![][30]

3. Монитор готов для отображения любого устройства в реальном времени, которое запустит приложение.
	![][31]

4. Вернитесь Android Studio, запустите приложение в мониторе или в подключенном устройстве, нажав на зеленый треугольник, а затем выбрав свое устройство.
	![][32]

5. Если все сработало, вы должны увидеть в мониторе один сеанс! 
	![][33]

**Поздравляем!** Вы успешно выполнили первый шаг этого учебника с помощью приложения, подключаемого к внутреннему серверу, который уже отправляет данные.


##<a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении

Служба Mobile Engagement позволяет взаимодействовать и использовать модуль обработки рекламных кампаний (REACH) при работе с пользователями с помощью push-уведомлений и обмена сообщениями внутри приложения в контексте кампаний. Этот модуль называется REACH на портале Mobile Engagement.
В последующих разделах будет выполнена настройка приложения для их получения.

### Включение обмена сообщениями внутри приложения

1. Скопируйте ресурсы обмена сообщениями внутри приложения в Manifest.xml между тегами < application > и </application >.

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
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>

2. Скопируйте ресурсы в проект, выполнив следующие действия.
	1. Перейдите назад к содержимому загрузки пакета SDK и откройте папку 'res'.
	2. Выберите две папки и скопируйте их в буфер обмена.

		![][23]

	4. Вернитесь в Android Studio, выберите часть проекта 'res' и вставьте, чтобы добавить ресурсы к проекту.

		![][24]

###Задание значка по умолчанию в уведомлениях
В следующем коде задается значок по умолчанию, который будет отображаться вместе с уведомлениями. Здесь используется значок, который предоставляется вместе с проектом, созданным с помощью Android Studio. Этот фрагмент кода xml необходимо вставить в Manifest.xml между тегами < application > и </application >

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###Включение приложения для получения push-уведомлений GCM

1. Введите метаданные gcm:отправитель, выполнив копирование и вставку в Manifest.xml между тегами < application > и </application >. Скрытое значение ниже (указанное звездочками) - это `project number`, полученный с консоли Google Play.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Вставьте приведенный ниже код в Manifest.xml между тегами < application > и </application >. Обратите внимание, что в `<category android:name="com.mycompany.mysuperapp" />` использовано имя пакета проекта. В вашем рабочем проекте оно будет другим.

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
				<category android:name="com.mycompany.mysuperapp" />
			</intent-filter>
		</receiver>

3. Добавьте последний набор разрешений, выделенный ниже, перед тегом < application>. Еще раз отметим, что использовалось это имя пакета проекта, которое вам необходимо заменить в своем рабочем приложении.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Предоставление доступа в ключу API GCM для Mobile Engagement

Чтобы разрешить службе Mobile Engagement отправлять push-уведомления от вашего имени, необходимо предоставить ей доступ к ключу API. Это можно сделать, настроив и введя ключ в портал Mobile Engagement.

1. Перейдите к порталу Mobile Engagement

	На портале Azure убедитесь, что вы находитесь в приложении, которое используется для данного проекта, а затем нажмите кнопку **Использовать**, расположенную в нижней части.

	![][26]

2. Теперь вы попадете на страницу параметров портала Engagement. На портале щелкните раздел **Собственные push-уведомления**, чтобы ввести ключ GCM.
	![][27]

3. Щелкните значок изменений перед **ключом API ** в разделе параметров GCM, как показано ниже.
	![][28]

4. Во всплывающем окне вставьте ключ сервера GCM, который был получен в разделе [Включение службы Google Cloud Messaging](#register) Затем нажмите кнопку **Ok**.

	![][29]

Все готово. Теперь займемся проверкой, правильно ли сделана базовая интеграция.

> [AZURE.IMPORTANT] Не забудьте выполнить построение, запустить с помощью нового кода, выйти из приложения и подождать порядка 1 минуты перед тем, как проделать следующие действия

##<a id="send"></a>Отправка уведомлений в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомление нашему приложению.

1. Перейдите на вкладку модуля **REACH** на портале Mobile Engagement.
	![][34]

2. Щелкните **Новое объявление**, чтобы создать кампанию push-уведомлений.
	![][35]

3. Настройте первое поле кампании, выполнив следующие действия.
	![][36]

	1. Присвойте своей кампании любое имя.
	2. Выберите **Тип доставки** как *System notification / Simple*: Это простой тип push-уведомлений Android, в состав которых входит заголовок и небольшая строка текста.
	3. Выберите **Время доставки** как *Any time*, чтобы позволить приложению получать уведомления вне зависимости от того, запущено оно или нет.
	4. В текст уведомления введите заголовок, который будет выделяться полужирным шрифтом в push-уведомлении.
	5. Затем введите свое сообщение.

4. Прокрутите вниз и в разделе содержимого выберите **Только уведомления**.
	![][37]

5. Настройка наиболее базовой кампании закончена. Теперь снова прокрутите вниз и создайте свою кампанию, чтобы сохранить ее.
![][38]

6. И наконец, активируйте свою кампанию.
![][39]


<!-- URLs. -->
[Пакет Android SDK для Mobile Engagement]: http://go.microsoft.com/?linkid=9863935
[Документация пакета Android SDK для Mobile Engagement]: http://go.microsoft.com/?linkid=9874682
[Портал управления Azure]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!--HONumber=47-->
