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


#Интеграция ADM с платформой Engagement

> [AZURE.IMPORTANT]Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, описанную в документе «Интеграция Engagement на платформе Android».
>
> Этот документ может оказаться полезным только в том случае, если проделана интеграция модуля обработки рекламных кампаний для поддержки кампаний, выполняемых в любое время. Для интеграции кампаний, обработанных модулем, в приложение необходимо сначала ознакомиться с разделом «Интеграция модуля обработки рекламных кампаний платформы Engagement для Android».

##Введение

Интеграция ADM позволяет приложению получать push-уведомления даже, если оно не запущено.

Фактически данные рекламной кампании не отправляются через ADM. Речь идет лишь о фоновом сигнале, который сообщает приложению о необходимости получить push-уведомление с платформы Engagement. Если приложение не работает во время получения push-уведомления ADM, оно инициирует подключение к серверам платформы Engagement для получения отправленных данных. Подключение к платформе Engagement остается активным в течение приблизительно минуты в случае, если пользователь запускает приложение в ответ на push-уведомление.

> [AZURE.IMPORTANT]Только устройства Amazon Kindle под управлением Android 4.0.3 или более поздней версии поддерживаются службой Amazon Device Messaging (ADM). Тем не менее, этот код можно безопасно интегрировать на других устройствах. Если приложение не удается вывести из спящего режима с помощью ADM, уведомление платформы Engagement будет получено в следующий раз, когда запускается приложение.

##Подписка на ADM

Если это еще не сделано, необходимо включить ADM в учетной записи Amazon.

Эта процедура подробно описана на странице [<https://developer.amazon.com/sdk/adm/credentials.html>].

После завершения процедуры вы получаете в свое распоряжение:

-   Учетные данные OAuth (идентификатор и секрет клиента) для платформы Engagement, чтобы иметь возможность направлять push-уведомления своим устройствам.
-   Ключ API, который должен быть интегрирован в приложение.

##Интеграция пакета SDK

### Управление регистрацией устройств

Каждое устройство должно отправлять команду регистрации серверам ADM, в противном случае с ними невозможно связаться.

Если вы уже используете [клиентскую библиотеку ADM] и [интегрировали ADM], можно переходить прямо к команде android-sdk-adm-receive.

Если интеграция ADM еще не выполнена, платформа Engagement предлагает более простой путь для ее включения в приложении.

Отредактируйте файл `AndroidManifest.xml`:

-   Добавьте пространство имен Amazon, файл должен начинаться следующим образом:

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   Внутри тега `<application/>` добавьте этот раздел:

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   После добавление тега Amazon может возникнуть ошибка построения, если конечная сборка проекта ниже версии Android 2.1. Необходимо использовать конечное построение **версии Android 2.1 или выше** (не беспокойтесь, значение `minSdkVersion` может быть равным 4).
-   Интегрируйте ключ API ADM как ресурс, выполнив [следующую процедуру].

Затем выполните указания следующих разделов.

### Передайте идентификатор регистрации службе Push-уведомлений платформы Engagement и начните получать уведомления.

Чтобы передать идентификатор регистрации устройства службе push-уведомлений Engagement и получать ее уведомления, необходимо добавить следующий код в файл `AndroidManifest.xml` внутри тега `<application/>` (даже в случае использования ADM без платформы Engagement):

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>
		
		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

Убедитесь в наличии следующих разрешений в `AndroidManifest.xml` (перед тегом `</application>`).

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Предоставление учетных данных OAuth для платформы Engagement

Отправьте учетные данные OAuth (идентификатор и секрет клиента) в $/#application/YOUR\_APPID/native-push.

Теперь можно выбирать параметр "Any Time" (в любое время) при создании объявлений и опросов модуля обработки рекламных кампаний.


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[клиентскую библиотеку ADM]: https://developer.amazon.com/sdk/adm/setup.html
[интегрировали ADM]: https://developer.amazon.com/sdk/adm/integrating-app.html
[следующую процедуру]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
 

<!---HONumber=August15_HO6-->