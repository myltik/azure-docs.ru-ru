---
title: Использование Azure PowerShell со службой хранилища Azure | Документация Майкрософт
description: Узнайте, как использовать командлеты Azure PowerShell для службы хранилища Azure.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: rogarana
ms.openlocfilehash: 951b69877718c5da3c165c24c297906a1ad9a976
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652507"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Использование Azure PowerShell со службой хранилища Azure

PowerShell используется для создания ресурсов Azure и управления ими с помощью командной строки PowerShell или сценариев. Для службы хранилища Azure эти командлеты делятся на две категории — уровня управления и уровня данных. Командлеты уровня управления используются для операций управления учетной записью хранения: создания и удаления учетных записей хранения, установки свойств, смены ключей доступа и т. д. Командлеты уровня данных используются для операций управления данными *в* учетной записи хранения: отправки больших двоичных объектов, создания общих файловых ресурсов и добавления сообщений в очередь.

В этом практическом руководстве описаны стандартные операции с использованием командлетов уровня управления для учетных записей хранения. Вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * Вывод списка учетных записей хранения.
> * Получение ссылки на имеющуюся учетную запись хранения.
> * Создание учетной записи хранения 
> * Настройка свойств учетной записи хранения.
> * Получение и повторное создание ключей доступа.
> * Защита доступа к учетной записи хранения 
> * Включение Аналитики Службы хранилища

В этой статье приводятся ссылки на несколько других статей об использовании PowerShell со службой хранилища Azure. В них описывается, как включать Аналитику Службы хранилища и обращаться к ней, как использовать командлеты плоскости данных и как обращаться к независимым облакам Azure для Китая и Германии, а также к облаку для государственных организаций.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для работы с этим упражнением требуется модуль Azure PowerShell версии 4.4 и выше. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Для этого упражнения команды можно вводить в обычное окно PowerShell или в редактор [интегрированной среды сценариев Windows PowerShell (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-), а затем протестировать одну или несколько из них во время выполнения этих примеров. Вы можете выделить строки, которые необходимо выполнить, и щелкнуть Run Selected (Запустить выделенные), чтобы выполнить эти команды.

Дополнительные сведения об учетных записях хранения см. в статьях [Введение в хранилище Microsoft Azure](storage-introduction.md) и [Об учетных записях хранения Azure](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Вывод списка учетных записей хранения в подписке

Выполните командлет [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount), чтобы получить список учетных записей хранения в текущей подписке. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Получение ссылки на учетную запись хранения

Затем потребуется получить ссылку на учетную запись хранения. Вы можете создать учетную запись хранения или получить ссылку на имеющуюся. В следующих разделах продемонстрированы оба способа. 

### <a name="use-an-existing-storage-account"></a>Использование имеющейся учетной записи хранения 

Для извлечения имеющейся учетной записи хранения вам понадобится имя группы ресурсов и учетной записи хранения. Задайте переменные для этих двух полей, а затем используйте командлет [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount). 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Теперь у вас есть переменная $storageAccount, указывающая на имеющуюся учетную запись хранения.

### <a name="create-a-storage-account"></a>Создание учетной записи хранения 

В следующем сценарии показано, как создать учетную запись хранения общего назначения с помощью команды [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). После создания учетной записи извлеките ее контекст, который можно использовать в последующих командах вместо указания аутентификации при каждом вызове.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

В этом сценарии используются следующие командлеты PowerShell. 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) — для получения списка допустимых расположений. В этом примере в качестве расположения используется `eastus`.

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) — для создания группы ресурсов. Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Наша группа ресурсов называется `teststoragerg`. 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) — для создания фактической учетной записи хранения. В этом примере используется `testpshstorage`.

Имя SKU определяет тип репликации учетной записи хранения (например, локально избыточное хранилище). Дополнительные сведения о репликации см. в статье [Репликация службы хранилища Azure](storage-redundancy.md).

> [!IMPORTANT]
> Имя для учетной записи хранения является уникальным в пределах Azure и должно содержать только строчные буквы. Соглашения об именовании и ограничениях см. в статье [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).
> 

Теперь у вас есть учетная запись хранения и ссылка на нее. 

## <a name="manage-the-storage-account"></a>Управление учетной записью хранения

В следующем разделе показаны некоторые команды, которые можно использовать для управления новой или имеющейся учетной записью хранения, имея на нее ссылку.

### <a name="storage-account-properties"></a>Свойства учетной записи хранения

Чтобы изменить параметры для учетной записи хранения, используйте командлет [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Несмотря на то что невозможно изменить расположение учетной записи хранения или группу ресурсов, в которой она находится, вы можете изменить множество других свойств. Ниже перечислены некоторые свойства, которые можно изменить с помощью PowerShell.

* **Личный домен**, присвоенный учетной записи хранения.

* **Теги**, присвоенные учетной записи хранения. Теги часто используются для классификации ресурсов в целях выставления счетов.

* **SKU** — это параметр репликации для учетной записи хранения (как, например, LRS для локально избыточного хранилища). Например, можно изменить Standard\_LRS на Standard\_GRS или Standard\_RAGRS. Обратите внимание, что невозможно изменить Standard\_ZRS или Premium\_LRS на другие SKU или заменить другие SKU этими.

* **Уровень доступа** для учетных записей хранилища BLOB-объектов. Для уровня доступа задано значение **Горячий** или **Холодный**, что позволяет свести к минимуму затраты, выбрав уровень доступа, соответствующий использованию учетной записи хранения. Дополнительные сведения см. в статье [Хранилище BLOB-объектов Azure: "горячий", "холодный" и архивный (предварительная версия) уровни](../blobs/storage-blob-storage-tiers.md).

* Разрешение только трафика по протоколу HTTPS. 

### <a name="manage-the-access-keys"></a>Управление ключами доступа

Учетная запись хранилища Azure предоставляется с двумя ключами учетной записи. Чтобы получить ключи, используйте командлет [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). В этом примере извлекается первый ключ. Получите другой ключ, использовав `Value[1]` вместо `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Чтобы повторно создать ключ, используйте [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Чтобы повторно создать другой ключ, используйте в качестве имени ключа значение `key2`, а не `key1`.

Повторно создайте один из ключей, а затем извлеките его снова, чтобы увидеть новое значение.

> [!NOTE] 
> Необходимо тщательно спланировать повторное создание ключа для рабочей учетной записи хранения. Повторное создание одного или обоих ключей приведет к тому, что с помощью повторно созданного ключа невозможно будет получить доступ к любому приложению. Дополнительные сведения см. в разделе [Повторное создание ключей доступа к хранилищу](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Удаление учетной записи хранения 

Чтобы удалить учетную запись хранения, используйте командлет [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> При удалении учетной записи хранения также удаляются все ресурсы, которые хранятся в ней. Если учетная запись удалена случайно, немедленно обратитесь в службу поддержки и создайте запрос на восстановление учетной записи хранения. Восстановление данных не гарантируется, но в некоторых случаях это возможно. Не создавайте учетную запись хранения с тем же именем, что и старая, до тех пор, пока не будет решена проблема по запросу в службе поддержки. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Защита учетной записи хранения с помощью виртуальных сетей и брандмауэров

По умолчанию все учетные записи хранения можно получить с помощью сети с подключением к Интернету. Однако можно настроить сетевые правила, чтобы разрешить приложениям из определенных виртуальных сетей получать доступ к учетной записи хранения. Дополнительные сведения см. в статье [Настройка брандмауэров службы хранилища Azure и виртуальных сетей (предварительная версия)](storage-network-security.md). 

В данной статье показано, как управлять этими параметрами, используя следующие командлеты PowerShell:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule);
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset);
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule).

## <a name="use-storage-analytics"></a>Использование Аналитики Службы хранилища  

[Решение "Аналитика Службы хранилища Azure"](storage-analytics.md) состоит из [метрик](/rest/api/storageservices/about-storage-analytics-metrics) и [ведения журнала](/rest/api/storageservices/about-storage-analytics-logging) аналитики службы хранилища. 

**Метрики аналитики службы хранилища** используются при сборе метрик для учетных записей хранения Azure, которые можно применять для отслеживания работоспособности учетной записи хранения. Метрики можно включить для больших двоичных объектов, файлов, таблиц и очередей.

**Ведение журналов аналитики службы хранилища** производится на стороне сервера и позволяет записывать сведения об успешных и неудачных запросах в вашей учетной записи хранения. Эти журналы позволяют просмотреть подробные сведения об операциях чтения, записи и удаления для таблиц, очередей и BLOB-объектов, а также причины неудачных запросов. Ведение журнала службы недоступно для службы файлов Azure.

Вы можете настроить мониторинг с помощью [портала Azure](https://portal.azure.com), PowerShell или программно, использовав клиентскую библиотеку хранилища. 

> [!NOTE]
> Минутную аналитику можно включить с помощью PowerShell. Эта возможность недоступна на портале.
>

* Сведения о том, как включить и просмотреть метрики службы хранилища с помощью PowerShell, представлены в разделе [Включение метрик с помощью PowerShell](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Сведения о том, как включить и получить журнал хранилища данных с помощью PowerShell, см. в статьях [How to enable Storage Logging using PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) (Включение ведения журнала хранилища с помощью PowerShell) и [Finding your Storage Logging log data](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data) (Поиск данных журнала хранилища).

* Подробнее об использовании метрик хранилища и ведения журнала для устранения неполадок хранилища см. в статье [Мониторинг, диагностика и устранение неполадок службы хранилища Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Управление данными в учетной записи хранения

Вы узнали о том, как управлять учетной записью хранения с помощью PowerShell. Изучите следующие статьи, в которых описано, как получить доступ к объектам данных в учетной записи хранения.

* [Выполнение операций в хранилище BLOB-объектов Azure с помощью Azure PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Как использовать PowerShell для управления службой файлов Azure](../files/storage-how-to-use-files-powershell.md)
* [Выполнение операций хранилища очередей Azure с помощью Azure PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Выполнение операций в хранилище таблиц Azure с помощью PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

API таблицы Azure Cosmos DB предоставляет расширенные функции для хранения таблиц, такие как комплексные возможности глобального распределения, низкие задержки операций чтения и записи, автоматическое вторичное индексирование и выделенная пропускная способность. 

* Дополнительные сведения см. в статье [Знакомство со службой Azure Cosmos DB. API таблицы](../../cosmos-db/table-introduction.md). 
* Чтобы узнать, как использовать PowerShell для выполнения операций API таблицы Azure Cosmos DB, см. статью [Perform Azure Cosmos DB Table API operations with Azure PowerShell](../../cosmos-db/table-powershell.md) (Выполнение операций в API таблицы Azure Cosmos DB с помощью Azure PowerShell).

## <a name="independent-cloud-deployments-of-azure"></a>Независимые облачные развертывания Azure

Большинство людей используют общедоступное облако Azure для глобального развертывания Azure. Но есть и независимые развертывания Microsoft Azure для обеспечения автономности и других целей. Такие независимые развертывания называются средами. Эти доступные среды включают:

* [Облако Azure для государственных организаций](https://azure.microsoft.com/features/gov/)
* [Облако Azure для Китая (обслуживается 21Vianet в Китае)](http://www.windowsazure.cn/).
* [Облако Azure для Германии](../../germany/germany-welcome.md)

Дополнительные сведения о доступе к эти облакам и связанным службам хранилища с помощью PowerShell см. в руководстве по [управлению службой хранилища в независимых облаках Azure с помощью PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали группу ресурсов и учетную запись хранения для этого примера, удалите их, удалив группу ресурсов. При этом будут также удалены все ресурсы, содержащиеся в группе. В этом случае происходит удаление созданной учетной записи хранения и самой группы ресурсов.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Дополнительная информация

В этом практическом руководстве описаны стандартные операции с использованием командлетов уровня управления для учетных записей хранения. Вы научились выполнять следующие задачи: 

> [!div class="checklist"]
> * Вывод списка учетных записей хранения.
> * Получение ссылки на имеющуюся учетную запись хранения.
> * Создание учетной записи хранения 
> * Настройка свойств учетной записи хранения.
> * Получение и повторное создание ключей доступа.
> * Защита доступа к учетной записи хранения 
> * Включение Аналитики Службы хранилища

В этой статье также приводятся ссылки на несколько других статей. В них описывается, как управлять объектами данных, как включать Аналитику Службы хранилища и как обращаться к независимым облакам Azure для Китая и Германии, а также к облаку для государственных организаций. Ниже приведены дополнительные связанные статьи и ресурсы:

* [AzureRM.Storage](/powershell/module/AzureRM.Storage/)
* [Azure.Storage](/powershell/module/azure.storage/)
* [Справочник по Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)
