---
title: Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с ускоренной сетью.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: aa74596906206ba4460e80af9015955c0b848cd4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины Linux с ускоренной сетью

> [!IMPORTANT] 
> Виртуальные машины должны создаваться с включенной функцией ускорения сети. Эту функцию невозможно включить на имеющихся виртуальных машинах. Чтобы включить функцию ускорения сети, сделайте следующее:
>   1. Удалите виртуальную машину.
>   2. Повторно создайте виртуальную машину с включенной функцией ускорения сети.
>

В этом руководстве вы узнаете, как создать виртуальную машину Linux с ускоренной сетью. Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот высокопроизводительный метод обеспечивает обход узла на пути к данным, что уменьшает задержку, дрожание и нагрузку ЦП. Он предназначен для ресурсоемких рабочих нагрузок сети на виртуальных машинах поддерживаемых типов. На следующем рисунке приведена схема обмена данными между двумя виртуальными машинами с функцией ускорения сети и без нее:

![Сравнение](./media/create-vm-accelerated-networking/accelerated-networking.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторах см. в статье о [виртуализации сети Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, которые применяет виртуальный коммутатор, выгружены и применяются на аппаратном уровне. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом, увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
* **Ubuntu 16.04**: 4.11.0-1013 или более поздняя версия ядра
* **SLES 12 с пакетом обновления 3 (SP3):** 4.4.92–6.18 или более поздняя версия ядра
* **RHEL 7.4**: 7.4.2017120423 или более поздняя версия ядра.
* **CentOS 7.4**: 7.4.20171206 или более поздняя версия ядра.

## <a name="supported-vm-instances"></a>Поддерживаемые экземпляры виртуальных машин
Функция ускорения сети поддерживается для большинства размеров экземпляров, оптимизированных для вычислений, и экземпляров общего назначения с количеством виртуальных ЦП от 4. На таких экземплярах, как D/DSv3 или E/ESv3, поддерживающих технологию Hyper-Threading, функция ускорения сети поддерживается на экземплярах виртуальной машины с количеством виртуальных ЦП от 8.  Поддерживаемые серии: D/DSv2, D/DSv3, E-ESv3, F-Fs-Fsv2 и Ms-Mms. 

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux в Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>регионы
Доступно во всех общедоступных регионах Azure, а также в облаках Azure для государственных организаций.

## <a name="limitations"></a>Ограничения
При использовании этой возможности действуют следующие ограничения:

* **Создание сетевого интерфейса.** Функцию ускорения сети можно включить только в новом сетевом интерфейсе. Ее нельзя включить в имеющемся сетевом интерфейсе.
* **Создание виртуальной машины.** Сетевой интерфейс с включенной функцией ускорения сети можно подключить к виртуальной машине при ее создании. Этого нельзя сделать для имеющейся виртуальной машины. При добавлении виртуальной машины в имеющуюся группу доступности для всех виртуальных машин в группе доступности нужно включить функцию ускорения сети.
* **Развертывание только с помощью Azure Resource Manager:** классические виртуальные машины нельзя развернуть с включенной функцией ускорения сети.

В этой статье описано, как создать виртуальную машину с ускорением работы в сети. Но с помощью Azure CLI вы также можете [создать аналогичную виртуальную машину на портале Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Создавая виртуальную машину с поддерживаемой операционной системой и размером виртуальной машины, на портале в разделе **Параметры** выберите **Включено** для параметра **Ускорение работы в сети**. После создания виртуальной машины необходимо выполнить инструкции в разделе [Подтверждение активации ускоренной сети](#confirm-that-accelerated-networking-is-enabled).

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/reference-index#az_login). В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *myNic* и *myVm*.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *centralus*.

```azurecli
az group create --name myResourceGroup --location centralus
```

Необходимо выбрать поддерживаемый регион Linux, указанный в статье [General availability (Windows) and preview (Linux): Accelerated Networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) (Ускоренная сеть: общедоступная версия (Windows) и предварительная версия (Linux)).

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). В следующем примере создаются виртуальная сеть *myVnet* и одна подсеть.

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Создание группы безопасности сети
Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). В следующем примере создается группа безопасности сети *myNetworkSecurityGroup*.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Группа безопасности сети содержит несколько правил по умолчанию, одно из которых отключает входящий доступ из Интернета. Откройте порт, чтобы разрешить доступ к виртуальной машине по SSH с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Создание сетевого интерфейса с функцией ускорения сети

Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Если вы не планируете входить на виртуальную машину из Интернета, общедоступный IP-адрес не требуется, однако он нужен, чтобы выполнить шаги, описанные в этой статье.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Создайте сетевой интерфейс с включенной функцией ускорения сети, выполнив команду [az network nic create](/cli/azure/network/nic#az_network_nic_create). В следующем примере создается сетевой интерфейс с именем *myNic* в подсети *mySubnet* виртуальной сети *myVnet* и группа безопасности сети *myNetworkSecurityGroup* связывается с сетевым интерфейсом:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>Создание виртуальной машины и подключение сетевого адаптера
При создании виртуальной машины укажите сетевой адаптер, созданный с помощью `--nics`. Необходимо выбрать размер и распределение, указанные в статье [General availability (Windows) and preview (Linux): Accelerated Networking](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) (Ускоренная сеть: общедоступная версия (Windows) и предварительная версия (Linux)). 

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создается виртуальная машина *myVM* с образом UbuntuLTS и размером, поддерживающим ускоренную сеть (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Чтобы просмотреть список всех размеров виртуальных машин и их характеристики, ознакомьтесь со статьей [Размеры виртуальных машин Linux в Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

После создания виртуальной машины возвращается результат, аналогичный приведенному ниже. Запишите значение **publicIpAddress**. Этот адрес используется для доступа к виртуальной машине в последующих шагах.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Подтверждение активации ускоренной сети

Используйте следующую команду для создания сеанса SSH с виртуальной машиной. Замените `<your-public-ip-address>` общедоступным IP-адресом, назначенным созданной виртуальной машине, а также замените *azureuser*, если при создании виртуальной машины вы использовали другое значение для `--admin-username`.

```bash
ssh azureuser@<your-public-ip-address>
```

В окне оболочки Bash введите `uname -r` и убедитесь, что версия ядра соответствует одной из следующих версий или более поздней версии:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Выполнив команду `lspci`, убедитесь, что устройство Mellanox VF находится на виртуальной машине. Выходные данные аналогичны приведенным ниже.

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Проверьте работоспособность виртуальной функции VF с помощью команды `ethtool -S eth0 | grep vf_`. Если будут выведены выходные данные, аналогичные следующим, это значит, что ускоренная сеть включена и работает.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Ускорение работы в сети теперь включено на вашей виртуальной машине.
