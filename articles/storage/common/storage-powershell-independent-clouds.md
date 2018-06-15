---
title: Управление службой хранилища в независимых от Azure облаках с помощью Azure PowerShell | Документация Майкрософт
description: Управление службой хранилища в облаках для Китая, Германии и государственных организаций с помощью Azure PowerShell
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: 3eecf8918e6628071e44cc588b1826df68a00f10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186925"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Управление службой хранилища в независимых от Azure облаках с помощью Azure PowerShell

Большинство людей используют общедоступное облако Azure для глобального развертывания Azure. Но есть и независимые развертывания Microsoft Azure для обеспечения автономности и других целей. Такие независимые развертывания называются средами. Ниже приведен список независимых облаков, доступных в настоящее время:

* [Облако Azure для государственных организаций](https://azure.microsoft.com/features/gov/).
* [Облако Azure для Китая (обслуживается 21Vianet в Китае)](http://www.windowsazure.cn/).
* [Облако Azure для Германии](../../germany/germany-welcome.md).

## <a name="using-an-independent-cloud"></a>Использование независимого облака 

Чтобы использовать службу хранилища Azure в одном из независимых облаков, нужно подключиться к нему вместо общедоступного облака Azure. Чтобы использовать одно из независимых облаков Azure вместо общедоступного, выполните следующие инструкции:

* Укажите *среду*, к которой выполняется подключение.
* Определите и используйте доступные регионы.
* Используйте правильный суффикс конечной точки, который отличается от суффикса общедоступного облака Azure.

Для работы с этим руководством требуется модуль Azure PowerShell 4.4.0 или более поздней версии. В окне PowerShell выполните `Get-Module -ListAvailable AzureRM`, чтобы найти версию. Если версия не отображается или необходимо обновить модуль, см. статью об [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Вход в Azure

Запустите командлет [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) , чтобы просмотреть доступные среды Azure:
   
```powershell
Get-AzureRmEnvironment
```

Войдите в учетную запись с доступом к облаку, к которому требуется подключиться, и укажите среду. В этом примере показано, как войти в учетную запись облака Azure для государственных организаций.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

Для доступа к облаку для Китая используйте среду **AzureChinaCloud**. Для доступа к облаку для Германии используйте среду**AzureGermanCloud**.

На этом этапе для создания учетной записи хранения иди другого ресурса вам может потребоваться список расположений в выбранном облаке. Подайте запрос на список с помощью командлета [Get AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

В следующей таблице показаны расположения, доступные в облаке для Германии.

|Расположение | DisplayName |
|----|----|
| germanycentral | Центральная Германия|
| germanynortheast | Северо-восточная Германия | 


## <a name="endpoint-suffix"></a>Суффикс конечной точки

Суффикс конечной точки для каждой из этих сред отличается от суффикса конечной точки общедоступного облака Azure. Например, суффикс конечной точки большого двоичного объекта в общедоступном облаке Azure — **blob.core.windows.net**. В облаке Azure для государственных организаций суффикс конечной точки большого двоичного объекта — **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Получение конечной точки с помощью командлета Get-AzureRMEnvironment 

Получите суффикс конечной точки с помощью командлета [Get AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). Конечная точка — это свойство среды *StorageEndpointSuffix*. В приведенном ниже фрагменте кода показано, как это сделать. Все эти команды возвращают такие значения, как core.cloudapp.net или core.cloudapi.de и т. д. Добавьте это значение в службу хранилища для доступа к ней. Например, конечная точка queue.core.cloudapi.de получит доступ к службе очередей в облаке для Германии.

Этот фрагмент кода позволяет получить все среды и суффикс конечной точки для каждой из них.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Эта команда возвращает следующие результаты:

| ИМЯ| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud; | core.chinacloudapi.cn|
| AzureCloud; | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment; | core.usgovcloudapi.net |

Чтобы получить все свойства для указанной среды, вызовите **Get AzureRmEnvironment** и укажите имя облака. Этот фрагмент кода позволяет получить список свойств. Найдите **StorageEndpointSuffix** в списке. Следующий пример касается облака для Германии.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

Результаты должны выглядеть следующим образом:

|Имя свойства|Значение|
|----|----|
| ИМЯ | AzureGermanCloud |
| EnableAdfsAuthentication | Ложь |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Чтобы получить только свойство суффикса конечной точки хранилища, обратитесь к определенному облаку и подайте запрос только на это свойство.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Вы получите следующую информацию:

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Получение конечной точки из учетной записи хранения

Для получения конечных точек также можно просмотреть свойства учетной записи хранения. Это целесообразно, если вы уже используете учетную запись хранения в скрипте PowerShell. Вы можете просто получить нужную точку. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Для учетной записи хранения в облаке для государственных организаций возвращаются следующие значения: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>После настройки среды

Теперь вы можете использовать тот же скрипт PowerShell, с помощью которого управляли учетными записями хранения и осуществляли доступ к плоскости данных, как описано в статье [Использование Azure PowerShell со службой хранилища Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали новую группу ресурсов и учетную запись хранения для этого примера, удалите их, удалив группу ресурсов. При этом будут также удалены все ресурсы, содержащиеся в группе. В этом случае происходит удаление созданной учетной записи хранения и самой группы ресурсов.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

* [Использование учетных данных для входа в разных сеансах PowerShell](/powershell/azure/context-persistence).
* [Azure Government storage](../../azure-government/documentation-government-services-storage.md) (Хранилище Azure для государственных организаций).
* [Azure Government developer guide](../../azure-government/documentation-government-developer-guide.md) (Руководство для разработчиков Microsoft Azure для государственных организаций).
* [Заметки для разработчика для Azure в китайских приложениях](https://msdn.microsoft.com/library/azure/dn578439.aspx).
* [Документация по Azure для Германии](../../germany/germany-welcome.md).
