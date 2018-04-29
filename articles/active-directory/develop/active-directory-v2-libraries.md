---
title: Библиотеки аутентификации Azure Active Directory версии 2.0 | Документация Майкрософт
description: Сведения о совместимых клиентских библиотеках и серверных библиотеках ПО промежуточного слоя, а также ссылки на связанные библиотеки, исходный код и примеры для конечной точки Azure Active Directory версии 2.0.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0d9e2831f9d8676eb3e7fac91c58f3977f2e0f32
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Библиотеки проверки подлинности Azure Active Directory версии 2.0
[Конечная точка Azure Active Directory (Azure AD) версии 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) поддерживает стандартные отраслевые протоколы OAuth 2.0 и OpenID Connect 1.0. С конечной точкой версии 2.0 можно использовать различные библиотеки от корпорации Майкрософт и других организаций.

При создании приложения, использующего конечную точку версии 2.0, рекомендуется использовать библиотеки, написанные специалистами в области протоколов, сторонниками методов жизненного цикла разработки защищенных приложений (SDL), [таких, каким следуют в корпорации Майкрософт][Microsoft-SDL]. Если вы приняли решение написать код поддержки для протоколов, рекомендуется следовать методам SDL и внимательно просмотреть рекомендации по безопасности в спецификациях стандартов для каждого из протоколов.

> [!NOTE]
> Ищете библиотеки Azure AD версии 1.0 (ADAL)? Ознакомьтесь с [руководством по библиотекам ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Типы библиотек
Конечная точка Azure AD версии 2.0 поддерживает два типа библиотек.

* **Клиентские библиотеки.** Собственные клиенты и серверы используют клиентские библиотеки, чтобы получить маркер доступа для вызова ресурса, например Microsoft Graph.
* **Серверные библиотеки ПО промежуточного слоя.** Веб-приложения используют серверные библиотеки ПО промежуточного слоя для входа пользователей. Веб-API используют серверные библиотеки ПО промежуточного слоя для проверки маркеров, отправляемых собственными клиентами или другими серверами.

## <a name="library-support"></a>Поддержка библиотек
Так как при использовании конечной точки версии 2.0 можно выбрать любую библиотеку, соответствующую стандартам, важно знать, куда обратиться за поддержкой. Проблемы и запросы возможностей в коде библиотеки отправляются владельцу библиотеки. Проблемы и запросы возможностей в реализации протокола на стороне службы отправляются в корпорацию Майкрософт.

Для библиотек предусмотрено две категории поддержки:

* **Библиотеки, поддерживаемые корпорацией Майкрософт.** Корпорация Майкрософт предоставляет исправления для этих библиотек, к тому же она провела для них комплексную экспертизу жизненного цикла разработки защищенных приложений.
* **Совместимые библиотеки.** Корпорация Майкрософт протестировала такие библиотеки в основных сценариях и подтвердила их совместимость с конечной точкой версии 2.0. Корпорация Майкрософт не предоставляет исправления для этих библиотек и не выполняла их проверку. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки.

Список библиотек, совместимых с конечной точкой версии 2.0, см. в следующих разделах этой статьи.


## <a name="microsoft-supported-client-libraries"></a>Клиентские библиотеки, поддерживаемые корпорацией Майкрософт

> [!IMPORTANT]
> Библиотеки MSAL предварительной версии можно использовать в рабочей среде. Для этих библиотек предоставляется такая же поддержка рабочего уровня, как и для текущих рабочих библиотек (ADAL). Во время действия предварительной версии мы можем вносить изменения в API MSAL, формат внутреннего кэша и другие механизмы этих библиотек без уведомления. Вы будете применять их вместе с исправлениями ошибок или обновлениями функций. Это может повлиять на приложение. Например, изменение формата кэша может повлиять на пользователей (в частности, потребуется повторный вход). Изменение API может потребовать обновления кода. После выпуска общедоступной версии вам понадобится перейти на нее в течение шести месяцев, так как приложения, написанные с помощью предварительной версии библиотеки, могут перестать работать.

| платформа | Библиотека | Загрузка | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| Клиент .NET, Магазин Windows, UWP, Xamarin iOS и Android | MSAL для .NET (предварительная версия) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Классическое приложение](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (предварительная версия) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Одностраничное приложение](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS, macOS | MSAL (предварительная версия) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Приложение iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (предварительная версия) | [Центральный репозиторий](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Приложение Android](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [Javadocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Серверные библиотеки ПО промежуточного слоя, поддерживаемые корпорацией Майкрософт

| платформа | Библиотека | Загрузка | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | ПО промежуточного слоя OWIN OpenID Connect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Приложение MVC](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | ПО промежуточного слоя носителя OWIN OAuth для AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | Обработчик JWT для .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ПО промежуточного слоя ASP.NET OpenID Connect |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Безопасность ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Приложение MVC](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ПО промежуточного слоя носителя ASP.NET OAuth |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Безопасность ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | Обработчик JWT для .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Веб-приложение](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Совместимые клиентские библиотеки
| платформа | Имя библиотеки | Проверенные версии | Исходный код | Образец |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Пример встроенного приложения](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Пример встроенного приложения](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Безопасная проверка пароля](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java (scribejava)](https://github.com/scribejava/scribejava) | [Версия 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [The PHP League (oauth2-client)](https://github.com/thephpleague/oauth2-client) | [Версия 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Связанная информация
Дополнительные сведения о конечной точке Azure AD версии 2.0 см. в статье [Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
