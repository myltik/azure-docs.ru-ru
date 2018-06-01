---
title: Настройка гибридных устройств, присоединенных к Azure Active Directory | Документация Майкрософт
description: Сведения о настройке гибридных устройств, присоединенных к Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a74a16fa583ac3bc7ea2250f916e855a0bd9d1c1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258318"
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Настройка гибридных устройств, присоединенных к Azure Active Directory

Благодаря управлению устройствами в Azure Active Directory (Azure AD) ваши пользователи получают доступ к ресурсам с устройств, которые соответствуют стандартам безопасности и нормативным требованиям. Дополнительные сведения см. в статье [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md).

Если вы хотите присоединить входящие в состав домена устройства к Azure AD в имеющейся локальной среде Active Directory, можно настроить гибридные устройства, присоединенные к Azure AD. В этой статье вы найдете описание соответствующих действий. 


## <a name="before-you-begin"></a>Перед началом работы

Прежде чем настраивать в среде гибридные устройства, присоединенные к Azure AD, ознакомьтесь с ограничениями и поддерживаемыми сценариями.  

При использовании [средства Sysprep](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc721940(v=ws.10)) убедитесь, что создаются образы на основе экземпляра Windows, который еще не зарегистрирован в Azure AD.

Как только будут выполнены описанные выше этапы настройки, для всех присоединенных к домену устройств под управлением юбилейного обновления Windows 10 и Windows Server 2016 при перезагрузке устройства и при входе пользователя выполняется автоматическая регистрация в Azure AD. **Если автоматическая регистрация нежелательна или требуется контролировать выпуск**, сначала выполните инструкции в разделе "Шаг 4. Контроль развертывания". Это позволит выборочно включить или отключить автоматический выпуск, прежде чем выполнять другие действия по настройке.  

Чтобы упростить описания, в этой статье используются следующие термины. 

- **Текущие устройства Windows.** Это собирательное описание всех присоединенных к домену устройств под управлением Windows 10 или Windows Server 2016.
- **Устройства Windows нижнего уровня.** Этот термин относится ко всем **поддерживаемым** устройствам Windows, присоединенным к домену, операционная система которых отлична от Windows 10 и Windows Server 2016.  

### <a name="windows-current-devices"></a>Текущие устройства Windows

- Для устройств под управлением Windows Desktop поддерживается юбилейное обновление Windows 10 (версия 1607) или более поздняя версия. 
- Регистрация текущих устройств Windows **поддерживается** в средах, не являющихся федеративными, например в конфигурациях с синхронизацией хэша паролей.  


### <a name="windows-down-level-devices"></a>Устройства Windows нижнего уровня

- Поддерживаются устройства Windows нижнего уровня со следующими системами:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- Регистрация устройств Windows нижнего уровня **поддерживается** в средах, не являющихся федеративными, с помощью простого единого входа. [Простой единый вход Azure Active Directory](https://aka.ms/hybrid/sso).
- Регистрация устройств Windows нижнего уровня **не поддерживается** на устройствах с перемещаемыми профилями. Если вам требуются перемещаемые профили или параметры, используйте только Windows 10.



## <a name="prerequisites"></a>предварительным требованиям

Прежде чем настраивать в организации гибридные устройства, присоединенные к Azure AD, обязательно убедитесь в следующем:

- Вы работаете с актуальной версией Azure AD Connect.

- В Azure AD Connect синхронизированы объекты-компьютеры гибридных устройств, которые нужно присоединить к Azure AD. Если объекты-компьютеры принадлежат конкретным подразделениям (OU), эти подразделения также нужно настроить для синхронизации в Azure AD Connect.

  

Azure AD Connect выполняет следующие функции:

- Отслеживает связи между учетными записями на компьютерах в локальной среде Active Directory (AD) и объектами устройств в Azure AD. 
- Поддерживает другие функции, связанные с устройствами, например Windows Hello для бизнеса.

Убедитесь, что следующие URL-адреса доступны с компьютеров в сети организации для регистрации компьютеров в Azure AD:

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com

- https://device.login.microsoftonline.com

- служба токенов безопасности вашей организации (федеративные домены).

Если еще не сделано, служба токенов безопасности вашей организации (для федеративных доменов) должна быть включена в пользовательских параметрах локальной интрасети.

Если в организации планируется использовать простой единый вход, следующие URL-адреса должны быть доступны с компьютеров в вашей организации, и они также должны быть добавлены в зону локальной интрасети пользователя:

- https://autologon.microsoftazuread-sso.com

- https://aadg.windows.net.nsatc.net

- Кроме того, в зоне интрасети пользователя должен быть включен следующий параметр: "Разрешить обновление строки состояния в сценарии".

Если ваша организация использует управляемую (нефедеративную) конфигурацию с локальной службой AD и не использует ADFS для образования федерации с Azure AD, то соединение посредством гибридной среды Azure AD в Windows 10 зависит от синхронизации объектов-компьютеров в AD с Azure AD. Убедитесь в том, что для всех подразделений с компьютерами-объектами, которые должны быть соединены посредством гибридной среды Azure AD, обеспечивается синхронизация в конфигурации синхронизации Azure AD Connect.

Если вашей организации требуется доступ к Интернету через исходящий прокси-сервер, необходимо реализовать автоматическое обнаружение веб-прокси (WPAD), чтобы компьютеры Windows 10 можно было зарегистрировать в Azure AD.

## <a name="configuration-steps"></a>Этапы настройки

Гибридные устройства, присоединенные к Azure AD, можно настроить для различных типов платформ устройств Windows. В этом разделе описаны обязательные действия для всех типичных сценариев настройки.  

Следующая таблица содержит обзор действий, необходимых для вашего сценария.  



| Действия                                      | Текущие устройства Windows и синхронизация хэша паролей | Текущие устройства Windows и федерация | Устройства Windows нижнего уровня |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Шаг 1. Настройка точки подключения службы | ![Проверка][1]                            | ![Проверка][1]                    | ![Проверка][1]        |
| Шаг 2. Настройка выдачи утверждений           |                                        | ![Проверка][1]                    | ![Проверка][1]        |
| Шаг 3. Включение поддержки устройств с ОС, отличными от Windows 10      |                                        |                                | ![Проверка][1]        |
| Шаг 4. Контроль развертывания     | ![Проверка][1]                            | ![Проверка][1]                    | ![Проверка][1]        |
| Шаг 5. Проверка присоединенных устройств          | ![Проверка][1]                            | ![Проверка][1]                    | ![Проверка][1]        |



## <a name="step-1-configure-service-connection-point"></a>Шаг 1. Настройка точки подключения службы

Объект точки подключения службы (SCP) используется устройствами во время регистрации для получения сведений о клиенте Azure AD. В локальной среде Active Directory (AD) должен существовать объект SCP для гибридных устройств, присоединенных к Azure AD, определенный в разделе контекста именования конфигурации в лесу компьютеров. В каждом лесу существует только один контекст именования конфигурации. В конфигурации Active Directory с несколькими лесами точка подключения службы должна существовать во всех лесах, содержащих присоединенные к домену компьютеры.

Для получения контекста именования конфигурации для леса можно использовать командлет [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx).  

Для леса, в котором домен Active Directory имеет имя *example.com*, контекст именования конфигурации будет таким:

`CN=Configuration,DC=fabrikam,DC=com`

В лесу объект SCP для автоматической регистрации присоединенных к домену устройств имеет следующее расположение:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

В зависимости от того, как выполнялось развертывание Azure AD Connect, объект SCP уже может быть настроен.
Чтобы проверить существование объекта и получить значения для обнаружения, используйте следующий сценарий Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

В выходных данных **$scp.Keywords** указаны сведения о клиенте Azure AD, такие как показаны ниже:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Если точка подключения службы не существует, создайте ее, выполнив командлет `Initialize-ADSyncDomainJoinedComputerSync` на сервере Azure AD Connect. Для выполнения этого командлета необходимы учетные данные администратора организации.  
Этот командлет:

- Создает точку подключения службы в лесу Active Directory, к которому подключена служба Azure AD Connect. 
- Требует указать обязательный параметр `AdConnectorAccount`, который обозначает учетную запись, настроенную в Azure AD Connect в качестве учетной записи соединителя Active Directory. 


Следующий скрипт демонстрирует использование этого командлета. В этом скрипте есть параметр `$aadAdminCred = Get-Credential`, который требует ввести имя пользователя. Имя пользователя следует вводить в формате имени участника-пользователя (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

Командлет `Initialize-ADSyncDomainJoinedComputerSync`:

- В этом командлете используется модуль PowerShell и средства AD DS, которые используют веб-службы Active Directory в контроллере домена. Поддержку веб-служб Active Directory выполняют контроллеры домена под управлением Windows Server 2008 R2 и более поздних версий.
- Он поддерживается только **модулем MSOnline PowerShell версии 1.1.166.0**. Чтобы скачать этот модуль, используйте эту [ссылку](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/).   
- Если не установить средства AD DS, выполнение командлета `Initialize-ADSyncDomainJoinedComputerSync` завершится ошибкой.  Средства AD DS можно установить с помощью диспетчера сервера в разделе "Компоненты" — "Средства удаленного администрирования сервера" — "Средства администрирования ролей".

Если вы используете контроллер домена под управлением Windows Server 2008 или более ранних версий, для создания точки подключения службы используйте приведенный ниже скрипт.

В конфигурации с несколькими лесами следует использовать следующий сценарий, чтобы создать точку подключения службы в каждом лесу, где существуют компьютеры.
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

В приведенном выше сценарии

- `$verifiedDomain = "contoso.com"` — заполнитель, который необходимо заменить одним из проверенных доменных имен в Azure AD. Необходимо будет стать владельцем этого домена, прежде чем использовать его.

Дополнительные сведения о проверенных доменных именах см. в статье [Добавление имени личного домена в Azure Active Directory](active-directory-domains-add-azure-portal.md).  
Чтобы получить список проверенных доменов компании, можно использовать командлет [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0). 

![Get-AzureADDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

## <a name="step-2-setup-issuance-of-claims"></a>Шаг 2. Настройка выдачи утверждений

В федеративной конфигурации Azure AD устройства проходят проверку подлинности в Azure AD, используя службы федерации Active Directory или локальную службу федерации стороннего поставщика. При проверке подлинности устройства получают маркер доступа для регистрации в службе регистрации устройств Azure Active Directory (Azure DRS).

Текущие устройства Windows с помощью встроенной проверки подлинности Windows проходят проверку подлинности в активной конечной точке WS-Trust (версии 1.3 или 2005), размещенной в локальной службе федерации.

> [!NOTE]
> При использовании AD необходимо включить **adfs/services/trust/13/windowstransport** или **adfs/services/trust/2005/windowstransport**. При использовании прокси-службы проверки подлинности через Интернет также убедитесь, что эта конечная точка публикуется через прокси-службу. В разделе **Служба > Конечные точки** вы можете увидеть, какие конечные точки активированы в консоли управления AD FS.
>
>Если вы не используете AD FS в качестве локальной службы федерации, обратитесь к своему поставщику за инструкциями и убедитесь, что система поддерживает конечные точки WS-Trust 1.3 или 2005 и что эти конечные точки публикуются с использованием файла обмена метаданными (MEX-файла).

Чтобы регистрация устройства прошла успешно, в полученном с помощью DRS Azure маркере должны существовать следующие утверждения. Azure DRS создаст в Azure AD объект устройства, который содержит часть этой информации. Azure AD Connect на ее основе сопоставит новый объект устройства с локальной учетной записью компьютера.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Если у вас есть несколько подтвержденных доменных имен, для компьютеров нужно предоставить следующее утверждение:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Если вы уже используете утверждение ImmutableID (например, в качестве альтернативного идентификатора для входа), предоставьте еще одно утверждение для компьютеров:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

В следующих разделах вы найдете следующую информацию:
 
- какие значения должны входить в каждое утверждение;
- как выглядит определение в AD FS.

Определение помогает проверить, существуют ли нужные значения, или их необходимо создать.

> [!NOTE]
> Если вы не используете AD FS в качестве локального сервера федерации, следуйте инструкциям поставщика, чтобы создать соответствующую конфигурацию для выдачи этих утверждений.

### <a name="issue-account-type-claim"></a>Выдача утверждений для типа учетной записи

**`http://schemas.microsoft.com/ws/2012/01/accounttype`**. Это утверждение должно содержать значение **DJ**, которое идентифицирует устройство как присоединенный к домену компьютер. В AD FS вы можете добавить правила преобразования выдачи, например такие:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Выдача идентификатора objectGUID для локальной учетной записи компьютера

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`**. Это утверждение должно содержать значение **objectGUID** для учетной записи локального компьютера. В AD FS вы можете добавить правила преобразования выдачи, например такие:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Выдача идентификатора objectSID для локальной учетной записи компьютера

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`**. Это утверждение должно содержать значение **objectSID** для учетной записи локального компьютера. В AD FS вы можете добавить правила преобразования выдачи, например такие:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Выдача issuerID для компьютера при наличии нескольких проверенных доменных имен в Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`**. Это утверждение должно содержать универсальный идентификатор ресурса (URI) для любого проверенного доменного имени, которое подключается с использованием локальной службы федерации (AD FS или стороннего поставщика), выдающей маркер. В AD FS вы можете добавить правила преобразования выдачи, похожие на представленные ниже, именно в таком порядке после указанных выше правил. Обратите внимание, что для явной выдачи правил пользователям необходимо одно правило. В указанных ниже правилах добавлено первое правило, различающее пользователей и компьютеры при проверке подлинности.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


В приведенном выше утверждении

- `<verified-domain-name>` — заполнитель, который необходимо заменить одним из проверенных доменных имен в Azure AD. Например, Value = "http://contoso.com/adfs/services/trust/"



Дополнительные сведения о проверенных доменных именах см. в статье [Добавление имени личного домена в Azure Active Directory](active-directory-domains-add-azure-portal.md).  

Чтобы получить список проверенных доменов компании, можно использовать командлет [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Выдача ImmutableID для компьютера, если он уже существует для пользователей (например, в качестве альтернативного имени для входа)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`**. Это утверждение должно содержать допустимое значение для компьютеров. В AD FS можно создать такие правила преобразования выдачи:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Вспомогательный скрипт для создания правила преобразования выдачи AD FS

Следующий скрипт поможет вам создавать описанные выше правила преобразования выдачи.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Примечания 

- Этот скрипт добавляет правила к существующим правилам. Не выполняйте этот скрипт дважды, иначе набор правил будет также добавлен дважды. Перед повторным запуском скрипта убедитесь, что для этих утверждений не существует установленных правил (при соответствующих условиях).

- Если у вас есть несколько проверенных доменных имен (это можно проверить на портале Azure AD или с помощью командлета Get-MsolDomains), в скрипте для переменной **$multipleVerifiedDomainNames** задайте значение **$true**. Также не забудьте удалить все существующие утверждения issuerid, которые могли создаваться в Azure AD Connect или другими средствами. Вот пример для этого правила:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Если утверждение **ImmutableID** для учетных записей пользователей уже выдано, задайте для **$immutableIDAlreadyIssuedforUsers** в сценарии значение **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Шаг 3. Устройства Windows нижнего уровня

Если некоторые из присоединенных к домену устройств являются устройствами Windows нижнего уровня, выполните следующие действия:

- Настройте в Azure AD политику, разрешающую пользователям регистрировать устройства.
 
- Настройте локальную службу федерации, чтобы она выдавала утверждения **встроенной проверки подлинности Windows (IWA)**, необходимые для регистрации устройств.
 
- В локальные зоны интрасети добавьте конечную точку проверки подлинности устройств в Azure AD, чтобы избежать запросов сертификатов при проверке подлинности устройств.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Настройка политики регистрации устройств в Azure AD

Чтобы поддерживать регистрацию устройств Windows нижнего уровня, необходимо установить параметр, разрешающий пользователям регистрировать устройства в Azure AD. Эти настройки можно найти на портале Azure:

`Azure Active Directory > Users and groups > Device settings`
    
Следующая политика должна иметь значение **Все**: **Пользователи могут регистрировать устройства в Azure AD**.

![Регистрация устройств](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Настройка локальной службы федерации 

Локальная служба федерации должна поддерживать выдачу утверждений **authenticationmehod** и **wiaormultiauthn** при получении запросов на аутентификацию к проверяющей стороне Azure AD, содержащих параметр resouce_params с закодированным значением, как показано ниже.

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

При поступлении такого запроса локальная служба федерации должна выполнить проверку подлинности пользователя, используя встроенную проверку подлинности Windows. При успешной проверке подлинности она должна выдать следующие два утверждения:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

В AD FS следует добавить правило преобразования выдачи, пропускающее метод проверки подлинности.  

**Чтобы добавить это правило, сделайте следующее:**

1. В консоли управления AD FS откройте `AD FS > Trust Relationships > Relying Party Trusts`.
2. Щелкните правой кнопкой мыши объект отношений доверия с проверяющей стороной платформы удостоверений Microsoft Office 365 и выберите **Edit Claim Rules** (Изменить правила для утверждений).
3. На вкладке **Правила преобразования выдачи** выберите **Добавить правило**.
4. Из списка шаблонов **Claim rule** (Правило для утверждений) выберите **Отправка утверждений с помощью настраиваемого правила**.
5. Щелкните **Далее**.
6. В поле **Claim rule name** (Имя правила для утверждений метода проверки подлинности) введите **Auth Method Claim Rule** (Правило для утверждений метода проверки подлинности).
7. В поле **Claim rule** (Правило утверждения) введите такое правило:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. На сервере федерации введите указанную ниже команду PowerShell, заменив в ней **\<RPObjectName\>** именем объекта проверяющей стороны, которое соответствует объекту доверия проверяющей стороны Azure AD. Как правило, этот объект называется **платформой удостоверений Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Добавление конечной точки для проверки подлинности устройств Azure AD в локальные зоны интрасети

Чтобы избежать запросов сертификатов при проверке подлинности в Azure AD пользователей с зарегистрированных устройств, можно отправить политику на присоединенные к домену устройства, добавив следующий URL-адрес в зону локальной интрасети в Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Шаг 4. Контроль развертывания

Когда вы выполните эти обязательные шаги, все будет готово для автоматического присоединения к Azure AD устройств, присоединенных к домену.

- Все присоединенные к домену устройства под управлением юбилейного обновления Windows 10 и Windows Server 2016 будут выполнять автоматическую регистрацию в Azure AD при перезагрузке устройства и при входе пользователя. 

- Новые устройства зарегистрируются в Azure AD, когда завершится перезагрузка после присоединения к домену.

- Устройства, которые были ранее зарегистрированы в Azure AD (например, для Intune), перейдут в состояние *Присоединено к домену и Зарегистрировано в AAD*. Но из-за обычного потока действий домена и пользователя, чтобы завершить этот процесс на всех устройствах понадобится некоторое время.

### <a name="remarks"></a>Примечания

- Для управления развертыванием автоматической регистрации присоединенных к домену компьютеров Windows 10 и Windows Server 2016 можно использовать объект групповой политики. **Если вы не хотите, чтобы эти устройства автоматически регистрировались в Azure AD, или вы хотите управлять регистрацией**, то необходимо развернуть групповую политику, отключающую автоматическую регистрацию, для всех таких устройств, прежде чем начинать настройку. После завершения настройки, когда все будет готово к тестированию, необходимо будет развернуть групповую политику, включающую автоматическую регистрацию, только для тестовых устройств, а также для других устройств на ваш выбор.

- Чтобы развернуть компьютеры Windows нижнего уровня, вы можете развернуть на выбранных компьютерах [пакет установщика Windows](#windows-installer-packages-for-non-windows-10-computers).

- Когда вы отправляете объект групповой политики на присоединенные к домену устройства Windows 8.1, предпринимается попытка присоединения. Но мы рекомендуем использовать для присоединения всех устройств Windows нижнего уровня [пакет установщика Windows](#windows-installer-packages-for-non-windows-10-computers). 

### <a name="create-a-group-policy-object"></a>Создание объекта групповой политики 

Чтобы управлять развертыванием текущих компьютеров Windows, следует развернуть на компьютерах, которые нужно зарегистрировать, объект групповой политики **Зарегистрировать подключенные к домену компьютеры в качестве устройств**. Например, можно развернуть политику в подразделении или группе безопасности.

**Чтобы задать политику, сделайте следующее:**

1. Откройте **диспетчер сервера** и перейдите к разделу `Tools > Group Policy Management`.
2. Перейдите к узлу домена, на котором нужно активировать автоматическую регистрацию текущих компьютеров Windows.
3. Щелкните правой кнопкой мыши **Объекты групповой политики**, а затем выберите **Создать**.
4. Введите имя объекта групповой политики. Например, *Гибридное присоединение к Azure AD. 
5. Последовательно выберите **ОК**.
6. Щелкните новый объект групповой политики правой кнопкой мыши, а затем выберите **Изменить**.
7. Выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Регистрация устройств**. 
8. Щелкните правой кнопкой мыши пункт **Зарегистрировать подключенные к домену компьютеры в качестве устройств** и выберите **Изменить**.
   
   > [!NOTE]
   > В более ранних версиях консоли управления групповыми политиками этот шаблон групповой политики назывался по-другому. Если вы используете более раннюю версию консоли, перейдите к `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Выберите параметр **Включено**, а затем — **Применить**. Если нужно, чтобы групповая политика блокировала автоматическую регистрацию управляемых устройств в Azure AD, выберите **Отключено**.

8. Последовательно выберите **ОК**.
9. Свяжите объект групповой политики с выбранным расположением. Например, его можно связать с определенным подразделением. Его также можно связать с определенной группой безопасности компьютеров, которые присоединяются к Azure AD автоматически. Чтобы задать эту политику для всех присоединенных к домену компьютеров Windows 10 и Windows Server 2016 в организации, свяжите объект групповой политики с доменом.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Пакеты установщика Windows для компьютеров, не работающих на базе Windows 10

Чтобы присоединить в федеративной среде присоединенные к домену компьютеры Windows нижнего уровня, скачайте из Центра загрузки Майкрософт и установите пакет установщика Windows (MSI-файл), опубликованный на странице [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/en-us/download/details.aspx?id=53554) (Microsoft Workplace Join для компьютеров под управлением ОС, отличной от Windows 10).

Для развертывания пакета используйте систему распространения программного обеспечения, например System Center Configuration Manager. Пакет поддерживает параметры стандартной автоматической установки с использованием параметра *quiet*. В System Center Configuration Manager доступны дополнительные преимущества предыдущих версий, такие как возможность отслеживать ход выполнения регистрации. Дополнительные сведения см. на странице [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Установщик создает в системе запланированную задачу, которая выполняется в контексте пользователя. Задача запускается в момент входа пользователя в систему Windows. Эта задача автоматически присоединяет устройство к Azure AD, используя учетные данные пользователя, после проверки подлинности с использованием встроенной проверки подлинности Windows. Чтобы просмотреть запланированные задачи, выберите на устройстве **Microsoft** > **Присоединение к рабочей области** и перейдите к библиотеке планировщика задач.

## <a name="step-5-verify-joined-devices"></a>Шаг 5. Проверка присоединенных устройств

Список успешно присоединенных корпоративных устройств вашей организации вы можете получить с помощью командлета [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice), входящего в [модуль PowerShell для Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

В выходных данных командлета отображаются устройства, которые зарегистрированы и присоединены к Azure AD. Чтобы получить полный список устройств, используйте параметр **-All**. Устройства затем можно отфильтровать по свойству **deviceTrustType**. У присоединенных к домену устройств оно имеет значение **Domain Joined**.

## <a name="next-steps"></a>Дополнительная информация

* [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
