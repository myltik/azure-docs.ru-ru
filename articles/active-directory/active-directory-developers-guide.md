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
   ms.date="02/25/2016"
   ms.author="mbaldwin"/>


# Руководство разработчика по Azure Active Directory

## Обзор
Azure Active Directory является платформой для управления удостоверениями, которая доступна как служба (IDMaaS). Она позволяет разработчикам легко интегрировать в свои приложения функции управления удостоверениями. В следующих статьях содержатся основные сведения о реализации и основные функции платформы Azure Active Directory. Рекомендуется ознакомиться с ними по порядку или сразу перейти к [Приступая к работе](#getting-started), если требуется более подробное ознакомление.


1. [Преимущества интеграции с Azure Active Directory](active-directory-how-to-integrate.md): узнайте, почему интеграция с Azure Active Directory является лучшим решением для обеспечения безопасного входа и авторизации.

1. [Сценарии проверки подлинности Active Directory](active-directory-authentication-scenarios.md): обеспечьте вход в приложение, используя упрощенную проверку подлинности в Azure Active Directory.

1. [Интеграция приложений с Azure Active Directory](active-directory-integrating-applications.md): узнайте, как добавлять, обновлять и удалять приложения из Azure Active Directory, а также изучите рекомендации по добавлению фирменной символики в интегрированные приложения.

1. [Azure Active Directory Graph API](active-directory-graph-api.md): используйте Azure Active Directory Graph API для программного доступа к Azure Active Directory через конечные точки REST API. Обратите внимание, что API Graph Azure AD также доступен через [Microsoft Graph](https://graph.microsoft.io/) — унифицированный API, обеспечивающий доступ к нескольким API облачной службы Майкрософт через одну конечную точку REST API и с использованием единого маркера доступа.

1. [Библиотеки проверки подлинности Azure Active Directory](active-directory-authentication-libraries.md). Выполняйте проверку подлинности пользователей, которым требуются маркеры доступа, с помощью библиотек проверки подлинности Azure AD для .NET, JavaScript, Objective-C, Android и т. д.


## Приступая к работе

Эти учебники предназначены для нескольких платформ и помогут вам быстро приступить к разработке приложений с помощью Azure Active Directory. Вам необходим [клиент Azure Active Directory](active-directory-howto-tenant.md).

### Руководства по быстрому запуску мобильных приложений и приложений для ПК

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Магазин Windows](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova;](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Магазин Windows](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Краткие руководства по началу работы с веб-приложениями

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java:](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Краткие руководства по началу работы с веб-интерфейсами API

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Руководства по быстрому запуску запросов каталога

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## Инструкции

В этих статьях описываются способы выполнения конкретных задач с использованием Azure Active Directory.

- [Как получить клиент Azure Active Directory](active-directory-howto-tenant.md)
- [Добавление приложения в коллекцию приложений Azure Active Directory](active-directory-app-gallery-listing.md)
- [Основные сведения о манифесте приложения Azure Active Directory](active-directory-application-manifest.md)
- [Создание приложения с помощью API Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Отправка веб-приложений для Office 365 на панель мониторинга продавца](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Предварительная версия. Как создавать приложения для входа пользователей с личными, рабочими и учебными учетными записями](active-directory-appmodel-v2-overview.md)
- [Предварительная версия. Как создавать приложения, которые выполняют регистрацию и вход пользователей](../active-directory-b2c/active-directory-b2c-overview.md)


## Справочные материалы

Эти статьи содержат основные справочники по REST и API библиотек проверки подлинности, протоколам, ошибкам, образцам кода и конечных точкам.

###  Поддержка
- [Вопросы с тегами](http://stackoverflow.com/questions/tagged/azure-active-directory). Здесь вы найдете решения Azure Active Directory, связанные с переполнением стека, по тегам [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) и [adal](http://stackoverflow.com/questions/tagged/adal).

### Код

- [Библиотеки Azure Active Directory с открытым исходным кодом](http://github.com/AzureAD). Чтобы быстро найти исходный код библиотеки, воспользуйтесь нашим [списком библиотек](active-directory-authentication-libraries.md).

- [Примеры кода Azure Active Directory ](https://github.com/azure-samples?query=active-directory). Самый простой способ навигации по списку примеров — это [индекс примеров кода](active-directory-code-samples.md).


### API Graph

- [Справочник по API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx). Справочник по REST для API Graph для Azure Active Directory. [Просмотрите интерактивный справочник по API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Области разрешений API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes). Области разрешений OAuth 2.0, которые необходимы для управления доступом приложения к данным каталога в клиенте.

### Библиотеки проверки подлинности

- [.NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): документация для библиотеки проверки подлинности .NET.

### Протоколы проверки подлинности

- [Справочник по протоколу SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx): протокол SAML 2.0 позволяет приложениям предоставлять пользователям единый вход.


- [Справочник по протоколу OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx). Протокол OAuth 2.0 позволяет предоставлять доступ к веб-приложениям и интерфейсам веб-API в клиенте Azure Active Directory.


- [Справочник по протоколу OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): протокол OpenID Connect 1.0 расширяет OAuth 2.0, позволяя использовать его как протокол проверки подлинности.


- [Справочник по протоколу WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx). Протокол WS-Federation 1.2 описан в спецификации федерации веб-служб версии 1.2.

- [Поддерживаемые маркеры и типы утверждений](active-directory-token-and-claims.md). В этом руководстве вы найдете основные сведения об утверждениях в маркерах SAML 2.0 и веб-маркерах JSON (JWT).

## Видеоролики

### Build 2015

В этих обзорных презентациях представители разработчиков освещают ключевые темы, связанные с разработкой приложений с помощью Azure Active Directory, включая IDMaaS, проверку подлинности, федерацию удостоверений и единый вход.

- [Azure Active Directory: "управление удостоверениями как служба" для современных приложений](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Разработка современных веб-приложений с помощью Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Разработка современных приложений в машинном коде с помощью Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure, пятница
[Azure, пятница](https://azure.microsoft.com/documentation/videos/azure-friday/) — это еженедельная серия коротких (10–15 минут) видеоинтервью со специалистами на разные темы, связанные с Azure. Используйте функцию фильтра служб на странице, чтобы просмотреть все видео Azure Active Directory.

- [Удостоверение Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Удостоверение Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Удостоверение Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Социальные сети

- [Блог группы Active Directory](http://blogs.technet.com/b/ad/). Будьте в курсе последних разработок в сфере Azure Active Directory.

- [Блог группы Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam). Информация об Azure Active Directory, связанная с API Graph.

- [Облачная идентификация](http://www.cloudidentity.net). Соображения об управлении удостоверениями от главного руководителя программы Azure Active Directory.

- [Azure Active Directory в Твиттере](https://twitter.com/azuread). Объявления Azure Active Directory длиной не более 140 знаков.

<!---HONumber=AcomDC_0302_2016-->