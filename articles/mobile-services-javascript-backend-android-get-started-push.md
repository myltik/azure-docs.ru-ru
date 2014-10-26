<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# <a name="getting-started-with-push"> </a>Приступая к работе с push-уведомлениями в мобильных службах

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
<!-- <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Серверная часть .NET" >Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/" title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

> [WACOM.NOTE]В этом учебнике показана интеграция мобильных служб с центрами уведомлений, а именно — отправка push-уведомлений из мобильной службы. Пользователям со старой версией мобильной службы, в которой используется устаревшая система принудительной отправки и не обновлены центры уведомлений, *рекомендуется выполнить обновление* во время прохождения этого учебника. Для тех, кто предпочитает отказаться от обновления, предназначена следующая версия учебника: [Приступая к работе с push-уведомлениями (в устаревшей версии)][Приступая к работе с push-уведомлениями (в устаревшей версии)]

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1.  [Включение Google Cloud Messaging][Включение Google Cloud Messaging]
2.  [Настройка мобильных служб][Настройка мобильных служб]
3.  [Добавление push-уведомлений в приложение][Добавление push-уведомлений в приложение]
4.  [Обновление скриптов для отправки push-уведомлений][Обновление скриптов для отправки push-уведомлений]
5.  [Вставка данных для получения уведомлений][Вставка данных для получения уведомлений]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], чтобы подключить свой проект к мобильной службе.

## <span id="register"></span></a>Включение Google Cloud Messaging

> [WACOM.NOTE]Для выполнения этой процедуры необходима учетная запись Google с проверенным электронным адресом. Чтобы создать новую учетную запись Google, перейдите по ссылке [accounts.google.com][accounts.google.com].

[WACOM.INCLUDE [Enable GCM][Enable GCM]]

Далее вы будете использовать это значение ключа API, чтобы предоставить мобильным службам возможность выполнять аутентификацию с использованием GCM и отправлять push-уведомления от имени своего приложения.

## <span id="configure"></span></a>Настройка мобильных служб для отправки push-запросов

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

    ![][]

2.  Перейдите на вкладку **Push-уведомления**, введите значение параметра **Ключ API**, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку **Сохранить**.

    > [WACOM.NOTE]Если вы проходите обучение с использованием мобильной службы предыдущей версии, в нижней части вкладки **Push-уведомления** может отображаться надпись **Включить улучшенные push-уведомления**. Щелкните ее, чтобы обновить мобильную службу для интеграции с центрами уведомлений. Это изменение нельзя отменить. Подробные указания по включению улучшенных push-уведомлений в рабочей мобильной службе см. в [этом руководстве][этом руководстве].

    ![][1]

    <div class="dev-callout"><b>Важно!</b>
<p>При задании учетных данных GCM для улучшенных push-уведомлений на вкладке Push-уведомления портала эти сведения передаются в концентраторы уведомлений в целях настройки концентратора уведомлений с вашим приложением.</p>
</div>

Мобильная служба и приложение теперь настроены для работы с GCM и концентраторами уведомлений.

## <span id="add-push"></span></a>Добавление push-уведомлений в приложение

### Проверка версии Android SDK

[WACOM.INCLUDE [Проверка пакета SDK][Проверка пакета SDK]]

Далее следует установить службы Google Play. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте.

Если вы будете тестировать приложение на более старом устройстве, обратитесь к руководству [Настройка пакета SDK служб Google Play][Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение.

### Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services][Add Play Services]]

### Добавление кода

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][mobile-services-android-getting-started-with-push]]

## <span id="update-scripts"></span></a>Обновление зарегистрированных скриптов вставки на портале управления

1.  На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][2]

2.  В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

    ![][3]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';
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

    При этом регистрируется новый скрипт вставки, в котором [объект gcm][объект gcm] используется для отправки push-уведомлений на все зарегистрированные устройства после успешной вставки данных.

## <span id="test"></span></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

### Настройка эмулятора для тестирования

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

1.  Перезапустите Eclipse, в обозревателе пакетов щелкните правой кнопкой мыши проект, щелкните **Свойства**, выберите **Android**, установите флажок **API-интерфейсы Google**, а затем нажмите кнопку **ОК**.

    ![][4]

    Проект будет предназначен для API-интерфейсов Google.

2.  В **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство, щелкните **Изменить**.

    ![][5]

3.  Выберите **API-интерфейсы Google** в **Цель**, а затем нажмите кнопку ОК.

    ![][6]

    При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google.

### Выполнение теста

1.  В меню **Выполнить** в Eclipse выберите **Выполнить** для запуска приложения.

2.  В приложении введите содержательный текст (например, *Новая задача для мобильных служб*, а затем нажмите кнопку **Добавить**.

    ![][7]

3.  Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.

Вы успешно завершили ознакомление с данным учебником.

## <a name="next-steps"> </a>Дальнейшие действия

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Дополнительные сведения о мобильных службах и концентраторах уведомлений см. в следующих разделах.

-   [Приступая к работе с данными (][Приступая к работе с данными]
    <br/>Дополнительные сведения о хранении данных и запросах к ним при помощи мобильных служб.

-   [Приступая к работе с проверкой подлинности][Приступая к работе с проверкой подлинности]
    <br/>Дополнительные сведения о проверке подлинности пользователей приложения с разными типами учетных записей с помощью мобильных служб.

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
    <br/>Дополнительные сведения о работе концентраторов уведомлений по доставке уведомлений в приложения на всех основных клиентских платформах.

-   [Использование библиотеки клиента Android для мобильных служб][Использование библиотеки клиента Android для мобильных служб]
    <br/>Дополнительные сведения об использовании мобильных служб с Android.

-   [Справочник серверных сценариев мобильных служб][Справочник серверных сценариев мобильных служб]
    <br/>Узнайте о том, как реализовать бизнес-логику в мобильной службе.

<!-- Anchors.  Images. URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "Серверная часть JavaScript"
  [Приступая к работе с push-уведомлениями (в устаревшей версии)]: /ru-ru/documentation/articles/mobile-services-android-get-started-push/
  [Включение Google Cloud Messaging]: #register
  [Настройка мобильных служб]: #configure
  [Добавление push-уведомлений в приложение]: #add-push
  [Обновление скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения уведомлений]: #test
  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-android-get-started/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [этом руководстве]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Проверка пакета SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [Настройка пакета SDK служб Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [объект gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-android-get-started-users
  [Что такое концентраторы уведомлений?]: /ru-ru/documentation/articles/notification-hubs-overview/
  [Использование библиотеки клиента Android для мобильных служб]: /ru-ru/documentation/articles/mobile-services-android-how-to-use-client-library
  [Справочник серверных сценариев мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
