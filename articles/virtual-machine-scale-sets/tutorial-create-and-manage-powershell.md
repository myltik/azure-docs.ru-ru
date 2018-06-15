---
title: Руководство по созданию масштабируемого набора виртуальных машин Azure и управлению им | Документация Майкрософт
description: Сведения об использовании Azure PowerShell для создания масштабируемого набора виртуальных машин и сведения о некоторых стандартных задачах управления, включая запуск и остановку экземпляра или изменение емкости масштабируемого набора.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7531ddce37b2f6d140cb277727d6168d8d98ba1e
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802390"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Руководство по созданию масштабируемого набора виртуальных машин с помощью Azure PowerShell
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. На протяжении жизненного цикла масштабируемого набора виртуальных машин может возникнуть необходимость выполнить одну или несколько задач управления. Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание масштабируемого набора виртуальных машин и подключение к нему.
> * Выбор и использование образов виртуальных машин
> * Просмотр и использование определенных размеров экземпляров виртуальных машин.
> * Масштабирование масштабируемого набора вручную.
> * Выполнение стандартных задач управления масштабируемым набором.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 6.0.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure. 


## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создать до создания масштабируемого набора виртуальных машин. Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В примере создается группа ресурсов под названием *myResourceGroup* в регионе *EastUS*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
Имя группы ресурсов указывается при создании или изменении масштабируемого набора в этом руководстве.


## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Первым делом настройте имя и пароль администратора для экземпляров виртуальных машин с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Теперь создайте масштабируемый набор виртуальных машин с помощью командлета [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Чтобы распределить трафик между отдельными экземплярами виртуальных машин, создается еще и подсистема балансировки нагрузки. Подсистема балансировки нагрузки определяет правила передачи трафика на TCP-порт 80, а также разрешает подключение удаленного рабочего стола трафик через TCP-порт 3389 и удаленное взаимодействие PowerShell через TCP-порт 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Создание и настройка всех ресурсов и экземпляров виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Просмотр экземпляров виртуальных машин в масштабируемом наборе
Чтобы просмотреть список экземпляров виртуальных машин в масштабируемом наборе, выполните командлет [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm), как показано ниже.

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

В следующем примере выходных данных показано два экземпляра виртуальной машины в масштабируемом наборе.

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Чтобы просмотреть дополнительные сведения о конкретном экземпляре виртуальной машины, добавьте параметр `-InstanceId` в командлет [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Следующий пример возвращает сведения об экземпляре виртуальной машины *1*.

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Вывод списка со сведениями о подключении
Общедоступный IP-адрес назначен подсистеме балансировки нагрузки, перенаправляющей трафик к отдельным экземплярам виртуальных машин. По умолчанию правила преобразования сетевых адресов (NAT) добавляются в подсистему балансировки нагрузки Azure для переадресации трафика удаленного подключения на каждую виртуальную машину на определенном порте. Чтобы подключиться к экземплярам виртуальных машин в масштабируемом наборе, необходимо создать удаленное подключение по назначенному общедоступному IP адресу и номеру порта.

Чтобы получить список портов NAT для подключения к экземплярам виртуальных машин в масштабируемом наборе, сначала получите объект подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Затем просмотрите правила преобразования сетевых адресов для входящих подключений с помощью командлета [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig).

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

В следующем примере выходных данных показано имя экземпляра, общедоступный IP-адрес подсистемы балансировки нагрузки и номер порта, в который правила преобразования сетевых адресов перенаправляют трафик.

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

Как показано с помощью предыдущей команды [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm), *имя* правила соответствует имени экземпляра виртуальной машины. Например, чтобы подключиться к экземпляру виртуальной машины *0*, используйте *myScaleSet3389.0* и подключитесь к порту *50001*. Для подключения к экземпляру виртуальной машины *1* используйте значение из *myScaleSet3389.1* и подключитесь к порту *50002*. Для осуществления удаленного подключения с помощью PowerShell подключитесь к правилу соответствующего экземпляра виртуальной машины по *TCP*-порту *5985*.

Получите сведения об общедоступном IP-адресе подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress).

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Выходные данные примера:

```powershell
IpAddress
---------
52.168.121.216
```

Создайте удаленное подключение к первому экземпляру виртуальной машины. Укажите общедоступный IP-адрес и номер порта требуемого экземпляра виртуальной машины, как показано в предыдущих командах. При появлении запроса введите учетные данные, используемые для создания масштабируемого набора (по умолчанию в используемых командах этими значениями являются *azureuser* и *P@ssw0rd!*). Если вы используете Azure Cloud Shell, выполните этот шаг с помощью локальной командной строки PowerShell или клиента удаленного рабочего стола. В следующем примере устанавливается подключение к экземпляру виртуальной машины *1*.

```powershell
mstsc /v 52.168.121.216:50001
```

После входа в экземпляр виртуальной машины вы можете выполнить некоторые изменения конфигурации вручную. Теперь отключите удаленное подключение.


## <a name="understand-vm-instance-images"></a>Описание образов экземпляра виртуальной машины
Azure Marketplace содержит множество образов, которые можно использовать для создания экземпляров виртуальных машин. Для просмотра списка доступных издателей используйте командлет [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher).

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Для просмотра списка образов, доступных для данного издателя, воспользуйтесь командлетом [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). Кроме того, список образов можно отфильтровать по издателю или предложению с помощью аргумента `-PublisherName` или `–Offer` соответственно. В следующем примере список всех образов фильтруется по имени издателя *MicrosoftWindowsServer* и предложению *WindowsServer*.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

В следующем примере выходных данных показаны все доступные образы Windows Server.

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

При создании масштабируемого набора в начале руководства для экземпляров виртуальных машин использовался предлагаемый по умолчанию образ виртуальной машины *Windows Server 2016 DataCenter*. Вы можете указать другой образ виртуальной машины на основе выходных данных командлета [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). В следующем примере создается масштабируемый набор с использованием параметра `-ImageName` для указания образа виртуальной машины *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Так как создание и настройка всех ресурсов и экземпляров виртуальных машин масштабируемого набора занимает несколько минут, вам не нужно развертывать следующий масштабируемый набор.

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>Описание размеров экземпляра виртуальной машины
Размер экземпляра виртуальной машины (или *номер SKU*) определяет количество выделяемых ему вычислительных ресурсов, таких как ЦП, GPU и память. Размеры экземпляров виртуальных машин в масштабируемом наборе должны соответствовать ожидаемой рабочей нагрузке.

### <a name="vm-instance-sizes"></a>Размеры экземпляров виртуальных машин
В приведенной ниже таблице указаны категории распространенных размеров виртуальной машины и варианты использования.

| type                     | Распространенные размеры           |    ОПИСАНИЕ       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Универсальные](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0–7| Сбалансированное соотношение ресурсов ЦП и памяти. Идеально подходят для разработки и тестирования малых и средних приложений и решений для обработки данных.  |
| [Оптимизированные для вычислений](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | Высокое соотношение ресурсов ЦП и памяти. Подходят для приложений со средним объемом трафика, сетевых устройств и пакетных процессов.        |
| [Оптимизированные для памяти](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Высокое соотношение ресурсов памяти и числа ядер. Отлично подходят для реляционных баз данных, кэша среднего и большого объема, а также выполняющейся в памяти аналитики.                 |
| [Оптимизированные для хранилища](../virtual-machines/windows/sizes-storage.md)      | Ls                | Высокая пропускная способность дисков и количество операций ввода-вывода. Идеальный вариант для работы с большими данными, а также с базами данных SQL и NoSQL.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | Специализированные виртуальные машины, предназначенные для ресурсоемкой отрисовки изображений и редактирования видео.       |
| [Высокопроизводительные](../virtual-machines/windows/sizes-hpc.md) | H, A8–A11          | Виртуальные машины с самыми мощными ЦП, для которых можно настроить сетевые интерфейсы с высокой пропускной способностью (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Поиск доступных размеров экземпляров виртуальной машины
Чтобы просмотреть список доступных размеров экземпляров виртуальных машин в определенном регионе, используйте команду [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). 

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

Вывод команды будет примерно таким, как в следующем сокращенном примере, в котором показаны ресурсы, назначенные каждому размеру виртуальной машины:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

При создании масштабируемого набора в начале этого руководства для экземпляров виртуальной машины был указан стандартный номер SKU виртуальной машины *Standard_DS1_v2*. Вы можете указать другой размер экземпляра виртуальной машины на основе выходных данных командлета [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). В указанном ниже примере будет создан масштабируемый набор с использованием параметра `-VmSize`, который позволяет указать размер экземпляра виртуальной машины *Standard_F1*. Так как создание и настройка всех ресурсов и экземпляров виртуальных машин масштабируемого набора занимает несколько минут, вам не нужно развертывать следующий масштабируемый набор.

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Изменение емкости масштабируемого набора
При создании масштабируемого набора вы запросили два экземпляра виртуальных машин. Чтобы увеличить или уменьшить число экземпляров виртуальных машин в масштабируемом наборе, можно изменить его емкость вручную. Масштабируемый набор создает или удаляет необходимое количество экземпляров виртуальной машины, а затем настраивает подсистему балансировки нагрузки для распределения трафика.

Сначала создайте объект масштабируемого набора с помощью командлета [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), затем укажите новое значение `sku.capacity`. Чтобы применить изменение емкости, используйте командлет [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). В следующем примере число экземпляров виртуальных машин в масштабируемом наборе определяется равным *3*:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

На обновление емкости масштабируемого набора требуется несколько минут. Чтобы просмотреть количество экземпляров, присутствующих в масштабируемом наборе, выполните командлет [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss).

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

В следующем примере выходных данных показано, что емкость масштабируемого набора равна *3*.

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Общие задачи управления
Теперь вы можете создать масштабируемый набор, вывести сведения о подключениях и подключиться к экземплярам виртуальных машин. Вы узнали, как использовать разные образы операционной системы для экземпляров виртуальных машин, выбирать разные размеры виртуальной машины и изменять количество экземпляров вручную. В рамках повседневных задач по управлению вам может понадобиться остановить, запустить или перезапустить экземпляры виртуальных машин в масштабируемом наборе.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Остановка и освобождение экземпляров виртуальных машин в масштабируемом наборе
Чтобы остановить одну или несколько виртуальных машин в масштабируемом наборе, используйте командлет [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). С помощью параметра `-InstanceId` можно указать одну или несколько виртуальных машин, которые нужно остановить. Если не указать идентификатор экземпляра, останавливаются все виртуальные машины в масштабируемом наборе. Следующий пример останавливает экземпляр *1*.

```azurepowershell-interactive
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

По умолчанию остановленные виртуальные машины освобождаются, и за них не взимается плата за вычислительные операции. Если требуется, чтобы остановленная виртуальная машина осталась в подготовленном состоянии, добавьте параметр `-StayProvisioned` в предыдущую команду. За остановленные виртуальные машины, которые остаются в подготовленном состоянии, взимается плата за вычислительные операции по стандартной ставке.

### <a name="start-vm-instances-in-a-scale-set"></a>Запуск экземпляров виртуальных машин в масштабируемом наборе
Чтобы запустить одну или несколько виртуальных машин в масштабируемом наборе, используйте командлет [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). С помощью параметра `-InstanceId` можно указать одну или несколько виртуальных машин, которые нужно запустить. Если не указать идентификатор экземпляра, запускаются все виртуальные машины в масштабируемом наборе. Следующий пример запускает экземпляр *1*.

```azurepowershell-interactive
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Перезапуск экземпляров виртуальных машин в масштабируемом наборе
Чтобы перезапустить одну или несколько виртуальных машин в масштабируемом наборе, используйте командлет [Retart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). С помощью параметра `-InstanceId` можно указать одну или несколько виртуальных машин, которые нужно перезапустить. Если не указать идентификатор экземпляра, перезапускаются все виртуальные машины в масштабируемом наборе. Следующий пример перезапускает экземпляр *1*.

```azurepowershell-interactive
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Очистка ресурсов
При удалении группы ресурсов будут также удалены все содержащиеся в ней ресурсы: экземпляры виртуальной машины, виртуальная сеть и диски. Параметр `-Force` подтверждает, что вы хотите удалить ресурсы без дополнительного запроса. При использовании параметра `-AsJob` управление возвращается в командную строку без ожидания завершения операции.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создавать стандартный масштабируемый набор и выполнять задачи управления с помощью Azure PowerShell:

> [!div class="checklist"]
> * Создание масштабируемого набора виртуальных машин и подключение к нему.
> * Выбор и использование образов виртуальных машин
> * Просмотр и использование определенных размеров виртуальных машин
> * Масштабирование масштабируемого набора вручную.
> * Выполнение стандартных задач управления масштабируемым набором.

Перейдите к следующему руководству, чтобы узнать о дисках масштабируемого набора.

> [!div class="nextstepaction"]
> [Использование дисков данных с масштабируемыми наборами](tutorial-use-disks-powershell.md)
