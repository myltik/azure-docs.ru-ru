
<properties
	pageTitle="Условный доступ. Поддерживаемые приложения | Microsoft Azure"
	description="Используя управление условным доступом при проверке подлинности пользователя и перед предоставлением ему доступа к приложению Azure Active Directory проверяет определенные условия, которые вы можете выбрать. Если эти условия выполняются, пользователь проходит проверку подлинности, и ему дается доступ к приложению."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/23/2016"
	ms.author="femila"/>


# Поддержка условного доступа для приложений

Правила условного доступа поддерживаются в приложениях, подключенных к Azure AD, предварительно интегрированных федеративных приложениях SaaS, приложениях, в которых используется единый вход с паролем, в линейке бизнес-приложений, а также в прокси приложений Azure AD. Подробный список приложений с поддержкой условного доступа см. в разделе [Службы, включаемые с условным доступом](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Условный доступ поддерживается в мобильных и классических приложениях, в которых используется современная проверка подлинности. В этом разделе описаны компоненты, поддерживаемые в мобильных и настольных версиях таких приложений.

 Приложения с современной проверкой подлинности могут отображать страницы входа Azure AD. Это позволяет предлагать пользователю выполнить многофакторную проверку подлинности внутри приложения или отображать сообщение, предназначенное конечному пользователю, когда доступ заблокирован. Важно понимать, какие приложения поддерживаются, а также действия, которые могут потребоваться для защиты других точек входа.

## Приложения, использующие современную проверку подлинности
Следующие приложения были проверены с помощью многофакторной проверки подлинности (MFA) и политики расположения, заданной для целевой службы.

| Приложение | Целевая служба | платформа |
|--------------|-----------------|----------------------------------------------------------------|
| Outlook 2016 | Exchange | Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
| Outlook 2013 (требуется включение современной проверки подлинности)| Exchange |Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Skype для бизнеса (с современной проверкой подлинности)|Exchange (Exchange используется для доступа к календарю и журналу бесед)| Windows 10, Windows 8.1, Windows 7 |
|Приложение Outlook Mobile|Exchange| iOS и Android |
|Office 2016; Word, Excel, Sharepoint|SharePoint| Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
|Office 2013 (требуется включение современной проверки подлинности)|SharePoint|Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Приложение Dynamics CRM|Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Приложение Yammer|Yammer| Windows Mobile 10, iOS, Android|
|Удаленное приложение Azure|Удаленная служба приложений Azure|Windows 10, Windows 8.1, Windows 7, Mac, iOS, Android|

## Приложения, не использующие современную проверку подлинности

В настоящее время доступ к приложениям, не использующим современную проверку подлинности, должен блокироваться с помощью других методов, потому что условный доступ для них не является обязательным. Это в первую очередь касается доступа к Exchange и SharePoint, так как предыдущие версии приложений были созданы с использованием более старых протоколов.

## SharePoint
Устаревшие протоколы можно отключить в SharePoint, используя командлет Set-SPOTenant. Этот командлет не позволит клиентам Office, использующим устаревшие протоколы проверки подлинности, получать доступ к ресурсам SharePoint Online.

**Пример команды**: `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## Exchange

В Exchange существует две основные категории протокола. Проанализируйте и выберите правильную политику для своей организации.

1. Exchange ActiveSync. По умолчанию политика условного доступа для многофакторной проверки подлинности и расположения не является обязательной для Exchange ActiveSync. Это позволяет обеспечить защиту доступа либо непосредственно настройкой политики Exchange ActiveSync, либо блокируя Exchange ActiveSync с помощью правил AD FS.
2. Устаревшие протоколы. Устаревшие протоколы можно заблокировать в AD FS. Таким образом будет заблокирован доступ для старых клиентов Office, таких как Office 2013, не поддерживающих современную проверку подлинности, и более ранних версий.


### Пример правил AD FS
Для блокирования доступа устаревших протоколов в AD FS можно использовать следующие правила. Существует две распространенные конфигурации. Вариант 1: разрешить Exchange ActiveSync и разрешить устаревшие приложения только в интрасети.

Применяя следующие три правила к отношению доверия с проверяющей стороной AD FS для платформы идентификации Microsoft Office 365, трафик Exchange ActiveSync будет разрешен, так же как и трафик в браузере и трафик современной проверки подлинности. Для устаревших приложений будет заблокирован доступ к экстрасети.

Правило 1

    `@RuleName = “Allow all intranet traffic”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

Правило 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

Правило 3

	@RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### Вариант 2: разрешить Exchange ActiveSync и блокировать устаревшие приложения. 
Применяя следующие три правила к отношению доверия с проверяющей стороной AD FS для платформы идентификации Microsoft Office 365, трафик Exchange ActiveSync будет разрешен, так же как и трафик в браузере и трафик современной проверки подлинности. Для устаревших приложений будет заблокирован доступ к любому расположению.

Правило 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Правило 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Правило 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

<!---HONumber=AcomDC_0629_2016-->