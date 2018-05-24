---
title: Azure Active Directory для разработчиков | Документация Майкрософт
description: В этой статье представлены общие сведения о входе в рабочие и учебные учетные записи Майкрософт с использованием Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6f3c0e93b20bbc570f4715318a49b502549ff295
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257555"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory для разработчиков

Azure Active Directory (Azure AD) — это облачная служба идентификации, которая позволяет разработчиками создавать приложения с поддержкой безопасного входа пользователей с рабочей или учебной учетной записью Майкрософт. Azure AD могут использовать разработчики, которые создают приложения с одним клиентом и бизнес-приложения, а также разрабатывают мультитенантные приложения. Помимо применения базовых возможностей входа, Azure AD также позволяет приложениям вызывать такие API Майкрософт, как [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) и пользовательские API, которые созданы на платформе Azure AD. В этом документе показано, как с помощью стандартных отраслевых протоколов, таких как OAuth 2.0 и OpenID Connect, добавить поддержку Azure AD в ваше приложение.

> [!NOTE]
> Большая часть содержимого на этой странице описывает конечную точку Azure AD версии 1.0, которая поддерживает только рабочие или учебные учетные записи Майкрософт. Сведения о том, как войти в личную учетную запись или учетную запись пользователя Майкрософт, см. в статье [Настройка входа пользователей с помощью учетной записи Майкрософт и Azure Active Directory в одном приложении](active-directory-appmodel-v2-overview.md). Конечная точка Azure AD версии 2.0 предоставляет разработчикам унифицированные возможности для работы с приложениями, которые должны поддерживать вход пользователей с учетными записями Azure AD (рабочими и учебными) и личными учетными записями Майкрософт.

| | |
| --- | --- |
|[Основные сведения об аутентификации](active-directory-authentication-scenarios.md) | Общие сведения об аутентификации с помощью Azure AD. |
|[Типы приложений](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Обзор сценариев аутентификации, поддерживаемых Azure AD. |      
| | |

## <a name="get-started"></a>Начало работы
Из перечисленных ниже пошаговых руководств вы узнаете, как создать приложение на предпочитаемой платформе с использованием пакета SDK для библиотеки аутентификации Azure AD (ADAL). Сведения об использовании библиотеки аутентификации Майкрософт (MSAL) см. в статье [Настройка входа пользователей с помощью учетной записи Майкрософт и Azure Active Directory в одном приложении](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Мобильные и классические приложения](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Мобильные и классические приложения</center> | [Обзор](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Веб-приложения](./media/active-directory-developers-guide/Web_app.png)<br />Веб-приложения</center> | [Обзор](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) | |
| <center>![Одностраничные приложения](./media/active-directory-developers-guide/SPA.png)<br />Одностраничные приложения</center> | [Обзор](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Веб-интерфейсы API](./media/active-directory-developers-guide/Web_API.png)<br />Веб-интерфейсы API</center> | [Обзор](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Обмен между службами](./media/active-directory-developers-guide/Service_App.png)<br />Обмен между службами</center> | [Обзор](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Практические руководства
В приведенных ниже пошаговых руководствах объясняется, как выполнять общие задачи в Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Регистрация приложения](active-directory-integrating-applications.md)           | Как зарегистрировать приложение в Azure AD. |
|[Мультитенантные приложения](active-directory-devhowto-multi-tenant-overview.md)    | Как войти в любую рабочую учетную запись Майкрософт. |
|[Протоколы OAuth и OpenID Connect](active-directory-protocols-openid-connect-code.md)| Как обеспечить вход пользователей и вызывать веб-интерфейсы API, используя протоколы аутентификации Майкрософт. |
|  |  |

## <a name="reference-topics"></a>Справочные материалы
В этих статьях содержатся подробные сведения об API-интерфейсах, сообщениях протокола и терминах, используемых в Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Библиотеки проверки подлинности Azure Active Directory](active-directory-authentication-libraries.md)   | Общие сведения о библиотеках и пакетах SDK, предоставляемых Azure AD. |
| [Примеры кода](active-directory-code-samples.md)                                  | Список всех примеров кода Azure AD. |
| [Глоссарий](active-directory-dev-glossary.md)                                      | Термины и определения слов, используемых в этой документации. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
