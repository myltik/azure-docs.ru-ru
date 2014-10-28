<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

В этом учебнике рассматриваются следующие действия.

1.  [Включение Google Cloud Messaging][Включение Google Cloud Messaging]
2.  [Настройка мобильных служб для отправки push-запросов][Настройка мобильных служб для отправки push-запросов]
3.  [Обновление сервера для отправки push-уведомлений][Обновление сервера для отправки push-уведомлений]
4.  [Добавление push-уведомлений в приложение][Добавление push-уведомлений в приложение]
5.  [Включение push-уведомлений для локального тестирования][Включение push-уведомлений для локального тестирования]
6.  [Тестирование приложения с помощью опубликованной мобильной службы][Тестирование приложения с помощью опубликованной мобильной службы]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], чтобы подключить свой проект к мобильной службе. Таким образом, для работы с этим учебником требуется Visual Studio 2013.

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

## <span id="register"></span></a>Включение Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <span id="configure"></span></a>Настройка мобильных служб для отправки push-запросов

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

    ![][0]

2.  Перейдите на вкладку **Push-уведомления**, введите значение **ключа API**, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку **Сохранить**.

    ![][1]

    <div class="dev-callout"><b>Важно!</b><p>При задании учетных данных GCM для улучшенных push-уведомлений на вкладке Push-уведомления портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.</p></div>

Ваша мобильная служба теперь настроена для работы с GCM и концентраторами уведомлений.

## <a name="download-the-service"></a><span class="short-header"> Загрузка службы</span>Загрузка службы на локальный компьютер

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">Тестирование службы</span>Тестирование мобильной службы

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a> Обновление сервера для отправки push-уведомлений

1.  В обозревателе решений Visual Studio разверните папку **Контроллеры** в проекте мобильной службы. Откройте файл TodoItemController.cs. Добавьте в начало файла следующие инструкции `using`:

        using System;
        using System.Collections.Generic;

2.  Обновите определение метода `PostTodoItem` с помощью следующего кода:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Этот код отправит push-уведомление (с текстом вставленного элемента) после вставки элемента списка дел. В случае возникновения ошибки код добавит запись в журнал ошибок, которую можно просмотреть на вкладке **Журналы** мобильной службы на портале управления.

## <a name="publish-the-service"></a><span class="short-header">Публикация службы</span>Публикация мобильной службы в Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>Добавление push-уведомлений в приложение

### Проверка версии Android SDK

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте.

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

### Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Добавление кода

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

## <a name="test-app"></a><span class="short-header">Тестирование приложения</span>Тестирование работы приложения с опубликованной мобильной службой

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

### При использовании эмулятора для тестирования...

Убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1.  В разделе **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство, выберите команду **Изменить** (или **Создать**, если пока нет никаких устройств).

    ![][2]

2.  Выберите **API-интерфейсы Google** (или **API-интерфейсы Google x86**) в разделе **Цель**, а затем нажмите кнопку "ОК".

    ![][3]

    При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google. Если имеется несколько установленных версий пакета Android SDK, убедитесь, что уровень API соответствует установленному ранее в свойствах проекта.

### <span id="local-testing"></span></a> Включение push-уведомлений для локального тестирования

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

### Выполнение теста

1.  В меню **Выполнить** в Eclipse выберите **Выполнить** для запуска приложения.

2.  В приложении введите содержательный текст (например, *Новая задача для мобильных служб*, а затем нажмите кнопку **Добавить**.

    ![][4]

3.  Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.

Вы успешно завершили ознакомление с данным учебником.

## <a name="next-steps"> </a>Дальнейшие действия

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

-   [Приступая к работе с данными (][Приступая к работе с данными]
    Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
    Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

-   [Использование библиотеки клиента Android для мобильных служб][Использование библиотеки клиента Android для мобильных служб]
    Дополнительные сведения об использовании мобильных служб с Android.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "Серверная часть JavaScript"
  [Включение Google Cloud Messaging]: #register
  [Настройка мобильных служб для отправки push-запросов]: #configure
  [Обновление сервера для отправки push-уведомлений]: #update-server
  [Добавление push-уведомлений в приложение]: #update
  [Включение push-уведомлений для локального тестирования]: #local-testing
  [Тестирование приложения с помощью опубликованной мобильной службы]: #test-app
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
  [Использование библиотеки клиента Android для мобильных служб]: /ru-ru/documentation/articles/mobile-services-android-how-to-use-client-library
