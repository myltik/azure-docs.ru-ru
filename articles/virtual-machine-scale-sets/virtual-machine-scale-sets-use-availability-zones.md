---
title: Создание масштабируемого набора Azure, который использует зоны доступности (предварительная версия) | Документация Майкрософт
description: Узнайте, как создавать масштабируемые наборы виртуальных машин Azure, которые используют зоны доступности для повышения избыточности и минимизации простоев
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 8b497af8bc7e3060e184dd6a029b23ccb2d2bbfb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Создание масштабируемого набора Azure, который использует зоны доступности (предварительная версия)
Чтобы предотвратить сбои масштабируемых наборов виртуальных машин на уровне центра обработки данных, можно создать масштабируемые наборы в зонах доступности. Регионы Azure, которые поддерживают зоны доступности, имеют по крайней мере три отдельные зоны, для каждой из которых предусмотрены собственные независимые источники питания, сети и системы охлаждения. Дополнительные сведения см. в статье [Общие сведения о зонах доступности в Azure (предварительная версия)](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Однозонные масштабируемые наборы и масштабируемые наборы, избыточные в пределах зоны
При развертывании масштабируемого набора виртуальных машин можно использовать одну или несколько зон доступности в регионе.

Когда вы создаете масштабируемый набор в одной зоне, вы определяете, в какой зоне задействованы все экземпляры виртуальных машин, а управление масштабируемым набором и его автоматическое масштабирование осуществляется только в этой зоне. Масштабируемый набор, избыточный в пределах зоны, позволяет создать единый масштабируемый набор в рамках нескольких зон. При создании экземпляров виртуальных машин по умолчанию они будут равномерно распределяется в пределах зон. Если прерывание происходит в одной из зон, масштабируемый набор не масштабируется автоматически для увеличения емкости. Лучше всего настроить правила автоматического масштабирования на основе использования ЦП или памяти. Правила автоматического масштабирования позволят масштабируемому набору реагировать на потерю экземпляров виртуальных машин в этой зоне путем развертывания новых экземпляров в остальных рабочих зонах.

Чтобы использовать зоны доступности, масштабируемый набор должен быть создан в [поддерживаемом регионе Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones). Необходима также [регистрация в предварительной версии зон доступности](http://aka.ms/azenroll). Вы можете создать масштабируемый набор, использующий зоны доступности, с помощью одного из следующих методов:

- [портал Azure](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Шаблоны диспетчера ресурсов Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Использование портала Azure
Процесс создания масштабируемого набора, использующего зону доступности, описан в статье [Создание масштабируемого набора виртуальных машин с помощью Azure PowerShell](quick-create-portal.md). Убедитесь, что вы зарегистрированы [в предварительной версии зон доступности](http://aka.ms/azenroll). При выборе поддерживаемого региона Azure можно создать масштабируемый набор в одной из доступных зон, как показано в следующем примере:

![Создание масштабируемого набора в одной зоне доступности](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Масштабируемый набор и вспомогательные ресурсы, такие как подсистема балансировки нагрузки Azure и общедоступный IP-адрес, создаются в одной зоне, указанной вами.


## <a name="use-the-azure-cli-20"></a>Использование Azure CLI 2.0
Процесс создания масштабируемого набора, использующего зону доступности, описан в статье [Создание масштабируемого набора виртуальных машин с помощью Azure PowerShell](quick-create-cli.md). Чтобы использовать зоны доступности, необходимо создать масштабируемый набор в поддерживаемом регионе Azure и [зарегистрироваться в предварительной версии зон доступности](http://aka.ms/azenroll).

Добавьте параметр `--zones` в команду [az vmss create](/cli/azure/vmss#az_vmss_create) и укажите, какую зону использовать (например, зону *1*, *2* или *3*). В следующем примере создается однозонный масштабируемый набор *myScaleSet* в зоне *1*:

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
Полный пример однозонного масштабируемого набора и сетевых ресурсов см. в этом [примере сценария CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.).

### <a name="zone-redundant-scale-set"></a>Масштабируемый набор, избыточный в пределах зоны
Чтобы создать масштабируемый набор, избыточный в пределах зоны, используйте общедоступный IP-адрес и подсистему балансировки нагрузки с номером SKU *Стандартный*. Для повышения избыточности в рамках номера SKU *Стандартный* создаются сетевые ресурсы, избыточные в пределах зоны. Дополнительные сведения см. в статье [Azure Load Balancer Standard overview (preview)](../load-balancer/load-balancer-standard-overview.md) (Обзор Azure Load Balancer уровня "Стандартный" (предварительная версия)). При первом создании масштабируемого набора или подсистемы балансировки нагрузки, избыточной в пределах зоны, необходимо выполнить следующие действия, чтобы зарегистрировать учетную запись для использования этих компонентов предварительной версии.

1. Зарегистрируйте свою учетную запись для использования масштабируемого набора, избыточного в пределах зоны, и сетевых функций с помощью команды [az feature register](/cli/azure/feature#az_feature_register) следующим образом:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. Процесс регистрации компонентов может занять несколько минут. Состояние операции можно проверить, выполнив команду [az feature show](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    В следующем примере показано состояние компонента как *Зарегистрировано*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. После *регистрации* масштабируемого набора, избыточного в пределах зоны, и сетевых ресурсов повторно зарегистрируйте поставщики *вычислений* и *сети* с помощью команды [az provider register](/cli/azure/provider#az_provider_register) следующим образом:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Чтобы создать масштабируемый набор, избыточный в пределах зоны, укажите несколько зон с помощью параметра `--zones`. В следующем примере создается масштабируемый набор, избыточный в пределах зоны, с именем *myScaleSet* в зонах *1,2 и 3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора в указанных зонах занимает несколько минут. Полный пример масштабируемого набора, избыточного в пределах зоны, и сетевых ресурсов см. в этом [примере сценария CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh).


## <a name="use-azure-powershell"></a>Использование Azure PowerShell
Процесс создания масштабируемого набора, использующего зону доступности, описан в статье [Создание масштабируемого набора виртуальных машин с помощью Azure PowerShell](quick-create-powershell.md). Чтобы использовать зоны доступности, необходимо создать масштабируемый набор в поддерживаемом регионе Azure и [зарегистрироваться в предварительной версии зон доступности](http://aka.ms/azenroll). Добавьте параметр `-Zone` в команду [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) и укажите требуемую зону (например, *1*, *2* или *3*). 

В следующем примере создается файл конфигурации однозонного масштабируемого набора с именем *vmssConfig* в зоне *1* региона *Восточная часть США 2*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Полный пример однозонного масштабируемого набора и сетевых ресурсов см. в этом [примере сценария PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1).

### <a name="zone-redundant-scale-set"></a>Масштабируемый набор, избыточный в пределах зоны
Чтобы создать масштабируемый набор, избыточный в пределах зоны, используйте общедоступный IP-адрес и подсистему балансировки нагрузки с номером SKU *Стандартный*. Для повышения избыточности в рамках номера SKU *Стандартный* создаются сетевые ресурсы, избыточные в пределах зоны. Дополнительные сведения см. в статье [Azure Load Balancer Standard overview (preview)](../load-balancer/load-balancer-standard-overview.md) (Обзор Azure Load Balancer уровня "Стандартный" (предварительная версия)). При первом создании масштабируемого набора или подсистемы балансировки нагрузки, избыточной в пределах зоны, необходимо выполнить следующие действия, чтобы зарегистрировать учетную запись для использования этих компонентов предварительной версии.

1. Зарегистрируйте учетную запись для использования масштабируемого набора, избыточного в пределах зоны, и сетевых функций с помощью команды [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) следующим образом:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. Процесс регистрации компонентов может занять несколько минут. Состояние операции можно проверить с помощью команды [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    В следующем примере показано состояние компонента как *Зарегистрировано*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. После того, как состояние масштабируемого набора, избыточного в пределах зоны, и сетевых ресурсов изменится на *Зарегистрировано*, повторно зарегистрируйте поставщики *вычислений* и *сети* с помощью команды[Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) следующим образом:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Чтобы создать масштабируемый набор, избыточный в пределах зоны, укажите несколько зон с помощью параметра `-Zone`. В следующем примере создается файл конфигурации масштабируемого набора, избыточного в пределах зоны, с именем *myScaleSet* в зонах *1, 2 и 3* региона *Восточная часть США 2*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

При создании общедоступного IP-адреса с помощью команды [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) или подсистемы балансировки нагрузки с помощью команды [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), укажите *-SKU "Standard"* для создания сетевых ресурсов, избыточных в пределах зоны. Необходимо также создать группы безопасности сети и правила, чтобы разрешить любой трафик. Дополнительные сведения см. в статье [Azure Load Balancer Standard overview (preview)](../load-balancer/load-balancer-standard-overview.md) (Обзор Azure Load Balancer уровня "Стандартный" (предварительная версия)).

Полный пример масштабируемого набора, избыточного в пределах зоны, и сетевых ресурсов см. в этом [примере сценария PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1).


## <a name="use-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure
Создание масштабируемого набора, использующего зону доступности, описано в статьях о начале работы для [Linux](quick-create-template-linux.md) или [Windows](quick-create-template-windows.md). Чтобы использовать зоны доступности, необходимо создать масштабируемый набор в поддерживаемом регионе Azure и [зарегистрироваться в предварительной версии зон доступности](http://aka.ms/azenroll). Добавьте свойство `zones` в тип ресурса *Microsoft.Compute/virtualMachineScaleSets* в шаблон и укажите требуемую зону (например, *1*, *2* или *3*).

В следующем примере создается однозонный масштабируемый набор Linux с именем *myScaleSet* в зоне *1* региона *Восточная часть США 2*:

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

Полный пример однозонного масштабируемого набора и сетевых ресурсов см. в этом [примере шаблона Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json).

### <a name="zone-redundant-scale-set"></a>Масштабируемый набор, избыточный в пределах зоны
Чтобы создать масштабируемый набор, избыточный в пределах зоны, укажите несколько значений в свойстве `zones` типа ресурса *Microsoft.Compute/virtualMachineScaleSets*. В следующем примере создается масштабируемый набор, избыточный в пределах зоны, с именем *myScaleSet* в зонах *1,2 и 3* региона *Восточная часть США 2*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

При создании общедоступного IP-адреса или подсистемы балансировки нагрузки укажите свойство *"sku": { "name": "Standard" }"* для создания сетевых ресурсов, избыточных в пределах зоны. Необходимо также создать группы безопасности сети и правила, чтобы разрешить любой трафик. Дополнительные сведения см. в статье [Azure Load Balancer Standard overview (preview)](../load-balancer/load-balancer-standard-overview.md) (Обзор Azure Load Balancer уровня "Стандартный" (предварительная версия)).

Полный пример масштабируемого набора, избыточного в пределах зоны, и сетевых ресурсов см. в этом [примере шаблона Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json).


## <a name="next-steps"></a>Дополнительная информация
После создания масштабируемого набора в зоне доступности см. статьи [Развертывание приложения в масштабируемых наборах виртуальных машин](virtual-machine-scale-sets-deploy-app.md) или [Обзор автомасштабирования с помощью масштабируемых наборов виртуальных машин Azure](virtual-machine-scale-sets-autoscale-overview.md).
