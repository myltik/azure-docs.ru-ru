<properties
	pageTitle="Доменные службы Azure AD: включение синхронизации паролей | Microsoft Azure"
	description="Приступая к работе с доменными службами Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/07/2016"
	ms.author="maheshu"/>

# Доменные службы Azure AD *(предварительная версия)* — включение синхронизации паролей в доменные служб Azure AD

## Задача 5. Включение синхронизации паролей для доменных служб AAD для синхронизированного клиента Azure AD
Включив доменные службы Azure AD для своего каталога Azure AD, вы также сможете включить синхронизацию паролей для этих служб. После этого пользователи смогут входить в домен, используя свои корпоративные учетные данные.

Этапы настройки отличаются в зависимости от того, есть ли у вашей организации исключительно облачный каталог Azure AD или для нее настроена синхронизация локального каталога с помощью Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Исключительно облачный каталог Azure AD](active-directory-ds-getting-started-password-sync.md)
- [Синхронизированный каталог Azure AD](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Синхронизированные клиенты. Включение синхронизации хэшей учетных данных NTLM и Kerberos с Azure AD
Клиент Azure AD для вашей организации настраивается на синхронизацию с локальным каталогом с помощью Azure AD Connect. По умолчанию Azure AD Connect не синхронизирует хэши учетных данных NTLM и Kerberos с Azure AD. Чтобы воспользоваться доменными службами Azure AD, нужно настроить Azure AD Connect для синхронизации хэшей учетных данных, необходимых для проверки подлинности NTLM и Kerberos. Для включения синхронизации требуемых хэшей учетных данных с клиентом Azure AD необходимо выполнить следующие действия.

#### Установка или обновление Azure AD Connect

Необходимо установить последнюю рекомендуемую версию службы Azure AD Connect на компьютере, присоединенном к домену. Если служба Azure AD Connect уже установлена, необходимо обновить установку до последней версии. Убедитесь, что вы используете последнюю версию Azure AD Connect. Так вы избежите возникновения известных проблем и ошибок, которые уже могли быть исправлены в обновлении.

**[Скачать Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Рекомендуемая версия: **1.1.281.0** (опубликована 7 сентября 2016 г.).

  > [AZURE.WARNING] Последняя рекомендуемая версия Azure AD Connect НЕОБХОДИМА для синхронизации устаревших учетных данных (требуются для проверки подлинности NTLM и Kerberos) с вашим клиентом Azure AD. В предыдущих выпусках Azure AD Connect и в устаревшей версии DirSync эта возможность недоступна.

Инструкции по установке Azure AD Connect доступны в следующей статье: [Приступая к работе с Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### Принудительная полная синхронизация паролей в Azure AD

Для включения принудительной полной синхронизации паролей вместе с синхронизацией хэшей паролей всех локальных пользователей выполните описанный ниже сценарий PowerShell в каждом лесу AD. Этот сценарий включает синхронизацию хэшей учетных данных, необходимых для проверки подлинности NTLM или Kerberos, с вашим клиентом Azure AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

В зависимости от размера каталога (число пользователей, групп и т. д.) синхронизация хэшей учетных данных с Azure AD может занять некоторое время. Пароли можно будет использовать в управляемом домене доменных служб AD Azure вскоре после синхронизации хэшей учетных данных с Azure AD.


<br>

## Похожий контент

- [Доменные службы Azure AD (предварительная версия) — приступая к работе](active-directory-ds-getting-started-password-sync.md)

- [Administer an Azure AD Domain Services managed domain (Администрирование управляемого домена доменных служб Azure AD)](active-directory-ds-admin-guide-administer-domain.md)

- [Join a Windows virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-windows-vm.md) (Присоединение виртуальной машины Windows к управляемому домену доменных служб Azure AD)

- [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain](active-directory-ds-admin-guide-join-rhel-linux-vm.md) (Присоединение виртуальной машины Red Hat Enterprise Linux к управляемому домену доменных служб Azure AD)

<!---HONumber=AcomDC_0914_2016-->