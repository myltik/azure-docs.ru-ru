---
title: "Управление решениями Azure с помощью PowerShell | Документация Майкрософт"
description: "Воспользуйтесь Azure PowerShell и Resource Manager для управления ресурсами."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: d1f4b7806f72dc2632ba981781097b19e89d07f3


---
# <a name="manage-resources-with-azure-powershell-and-resource-manager"></a>Управление ресурсами с помощью Azure PowerShell и Resource Manager
> [!div class="op_single_selector"]
> * [Портал](resource-group-portal.md)
> * [Интерфейс командной строки Azure](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [ИНТЕРФЕЙС REST API](resource-manager-rest-api.md)
>
>

В этом разделе вы узнаете, как управлять решениями с помощью Azure PowerShell и Azure Resource Manager. Если вы не знакомы с Resource Manager, то см. статью [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md). Этот раздел посвящен задачам управления. Вы сможете выполнять следующие задачи:

1. Создание группы ресурсов
2. Добавление ресурса в группу ресурсов
3. Добавление тега к ресурсу
4. Запрос ресурсов на основе имен или значений тегов
5. Применение и удаление блокировки ресурса
6. Создание шаблона Resource Manager из группы ресурсов
7. Удаление группы ресурсов

## <a name="get-started-with-azure-powershell"></a>Начало работы с Azure PowerShell

Если вы еще не установили Azure PowerShell, то см. статью [Get started with Azure PowerShell cmdlets](/powershell/azureps-cmdlets-docs) (Приступая к работе с командлетами Azure PowerShell).

Если вы уже установили Azure PowerShell, но давно не выполняли обновление, то рекомендуется установить последнюю версию. Обновить версию можно тем же методом, что применялся для ее установки. Например, если вы использовали установщик веб-платформы, то запустите его снова и выберите параметр "Обновить".

Для проверки версии модуля ресурсов Azure воспользуйтесь следующим командлетом:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Этот раздел был обновлен для версии 3.3.0. Если у вас установлена более ранняя версия, то выполняемые действия могут отличаться от шагов, описанных в этом разделе. Документация по командлетам, используемым в этой версии, доступна в [модуле AzureRM.Resources](/en-us/powershell/resourcemanager/azurerm.resources/v3.3.0/azurerm.resources).

## <a name="log-in-to-your-azure-account"></a>Вход в учетную запись Azure
Прежде чем начать работу над решением, необходимо войти в учетную запись.

Чтобы войти в учетную запись Azure, используйте командлет **Add-AzureRmAccount**.

```powershell
Add-AzureRmAccount
```

Командлет запрашивает учетные данные входа для вашей учетной записи Azure. После выполнения входа он загружает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

Командлет возвращает сведения об учетной записи и подписке, которые используются для выполнения задач.

```powershell
Environment           : AzureCloud
Account               : example@contoso.com
TenantId              : {guid}
SubscriptionId        : {guid}
SubscriptionName      : Example Subscription One
CurrentStorageAccount :

```

При наличии нескольких подписок можно переключаться с одной подписки на другую. Для начала давайте просмотрим все подписки для учетной записи.

```powershell
Get-AzureRmSubscription
```

Отобразятся включенные и отключенные подписки.

```powershell
SubscriptionName : Example Subscription One
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Two
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Enabled

SubscriptionName : Example Subscription Three
SubscriptionId   : {guid}
TenantId         : {guid}
State            : Disabled
```

Чтобы переключиться на другую подписку, укажите имя подписки с помощью командлета **Set-AzureRmContext**.

```powershell
Set-AzureRmContext -SubscriptionName "Example Subscription Two"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Прежде чем развертывать ресурсы в подписке, необходимо создать группу ресурсов, которая будет их содержать.

Чтобы создать группу ресурсов, используйте командлет **New-AzureRmResourceGroup** . В команде используются параметр **Name** для указания имени группы ресурсов и параметр **Location** для указания ее расположения.

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location "South Central US"
```

Выходные данные имеют следующий формат:

```powershell
ResourceGroupName : TestRG1
Location          : southcentralus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1
```

Чтобы получить сведения о группе ресурсов, используйте следующий командлет:

```powershell
Get-AzureRmResourceGroup -ResourceGroupName TestRG1
```

Чтобы просмотреть все группы ресурсов в подписке, не указывайте имя:

```powershell
Get-AzureRmResourceGroup
```

## <a name="add-resources-to-a-resource-group"></a>Добавление ресурсов в группу ресурсов
Чтобы добавить ресурс в группу ресурсов, можно использовать командлет **New-AzureRmResource** или командлет для конкретного типа создаваемого ресурса (например **New-AzureRmStorageAccount**). Возможно, проще использовать командлет для конкретного типа ресурса, так как он включает в себя параметры для свойств, которые необходимы для нового ресурса. Чтобы использовать командлет **New-AzureRmResource**, необходимо знать все свойства и ввести их без запроса.

Однако добавление ресурса с помощью командлетов может привести к путанице в будущем, потому что новый ресурс не существует в шаблоне Resource Manager. Корпорация Майкрософт рекомендует определять инфраструктуру решения Azure в шаблоне Resource Manager. Шаблоны обеспечивают надежность развертывания и позволяют развернуть решение повторно. В этом разделе не показан процесс развертывания в подписке шаблона Resource Manager. Эти сведения см. в статье [Deploy resources with Resource Manager templates and Azure PowerShell](resource-group-template-deploy.md) (Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell). В этом примере учетная запись хранения создается с помощью командлета PowerShell, но затем создается шаблон из группы ресурсов.

Следующий командлет создает учетную запись хранения. Вместо имени, использованного в примере, укажите уникальное имя для учетной записи хранения. Это имя должно содержать от 3 до 24 знаков и состоять только из цифр и букв нижнего регистра. При использовании имени из примера возникнет ошибка, так как это имя уже используется.

```powershell
New-AzureRmStorageAccount -ResourceGroupName TestRG1 -AccountName mystoragename -Type "Standard_LRS" -Location "South Central US"
```

Чтобы получить сведения об этом ресурсе, используйте следующий командлет:

```powershell
Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1
```

## <a name="add-a-tag"></a>Добавление тега

Теги позволяют организовать ресурсы в соответствии с различными свойствами. Например, в разных группах ресурсов могут находиться ресурсы, относящиеся к одному отделу. Чтобы пометить ресурсы как относящиеся к одной категории, к ним можно применить тег отдела и значение. Также можно пометить, в какой среде используется ресурс — рабочей или тестовой. В этом примере теги применяются только к одному ресурсу, но в вашей среде, скорее всего, имеет смысл применить теги ко всем ресурсам.

Следующий командлет применяет к учетной записи хранения два тега:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
 ```

Теги обновляются как один объект. Для добавления тега к ресурсу, который уже содержит теги, сначала получите сведения о существующих тегах. Добавьте новый тег в объект, содержащий существующие теги, а затем повторно примените к ресурсу все теги.

```powershell
$tags = (Get-AzureRmResource -ResourceName mystoragename -ResourceGroupName TestRG1).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName mystoragename -ResourceGroupName TestRG1 -ResourceType Microsoft.Storage/storageAccounts
```

## <a name="search-for-resources"></a>Поиск ресурсов

Используйте командлет **Find-AzureRmResource**, чтобы получить сведения о ресурсах для разных условий поиска.

* Чтобы найти ресурс по имени, укажите параметр **ResourceNameContains**:

  ```powershell
  Find-AzureRmResource -ResourceNameContains mystoragename
  ```

* Чтобы получить сведения о всех ресурсах в группе ресурсов, укажите параметр **ResourceGroupNameContains**:

  ```powershell
  Find-AzureRmResource -ResourceGroupNameContains TestRG1
  ```

* Для получения сведений о всех ресурсах с именем и значением тега, укажите параметры **TagName** и **TagValue**:

  ```powershell
  Find-AzureRmResource -TagName Dept -TagValue IT
  ```

* Для всех ресурсов с определенным типом ресурса укажите параметр **ResourceType**:

  ```powershell
  Find-AzureRmResource -ResourceType Microsoft.Storage/storageAccounts
  ```

## <a name="lock-a-resource"></a>Блокировка ресурса

Чтобы гарантировать, что критически важный ресурс не будет случайно удален или изменен, примените к нему блокировку ресурса. Можно указать параметр **CanNotDelete** или **ReadOnly**.

Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей эти действия предоставляются только владельцу и администратору доступа пользователей.

Чтобы применить блокировку, используйте следующий командлет:

```powershell
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Заблокированный ресурс в предыдущем примере невозможно удалить, пока не будет снята блокировка. Для снятия блокировки используйте следующий командлет:

```powershell
Remove-AzureRmResourceLock -LockName LockStorage -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
```

Дополнительные сведения об установке блокировок см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

## <a name="export-resource-manager-template"></a>Экспорт шаблона Resource Manager
Для существующей группы ресурсов (развернутой с помощью PowerShell или других средств, например через портал) можно просмотреть шаблон Resource Manager. Экспорт шаблона обеспечивает два преимущества:

1. Последующие развертывания решения можно с легкостью автоматизировать, так как в шаблоне определены все компоненты инфраструктуры.
2. Вы можете ознакомиться с синтаксисом шаблона, просмотрев представление JSON решения.

> [!NOTE]
> Сейчас доступна только предварительная версия функции экспорта шаблона, которая поддерживается не для всех типов ресурсов. При попытке экспорта шаблона может появиться сообщение о том, что некоторые ресурсы не экспортированы. При необходимости эти ресурсы можно определить вручную после скачивания шаблона.
>
>

Чтобы просмотреть шаблон для группы ресурсов, выполните командлет **Export-AzureRmResourceGroup** .

```powershell
Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
```

Существует много вариантов и сценариев для экспорта шаблона Resource Manager. Дополнительные сведения см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).

## <a name="remove-resources-or-resource-group"></a>Удаление ресурсов или группы ресурсов
Ресурс или группу ресурсов можно удалить. При удалении группы ресурсов также удаляются все ресурсы, входящие в эту группу.

* Чтобы удалить ресурс из группы ресурсов, используйте командлет **Remove-AzureRmResource** . Этот командлет удаляет ресурс, но не удаляет группу ресурсов.

  ```powershell
  Remove-AzureRmResource -ResourceName mystoragename -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName TestRG1
  ```

* Чтобы удалить группу ресурсов со всем ее содержимым, используйте командлет **Remove-AzureRmResourceGroup**.

  ```powershell
  Remove-AzureRmResourceGroup -Name TestRG1
  ```

В обоих командлетах требуется подтвердить, что вы хотите удалить ресурс или группу ресурсов. В случае успешного выполнения операции командлет возвращает значение **True**.

## <a name="run-resource-manager-scripts-with-azure-automation"></a>Выполнение сценариев Resource Manager со службой автоматизации Azure

В этом разделе показано, как выполнять базовые операции с ресурсами, используя Azure PowerShell. Для расширенного управления обычно требуется создать сценарий, который затем применяется при необходимости или по расписанию. [Служба автоматизации Azure](../automation/automation-intro.md) предоставляет возможность автоматизации часто используемых сценариев, которые управляют решениями Azure.

Ниже приведены статьи, в которых показано использование службы автоматизации Azure, Resource Manager и PowerShell для эффективного выполнения задач управления.

- Сведения о создании модулей Runbook см. в статье [Мой первый модуль Runbook PowerShell](../automation/automation-first-runbook-textual-powershell.md).
- Сведения о работе с галереями сценариев см. в статье [Коллекции модулей Runbook и других модулей для службы автоматизации Azure](../automation/automation-runbook-gallery.md).
- Сведения о модулях Runbook, запускающих и останавливающих виртуальные машины, см. в статье [Сценарий службы автоматизации Azure: создание расписания запуска и завершения работы виртуальной машины Azure с помощью тегов в формате JSON](../automation/automation-scenario-start-stop-vm-wjson-tags.md).
- Сведения о модулях Runbook, запускающих и останавливающих виртуальные машины в нерабочее время, см. в статье [Решение для запуска и остановки виртуальных машин в нерабочее время [предварительная версия] в службе автоматизации](../automation/automation-solution-vm-management.md).

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о создании шаблонов Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Сведения о развертывании шаблонов см. в статье [Развертывание приложения с использованием шаблона Azure Resource Manager](resource-group-template-deploy.md).
* Существующие ресурсы можно переместить в новую группу ресурсов. Примеры см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).




<!--HONumber=Jan17_HO4-->


