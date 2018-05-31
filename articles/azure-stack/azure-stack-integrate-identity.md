---
title: 'Интеграция центра обработки данных Azure Stack: идентификация'
description: Узнайте, как интегрировать AD FS Azure Stack с AD FS центра обработки данных.
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: dc6c8ef2953b7495c734ec8b16530cdd812ac792
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770525"
---
# <a name="azure-stack-datacenter-integration---identity"></a>Интеграция центра обработки данных Azure Stack: идентификация
Azure Stack можно развернуть с помощью Azure Active Directory (Azure AD) или служб федерации Active Directory (AD FS) в качестве поставщика удостоверений. Сделать выбор следует перед развертыванием Azure Stack. Развертывание с помощью AD FS также называется развертыванием Azure Stack в отключенном режиме.

В следующей таблице показаны различия между двумя вариантами идентификации.

||Без подключения к Интернету|С подключением к Интернету|
|---------|---------|---------|
|Выставление счетов|Должна быть оплата на основе емкости.<br> Только при наличии соглашения Enterprise (EA).|Оплата на основе емкости или оплата по мере использования.<br>EA или поставщик облачных решений (CSP).|
|Удостоверение|Должны быть службы AD FS.|Azure AD или AD FS.|
|Синдикация Marketplace|Поддерживаются<br>Лицензирование BYOL.|Поддерживаются<br>Лицензирование BYOL.|
|Регистрация|Рекомендуется, требуется съемный носитель<br> и отдельное подключенное устройство.|Автоматическая регистрация.|
|Исправления и обновления|Установка обязательна, требуется съемный носитель<br> и отдельное подключенное устройство.|Пакет обновления можно скачать напрямую<br> из Интернета в Azure Stack.|

> [!IMPORTANT]
> Невозможно переключить поставщик удостоверений без повторного развертывания всего решения Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Службы федерации Active Directory и Graph

Развертывание AD FS позволяет удостоверениям в существующем лесу Active Directory проходить аутентификацию в ресурсах, размещенных в Azure Stack. Этот существующий лес Active Directory требует развертывания AD FS, чтобы можно было создать отношения доверия с федерацией AD FS.

Аутентификация является одной частью применения удостоверения. Для управления доступом на основе ролей (RBAC) в Azure Stack необходимо настроить компонент Graph. При делегировании доступа к ресурсу компонент Graph ищет учетную запись пользователя в существующем лесу Active Directory, используя протокол LDAP.

![Архитектура AD FS Azure Stack](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Существующие службы AD FS представляют собой службу токенов безопасности (STS) учетных записей, которая отправляет утверждения в AD FS Azure Stack (ресурс STS). В Azure Stack служба автоматизации создает отношения доверия с поставщиком утверждений с конечной точкой метаданных для существующих служб AD FS.

В существующих AD FS должны быть настроены отношения доверия с проверяющей стороной. Этот шаг не выполняется службой автоматизации и должен быть выполнен оператором. Конечная точка метаданных Azure Stack описывается в файле AzureStackStampDeploymentInfo.JSON или с помощью привилегированной конечной точки посредством команды `Get-AzureStackInfo`.

Конфигурация отношений доверия с проверяющей стороной также требует настройки правил преобразования утверждений, которые предоставляются корпорацией Майкрософт.

Для настройки Graph необходимо предоставить учетную запись службы с разрешением на чтение в существующей службе Active Directory. Эта учетная запись необходима в качестве входных данных службе автоматизации для реализации сценариев RBAC.

Для выполнения последнего шага для подписки поставщика по умолчанию настраивается новый владелец. Эта учетная запись имеет полный доступ ко всем ресурсам после входа на портал администрирования Azure Stack.

Требования:


|Компонент|Требование|
|---------|---------|
|График|Microsoft Active Directory 2012, Microsoft Active Directory 2012 R2 или Microsoft Active Directory 2016|
|AD FS|Windows Server 2012, Windows Server 2012 R2 или Windows Server 2016|

## <a name="setting-up-graph-integration"></a>Настройка интеграции Graph

Graph поддерживает только интеграцию с отдельным лесом Active Directory. Если существует несколько лесов, только лес, указанный в конфигурации, будет использоваться для получения пользователей и групп.

Необходимо указать следующие сведения в качестве входных данных для параметров службы автоматизации.


|Параметр|ОПИСАНИЕ|Пример|
|---------|---------|---------|
|CustomADGlobalCatalog|Полное доменное имя целевого леса Active Directory<br>для интеграции.|Contoso.com|
|CustomADAdminCredentials|Пользователь с разрешением на чтение LDAP.|ВАШ_ДОМЕН\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Создание учетной записи пользователя в существующей службе Active Directory (необязательно)

При необходимости можно создать учетную запись для службы Graph в существующей службе Active Directory. Выполните этот шаг, если у вас еще нет учетной записи, которую требуется использовать.

1. В существующей службе Active Directory создайте следующую учетную запись пользователя (рекомендация).
   - **Имя пользователя**: graphservice.
   - **Пароль**: используйте надежный пароль.<br>Настройте бессрочный пароль.

   Особые разрешения или членство не требуются.

#### <a name="trigger-automation-to-configure-graph"></a>Активация службы автоматизации для настройки Graph

Для выполнения этой процедуры используйте компьютер в сети центра обработки данных, который может взаимодействовать с привилегированной конечной точкой в Azure Stack.

2. Откройте сеанс Windows PowerShell с повышенными правами (запуск от имени администратора) и подключитесь к IP-адресу привилегированной конечной точки. Используйте учетные данные **CloudAdmin** для аутентификации.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. После подключения к привилегированной конечной точке выполните следующие команды. 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   При появлении запроса укажите данные учетной записи пользователя, которую требуется использовать для службы Graph (например, graphservice). Для командлета Register-DirectoryService нужно указать имя леса и корневой домен в лесу, а не другие домены в лесу.

   > [!IMPORTANT]
   > Дождитесь появления всплывающего элемента для ввода учетных данных (Get-Credential не поддерживается на привилегированной конечной точке) и укажите данные учетной записи службы Graph.

#### <a name="graph-protocols-and-ports"></a>Протоколы и порты Graph

Служба Graph в Azure Stack использует перечисленные ниже протоколы и порты для связи с доступным для записи глобальным сервером каталога (GC) и центром распространения ключей (KDC), который может обрабатывать запросы на вход в целевом лесу Active Directory.

Служба Graph в Azure Stack использует приведенные ниже протоколы и порты для связи с целевой службой Active Directory.

|type|Порт|Протокол|
|---------|---------|---------|
|LDAP|389|TCP или UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Настройка интеграции AD FS путем скачивания метаданных федерации

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:

|Параметр|ОПИСАНИЕ|Пример|
|---------|---------|---------|
|CustomAdfsName|Имя поставщика утверждений. <cr>Так оно отображается на целевой странице AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Ссылка на метаданные федерации:|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Активация службы автоматизации для настройки отношений доверия с поставщиком утверждений в Azure Stack

Для выполнения этой процедуры используйте компьютер, который может взаимодействовать с привилегированной конечной точкой в Azure Stack. Ожидается, что сертификат, используемый **AD FS службы токенов безопасности** учетной записи, является доверенным для Azure Stack.

1. Откройте сеанс Windows PowerShell с повышенными правами и подключитесь к привилегированной конечной точке.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Подключившись к привилегированной конечной точке, выполните следующую команду, указав параметры, подходящие для используемой среды.

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Выполните следующую команду, чтобы обновить владельца подписки поставщика по умолчанию, указав параметры, подходящие для используемой среды.

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Настройка интеграции AD FS путем предоставления файла метаданных федерации

Используйте этот метод, если выполняется одно из следующих условий:

- Цепочка сертификатов для AD FS отличается по сравнению с другими конечными точками в Azure Stack.
- Нет сетевого подключения между экземпляром AD FS Azure Stack и существующим сервером AD FS.

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:


|Параметр|ОПИСАНИЕ|Пример|
|---------|---------|---------|
|CustomAdfsName|Имя поставщика утверждений. Так оно отображается на целевой странице AD FS.|Contoso|
|CustomADFSFederationMetadataFile|Файл метаданных федерации:|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>Создание файла метаданных федерации

Для выполнения следующей процедуры необходимо использовать компьютер с сетевым подключением к существующему развертыванию AD FS, которое становится службой токенов безопасности для учетной записи. Кроме того, нужно установить необходимые сертификаты.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующую команду, указав параметры, подходящие для используемой среды.

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Скопируйте файл метаданных в файловый ресурс, доступный из привилегированной конечной точки.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Активация службы автоматизации для настройки отношений доверия с поставщиком утверждений в Azure Stack

Для выполнения этой процедуры используйте компьютер, который может взаимодействовать с привилегированной конечной точкой в Azure Stack.

1. Откройте сеанс Windows PowerShell с повышенными правами и подключитесь к привилегированной конечной точке.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Подключившись к привилегированной конечной точке, выполните следующую команду, указав параметры, подходящие для используемой среды.

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. Выполните следующую команду, чтобы обновить владельца подписки поставщика по умолчанию, указав параметры, подходящие для используемой среды.

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Настройка проверяющей стороны в существующем развертывании AD FS (службе токенов безопасности для учетной записи)

Корпорация Майкрософт предоставляет сценарий, который настраивает отношения доверия с проверяющей стороной, включая правила преобразования утверждений. Использовать этот сценарий необязательно, можно вручную выполнить необходимые команды.

Можно скачать вспомогательный сценарий из раздела [инструментов Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) на сайте GitHub.

Если вы решили вручную выполнить команды, сделайте следующее.

1. Скопируйте приведенное ниже содержимое в TXT-файл (например, сохраните этот файл как c:\ClaimRules.txt) на экземпляре AD FS или элементе фермы своего центра обработки данных.

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Чтобы включить аутентификацию на основе Windows Forms, откройте сеанс Windows PowerShell от имени привилегированного пользователя и выполните следующую команду.

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. Чтобы добавить отношения доверия с проверяющей стороной, выполните следующую команду Windows PowerShell на экземпляре AD FS или элементе фермы. Обязательно обновите конечную точку AD FS и укажите файл, созданный на шаге 1.

   **Для AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **Для AD FS 2012 и AD FS 2012 R2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Чтобы настроить правила авторизации выдачи при использовании AD FS на основе Windows Server 2012 или Windows Server 2012 R2, необходимо использовать оснастку MMC AD FS.

4. При использовании браузера Internet Explorer или Edge для доступа к Azure Stack необходимо игнорировать привязки токенов. В противном случае попытка входа завершится сбоем. На экземпляре AD FS или элементе фермы выполните следующую команду.

   > [!note]  
   > Эта инструкция неприменима при использовании Windows Server 2012 или 2012 R2 AD FS. Можно пропустить эту команду и продолжать интеграцию.

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. Чтобы использовать маркеры обновления, откройте сеанс Windows PowerShell с повышенными правами и выполните следующие команды.

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Создание имени субъекта-службы

Существует множество сценариев, в которых требуется использовать имя субъекта-службы для проверки аутентификации. Ниже приводятся некоторые примеры:

- использование интерфейса командной строки с развертыванием AD FS в Azure Stack;
- использование пакета управления System Center для Azure Stack при развертывании с AD FS;
- использование поставщиков ресурсов Azure Stack при развертывании с AD FS;
- различные приложения;
- необходимость в неинтерактивном входе.

Дополнительные сведения о создании имени субъекта-службы см. в разделе [Создание субъекта-службы для AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Устранение неполадок

### <a name="configuration-rollback"></a>Откат конфигурации

Если возникла ошибка, которая оставила среду в состоянии, в котором аутентификация невозможна, можно выполнить откат.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие команды.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Затем выполните следующий командлет.

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   После выполнения отката откатываются все изменения конфигурации. Возможна будет только аутентификация с использованием встроенного пользователя **CloudAdmin**.

   > [!IMPORTANT]
   > Необходимо настроить первоначального владельца подписки поставщика по умолчанию.

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Сбор дополнительных журналов

В случае сбоя при выполнении какого-либо из командлетов можно собрать дополнительные журналы, выполнив командлет `Get-Azurestacklogs`.

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие команды.

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Затем выполните следующий командлет.

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>Дополнительная информация

[Интеграция внешнего решения для мониторинга с Azure Stack](azure-stack-integrate-monitor.md)
