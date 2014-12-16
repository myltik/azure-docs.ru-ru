<properties pageTitle="Начало работы с push-уведомлением с помощью серверной мобильной службы JavaScript" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />


# Добавление push-уведомлений к приложению мобильных служб

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

В этом разделе показано, как использовать мобильные службы Azure с серверной версией JavaScript для отправки push-уведомлений в универсальное приложение Windows. В этом учебнике вам предстоит использовать push-уведомления с помощью концентраторов уведомлений Azure в проекте универсального приложения Windows. В результате ваша мобильная служба будет отправлять push-уведомление из серверной части JavaScript во все зарегистрированные приложения Магазина Windows и Магазина Windows Phone каждый раз при вставке записи в таблицу TodoList. Создаваемый вами концентратор уведомлений является бесплатным для вашей мобильной службы, может управляться независимо от мобильной службы и может использоваться другими приложениями и службами.

>[WACOM.NOTE]В этом разделе показано, как использовать средства Visual Studio 2013 с обновлением 3 для отправки push-уведомлений из мобильных служб в универсальное приложение Windows. Эти же инструкции можно использовать для добавления push-уведомлений из мобильных служб в приложение Магазина Windows или Магазина Windows Phone 8.1. Если вы не можете выполнить обновление до Visual Studio 2013 с обновлением 3 или предпочитаете вручную добавить проект мобильной службы в приложение Магазина Windows, см. [эту версию](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) раздела.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Регистрация приложения для получения push-уведомлений](#register)
2. [Обновление службы для отправки push-уведомлений](#update-service)
3. [Тестирование push-уведомлений в приложении](#test)

Для работы с этим учебником требуется:

* Активная [учетная запись Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* [Visual Studio Express 2013 для Windows](http://go.microsoft.com/fwlink/?LinkId=257546) с обновлением 3 или более поздней версии 

##<a id="register"></a>Регистрация приложения для получения push-уведомлений

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Перейдите в папку проекта <code>\services\mobileServices\scripts</code>, скопируйте созданный файл скрипта <<em>имя_вашей_службы</em>>.push.register.js в общую папку <code>\js</code>, а затем удалите этот файл из проектов приложений для Windows и Windows Phone.</p></li> 
<li><p>Откройте этот файл скрипта в общей папке проекта <code>\js</code>, найдите в прослушивателе событий <em>activated</em> код, который регистрирует URL-адрес канала устройства в концентраторе уведомлений, и удалите функцию обещания <strong>done</strong>.</p>
<p>В примере в этом учебнике уведомления отправляются при вставке нового элемента, а не при вызове настраиваемого API.</p></li>
<li><p>В проекте приложения Windows откройте файл default.html и измените путь ссылки на файл скрипта из общей папки проекта <code>\js</code> так, чтобы он выглядел следующим образом:</p><pre><code><script src="/js/your_service_name.push.register.js"></script></code></pre></li>
<li><p>Повторите этот шаг для проекта приложения WindowsPhone.</p>
<p>Теперь в обоих проектах используется общая версия скрипта регистрации push-уведомлений.</p></li>
</ol>

После включения push-уведомлений в приложении необходимо обновить мобильную службу так, чтобы она отправляла push-уведомления. 

##<a id="update-service"></a>Обновление службы для отправки push-уведомлений

Ниже показано, как обновить сценарий вставки, зарегистрированный в таблице TodoItem. Подобный код можно реализовать в любом серверном скрипте или в другом месте в серверных службах. 

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Тестирование push-уведомлений в приложении

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в приложениях для Магазина Windows возможности использования мобильных служб и концентраторов уведомлений для отправки push-уведомлений. Затем рассмотрите возможность пройти следующий учебник, [Отправка push-уведомлений пользователям, прошедшим проверку подлинности], в котором показано, как использовать теги для отправки push-уведомлений из мобильной службы только пользователям, прошедшим проверку подлинности.

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с различными типами учетных записей с использованием мобильных служб.

* [Что такое концентраторы уведомлений?]
  <br/>Применение концентраторов уведомлений для доставки уведомлений в приложения на всех основных клиентских платформах.

* [Отладка приложений концентраторов уведомлений](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Устранение неполадок и отладка решений на основе концентраторов уведомлений. 

* [Использование клиента HTML/JavaScript для мобильных служб Azure]
  <br/>Дополнительные сведения об использовании мобильных служб из приложений, написанных на HTML и JavaScript.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK для Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users

[Рассылка push-уведомлений проверенным пользователям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/

[Использование клиента HTML/JavaScript для мобильных служб Azure]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library
