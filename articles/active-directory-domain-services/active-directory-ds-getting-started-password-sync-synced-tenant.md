---
title: "Доменные службы Azure AD. Включение синхронизации паролей | Документация Майкрософт"
description: "Приступая к работе с доменными службами Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/30/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 947ea3c9d789ecf5a754001aafcda6f8bcd41047
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017


---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Включение синхронизации паролей с доменными службами Azure AD
Выполняя предыдущие задачи, вы включили доменные службы Azure Active Directory для клиента Azure Active Directory (Azure AD). Следующая задача — включить синхронизацию необходимых хэшей учетных данных, чтобы проверить подлинность NTLM и Kerberos в доменных службах Azure AD. Когда синхронизация учетных данных настроена, пользователи могут входить в управляемый домен с помощью учетных данных организации.

Этапы настройки различаются для облачных учетных записей пользователей и учетных записей пользователей, синхронизированных из локального каталога с помощью Azure AD Connect. Если клиент Azure AD содержит и пользователей в облаке, и пользователей из локального каталога AD, необходимо выполнить оба этапа.

<br>

> [!div class="op_single_selector"]
> * **Учетные записи пользователей в облаке.** [Синхронизация паролей учетных записей пользователей в облаке с управляемым доменом](active-directory-ds-getting-started-password-sync.md)
> * **Локальные учетные записи пользователей.** [Синхронизация паролей учетных записей пользователей, синхронизированных из локального каталога AD, с управляемым доменом](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Задача 5. Включение синхронизации паролей учетных записей пользователей, синхронизированных из локального каталога AD, с управляемым доменом
Клиент Azure AD для вашей организации настраивается на синхронизацию с локальным каталогом с помощью Azure AD Connect. По умолчанию Azure AD Connect не синхронизирует хэши учетных данных NTLM и Kerberos с Azure AD. Чтобы воспользоваться доменными службами Azure AD, нужно настроить Azure AD Connect для синхронизации хэшей учетных данных, необходимых для проверки подлинности NTLM и Kerberos. Приведенные ниже шаги позволяют включить синхронизацию требуемых хэшей учетных данных из локального каталога с клиентом Azure AD.

> [!NOTE]
> Если ваша организация использует учетные записи пользователей, синхронизированные из локального каталога, необходимо включить синхронизацию хэшей NTLM и Kerberos. Это позволит использовать управляемый домен. Синхронизированная учетная запись пользователя — это учетная запись, созданная в локальном каталоге и синхронизированная с клиентом Azure AD с помощью Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Установка или обновление Azure AD Connect
Установите последнюю рекомендуемую версию службы Azure AD Connect на компьютере, присоединенном к домену. Если служба Azure AD Connect уже установлена, необходимо обновить установку до последней версии. Убедитесь, что вы используете последнюю версию Azure AD Connect. Так вы избежите возникновения известных проблем и ошибок, которые уже могли быть исправлены в обновлении.

**[Загрузка Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Рекомендуемая версия: **1.1.553.0** (опубликована 27 июня 2017 г.).

> [!WARNING]
> Последняя рекомендуемая версия Azure AD Connect НЕОБХОДИМА для синхронизации устаревших учетных данных (требуются для проверки подлинности NTLM и Kerberos) с вашим клиентом Azure AD. В предыдущих выпусках Azure AD Connect и в устаревшей версии DirSync эта возможность недоступна.
>
>

Инструкции по установке Azure AD Connect доступны в следующей статье: [Приступая к работе с Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Включение синхронизации хэшей учетных данных NTLM и Kerberos с Azure AD
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

## <a name="related-content"></a>Похожий контент
* [Доменные службы Azure AD (предварительная версия) — приступая к работе](active-directory-ds-getting-started-password-sync.md)
* [Administer an Azure AD Domain Services managed domain (Администрирование управляемого домена доменных служб Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [Join a Windows virtual machine to an Azure AD Domain Services managed domain (Присоединение виртуальной машины Windows к управляемому домену доменных служб Azure AD)](active-directory-ds-admin-guide-join-windows-vm.md)
* [Join a Red Hat Enterprise Linux virtual machine to an Azure AD Domain Services managed domain (Присоединение виртуальной машины Red Hat Enterprise Linux к управляемому домену доменных служб Azure AD)](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

