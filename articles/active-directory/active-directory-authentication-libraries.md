<properties
   pageTitle="Библиотеки проверки подлинности Azure Active Directory | Microsoft Azure"
   description="Библиотека проверки подлинности Azure AD (ADAL) позволяет разработчикам клиентских приложений легко проверять подлинность пользователей в облачной или локальной Active Directory (AD) и получать маркеры доступа для защиты вызовов API."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# Библиотеки проверки подлинности Azure Active Directory

Библиотека проверки подлинности Azure AD (ADAL) позволяет разработчикам клиентских приложений легко проверять подлинность пользователей в облачной или локальной Active Directory (AD) и получать маркеры доступа для защиты вызовов API. ADAL имеет множество функций, которые упрощают проверку подлинности для разработчиков. К ним относятся поддержка асинхронных операций, настраиваемый кэш маркеров, в котором хранятся маркеры доступа и маркеры обновления, автоматическое обновление маркера, когда срок действия маркера доступа истекает и маркер обновления доступен, и многие другие функции. ADAL берет на себя большую часть сложных операций и позволяет разработчику сосредоточиться на бизнес-логике своего приложения и легко защитить ресурсы, не будучи экспертом в области безопасности.

ADAL доступна на различных платформах.

|Платформа|Имя пакета|Клиентская/серверная библиотека|Загрузить|Исходный код|Документация и примеры|
|---|---|---|---|---|---|
|Клиент .NET, магазин Windows, Windows Phone (8.1)|Библиотека проверки подлинности Active Directory (ADAL) для .NET|Клиент|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL для .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[Документация](https://msdn.microsoft.com/library/azure/mt417579.aspx)|
|JavaScript|Библиотека проверки подлинности Active Directory (ADAL) для JavaScript|Клиент|[ADAL для JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL для JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|Пример: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Библиотека проверки подлинности Active Directory (ADAL) для Objective-C|Клиент|[ADAL для Objective-C (CocoaPods)](https://cocoapods.org/?q=adal%20io)|[ADAL для Objective-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|Пример: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Библиотека проверки подлинности Active Directory (ADAL) для Android|Клиент|[ADAL для Android (центральный репозиторий)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL для Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|Пример: [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Библиотека проверки подлинности Active Directory (ADAL) для Node.js|Клиент|[ADAL для Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL для Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|Пример: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Промежуточный слой проверки подлинности Microsoft Azure Active Directory Passport для Node.js|Клиент|[Azure Active Directory Passport для Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory для Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Библиотека проверки подлинности Active Directory (ADAL) для Java|Клиент|[ADAL для Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL для Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Расширения протокола удостоверения для платформы Microsoft .NET Framework 4.5|сервер;|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[расширения модели удостоверения Azure AD для .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Обработчик веб-маркеров JSON для платформы Microsoft .NET Framework 4.5|сервер;|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[расширения модели удостоверения Azure AD для .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Промежуточный слой OWIN, который позволяет приложению использовать технологии корпорации Майкрософт для проверки подлинности.|сервер;|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|Промежуточный слой OWIN, который позволяет приложению использовать OpenIDConnect для проверки подлинности.|сервер;|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Пример: [WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|Промежуточный слой OWIN, который позволяет приложению использовать WS-Federation для проверки подлинности.|сервер;|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|Пример: [WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## Сценарии

Ниже приведены три типичных сценария, в которых для проверки подлинности может использоваться ADAL.

### Проверка подлинности пользователей клиентского приложения на удаленном ресурсе

В этом сценарии у разработчика есть клиент, например приложение WPF, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. У разработчика есть подписка Azure, он знает, как вызвать нижестоящий веб-интерфейс API и какой клиент Azure AD используется этим веб-интерфейсом. В результате он может упростить проверку подлинности в Azure AD, полностью делегируя процесс проверки подлинности службе ADAL или явно обрабатывая учетные данные пользователя. ADAL упрощает проверку подлинности пользователя, получение маркера доступа и маркера обновления от Azure AD и использование маркера доступа для выполнения запросов к веб-интерфейсу API.

Пример кода, который демонстрирует этот сценарий с использованием проверки подлинности Azure AD, см. в разделе [Собственное клиентское приложение WPF для веб-интерфейса API](https://github.com/azureadsamples/nativeclient-dotnet).

### Проверка подлинности серверного приложения на удаленном ресурсе

В этом сценарии у разработчика есть приложение, запущенное на сервере, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. У разработчика есть подписка Azure, он знает, как вызвать нижестоящую службу и какой клиент Azure AD используется веб-интерфейсом API. В результате он может использовать ADAL для упрощения проверки подлинности в Azure AD, явно обрабатывая учетные данные приложения. ADAL упрощает получение маркера из Azure AD с помощью учетных данных клиента приложения и последующее использование этого маркера для выполнения запросов к веб-интерфейсу API. ADAL также управляет жизненным циклом маркера доступа, кэшируя его и возобновляя его действие при необходимости. Пример кода, демонстрирующий этот сценарий, см. в разделе [Консольное приложение для веб-интерфейса API](https://github.com/AzureADSamples/Daemon-DotNet).

### Проверка подлинности серверного приложения от имени пользователя для доступа к удаленному ресурсу

В этом сценарии у разработчика есть приложение, запущенное на сервере, которому требуется доступ к удаленному ресурсу, защищенному Azure AD, например к веб-интерфейсу API. Запрос должен выполняться от имени пользователя в Azure AD. У разработчика есть подписка Azure, он знает, как вызвать нижестоящий веб-интерфейс API и какой клиент Azure AD используется службой. После проверки подлинности пользователя для веб-приложения оно может получить код проверки подлинности пользователя из Azure AD. Затем веб-приложение может использовать ADAL для получения маркера доступа и обновления маркера от имени пользователя с помощью кода проверки подлинности и учетных данных клиента, связанных с приложением в Azure AD. После получения маркера доступа приложение может обращаться к веб-интерфейсу API до истечения срока действия маркера. После истечения срока действия маркера веб-приложение может использовать ADAL для получения нового маркера доступа с помощью обновления маркера, полученного ранее.


## См. также

[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

[Сценарии проверки подлинности в Azure Active Directory](active-directory-authentication-scenarios.md)

[Примеры кода Azure Active Directory](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->