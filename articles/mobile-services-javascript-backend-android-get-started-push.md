<properties urlDisplayName="Get Started with Push (JS)" pageTitle="Приступая к работе с push-уведомлениями (Android JavaScript) | Центр мобильных разработок" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services " authors="ricksal"  solutions="" writer="ricksal" manager="dwrede" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal" />

# Добавление push-уведомлений к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе описано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android, используя облачную службу передачи сообщений Google (GCM). В этом учебнике объясняется включение push-уведомлений с помощью концентраторов уведомлений Azure в проекте быстрого запуска. В результате ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Включение Google Cloud Messaging](#register)
2. [Настройка мобильных служб](#configure)
3. [Добавление push-уведомлений в приложение](#add-push)
4. [Обновление скриптов для отправки push-уведомлений](#update-scripts)
5. [Вставка данных для получения уведомлений](#test)

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе.  

>[AZURE.NOTE] Если требуется просмотреть исходный код завершенного приложения, см. <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">здесь</a>.


##<a id="register"></a>Включение Google Cloud Messaging

>[WACOM.NOTE]Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

[WACOM.INCLUDE [Включение GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Далее вы будете использовать это значение ключа API, чтобы предоставить мобильным службам возможность выполнять аутентификацию с использованием GCM и отправлять push-уведомления от имени своего приложения.

##<a id="configure"></a>Настройка мобильных служб для отправки push-запросов

1. Войдите в [Портал управления Azure], щелкните **Мобильные службы**, затем щелкните свое приложение.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Откройте вкладку **Push-уведомления**, введите значение **ключа API**, полученное от GCM в предыдущей процедуре, затем щелкните **Сохранить**.

	>[WACOM.NOTE]При изучении этого учебника с использованием старой мобильной службы вы можете увидеть внизу вкладки **Push-уведомления** ссылку **Включение улучшенных push-уведомлений**. Щелкните по ней, чтобы обновить мобильную службу и интегрировать ее с концентраторами уведомлений. Это изменение нельзя будет отменить. Подробности включения улучшенных push-уведомлений в рабочей мобильной службе см. в <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">этом руководстве</a>.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Важно!</b>
	<p>При задании учетных данных GCM для улучшенных push-уведомлений на вкладке Push-уведомления портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.</p>
    </div>


Мобильная служба и приложение теперь настроены для работы с GCM и концентраторами уведомлений.

##<a id="add-push"></a>Добавление push-уведомлений в приложение

###Проверка версии Android SDK

[WACOM.INCLUDE [Проверка пакета SDK](../includes/mobile-services-verify-android-sdk-version.md)]

На следующем шаге необходимо установить службы Google Play. Облачные средства передачи сообщений Google задают определенные минимальные требования уровня API по разработке и тестированию, которым должно соответствовать свойство **minSdkVersion** в манифесте. 

Если тестирование будет проводиться на более старом устройстве, проверьте раздел [Установка SDK служб Google Play], чтобы определить, насколько низким может быть установлено это значение, и задайте его должным образом.

###Добавление служб Google Play в проект

[WACOM.INCLUDE [Добавление служб воспроизведения](../includes/mobile-services-add-Google-play-services.md)]

###Добавление кода

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Обновление зарегистрированных сценариев вставки на портале управления

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. В **todoitem** откройте вкладку **Скрипт** и выберите **Вставка**.
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и щелкните **Сохранить**:

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

   	Тем самым будет зарегистрирован новый сценарий вставки, который использует [gcm-объект] для отправки push-уведомлений всем зарегистрированным устройствам после успешного выполнения вставки. 

##<a id="test"></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

###Настройка эмулятора для тестирования

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1. Перезапустите Eclipse, в обозревателе пакетов щелкните правой кнопкой мыши проект, щелкните **Свойства**, выберите **Android**, установите флажок **API-интерфейсы Google**, а затем нажмите кнопку **ОК**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Проект будет предназначен для API-интерфейсов Google.

2. В разделе **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство и щелкните **Изменить**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Выберите **API-интерфейсы Google** в разделе **Цель**, а затем нажмите кнопку "ОК".

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google.

###Выполнение теста

1. Откройте меню **Выполнить** в Eclipse и щелкните **Выполнить**, чтобы запустить приложение.

2. В приложении введите значимый текст, такой как _Новая задача мобильных служб_, затем щелкните кнопку **Добавить**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.


Вы успешно завершили ознакомление с данным учебником.


## <a name="next-steps"> </a>Дальнейшие действия

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Применение тегов для отправки push-уведомлений из мобильной службы только пользователю, прошедшему проверку подлинности.

+ [Отправка широковещательных уведомлений подписчикам]
	<br/>Предоставление пользователям возможности регистрироваться и получать push-уведомления только по интересующим их категориям.

+ [Отправка подписчикам уведомлений на основе шаблона]
	<br/>Использование шаблонов для отправки push-уведомлений из мобильной службы без создания зависящих от платформы полезных данных на сервере.
-->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с различными типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Применение концентраторов уведомлений для доставки уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Устранение неполадок и отладка решений на основе концентраторов уведомлений. 

* [Использование клиентской библиотеки Android для мобильных служб]
  <br/>Использование мобильных служб с Android.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о способах реализации бизнес-логики в мобильной службе.


<!-- Anchors. -->
[Регистрация приложения для push-уведомлений и конфигурация мобильных служб]: #register
[Обновление созданного кода push-уведомлений]: #update-scripts
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-android-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-android-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-android-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-js
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Настройка пакета SDK служб Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
[Портал управления Azure]: https://manage.windowsazure.com/
[Использование клиентской библиотеки Android для мобильных служб]: /ru-ru/documentation/articles/mobile-services-android-how-to-use-client-library

[Объект gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
[Отправка широковещательных уведомлений подписчикам]: /ru-ru/documentation/articles/notification-hubs-android-send-breaking-news/
[Отправка подписчикам уведомлений на основе шаблона]: /ru-ru/documentation/articles/notification-hubs-android-send-localized-breaking-news/
