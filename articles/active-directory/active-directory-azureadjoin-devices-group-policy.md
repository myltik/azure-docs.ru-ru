<properties 
	pageTitle="Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10 | Microsoft Azure" 
	description="Описание настройки групповых политик, которые позволяют присоединять устройства к домену в корпоративной сети." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="11/19/2015" 

	ms.author="femila"/>

# Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10

Присоединение к домену является традиционным способом, которые организации используют для подключения устройств в течение последних 15 и более лет. Оно давало пользователям возможность выполнять вход на свои устройства с помощью рабочих учетных записей AD, а ИТ-персоналу — полностью управлять этими устройствами. Организации обычно полагаются на создание образов для подготовки устройств для пользователей и используют для управления ими System Center Configuration Manager (SCCM) или групповые политики.

Присоединение к домену в Windows 10 обеспечивает следующие преимущества после подключения к Azure AD:

- единый вход для доступа к ресурсам Azure AD из любого места;
- доступ к корпоративному хранилищу Windows с помощью рабочих учетных записей (не требуется учетная запись Майкрософт);
- роуминг соответствующих требованиям предприятия параметров пользователей для устройств с помощью рабочей учетной записи (не требуется учетная запись Майкрософт);
- строгая проверка подлинности и удобный вход для рабочих учетных записей с помощью Microsoft Passport и Windows Hello;
- возможность ограничения доступа к устройствам, соответствующим политикам организации в отношении устройств.

##Предварительные требования

Присоединение к домену работает, как и прежде, однако чтобы получить преимущества Azure AD для единого входа, роуминга параметров с помощью рабочей учетной записи и доступа к Магазину Windows с рабочей учетной записью, потребуется следующее:

- подписка Azure AD;
- Azure AD Connect для расширения локального каталога до Azure AD;
- задание политики для подключения устройств, присоединенных к домену, к Azure AD;
- сборка Windows 10 (сборка 10551 или более поздней версии) для устройств.

Для включения Microsoft Passport for Work и Windows Hello дополнительно потребуется следующее:

- инфраструктура PKI для выдачи сертификатов пользователей;
- System Center Configuration Manager (ознакомительная техническая версия 1509). Дополнительные сведения см. в разделе [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) и [блоге рабочей группы по System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Это необходимо для развертывания сертификатов пользователей на основе ключей Microsoft Passport.

В качестве альтернативы для требования развертывания PKI можно реализовать следующую конфигурацию:

- Несколько контроллеров домена с доменными службами Active Directory Windows Server 2016.

Для включения условного доступа можно создать политику, разрешающую доступ к "присоединенным к домену" устройствам без дополнительных развертываний. Для управления доступом в зависимости от соответствия устройства требованиям необходимо следующее:

- System Center Configuration Manager (ознакомительная техническая версия 1509) для сценариев с использованием Microsoft Passport.

## Инструкции по развертыванию


## Шаг 1. Развертывание Azure Active Directory Connect

Azure AD Connect позволяет выполнять подготовку локальных компьютеров как объектов устройств в облаке. Сведения о развертывании Azure AD Connect см. в статье "Обеспечение гибридного управления для каталога с помощью Azure AD Connect".

 - Если вы выполнили [выборочную установку Azure AD Connect](https://azure.microsoft.com/ru-RU/documentation/articles/active-directory-aadconnect-get-started-custom/) (не экспресс-установку), необходимо выполнить процедуру **Создание точки подключения службы в локальной службе Active Directory**, описанную ниже.
 - При наличии федеративной конфигурации с Azure AD перед установкой Azure AD Connect (например, в случае предварительного развертывания служб федерации Active Directory) потребуется выполнить процедуру **Настройка правил утверждений служб федерации Active Directory**, описанную ниже.

### Создание точки подключения службы в локальной службе Active Directory

Присоединенные к домену устройства будут использовать этот объект для получения сведений о клиенте Azure AD во время автоматической регистрации с помощью службы регистрации устройств Azure. На сервере Azure AD Connect выполните следующие команды PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

>[AZURE.NOTE]Замените [*connector account name*] учетной записью домена, которая используется в качестве учетной записи соединителя Active Directory.

>[AZURE.NOTE]Имя пользователя учетных данных, указываемое при появлении всплывающего окна Get-Credential, должно быть в формате **user@example.com*.

### Настройка правил утверждений служб федерации Active Directory
Такая настройка позволяет мгновенно выполнять регистрацию компьютеров с Azure DRS, позволяя компьютерам проходить проверку подлинности с помощью протокола Kerberos или NTLM через службы AD FS. Если не выполнить эту настройку, компьютеры будут медленнее получать доступ к Azure AD, так как подключение будет выполняться в соответствии с расписанием синхронизации Azure AD Connect.

>[AZURE.NOTE]Если вы не используете службы AD FS как локальный сервер федерации, следуйте инструкциям поставщика для создания правил утверждений.

На сервере AD FS (или в сеансе подключения к серверу AD FS) выполните следующие команды PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed 
     |   for DomainJoin++.  The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>
 
    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules
 
    $rule1 = '@RuleName = "Issue object GUID" 
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'
 
    $rule2 = '@RuleName = "Issue account type for domain joined computers" 
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'
 
    $rule3 = '@RuleName = "Pass through primary SID" 
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 
          => issue(claim = c2);'
 
    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3
 
    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules
 
    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

>[AZURE.NOTE]Компьютеры с Windows 10 будут проходить проверку подлинности с помощью встроенной проверки подлинности Windows для активной конечной точки WS-Trust, размещенной в службах федерации Active Directory. Необходимо убедиться, что эта конечная точка включена. При использовании прокси-службы проверки подлинности через Интернет также необходимо убедиться, что эта конечная точка публикуется через прокси-службу. Для этого убедитесь, что конечная точка adfs/services/trust/13/windowstransport отображается как включенная в консоли управления AD FS (раздел "Службы" > "Конечные точки").


## Шаг 2. Настройка автоматической регистрации устройств с помощью групповой политики в Active Directory

Вы можете использовать групповую политику Active Directory, чтобы настроить автоматическую регистрацию присоединенных к домену устройств Windows 10 с помощью Azure AD. Для этого выполните следующие пошаговые инструкции.

1. 	Откройте диспетчер серверов и выберите **Инструменты** > **Управление групповой политикой**.
2.	В разделе "Управление групповой политикой" перейдите к соответствующему узлу домена, для которого необходимо включить присоединение к Azure AD.
3.	Щелкните правой кнопкой мыши **Объекты групповой политики** и выберите **Создать**. Укажите имя объекта групповой политики, например "Автоматическое присоединение к Azure AD". Нажмите кнопку **ОК**.
4.	Щелкните правой кнопкой мыши новый объект групповой политики и выберите **Изменить**.
5.	Последовательно выберите **Конфигурация компьютера** > **Политики** > **Административные шаблоны** > **Компоненты Windows** > **Присоединение к рабочей области**.
6.	Щелкните правой кнопкой мыши **Автоматическое присоединение к рабочей области клиентских компьютеров** и выберите пункт **Изменить**.
7.	Установите переключатель **Включено**, а затем щелкните **Применить**. Нажмите кнопку **ОК**.
8.	Теперь вы можете связать объект групповой политики с выбранным расположением. Чтобы включить эту политику для всех присоединенных к домену устройств Windows 10 в организации, свяжите групповую политику с доменом. Например:
 - с определенным подразделением в AD, в котором будут размещаться компьютеры, присоединенные к домену Windows 10;
 - с определенной группой безопасности, содержащей присоединенные к домену Windows 10 компьютеры, для которых будет выполняться автоматическая регистрация в Azure AD.
 
>[AZURE.NOTE]Этот шаблон групповой политики был переименован в Windows 10. При запуске средства групповой политики с компьютера Windows 10 политика будет отображаться как <br> **Зарегистрировать подключенные к домену компьютеры в качестве устройств** и размещаться в следующем разделе:<br> ***Конфигурация компьютера/Политики/Административные шаблоны/Компоненты Windows/Регистрация устройств***

 
## Дополнительная информация
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->