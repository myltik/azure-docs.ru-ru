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
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="piyushjo" />
	
# Начало работы с Azure Mobile Engagement для приложений Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)

В этом разделе рассматривается применение Azure Mobile Engagement для получения информации об использовании приложения и отправки push-уведомлений сегментированным пользователям приложения Android. В этом учебнике описывается простой сценарий вещания с использованием Mobile Engagement. В рассматриваемом сценарии создается пустое приложение Android, которое собирает основные данные и получает push-уведомления с помощью службы Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления на все устройства или взять за цель конкретных пользователей на основе свойств их устройств. Рекомендуем вам изучить следующий учебник, чтобы узнать об использовании Mobile Engagement для охвата определенных пользователей и групп устройств.


Для работы с данным учебником требуется следующее:

+ Пакет Android SDK (предполагается, что вы будете использовать Android Studio), который можно скачать [здесь](http://go.microsoft.com/fwlink/?LinkId=389797).
+ [Пакет SDK Android для Mobile Engagement].

> [AZURE.IMPORTANT]Выполнение этого учебника является необходимым условием для работы со всеми остальными учебниками, посвященными Mobile Engagement для приложений Android. Для его выполнения необходимо иметь активную учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения

1. Войдите на [портал управления Аzure] и нажмите элемент **+СОЗДАТЬ** в нижней части экрана.

2. Щелкните **Службы приложений**, **Mobile Engagement**, а затем — **Создать**.

   	![][7]

3. Во всплывающем окне введите следующую информацию:
 
   	![][8]

	1. **Имя приложения**: введите имя своего приложения. Вы можете использовать любые символы.
	2. **Платформа**: выберите целевую платформу для этого приложения (если приложение нацелено на несколько платформ, изучите этот учебник для каждой платформы).
	3. **Имя ресурса приложения**. Это имя, по которому приложение можно будет открывать через API и по URL-адресам. Мы рекомендуем использовать обычные символы URL-адреса: автоматически созданное имя должно предоставлять твердую основу. Мы также рекомендуем добавить имя платформы, чтобы избежать конфликта имен, так как это имя должно быть уникальным.
	4. **Местоположение**: выберите центр обработки данных, в котором будет размещено это приложение (и, что еще важнее, его коллекция — см. ниже).
	5. **Коллекция**. Если вы уже создали приложение, то выберите созданную ранее коллекцию, а если нет, то выберите "Создать коллекцию".
	6. **Имя коллекции**. Указывает на выбранную группу приложений. Если задать это имя, все приложения будут находиться в одной группе, что позволит выполнять общие вычисления. Настоятельно рекомендуем использовать название вашей компании или отдела.


	По окончании нажмите кнопку с галочкой, чтобы завершить создание приложения.

4. Теперь щелкните или выберите только что созданное приложение на вкладке **Приложение**.
 
   	![][9]

5. Затем щелкните **Сведения о подключении**, чтобы отобразить параметры подключения, которые следует использовать при интеграции пакета SDK.
 
   	![][10]

6. После этого введите значение в поле **Строка подключения**, которая понадобится для определения этого приложения в коде приложения.

   	![][11]

	>[AZURE.TIP]Для удобства вы можете использовать значок «Копировать» справа от поля «Строка подключения», чтобы скопировать название в буфер обмена.

##<a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement

В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений. Документацию о полной интеграции можно найти в [документации пакета SDK Android для Mobile Engagement].

Создадим основное приложение с помощью Android Studio, чтобы продемонстрировать интеграцию.

###Создание нового проекта Android

Можно пропустить этот шаг, если у вас уже есть приложение, и вы знакомы с разработкой Android.

1. Запустите Android Studio и выберите во всплывающем окне **Начать новый проект Android Studio**.

   	![][12]

2. Введите имя приложения и домен компании. Запишите их, так как они потребуются вам позже, а затем нажмите кнопку **Далее**.

   	![][13]

3. Теперь выберите целевой форм-фактор и уровень API, а затем нажмите кнопку **Далее**.
	>[AZURE.NOTE]Для службы Mobile Engagement необходим уровень API минимум 10 (Android 2.3.3).

   	![][14]

4. Сейчас добавим действие к нашему простому приложению, которое будет его основным и единственным экраном. Убедитесь, что выбрано значение **Пустое действие**, и нажмите кнопку **Далее**.

   	![][15]

5. На последнем экране мастера вы можете оставить все без изменения для целей данного учебника. Нажмите кнопку **Готово**.

   	![][16]

Android Studio создаст демонстрационное приложение, к которому будет выполняться интеграция Mobile Engagement.

###Включение библиотеки пакета SDK в проект

Скачайте и интегрируйте библиотеку пакета SDK.

1. Скачайте [пакет SDK Android для Mobile Engagement].
2. Извлеките файл архива в папку на своем компьютере.
3. Определите библиотеку JAR-файлов для текущей версии этого пакета SDK (данная документация была подготовлена для версии 3.0.0) с скопируйте его в буфер обмена.

	![][17]

4. Перейдите к разделу проекта (1) и вставьте JAR-файл в папку библиотек (2).

	![][18]

5. Синхронизируйте проект, чтобы загрузить библиотеку.

	![][19]


###Подключение приложения к серверной части Mobile Engagement с помощью строки подключения

1. Скопируйте следующие строки кода в раздел создания приложения (эта операция должна выполняться только в одном месте приложения, обыкновенно в разделе основных действий).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Вернитесь на страницу **Сведения о подключении** портала Azure для вашего приложения и скопируйте **строку подключения**.

	![][11]

3. Вставьте ее в параметр `setConnectionString`, чтобы заменить приведенный пример, как показано ниже (AppId и Sdkkey скрыты).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. Классы EngagementConfiguration и EngagementAgent вероятно будут показаны как неразрешенные (красным цветом в коде). Щелкните каждый из неразрешенных классов, а затем нажмите Alt-Enter, чтобы их автоматически разрешить.

	![][20]

###Добавление разрешений и объявления службы

1. Добавьте эти разрешения в Manifest.xml проекта непосредственно перед тегом `<application>` или после него:
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	Вы должны получить результат, показанный ниже:

	![][21]

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

Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Mobile Engagement. Мы сделаем это путем создания подклассов действия с помощью EngagementActivity, которая предоставляется пакетом SDK. Чтобы выполнить это, замените класс super у MainActivity, который располагается перед ActionBarActivity, на EngagementActivity, как показано ниже:

![][22]

>[AZURE.NOTE]Не забудьте разрешить класс, поскольку он отображается красным цветом, щелкнув его и нажав Alt-Enter.

##<a id="monitor"></a>Как проверить, что приложение подключено, с помощью мониторинга в реальном времени

В этом разделе показано, как сделать так, чтобы приложение подключалось к серверной части Mobile Engagement с помощью функции мониторинга в реальном времени.

1. Перейдите на портал Mobile Engagement.

	На портале Azure убедитесь, что вы находитесь в приложении, которое используется для данного проекта, а затем нажмите кнопку **Использовать** внизу.

	![][26]

2. Вы перейдете на страницу параметров вашего приложения на портале Engagement. На этой странице щелкните вкладку **Монитор**, как показано ниже. ![][30]

3. На вкладке "Монитор" в режиме реального времени показываются все устройства, которые запускают приложение. ![][31]

4. Вернитесь в Android Studio, запустите приложение в мониторе или на подключенном устройстве, щелкнув зеленый треугольник, а затем выбрав свое устройство. ![][32]

5. Если все сделано правильно, в разделе "Монитор" отобразится один сеанс.![][33]

**Поздравляем!** Вы успешно выполнили первый шаг этого учебника, суть которого заключалась в подключении приложения к серверной части Mobile Engagement, которая уже отправляет данные.


##<a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении

Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ. В следующих разделах описаны действия по настройке приложения для приема уведомлений и сообщений.

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
	1. Перейдите назад к скачанному содержимому пакета SDK и откройте папку res.
	2. Выберите две папки и скопируйте их в буфер обмена.

		![][23]

	4. Вернитесь в Android Studio, выберите часть res проекта и вставьте ее, чтобы добавить ресурсы в проект.

		![][24]

###Задание значка по умолчанию в уведомлениях
В следующем коде задается значок по умолчанию, который будет отображаться вместе с уведомлениями. Здесь используется значок, который предоставляется вместе с проектом, созданным с помощью Android Studio. Этот фрагмент XML-кода необходимо вставить в Manifest.xml между тегами < application > и </application >. Убедитесь, что в приложении есть ic_launcher или другой файл значка. В противном случае уведомление не будет отображаться.

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###Включение приложения для получения push-уведомлений GCM

1. Введите метаданные gcm:отправитель, выполнив копирование и вставку в Manifest.xml между тегами < application > и </application >. Скрытое значение ниже (указанное звездочками) — это `project number`, полученный из консоли Google Play. Перенос строки (\\n) указан преднамеренно, поэтому добавьте его в конец номера проекта. 

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Вставьте приведенный ниже код в Manifest.xml между тегами < application > и </application >. Обратите внимание, что в `<category android:name="com.mycompany.mysuperapp" />` мы использовали имя пакета проекта. В вашем рабочем проекте оно будет другим.

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

3. Добавьте последний набор разрешений, выделенный ниже, перед тегом < application> или после него. Еще раз отметим, что использовалось это имя пакета проекта, которое вам необходимо заменить в своем рабочем приложении.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Предоставление доступа в ключу API GCM для Mobile Engagement

Чтобы разрешить службе Mobile Engagement отправлять push-уведомления от вашего имени, необходимо предоставить ей доступ к ключу API. Это можно сделать, настроив и введя ключ в портал Mobile Engagement.

1. Перейдите на портал Mobile Engagement

	На портале Azure убедитесь, что вы находитесь в приложении, которое используется для данного проекта, а затем нажмите кнопку **Использовать** внизу.

	![][26]

2. Теперь вы попадете на страницу параметров портала Engagement. Щелкните раздел **Системное push-уведомление**, чтобы ввести ключ GCM: ![][27]

3. Щелкните значок правки перед **ключом API** в разделе параметров GCM, как показано ниже: ![][28]

4. Во всплывающем окне вставьте ключ GCM-сервера, который был получен в разделе [Включение Google Cloud Messaging](#register), а затем нажмите кнопку **ОК**.

	![][29]

Все готово. Осталось только проверить, правильно ли выполнена эта базовая интеграция.

> [AZURE.IMPORTANT]Не забудьте выполнить построение, запустить с помощью нового кода, выйти из приложения и подождать порядка 1 минуты перед тем, как проделать следующие действия

##<a id="send"></a>Отправка уведомления в приложение

Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомления в приложение.

1. Перейдите на вкладку **РЕКЛАМНАЯ КАМПАНИЯ** на портале Mobile Engagement. ![][34]

2. Щелкните **Создать объявление**, чтобы создать кампанию push-уведомлений. ![][35]

3. Выполните следующие шаги для настройки первого поля кампании: ![][36]

	1. Присвойте кампании любое имя.
	2. Выберите значение *Системное уведомление/простое* для параметра **Тип доставки**. Это простой тип push-уведомлений Android, в состав которых входит заголовок и небольшая строка текста.
	3. Выберите для параметра **Время доставки** значение *Любое*, чтобы приложение могло принимать уведомления независимо от того, запущено оно или нет.
	4. В тексте уведомления введите заголовок, которой будет отображаться полужирным шрифтом в push-уведомлении.
	5. Затем введите текст сообщения.

4. Прокрутите вниз и в разделе "Содержимое" выберите **Только уведомления**. ![][37]

5. Вы закончили настройку самой простой кампании. Теперь снова выполните прокрутку вниз, создайте кампанию и сохраните ее. ![][38]

6. Наконец, активируйте свою кампанию. ![][39]


<!-- URLs. -->
[Пакет SDK Android для Mobile Engagement]: http://go.microsoft.com/?linkid=9863935
[документации пакета SDK Android для Mobile Engagement]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-common/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-common/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-common/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-common/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-common/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-common/clic-monitor-tab.png
[31]: ./media/mobile-engagement-common/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-common/reach-tab.png
[35]: ./media/mobile-engagement-common/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-common/campaign-content.png
[38]: ./media/mobile-engagement-common/campaign-create.png
[39]: ./media/mobile-engagement-common/campaign-activate.png

<!--HONumber=54-->