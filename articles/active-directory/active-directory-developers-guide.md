<properties
   pageTitle="Руководство разработчика по Azure Active Directory | Microsoft Azure"
   description="Данная статья содержит полное руководство по ориентированным на разработчиков ресурсам для Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/12/2015"
   ms.author="mbaldwin"/>


# Руководство разработчика по Azure Active Directory

## Обзор
Azure Active Directory является платформой для управления удостоверениями, которая доступна как служба (IDMaaS). Она позволяет разработчикам легко интегрировать в свои приложения функции управления удостоверениями. В следующих статьях содержатся основные сведения о реализации и основные функции платформы Azure Active Directory. Рекомендуется ознакомиться с ними по порядку или сразу перейти к [Приступая к работе](#getting-started), если требуется более подробное ознакомление.


1. [Интеграция с Azure Active Directory](active-directory-how-to-integrate.md): узнайте, почему интеграция с Azure Active Directory обеспечивает наилучшее решение для безопасного входа и авторизации.

1. [Сценарии проверки подлинности Active Directory](active-directory-authentication-scenarios.md): обеспечьте вход в приложение, используя упрощенную проверку подлинности в Azure Active Directory.

1. [API Graph Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974476.aspx): используйте API Graph Azure Active Directory для программного доступа к Azure Active Directory через конечные точки API REST.

1. [Интеграция приложений в Azure Active Directory](active-directory-integrating-applications.md): узнайте больше о регистрации приложения и ознакомьтесь с рекомендациями по фирменному стилю многопользовательских приложений.

1. [Библиотеки проверки подлинности Azure Active Directory](active-directory-authentication-libraries.md): используйте библиотеки Azure для простой проверки подлинности пользователей, которым требуются маркеры доступа.

Чтобы ознакомиться с обзорами Azure Active Directory, представленными на конференции Build 2015, см. раздел [Видео](#videos) ниже.


## Приступая к работе

Эти учебники предназначены для нескольких платформ и помогут вам быстро приступить к разработке приложений с помощью Azure Active Directory. Для обучения требуется [клиент Azure Active Directory](active-directory-howto-tenant.md).

### Руководства по быстрому запуску мобильных приложений или приложений для ПК

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Магазин Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova;](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Магазин Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)


### Руководства по быстрому запуску веб-приложений или веб-API

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|:--:|:--:
|[Веб-приложение .NET](active-directory-devquickstarts-webapp-dotnet.md)|[Веб-интерфейс API .NET](active-directory-devquickstarts-webapi-dotnet.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)


## Инструкции

В этих статьях описываются способы выполнения конкретных задач с использованием Azure Active Directory.

- [Как получить клиент Azure Active Directory](active-directory-howto-tenant.md)
- [Добавление приложения в коллекцию приложений Azure Active Directory](active-directory-app-gallery-listing.md)
- [Создание приложения с помощью API Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Отправка веб-приложений для Office 365 на панель мониторинга продавца](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Предварительная версия. Как создавать приложения для входа пользователей с личными, рабочими и учебными учетными записями](active-directory-appmodel-v2-overview.md)


## Справочные материалы

Эти статьи содержат основные справочники по REST и API библиотек проверки подлинности, протоколам, ошибкам, образцам кода и конечных точкам.

###  Поддержка
- [Вопросы с тегами](http://stackoverflow.com/questions/tagged/azure-active-directory): ищите решения Azure Active Directory, связанные с переполнением стека, по тегам [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) и [adal](http://stackoverflow.com/questions/tagged/adal).

### Код

- [Библиотеки Azure AD с открытым исходным кодом](http://github.com/AzureAD): самый простой способ найти источник библиотеки — это [список библиотек](active-directory-authentication-libraries.md).

- [Примеры кода Azure Active Directory](http://github.com/AzureADSamples): самый простой способ навигации по списку примеров — это [индекс примеров кода](active-directory-code-samples.md).


### Graph API

- [Справочник по API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): справочник по REST для API Graph Azure Active Directory. [Просмотрите новый интерактивный справочник по API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Области разрешений API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes): области разрешений OAuth 2.0, которые используются для управления доступом приложения к каталогу данных в клиенте.


### Протоколы проверки подлинности

- [Справочник по протоколу SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx): протокол SAML 2.0 позволяет приложениям предоставлять пользователям единый вход.


- [Справочник по протоколу OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx): протокол OAuth 2.0 обеспечивает авторизацию доступа к веб-приложениям и веб-API в клиенте Azure Active Directory.


- [Справочник по протоколу OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): протокол OpenID Connect 1.0 расширяет OAuth 2.0, позволяя использовать его как протокол проверки подлинности.


- [Справочник по протоколу WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): протокол WS-Federation 1.2 описан в спецификации федерации веб-служб версии 1.2.

- [Поддерживаемые маркеры и типы утверждений](active-directory-token-and-claims.md): это руководство позволит вам понять и оценить утверждения в маркерах SAML 2.0 и веб-маркерах JSON (JWT).

## Видеоролики

### Build 2015

В этих обзорных презентациях представители разработчиков освещают ключевые темы, связанные с разработкой приложений с помощью Azure Active Directory, включая IDMaaS, проверку подлинности, федерацию удостоверений и единый вход.

- [Azure Active Directory: "управление удостоверениями как служба" для современных приложений](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [Разработка современных веб-приложений с помощью Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [Разработка современных приложений в машинном коде с помощью Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) — это еженедельные 10–15-минутные пятничные видеоинтервью со специалистами по различным связанным с Azure вопросам. Используйте функцию фильтра служб на странице, чтобы просмотреть все видео Azure Active Directory.

- [Удостоверение Azure 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Удостоверение Azure 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Удостоверение Azure 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Социальные сети

- [Блог группы Active Directory](http://blogs.technet.com/b/ad/): будьте в курсе последних разработок в сфере Azure Active Directory.

- [Блог группы Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam): информация об Azure Active Directory, которая относится к API Graph.

- [Облачная идентификация](http://www.cloudidentity.net): соображения насчет "управления удостоверениями как служба" от главного руководителя программы Azure Active Directory.

- [Azure Active Directory в Twitter](https://twitter.com/azuread): объявления Azure Active Directory длиной 140 символов и меньше.

<!---HONumber=August15_HO8-->