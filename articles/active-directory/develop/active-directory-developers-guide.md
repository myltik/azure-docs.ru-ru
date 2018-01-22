---
title: "Azure Active Directory для разработчиков | Документация Майкрософт"
description: "В этой статье представлены общие сведения о входе в рабочие и учебные учетные записи Майкрософт с использованием Azure Active Directory."
services: active-directory
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 89a232af6387f6403e6e341cced16d06e9979dae
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2018
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory для разработчиков
Azure Active Directory (Azure AD) — это облачная служба идентификации, с помощью которой разработчики обеспечивают безопасный вход пользователей с рабочей или учебной учетной записью Майкрософт. В этом документе показано, как с помощью стандартных отраслевых протоколов OAuth 2.0 и OpenID Connect добавить поддержку Azure AD в ваше приложение.

| | |
| --- | --- |
|[Основные сведения об аутентификации](active-directory-authentication-scenarios.md) | Общие сведения об аутентификации с помощью Azure AD. |
|[Типы приложений](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Обзор сценариев аутентификации, поддерживаемых Azure AD. |                                
                                                                              
## <a name="get-started"></a>Начало работы
Из этих пошаговых руководств вы узнаете, как использовать библиотеки аутентификации Майкрософт, чтобы обеспечить вход пользователей Azure AD в систему.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Мобильные и классические приложения](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Мобильные и классические приложения</center> | [Обзор](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Веб-приложения](./media/active-directory-developers-guide/Web_app.png)<br />Веб-приложения</center> | [Обзор](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |  |
| <center>![Одностраничные приложения](./media/active-directory-developers-guide/SPA.png)<br />Одностраничные приложения</center> | [Обзор](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Веб-интерфейсы API](./media/active-directory-developers-guide/Web_API.png)<br />Веб-интерфейсы API</center> | [Обзор](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Обмен между службами](./media/active-directory-developers-guide/Service_App.png)<br />Обмен между службами</center> | [Обзор](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>Практические руководства
В этих руководствах объясняется, как выполнять общие задачи с помощью Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Регистрация приложения](active-directory-integrating-applications.md)           | Как зарегистрировать приложение в Azure AD. |
|[Мультитенантные приложения](active-directory-devhowto-multi-tenant-overview.md)    | Как войти в любую рабочую учетную запись Майкрософт. |
|[Протоколы OAuth и OpenID Connect](active-directory-protocols-openid-connect-code.md)| Как обеспечить вход пользователей и вызывать веб-интерфейсы API, используя протоколы аутентификации Майкрософт. |
|[Дополнительные руководства](active-directory-developers-guide-index.md#guides)        |  Список доступных руководств по Azure AD.   |

## <a name="reference-topics"></a>Справочные материалы
В этих статьях содержатся подробные сведения об API-интерфейсах, сообщениях протокола и терминах, используемых в Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Библиотеки проверки подлинности Azure Active Directory](active-directory-authentication-libraries.md)   | Общие сведения о библиотеках и пакетах SDK, предоставляемых Azure AD. |
| [Примеры кода](active-directory-code-samples.md)                                  | Список всех примеров кода Azure AD. |
| [Глоссарий](active-directory-dev-glossary.md)                                      | Термины и определения слов, используемых в этой документации. |
| [Дополнительные справочные материалы](active-directory-developers-guide-index.md#reference)| Список доступных справочных материалов по Azure AD.   |


> [!NOTE]
> Если вам нужно войти в личные учетные записи Майкрософт, рекомендуем использовать [конечную точку Azure AD версии 2.0](active-directory-appmodel-v2-overview.md). Конечная точка Azure AD версии 2.0 — это объединение личных учетных записей Майкрософт и рабочих учетных записей Майкрософт (от Azure AD) в единую систему аутентификации.


[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]