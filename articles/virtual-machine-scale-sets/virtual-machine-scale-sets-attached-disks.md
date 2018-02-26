---
title: "Масштабируемые наборы виртуальных машин Azure и подключенные диски данных | Документация Майкрософт"
description: "Сведения об использовании дисков данных с масштабируемыми наборами виртуальных машин"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: 52ea7e35b941d5b1e45f39203757e4a3644cc9a5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Масштабируемые наборы виртуальных машин Azure и подключенные диски данных
[Масштабируемые наборы виртуальных машин](/azure/virtual-machine-scale-sets/) Azure теперь поддерживают виртуальные машины с подключенными дисками данных. Диски данных можно определить в профиле хранилища для масштабируемых наборов, созданных с использованием управляемых дисков Azure. Ранее единственными непосредственно подключаемыми хранилищами, доступными для виртуальных машин в масштабируемых наборах, были временные диски и диск операционной системы.

> [!NOTE]
>  При создании масштабируемого набора с определенными подключенными дисками данных для их использования вам по-прежнему необходимо подключать и форматировать диски в виртуальной машине (точно так же, как и для автономных виртуальных машин Azure). Для этого удобно использовать расширение пользовательских скриптов, которое вызывает стандартный скрипт, чтобы секционировать и форматировать все диски данных на виртуальной машине.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Создание масштабируемого набора с подключенными дисками данных
Простым способом создания масштабируемого набора с подключенными дисками является использование команды [az vmss create](/cli/azure/vmss#az_vmss_create). В следующем примере создается группа ресурсов Azure и масштабируемый набор, состоящий из 10 виртуальных машин Ubuntu, каждая из которых содержит 2 подключенных диска данных объемом 50 и 100 ГБ соответственно.

```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Команда [az vmss create](/cli/azure/vmss#az_vmss_create) задаст определенные значения конфигурации по умолчанию, если их не указать. Чтобы узнать, как параметры можно переопределить, используйте следующую команду:

```bash
az vmss create --help
```

Другой способ создания масштабируемого набора с подключенными дисками данных — это определение данного набора в шаблоне Azure Resource Manager, добавление раздела _dataDisks_ в профиле _storageProfile_ и развертывание шаблона. Диски данных объемом 50 и 100 ГБ (из предыдущего примера) будут определены, как показано в следующем примере шаблона:

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```

Вы можете увидеть полный, готовый к развертыванию пример шаблона масштабируемого набора с подключенным диском здесь: [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Создание кластера Service Fabric с подключенными дисками данных
Каждому [типу узла](../service-fabric/service-fabric-cluster-nodetypes.md) в кластере [Service Fabric](/azure/service-fabric) в Azure соответствует масштабируемый набор виртуальных машин.  С помощью шаблона Azure Resource Manager можно присоединять диски данных к масштабируемым наборам, составляющим кластер Service Fabric. В качестве отправной точки можно использовать [существующий шаблон](https://github.com/Azure-Samples/service-fabric-cluster-templates). В шаблоне включите раздел _dataDisks_ в раздел _storageProfile_ ресурсов _Microsoft.Compute/virtualMachineScaleSets_ и разверните шаблон. В следующем примере подключается диск данных размером 128 ГБ:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

При развертывании кластера можно автоматически секционировать, форматировать и подключать диски данных.  Добавьте расширение пользовательского скрипта в подраздел _extensionProfile_ раздела _virtualMachineProfile_ масштабируемых наборов.

Чтобы автоматически подготовить диски данных в кластере Windows, добавьте следующие строки:

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Чтобы автоматически подготовить диски данных в кластере Linux, добавьте следующие строки:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Добавление диска данных к имеющемуся масштабируемому набору
> [!NOTE]
>  Диски данных можно подключать только для масштабируемого набора, который был создан с помощью [управляемых дисков Azure](./virtual-machine-scale-sets-managed-disks.md).

Вы можете добавить диск данных в масштабируемый набор виртуальных машин, используя команду Azure CLI _az vmss disk attach_. Убедитесь, что вы указали логический номер устройства (LUN), который еще не используется. В следующем примере с использованием интерфейса командной строки добавляется диск размером 50 ГБ в LUN 3.

```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

В следующем примере с использованием PowerShell добавляется диск размером 50 ГБ в LUN 3.

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> Виртуальные машины разных размеров имеют различные ограничения на количество подключенных дисков, которые они поддерживают. Прежде чем добавлять новый диск, просмотрите [характеристики размеров виртуальных машин](../virtual-machines/windows/sizes.md).

Еще один способ добавить диск — добавить новую запись в свойство _dataDisks_ в _storageProfile_ определения масштабируемого набора и применить это изменение. Чтобы проверить это, найдите имеющееся определение масштабируемого набора в [обозревателе ресурсов Azure](https://resources.azure.com/). Выберите _Изменить_ и добавьте новый диск в список дисков данных, как показано в примере ниже.

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

Затем выберите _PUT_ для применения изменений к масштабируемому набору. Этот пример будет работать, если вы используете размер виртуальной машины, который поддерживает более двух подключенных дисков данных.

> [!NOTE]
> При внесении изменений в определение масштабируемого набора, таких как добавление или удаление диска данных, они применяются ко всем недавно созданным виртуальным машинам. Что же касается имеющихся виртуальных машин, изменения будут применены, только если свойству _upgradePolicy_ присвоено значение Automatic. Если задано значение Manual, необходимо вручную применить новую модель к имеющимся виртуальным машинам. Это можно сделать на портале, с помощью команды PowerShell _Update-AzureRmVmssInstance_ или с помощью команды CLI _az vmss update-instances_.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Добавление предварительно заполненных дисков данных в существующий масштабируемый набор 
> Диски, добавляемые в существующую модель масштабируемого набора, по умолчанию всегда будут пустыми. Это также касается экземпляров, создаваемых в этом наборе. Такое поведение связано с тем, что в определении масштабируемого набора указан пустой диск данных. Чтобы создать предварительно заполненный диск данных для существующего масштабируемого набора, выберите один из двух способов:

* Скопируйте данные из нулевого экземпляра виртуальной машины на диски данных других виртуальных машин, используя пользовательский скрипт.
* Создайте управляемый образ диска ОС и диска данных (с необходимыми данными), а затем создайте масштабируемый набор с этим образом. Таким образом, каждая новая созданная виртуальная машина имеет диск данных, который указан в определении масштабируемого набора. Так как это определение ссылается на образ диска данных с необходимыми данными, то в каждой виртуальной машине с масштабируемым набором присутствуют эти изменения.

> Сведения о способе создания настраиваемого образа см. в статье [Создание управляемого образа универсальной виртуальной машины в Azure](/azure/virtual-machines/windows/capture-image-resource/). 

> Пользователь должен создать нулевой экземпляр виртуальной машины с необходимыми данными, а затем использовать виртуальный жесткий диск в определении образа.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Удаление диска данных из масштабируемого набора
Вы можете удалить диск данных в масштабируемом наборе виртуальных машин, используя команду Azure CLI _az vmss disk detach_. Например, следующая команда удаляет диск, определенный в LUN 2:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
Еще один способ удалить диск в масштабируемом наборе — удалить запись из свойства _dataDisks_ в _storageProfile_ и применить изменения. 

## <a name="additional-notes"></a>Дополнительные замечания
Поддержка Управляемых дисков Azure и дисков данных, подключенных к масштабируемым наборам, доступны в версии API [_30-04-2016-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) или более поздней версии API Microsoft.Compute.

В начальной реализации поддержки подключенных дисков для масштабируемых наборов нельзя подключать или отключать диски данных в отдельных виртуальных машинах масштабируемого набора.

Изначально поддержка портала Azure для подключенных дисков данных в масштабируемых наборах ограничена. В зависимости от требований для управления подключенными дисками можно использовать шаблоны Azure, интерфейс командной строки, PowerShell, пакеты SDK и REST API.


