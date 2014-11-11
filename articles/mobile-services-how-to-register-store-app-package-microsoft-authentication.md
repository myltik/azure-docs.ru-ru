<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Регистрация пакета приложения для магазина Windows для проверки подлинности Microsoft

Мобильные службы Azure поддерживают методы проверки подлинности как со стороны клиента, так и со стороны сервера. Проверка подлинности со стороны сервера использует поставщиков удостоверений, включая учетную запись Майкрософт. При использовании учетной записи Майкрософт для проверки подлинности со стороны сервера без регистрации приложения с помощью мобильных служб пользователям предлагается вводить учетные данные при каждом запросе проверки подлинности. Если приложение зарегистрировано, учетные данные для входа учетной записи Майкрософт кэшируются и могут использоваться для проверки подлинности без запроса пользователю ввести их снова. В этом разделе показано, как зарегистрировать пакет приложения Магазина Windows, чтобы было удобнее пользоваться учетной записью Майкрософт при применении для проверки подлинности мобильных служб Azure.

> [WACOM.NOTE]Visual Studio 2013 позволяет легко регистрировать пакет приложения для Магазина Windows в мобильных службах. Дополнительные сведения см. на странице [Краткое руководство: добавление push-уведомлений в мобильную службу][Краткое руководство: добавление push-уведомлений в мобильную службу] в Центре разработки для Windows.

Аутентификацию со стороны клиента можно использовать, чтобы обеспечить работу единого входа на устройстве Windows с помощью Live Connect. При использовании интерфейсов API подключения Live Connect вам не нужно выполнять действия, описанные в этом разделе. Дополнительные сведения см. в разделе [Проверка подлинности приложения Магазина Windows с помощью единого входа Live Connect][Проверка подлинности приложения Магазина Windows с помощью единого входа Live Connect].

> [WACOM.NOTE]Аутентификация со стороны клиента с использованием Live Connect пока еще не поддерживается серверной мобильной службой для .NET.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

После регистрации пакета приложения не забудьте указать значение **истина** для параметра *useSingleSignOn* при вызове метода [LoginAsync][LoginAsync]. Это предоставит пользователям ускоренную процедуру входа при использовании учетной записи Майкрософт.



  [Краткое руководство: добавление push-уведомлений в мобильную службу]: http://go.microsoft.com/fwlink/p/?LinkId=309101
  [Проверка подлинности приложения Магазина Windows с помощью единого входа Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [LoginAsync]: http://go.microsoft.com/fwlink/p/?LinkId=311594
