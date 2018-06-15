---
title: Предоставление разрешения на доступ к хранилищу ключей Azure нескольким приложениям | Документация Майкрософт
description: Узнайте, как нескольким приложениям предоставить разрешение на доступ к хранилищу ключей
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: ddeaf184138bd48d324799ddb45248b0a0ee8eeb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30174987"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Предоставление разрешения на доступ к хранилищу ключей нескольким приложениям

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>В. У меня много приложений (свыше 16), которым требуется доступ к хранилищу ключей. Так как хранилище ключей разрешает доступ только 16 приложениям, как получить доступ остальным приложениям?

Политика контроля доступа хранилища ключей поддерживает не более 16 приложений. Однако вы можете создать группу безопасности Azure Active Directory. Добавьте все связанные субъекты-службы в эту группу безопасности, а затем предоставьте доступ данной группе безопасности к хранилищу ключей.

Необходимые компоненты для установки:
* [Установка модуля PowerShell V2 для Azure Active Directory](https://www.powershellgallery.com/packages/AzureAD).
* [Установка Azure PowerShell](/powershell/azure/overview).
* Чтобы выполнить следующие команды, необходимо получить разрешение на создание и изменение групп в клиенте Azure Active Directory. Если у вас нет разрешений, обратитесь к администратору Azure Active Directory.

Теперь в PowerShell выполните следующие команды.

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Если группе приложений необходимо предоставить другой набор разрешений, создайте для таких приложений отдельную группу безопасности Azure Active Directory.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статье [Защита хранилища ключей](key-vault-secure-your-key-vault.md).
