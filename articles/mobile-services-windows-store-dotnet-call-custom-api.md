<properties urlDisplayName="Вызов настраиваемого API из клиента" pageTitle="Вызов настраиваемого API из клиента Магазина Windows — мобильные службы" metaKeywords="" description="Определение настраиваемого интерфейса API и вызов его из приложения Магазина Windows, которое использует мобильные службы Azure." metaCanonical="" services="" documentationCenter="" title="Вызов настраиваемого API из клиента" authors="glenga" solutions="" manager="" editor="" />

# Вызов настраиваемого интерфейса API из клиента

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Магазин Windows C#" class="current">Магазин Windows C#</a><a href="/ru-ru/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a>
</div>
<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title="Сервер .NET">Сервер .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api"  title="Сервер JavaScript" class="current">Сервер JavaScript</a></div>

В этом разделе показано, как вызывать настраиваемый API из приложений Магазина Windows. Настраиваемый API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов списка дел в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. Для этого требуются следующие действия:

1. [Определение настраиваемого API]
2. [Обновление приложения для вызова настраиваемого API]
3. [Тестирование приложения] 

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами] или [Приступая к работе с данными]. В этом учебнике используется Visual Studio 2012 Express для Windows 8.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]


## Дальнейшие действия

Узнав, как создать настраиваемый API и вызывать его из приложения для Магазина Windows, изучите следующие разделы о мобильных службах:

* [Определение настраиваемого интерфейса API с поддержкой периодических уведомлений]
	<br/>Узнайте, как использовать настраиваемый API для поддержки периодических уведомлений в приложениях Магазина Windows. Если периодические уведомления включены, Windows будет периодически обращаться к конечной точке настраиваемого API и использовать полученный XML в формате для плиток, чтобы обновить плитку приложения в меню "Пуск".

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительные сведения о создании настраиваемых API.

* [Хранение серверных скриптов системе управления версиями]
  <br/> Узнайте, как использовать функцию управления версиями, чтобы легко и надежно разрабатывать и публиковать код скриптов настраиваемого API.

<!-- Anchors. -->
[Определение настраиваемого API]: #define-custom-api
[Обновление приложения для вызова настраиваемого API]: #update-app
[Тестирование приложения]: #test-app
[Дальнейшие действия]: #next-steps

<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/

[Определение настраиваемого интерфейса API с поддержкой периодических уведомлений]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications
[Хранение серверных скриптов системе управления версиями]: /ru-ru/documentation/articles/mobile-services-store-scripts-source-control


