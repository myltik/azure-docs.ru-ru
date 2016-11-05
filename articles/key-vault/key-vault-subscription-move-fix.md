---
title: Изменение идентификатора клиента хранилища ключей после перемещения подписки | Microsoft Docs
description: Узнайте, как изменить идентификатор клиента хранилища ключей после перемещения подписки в другой клиент.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# Изменение идентификатора клиента хранилища ключей после перемещения подписки
### В. Моя подписка перемещена из клиента А в клиент Б. Как изменить идентификатор клиента имеющегося хранилища ключей и настроить правильные списки ACL для субъектов в клиенте Б?
При создании хранилища ключей в подписке оно автоматически привязывается к идентификатору клиента Azure Active Directory по умолчанию для этой подписки. Все записи политики доступа также привязываются к этому идентификатору клиента. При перемещении подписки Azure из клиента A в клиент Б имеющиеся хранилища ключей недоступны для субъектов (пользователей и приложений) в клиенте Б. Чтобы устранить эту проблему, необходимо:

* изменить идентификатор клиента, связанный со всеми имеющимися хранилищами ключей в этой подписке, для клиента Б;
* удалить все имеющиеся записи политики доступа;
* добавить новые записи политики доступа, связанные с клиентом Б.

Например, при наличии хранилища ключей myvault в подписке, которая была перенесена из клиента A в клиент Б, необходимо изменить идентификатор клиента для этого хранилища ключей и удалить старые политики доступа.

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Так как это хранилище находилось в клиенте A перед перемещением, исходное значение **$vault.Properties.TenantId** является клиентом А, в то время как значение **(Get-AzureRmContext).Tenant.TenantId** — это клиент Б.

Теперь, когда хранилище связано с правильным идентификатором клиента и старые записи политики доступа удалены, можно настроить новые записи политики доступа с помощью командлета [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## Дальнейшие действия
* Если у вас возникли вопросы о хранилище ключей, посетите [форумы хранилища ключей Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

<!---HONumber=AcomDC_0921_2016-->