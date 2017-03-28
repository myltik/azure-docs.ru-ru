---
title: "Библиотеки аутентификации Azure Active Directory версии&2;.0 | Документация Майкрософт"
description: "Сведения о совместимых клиентских библиотеках и серверных библиотеках ПО промежуточного слоя, а также ссылки на связанные библиотеки, исходный код и примеры для конечной точки Azure Active Directory версии&2;.0."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2016
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 9710381787cc1fe570443467b6980616879c503f
ms.openlocfilehash: ab5576d09e5a51cd7ae602d57693558715e12d66
ms.lasthandoff: 02/27/2017


---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Библиотеки проверки подлинности Azure Active Directory версии&2;.0
Конечная точка Azure Active Directory (Azure AD) версии 2.0 поддерживает стандартные отраслевые протоколы OAuth 2.0 и OpenID Connect 1.0. С конечной точкой версии&2;.0 можно использовать различные библиотеки от корпорации Майкрософт и других организаций.

При создании приложения, использующего конечную точку версии&2;.0, рекомендуется использовать библиотеки, написанные специалистами в области протоколов, сторонниками методов жизненного цикла разработки защищенных приложений (SDL), [таких, каким следуют в корпорации Майкрософт][Microsoft-SDL]. Если вы приняли решение написать код поддержки для протоколов, рекомендуется следовать методам SDL и внимательно просмотреть рекомендации по безопасности в спецификациях стандартов для каждого из протоколов.

## <a name="types-of-libraries"></a>Типы библиотек
Azure AD версии&2;.0 поддерживает два типа библиотек.

* **Клиентские библиотеки.** Собственные клиенты и серверы используют клиентские библиотеки, чтобы получить маркер доступа для вызова ресурса, например Microsoft Graph.
* **Серверные библиотеки ПО промежуточного слоя.** Веб-приложения используют серверные библиотеки ПО промежуточного слоя для входа пользователей. Веб-API используют серверные библиотеки ПО промежуточного слоя для проверки маркеров, отправляемых собственными клиентами или другими серверами.

## <a name="library-support"></a>Поддержка библиотек
Так как при использовании конечной точки версии&2;.0 можно выбрать любую библиотеку, соответствующую стандартам, важно знать, куда обратиться за поддержкой. Проблемы и запросы возможностей в коде библиотеки отправляются владельцу библиотеки. Проблемы и запросы возможностей в реализации протокола на стороне службы отправляются в корпорацию Майкрософт.

Для библиотек предусмотрено две категории поддержки:

* **Библиотеки, поддерживаемые корпорацией Майкрософт.** Корпорация Майкрософт предоставляет исправления для этих библиотек, к тому же она провела для них комплексную экспертизу жизненного цикла разработки защищенных приложений.
* **Совместимые библиотеки.** Корпорация Майкрософт протестировала такие библиотеки в основных сценариях и подтвердила их совместимость с конечной точкой версии&2;.0. Корпорация Майкрософт не предоставляет исправления для этих библиотек и не выполняла их проверку. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки.

Список библиотек, совместимых с конечной точкой версии&2;.0, см. в следующих разделах этой статьи.

## <a name="microsoft-supported-client-libraries"></a>Клиентские библиотеки, поддерживаемые корпорацией Майкрософт
| Платформа | Имя библиотеки | Загрузить | Исходный код | Образец |
|:---:|:---:|:---:|:---:|:---:|
| .NET, Магазин Windows, Xamarin |Библиотека аутентификации Майкрософт (MSAL) для .NET |[Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] |[MSAL для .NET (GitHub)][ClientLib-NET-Repo] |[Пример классического собственного клиента Windows][ClientLib-NET-Sample] |
| Node.js |Подключаемый модуль Passport.js Microsoft Azure Active Directory |[Passport-Azure-AD (npm)][ClientLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] |Скоро |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Серверные библиотеки ПО промежуточного слоя, поддерживаемые корпорацией Майкрософт
| Платформа | Имя библиотеки | Загрузить | Исходный код | Образец |
|:---:|:---:|:---:|:---:|:---:|
| .NET 4.x |ПО промежуточного слоя OWIN OpenID Connect для ASP.NET |[Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] |[Проект Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] |[Пример веб-приложения][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x |ПО промежуточного слоя носителя OWIN OAuth для ASP.NET |[Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] |[Проект Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] |[Пример веб-API][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core |ПО промежуточного слоя OWIN OpenID Connect для .NET Core |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[Безопасность ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Пример веб-приложения][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core |ПО промежуточного слоя носителя OWIN OAuth для .NET Core |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[Безопасность ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |Скоро |
| Node.js |Подключаемый модуль Passport.js Microsoft Azure Active Directory |[Passport-Azure-AD (npm)][ServerLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] |[Пример веб-приложения][ServerLib-Node-Sample] |

<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>Совместимые клиентские библиотеки
| Платформа | Имя библиотеки | Проверенные версии | Исходный код | Образец |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Пример встроенного приложения](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Пример встроенного приложения](active-directory-v2-devquickstarts-ios.md) |
| Java | [Scribe Java (scribejava)](https://github.com/scribejava/scribejava) | [Версия 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | Скоро |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Безопасная проверка пароля](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| PHP | [The PHP League (oauth2-client)](https://github.com/thephpleague/oauth2-client) | [Версия 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | Скоро |
| Python-Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |[Веб-приложение](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |Скоро |

<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>Совместимые серверные библиотеки ПО промежуточного слоя
Скоро

## <a name="related-content"></a>Связанная информация
Дополнительные сведения о конечной точке Azure AD версии&2;.0 см. в статье [Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении][AAD-App-Model-V2-Overview].

Оставляйте свои замечания и пожелания в разделе DISQUS ниже. Они помогают нам улучшать содержимое веб-сайта.

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
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
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
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/

