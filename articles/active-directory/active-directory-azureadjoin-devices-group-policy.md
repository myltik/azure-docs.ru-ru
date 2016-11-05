---
title: Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10 | Microsoft Docs
description: Описание настройки групповой политики, которая позволяет присоединять устройства к домену в корпоративной сети.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
tags: azure-classic-portal

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila

---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10
Организации уже более 15 лет используют присоединение к домену для подключения устройств. Оно позволяет пользователям входить на устройства с помощью рабочих учетных записей Windows Server Active Directory (Active Directory), а ИТ-специалистам — полностью управлять этими устройствами. Организации обычно полагаются на создание образов для подготовки устройств для пользователей и используют для управления ими System Center Configuration Manager (SCCM) или групповые политики.

Присоединение к домену в Windows 10 обеспечивает следующие преимущества после подключения устройств к Azure Active Directory (Azure AD):

* единый вход для доступа к ресурсам Azure AD из любого расположения;
* доступ к корпоративному хранилищу Windows с помощью рабочих учетных записей (не требуется учетная запись Майкрософт);
* перенос пользовательских параметров между устройствами, которые используют рабочую учетную запись (с соблюдением корпоративных требований; не требуется учетная запись Майкрософт);
* строгая проверка подлинности и удобный вход для рабочих учетных записей с помощью Microsoft Passport и Windows Hello;
* возможность ограничить доступ только для устройств, соответствующих параметрам групповой политики устройств организации.

## <a name="prerequisites"></a>Предварительные требования
Присоединение к домену доступно, как и раньше. Однако чтобы получить преимущества Azure AD в отношении единого входа, перемещения параметров с помощью рабочей учетной записи и доступа к Магазину Windows с рабочей учетной записью, потребуется следующее:

* Подписка Azure AD
* Azure AD Connect для расширения локального каталога до Azure AD;
* политика, настроенная для подключения к Azure AD устройств, присоединенных к домену;
* сборка Windows 10 (сборка 10551 или более поздней версии) для устройств.

Для включения Microsoft Passport for Work и Windows Hello также потребуется следующее:

* инфраструктура открытых ключей (PKI) для выдачи сертификатов пользователей;
* System Center Configuration Manager (ознакомительная техническая версия 1509). Дополнительные сведения см. в разделе [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) и [блоге рабочей группы по System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). Это необходимо для развертывания сертификатов пользователей на основе ключей Microsoft Passport.

В качестве альтернативы для требования развертывания PKI можно реализовать следующую конфигурацию:

* Несколько контроллеров домена с доменными службами Active Directory Windows Server 2016.

Для включения условного доступа можно создать параметры групповой политики, разрешающие доступ к присоединенным к домену устройствам без дополнительных развертываний. Для управления доступом в зависимости от соответствия устройства требованиям необходимо следующее:

* System Center Configuration Manager (ознакомительная техническая версия 1509) для сценариев с использованием Microsoft Passport.

## <a name="deployment-instructions"></a>Инструкции по развертыванию
### <a name="step-1:-deploy-azure-active-directory-connect"></a>Шаг 1. Развертывание Azure Active Directory Connect
Azure AD Connect позволяет выполнять подготовку локальных компьютеров как объектов устройств в облаке. Сведения о развертывании Azure AD Connect см. в разделе "Установка Azure AD Connect" в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md#install-azure-ad-connect).

* Если вы выполнили [выборочную установку Azure AD Connect](active-directory-aadconnect-get-started-custom.md) (не экспресс-установку), то необходимо выполнить процедуру **Создание точки подключения службы в локальном каталоге Active Directory**, описанную далее в этом разделе.
* При наличии федеративной конфигурации с Azure AD перед установкой Azure AD Connect (например в случае предварительного развертывания служб федерации Active Directory) потребуется выполнить процедуру **Настройка правил утверждений служб федерации Active Directory** , описанную далее в этом разделе.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Создание точки подключения службы в локальном каталоге Active Directory
Присоединенные к домену устройства будут использовать точку подключения службы для получения сведений о клиенте Azure AD во время автоматической регистрации с помощью службы регистрации устройств Azure.

На сервере Azure AD Connect выполните следующие команды PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


При выполнении командлета $aadAdminCred = Get-Credential используйте формат *user@example.com* для имени пользователя, которому принадлежат учетные данные, введенные при появлении всплывающего окна Get-Credential.

При выполнении командлета Initialize-ADSyncDomainJoinedComputerSync… замените [*connector account name*] учетной записью домена, которая используется в качестве учетной записи соединителя Active Directory.

#### <a name="configure-ad-fs-claim-rules"></a>Настройка правил утверждений служб федерации Active Directory
Настройка правил утверждений служб AD FS позволяет мгновенно выполнять регистрацию компьютеров с помощью службы регистрации Azure, позволяя компьютерам проходить проверку подлинности с помощью протокола Kerberos или NTLM через службы AD FS. Если не выполнить эту настройку, компьютеры будут медленнее получать доступ к Azure AD, так как подключение будет выполняться в соответствии с расписанием синхронизации Azure AD Connect.

> [!NOTE]
> Если вы не используете службы AD FS в качестве локального сервера федерации, следуйте инструкциям поставщика для создания правил утверждений.
> 
> 

На сервере AD FS (или в сеансе подключения к серверу AD FS) выполните следующие команды PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
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

> [!NOTE]
> Компьютеры с Windows 10 будут проходить проверку подлинности с помощью встроенной проверки подлинности Windows для активной конечной точки WS-Trust, размещенной в службах федерации Active Directory. Убедитесь, что эта конечная точка включена. При использовании прокси-службы проверки подлинности через Интернет также убедитесь, что эта конечная точка публикуется через прокси-службу. Для этого перейдите в раздел adfs/services/trust/13/windowstransport. Она должна отобразиться как включенная в консоли управления AD FS в разделе **Службы** > **Конечные точки**.
> 
> 

### <a name="step-2:-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Шаг 2. Настройка автоматической регистрации устройств с помощью групповой политики в Active Directory
Вы можете использовать групповую политику Active Directory, чтобы настроить автоматическую регистрацию присоединенных к домену устройств Windows 10 в Azure AD.

> [!NOTE]
> Дополнительные сведения о настройке автоматической регистрации устройств см. в статье [Настройка автоматической регистрации в Azure Active Directory присоединенных к домену устройств Windows](active-directory-conditional-access-automatic-device-registration-setup.md).
> 
> Этот шаблон групповой политики был переименован в Windows 10. Если вы используете инструмент групповой политики на компьютере Windows 10, то политика будет отображаться следующим образом: <br>
> **Регистрация подключенных к домену компьютеров как устройств**<br>
> Политика находится в следующем расположении:<br>
> ***Конфигурация компьютера/Политики/Административные шаблоны/Компоненты Windows/Регистрация устройства***
> 
> 

## <a name="additional-information"></a>Дополнительная информация
* [Windows 10 для предприятия: использование устройств для работы](active-directory-azureadjoin-windows10-devices-overview.md)
* [Использование возможностей облачных служб на устройствах с Windows 10 с помощью присоединения к Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Сценарии использования для присоединения к Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Подключение присоединенных к домену устройств к Azure AD для работы в Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Настройка присоединения к Azure AD](active-directory-azureadjoin-setup.md)

<!--HONumber=Oct16_HO2-->


