---
title: "Создания масштабирование Azure, которая использует доступности зоны (Предварительная версия) | Документы Microsoft"
description: "Сведения о создании набора масштабирования виртуальной машины Azure, использующие зон доступности для повышения избыточности от сбоев"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Создание набора масштабирования виртуальных машин, использующий доступности зоны (Предварительная версия)
Для защиты вашей наборы масштабирования виртуальной машины от сбоев на уровне центра обработки данных, можно создать в зоне доступности наборе масштабирования. Azure регионах, которые поддерживают доступность зон иметь по крайней мере три отдельные зоны, каждая из которых собственные независимые power источника, сети и охлаждения. Дополнительные сведения см. в статье [Общие сведения о зонах доступности в Azure (предварительная версия)](../availability-zones/az-overview.md).

Чтобы использовать доступность зон, ваш набор масштабирования должны быть созданы в [поддерживается регион Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Можно создать набор масштабирования, использующий доступности зоны с одним из следующих методов:

- [портал Azure](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Шаблоны диспетчера ресурсов Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Использование портала Azure
При создании набора масштабирования, использующий зону доступности является таким же, как описано в [Приступая к работе статьи](virtual-machine-scale-sets-create-portal.md). При выборе поддерживаемом регионе Azure, можно создать наборе в одной из доступных зон, масштабирования, как показано в следующем примере:

![Создайте в одной зоне доступности наборе масштабирования](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Набор масштабирования и вспомогательные ресурсы, такие как подсистемы балансировки нагрузки Azure и общедоступный IP-адрес, создаются в одной зоне, указанной вами.


## <a name="use-the-azure-cli-20"></a>Использование Azure CLI 2.0
При создании набора масштабирования, использующий зону доступности является таким же, как описано в [Приступая к работе статьи](virtual-machine-scale-sets-create-cli.md). Чтобы использовать доступность зон, необходимо создать шкалу в поддерживаемом регионе Azure. Добавить `--zones` параметр [создать az vmss](/cli/azure/vmss#az_vmss_create) команду и укажите часовой пояс, используемый (например зоны *1*, *2*, или *3*). В следующем примере создается набор именованных масштабирования *myScaleSet* в зоне *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Он занимает несколько минут для создания и настройки все шкалы заданные в зоне, указать ресурсы и виртуальные машины.


## <a name="use-azure-powershell"></a>Использование Azure PowerShell
При создании набора масштабирования, использующий зону доступности является таким же, как описано в [Приступая к работе статьи](virtual-machine-scale-sets-create-powershell.md). Чтобы использовать доступность зон, необходимо создать шкалу в поддерживаемом регионе Azure. Добавить `-Zone` параметр [New AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) команду и укажите часовой пояс, используемый (таких как зона *1*, *2*, или *3*). В следующем примере создается файл конфигурации набора масштабирования с именем *vmssConfig* в *восточная часть США 2* зоны *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Чтобы создать набор фактический масштаб, выполните дополнительные действия, как описано в [Приступая к работе статьи](virtual-machine-scale-sets-create-powershell.md).


## <a name="use-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure
При создании набора масштабирования, использующий зону доступности является таким же, как описано в статье началу работы для [Linux](virtual-machine-scale-sets-create-template-linux.md) или [Windows](virtual-machine-scale-sets-create-template-windows.md). Чтобы использовать доступность зон, необходимо создать шкалу в поддерживаемом регионе Azure. Добавить `zones` свойства *Microsoft.Compute/virtualMachineScaleSets* ресурсов введите шаблон и укажите часовой пояс, используемый (таких как зона *1*, *2*, или *3*). В следующем примере создается наборе именованный масштабирования Linux *myScaleSet* в *восточная часть США 2* зоны *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Чтобы создать набор фактический масштаб, выполните дополнительные действия, как описано в статье началу работы для [Linux](virtual-machine-scale-sets-create-template-linux.md) или [Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>Дальнейшие действия
После создания наборе в зоне доступности масштабирования рассказывается, как [развертывать приложения на виртуальной машине масштабировать наборов](virtual-machine-scale-sets-deploy-app.md) или [использовании автомасштабирования с наборы масштабирования виртуальных машин](virtual-machine-scale-sets-autoscale-overview.md).
