<properties 
	pageTitle="Интеграция пакета Android SDK для Azure Mobile Engagement" 
	description="Последние обновления и процедуры пакета Android SDK для Azure Mobile Engagement"
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
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Интеграция GCM с помощью службы Mobile Engagement

> [AZURE.IMPORTANT]Перед выполнением действий, описанных в этом руководстве, необходимо выполнить процедуру интеграции, описанную в документе «Интеграция Engagement на платформе Android».
>
> Этот документ может оказаться полезным только в том случае, если проделана интеграция модуля обработки рекламных кампаний для поддержки кампаний, выполняемых в любое время. Для интеграции кампаний, обработанных модулем, в приложение необходимо сначала ознакомиться с разделом «Интеграция модуля обработки рекламных кампаний платформы Engagement для Android».

##Введение

Интеграция GCM позволяет приложению получать push-уведомления даже в случае, если оно не запущено.

Фактически данные рекламной кампании не отправляются через GCM. Речь идет лишь о фоновом сигнале, который сообщает приложению о необходимости получить push-уведомление с платформы Engagement. Если приложение не работает во время получения push-уведомления GCM, оно инициирует подключение к серверам платформы Engagement для получения отправленных данных. Подключение к платформе Engagement остается активным в течение приблизительно минуты в случае, если пользователь запускает приложение в ответ на push-уведомление.

Следует иметь в виду, что платформа Engagement использует только сообщения [отправки для синхронизации] с ключом свертывания `engagement.tickle`.

> [AZURE.IMPORTANT]С помощью GCM могут быть активированы только устройства под управлением Android 2.2 или выше с установленным Google Play, а также включенным фоновым подключением Google.Тем не менее можно безопасно интегрировать этот код в более старых версиях пакета SDK для Android, а так же на устройствах, которые не могут поддерживать службу GCM (она использует только цели). Если приложение не удается вывести из спящего режима с помощью GCM, уведомление платформы Engagement будет получено в следующий раз, когда запускается приложение.


> [AZURE.WARNING]Если код клиента управляет идентификаторами регистрации C2DM, а пакет SDK для платформы Engagement настроен на использование GCM возникает конфликт идентификаторов регистрации. Следует использовать GCM на платформе Engagement только в случае, если ваш собственный код не использует C2DM.

##Подписка на GCM и включение службы GCM

Если это еще не сделано, необходимо включить службу GCM вашей учетной записи Google.

На момент написания этого документа (5 февраля 2014 г.) можно было выполнить процедуру, приведенную на сайте [<http://developer.android.com/guide/google/gcm/gs.html>].

Выполните эту процедуру просто для того, чтобы включить GCM в вашей учетной записи. После того как вы доберетесь до раздела **Получение ключа API**, не читайте его, а вернитесь на эту страницу, вместо того чтобы дальше выполнять процедуру Google.

В процедуре поясняется, что **номер проекта** используется как **идентификатор отправителя GCM**, который понадобится позднее в данной процедуре.

> [AZURE.IMPORTANT]**Номер проекта** не следует путать с **идентификатором проекта**. Идентификатор проекта теперь может быть другим (это имя для новых проектов). Для интеграции в пакет SDK для платформы Engagement необходим **номер проекта**, который отображается в меню **Обзор** в [консоли разработчиков Google].

##Интеграция пакета SDK

### Управление регистрацией устройств

Каждое устройство должно отправлять команду регистрации серверам Google, в противном случае с ними невозможно связаться.

Устройство также может отменить регистрацию на получение уведомлений GCM (отмена регистрации устройства выполняется автоматически при удалении приложения).

При использовании [клиентской библиотеки GCM] можно сразу переходить к команде чтения android-sdk-gcm-receive.

Если вы сами еще не отправили намерение о регистрации, можно сделать так, чтобы платформа Engagement выполняла регистрацию устройств автоматически.

Для этого добавьте следующий код в файл `AndroidManifest.xml` внутри тега `<application/>`:

			<!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
			<meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### Передайте идентификатор регистрации службе Push-уведомлений платформы Engagement и начните получать уведомления.

Чтобы передать идентификатор регистрации устройства службе push-уведомлений Engagement и получать ее уведомления, необходимо добавить следующий код в файл `AndroidManifest.xml` внутри тега `<application/>` (даже если вы управляете регистрацией устройств самостоятельно):

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
			    <category android:name="<your_package_name>" />
			  </intent-filter>
			</receiver>

Убедитесь в наличии следующих разрешений в `AndroidManifest.xml` (после тега `</application>`).

			<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
			<uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
			<permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##Предоставление платформе Engagement доступа к ключу API сервера

Если это еще не сделано, создайте **ключ API сервера** в [консоли разработчиков Google].

Ключ сервера **НЕ ДОЛЖЕН иметь ограничений IP-адресов**.

На момент написания этого документа (5 февраля 2014 года) процедура выглядела следующим образом:

-   Чтобы сделать это, [откройте консоль разработчиков Google].
-   Выберите тот же проект, что и ранее в процедуре (проект с **номером проекта**, который был интегрирован в `AndroidManifest.xml`).
-   Перейдите к разделу "API и проверка подлинности" -> "Учетные данные" и щелкните "СОЗДАТЬ НОВЫЙ КЛЮЧ" в разделе "Доступ к общему API".
-   Выберите "Ключ сервера".
-   На следующем экране оставьте значение пустым **(без ограничения IP-адресов)**, а затем нажмите кнопку "Создать".
-   Скопируйте созданный **ключ API**.
-   Перейдите к $/#application/YOUR\_ENGAGEMENT\_APPID/native-push.
-   В разделе GCM замените ключ API на тот, который вы только что создали и скопировали.

Теперь можно выбирать параметр "Any Time" (в любое время) при создании объявлений и опросов модуля обработки рекламных кампаний.

> [AZURE.IMPORTANT]Платформе Engagement фактически необходим **ключ сервера**, так как ключ Android не может использоваться серверами Engagement.

##Тест

Теперь проверьте интеграцию, ознакомившись со статьей "Тестирование интеграции Engagement в Android".


[отправки для синхронизации]: http://developer.android.com/google/gcm/adv.html#collapsible
[<http://developer.android.com/guide/google/gcm/gs.html>]: http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]: https://cloud.google.com/console
[клиентской библиотеки GCM]: http://developer.android.com/guide/google/gcm/gs.html#libs
[консоли разработчиков Google]: https://cloud.google.com/console
[откройте консоль разработчиков Google]: https://cloud.google.com/console

 

<!---HONumber=Oct15_HO3-->