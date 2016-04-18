<properties 
	pageTitle="Блокировка ресурсов с помощью диспетчера ресурсов | Microsoft Azure" 
	description="Запрет на обновление или удаление определенных ресурсов для пользователей путем применения ограничения для всех пользователей и ролей." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2016" 
	ms.author="tomfitz"/>

# Блокировка ресурсов с помощью диспетчера ресурсов Azure

Администратору в некоторых сценариях нужно будет заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи организации не могли случайно удалить критически важные ресурсы. После блокирования авторизованные пользователи смогут читать и изменять ресурсы, но не смогут удалить их.

Блокировки отличаются от контроля доступа на основе ролей при назначении пользователям разрешений для выполнения определенных действий. Сведения о настройке разрешений для пользователей и ролей см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md). В отличие от контроля доступа на основе ролей блокировки управления используются для применения ограничения для всех пользователей и ролей. Кроме того, блокировки обычно применяются только в течение ограниченного времени.

При применении блокировки к родительской области все дочерние ресурсы наследуют ту же блокировку.

## Кто может создавать и удалять блокировки в вашей организации

Для создания или удаления блокировок управления необходимо иметь доступ к действиям **Microsoft.Authorization/*** или **Microsoft.Authorization/locks/***. Из встроенных ролей эти действия предоставляются только **Владельцу** и **Администратору доступа пользователей**. Дополнительные сведения о назначении контроля доступа см. в статье [Контроль доступа на основе ролей Azure](./active-directory/role-based-access-control-configure.md).

## Создание блокировки в шаблоне

В приведенном ниже примере показан шаблон, который создает блокировку для учетной записи хранения. Учетная запись хранения, к которой применяется блокировка, указывается в качестве параметра. Имя блокировки составляется из имени ресурса с **/Microsoft.Authorization/** и имени самой блокировки (в этом случае — **myLock**).

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

## Создание блокировки с помощью REST API

Вы можете заблокировать развернутые ресурсы с помощью [REST API для блокировок управления](https://msdn.microsoft.com/library/azure/mt204563.aspx). REST API позволяет создавать и удалять блокировки и получать информацию о существующих блокировках.

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

Примеры см. в статье [REST API для блокировок управления](https://msdn.microsoft.com/library/azure/mt204563.aspx).

## Создание блокировки с помощью Azure PowerShell

Вы можете заблокировать развернутые ресурсы с помощью Azure PowerShell, используя командлет **New-AzureRmResourceLock**, как показано ниже.

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

В Azure PowerShell доступны другие команды для рабочих блокировок, такие как **Set-AzureRmResourceLock** для обновления блокировки и **Remove-AzureRmResourceLock** для удаления блокировки.

## Дальнейшие действия

- Дополнительные сведения о работе с блокировкой ресурсов см. в статье [Блокировка ресурсов Azure](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx).
- Дополнительные сведения о логической организации ресурсов см. в статье [Использование тегов для организации ресурсов](resource-group-using-tags.md).
- Для изменения группы, в которой находится ресурс, см. статью [Перемещение ресурсов в новую группу ресурсов](resource-group-move-resources.md).
- Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Дополнительные сведения см. в статье [Применение политик для управления ресурсами и контроля доступа](resource-manager-policy.md).

<!---HONumber=AcomDC_0406_2016-->