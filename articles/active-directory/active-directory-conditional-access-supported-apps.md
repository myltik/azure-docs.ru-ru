
---
title: Условный доступ. Поддерживаемые приложения | Microsoft Docs
description: Используя управление условным доступом при проверке подлинности пользователя и перед предоставлением ему доступа к приложению Azure Active Directory проверяет определенные условия, которые вы можете выбрать. Если эти условия выполняются, пользователь проходит проверку подлинности, и ему дается доступ к приложению.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: markvi

---
# <a name="conditional-access-support-for-applications"></a>Поддержка условного доступа для приложений
Правила условного доступа поддерживаются в приложениях, подключенных к Azure Active Directory, предварительно интегрированных федеративных приложениях SaaS, приложениях, в которых используется единый вход с паролем, и в бизнес-приложениях, а также в прокси приложений Azure AD. Подробный список приложений с поддержкой условного доступа см. в разделе [Службы, включаемые с условным доступом](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). Условный доступ поддерживается в мобильных и классических приложениях, в которых используется современная проверка подлинности. В этой статье описаны компоненты, поддерживаемые в мобильных и настольных версиях таких приложений.

Приложения с современной проверкой подлинности могут отображать страницы входа Azure AD. Это позволяет предлагать пользователю выполнить многофакторную проверку подлинности внутри приложения или отображать сообщение, предназначенное конечному пользователю, когда доступ заблокирован. Современная аутентификация также нужна для аутентификации устройства с помощью Azure AD. Поэтому вычисляются политики условного доступа на основе устройств.

Важно понимать, какие приложения поддерживаются, а также действия, которые могут потребоваться для защиты других точек входа.

## <a name="applications-using-modern-authentication"></a>Приложения, использующие современную проверку подлинности
Следующие приложения поддерживают условный доступ при получении доступа к Office 365 и другим приложениям служб, подключенных к Azure AD:

| Целевая служба | Платформа | Приложение |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |Приложения "Почта", "Календарь" и "Люди", Outlook 2016, Outlook 2013 (с современной аутентификацией), Skype для бизнеса (с современной аутентификацией) |
| Office 365 Exchange Online |Windows 7, Windows 8.1 |Outlook 2016, Outlook 2013 (с современной аутентификацией), Skype для бизнеса (с современной аутентификацией) |
| Office 365 Exchange Online |iOS, Android |Приложение Outlook Mobile |
| Office 365 Exchange Online |Mac OSX |Outlook 2016 для MFA (только по расположению), поддержка политик на основе устройств ожидается в будущем, поддержка Skype для бизнеса ожидается в будущем |
| Office 365 SharePoint Online |Windows 10 |Приложения Office 2016, универсальные приложения Office, Office 2013 (с современной аутентификацией), поддержка приложения OneDrive для бизнеса (NGSC или клиент синхронизации нового поколения) ожидается в будущем, поддержка групп Office ожидается в будущем, поддержка приложений SharePoint ожидается в будущем |
| Office 365 SharePoint Online |Windows 7, Windows 8.1 |Приложения Office 2016, Office 2013 (с современной аутентификацией), приложение OneDrive для бизнеса (клиент синхронизации Groove) |
| Office 365 SharePoint Online |iOS, Android |Мобильные приложения Office |
| Office 365 SharePoint Online |Mac OSX |Приложения Office 2016 для MFA (только по расположению), поддержка политик на основе устройств ожидается в будущем |
| Office 365 Yammer |Windows 10, iOS и Android |Приложение Office Yammer |
| Dynamics CRM |Windows 10, 7, 8.1, iOS и Android |Приложение Dynamics CRM |
| Служба PowerBI |Windows 10, 7, 8.1, iOS и Android |Приложение PowerBI |
| Удаленная служба приложений Azure |Windows 10, 7, 8.1, iOS и Android, Mac OSX |Azure RemoteApp |
| Все службы приложения "Мои приложения" |Android и iOS |Все службы приложения "Мои приложения" |

## <a name="applications-that-do-not-use-modern-authentication"></a>Приложения, не использующие современную проверку подлинности
В настоящее время доступ к приложениям, не использующим современную проверку подлинности, должен блокироваться с помощью других методов, потому что условный доступ для них не является обязательным. Это в первую очередь касается доступа к Exchange и SharePoint, так как предыдущие версии приложений были созданы с использованием более старых протоколов.

## <a name="office-365-sharepoint-online"></a>Office 365 SharePoint Online
Устаревшие протоколы можно отключить в SharePoint, используя командлет Set-SPOTenant. Этот командлет не позволит клиентам Office, использующим устаревшие протоколы проверки подлинности, получать доступ к ресурсам SharePoint Online. 

**Пример команды**:     `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

## <a name="office-365-exchange-online"></a>Office 365 Exchange Online
В Exchange существует две основные категории протокола. Проанализируйте и выберите правильную политику для своей организации.

1. Exchange ActiveSync. По умолчанию политика условного доступа для многофакторной проверки подлинности и расположения не является обязательной для Exchange ActiveSync. Это позволяет обеспечить защиту доступа либо непосредственно настройкой политики Exchange ActiveSync, либо блокируя Exchange ActiveSync с помощью правил AD FS.
2. Устаревшие протоколы. Устаревшие протоколы можно заблокировать в AD FS. Таким образом будет заблокирован доступ для старых клиентов Office, таких как Office 2013, не поддерживающих современную проверку подлинности, и более ранних версий.

### <a name="example-ad-fs-rules"></a>Пример правил AD FS
Для блокирования доступа устаревших протоколов в AD FS можно использовать следующие правила. Существует две распространенные конфигурации.

### <a name="option-1:-allow-exchange-activesync-and-only-allow-legacy-apps-on-the-intranet"></a>Вариант 1: разрешить Exchange ActiveSync и разрешить устаревшие приложения только в интрасети.
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

### <a name="option-2:-allow-exchange-activesync-and-block-legacy-apps"></a>Вариант 2: разрешить Exchange ActiveSync и блокировать устаревшие приложения.
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











<!--HONumber=Oct16_HO2-->


