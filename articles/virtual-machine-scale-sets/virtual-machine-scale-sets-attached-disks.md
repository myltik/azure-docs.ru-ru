---
title: Масштабируемые наборы виртуальных машин Azure и подключенные диски данных | Документация Майкрософт
description: Сведения об использовании дисков данных с масштабируемыми наборами виртуальных машин
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Масштабируемые наборы виртуальных машин Azure и подключенные диски данных
Для развертывания доступного хранилища в [масштабируемых наборах виртуальных машин](/azure/virtual-machine-scale-sets/) Azure поддерживаются экземпляры виртуальных машин с подключенными дисками данных. Вы можете подключить диски данных к создаваемому или существующему масштабируемому набору.

> [!NOTE]
>  При создании масштабируемого набора с подключенными дисками данных для их использования вам необходимо подключать и форматировать диски в виртуальной машине (точно так же, как и для автономных виртуальных машин Azure). Для этого удобно использовать расширение пользовательских скриптов, которое вызывает скрипт, чтобы секционировать и форматировать все диски данных на виртуальной машине. Эти примеры см. в руководствах по использованию [Azure CLI 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) и [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Создание дисков и управление ими в масштабируемом наборе
Дополнительные сведения о том, как создать масштабируемый набор с подключенными дисками данных, подготовить и отформатировать или добавить и удалить диски данных, см. в одном из следующих руководств:

- [Azure CLI 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

В оставшейся части этой статьи приводятся конкретные варианты использования, например использование кластеров Service Fabric, для которых требуются диски с данными, или подключение существующих дисков данных с содержимым к масштабируемому набору.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Создание кластера Service Fabric с подключенными дисками данных
Каждому [типу узла](../service-fabric/service-fabric-cluster-nodetypes.md) в кластере [Service Fabric](/azure/service-fabric) в Azure соответствует масштабируемый набор виртуальных машин.  С помощью шаблона Azure Resource Manager можно подключать диски данных к масштабируемым наборам, которые входят в кластер Service Fabric. В качестве отправной точки можно использовать [существующий шаблон](https://github.com/Azure-Samples/service-fabric-cluster-templates). В шаблоне включите раздел _dataDisks_ в раздел _storageProfile_ ресурсов _Microsoft.Compute/virtualMachineScaleSets_ и разверните шаблон. В следующем примере подключается диск данных размером 128 ГБ:

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


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Добавление предварительно заполненных дисков данных в существующий масштабируемый набор 
> Диски, добавляемые в существующую модель масштабируемого набора, по умолчанию всегда будут пустыми. Это также касается экземпляров, создаваемых в этом наборе. Такое поведение связано с тем, что в определении масштабируемого набора указан пустой диск данных. Чтобы создать предварительно заполненный диск данных для существующего масштабируемого набора, выберите один из двух способов:

* Скопируйте данные из нулевого экземпляра виртуальной машины на диски данных других виртуальных машин, используя пользовательский скрипт.
* Создайте управляемый образ диска ОС и диска данных (с необходимыми данными), а затем создайте масштабируемый набор с этим образом. Таким образом, каждая новая созданная виртуальная машина имеет диск данных, который указан в определении масштабируемого набора. Так как это определение ссылается на образ диска данных с необходимыми данными, то в каждой виртуальной машине с масштабируемым набором присутствуют эти изменения.

> Сведения о способе создания настраиваемого образа см. в статье [Создание управляемого образа универсальной виртуальной машины в Azure](/azure/virtual-machines/windows/capture-image-resource/). 

> Пользователь должен создать нулевой экземпляр виртуальной машины с необходимыми данными, а затем использовать виртуальный жесткий диск в определении образа.


## <a name="additional-notes"></a>Дополнительные замечания
Поддержка Управляемых дисков Azure и дисков данных, подключенных к масштабируемым наборам, доступны в версии API [_30-04-2016-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) или более поздней версии API Microsoft.Compute.

В начальной реализации поддержки подключенных дисков для масштабируемых наборов нельзя подключать или отключать диски данных в отдельных виртуальных машинах масштабируемого набора.

Изначально поддержка портала Azure для подключенных дисков данных в масштабируемых наборах ограничена. В зависимости от требований для управления подключенными дисками можно использовать шаблоны Azure, интерфейс командной строки, PowerShell, пакеты SDK и REST API.


