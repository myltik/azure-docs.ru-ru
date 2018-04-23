---
title: Выполнение операций в API таблицы Azure Cosmos DB с помощью PowerShell | Документация Майкрософт
description: Как выполнять операции в API таблицы Azure Cosmos DB с помощью PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d40a56ca3c07bfadcf6e24d407b059a39522ca2b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Выполнение операций в API таблицы Azure Cosmos DB с помощью Azure PowerShell 

>[!NOTE]
>API таблицы Azure Cosmos DB предоставляет расширенные функции для хранения таблиц, такие как комплексные возможности глобального распределения, низкие задержки операций чтения и записи, автоматическое вторичное индексирование и выделенная пропускная способность. В большинстве случаев команды PowerShell в этой статье работают как для API таблицы Azure Cosmos DB, так и для хранилища таблиц Azure, однако в этой статье рассматривается API таблицы Azure Cosmos DB. Если вы используете хранилище таблиц Azure, см. статью [Perform Azure Table storage operations with Azure PowerShell](table-storage-how-to-use-powershell.md) (Выполнение операций в хранилище таблиц Azure с помощью Azure PowerShell).
>

API таблицы Azure Cosmos DB позволяет хранить и запрашивать огромные наборы структурированных нереляционных данных. Основными компонентами службы являются таблицы, сущности и свойства. Таблица представляет собой коллекцию сущностей. Сущность — это набор свойств. Каждая сущность может иметь до 252 свойств. Все они являются парами "имя — значение". В этой статье предполагается, что вы уже знакомы с основными понятиями API таблицы Azure Cosmos DB. Дополнительные сведения см. в статье [Знакомство со службой Azure Cosmos DB. API таблицы](table-introduction.md) и [Azure Cosmos DB. Создание приложения .NET с помощью API таблицы](create-table-dotnet.md).

В данной статье рассматриваются распространенные операции в API таблицы. Вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * Создание таблицы
> * извлечение таблицы;
> * добавление сущностей таблицы;
> * запрос к таблице;
> * удаление сущностей таблицы.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством требуется модуль Azure PowerShell 4.4.0 или более поздней версии. В окне PowerShell выполните `Get-Module -ListAvailable AzureRM`, чтобы найти версию. Если версия не отображается или нужно установить обновление, ознакомьтесь со статьей [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Установка и настройка Azure PowerShell). 

После установки или обновления Azure PowerShell нужно установить модуль **AzureRmStorageTable**, который содержит команды для управления сущностями. Чтобы установить этот модуль, запустите PowerShell от имени администратора и используйте команду **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

Затем установите сборки Azure Cosmos DB локально, чтобы использовать эти командлеты PowerShell. Инструкции о том, как это сделать, см. в статье [Azure RM Storage Tables PowerShell module now includes support for Cosmos DB Tables](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/) (Модуль PowerShell таблиц хранилища Azure RM теперь включает поддержку таблиц Cosmos DB).

Чтобы выполнить приведенные ниже упражнения, необходима учетная запись базы данных Azure Cosmos DB. Если у вас ее нет, создайте такую учетную запись с помощью [портала Azure](https://portal.azure.com). Дополнительные сведения о создании учетной записи базы данных см. в разделе [Создание учетной записи базы данных](create-table-dotnet.md#create-a-database-account).

Получите имя учетной записи базы данных и группы ресурсов с портала. Эти значения необходимо добавить в свой сценарий для получения доступа к таблицам. 

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Создание таблицы или получение ссылки на нее

Чтобы создать таблицу или получить ссылку на таблицу, используйте командлет **Get AzureStorageTableTable**. Если вызвать этот командлет с именем несуществующей таблицы, он создаст новую таблицу с этим именем и вернет ссылку на нее. Если таблица существует, он возвращает ссылку на имеющуюся таблицу.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Вы не можете перечислить таблицы в учетной записи Azure Cosmos DB с помощью PowerShell, однако можно войти на портал и просмотреть таблицу. Теперь давайте рассмотрим управление сущностями в таблице.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Удаление таблицы 

PowerShell не поддерживает удаление таблиц из Azure Cosmos DB. Чтобы удалить таблицу, перейдите на [портал Azure](https://azure.portal.com), найдите учетную запись Azure Cosmos DB, которая используется, а затем найдите и удалите таблицу. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали группу ресурсов и учетную запись Azure Cosmos DB в этой группе, можно удалить все ресурсы, созданные в этом упражнении, путем удаления группы ресурсов. Эта команда удаляет все ресурсы, которые содержатся в группе, и саму группу ресурсов.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом практическом руководстве описаны распространенные операции в API таблицы, выполняемые с помощью PowerShell, в частности: 

> [!div class="checklist"]
> * Создание таблицы
> * извлечение таблицы;
> * добавление сущностей таблицы;
> * запрос к таблице;
> * удаление сущностей таблицы.

Дополнительные сведения см. в следующих статьях:

* [Working with Azure Storage Tables from PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) (Работа с таблицами хранилища Azure из PowerShell)

* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
