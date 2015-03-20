<properties 
	pageTitle="Интеграция пакета Android SDK для Azure Mobile Engagement" 
	description="Последние обновления и процедуры пакета Android SDK для Azure Mobile Engagement"
	services="mobile-engagement" 
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
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


#Процедуры обновления

Если уже выполнена интеграция более старой версии пакета SDK в приложение, необходимо принять во внимание следующие моменты при обновлении пакета SDK.

Возможно придется выполнить несколько процедур, если было пропущено несколько версий пакета SDK. Например, при миграции из версии 1.4.0 в 1.6.0, необходимо сначала выполнить процедуру "из версии 1.4.0 в 1.5.0", а затем процедуру "из версии 1.5.0 в 1.6.0".

Вне зависимости от того, из какой версии выполняется обновление, необходимо заменить все `mobile-engagement-VERSION.jar` на новые версии.

###Из версии 2.4.0 в 3.0.0

Ниже описан процесс переноса интеграции пакета SDK из службы Capptain, предлагаемой Capptain SAS, в приложение под управлением службы Azure Mobile Engagement. 

>[AZURE.IMPORTANT] Capptain и Mobile Engagement - это не одни и те же службы, и процедура, приведенная ниже, показывает только перенос клиентского приложения. Перенос пакета SDK в приложение НЕ переносит данные из серверов Capptain в серверы Mobile Engagement

При выполнении миграции из более ранней версии получите рекомендации на веб-сайте Capptain, чтобы сначала перейти на версию 2.4, а затем примените следующие процедуры

#### JAR-файл

Замените `capptain.jar` на `mobile-engagement-VERSION.jar` в папке `libs`.

#### Файлы ресурсов

Каждый предоставленный файл ресурсов ( с префиксом `capptain_`) должен быть заменен на новый (с префиксом `engagement_`).

Если эти файл были настроены, необходимо применить настройку к новым файлам, **все идентификаторы фалов ресурсов также необходимо переименовать**.

#### Идентификатор приложения

Теперь служба Engagement использует строку подключения для настройки идентификаторов пакета SDK, таких как идентификатор приложения.

Необходимо использовать метод `EngagementAgent.init` в действии запуска следующим образом:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

Эта строка подключения для приложения отображается на портале Azure.

Удалите все вызовы к `CapptainAgent.configure`, поскольку `EngagementAgent.init` заменяет этот метод.

`appId` больше нельзя настраивать с помощью `AndroidManifest.xml`.

Удалите этот раздел из `AndroidManifest.xml`, если он имеется:

			<meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

#### API Java

Любой вызов любого класса Java пакета SDK должен быть переименован, например, `CapptainAgent.getInstance(this)` должен быть переименован на `EngagementAgent.getInstance(this)`, `extends CapptainActivity` должен быть переименован на `extends EngagementActivity` и т. д.

Если была выполнена интеграция с файлами параметров агента по умолчанию, то теперь имя файла по умолчанию - `engagement.agent`, а ключ - `engagement:agent`.

При создании веб-объявлений модуль привязки Javascript теперь `engagementReachContent`.

#### AndroidManifest.xml

Выполнено много изменений. Служба больше не используется совместно, а большое количество получателей больше нельзя экспортировать.

Объявление службы теперь упрощено, удален фильтр намерений и все метаданные в нем, а также добавлен `exportable=false`.

Кроме того, все переименовано для использования службы Engagement.

Теперь это должно выглядеть следующим образом:

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

При необходимости включить журналы тестирования метаданные теперь перемещаются в тег приложения и переименовываются:

			<application>
			
			  <meta-data android:name="engagement:log:test" android:value="true" />
			
			  <service/>
			
			</application>

Все остальные метаданные только что были переименованы. Здесь приводится полный список (разумеется, следует переименовывать только те, которые используются):

			<meta-data
			  android:name="engagement:reportCrash"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:sessionTimeout"
			  android:value="10000"/>
			<meta-data
			  android:name="engagement:burstThreshold"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:connection:delay"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:locationReport:lazyArea"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:background"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:fine"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:agent:settings:name"
			  android:value="engagement.agent"/>
			<meta-data
			  android:name="engagement:agent:settings:mode"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:gcm:sender"
			  android:value="<YOUR_PROJECT_NUMBER>\n"/>
			<meta-data
			  android:name="engagement:adm:register"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:reach:notification:icon"
			  android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
			
			<activity android:name="SomeActivityWithoutReachOverlay">
			  <meta-data
			    android:name="engagement:notification:overlay"
			    android:value="false"/>
			</activity>

Отслеживание Google Play и SmartAd удалено из пакета SDK. Необходимо просто удалить это без замены.

			<meta-data 
				android:name="capptain:track:installReferrerForwardList"
				android:value="com.class1,com.class2"/>
			<meta-data
				android:name="capptain:track:adservers"
				android:value="smartad" />

Действия модуля Reach теперь объявляются следующим образом:

			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/plain"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/html"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			
Если действия модуля Reach настроены, необходимо изменить только действия намерений для соответствия  `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` или `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Получатели рассылок были переименованы, плюс было добавлено `exported=false`. Ниже приводится полный список получателей с новой спецификацией (разумеется, необходимо переименовывать только тех, которые используются):

			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
			  android:exported="false">
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
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
			  android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
			    <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
			  android:permission="com.amazon.device.messaging.permission.SEND">
			  <intent-filter>
			    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
			    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
			  </intent-filter>
			</receiver>

Получатель отслеживания был удален, поэтому нужно удалить данный раздел:

		  <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
		    <intent-filter>
		      <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
		      <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
		    </intent-filter>
		  </receiver>

Обратите внимание, что объявление реализации получателя рассылок **EngagementMessageReceiver** изменено в `AndroidManifest.xml`. Это вызвано тем, что API используется для отправки и удаления произвольных сообщений XMPP из произвольных сущностей XMPP, а API для отправки и получения сообщений между устройствами был удален. Таким образом, необходимо также удалить следующие обратные вызовы из реализации **EngagementMessageReceiver** :

			protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

и

			protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

затем удалить все вызовы в **EngagementAgent** для :

			sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

и

			sendXMPPMessage(android.os.Bundle msg)

#### Proguard

На настройку Proguard может оказать влияние ребрендинг, правила теперь выглядят следующим образом:

			-dontwarn android.**
			-keep class android.support.v4.** { *; }
			
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			  <methods>;
			}

<!--HONumber=47-->
