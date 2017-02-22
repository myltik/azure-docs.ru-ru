---
title: "Библиотеки проверки подлинности Azure Active Directory | Документация Майкрософт"
description: "Библиотека проверки подлинности Azure AD (ADAL) позволяет разработчикам клиентских приложений легко проверять подлинность пользователей в облачной или локальной Active Directory (AD) и получать маркеры доступа для защиты вызовов API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/13/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: f369cac86f96227bb6c0881004d8dda485a26ee2
ms.openlocfilehash: c0b0e94175359bde64cf0d2bcefb53cb7530e391


---
# <a name="azure-active-directory-authentication-libraries"></a>Библиотеки проверки подлинности Azure Active Directory
Библиотека проверки подлинности Azure AD (ADAL) позволяет разработчикам клиентских приложений легко проверять подлинность пользователей в облачной или локальной Active Directory (AD) и получать маркеры доступа для защиты вызовов API. ADAL имеет множество функций, которые упрощают проверку подлинности для разработчиков. К ним относятся поддержка асинхронных операций, настраиваемый кэш маркеров, в котором хранятся маркеры доступа и маркеры обновления, автоматическое обновление маркера, когда срок действия маркера доступа истекает и маркер обновления доступен, и многие другие функции. ADAL берет на себя большую часть сложных операций и позволяет разработчику сосредоточиться на бизнес-логике своего приложения и легко защитить ресурсы, не будучи экспертом в области безопасности.

ADAL доступна на различных платформах.

### <a name="client-libraries"></a>Клиентские библиотеки

| Платформа | Библиотека | Загрузить | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| Клиент .NET, Магазин Windows, UWP, Xamarin iOS и Android |ADAL .NET версии&3; |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Классическое приложение](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[Справочные материалы](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| Клиент .NET, Магазин Windows, Windows Phone 8.1 |ADAL .NET версии&2; |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [Классическое приложение](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[Справочные материалы](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Одностраничное приложение](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Приложение iOS](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [Справочные материалы](http://cocoadocs.org/docsets/ADALiOS)|
| Android |ADAL |[Центральный репозиторий](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Приложение Android](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [Javadocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Веб-приложение Java](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>Серверные библиотеки 

| Платформа | Библиотека | Загрузить | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN для AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Приложение MVC](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN для OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Веб-приложение](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[Github](https://github.com/AzureAD/passport-azure-ad) | [Веб-интерфейс API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN для WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Веб-приложение MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Расширения протокола удостоверения для .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Обработчик JWT для .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>Сценарии
Ниже приведены три типичных сценария, в которых для проверки подлинности может использоваться ADAL.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Проверка подлинности пользователей клиентского приложения на удаленном ресурсе
В этом сценарии у разработчика есть клиент, например приложение WPF, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. У разработчика есть подписка Azure, он знает, как вызвать нижестоящий веб-интерфейс API и какой клиент Azure AD используется этим веб-интерфейсом. В результате он может упростить проверку подлинности в Azure AD, полностью делегируя процесс проверки подлинности службе ADAL или явно обрабатывая учетные данные пользователя. ADAL упрощает проверку подлинности пользователя, получение маркера доступа и маркера обновления от Azure AD и использование маркера доступа для выполнения запросов к веб-интерфейсу API.

Пример кода, который демонстрирует этот сценарий с использованием проверки подлинности Azure AD, см. в разделе [Собственное клиентское приложение WPF для веб-интерфейса API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Проверка подлинности серверного приложения на удаленном ресурсе
В этом сценарии у разработчика есть приложение, запущенное на сервере, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. У разработчика есть подписка Azure, он знает, как вызвать нижестоящую службу и какой клиент Azure AD используется веб-интерфейсом API. В результате он может использовать ADAL для упрощения проверки подлинности в Azure AD, явно обрабатывая учетные данные приложения. ADAL упрощает получение маркера из Azure AD с помощью учетных данных клиента приложения и последующее использование этого маркера для выполнения запросов к веб-интерфейсу API. ADAL также управляет жизненным циклом маркера доступа, кэшируя его и возобновляя его действие при необходимости. Пример кода, демонстрирующий этот сценарий, см. в разделе [Консольное приложение для веб-интерфейса API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Проверка подлинности серверного приложения от имени пользователя для доступа к удаленному ресурсу
В этом сценарии у разработчика есть приложение, запущенное на сервере, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. Запрос должен выполняться от имени пользователя в Azure AD. У разработчика есть подписка Azure, он знает, как вызвать нижестоящий веб-интерфейс API и какой клиент Azure AD используется службой. После проверки подлинности пользователя для веб-приложения оно может получить код проверки подлинности пользователя из Azure AD. Затем веб-приложение может использовать ADAL для получения маркера доступа и обновления маркера от имени пользователя с помощью кода проверки подлинности и учетных данных клиента, связанных с приложением в Azure AD. После получения маркера доступа приложение может обращаться к веб-интерфейсу API до истечения срока действия маркера. После истечения срока действия маркера веб-приложение может использовать ADAL для получения нового маркера доступа с помощью обновления маркера, полученного ранее.

## <a name="see-also"></a>См. также
[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

[Сценарии проверки подлинности в Azure Active Directory](active-directory-authentication-scenarios.md)

[Примеры кода Azure Active Directory](active-directory-code-samples.md)



<!--HONumber=Jan17_HO3-->


