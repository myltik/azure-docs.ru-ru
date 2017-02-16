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
ms.date: 12/14/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: da1a8c45a1753d617e0cb62f99bc47d22838a8e8
ms.openlocfilehash: ac58f3f5498edbd6f7b19db647a9513a485d6dbf


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Настройка автоматической регистрации присоединенных к домену устройств Windows в Azure Active Directory

Для использования [условного доступа на основе устройств в Azure Active Directory](active-directory-conditional-access.md) необходимо зарегистрировать компьютеры в Azure Active Directory (Azure AD). В этой статье описана процедура настройки автоматической регистрации присоединенных к домену устройств Windows вашей организации в Azure AD.

> [!NOTE]
> В ноябрьском обновлении Windows 10 представлены некоторые расширенные пользовательские функции Azure AD. Однако юбилейное обновление Windows 10 полностью поддерживает условный доступ на основе устройств. Дополнительные сведения об условном доступе см. в статье [Условный доступ в Azure Active Directory](active-directory-conditional-access.md). Дополнительные сведения об устройствах Windows 10 в рабочей области и о процедуре регистрации устройств Windows 10 в Azure AD см. в статье [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md).
> 
> 

Для устройств под управлением Windows вы можете зарегистрировать более ранние версии этой ОС, включая следующие:

- Windows 8.1
- Windows 7

Для устройств под управлением Windows Server можно зарегистрировать следующие платформы:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>Предварительные требования

Главное требование для автоматической регистрации присоединенных к домену устройств с помощью Azure AD — это наличие последней версии Azure Active Directory Connect (Azure AD Connect).

В зависимости от способа развертывания Azure AD Connect (экспресс-установка, выборочная установка или обновление на месте) следующие компоненты могут быть настроены автоматически:

- **Точка подключения службы в локальной службе Active Directory.** Требуется для получения сведений о клиенте Azure AD в компьютерах, которые нужно зарегистрировать в Azure AD.
 
- **Правила преобразования выдачи служб федерации Active Directory (AD FS).** Требуются для аутентификации компьютеров после регистрации (применимо к федеративным конфигурациям).

Если некоторые устройства в вашей организации не являются присоединенными к домену устройствами Windows 10, сделайте следующее:

* Настройте политику регистрации устройств в Azure AD.
* В службах федерации Active Directory в качестве действующей альтернативы многофакторной идентификации выберите встроенную проверку подлинности Windows (IWA).

## <a name="step-1-configure-service-connection-point"></a>Шаг 1. Настройка точки подключения службы 

Объект точки подключения службы должен существовать в разделе контекста именования конфигурации домена, к которому присоединен компьютер. Точка подключения службы содержит сведения о клиенте Azure AD, в котором будут регистрироваться компьютеры. В конфигурации Active Directory с несколькими лесами точка подключения службы должна существовать во всех лесах с присоединенными к домену компьютерами.

Точка подключения службы находится по адресу:  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[ваш_контекст_именования-конфигурации]**.

Для леса, в котором домену Active Directory назначено имя *example.com*, контекст именования конфигурации следующий:  

**CN=Configuration,DC=example,DC=com**.

Вы можете проверить существование объекта и получить значения обнаружения, используя следующий сценарий Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

В выходных данных **$scp.Keywords** указаны сведения о клиенте Azure AD, такие как показаны ниже:

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Если точка подключения службы не существует, создайте ее, выполнив на сервере Azure AD Connect следующий сценарий PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**Примечания:**

- При выполнении командлета **$aadAdminCred = Get-Credential** требуется ввести имя пользователя. Используйте следующий формат имени пользователя: **user@example.com**. 


- При выполнении командлета **Initialize-ADSyncDomainJoinedComputerSync** замените [*connector account name*] учетной записью домена, которая используется в качестве учетной записи соединителя Active Directory.
  
- В командлете используется модуль Active Directory PowerShell, который работает на основе веб-служб Active Directory в контроллере домена. Контроллеры домена под управлением Windows Server 2008 R2 и более поздних версий поддерживают веб-службы Active Directory. Чтобы создать точку подключения службы и присвоить значения ключевых слов, для контроллеров домена под управлением Windows Server 2008 или более ранних версий используйте API System.DirectoryServices с помощью PowerShell.
 
 



##<a name="step-2-register-your-devices"></a>Шаг 2. Регистрация устройств

Выбор подходящей процедуры регистрации устройства зависит от того, входит ли организация в федерацию или нет. 


### <a name="device-registration-in-non-federated-organizations"></a>Регистрация устройства в нефедеративных организациях

Регистрация устройства в нефедеративной организации поддерживается, только если выполнены следующие требования:

- ваше устройство под управлением Windows 10 или Windows Server 2016;
- устройства присоединены к домену;
- включена синхронизация паролей с помощью Azure AD Connect.

Если все эти требования выполняются, вам не нужно участвовать в регистрации устройств.  


### <a name="device-registration-in-federated-organizations"></a>Регистрация устройства в федеративных организациях

В федеративной конфигурации Azure AD устройства проходят проверку подлинности в Azure AD с помощью служб федерации Active Directory (или на локальном сервере федерации). Они регистрируются в службе регистрации устройств Azure Active Directory.

Для компьютеров, работающих на базе Windows 10 и Windows Server 2016, Azure AD Connect связывает объект устройства в Azure AD с объектом локальной учетной записи компьютера. Для завершения регистрации и создания объекта устройства во время проверки подлинности в службе регистрации устройств Azure AD должны существовать следующие утверждения:

- **http://schemas.microsoft.com/ws/2012/01/accounttype** содержит значение DJ, определяющее субъект средства проверки подлинности в качестве присоединенного к домену компьютера.

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** содержит значение атрибута **objectGUID** локальной учетной записи компьютера.
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** содержит основной идентификатор безопасности (SID) компьютера, соответствующий значению атрибута **objectSid** локальной учетной записи компьютера.

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** содержит значение, которое Azure AD использует, чтобы установить доверительные отношения с маркером, выданным службами федерации Active Directory или локальной службой маркеров безопасности. Это важно, если в Azure AD есть несколько проверенных доменов. В случае со службами федерации Active Directory должно быть указано значение **http://\<*domain-name*\>/adfs/services/trust/**, где **\<domain-name\>** — это проверенное доменное имя в Azure AD.

Дополнительные сведения о проверенных доменных именах см. в статье [Добавление имени личного домена в Azure Active Directory](active-directory-add-domain.md).  
Чтобы получить список проверенных доменов компании, можно использовать командлет [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain). 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


Присоединенные к домену компьютеры Windows 10 и Windows Server 2016 проходят аутентификацию с помощью встроенной аутентификации Windows для активной конечной точки WS-Trust, размещенной в AD FS. Убедитесь, что эта конечная точка включена. При использовании прокси-службы проверки подлинности через Интернет также убедитесь, что эта конечная точка публикуется через прокси-службу. Конечная точка — **adfs/services/trust/13/windowstransport**. 

Ее следует включить в консоли управления AD FS в разделе **Служба > Конечные точки**. Если вы не используете службы AD FS в качестве локального сервера федерации, то следуйте инструкциям поставщика, чтобы убедиться, что соответствующая конечная точка включена. 



> [!NOTE]
> Если вы не используете службы федерации Active Directory в качестве локального сервера федерации, следуйте инструкциям поставщика, чтобы создать правила для выдачи этих утверждений.
> 
> 




**Для создания правил вручную в AD FS сделайте следующее:**

- Выберите один из следующих сценариев Windows PowerShell. 
- Запустите сценарий Windows PowerShell в сеансе, подключенном к серверу. 
- Замените первую строку проверенным доменным именем организации в Azure AD.




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>Настройка правил AD FS в среде с одним доменом

Для добавления правил AD FS, если у вас только **один проверенный домен**, используйте следующий сценарий:


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

    $rule1 = '@RuleName = "Issue object GUID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(claim = c2);' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>Настройка правил AD FS в среде с несколькими доменами

Если у вас несколько проверенных доменов, сделайте следующее:

1. Удалите имеющееся правило **IssuerID**, созданное в Azure AD Connect.  
Ниже приведен пример этого правила: c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. Выполните следующий сценарий: 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

        $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

        $rule2 = '@RuleName = "Issue account type for domain joined computers" 

        c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

        $rule3 = '@RuleName = "Pass through primary SID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(claim = c2);' 

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>Шаг 3. Настройка аутентификации при регистрации устройств в службах федерации Active Directory

Убедитесь, что встроенная аутентификация Windows настроена в качестве действующей альтернативы Многофакторной идентификации для регистрации устройств в службах федерации Active Directory. Для этого нужно правило преобразования выдачи, передающее метод проверки подлинности.

1. В консоли управления служб федерации Active Directory выберите **AD FS** > **Отношения доверия** > **Отношения доверия проверяющей стороны**.
2. Щелкните правой кнопкой мыши объект отношений доверия с проверяющей стороной платформы удостоверений Microsoft Office 365 и выберите **Edit Claim Rules** (Изменить правила для утверждений).
3. На вкладке **Правила преобразования выдачи** выберите **Добавить правило**.
4. Из списка шаблонов **Claim rule** (Правило для утверждений) выберите **Отправка утверждений с помощью настраиваемого правила**.
5. Щелкните **Далее**.
6. В поле **Claim rule name** (Имя правила для утверждений метода проверки подлинности) введите **Auth Method Claim Rule** (Правило для утверждений метода проверки подлинности).
7. В поле **Claim rule** (Правило для утверждения) введите это правило:  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. На сервере федерации введите следующую команду PowerShell:
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** — это имя объекта проверяющей стороны для объекта отношений доверия с проверяющей стороной Azure AD. Как правило, этот объект называется **платформой удостоверений Microsoft Office 365**.



##<a name="step-4-deployment-and-rollout"></a>Шаг 4. Развертывание

После выполнения предварительных требований для присоединенных к домену компьютеров они готовы к регистрации в Azure AD.

Присоединенные к домену компьютеры с юбилейным обновлением Windows 10 и Windows Server 2016 автоматически зарегистрируются в Azure AD при следующей перезагрузке устройства или при входе пользователя в систему Windows. Новые компьютеры, присоединяемые к домену, зарегистрируются в Azure AD при перезагрузке после присоединения к домену.

> [!NOTE]
> Присоединенные к домену компьютеры с обновлением Windows 10, выпущенным в ноябре, будут автоматически регистрироваться в Azure AD только в том случае, если задан объект групповой политики развертывания.
> 
> 

Для управления развертыванием автоматической регистрации присоединенных к домену компьютеров Windows 10 и Windows Server 2016 можно использовать объект групповой политики. 

Для развертывания автоматической регистрации присоединенных к домену компьютеров, работающих не на базе Windows 10, на выбранных компьютерах можно развернуть пакет установщика Windows.

> [!NOTE]
> Для всех компьютеров под управлением ОС, отличной от Windows 10 или Windows Server 2016, рекомендуется использовать пакет установщика Windows, как описано далее в этом документе.
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Создание объекта групповой политики для управления развертыванием автоматической регистрации

Для управления развертыванием автоматической регистрации компьютеров в Azure AD можно развернуть групповую политику **регистрации присоединенных к домену компьютеров в качестве устройств** на компьютерах, которые нужно зарегистрировать. Например, можно развернуть политику в подразделении или группе безопасности.

**Чтобы задать политику, сделайте следующее:**

1. Откройте диспетчер серверов и выберите **Инструменты** > **Управление групповой политикой**.
2. Перейдите к узлу домена, соответствующему домену, на котором нужно активировать автоматическую регистрацию компьютеров Windows 10 и Windows Server 2016.
3. Щелкните правой кнопкой мыши **Объекты групповой политики**, а затем выберите **Создать**.
4. Введите имя объекта групповой политики. Например, *автоматическая регистрация в Azure AD*. Нажмите кнопку **ОК**.
5. Щелкните новый объект групповой политики правой кнопкой мыши, а затем выберите **Изменить**.
6. Выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Регистрация устройств**. Щелкните правой кнопкой мыши пункт **Зарегистрировать подключенные к домену компьютеры в качестве устройств** и выберите пункт **Изменить**.
   
   > [!NOTE]
   > В более ранних версиях консоли управления групповыми политиками этот шаблон групповой политики назывался по-другому. Если вы используете более раннюю версию консоли, выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Присоединение к рабочей области** > **Automatically workplace join client computers** (Автоматическое присоединение клиентских компьютеров к рабочей области).
   > 
   > 
7. Выберите параметр **Включено**, а затем — **Применить**.
8. Нажмите кнопку **ОК**.
9. Свяжите объект групповой политики с выбранным расположением. Например, его можно связать с определенным подразделением. Его также можно связать с определенной группой безопасности компьютеров, которые регистрируются в Azure AD автоматически. Чтобы задать эту политику для всех присоединенных к домену компьютеров Windows 10 и Windows Server 2016 в организации, свяжите объект групповой политики с доменом.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Пакеты установщика Windows для компьютеров, не работающих на базе Windows 10
Чтобы зарегистрировать присоединенные к домену компьютеры, работающие под управлением Windows 8.1, Windows 7, Windows 2012 R2, Windows Server 2012 или Windows Server 2008 R2 в федеративной среде, можно скачать и установить эти файлы пакетов установщика Windows (MSI):

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Для развертывания пакета используйте систему распространения программного обеспечения, например System Center Configuration Manager. Пакет поддерживает параметры стандартной автоматической установки с использованием параметра *quiet*. В System Center Configuration Manager 2016 доступны дополнительные преимущества предыдущих версий, такие как возможность отслеживать ход выполнения регистраций. Дополнительные сведения см. на странице [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

Установщик создает в системе запланированную задачу, которая выполняется в контексте пользователя. Задача запускается в момент входа пользователя в систему Windows. Эта задача автоматически регистрирует устройство в Azure AD, используя учетные данные пользователя, после проверки подлинности с помощью IWA. Чтобы просмотреть запланированные задачи, выберите **Microsoft** > **Присоединение к рабочей области** и перейдите к библиотеке планировщика задач.

## <a name="next-steps"></a>Дальнейшие действия
* [Условный доступ в Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Dec16_HO4-->


