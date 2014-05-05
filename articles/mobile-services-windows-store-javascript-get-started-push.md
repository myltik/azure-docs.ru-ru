<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Приступая к работе с push-уведомлениями (JS)" pageTitle="Приступая к работе с push-уведомлениями (Магазин Windows JavaScript) | Центр разработчиков для мобильных устройств" metaKeywords="" description="Использование мобильных служб Azure для отправки push-уведомлений в приложение Магазина Windows на JavaScript." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями в мобильных службах" authors="glenga" solutions="" manager="" editor="" />


# Приступая к работе с push-уведомлениями в мобильных службах
<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript" class="current">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Сервер .NET">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a></div>		


В этом разделе показано, как Visual Studio 2013 позволяет использовать мобильные службы Azure для отправки push-уведомлений в приложение Магазина Windows. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Windows (WNS) непосредственно в Visual Studio. По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

>[WACOM.NOTE]Мобильные службы теперь интегрируются с концентраторами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и масштабирование. Эти интегрированные функции в настоящее время находятся на стадии предварительной версии. Дополнительные сведения см. в этой версии [Приступая к работе с push-уведомлениями](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для push-уведомлений и конфигурация мобильных служб]
2. [Обновление созданного кода push-уведомлений]
3. [Вставка данных для получения уведомлений]

Этот учебник основан на возможностях быстрого начала работы с мобильными службами. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Если мобильная служба не подключена, мастер добавления push-уведомлений создает это подключение. 

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Добавьте и настройте push-уведомления в приложении</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Откройте созданный файл кода push.register.js и проверьте код, который получает идентификатор установки и канал для устройства и вставляет эти данные в таблицу новых <strong>каналов</strong>.</p> 

	<p>Эта таблица была создана в вашей мобильной службе с помощью мастера добавления push-уведомлений. Этот код гарантирует, что попытка регистрации устройства будет проводиться при активации приложения.</p></li>
<li><p>В обозревателе сервера разверните <strong>Azure</strong>, <strong>Мобильные службы</strong>, имя вашей службы и <strong>каналы</strong>, затем откройте файл insert.js.</p> 

<p>Этот файл, хранящийся в вашей мобильной службе, содержит код JavaScript, который выполняется, когда клиент отправляет запрос на регистрацию устройства, путем вставки данных в таблицу каналов.</p> 

<div class="dev-callout"><b>Примечание.</b>
	<p>Исходная версия данного файла содержит код, который проверяет имеющуюся регистрацию устройства. Он также содержит код, который отправляет push-уведомление при добавлении новой регистрации в таблицу каналов. Код, который отправляет push-уведомление, может быть включен в любой зарегистрированный файл сценария. Расположение этого сценария зависит от того, что выступает триггером уведомления. Сценарии могут быть зарегистрированы по операции вставки, обновления, удаления или чтения в таблицы, в качестве запланированного задания или в качестве пользовательского интерфейса API. Дополнительные сведения см. в разделе <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Работа с серверными сценариями в мобильных службах</a>.</p>
</div>
</li> 
<li><p>Нажмите клавишу F5, чтобы запустить приложение и проверить мгновенное получение уведомления от мобильной службы.</p>
<p>Это уведомление было создано путем вставки в таблицу новых каналов строки, которая является регистрацией устройства.</p>
</li>
</ol>

Хотя созданный код без труда демонстрирует возможность уведомления при работе приложения, это, в целом, не является хорошим примером. Затем необходимо будет удалить код уведомления из таблицы каналов и заменить его (с некоторыми изменениями) в таблице TodoItem. 

<h2><a name="update-scripts"></a><span class="short-header">Обновление кода</span>Обновление созданного кода push-уведомлений</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении</h2>

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

2. В приложении введите текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**.

   	![][13]

   	Обратите внимание, что после завершения вставки приложение получает push-уведомление из WNS.

   	![][14]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике демонстрируются основные функциональные возможности push-уведомлений, предоставляемые мобильными службами. Если вашему приложению требуются более сложные функции, такие как отправка уведомлений между различными платформами, маршрутизация на основе подписки или очень большие объемы данных, рассмотрите возможность использования концентраторов уведомлений Azure для вашей мобильной службы. Дополнительные сведения см. в следующих разделах, посвященных концентраторам уведомлений:

+ [Приступая к работе с концентраторами уведомлений]
  <br/>Сведения об использовании концентраторов уведомлений в приложении Магазина Windows.

+ [Рассылка уведомлений подписчикам]
	<br/>Сведения о том, как пользователи могут зарегистрироваться и получать push-уведомления для категорий, в которых они заинтересованы.

+ [Рассылка уведомлений пользователям]
	<br/>Дополнительные сведения о том, как отправлять push-уведомления из мобильной службы конкретным пользователям на любом устройстве.

+ [Отправка пользователям уведомлений между различными платформами]
	<br/>Дополнительные сведения об использовании шаблонов для отправки push-уведомлений из мобильной службы без создания полезных данных для конкретных платформ на стороне сервера.

Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с данными]
  <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о регистрации и использовании серверных скриптов.

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.  

<!-- Anchors. -->
[Регистрация приложения для push-уведомлений и конфигурация мобильных служб]: #register
[Обновление созданного кода push-уведомлений]: #update-scripts
[Вставка данных для получения уведомлений]: #test
[Следующие шаги]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-js/
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-js
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-push-js

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/develop/mobile/how-to-guides/work-with-html-js-client/
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с концентраторами уведомлений]: /ru-ru/manage/services/notification-hubs/getting-started-windows-dotnet/
[Что такое концентраторы уведомлений?]: /ru-ru/develop/net/how-to-guides/service-bus-notification-hubs/
[Рассылка уведомлений подписчикам]: /ru-ru/manage/services/notification-hubs/breaking-news-dotnet/
[Рассылка уведомлений пользователям]: /ru-ru/manage/services/notification-hubs/notify-users/
[Отправка пользователям уведомлений между различными платформами]: /ru-ru/manage/services/notification-hubs/notify-users-xplat-mobile-services/

