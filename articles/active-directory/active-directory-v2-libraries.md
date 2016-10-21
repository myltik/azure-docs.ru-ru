<properties
   pageTitle="Библиотеки версии 2.0 для Azure Active Directory | Microsoft Azure"
   description="Содержит список всех совместимых клиентских библиотек и серверных библиотек ПО промежуточного слоя, а также ссылки на связанные библиотеки, исходный код и примеры для конечной точки Azure Active Directory версии 2.0."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/26/2016"
   ms.author="skwan;bryanla"/>


# Azure Active Directory (AD) версии 2.0 и библиотеки аутентификации
Конечная точка версии 2.0 Azure AD поддерживает стандартные отраслевые протоколы OAuth 2.0 и OpenID Connect 1.0. Конечную точку версии 2.0 можно использовать с множеством библиотек корпорации Майкрософт и сторонних поставщиков.

При создании приложения, использующего конечную точку версии 2.0, рекомендуется использовать библиотеки, написанные специалистами в области протоколов, сторонниками методов жизненного цикла разработки защищенных приложений (SDL), [таких, каким следуют в корпорации Майкрософт](Microsoft-SDL). Если принято решение написать код поддержки для протоколов, рекомендуется следовать методам SDL и внимательно просмотреть рекомендации по безопасности в спецификациях стандартов для каждого из протоколов.

## Типы библиотек
С версией 2.0 совместимы два вида библиотек:

- **Клиентские библиотеки**. Клиентские библиотеки используются во встроенных клиентах и на серверах для получения маркеров доступа для вызова ресурса, например Microsoft Graph.
- **Серверные библиотеки ПО промежуточного слоя**. Серверные библиотеки ПО промежуточного слоя используют веб-приложения для реализации входа пользователя и веб-API для проверки маркеров, отправленных встроенными клиентами или другими серверами.

## Поддержка библиотек
Так как при использовании конечной точки версии 2.0 можно выбрать любую библиотеку, соответствующую стандартам, важно знать, куда обратиться за поддержкой. Проблемы и запросы возможностей в коде библиотеки отправляются владельцу библиотеки. Проблемы и запросы возможностей в реализации протокола на стороне службы отправляются в корпорацию Майкрософт.

Для библиотек предусмотрено две категории поддержки:

- **Библиотеки, поддерживаемые корпорацией Майкрософт**. Корпорация Майкрософт предоставляет исправления для этих библиотек. Корпорация Майкрософт провела комплексную экспертизу жизненного цикла разработки защищенных приложений для этих библиотек.
- **Совместимые библиотеки**. Корпорация Майкрософт протестировала несколько библиотек в основных сценариях и подтвердила их совместимость с конечной точкой версии 2.0. Корпорация Майкрософт не предоставляет исправления для этих библиотек и не выполняла их проверку. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки.

Список библиотек, совместимых с конечной точкой версии 2.0, см. в следующих разделах.

## Клиентские библиотеки, поддерживаемые корпорацией Майкрософт
| Платформа| Имя библиотеки| Загрузить | Исходный код | Образец |
| :-: | :-: | :-: | :-: | :-: |
| .NET, Магазин Windows, Xamarin | Библиотека аутентификации Майкрософт (MSAL) для .NET | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL для .NET (Github)][ClientLib-NET-Repo] | [Пример встроенного настольного клиента Windows][ClientLib-NET-Sample] |
| Node.js | Подключаемый модуль Passport.js Microsoft Azure Active Directory | [Passport-Azure-AD (npm)][ClientLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] | Скоро |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## Серверные библиотеки ПО промежуточного слоя, поддерживаемые корпорацией Майкрософт
| Платформа| Имя библиотеки| Загрузить | Исходный код | Образец |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | ПО промежуточного слоя OWIN OpenID Connect для ASP.NET | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [Проект Katana (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Пример веб-приложения][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | ПО промежуточного слоя носителя OWIN OAuth для ASP.NET | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [Проект Katana (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [Пример веб-API][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core | ПО промежуточного слоя OWIN OpenID Connect для .NET Core | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [Безопасность ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Пример веб-приложения][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core | ПО промежуточного слоя носителя OWIN OAuth для .NET Core | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [Безопасность ASP.NET (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | Скоро |
| Node.js | Подключаемый модуль Passport.js Microsoft Azure Active Directory | [Passport-Azure-AD (npm)][ServerLib-Node-Lib] | [Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] | [Пример веб-приложения][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .Net | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .Net (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## Совместимые клиентские библиотеки
| Платформа| Имя | Проверенные версии | Исходный код | Образец |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0\.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [Пример встроенного приложения](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1\.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [Пример встроенного приложения](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1\.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | Скоро |
| Python — Flask | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | 0\.9.3 | [Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) | Скоро |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | Скоро |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## Совместимые серверные библиотеки ПО промежуточного слоя 
Скоро


## Связанная информация
Дополнительные сведения о конечной точке версии 2.0 Azure AD см. в статье [Azure AD App Model v2 Overview][AAD-App-Model-V2-Overview] \(Обзор модели приложения Azure AD версии 2.0).

Оставляйте свои замечания и пожелания в разделе DISQUS ниже. Они помогают нам улучшать содержимое веб-сайта.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]: [ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]: [ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]: [ClientLib-Android-Repo]:
[ClientLib-Android-Sample]: [ClientLib-Js-Lib]:
[ClientLib-Js-Repo]: [ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]: /
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]: /
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/

<!---HONumber=AcomDC_0928_2016-->