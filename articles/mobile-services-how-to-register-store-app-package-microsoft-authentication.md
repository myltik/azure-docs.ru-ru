<properties urlDisplayName="Shared Access Signature Part 1" pageTitle="Регистрация пакета приложения для магазина Windows для проверки подлинности Microsoft" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Регистрация пакета приложения для магазина Windows для проверки подлинности Microsoft

Мобильные службы Azure поддерживают методы проверки подлинности как со стороны клиента, так и со стороны сервера. Проверка подлинности со стороны сервера использует поставщиков удостоверений, включая учетную запись Майкрософт. При использовании учетной записи Майкрософт для проверки подлинности со стороны сервера без регистрации приложения с помощью мобильных служб пользователям предлагается вводить учетные данные при каждом запросе проверки подлинности. Если приложение зарегистрировано, учетные данные для входа учетной записи Майкрософт кэшируются и могут использоваться для проверки подлинности без запроса пользователю ввести их снова. В этом разделе показано, как зарегистрировать пакет приложения Магазина Windows, чтобы было удобнее пользоваться учетной записью Майкрософт при применении для проверки подлинности мобильных служб Azure. 

>[WACOM.NOTE]Visual Studio 2013 позволяет легко регистрировать пакет приложения для Магазина Windows в мобильных службах. Дополнительную информацию см. в разделе <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Краткое руководство: добавление push-уведомлений для мобильной службы</a> в Центре разработки для Windows.

Аутентификацию со стороны клиента можно использовать, чтобы обеспечить работу единого входа на устройстве Windows с помощью Live Connect. При использовании интерфейсов API подключения Live Connect вам не нужно выполнять действия, описанные в этом разделе. Дополнительную информацию см. в разделе [Аутентификация приложения для Магазина Windows с помощью единого входа Live Connect].   

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

После регистрации пакета приложение обязательно укажите значение <strong>true</strong> для <em>useSingleSignOn</em> при вызове метода <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> . Это предоставит пользователям ускоренную процедуру входа при использовании учетной записи Майкрософт.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/
[Аутентификация приложения для Магазина Windows с помощью единого входа Live Connect]: /ru-ru/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Get started with users C#] (Начало работы с пользователями для C#): /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with users JavaScript] (Начало работы с пользователями для JavaScript): /ru-ru/develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=35.1-->
