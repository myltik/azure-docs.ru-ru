---
title: Примеры кода Azure Active Directory | Документы Майкрософт
description: Предоставляет список примеров кода Azure Active Directory (конечная точка версии 1), сгруппированных по сценарию.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 299e0d4fa53f9b8a2aef2fc881b136aa41aacfe4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157781"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Примеры кода Azure Active Directory (конечная точка версии 1)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Для добавления проверки подлинности и авторизации в веб-приложения и веб-интерфейсы API можно использовать Microsoft Azure Active Directory (Azure AD).

Этот раздел содержит ссылки на примеры, с помощью которых вы сможете получить дополнительные сведения о конечной точке Azure AD версии 1. В этих примерах показано, как это можно сделать. Также приведены фрагменты кода, которые можно использовать в приложениях. На странице примеров кода приведены ссылки на статьи с подробными сведениями о требованиях, установке и настройке. Код указан с комментариями, чтобы можно было понять важные разделы.

> [!NOTE]
> Если вас интересуют примеры кода Azure AD версии 2, см. раздел [Примеры кода версии 2.0 для сценариев](active-directory-v2-code-samples.md).

Сведения о базовых сценариях для каждого типа примера см. в разделе [Сценарии проверки подлинности в Azure AD](active-directory-authentication-scenarios.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Классические и мобильные клиентские приложения, вызывающие Microsoft Graph или веб-API

В следующих примерах показаны общедоступные клиентские приложения (классические и мобильные приложения), которые обращаются к Microsoft Graph или веб-API от имени пользователя.

Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET или ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Классическое приложение (WPF)           | .NET и C# | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Мобильное приложение (UWP)            | .NET и C#  | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (веб-API для одного клиента) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (веб-API для нескольких клиентов)|
Мобильное приложение (Android, iOS, UWP)   | .NET и C# (Xamarin) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Мобильное приложение (Android)           | Android и Java | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
Мобильное приложение (iOS)           | iOS и Objective C | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Классическое приложение (консольное)          | .NET и C# | Имя пользователя и пароль </p> Встроенная проверка подлинности Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Классическое приложение (консольное)           | .NET Core и C# | Профиль устройства | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Веб-приложения

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Веб-приложения, обрабатывающие вход пользователей и вызывающие Microsoft Graph или веб-API с удостоверением пользователя

 платформа | Только вход пользователей | Вызывает Microsoft Graph или AAD Graph| Вызывает другой веб-API ASP.NET или ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Веб-приложения, демонстрирующие управление доступом на основе ролей (авторизация)

В следующих примерах показано, как реализовать управление доступом на основе ролей, которое используется для предоставления доступа к определенным функциям веб-приложения только определенным пользователям. Эти пользователи проходят проверку подлинности в зависимости от того, принадлежат ли они группе или роли Azure AD.

платформа | Образец | ОПИСАНИЕ
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | Веб-приложение MVC .NET 4.5, использующее для проверки подлинности **группы** Azure AD
ASP.NET 4.5 | [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Веб-приложение MVC .NET 4.5, использующее для проверки подлинности **роли** Azure AD

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Управляющая программа (которая обращается к веб-API с удостоверением приложения)

В следующих примерах показаны классические приложения и веб-приложения, которые обращаются к Microsoft Graph или веб-API без указания пользователя (с удостоверением приложения).

Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET или ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Управляющая программа (консольная)          | .NET и C#  | Учетные данные клиента с секретом приложения или сертификатом | | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Управляющая программа (консольная)         | .NET Core и C# | Учетные данные клиента с сертификатом| | [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Классические приложения            | Java | Учетные данные клиента |   [java-native-headless](https://github.com/azure-samples/active-directory-java-native-headless) |
Веб-приложение ASP.NET  | .NET и C# | Учетные данные клиента |    | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Веб-API

### <a name="web-api-protected-by-azure-active-directory"></a>Веб-API, защищенный с помощью Azure Active Directory

В следующем примере показано, как защитить веб-API node.js с помощью Azure AD.

платформа | Образец | ОПИСАНИЕ
 -------- | ------------------- | ---------------------
Node.js | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  Веб-API NodeJS, защищенный с помощью Azure AD и маркеров доступа OAuth 2.0.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Веб-API, вызывающий Microsoft Graph или другой веб-API

В следующих примерах показан веб-API, который вызывает другой веб-API. Во втором примере показана организация условного доступа.

 платформа |  Вызывает Microsoft Graph | Вызывает другой веб-API ASP.NET или ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Одностраничные приложения

В этом примере показано, как создать одностраничное приложение, защищенное с помощью Azure AD.

 платформа |  Вызывает Microsoft Graph | Вызывает собственный API
 -------- |  --------------------- | -------------------------
JavaScript (Angular) или ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp)

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о примерах и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Примеры и учебники сообщества Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>См. также

[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

[Основные понятия и справочные материалы по Azure AD API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Библиотека вспомогательных методов для Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
