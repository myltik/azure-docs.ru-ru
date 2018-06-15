---
title: Изменение идентификатора клиента хранилища ключей после перемещения подписки | Документация Майкрософт
description: Узнайте, как изменить идентификатор клиента хранилища ключей после перемещения подписки в другой клиент.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: 91c042ebda9e7a2eba4835abc079568e1ed2e537
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725512"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Изменение идентификатора клиента хранилища ключей после перемещения подписки
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>В. Моя подписка перемещена из клиента А в клиент Б. Как изменить идентификатор клиента имеющегося хранилища ключей и настроить правильные списки ACL для субъектов в клиенте Б?
При создании хранилища ключей в подписке оно автоматически привязывается к идентификатору клиента Azure Active Directory по умолчанию для этой подписки. Все записи политики доступа также привязываются к этому идентификатору клиента. При перемещении подписки Azure из клиента A в клиент Б существующие хранилища ключей недоступны для субъектов (пользователей и приложений) в клиенте Б. Вот как устранить эту проблему.

* Измените идентификатор клиента, связанный со всеми существующими хранилищами ключей в этой подписке, для клиента Б.
* Удалите все существующие записи политики доступа.
* Добавьте новые записи политики доступа, связанные с клиентом Б.

Например, при наличии хранилища ключей myvault в подписке, которая была перенесена из клиента A в клиент Б, необходимо изменить идентификатор клиента для этого хранилища ключей и удалить старые политики доступа.

<pre>
Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Так как это хранилище находилось в клиенте A перед перемещением, исходное значение **$vault.Properties.TenantId** является клиентом А, в то время как значение **(Get-AzureRmContext).Tenant.TenantId** — это клиент Б.

Теперь, когда хранилище связано с правильным идентификатором клиента и старые записи политики доступа удалены, можно настроить новые записи политики доступа с помощью командлета [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Дополнительная информация
Если у вас возникли вопросы о хранилище ключей Azure, посетите [форумы хранилища ключей Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

