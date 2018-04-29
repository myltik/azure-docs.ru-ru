---
title: Библиотеки проверки подлинности Azure Active Directory | Документация Майкрософт
description: Библиотека проверки подлинности Azure AD (ADAL) позволяет разработчикам клиентских приложений легко проверять подлинность пользователей в облачной или локальной Active Directory (AD) и получать маркеры доступа для защиты вызовов API.
services: active-directory
documentationcenter: ''
author: bryanla
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: e2c77b2e47ce3dd900b1b277e802a07cfed84702
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Библиотеки проверки подлинности Azure Active Directory
Библиотека аутентификации Azure Active Directory (ADAL) позволяет разработчикам приложений аутентифицировать пользователей в облачной или локальной среде Active Directory (AD) и получать маркеры для защиты вызовов API. ADAL упрощает для разработчиков работу с проверкой подлинности, предоставляя такие возможности, как:
 - настраиваемый кэш маркеров, в котором хранятся маркеры доступа и маркеры обновления;
 - автоматическое обновление маркеров после истечения срока их действия, если доступен маркер обновления;
 - поддержка асинхронных вызовов методов;
 - и многое другое.

> [!NOTE]
> Ищете библиотеки Azure AD версии 2.0 (MSAL)? Ознакомьтесь с [руководством по библиотекам MSAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

### <a name="client-libraries"></a>Клиентские библиотеки

| платформа | Библиотека | Загрузка | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| Клиент .NET, Магазин Windows, UWP, Xamarin iOS и Android |ADAL .NET версии 3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Классическое приложение](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Справочные материалы](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| Клиент .NET, Магазин Windows, Windows Phone 8.1 |ADAL .NET версии 2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Классическое приложение](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Одностраничное приложение](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Приложение iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Справочные материалы](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[Центральный репозиторий](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Приложение Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [Javadocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Веб-приложение Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Веб-приложение Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

### <a name="server-libraries"></a>Серверные библиотеки

| платформа | Библиотека | Загрузка | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN для AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Приложение MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN для OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Веб-приложение](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Веб-интерфейс API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN для WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Веб-приложение MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Расширения протокола удостоверения для .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Обработчик JWT для .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Клиентские библиотеки версии 2.0 (MSAL)

[Конечная точка Azure AD версии 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) сочетает Azure AD и учетные записи Майкрософт в одной конечной точке. Чтобы получить доступ к этой конечной точке, разработчики могут использовать [предварительные версии библиотек MSAL с поддержкой рабочей среды](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) вместо ADAL.

| платформа | Библиотека | Загрузка | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| Клиент .NET, Магазин Windows, UWP, Xamarin iOS и Android |MSAL для .NET (предварительная версия) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Классическое приложение](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Справочные материалы](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) |
| JavaScript |MSAL для JavaScript (предварительная версия) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Одностраничное приложение](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Справочные материалы](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) |
| iOS |MSAL для iOS (предварительная версия) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Приложение iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Справочные материалы](https://azuread.github.io/docs/objc/) |
| Android |MSAL для Android (предварительная версия) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Приложение Android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Справочные материалы](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Сценарии

Ниже приведены три типичных сценария, в которых ADAL может использоваться для проверки подлинности клиента, обращающегося к удаленному ресурсу.  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Проверка подлинности пользователей собственного клиентского приложения, выполняющегося на устройстве

В этом сценарии у разработчика есть клиентское приложение WPF, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-API. У разработчика есть подписка Azure, он знает, как вызвать нижестоящий веб-интерфейс API и какой клиент Azure AD используется этим веб-интерфейсом. В результате он может упростить проверку подлинности в Azure AD, полностью делегируя процесс проверки подлинности службе ADAL или явно обрабатывая учетные данные пользователя. ADAL упрощает проверку подлинности пользователя, получение маркера доступа и маркера обновления от Azure AD и использование маркера доступа для выполнения запросов к веб-интерфейсу API.

Пример кода, который демонстрирует этот сценарий с использованием проверки подлинности Azure AD, см. в разделе [Собственное клиентское приложение WPF для веб-интерфейса API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Проверка подлинности конфиденциального клиентского приложения, выполняющегося на веб-сервере

В этом сценарии у разработчика есть приложение, запущенное на сервере, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. У разработчика есть подписка Azure, он знает, как вызвать нижестоящую службу и какой клиент Azure AD используется веб-интерфейсом API. В результате он может использовать ADAL для упрощения проверки подлинности в Azure AD, явно обрабатывая учетные данные приложения. ADAL упрощает получение маркера из Azure AD с помощью учетных данных клиента приложения и последующее использование этого маркера для выполнения запросов к веб-интерфейсу API. ADAL также управляет жизненным циклом маркера доступа, кэшируя его и возобновляя его действие при необходимости. Пример кода для такого сценария представлен в описании [консольной управляющей программы для веб-API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Проверка подлинности конфиденциального клиентского приложения, выполняющегося на веб-сервере, от имени пользователя

В этом сценарии у разработчика есть приложение, запущенное на сервере, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. Запрос должен выполняться от имени пользователя Azure AD. У разработчика есть подписка Azure, он знает, как вызвать нижестоящий веб-интерфейс API и какой клиент Azure AD используется службой. После проверки подлинности пользователя для веб-приложения оно может получить код проверки подлинности пользователя из Azure AD. Затем веб-приложение может использовать ADAL для получения маркера доступа и обновления маркера от имени пользователя с помощью кода проверки подлинности и учетных данных клиента, связанных с приложением в Azure AD. После получения маркера доступа приложение может обращаться к веб-интерфейсу API до истечения срока действия маркера. После истечения срока действия маркера веб-приложение может использовать ADAL для получения нового маркера доступа с помощью обновления маркера, полученного ранее. Пример кода для такого сценария представлен в описании [собственного клиента для веб-API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>См. также

- [Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)
- [Сценарии проверки подлинности в Azure Active Directory](active-directory-authentication-scenarios.md)
- [Примеры кода Azure Active Directory](active-directory-code-samples.md)
