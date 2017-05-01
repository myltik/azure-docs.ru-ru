---
title: "Преобразование виртуальной машины Azure в масштабируемый набор | Документация Майкрософт"
description: "Создание и развертывание масштабируемого набора виртуальных машин Linux в Azure с помощью Azure CLI."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 8d3376d2791b1349298db618d475ce5573083702
ms.lasthandoff: 04/07/2017

---

# <a name="convert-an-existing-azure-virtual-machine-to-a-scale-set"></a>Преобразование существующей виртуальной машины Azure в масштабируемый набор

В этом руководстве показано, как с помощью Azure CLI 2.0 преобразовать виртуальную машину в масштабируемый набор виртуальных машин. Вы также узнаете, как автоматизировать настройку виртуальных машин в масштабируемом наборе. Дополнительные сведения об установке Azure CLI 2.0 см. в разделе [Getting Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) (Приступая к работе с Azure CLI 2.0). Дополнительные сведения о наборах масштабирования см. в разделе [Наборы масштабирования виртуальных машин](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

## <a name="step-1---deprovision-the-vm"></a>Шаг 1. Отзыв виртуальной машины

Используйте SSH, чтобы подключиться к виртуальной машине.

Отзовите виртуальную машину с помощью агента виртуальной машины Azure, чтобы удалить ее файлы и данные. Подробный обзор отзыва представлен в разделе [Запись образа виртуальной машины Linux](capture-image.md).

```bash
sudo waagent -deprovision+user -force
exit
```

## <a name="step-2---capture-an-image-of-the-vm"></a>Шаг 2. Запись образа виртуальной машины

Подробный обзор записи представлен в разделе [Запись образа виртуальной машины Linux](capture-image.md).

Отмените подготовку виртуальной машины командой [az vm deallocate](/cli/azure/vm#deallocate):

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Обобщите виртуальную машину с помощью команды [az vm generalize](/cli/azure/vm#generalize):

```azurecli
az vm generalize --resource-group myResourceGroup --name myVM
```

Создайте образ из ресурса виртуальной машины с помощью команды [az image create](/cli/azure/image#create):

```azurecli
az image create --resource-group myResourceGroup --name myImage --source myVM
```

## <a name="step-3---create-the-scale-set"></a>Шаг 3. Создание масштабируемого набора

Получите **идентификатор** образа.

```azurecli
az image show --resource-group myResourceGroup --name myImage --query id
```

```json
"/subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage"
```

Создайте виртуальную машину из ресурса образа с помощью команды [az vmss create](/cli/azure/vmss#create).

```azurecli
az vmss create --resource-group myResourceGroup --name myScaleSet --image /subscriptions/afbdaf8b-9188-4651-bce1-9115dd57c98b/resourceGroups/vmtest/providers/Microsoft.Compute/images/myImage --upgrade-policy-mode automatic --vm-sku Standard_DS1_v2 --data-disk-sizes-gb 10 --admin-username azureuser --generate-ssh-keys
```

Эта команда также подключает диск данных емкостью в 10 ГБ. Помните, что допустимое количество дисков данных зависит от выбранного размера виртуальной машины (мы использовали **Standard_DS1_v2**). Чтобы узнать больше, ознакомьтесь с [размерами виртуальных машин](sizes.md).

После завершения создания масштабируемого набора подключитесь к нему. Получите список IP-адресов экземпляров для подключения по протоколу SSH, выполнив команду [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info).

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

```json
[
  "52.183.00.000:50000",
  "52.183.00.000:50003"
]
```

Теперь можно подключиться к экземпляру виртуальной машины, чтобы инициализировать диск данных.

```bash
ssh -i ~/.ssh/id_rsa.pub -p 50000 azureuser@52.183.00.000
```

## <a name="step-4---initialize-the-data-disk"></a>Шаг 4. Инициализация диска данных

Подключившись к виртуальной машине, создайте разделы на диске с помощью `fdisk`.

```bash
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | sudo fdisk /dev/sdc
```

Запишите файловую систему на раздел, выполнив команду `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Подключите новый диск, чтобы он был доступен в операционной системе.

```bash
sudo mkdir /datadrive ; sudo mount /dev/sdc1 /datadrive
```

Теперь этот диск доступен через точку подключения диска данных, которую можно проверить с помощью `ls /datadrive/`.

Завершите сеанс SSH.


## <a name="step-5---configure-firewall"></a>Шаг 5. Настройка брандмауэра

Разрешите в брандмауэре трафик веб-сервера, размещенного в масштабируемом наборе. Вместе с масштабируемым набором также была создана подсистема балансировки нагрузки, используемая для подключения по протоколу **SSH** к отдельным виртуальным машинам. Чтобы открыть порт, необходимы два фрагмента информации, которые можно получить с помощью Azure CLI.

* **Интерфейсный пул IP-адресов**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query frontendIpConfigurations[0].name`

* **Внутренний пул IP-адресов**  
`az network lb show --resource-group myResourceGroup --name myScaleSetLB --output table --query backendAddressPools[0].name`

С помощью этих двух имен можно открыть порт **80**.

```azurecli
az network lb rule create --backend-pool-name myScaleSetLBBEPool --backend-port 80 --frontend-ip-name loadBalancerFrontEnd --frontend-port 80 --name webserver --protocol tcp --resource-group myResourceGroup --lb-name myScaleSetLB
```


## <a name="step-6---automate-configuration"></a>Шаг 6. Автоматизация настройки

Диск данных нужно настроить на каждом экземпляре виртуальной машины. Можно автоматизировать настройку виртуальной машины с помощью расширения **CustomScript**.

Сначала создайте *SH*-файл сценария, который содержит команды форматирования диска.

```sh
#!/bin/bash

# Setup the data disk
(echo n; echo p; echo 1; echo  ; echo  ; echo w) | fdisk /dev/sdc
fdisk /dev/sdc
mkfs -t ext4 /dev/sdc1
mkdir /datadrive
mount /dev/sdc1 /datadrive

exit 0
```

Затем передайте этот файл в расположение, доступное расширению **CustomScript**. Копия доступна [здесь](https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4).

Создайте локальный файл **settings.json** и поместите в него следующий блок JSON. В свойстве `flieUris` нужно указать расположение, в которое был передан файл сценария.

```json
{
  "fileUris": ["https://gist.githubusercontent.com/Thraka/ab1d8b78ac4b23722f3d3c1c03ac5df4/raw/3ac6e385010ac675e23ce583ce27b1a752f1b482/prep-vmss.sh"],
  "commandToExecute": "bash prep-vmss.sh" 
}
```

Разверните эту команду в масштабируемом наборе с помощью расширения **CustomScript**, указав только что созданный файл **settings.json**.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

Это расширение автоматически запускается на всех текущих экземплярах и экземплярах, который будут созданы позже при масштабировании.

## <a name="step-7---configure-autoscale-rules"></a>Шаг 7. Настройка правил автомасштабирования

В настоящее время с помощью Azure CLI нельзя задать правила автомасштабирования. Для настройки автомасштабирования используйте [портал Azure](https://portal.azure.com).

## <a name="step-8---management-tasks"></a>Шаг 8. Задачи управления

На протяжении жизненного цикла масштабируемого набора может возникнуть необходимость выполнить одну или несколько задач управления. Кроме того, может потребоваться создать сценарии для автоматизации различных задач жизненного цикла. Azure CLI позволяет сделать это быстро. Ниже приведено несколько распространенных задач.

### <a name="get-connection-info"></a>Получение сведений о подключении

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroup --name myScaleSet
```

### <a name="set-instance-count-manual-scale"></a>Задание числа экземпляров (ручное масштабирование)

```azurecli
az vmss scale --resource-group myResourceGroup --name myScaleSet --new-capacity 4
```

### <a name="delete-resource-group"></a>Удалить группу ресурсов

При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о некоторых функциях масштабируемых наборов виртуальных машин, представленных в этом руководстве, см. в следующих статьях:

- [Общие сведения о масштабируемых наборах виртуальных машин в Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Обзор Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
- [Управление потоком сетевого трафика с помощью групп безопасности сети](../../virtual-network/virtual-networks-nsg.md)
