<properties
   pageTitle="Руководство разработчика по Azure Active Directory"
   description="Полное руководство по ориентированным на разработчиков ресурсам для Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/10/2015"
   ms.author="mbaldwin"/>


# Руководство разработчика по Azure Active Directory

## Обзор
Azure Active Directory является платформой для управления удостоверениями, которая доступна как служба (IDMaaS). Она позволяет разработчикам легко интегрировать в свои приложения функции управления удостоверениями. В следующих статьях содержатся основные сведения о реализации и основные функции платформы Azure Active Directory. Рекомендуется ознакомиться с ними по порядку или сразу перейти к [Приступая к работе](#getting-started), если требуется более подробное ознакомление.


1. **[Преимущества интеграции с Azure AD](active-directory-how-to-integrate.md)**: узнайте, почему интеграция с Azure Active Directory является лучшим решением для обеспечения безопасного входа и авторизации.

1. **[Использование Azure AD для входа](active-directory-authentication-scenarios.md)**: воспользуйтесь преимуществами упрощенной проверки подлинности Azure Active Directory для предоставления входа в приложение.

1. **[Запрос к каталогу](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: используйте Azure Active Directory Graph API для программного доступа к Azure AD через конечные точки REST API.

1. **[Основные сведения о модели приложения](https://msdn.microsoft.com/library/azure/dn151122.aspx)**: дополнительные сведения о регистрации приложения и рекомендации по фирменной символике для мультитенантных приложений.

1. **[Библиотеки](https://msdn.microsoft.com/library/azure/dn151135.aspx)**: простой способ проверки подлинности пользователей для получения маркера доступа с помощью библиотеки проверки подлинности Azure.

Чтобы ознакомиться с обзорами AAD, представленными на конференции Build 2015, см. раздел [Видео](#videos) ниже.


## Приступая к работе

Эти учебники предназначены для нескольких платформ и обучения разработке приложений с помощью Azure Active Directory. Необходимо [получение клиента Azure Active Directory](active-directory-howto-tenant.md).

#### Руководства по быстрому запуску мобильных приложений или приложений для ПК

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Магазин Windows](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


####Руководства по быстрому запуску веб-приложений или веб-интерфейса API

- [Веб-приложение .NET](active-directory-devquickstarts-webapp-dotnet.md)
- [Веб-интерфейс API .NET](active-directory-devquickstarts-webapi-dotnet.md)
- [Javascript](active-directory-devquickstarts-angular.md)
- [Node.js](active-directory-devquickstarts-webapi-nodejs.md)


## Инструкции

В этих статьях описываются способы выполнения конкретных задач с использованием Azure Active Directory (AD).

- [Как получить клиент Azure AD](active-directory-howto-tenant.md)
- [Как разместить свое приложение в коллекции приложений Azure AD](active-directory-app-gallery-listing.md)
- [Как приступить к работе с API-интерфейсами Office 365 в приложениях](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Как добавить веб-приложение для Office 365 на панель мониторинга продавца](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)


## Справочные материалы

Эти статьи содержат основные справочники по REST и API-интерфейсам библиотек проверки подлинности, протоколам, ошибкам, образцам кода и конечных точкам.

###  Поддержка
- **[Для получения поддержки](http://stackoverflow.com/questions/tagged/azure-active-directory)**: поиск решения для Azure AD на сайте Stack Overflow по тегам [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) and [adal](http://stackoverflow.com/questions/tagged/adal).

### Код

- **[Библиотеки Azure AD с открытым исходным кодом](http://github.com/AzureAD)**: самый простой способ найти источник библиотеки с помощью нашего [списка библиотек](https://msdn.microsoft.com/library/azure/dn151135.aspx).

- **[Образцы Azure AD](http://github.com/AzureADSamples)**: самый простой способ перехода по списку образцов [Индекса образцов кода](active-directory-code-samples.md).


### Graph API

- **[Справочник по API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: справочник по REST для Azure Active Directory Graph API. [Просмотреть новый интерактивный справочник по API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **[Области разрешений API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)**: OAuth 2.0 области разрешений, которые используются для управления доступом приложения к каталогу данных в клиенте.


### Протоколы проверки подлинности

- **[Справочник по протоколу SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx)**: протокол SAML 2.0 позволяет приложениям предоставлять пользователям единый вход.


- **[Справочник по протоколу OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)**: протокол OAuth 2.0 обеспечивает авторизацию доступа к веб-приложениям и веб-интерфейсам API в клиенте Azure AD.


- **[Справочник по протоколу OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx)**: протокол OpenID Connect 1.0 расширяет OAuth 2.0 для использования в качестве протокола проверки подлинности..


- **[Справочник по протоколу WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx)**: протокол WS-Federation 1.2, как указано в спецификации федерации веб-служб версии 1.2.

- **[Поддерживаемые утверждения и маркеры безопасности](active-directory-token-and-claims.md)**: общие сведения и анализ утверждений в маркерах SAML 2.0 и веб-токенах JSON (JWT).

## Видео

### Build 2015

Представители разработчиков освещают ключевые темы, связанные с разработкой приложений с помощью Azure Active Directory, включая IDMaaS, проверку подлинности, федерацию удостоверений и единый вход.

- **[Azure Active Directory: «управление удостоверениями как служба» для современных приложений](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[Разработка современных веб-приложений с помощью Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[Разработка современных приложений в машинном коде с помощью Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) еженедельные 10–15-минутные пятничные видеоинтервью со специалистами по различным вопросам Azure. Используйте функцию фильтра служб на странице, чтобы просмотреть все видео Azure Active Directory.

- **[Удостоверение Azure 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**
- **[Удостоверение Azure 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**
- **[Удостоверение Azure 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**

## Социальные сети

- **[Блог группы Active Directory](http://blogs.technet.com/b/ad/)**: будьте в курсе последних разработок в сфере Azure AD.

- **[Блог Azure AD Graph](http://blogs.msdn.com/b/aadgraphteam)**: сведения об Azure AD, относящиеся к API Graph.

- **[Облачная идентификация](http://www.cloudidentity.net)**: соображения насчет «управления удостоверениями, как служба» от главного руководителя программы Azure Active Directory.

- **[Azure AD в Twitter](https://twitter.com/azuread)**: объявления Azure AD в 140 символов или меньше.

<!---HONumber=62-->