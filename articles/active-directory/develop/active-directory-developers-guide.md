---
title: "Azure Active Directory для разработчиков | Документация Майкрософт"
description: "В этой статье представлен обзор входов в рабочие и учебные учетные записи Майкрософт с использованием Azure Active Directory."
services: active-directory
author: dstrockis
manager: mbaldwin
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
ms.openlocfilehash: dce813b885d492343428428056a2e8aada27b461
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory для разработчиков
Azure Active Directory является облачной службой идентификации, которая позволяет разработчикам обеспечить безопасный вход любого пользователя с помощью рабочей или учебной учетной записи, поддерживаемой Майкрософт.  В приведенной здесь документации показано, как с помощью стандартных отраслевых протоколов проверки подлинности OAuth и OpenID Connect добавить поддержку Azure AD в ваше приложение.

| | |
| --- | --- |
|[Сценарии аутентификации в Azure Active Directory](active-directory-authentication-scenarios.md) | Общие сведения о проверке подлинности с помощью Azure AD |
|[Типы приложений](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Обзор сценариев проверки подлинности, поддерживаемых Azure AD |                                
                                                                              
## <a name="get-started"></a>Начало работы
Эти рекомендации помогут вам использовать наши библиотеки аутентификации для входа в систему пользователей Azure Active Directory.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Классические и мобильные приложения](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Классические и мобильные приложения</center> | [Обзор](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Веб-приложения](./media/active-directory-developers-guide/Web_app.png)<br />Веб-приложения</center> | [Обзор](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![Одностраничные приложения](./media/active-directory-developers-guide/SPA.png)<br />Одностраничные приложения</center> | [Обзор](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Веб-интерфейсы API](./media/active-directory-developers-guide/Web_API.png)<br />Веб-интерфейсы API</center> | [Обзор](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Обмен между службами](./media/active-directory-developers-guide/Service_App.png)<br />Обмен между службами</center> | [Обзор](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[Учетные данные клиента OAuth 2.0](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>Руководства
Эти статьи содержат сведения о способах выполнения общих задач с использованием Azure Active Directory.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Интеграция приложений с Azure Active Directory](active-directory-integrating-applications.md)           | Как зарегистрировать приложение в Azure AD |
|[Мультитенантные приложения](active-directory-devhowto-multi-tenant-overview.md)    | Как войти в любую рабочую учетную запись Майкрософт |
|[Предоставление доступа к веб-приложениям с помощью OpenID Connect и Azure Active Directory](active-directory-protocols-openid-connect-code.md)| Как выполнять вход пользователей и вызвать веб-API с помощью наших современных протоколов проверки подлинности |
|[Дополнительные руководства](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>Справочные материалы
В этих статьях содержатся подробные сведения об API, сообщениях протокола и терминах, используемых в Azure Active Directory.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Библиотеки проверки подлинности Azure Active Directory](active-directory-authentication-libraries.md)   | Общие сведения о библиотеках и пакетах SDK, предоставляемых Azure AD |
| [Примеры кода](active-directory-code-samples.md)                                  | Список всех примеров кода Azure AD |
| [Глоссарий](active-directory-dev-glossary.md)                                      | Термины и определения слов, используемых в данной документации |
| [Дополнительные справочные материалы](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>Справка и поддержка
Это лучшие места, чтобы получить помощь в разработке для Azure Active Directory.

|  |  
|---|
|[Stack Overflow`azure-active-directory` и `adal`теги](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Отзывы об Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|
| [Ознакомьтесь с чатом разработки Майкрософт (бесплатно в течение ограниченного периода)](http://aka.ms/devchat) |

<br />

> [!NOTE]
> Если вам нужно войти в личные учетные записи Майкрософт, вы можете рассмотреть возможность использования [конечной точки Azure AD v2.0](active-directory-appmodel-v2-overview.md).  Конечная точка Azure AD v2.0 — это объединение личных учетных записей Майкрософт и рабочих учетных записей Майкрософт (от Azure AD) в единую систему проверки подлинности.
