---
title: Изменение масштабируемого набора виртуальных машин Azure | Документация Майкрософт
description: Изменение масштабируемого набора виртуальных машин Azure
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
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Изменение масштабируемого набора виртуальных машин
В этой статье описано, как изменить имеющийся масштабируемый набор виртуальных машин, в том числе как изменить конфигурацию масштабируемого набора, работающих в наборе приложений, а также как управлять доступностью и многое другое.

## <a name="fundamental-concepts"></a>Базовые понятия

### <a name="scale-set-model"></a>Модель масштабируемого набора

Масштабируемый набор содержит модель, которая фиксирует *требуемое* состояние набора в целом. Для запроса модели масштабируемого набора можно использовать следующие средства.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get).

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  Дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Дополнительные сведения см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Запросить модель масштабируемого набора можно также с помощью [обозревателя ресурсов Azure (предварительная версия)](https://resources.azure.com) или [пакетов SDK Azure](https://azure.microsoft.com/downloads/).

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже приведен пример выходных данных Azure CLI.

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
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
  .
  .
  .
}
```

Как видите, эти свойства применяются к масштабируемому набору в целом.



### <a name="scale-set-instance-view"></a>Представление экземпляра масштабируемого набора

Масштабируемый набор также имеет представление экземпляра, которое в целом фиксирует текущее состояние *среды выполнения*. Для запроса представления экземпляра масштабируемого набора можно использовать следующие средства.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  Дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Дополнительные сведения см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Запросить представление экземпляра масштабируемого набора можно также с помощью [обозревателя ресурсов Azure (предварительная версия)](https://resources.azure.com) или [пакетов SDK Azure](https://azure.microsoft.com/downloads/).

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже приведен пример выходных данных Azure CLI.

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
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
  .
  .
  .
}
```

Как видите, эти свойства предоставляют сводку текущего состояния среды выполнения виртуальных машин в масштабируемом наборе, в том числе состояние расширений, применяемых к набору (опущено для краткости).



### <a name="scale-set-vm-model-view"></a>Представление модели виртуальной машины в масштабируемом наборе

Как и масштабируемый набор, каждая виртуальная машина в наборе имеет свое представление модели. Для запроса представления модели виртуальной машины в масштабируемом наборе можно использовать следующие средства.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  Дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Дополнительные сведения см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Запросить модель виртуальной машины в масштабируемом наборе можно также с помощью [обозревателя ресурсов Azure (предварительная версия)](https://resources.azure.com) или [пакетов SDK Azure](https://azure.microsoft.com/downloads/).

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже приведен пример выходных данных Azure CLI.

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Как видите, эти свойства описывают конфигурацию самой виртуальной машины, а не конфигурацию масштабируемого набора в целом. Например, модель масштабируемого набора имеет свойство `overprovision`, которого нет у модели виртуальной машины в наборе. Это отличие вызвано тем, что избыточная подготовка — это свойство масштабируемого набора в целом, а не отдельных виртуальных машин в нем. (Дополнительные сведения об избыточной подготовке см. в соответствующем разделе статьи [Рекомендации по проектированию масштабируемых наборов](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning).)



### <a name="scale-set-vm-instance-view"></a>Представление экземпляра виртуальной машины в масштабируемом наборе

Как и масштабируемый набор, каждая виртуальная машина в наборе имеет свое представление экземпляра. Для запроса представления экземпляра масштабируемого набора можно использовать следующие средства.

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  Дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Дополнительные сведения см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Запросить представление экземпляра виртуальной машины в масштабируемом наборе можно также с помощью [обозревателя ресурсов Azure (предварительная версия)](https://resources.azure.com) или [пакетов SDK Azure](https://azure.microsoft.com/downloads/).

Точное представление выходных данных зависит от параметров, введенных в команде. Ниже приведен пример выходных данных Azure CLI.

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
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
  .
  .
  .
}
```

Как видите, эти свойства описывают текущее состояние среды выполнения самой виртуальной машины, в том числе любые расширения, применяемые к масштабируемому набору (опущено для краткости).




## <a name="techniques-for-updating-global-scale-set-properties"></a>Методы обновления глобальных свойств масштабируемого набора

Для обновления глобального свойства масштабируемого набора необходимо обновить свойство в модели набора. Это можно сделать следующим образом.

* REST API: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

  Вы также можете развернуть шаблон Azure Resource Manager, обновив глобальные свойства масштабируемого набора с помощью свойств REST API.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  Дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

* Azure CLI:

  * Изменение свойства: `az vmss update --set {propertyPath}={value}`. 
  
  * Добавление объекта в свойство списка масштабируемого набора: `az vmss update --add {propertyPath} {JSONObjectToAdd}`. 
  
  * Удаление объекта из свойства списка масштабируемого набора: `az vmss update --remove {propertyPath} {indexToRemove}`. 
  
  Дополнительные сведения см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update). 
  
  Кроме того, если вы ранее развернули масштабируемый набор с помощью команды `az vmss create`, вы можете снова запустить команду `az vmss create`, чтобы его обновить. Проверьте, совпадают ли все свойства в команде `az vmss create` с предыдущими, за исключением тех, которые вы хотите изменить.



Обновить модель масштабируемого набора можно также с помощью [обозревателя ресурсов Azure (предварительная версия)](https://resources.azure.com) или [пакетов SDK Azure](https://azure.microsoft.com/downloads/).

После обновления модели масштабируемого набора новая конфигурация применяется ко всем новым виртуальным машинам, созданным в нем. Тем не менее модели имеющихся виртуальных машин в наборе все равно нужно обновить в соответствии с последней общей моделью масштабируемого набора. Модель каждой виртуальной машины имеет логическое свойство `latestModelApplied`, указывающее, актуальна ли виртуальная машина по отношению к последней общей модели масштабируемого набора. (Значение `true` означает, что виртуальная машина обновлена.)




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>Методы обновления виртуальных машин в соответствии с последней моделью масштабируемого набора

Масштабируемые наборы имеют *политику обновления*, которая определяет, как виртуальные машины обновляются в соответствии с последней моделью масштабируемого набора. Доступны три режима политики обновления:

- **Автоматический.** В этом режиме масштабируемый набор не дает никаких гарантий относительно порядка отключения виртуальных машин. Масштабируемый набор может одновременно завершить работу всех виртуальных машин. 
- **Последовательный.** В этом режиме масштабируемый набор развертывает обновления в виде пакетов с необязательной паузой между обработкой пакетов.
- **Вручную.** В этом режиме при обновлении модели масштабируемого набора с имеющимися виртуальными машинами ничего не происходит. Каждую имеющуюся виртуальную машину нужно обновить вручную. Это можно сделать следующим образом.

  - REST API: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances).

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    Дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

  - Azure CLI: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    Дополнительные сведения см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances).

  Обновить виртуальную машину в масштабируемом наборе вручную можно также с помощью [пакетов SDK Azure](https://azure.microsoft.com/downloads/).

>[!NOTE]
> В кластерах Azure Service Fabric можно использовать только автоматический режим, но обновление обрабатывается по-разному. Дополнительные сведения об обновлениях Service Fabric см. в [этой документации](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

Существует один тип изменения глобальных свойств масштабируемого набора, который не соответствует политике обновления. Это изменение в профиле ОС масштабируемого набора (например, имя и пароль администратора). Эти свойства можно изменить только в API-интерфейсе версии 2017-12-01 или более поздней. Эти изменения применяются только к виртуальным машинам, созданным после изменения модели масштабируемого набора. Чтобы обеспечить актуальность имеющихся виртуальных машин, необходимо пересоздать образ каждой из них. Ниже описано, как это сделать.

* REST API: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage).

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  Дополнительные сведения см. в [документации по PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm).

* Azure CLI: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  Дополнительные сведения см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage).

Чтобы пересоздать образ виртуальной машины в масштабируемом наборе вручную, можно также использовать [пакеты SDK Azure](https://azure.microsoft.com/downloads/).




## <a name="properties-with-restrictions-on-modification"></a>Свойства с ограничениями на изменение

### <a name="create-time-properties"></a>Свойства времени создания

Некоторые свойства можно задать только при первоначальном создании масштабируемого набора. Эти свойства включают в себя:

- Зоны
- издатель ссылки на образ;
- предложение ссылки на образ.

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>Свойства, которые можно изменить только на основе текущего значения

Некоторые свойства можно изменить только в зависимости от текущего значения. Эти свойства включают в себя:

- `singlePlacementGroup`. Если свойство `singlePlacementGroup` имеет значение true, его можно изменить на false. Однако, если свойство `singlePlacementGroup` имеет значение false, его *невозможно* изменить на true.
- `subnet`. Подсеть масштабируемого набора можно изменить, если исходная и новая подсети находятся в одной и той же виртуальной сети.

### <a name="properties-that-require-deallocation-to-change"></a>Свойства, для изменения которых требуется освободить виртуальные машины

Некоторым свойствам можно задать определенные значения, только если виртуальные машины в масштабируемом наборе освобождены. Эти свойства включают в себя:

- `sku name`. Если новый номер SKU виртуальной машины не поддерживается на оборудовании масштабируемого набора, необходимо освободить виртуальные машины в наборе перед изменением значения `sku name`. Дополнительные сведения об изменении размера виртуальных машин см. в [этой записи блога Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Обновления отдельных виртуальных машин

Некоторые изменения можно применить к отдельным виртуальным машинам, а не к глобальным свойствам масштабируемого набора. В настоящее время единственным обновлением отдельных виртуальных машин, которое поддерживается, является присоединение (отсоединение) дисков с данными к виртуальным машинам в масштабируемом наборе. Эта функция предоставляется в предварительной версии. Дополнительные сведения см. в [документации по предварительной версии](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios"></a>Сценарии

### <a name="application-updates"></a>Обновления приложений

Если приложение развертывается в масштабируемом наборе с помощью расширений, при обновлении конфигурации расширения приложение обновляется в соответствии с политикой обновления. Например, если у вас есть новая версия скрипта для запуска в расширении пользовательских скриптов, вы можете обновить свойство `fileUris`, чтобы указывать на новый скрипт. 

В некоторых случаях вы можете принудительно выполнить обновление, даже если конфигурация расширения не изменилась (например, вы обновили скрипт без изменения его URI). В таких случаях можно изменить `forceUpdateTag`, чтобы выполнить принудительное обновление. Платформа Azure не интерпретирует это свойство, поэтому изменение его значения не оказывает влияния на то, как выполняется расширение. Изменение приведет к повторному запуску расширения. 

Дополнительные сведения о `forceUpdateTag` см. в [документации по REST API для расширений](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Приложения также часто развертывают с помощью пользовательского образа. Этот сценарий описан в следующем разделе.

### <a name="os-updates"></a>Обновления ОС

При использовании образов платформы их можно обновить, изменив свойство `imageReference`. Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> В качестве эталонной версии образов платформы обычно указывается последняя. Это означает, что во время создания масштабируемых наборов, их масштабирования и повторного создания образов виртуальные машины создаются на основе последней доступной версии. Тем не менее это *не* означает, что образ ОС будет автоматически обновляться по мере выпуска новых версий. Этот отдельный компонент в настоящее время доступен в предварительной версии. Дополнительные сведения см. в статье [Автоматические обновления ОС масштабируемого набора виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

При использовании пользовательских образов их можно обновить, изменив идентификатор `imageReference`. Дополнительные сведения см. в [документации по REST API](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Примеры

### <a name="update-the-os-image-for-your-scale-set"></a>Обновление образа ОС масштабируемого набора

Предположим, что вы имеете масштабируемый набор, который работает под управлением старой версии Ubuntu LTS 16.04. Его нужно обновить до более нового выпуска версии 16.04 (например, 16.04.201801090). Свойство эталонной версии образа не входит в список, поэтому можно непосредственно изменить эти свойства с помощью следующих команд:

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Azure CLI: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>Обновление подсистемы балансировки нагрузки масштабируемого набора

Предположим, вы имеете масштабируемый набор с Azure Load Balancer и хотите заменить эту подсистему балансировки на шлюз приложений Azure. Свойства подсистемы балансировки нагрузки и шлюза приложений масштабируемого набора входят в список. Поэтому с помощью команд вы можете удалять и добавлять элементы списка вместо прямого изменения свойств.

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Azure CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> Эти команды предполагают наличие только одной конфигурации IP и балансировщика нагрузки в масштабируемом наборе. Если их несколько, возможно, потребуется использовать индекс списка, отличный от 0.