---
title: "Запрещение изменений в критических ресурсах Azure | Документация Майкрософт"
description: "Запрет на обновление или удаление определенных ресурсов для пользователей путем применения ограничения для всех пользователей и ролей."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: de8137a69ccc2028a7dcbff491573f36640bdc50


---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Блокировка ресурсов для предотвращения непредвиденных изменений 
Администратору иногда требуется заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи в организации не могли случайно удалить или изменить критически важные ресурсы. Можно установить уровень блокировки **CanNotDelete** или **ReadOnly**. 

* **CanNotDelete** означает, что авторизованные пользователи смогут читать и изменять ресурс, но не смогут его удалить. 
* **ReadOnly** означает, что авторизованные пользователи смогут читать ресурс, но не смогут его удалить или обновить. Применение этой блокировки подобно ограничению авторизованных пользователей с помощью разрешений, предоставляемых для роли **Читатель**. 

Блокировки Resource Manager применяются только к операциям, выполняемым в плоскости управления, которая включает в себя операции, передаваемые на `https://management.azure.com`. Эти блокировки не мешают ресурсам осуществлять свои собственные функции. Ограничиваются изменения ресурсов, но не операции с ними. Например, блокировка ReadOnly в Базе данных SQL не позволит вам удалить или изменить базу данных, но она не мешает создавать, обновлять и удалять данные в базе данных. Транзакции с данными разрешены, так как эти операции не передаются на `https://management.azure.com`.

Применение уровня блокировки **ReadOnly** (Только чтение) может привести к непредвиденным результатам, так как некоторые операции, кажущиеся операциями чтения, на самом деле требуют дополнительных действий. Например, установка уровня блокировки **ReadOnly** для учетной записи хранения не позволит всем пользователям получить список ключей. Операция отображения списка ключей обрабатывается с помощью запроса POST, поскольку возвращаемые ключи могут быть доступны для операций записи. Еще один пример: установка уровня блокировки **ReadOnly** для ресурса службы приложений не позволит обозревателю сервера Visual Studio отображать файлы для ресурса, так как для их взаимодействия требуется доступ на запись.

В отличие от управления доступом на основе ролей блокировки управления используются для применения ограничения для всех пользователей и ролей. Сведения о настройке разрешений для пользователей и ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../active-directory/role-based-access-control-configure.md).

При применении блокировки к родительской области все ресурсы в этой области наследуют ту же блокировку. Даже ресурсы, добавляемые позже, наследуют блокировку от родительского ресурса. Приоритет имеет наиболее строгая блокировка.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Кто может создавать и удалять блокировки в вашей организации
Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей эти действия предоставляются только **владельцу** и **администратору доступа пользователей**.

## <a name="creating-a-lock-through-the-portal"></a>Создание блокировки с помощью портала
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="creating-a-lock-in-a-template"></a>Создание блокировки в шаблоне
В приведенном ниже примере показан шаблон, который создает блокировку для учетной записи хранения. Учетная запись хранения, к которой применяется блокировка, указывается в качестве параметра. Имя блокировки состоит из имени ресурса с **/Microsoft.Authorization/** и имени самой блокировки (в этом случае — **myLock**).

Используемый тип зависит от типа ресурса. Тип для хранилища — Microsoft.Storage/storageaccounts/providers/locks.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## <a name="creating-a-lock-with-rest-api"></a>Создание блокировки с помощью REST API
Вы можете заблокировать развернутые ресурсы с помощью [REST API для блокировок управления](https://docs.microsoft.com/rest/api/resources/managementlocks). REST API позволяет создавать и удалять блокировки и получать информацию о существующих блокировках.

Чтобы создать блокировку, выполните следующую команду:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Областью может быть подписка, группа ресурсов или ресурс. Вы можете назначить блокировке любое имя. В качестве версии API (api-version) используйте значение **2015-01-01**.

В запросе включите объект JSON, который задает свойства блокировки.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 


## <a name="creating-a-lock-with-azure-powershell"></a>Создание блокировки с помощью Azure PowerShell
Вы можете заблокировать развернутые ресурсы с помощью Azure PowerShell, используя командлет **New-AzureRmResourceLock** , как показано в примере ниже.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

В Azure PowerShell доступны другие команды для рабочих блокировок, такие как **Set-AzureRmResourceLock** для обновления блокировки и **Remove-AzureRmResourceLock** для удаления блокировки.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о работе с блокировкой ресурсов см. в статье [Блокировка ресурсов Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx).
* Дополнительные сведения о логической организации ресурсов см. в статье [Использование тегов для организации ресурсов](resource-group-using-tags.md).
* Изменение группы, в которой находится ресурс, описано в статье [Перемещение ресурсов в новую группу ресурсов](resource-group-move-resources.md).
* Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).




<!--HONumber=Jan17_HO4-->


