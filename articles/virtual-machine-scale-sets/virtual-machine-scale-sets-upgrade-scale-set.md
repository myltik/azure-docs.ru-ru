---
title: Изменение масштабируемого набора виртуальных машин Azure | Документация Майкрософт
description: Узнайте, как изменить и обновить масштабируемый набор виртуальных машин Azure с помощью интерфейсов REST API, Azure PowerShell и Azure CLI 2.0.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: 662cea7ac47e411b127540faf5cab8b3c4d8964a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32194052"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Изменение масштабируемого набора виртуальных машин
На протяжении жизненного цикла приложений может потребоваться изменить или обновить масштабируемый набор виртуальных машин. Это может быть обновление конфигурации масштабируемого набора или изменение конфигурации приложения. В этой статье описывается, как можно изменить существующий масштабируемый набор с помощью интерфейсов REST API, Azure PowerShell или Azure CLI 2.0.

## <a name="fundamental-concepts"></a>Базовые понятия

### <a name="the-scale-set-model"></a>Модель масштабируемого набора
Масштабируемый набор содержит модель, которая фиксирует *требуемое* состояние набора в целом. Для запроса модели масштабируемого набора можно использовать: 

- REST API с [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get), как показано ниже.

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Командлет [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) в Azure PowerShell:

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Можно использовать команду [az vmss show](/cli/azure/vmss#az_vmss_show) в Azure CLI 2.0.

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Можно также использовать [resources.azure.com](https://resources.azure.com) или [пакеты SDK Azure](https://azure.microsoft.com/downloads/) для конкретного языка.

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже показан сокращенный пример выходных данных Azure CLI 2.0.

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Эти свойства применяются к масштабируемому набору в целом.


### <a name="the-scale-set-instance-view"></a>Представление экземпляра масштабируемого набора
Масштабируемый набор также имеет представление экземпляра, которое фиксирует текущее состояние *среды выполнения* масштабируемого набора в целом. Для запроса представления экземпляра масштабируемого набора можно использовать следующие средства.

- REST API с [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview), как показано ниже.

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Командлет [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) в Azure PowerShell:

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Можно использовать команду [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) в Azure CLI 2.0.

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Можно также использовать [resources.azure.com](https://resources.azure.com) или [пакеты SDK Azure](https://azure.microsoft.com/downloads/) для конкретного языка.

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже показан сокращенный пример выходных данных Azure CLI 2.0.

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Эти свойства предоставляют сводку текущего состояния среды выполнения виртуальных машин в масштабируемом наборе, включая состояние расширений, применяемых к набору.


### <a name="the-scale-set-vm-model-view"></a>Представление модели виртуальной машины масштабируемого набора
Как и масштабируемый набор, каждая виртуальная машина в наборе имеет свое представление модели. Для запроса представления модели виртуальной машины в масштабируемом наборе можно использовать следующие средства.

- REST API с [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get), как показано ниже.

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Командлет [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm) в Azure PowerShell:

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Можно использовать команду [az vmss show](/cli/azure/vmss#az_vmss_show) в Azure CLI 2.0.

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Можно также использовать [resources.azure.com](https://resources.azure.com) или [пакеты SDK Azure](https://azure.microsoft.com/downloads/).

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже показан сокращенный пример выходных данных Azure CLI 2.0.

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Эти свойства описывают конфигурацию самой виртуальной машины, а не конфигурацию масштабируемого набора в целом. Например, модель масштабируемого набора имеет свойство `overprovision`, которого нет у модели виртуальной машины в наборе. Это отличие вызвано тем, что избыточная подготовка является свойством масштабируемого набора в целом, а не отдельных виртуальных машин в наборе (дополнительные сведения об избыточной подготовке см. в разделе [Рекомендации по проектированию масштабируемых наборов](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Представление экземпляра виртуальной машины в масштабируемом наборе
Как и масштабируемый набор, каждая виртуальная машина в наборе имеет свое представление экземпляра. Для запроса представления экземпляра масштабируемого набора можно использовать следующие средства.

- REST API с [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview), как показано ниже.

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Командлет [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm) в Azure PowerShell:

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Можно использовать команду [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view) в Azure CLI 2.0.

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Можно также использовать [resources.azure.com](https://resources.azure.com) или [пакеты SDK Azure](https://azure.microsoft.com/downloads/).

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже показан сокращенный пример выходных данных Azure CLI 2.0.

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Эти свойства описывают текущее состояние среды выполнения самой виртуальной машины, включая любые расширения, применяемые к масштабируемому набору.


## <a name="how-to-update-global-scale-set-properties"></a>Как обновлять глобальные свойства масштабируемого набора
Для обновления глобального свойства масштабируемого набора необходимо обновить свойство в модели набора. Это можно сделать следующим образом.

- Можно использовать REST API с [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate), как показано ниже.

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Вы можете развернуть шаблон Resource Manager, обновив глобальные свойства масштабируемого набора с помощью свойств REST API.

- Можно использовать командлет [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) в Azure PowerShell:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Или можно использовать команду [az vmss update](/cli/azure/vmss#az_vmss_update) в Azure CLI 2.0.
    - Чтобы изменить свойство, выполните следующую команду.

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Чтобы добавить объект в свойство списка масштабируемого набора, выполните следующую команду. 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Чтобы удалить объект из свойства списка масштабируемого набора, выполните следующую команду. 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Если вы ранее развернули масштабируемый набор с помощью команды `az vmss create`, то вы можете снова выполнить команду `az vmss create`, чтобы его обновить. Проверьте, совпадают ли все свойства в команде `az vmss create` с предыдущими, за исключением тех, которые вы хотите изменить.

- Можно также использовать [resources.azure.com](https://resources.azure.com) или [пакеты SDK Azure](https://azure.microsoft.com/downloads/).

После обновления модели масштабируемого набора новая конфигурация применяется ко всем новым виртуальным машинам, созданным в наборе. Тем не менее модели для существующих виртуальных машин в наборе все равно должны быть обновлены в соответствии с последней общей моделью масштабируемого набора. В модели каждой виртуальной машины есть логическое свойство `latestModelApplied`. Оно указывает, актуальна ли виртуальная машина по отношению к последней общей модели масштабируемого набора (`true` означает, что виртуальная машина обновлена).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Как обновить виртуальные машины в соответствии с последней моделью масштабируемого набора
Масштабируемые наборы имеют политику обновления, которая определяет, как виртуальные машины обновляются в соответствии с последней моделью масштабируемого набора. Доступны три режима политики обновления:

- **Автоматический**. В этом режиме масштабируемый набор не дает никаких гарантий относительно порядка отключения виртуальных машин. Масштабируемый набор может одновременно завершить работу всех виртуальных машин. 
- **Последовательный**. В этом режиме масштабируемый набор развертывает обновления в виде пакетов с необязательной паузой между обработкой пакетов.
- **Вручную**. В этом режиме при обновлении модели масштабируемого набора с имеющимися виртуальными машинами ничего не происходит.
 
Чтобы обновить существующие виртуальные машины, для каждой из них нужно выполнить обновление в ручном режиме. Это можно сделать следующим образом.

- Можно использовать REST API с [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances), как показано ниже.

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Можно использовать командлет [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance) в Azure PowerShell:
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Или можно использовать команду [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances) в Azure CLI 2.0.

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Можно также использовать [пакеты SDK Azure](https://azure.microsoft.com/downloads/) для конкретного языка.

>[!NOTE]
> В кластерах Service Fabric можно использовать только *автоматический* режим, но обновление обрабатывается по-разному. Дополнительные сведения см. в разделе [Обновление приложения Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Существует один тип изменения глобальных свойств масштабируемого набора, который не соответствует политике обновления. Внести изменения в профиль ОС масштабируемого набора (например, изменить имя и пароль администратора) можно только в API версии *2017-12-01* или более поздней версии. Эти изменения применяются только к виртуальным машинам, созданным после изменения модели масштабируемого набора. Чтобы обеспечить актуальность существующих виртуальных машин, необходимо пересоздать образ каждой существующей виртуальной машины. Это можно сделать следующим образом.

- Можно использовать REST API с [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage), как показано ниже.

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Можно использовать командлет [Set-AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm) в Azure PowerShell:

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Или можно использовать команду [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage) в Azure CLI 2.0.

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Можно также использовать [пакеты SDK Azure](https://azure.microsoft.com/downloads/) для конкретного языка.


## <a name="properties-with-restrictions-on-modification"></a>Свойства с ограничениями на изменение

### <a name="create-time-properties"></a>Свойства времени создания
Некоторые свойства можно задать только при создании масштабируемого набора. Эти свойства включают в себя:

- зоны доступности;
- издатель ссылки на образ;
- предложение ссылки на образ.
- тип учетной записи хранения управляемого диска ОС.

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Свойства, которые можно изменить только на основе текущего значения
Некоторые свойства можно изменить только в зависимости от текущего значения. Эти свойства включают в себя:

- **singlePlacementGroup**. Если это свойство имеет значение true, его можно изменить на false. Однако, если значением является false, его **не возможно** изменить на true.
- **subnet**. Подсеть масштабируемого набора можно изменить, если исходная и новая подсети находятся в одной и той же виртуальной сети.

### <a name="properties-that-require-deallocation-to-change"></a>Свойства, для изменения которых требуется освободить виртуальные машины
Для некоторых свойств можно задать определенные значения, только если виртуальные машины в масштабируемом наборе освобождены. Эти свойства включают в себя:

- **SKU name**. Если новый SKU виртуальной машины не поддерживается на оборудовании масштабируемого набора, необходимо освободить виртуальные машины в наборе перед изменением имени SKU. Дополнительные сведения см. в статье [Изменение размера виртуальной машины Windows](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Обновления отдельных виртуальных машин
Некоторые изменения можно применить к отдельным виртуальным машинам, а не к глобальным свойствам масштабируемого набора. В настоящее время единственным обновлением отдельных виртуальных машин, которое поддерживается, является подключение или отключение дисков данных виртуальных машин в масштабируемом наборе. Эта функция предоставляется в предварительной версии. Дополнительные сведения см. в [документации по предварительной версии](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Сценарии

### <a name="application-updates"></a>Обновления приложений
Если приложение развертывается в масштабируемом наборе с помощью расширений, то при обновлении конфигурации расширения приложение будет обновлено в соответствии с политикой обновления. Например, если у вас есть новая версия сценария для запуска в расширении пользовательских сценариев, вы можете обновить свойство *fileUris*, чтобы указать новый сценарий. В некоторых случаях вы можете принудительно выполнить обновление, даже если конфигурация расширения не изменилась (например, вы обновили сценарий без изменения его универсального кода ресурса (URI)). В таких случаях можно изменить *forceUpdateTag* для принудительного обновления. Это свойство не интерпретируется платформой Azure. Если изменить его значение, это не повлияет работу расширения. Изменение просто приведет к повторному запуску расширения. Дополнительные сведения о *forceUpdateTag* см. в [документации по REST API для расширений](/rest/api/compute/virtualmachineextensions/createorupdate). Обратите внимание, что *forceUpdateTag* может использоваться со всеми расширениями, а не только с расширением пользовательских скриптов.

Приложения также часто развертывают с помощью пользовательского образа. Этот сценарий описан в следующем разделе.

### <a name="os-updates"></a>обновления ОС;
При использовании образов платформы Azure их можно обновить, изменив *imageReference* (дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Для образов платформы обычно указывается последняя версия в качестве эталонной версии образа. Во время создания, масштабирования и повторного создания образов виртуальные машины создаются на основе последней доступной версии. Тем не менее это **не** означает, что образ ОС будет автоматически обновляться по мере выпуска новых версий. Отдельный компонент, обеспечивающий автоматическое обновление ОС, в настоящий момент находится на этапе предварительной версии. Дополнительные сведения см. в [документации по автоматическому обновлению ОС](virtual-machine-scale-sets-automatic-upgrade.md).

При использовании пользовательских образов их можно обновить, изменив идентификатор *imageReference* (дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Примеры

### <a name="update-the-os-image-for-your-scale-set"></a>Обновление образа ОС масштабируемого набора
Предположим, что у вас есть масштабируемый набор, который работает под управлением старой версии Ubuntu LTS 16.04. Его нужно обновить до более новой версии Ubuntu LTS 16.04 (например, *16.04.201801090*). Свойство эталонной версии образа не входит в список, поэтому можно непосредственно изменить эти свойства с помощью одной из следующих команд.

- Можно использовать командлет [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) в Azure PowerShell:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Или можно использовать команду [az vmss update](/cli/azure/vmss#az_vmss_update_instances) в Azure CLI 2.0.

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Обновление подсистемы балансировки нагрузки масштабируемого набора
Предположим, у вас есть масштабируемый набор с Azure Load Balancer и вы хотите заменить Azure Load Balancer на шлюз приложений Azure. Свойства подсистемы балансировки нагрузки и шлюза приложений для масштабируемого набора являются частью списка, поэтому вы можете использовать команды для удаления и добавления элементов списка вместо прямого изменения свойств.

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the referencerence to the application gateway
    $ipconf = New-AzureRmVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI 2.0:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Эти команды предполагают наличие только одной конфигурации IP и балансировщика нагрузки в масштабируемом наборе. Если их несколько, может потребоваться использовать индекс списка, отличный от *0*.


## <a name="next-steps"></a>Дополнительная информация
Общие задачи управления масштабируемыми наборами можно также выполнять с помощью [Azure CLI 2.0](virtual-machine-scale-sets-manage-cli.md) или [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
