---
title: "Создание виртуальных машин Linux и управление ими с помощью Azure CLI | Документация Майкрософт"
description: "Руководство по созданию виртуальных машин Linux и управлению ими с помощью Azure CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7a6f255c64a584e29801aacb40c79462751fe535
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017

---

# <a name="create-and-manage-linux-vms-with-the-azure-cli"></a>Создание виртуальных машин Linux и управление ими с помощью Azure CLI

В этом руководстве рассматриваются основные элементы создания виртуальной машины Azure, в том числе выбор ее размера, выбор образа и развертывание. Кроме того, в этом руководстве рассматриваются такие базовые операции управления, как управление состоянием виртуальной машины, ее удаление и изменение размера.

Для работы с этим руководством можно использовать последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group#create). 

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины. В этом примере создается группа ресурсов *myResourceGroupVM* в регионе *westus*. 

```azurecli
az group create --name myResourceGroupVM --location westus
```

Группа ресурсов указывается при создании или изменении виртуальной машины, что показывается в этом руководстве.

## <a name="create-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину, выполнив команду [az vm create](https://docs.microsoft.com/cli/azure/vm#create). 

При создании виртуальной машины доступно несколько вариантов, таких как образ операционной системы, определение размера диска и учетные данные администратора. В этом примере создается виртуальная машина *myVM* под управлением Ubuntu Server. 

```azurecli
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys
```

После создания виртуальной машины Azure CLI выводит информацию о ней. Запишите `publicIpAddress`. Этот адрес может использоваться для доступа к виртуальной машине. 

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Подключение к виртуальной машине

Теперь к виртуальной машине можно подключиться по протоколу SSH. Замените IP-адрес в примере адресом `publicIpAddress`, записанным на предыдущем шаге.

```bash
ssh 52.174.34.95
```

Завершив работу с виртуальной машиной, закройте сеанс SSH. 

```bash
exit
```

## <a name="understand-vm-images"></a>Описание образов виртуальных машин

Azure Marketplace содержит множество образов, которые можно использовать для создания виртуальных машин. На предыдущих шагах виртуальная машина создавалась с помощью образа Ubuntu. На этом шаге Azure CLI используется для поиска на сайте Marketplace образа CentOS, который затем используется для развертывания второй виртуальной машины.  

Чтобы просмотреть список наиболее часто используемых образов, используйте команду [az vm image list](/cli/azure/vm/image#list).

```azurecli
az vm image list --output table
```

Она отобразит наиболее популярные образы виртуальных машин в Azure.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Получить полный список можно, добавив аргумент `--all`. Кроме того, список образов можно отфильтровать по издателю или предложению с помощью аргумента `--publisher` или `–-offer` соответственно. В этом примере список образов отфильтрован по предложению *CentOS*. 

```azurecli
az vm image list --offer CentOS --all --output table
```

Частичные выходные данные приведены ниже.

```azurecli
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Чтобы развернуть виртуальную машину с помощью определенного образа, запишите значение в столбце *Urn*. При указании образа его номер версии можно заменить ключевым словом latest. В этом случае будет выбрана последняя версия дистрибутива. В данном примере добавлен аргумент `--image`, чтобы указать последнюю версию образа CentOS 6.5.  

```azurecli
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Описание размеров виртуальных машин

Размер виртуальной машины определяет количество выделяемых ей вычислительных ресурсов, таких как ЦП, GPU и память. Размеры виртуальных машин должны соответствовать ожидаемой рабочей нагрузке. При увеличении рабочей нагрузки размер существующей виртуальной машины может быть изменен.

### <a name="vm-sizes"></a>Размеры виртуальных машин

В приведенной ниже таблицы указаны категории размеров и примеры использования.  

| Тип                     | Размеры           |    Описание       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Универсальные](sizes-general.md)         |DSv2, Dv2, DS, D, Av2, A0–A7| Сбалансированное соотношение ресурсов ЦП и памяти. Идеально подходят для разработки и тестирования малых и средних приложений и решений для обработки данных.  |
| [Оптимизированные для вычислений](sizes-compute.md)   | Fs, F             | Высокое соотношение ресурсов ЦП и памяти. Подходят для приложений со средним объемом трафика, сетевых устройств и пакетных процессов.        |
| [Оптимизированные для памяти](../virtual-machines-windows-sizes-memory.md)    | GS, G, DSv2, DS, Dv2, D   | Высокое соотношение ресурсов памяти и числа ядер. Отлично подходят для реляционных баз данных, кэша среднего и большого объема, а также выполняющейся в памяти аналитики.                 |
| [Оптимизированные для хранилища](../virtual-machines-windows-sizes-storage.md)      | Ls                | Высокая пропускная способность дисков и количество операций ввода-вывода. Идеальный вариант для работы с большими данными, а также с базами данных SQL и NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | Специализированные виртуальные машины, предназначенные для ресурсоемкой отрисовки изображений и редактирования видео.       |
| [Высокопроизводительные](sizes-hpc.md) | H, A8–A11          | Виртуальные машины с самыми мощными ЦП, для которых можно настроить сетевые интерфейсы с высокой пропускной способностью (RDMA). 


### <a name="find-available-vm-sizes"></a>Поиск всех доступных размеров виртуальных машин

Чтобы просмотреть список доступных размеров виртуальных машин в определенном регионе, используйте команду [az vm list-sizes](/cli/azure/vm#list-sizes). 

```azurecli
az vm list-sizes --location westus --output table
```

Частичные выходные данные приведены ниже.

```azurecli
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Создание виртуальной машины с определенным размером

В предыдущем примере создания виртуальной машины размер не был указан, что привело к использованию размера по умолчанию. Размер виртуальной машины можно выбрать во время ее создания с помощью команды [az vm create](/cli/azure/vm#create) и аргумента `--size`. 

```azurecli
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Изменение размера виртуальной машины

После развертывания виртуальной машины ее размер можно изменить, чтобы увеличить или уменьшить выделенные ей ресурсы.

Перед изменением размера виртуальной машины проверьте, доступен ли желаемый размер в текущем кластере Azure. Команда [az vm list-vm-resize-options](/cli/azure/vm#list-vm-resize-options) отображает список всех размеров. 

```azurecli
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Если желаемый размер доступен, то размер виртуальной машины можно изменить во включенном состоянии, однако виртуальную машину нужно будет перезагрузить. Используйте команду [az vm resize]( /cli/azure/vm#resize) для изменения размера.

```azurecli
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Если желаемый размер в текущем кластере недоступен, то перед изменением размера виртуальную машину нужно освободить. Используйте команду [az vm deallocate]( /cli/azure/vm#deallocate), чтобы остановить и освободить виртуальную машину. Обратите внимание на то, что после повторного включения виртуальной машины все данные на временном диске могут быть удалены. Кроме того, изменится общедоступный IP-адрес, если только не используется статический IP-адрес. 

```azurecli
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

После освобождения виртуальной машины ее размер можно изменить. 

```azurecli
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

После изменения размера можно запустить будет виртуальную машину.

```azurecli
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Состояния включенной виртуальной машины

Включенная виртуальная машина Azure может находиться в одном из многих состояний. Это состояние отражает текущее состояние виртуальной машины с точки зрения гипервизора. 

### <a name="power-states"></a>Состояния включения

| Состояние включения | Описание
|----|----|
| Starting | Указывает, что виртуальная машина запущена. |
| Выполнение | Указывает, что виртуальная машина работает. |
| Остановка | Указывает, что виртуальная машина останавливается. | 
| Остановлено | Указывает, что виртуальная машина остановлена. За виртуальные машины в остановленном состоянии по-прежнему взимается плата за вычислительные операции.  |
| Отмена выделения | Указывает, что виртуальная машина освобождается. |
| Освобождено | Указывает, что виртуальная машина удалена из гипервизора, но по-прежнему доступна в плоскости управления. За виртуальные машины в освобожденном состоянии не взимается плата за вычислительные операции. |
| - | Указывает, что состояние включенной виртуальной машины неизвестно. |

### <a name="find-power-state"></a>Поиск состояние включения

Чтобы получить состояние конкретной виртуальной машины, используйте команду [az vm get instance-view](/cli/azure/vm#get-instance-view). Необходимо указать допустимое имя виртуальной машины и группы ресурсов. 

```azurecli
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Выходные данные:

```azurecli
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Задачи управления

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать скрипты для автоматизации повторяющихся или сложных задач. С помощью Azure CLI в командной строке или в скриптах можно выполнять множество распространенных задач управления. 

### <a name="get-ip-address"></a>Получение IP-адреса

Эта команда возвращает частный и общедоступный IP-адрес виртуальной машины.  

```azurecli
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Прекращение работы виртуальной машины

```azurecli
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Запуск виртуальной машины

```azurecli
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Удалить группу ресурсов

При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

```azurecli
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы изучили основы создания виртуальной машины и управления ею. Перейдите к следующему руководству, чтобы узнать о дисках виртуальных машин.  

[Управление дисками Azure с помощью Azure CLI](./tutorial-manage-disks.md)
