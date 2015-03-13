<properties 
	pageTitle="Приступая к работе с push-уведомлениями (Android JavaScript) | Центр мобильных разработок" 
	description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений в приложение JavaScript для Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	Writer="ricksal" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/16/2014" 
	ms.author="ricksal"/>

# Добавление push-уведомлений в приложение мобильных служб

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android с помощью Google Cloud Messaging (GCM). В этом учебнике вам предстоит использовать push-уведомления с помощью центров уведомлений Azure в проекте быстрого запуска. По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Включение Google Cloud Messaging](#register)
2. [Настройка мобильных служб](#configure)
3. [Добавление push-уведомлений в приложение](#add-push)
4. [Обновление скриптов для отправки push-уведомлений](#update-scripts)
5. [Вставка данных для получения уведомлений](#test)


>[AZURE.NOTE] Если требуется просмотреть исходный код завершенного приложения, см. <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">здесь</a>.

##Предварительные требования

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Включение Google Cloud Messaging

>[AZURE.NOTE]Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Далее вы будете использовать это значение ключа API, чтобы предоставить мобильным службам возможность выполнять аутентификацию с использованием GCM и отправлять push-уведомления от имени своего приложения.

##<a id="configure"></a>Настройка мобильных служб для отправки push-запросов

1. Войдя на [портал управления Azure], щелкните элемент **Мобильные службы** и выберите нужное приложение.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Перейдите на вкладку **Push-уведомления**, введите значение **Ключ API**, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку **Сохранить**.

	>[AZURE.NOTE]При изучении этого учебника с использованием старой мобильной службы вы можете увидеть внизу вкладки **Push-уведомления** ссылку **Включение улучшенных push-уведомлений**. Щелкните по ней, чтобы обновить мобильную службу и интегрировать ее с центрами уведомлений. Это изменение нельзя будет отменить. Подробности о включении улучшенных push-уведомлений в рабочей мобильной службе см. в <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">этом руководстве</a>.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

	> [AZURE.IMPORTANT] При задании учетных данных WNS для улучшенных push-уведомлений на вкладке "Push-уведомления" портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.


Мобильная служба и приложение теперь настроены для работы с GCM и концентраторами уведомлений.

##<a id="add-push"></a>Добавление push-уведомлений в приложение

###Проверка версии Android SDK

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте. 

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

###Добавление служб Google Play в проект

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Обновление зарегистрированного скрипта вставки на портале управления

1. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    data: {
		        message: item.text 
		    }
		};		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	При этом регистрируется новый сценарий вставки, в котором [объект gcm] используется для отправки push-уведомлений на все зарегистрированные устройства после успешной вставки данных. 

##<a id="test"></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

###Настройка эмулятора для тестирования

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1. Перезапустите Eclipse, в обозревателе пакетов щелкните правой кнопкой мыши проект, выберите пункт **Свойства**, выберите **Android**, установите флажок **API-интерфейсы Google**, а затем нажмите кнопку **ОК**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Проект будет предназначен для API-интерфейсов Google.

2. В **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство, щелкните **Изменить**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Выберите **API-интерфейсы Google** в **Цель**, а затем нажмите кнопку ОК.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google.

###Выполнение теста

1. В меню **Выполнить** в Eclipse щелкните **Выполнить** для запуска приложения.

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_) и нажмите кнопку **Добавить**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.


Вы успешно завершили ознакомление с данным учебником.


## <a name="next-steps"> </a>Дальнейшие действия

<!---В этом учебнике содержатся базовые сведения о том, как включить в приложении Android возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Далее мы рекомендуем изучить учебник [Рассылка push-уведомлений проверенным пользователям], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только тем пользователям, которые прошли проверку подлинности.

+ [Рассылка push-уведомлений проверенным пользователям]
	<br/>Дополнительная информация о том, как использовать теги для отправки push-уведомлений из мобильной службы только прошедшим проверку пользователям.

+ [Рассылка широковещательных уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

+ [Рассылка уведомлений по шаблону подписчикам]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.
-->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Дополнительная информация о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительная информация об аутентификации учетных данных пользователей приложения с другими типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Дополнительные сведения о работе центров уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Руководство по устранению неполадок и решения по отладке центров уведомлений. 

* [Использование клиентской библиотеки Android для мобильных служб]
  <br/>Дополнительные сведения об использовании мобильных служб с Android.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительная информация о реализации бизнес-логики в вашей мобильной службе.


<!-- Anchors. -->
[Регистрация приложения для push-уведомлений и настройка мобильных служб]: #register
[Обновление созданного кода push-уведомлений]: #update-scripts
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-android-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-android-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-android-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Push-уведомления для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-js
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Настройка пакета SDK служб Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
[Портал управления Azure]: https://manage.windowsazure.com/
[Использование клиентской библиотеки Android для мобильных служб]: /ru-ru/documentation/articles/mobile-services-android-how-to-use-client-library

[Объект gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Рассылка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-android-send-breaking-news/
[Рассылка уведомлений по шаблону подписчикам]: /ru-ru/documentation/articles/notification-hubs-android-send-localized-breaking-news/


<!--HONumber=42-->
