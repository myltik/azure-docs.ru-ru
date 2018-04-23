---
title: Выполнение операций в хранилище таблиц Azure с помощью PowerShell | Документация Майкрософт
description: Выполнение операций в хранилище таблиц Azure с помощью PowerShell
services: cosmos-db
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: robinsh
ms.openlocfilehash: 0f6ceaa976263164c468eb573b119dd84c0d2429
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Выполнение операций в хранилище таблиц Azure с помощью Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Хранилище таблиц Azure — хранилище данных NoSQL, которое можно использовать для хранения и запросов огромных наборов структурированных нереляционных данных. Основными компонентами службы являются таблицы, сущности и свойства. Таблица представляет собой коллекцию сущностей. Сущность — это набор свойств. Каждая сущность может иметь до 252 свойств. Все они являются парами "имя — значение". В этой статье предполагается, что вы уже знакомы с понятиями хранилища таблиц Azure. Дополнительные сведения см. в статьях [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) (Общие сведения о модели данных службы таблиц) и [Приступая к работе с хранилищем таблиц Azure с помощью .NET](table-storage-how-to-use-dotnet.md).

В этом практическом руководстве рассматриваются распространенные операции в хранилище таблиц Azure. Вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * Создание таблицы
> * извлечение таблицы;
> * добавление сущностей таблицы;
> * запрос к таблице;
> * удаление сущностей таблицы.
> * Удаление таблицы

В этом практическом руководстве показано, как создать учетную запись службы хранилища Azure в новой группе ресурсов, чтобы вы могли легко удалить ее при необходимости. Вы можете использовать существующую учетную запись хранения.

Для работы с этим руководством требуется модуль Azure PowerShell 4.4.0 или более поздней версии. В окне PowerShell выполните `Get-Module -ListAvailable AzureRM`, чтобы найти версию. Если версия не отображается или нужно установить обновление, ознакомьтесь со статьей [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Установка и настройка Azure PowerShell). 

После установки или обновления Azure PowerShell нужно установить модуль **AzureRmStorageTable**, который содержит команды для управления сущностями. Чтобы установить этот модуль, запустите PowerShell от имени администратора и используйте команду **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Получение списка расположений

Если вы не знаете, какое расположение нужно использовать, можно получить список доступных расположений. Получив список, найдите расположение, которое нужно использовать. В этих примерах используется **eastus**. Сохраните это значение в переменной **location** для использования в будущем.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Сохраните имя группы ресурсов в переменной для будущего использования. В этом примере создается группа ресурсов с именем *pshtablesrg* в регионе *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Создать учетную запись хранения

Создайте стандартную учетную запись хранения общего назначения с локально избыточным хранилищем (LRS) с помощью команды [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Получите контекст учетной записи хранения, определяющий необходимую учетную запись хранения. Действуя в учетной записи хранения, ссылайтесь на контекст, вместо того чтобы многократно предоставлять учетные данные.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Создание таблицы

Чтобы создать таблицу, используйте командлет [New AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable). В этом примере таблица называется `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Извлечение списка таблиц, содержащихся в учетной записи хранения

Извлеките список таблиц, содержащихся в учетной записи хранения, с помощью командлета [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Получение ссылки на определенную таблицу

Чтобы выполнить операции с таблицей, необходима ссылка на конкретную таблицу. Получите ссылку с помощью командлета [Get AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Удаление таблицы

Чтобы удалить таблицу, используйте командлет [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Этот командлет удаляет таблицу, включая все ее данные.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали группу ресурсов и учетную запись хранения в начале этого практического руководства, можно удалить все ресурсы, созданные в этом упражнении, путем удаления группы ресурсов. Эта команда удаляет все ресурсы, которые содержатся в группе, и саму группу ресурсов.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом практическом руководстве описаны распространенные операции в хранилище таблиц Azure, выполняемые с помощью PowerShell, в частности: 

> [!div class="checklist"]
> * Создание таблицы
> * извлечение таблицы;
> * добавление сущностей таблицы;
> * запрос к таблице;
> * удаление сущностей таблицы.
> * Удаление таблицы

Дополнительные сведения см. в следующих статьях:

* [Командлеты PowerShell для службы хранилища](/powershell/module/azurerm.storage#storage)

* [Working with Azure Storage Tables from PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) (Работа с таблицами хранилища Azure из PowerShell)

* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
