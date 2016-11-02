<properties
    pageTitle="Настройка автоматической регистрации в Azure Active Directory присоединенных к домену устройств Windows| Microsoft Azure"
    description="ИТ-администраторы могут автоматически и без предупреждений регистрировать устройства Windows, присоединенные к домену, с помощью Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="markvi"/>




# <a name="how-to-setup-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Настройка автоматической регистрации в Azure Active Directory присоединенных к домену устройств Windows 

Регистрация в Azure AD присоединенных к домену компьютеров Windows необходима для включения [условного доступа на основе устройств в Azure Active Directory](active-directory-conditional-access.md).

Воспользуйтесь такими обновлениями, как использование рабочей или учебной учетной записи для расширенной процедуры единого входа в приложения Azure AD, перенос параметров между устройствами предприятия, использование Магазина Windows для бизнеса, а также более надежная аутентификация и удобная процедура входа с помощью Windows Hello. 

> [AZURE.NOTE] Обновление Windows 10, выпущенное в ноябре 2015 г., поддерживает некоторые расширенные пользовательские функции, однако полная поддержка условного доступа на основе устройств содержится в юбилейном обновлении Windows 10.  
Дополнительные сведения об условном доступе см. в статье [Условный доступ в Azure Active Directory](active-directory-conditional-access.md).  
Дополнительные сведения об устройствах Windows 10 в рабочей области и о процедуре регистрации пользователей в Azure AD см. в статье [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md). 


Регистрация поддерживается в предыдущих версиях Windows, включая: 

- Windows 8.1 

- Windows 7 

В случае использования компьютеров Windows Server в качестве настольных компьютеров (например, когда разработчик использует Windows Server как основной компьютер) зарегистрировать можно следующие платформы: 

- Windows Server 2016 

- Windows Server 2012 R2 

- Windows Server 2012 

- Windows Server 2008 R2 

Ниже описывается, что необходимо сделать в среде для регистрации в Azure AD присоединенных к домену устройств Windows вашей организации. Ознакомьтесь со следующими разделами: 

1. Предварительные требования 

2. Развертывание 

3. Устранение неполадок 

4. Часто задаваемые вопросы 

 

## <a name="prerequisites"></a>Предварительные требования 

Главное требование для включения автоматической регистрации присоединенных к домену устройств в Azure AD — это наличие последней версии Azure AD Connect. 

В зависимости от способа развертывания Azure AD Connect (экспресс-установка, выборочная установка или обновление на месте) следующие компоненты могут быть настроены автоматически: 

1. Точка подключения службы (SCP) в локальной службе Active Directory для получения компьютерами сведений о клиенте Azure AD во время регистрации в Azure AD. 

2. Правила преобразования выдачи AD FS для аутентификации компьютеров после регистрации (применимо к федеративным конфигурациям). 

При наличии в организации присоединенных к домену компьютеров, работающих не на базе Windows 10, необходимо выполнить следующие действия: 

1. Убедиться, что в Azure AD включена политика, позволяющая пользователям регистрировать устройства. 

2. Убедиться, что в AD FS в качестве действующей альтернативы многофакторной проверки подлинности (MFA) выбрана встроенная проверка подлинности Windows (WIA). 

 

## <a name="service-connection-point-for-discovery-of-azure-ad-tenant"></a>Точка подключения службы для получения сведений о клиенте Azure AD 

Объект SCP, содержащий сведения о клиенте Azure AD, где будут регистрироваться компьютеры, должен существовать в разделе контекста именования конфигурации леса домена, к которому присоединены компьютеры. В конфигурации Active Directory с несколькими лесами точка SCP должна существовать во всех лесах, где есть присоединенные компьютеры. 

Точка SCP должна находиться в следующем расположении в Active Directory: 

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context] 

> [AZURE.NOTE] Для леса, где домен Active Directory имеет имя example.com, контекст именования конфигурации будет следующим: CN=Configuration,DC=example,DC=com 

Проверить существование объекта и значений для получения сведений о клиенте Azure AD можно с помощью следующего сценария PowerShell (замените контекст именования конфигурации в примере собственными значениями): 

    $scp = New-Object System.DirectoryServices.DirectoryEntry; 

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com"; 

    $scp.Keywords; 

В выходных данных $scp.Keywords отображаются сведения клиенте Azure AD, такие как показаны ниже: 

    azureADName:microsoft.com 

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47 

Если точка SCP не существует, ее можно создать, выполнив на сервере Azure AD Connect следующий сценарий PowerShell: 

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1"; 

    $aadAdminCred = Get-Credential; 

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred; 


> [AZURE.NOTE] При выполнении команды $aadAdminCred = Get-Credential используйте формат имени пользователя user@example.com из учетных данных, которые вводятся при появлении всплывающего окна Get-Credential.  
> При выполнении командлета Initialize-ADSyncDomainJoinedComputerSync замените [connector account name] учетной записью домена, которая используется в качестве учетной записи соединителя Active Directory.  
> В указанном выше командлете используется модуль Active Directory PowerShell, который работает на основе веб-служб Active Directory (ADWS) в контроллерах домена. ADWS поддерживается в контроллерах домена Windows Server 2008 R2 или более поздних версий. При наличии только контроллеров домена Windows Server 2008 или более ранних версий воспользуйтесь API System.DirectoryServices с помощью PowerShell, чтобы создать точку SCP и назначить соответствующие значения ключевых слов. 

## <a name="ad-fs-rules-for-instant-computer-registration-(federated-orgs)"></a>Правила AD FS для мгновенной регистрации компьютеров (федеративные организации) 

В федеративных конфигурациях Azure AD компьютеры будут использовать AD FS (или локальный сервер федерации) при прохождении аутентификации для регистрации в Azure AD с помощью службы регистрации устройств Azure (Azure DRS). 

> [AZURE.NOTE] В конфигурации, не являющейся федеративной (т. е. хэши паролей пользователей синхронизированы с Azure AD), присоединенные к домену компьютеры Windows 10 и Windows Server 2016 проходят аутентификацию в службе Azure DRS, используя учетные данные, которые они записывают в учетные записи локальных компьютеров и которые затем применяются в Azure AD через Azure AD Connect. Сведения о компьютерах, работающих не на базе Windows 10 или Windows Server 2016, и в конфигурации, не являющейся федеративной, приводятся в подразделе, посвященном пакету установщика Windows, раздела "Развертывание" далее в этом документе. Там вы узнаете, какие параметры необходимы для включения в вашей организации условного доступа на основе устройств, чтобы зарегистрировать присоединенные к домену компьютеры, работающие не на базе Windows 10 или Windows Server 2016. 

Для компьютеров, работающих на базе Windows 10 и Windows Server 2016, Azure AD Connect связывает объект устройства в Azure AD с локальным объектом учетной записи компьютера. Чтобы это работало, в первую очередь для завершения регистрации и создания объекта устройства во время аутентификации в службе Azure DRS должны существовать следующие утверждения: 

- `http://schemas.microsoft.com/ws/2012/01/accounttype` — содержит значение DJ, определяющее субъект, который проверяется как присоединенный к домену компьютер. 
- `http://schemas.microsoft.com/identity/claims/onpremobjectguid` — содержит значение атрибута objectGUID локальной учетной записи компьютера. 
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` — содержит основной ИД безопасности компьютера, соответствующий значению атрибута objectSid локальной учетной записи компьютера. 
- `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` — содержит соответствующее значение, которое позволяет Azure AD доверять маркеру, выданному AD FS или локальной службой маркеров безопасности. Это важно в конфигурации Active Directory с несколькими лесами, где компьютеры могут быть присоединены не к тому лесу, который подключается к Azure AD и где находится AD FS или локальная служба маркеров безопасности. В случае с AD FS значение должно быть `http://<domain-name>/adfs/services/trust/`, где \<domain-name\> — это проверенное доменное имя в Azure AD. 

Чтобы создать эти правила вручную, в AD FS можно использовать следующий сценарий PowerShell в сеансе, подключенном к серверу. Обратите внимание, что первую строку необходимо заменить на проверенное доменное имя в Azure AD для вашей организации. 

> [AZURE.NOTE] Если вы не используете службы AD FS в качестве локального сервера федерации, то следуйте инструкциям поставщика, чтобы создать соответствующие правила для выдачи этих утверждений. 

    $validatedDomain = "example.com"      # Replace example.com with your validated domain name in Azure AD 

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

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account" 

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

    $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6 

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 
 
    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

> [AZURE.NOTE] Присоединенные к домену компьютеры Windows 10 и Windows Server 2016 будут проходить аутентификацию с помощью встроенной проверки подлинности Windows для активной конечной точки WS-Trust, размещенной в службах AD FS. Убедитесь, что эта конечная точка включена. При использовании прокси-службы проверки подлинности через Интернет также убедитесь, что эта конечная точка публикуется через прокси-службу. Конечная точка — adfs/services/trust/13/windowstransport. Она должна отобразиться как включенная в консоли управления AD FS в разделе "Службы" > "Конечные точки". Если вы не используете службы AD FS в качестве локального сервера федерации, то следуйте инструкциям поставщика, чтобы убедиться, что соответствующая конечная точка включена. 

 

## <a name="ensure-ad-fs-is-set-up-to-support-authentication-of-device-for-registration"></a>Настройка служб AD FS на поддержку аутентификации при регистрации устройств

Убедиться, что при регистрации устройств в качестве действующей альтернативы многофакторной проверки подлинности (MFA) в AD FS выбрана встроенная проверка подлинности Windows (WIA).

Для этого необходимо иметь правило преобразования выдачи, которое передает метод проверки подлинности.

1. Откройте консоль управления AD FS и выберите **AD FS > Отношения доверия > Отношения доверия проверяющей стороны**. 

2. Щелкните правой кнопкой мыши объект отношений доверия с проверяющей стороной платформы удостоверений Microsoft Office 365 и выберите **Edit Claim Rules**(Изменить правила для утверждений).

2.  На вкладке **Правила преобразования выдачи** выберите **Добавить правило**.

3.  Из списка шаблонов **Правило для утверждений** выберите **Отправка утверждений с помощью настраиваемого правила**. 

4.  Щелкните **Далее**.

4.  В текстовом поле **Имя правила утверждения** введите **Auth Method Claim Rule** (Правило для утверждений метода проверки подлинности).

5.  В текстовом поле **Правило для утверждений** введите `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
=> issue(claim = c);`.

6. Откройте Windows PowerShell на сервере федерации.

7. Введите следующую команду: 

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName>\** — это имя объекта проверяющей стороны для объекта отношений доверия с проверяющей стороной Azure Active Directory. Как правило, этот объект называется платформой удостоверений Microsoft Office 365.

 

## <a name="deployment-and-roll-out"></a>Развертывание 

Когда предварительные требования полностью выполнены, присоединенные к домену компьютеры готовы к регистрации в Azure AD. 

Присоединенные к домену компьютеры с юбилейным обновлением Windows 10 и Windows Server 2016 автоматически зарегистрируются в Azure AD при следующей перезагрузке или при входе пользователя в систему Windows. Новые компьютеры, присоединяемые к домену, будут регистрироваться в Azure AD при перезагрузке, которая следует за операцией присоединения к домену. 

> [AZURE.NOTE] Присоединенные к домену компьютеры с ОС Windows 10 будут автоматически регистрироваться в Azure AD, только если задан объект групповой политики развертывания. Дополнительные сведения см. в следующем разделе. 

Для управления развертыванием автоматической регистрации присоединенных к домену компьютеров Windows 10 и Windows Server 2016 существует объект групповой политики, который можно использовать для этой цели. Для развертывания автоматической регистрации присоединенных к домену компьютеров, работающих не на базе Windows 10, существует пакет установщика Windows, который можно развернуть на выбранных компьютерах. 

> [AZURE.NOTE] Групповая политика для управления развертыванием также запускает регистрацию присоединенных к домену компьютеров Windows 8.1. Вы можете использовать политику для регистрации своих присоединенных к домену компьютеров Windows 8.1, или если у вас разные версии Windows, включая 7 или версии Windows Server, то вы можете разрешить регистрацию всех компьютеров, работающих не на базе Windows 10 или Windows Server 2016, с помощью пакета установщика Windows. 

### <a name="group-policy-object-to-control-roll-out-of-automatic-registration"></a>Объект групповой политики для управления развертыванием автоматической регистрации 

Для управления развертыванием автоматической регистрации компьютеров в Azure AD можно развернуть присоединенные к домену компьютеры регистра групповой политики как устройства на компьютерах, которые вы хотите зарегистрировать. Например, можно развернуть политику на основе группы безопасности или подразделения. 

Чтобы настроить политику, выполните следующие действия: 

1. Откройте диспетчер серверов и выберите **Инструменты > Управление групповой политикой**. 

2. В разделе "Управление групповой политикой" перейдите к соответствующему узлу домена, для которого необходимо включить автоматическую регистрацию компьютеров Windows 10 или Windows Server 2016. 

3. Щелкните правой кнопкой мыши **Объекты групповой политики**, а затем выберите **Создать**. 

4. Введите имя объекта групповой политики, например *Автоматическая регистрация в Azure AD*. Нажмите кнопку ОК. 

4. Щелкните правой кнопкой мыши новый объект групповой политики, а затем выберите **Изменить**. 

5. Последовательно выберите **Конфигурация компьютера > Политики > Административные шаблоны > Компоненты Windows > Регистрация устройства**, щелкните правой кнопкой мыши **Зарегистрировать подключенные к домену компьютеры в качестве устройств**, а затем выберите **Изменить**. 

    > [AZURE.NOTE] В предыдущих версиях консоли управления групповыми политиками этот шаблон групповой политики имел другое имя. Если вы используете предыдущую версия консоли, то эта политика будет находиться в следующем расположении: "Конфигурация компьютера" > "Политики" > "Административные шаблоны" > "Компоненты Windows" > "Присоединение к рабочей области" с именем шаблона "Automatically workplace join client computers" (Автоматическое присоединение к рабочей области клиентских компьютеров). 

6. Установите переключатель в положение **Включено**, а затем щелкните **Применить**. 

7. Нажмите кнопку **ОК**. 

7. Свяжите объект групповой политики с выбранным расположением. Например, с определенным подразделением, в котором расположены компьютеры, или с группой безопасности, содержащей компьютеры, которые будут автоматически регистрироваться в Azure AD. Чтобы включить эту политику для всех присоединенных к домену компьютеров Windows 10 и Windows Server 2016, свяжите объект групповой политики с доменом. 

## <a name="msi-package-for-non-windows-10-computers"></a>Пакет MSI для компьютеров, работающих не на базе Windows 10  

Чтобы зарегистрировать присоединенные к домену компьютеры, работающие под управлением Windows 7, Windows 8.1, Windows 2008 R2, Windows Server 2012 или Windows Server 2012 R2, необходимо воспользоваться пакетом установщика Windows (MSI). Он доступен для скачивания:

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Для развертывания этого пакета необходимо использовать систему распространения программного обеспечения, например System Center Configuration Manager. Пакет поддерживает параметры стандартной автоматической установки с использованием параметра /quiet. При использовании System Center Configuration Manager 2016 вам будут доступны дополнительные преимущества, такие как возможность отслеживать ход выполнения регистраций. Дополнительные сведения см. в [System Center 2016](https://www.microsoft.com/cloud-platform/system-center-2016). 

Установщик создает в системе запланированную задачу, которая выполняется в контексте пользователя и запускается в момент входа пользователя в Windows. Эта задача автоматически регистрирует устройство в Azure AD, используя учетные данные пользователя, после аутентификации с помощью встроенной проверки подлинности Windows. Запланированное задание находится в библиотеке планировщика заданий на вкладке **Майкрософт > Присоединение к рабочей области**. 



## <a name="additional-topics"></a>Дополнительные разделы

- [Условный доступ к Azure Active Directory](active-directory-conditional-access.md)



<!--HONumber=Oct16_HO2-->


