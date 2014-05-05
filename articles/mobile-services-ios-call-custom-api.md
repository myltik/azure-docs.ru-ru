<properties pageTitle="Вызов настраиваемого интерфейса API из приложения iOS | Мобильные службы" metaKeywords="" description="Узнайте, как определить настраиваемый интерфейс API и вызывать его из приложения iOS, которое использует мобильные службы Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Вызов настраиваемого интерфейса API из клиента" authors="krisragh"  solutions="" writer="krisragh" manager="" editor=""  />

# Вызов настраиваемого интерфейса API из клиента 

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" class="current">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title="Сервер .NET">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-ios-call-custom-api"  title="Сервер JavaScript" class="current">Сервер JavaScript</a></div>

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложений iOS. Настраиваемый API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов списка дел в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. Для этого требуются следующие действия:

1. [Определение настраиваемого интерфейса API]
2. [Обновление приложения для вызова настраиваемого интерфейса API]
3. [Тестирование приложения] 

Этот учебник основан на возможностях быстрого начала работы с мобильными службами. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. Для этого учебника требуется [Пакет Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533), [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) и iOS 5.0 или более поздней версии.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для iOS, изучите следующие статьи о мобильных службах:

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых интерфейсов API.

* [Хранение серверных скриптов в системе управления версиями]
  <br/> Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код скриптов настраиваемого API.

<!-- Anchors. -->
[Определение настраиваемого интерфейса API]: #define-custom-api
[Обновление приложения для вызова настраиваемого интерфейса API]: #update-app
[Тестирование приложения]: #test-app
[Следующие шаги]: #next-steps

<!-- URLs. -->
[Push-уведомления Windows и Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Панель мониторинга "Мои приложения"]: http://go.microsoft.com/fwlink/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-ios-get-started-push/
[Хранение серверных скриптов в системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control

