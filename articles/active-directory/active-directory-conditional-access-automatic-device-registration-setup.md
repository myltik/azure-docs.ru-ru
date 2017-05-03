---
title: "Как настроить автоматическую регистрацию присоединенных к домену устройств Windows в Azure Active Directory | Документация Майкрософт"
description: "Настройте автоматическую регистрацию устройств Windows, присоединенных к домену, без предупреждений с помощью Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 6028116d18207d13729d1816f64ad192d4cdb491
ms.lasthandoff: 04/26/2017


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Настройка автоматической регистрации присоединенных к домену устройств Windows в Azure Active Directory

Для использования [условного доступа на основе устройств в Azure Active Directory](active-directory-conditional-access-azure-portal.md) необходимо зарегистрировать компьютеры в Azure Active Directory (Azure AD). Список устройств, зарегистрированных в организации, можно получить с помощью командлета [Get MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) в [модуле PowerShell Azure Active Directory](https://docs.microsoft.com/en-us/powershell/msonline/). 

В этой статье описана процедура настройки автоматической регистрации присоединенных к домену устройств Windows вашей организации в Azure AD.


Ознакомьтесь также с информацией по смежным темам:

- Условный доступ описан в статье [Условный доступ в Azure Active Directory — предварительная версия](active-directory-conditional-access-azure-portal.md). 
- Сведения об устройствах Windows 10 в рабочей области и об улучшенной процедуре регистрации пользователей в Azure AD см. в статье [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md).


## <a name="before-you-begin"></a>Перед началом работы

Прежде чем настраивать в своей среде автоматическую регистрацию присоединенных к домену устройств Windows, ознакомьтесь с ограничениями и поддерживаемыми сценариями.  

Чтобы упростить описания, в этой статье используются следующие термины. 

- **Текущие устройства Windows.** Это собирательное описание всех присоединенных к домену устройств под управлением Windows 10 или Windows Server 2016.
- **Устройства Windows нижнего уровня.** Этот термин относится ко всем **поддерживаемым** устройствам Windows, присоединенным к домену, операционная система которых отлична от Windows 10 и Windows Server 2016.  


### <a name="windows-current-devices"></a>Текущие устройства Windows

- Для настольных устройств под управлением ОС Windows мы рекомендуем использовать юбилейное обновление Windows 10 (версия 1607) или более позднюю версию. 
- Регистрация текущих устройств Windows **поддерживается** в средах, не являющихся федеративными, например в конфигурациях с синхронизацией хэша паролей.  


### <a name="windows-down-level-devices"></a>Устройства Windows нижнего уровня

- Поддерживаются устройства Windows нижнего уровня со следующими системами:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- Регистрация устройств Windows нижнего уровня **не поддерживается** в следующих сценариях:
    - В средах, не являющихся федеративными (конфигурации с синхронизацией хэша паролей).  
    - На устройствах с перемещаемыми профилями. Если вам требуются перемещаемые профили или параметры, используйте только Windows 10.



## <a name="prerequisites"></a>Предварительные требования

Прежде чем настроить в организации автоматическую регистрацию присоединенных к домену устройств, обязательно убедитесь в том, что вы используете последнюю версию Azure AD Connect.

Azure AD Connect выполняет следующие функции:

- Отслеживает связи между учетными записями на компьютерах в локальной среде Active Directory (AD) и объектами устройств в Azure AD. 
- Поддерживает другие функции, связанные с устройствами, например Windows Hello для бизнеса.



## <a name="configuration-steps"></a>Этапы настройки

В этом разделе описаны обязательные действия для всех типичных сценариев настройки.  
Следующая таблица содержит обзор действий, необходимых для вашего сценария.  



| Действия                                      | Текущие устройства Windows и синхронизация хэша паролей | Текущие устройства Windows и федерация | Устройства Windows нижнего уровня |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Шаг 1. Настройка точки подключения службы | ![Проверка][1]                            | ![Проверка][1]                    | ![Проверка][1]        |
| Шаг 2. Настройка выдачи утверждений           |                                        | ![Проверка][1]                    | ![Проверка][1]        |
| Шаг 3. Включение поддержки устройств с ОС, отличными от Windows 10      |                                        |                                | ![Проверка][1]        |
| Шаг 4. Контроль развертывания     | ![Проверка][1]                            | ![Проверка][1]                    | ![Проверка][1]        |
| Шаг 5. Проверка зарегистрированных устройств          | ![Проверка][1]                            | ![Проверка][1]                    | ![Проверка][1]        |



## <a name="step-1-configure-service-connection-point"></a>Шаг 1. Настройка точки подключения службы

Объект точки подключения службы (SCP) используется устройствами во время регистрации для получения сведений о клиенте Azure AD. В локальной среде Active Directory (AD) должен существовать объект SCP для автоматической регистрации присоединенных к домену устройств, определенный в разделе контекста именования конфигурации в лесу компьютеров. В каждом лесу существует только один контекст именования конфигурации. В конфигурации Active Directory с несколькими лесами точка подключения службы должна существовать во всех лесах, содержащих присоединенные к домену компьютеры.

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

Если точка подключения службы не существует, создайте ее, выполнив командлет `Initialize-ADSyncDomainJoinedComputerSync` на сервере Azure AD Connect.  
Этот командлет:

- Создает точку подключения службы в лесу Active Directory, к которому подключена служба Azure AD Connect. 
- Требует указать обязательный параметр `AdConnectorAccount`, который обозначает учетную запись, настроенную в Azure AD Connect в качестве учетной записи соединителя Active Directory. 


Следующий скрипт демонстрирует использование этого командлета. В этом скрипте есть параметр `$aadAdminCred = Get-Credential`, который требует ввести имя пользователя. Имя пользователя следует вводить в формате имени участника-пользователя (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

В командлете `Initialize-ADSyncDomainJoinedComputerSync` используется модуль PowerShell для Active Directory, который использует веб-службы Active Directory в контроллере домена. Поддержку веб-служб Active Directory выполняют контроллеры домена под управлением Windows Server 2008 R2 и более поздних версий. 

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

- `$<domain>` — URL-адрес службы AD FS,
- `<verified-domain-name>` — заполнитель, который необходимо заменить одним из проверенных доменных имен в Azure AD.



Дополнительные сведения о проверенных доменных именах см. в статье [Добавление имени личного домена в Azure Active Directory](active-directory-add-domain.md).  
Чтобы получить список проверенных доменов компании, можно использовать командлет [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain). 

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

Локальная служба федерации должна поддерживать выдачу утверждений **authenticationmehod** и **wiaormultiauthn** при получении запросов на проверку подлинности к проверяющей стороне Azure AD, содержащих параметр resouce_params с закодированным значением, как показано ниже:

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

Когда вы выполните эти обязательные шаги, все будет готово для автоматической регистрации в Azure AD устройств, присоединенных к домену. Все присоединенные к домену устройства под управлением юбилейного обновления Windows 10 и Windows Server 2016 будут выполнять автоматическую регистрацию в Azure AD при перезагрузке устройства и при входе пользователя. Новые устройства зарегистрируются в Azure AD, когда завершится перезагрузка после присоединения к домену.

### <a name="remarks"></a>Примечания

- Для управления развертыванием автоматической регистрации присоединенных к домену компьютеров Windows 10 и Windows Server 2016 можно использовать объект групповой политики.

- Устройства с обновлением Windows 10 от ноября 2015 г. автоматически регистрируются в Azure AD **только** в том случае, если задан объект групповой политики развертывания.

- Чтобы развернуть автоматическую регистрацию компьютеров Windows нижнего уровня, вы можете развернуть на выбранных компьютерах [пакет установщика Windows](#windows-installer-packages-for-non-windows-10-computers).

- Когда вы отправляете объект групповой политики на присоединенные к домену устройства Windows 8.1, предпринимается попытка регистрации. Но мы рекомендуем использовать для регистрации всех устройств Windows нижнего уровня [пакет установщика Windows](#windows-installer-packages-for-non-windows-10-computers). 

### <a name="create-a-group-policy-object"></a>Создание объекта групповой политики 

Чтобы управлять развертыванием автоматической регистрации текущих компьютеров Windows, следует развернуть на компьютерах, которые нужно зарегистрировать, объект групповой политики **Зарегистрировать подключенные к домену компьютеры в качестве устройств**. Например, можно развернуть политику в подразделении или группе безопасности.

**Чтобы задать политику, сделайте следующее:**

1. Откройте **диспетчер сервера** и перейдите к разделу `Tools > Group Policy Management`.
2. Перейдите к узлу домена, на котором нужно активировать автоматическую регистрацию текущих компьютеров Windows.
3. Щелкните правой кнопкой мыши **Объекты групповой политики**, а затем выберите **Создать**.
4. Введите имя объекта групповой политики. Например, *автоматическая регистрация в Azure AD*. Нажмите кнопку **ОК**.
5. Щелкните новый объект групповой политики правой кнопкой мыши, а затем выберите **Изменить**.
6. Выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Регистрация устройств**. Щелкните правой кнопкой мыши пункт **Зарегистрировать подключенные к домену компьютеры в качестве устройств** и выберите **Изменить**.
   
   > [!NOTE]
   > В более ранних версиях консоли управления групповыми политиками этот шаблон групповой политики назывался по-другому. Если вы используете более раннюю версию консоли, перейдите к `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Выберите параметр **Включено**, а затем — **Применить**.
8. Нажмите кнопку **ОК**.
9. Свяжите объект групповой политики с выбранным расположением. Например, его можно связать с определенным подразделением. Его также можно связать с определенной группой безопасности компьютеров, которые регистрируются в Azure AD автоматически. Чтобы задать эту политику для всех присоединенных к домену компьютеров Windows 10 и Windows Server 2016 в организации, свяжите объект групповой политики с доменом.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Пакеты установщика Windows для компьютеров, не работающих на базе Windows 10

Чтобы регистрировать в федеративной среде присоединенные к домену компьютеры Windows нижнего уровня, скачайте из Центра загрузки Майкрософт и установите пакет установщика Windows (MSI-файл), опубликованный на странице [Microsoft Workplace Join for non-Windows 10 computers](https://www.microsoft.com/en-us/download/details.aspx?id=53554) (Microsoft Workplace Join для компьютеров под управлением ОС, отличной от Windows 10).

Для развертывания пакета используйте систему распространения программного обеспечения, например System Center Configuration Manager. Пакет поддерживает параметры стандартной автоматической установки с использованием параметра *quiet*. В System Center Configuration Manager доступны дополнительные преимущества предыдущих версий, такие как возможность отслеживать ход выполнения регистрации. Дополнительные сведения см. на странице [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

Установщик создает в системе запланированную задачу, которая выполняется в контексте пользователя. Задача запускается в момент входа пользователя в систему Windows. Эта задача автоматически регистрирует устройство в Azure AD, используя учетные данные пользователя, после проверки подлинности с использованием встроенной проверки подлинности Windows. Чтобы просмотреть запланированные задачи, выберите на устройстве **Microsoft** > **Присоединение к рабочей области** и перейдите к библиотеке планировщика задач.

## <a name="step-5-verify-registered-devices"></a>Шаг 5. Проверка зарегистрированных устройств

Список успешно зарегистрированных корпоративных устройств вашей организации вы можете получить с помощью командлета [Get MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice), входящего в [модуль PowerShell для Azure Active Directory](https://docs.microsoft.com/en-us/powershell/msonline/).

Выходные данные этого командлета содержат список устройств, зарегистрированных в Azure AD. Чтобы получить полный список устройств, используйте параметр **-All**. Устройства затем можно отфильтровать по свойству **deviceTrustType**. У присоединенных к домену устройств оно имеет значение **Domain Joined**.

## <a name="next-steps"></a>Дальнейшие действия

* [Azure Active Directory automatic device registration FAQ](active-directory-device-registration-faq.md) (Часто задаваемые вопросы об автоматической регистрации устройств)
* [Troubleshooting auto-registration of domain joined computers to Azure AD – Windows 10 and Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md) (Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров под управлением Windows 10 и Windows Server 2016)
* [Troubleshooting auto-registration of domain joined computers to Azure AD for Windows down-level clients](active-directory-device-registration-troubleshoot-windows-legacy.md) (Устранение неполадок автоматической регистрации присоединенных к домену Azure AD компьютеров под управлением ОС, отличных от Windows 10)
* [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png

