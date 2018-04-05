---
title: Управление масштабируемыми наборами виртуальных машин с помощью Azure PowerShell | Документация Майкрософт
description: Стандартные командлеты Azure PowerShell для управления масштабируемыми наборами виртуальных машин, в том числе команды для запуска и остановки экземпляра, а также для изменения емкости масштабируемого набора.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: c463dd26c106b3178becc977a8afd742220d7973
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Управление масштабируемым набором виртуальных машин с помощью Azure PowerShell
На протяжении жизненного цикла масштабируемого набора виртуальных машин может возникнуть необходимость выполнить одну или несколько задач управления. Кроме того, можно создавать сценарии для автоматизации различных задач жизненного цикла. В этой статье подробно рассматриваются некоторые стандартные командлеты Azure PowerShell, которые позволяют выполнять эти задачи.

Для выполнения этих задач управления требуется последний модуль Azure PowerShell. Дополнительные сведения см. в разделе [Начало работы с Azure PowerShelll](/powershell/azure/get-started-azureps). Если необходимо создать масштабируемый набор виртуальных машин, это можно [сделать с помощью Azure PowerShell](quick-create-powershell.md).


## <a name="view-information-about-a-scale-set"></a>Просмотр информации о масштабируемом наборе
Чтобы просмотреть общие сведения о масштабируемом наборе, выполните командлет [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). Следующий пример возвращает сведения о масштабируемом наборе *myScaleSet* в группе ресурсов *myResourceGroup*. Введите свои имена следующим образом.

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Просмотр виртуальных машин в масштабируемом наборе
Чтобы просмотреть список экземпляров виртуальных машин в масштабируемом наборе, выполните командлет [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Следующий пример выводит список всех экземпляров виртуальных машин в масштабируемом наборе *myScaleSet* в группе ресурсов *myResourceGroup*. Введите собственные значения для имен.

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Чтобы просмотреть дополнительные сведения о конкретном экземпляре виртуальной машины, добавьте параметр `-InstanceId` в командлет [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) и укажите экземпляр для просмотра. Следующий пример возвращает сведения об экземпляре виртуальной машины *0* в масштабируемом наборе *myScaleSet* в группе ресурсов *myResourceGroup*. Введите свои имена следующим образом.

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Изменение емкости масштабируемого набора
Указанные выше команды показывали сведения о масштабируемом наборе и экземплярах виртуальных машин. Чтобы увеличить или уменьшить число экземпляров в масштабируемом наборе, можно изменить его емкость. Масштабируемый набор автоматически создает или удаляет необходимое количество виртуальных машин, а затем настраивает виртуальные машины для приема трафика приложения.

Сначала создайте объект масштабируемого набора с помощью командлета [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), затем укажите новое значение `sku.capacity`. Чтобы применить изменение емкости, используйте командлет [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). В следующем примере емкость масштабируемого набора *myScaleSet* в группе ресурсов *myResourceGroup* изменяется до *5* экземпляров. Укажите свои значения следующим образом.

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

На обновление емкости масштабируемого набора требуется несколько минут. При уменьшении емкости масштабируемого набора первыми удаляются виртуальные машины с наибольшим значением идентификатора экземпляра.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Остановка и запуск виртуальных машин в масштабируемом наборе
Чтобы остановить одну или несколько виртуальных машин в масштабируемом наборе, используйте командлет [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). С помощью параметра `-InstanceId` можно указать одну или несколько виртуальных машин, которые нужно остановить. Если не указать идентификатор экземпляра, останавливаются все виртуальные машины в масштабируемом наборе. Чтобы остановить несколько виртуальных машин, разделите идентификаторы экземпляров запятыми.

Следующий пример останавливает экземпляр *0* в масштабируемом наборе *myScaleSet* в группе ресурсов *myResourceGroup*. Укажите свои значения, как показано ниже.

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

По умолчанию остановленные виртуальные машины освобождаются, и за них не взимается плата за вычислительные операции. Если требуется, чтобы остановленная виртуальная машина осталась в подготовленном состоянии, добавьте параметр `-StayProvisioned` в предыдущую команду. За остановленные виртуальные машины, которые остаются в подготовленном состоянии, взимается плата за вычислительные операции по стандартной ставке.


### <a name="start-vms-in-a-scale-set"></a>Запуск виртуальных машин в масштабируемом наборе
Чтобы запустить одну или несколько виртуальных машин в масштабируемом наборе, используйте командлет [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). С помощью параметра `-InstanceId` можно указать одну или несколько виртуальных машин, которые нужно запустить. Если не указать идентификатор экземпляра, запускаются все виртуальные машины в масштабируемом наборе. Чтобы запустить несколько виртуальных машин, разделите идентификаторы экземпляров запятыми.

Следующий пример запускает экземпляр *0* в масштабируемом наборе *myScaleSet* в группе ресурсов *myResourceGroup*. Укажите свои значения, как показано ниже.

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Перезапуск виртуальных машин в масштабируемом наборе
Чтобы перезапустить одну или несколько виртуальных машин в масштабируемом наборе, используйте командлет [Retart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). С помощью параметра `-InstanceId` можно указать одну или несколько виртуальных машин, которые нужно перезапустить. Если не указать идентификатор экземпляра, перезапускаются все виртуальные машины в масштабируемом наборе. Чтобы перезапустить несколько виртуальных машин, разделите идентификаторы экземпляров запятыми.

Следующий пример перезапускает экземпляр *0* в масштабируемом наборе *myScaleSet* в группе ресурсов *myResourceGroup*. Укажите свои значения, как показано ниже.

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Удаление виртуальных машин из масштабируемого набора
Чтобы удалить одну или несколько виртуальных машин из масштабируемого набора, используйте командлет [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). С помощью параметра `-InstanceId` можно указать одну или несколько виртуальных машин, которые нужно удалить. Если не указать идентификатор экземпляра, удаляются все виртуальные машины в масштабируемом наборе. Чтобы удалить несколько виртуальных машин, разделите идентификаторы экземпляров запятыми.

Следующий пример удаляет экземпляр *0* в масштабируемом наборе *myScaleSet* в группе ресурсов *myResourceGroup*. Укажите свои значения, как показано ниже.

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Дополнительная информация
К другим типичным задачам управления масштабируемым набором относятся [развертывание приложения](virtual-machine-scale-sets-deploy-app.md) и [обновление экземпляров виртуальных машин](virtual-machine-scale-sets-upgrade-scale-set.md). Можно также использовать Azure PowerShell для [настройки правил автомасштабирования](virtual-machine-scale-sets-autoscale-overview.md).
