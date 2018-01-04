---
title: "Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт"
description: "Описание способов создания виртуальной машины Linux с помощью Accelerated сетевых."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: cd7889be101e718e309e630a04a2e23b6b5823ac
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины Linux с помощью Accelerated сетевых

В этом учебнике вы узнаете, как для создания виртуальной машины (VM) Linux с Accelerated работа по сети. Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот путь высокой производительности обходит узла из пути к данным, уменьшение задержки, флуктуации и использования ЦП, для использования с наиболее ресурсоемких рабочих нагрузок сети в поддерживаемых типов ВМ. На следующем рисунке показаны связи между двумя виртуальными машинами с и без ускорителем сети:

![Сравнение](./media/create-vm-accelerated-networking/accelerated-networking.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторах см. в статье о [виртуализации сети Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, которые применяет виртуальный коммутатор без этой функции, разгружаются и применяются на аппаратном уровне. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом, увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="limitations"></a>Ограничения
При использовании этой возможности действуют следующие ограничения:

* **Создание сетевого интерфейса.** Функцию ускорения сети можно включить только в новом сетевом интерфейсе. Ее нельзя включить в имеющемся сетевом интерфейсе.
* **Создание виртуальной машины.** Сетевой интерфейс с включенной функцией ускорения сети можно подключить к виртуальной машине при ее создании. Этого нельзя сделать для имеющейся виртуальной машины. Если добавление ВМ в существующую группу, все виртуальные машины в группу доступности должен также accelerated сети включена.
* **Области:** возможность доступна во многих регионах Azure, а также постоянно расширяется. Полный список см. в разделе [обновления виртуальной сети Azure](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview) блога.   
* **Поддерживаемые операционные системы:** 16.04 LTS Ubuntu Server 4.4.0-77 ядра или выше, SLES 12 SP2, RHEL 7.4 и 7.4 CentOS (опубликованные программой Wave мошенник).
* **Размер виртуальной машины.** Экземпляры общего назначения и оптимизированные для вычислений экземпляры с минимум восемью ядрами. Дополнительные сведения см. в разделе [размеры виртуальных Машин Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Набор поддерживаемых размеров экземпляр виртуальной Машины постоянно расширяется.
* **Только при развертывании на диспетчере ресурсов Azure:** с помощью Accelerated сетевых невозможно развернуть виртуальные машины (классические).


## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/#login). В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров включены *myResourceGroup*, *myNic*, и *myVm*.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в *centralus* расположение:

```azurecli
az group create --name myResourceGroup --location centralus
```

Необходимо выбрать в поддерживаемом регионе Linux [Linux accelerated сети](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet#create). В следующем примере создается виртуальная сеть с именем *myVnet* с одной подсетью:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Создание группы безопасности сети
Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg#create). В следующем примере создается группа безопасности сети *myNetworkSecurityGroup*.

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Сетевая группа безопасности содержит несколько правил по умолчанию, один из которых отключает все входящий доступ из Интернета. Откройте порт, чтобы разрешить доступ SSH для виртуальной машины с [создать правило nsg сети az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

## <a name="create-a-network-interface-with-accelerated-networking"></a>Создание сетевого интерфейса ускорителем сети.

Создайте общедоступный IP-адрес с помощью команды [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Общедоступный IP-адрес не требуется, если для доступа к виртуальной машине из Интернета, но для выполнения шагов в данной статье не планируется, это необходимо.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Создание сетевого интерфейса с [Создание сетевого адаптера сети az](/cli/azure/network/nic#create) ускорителем сети включена. В следующем примере создается с именем сетевого интерфейса *myNic* в *mySubnet* подсети *myVnet* виртуальную сеть и связывает  *myNetworkSecurityGroup* группу безопасности сети для сетевого интерфейса:

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

## <a name="create-a-vm-and-attach-the-nic"></a>Создайте виртуальную Машину и подключить сетевой Адаптер
При создании виртуальной Машины, укажите сетевой Адаптер, созданные с помощью `--nics`. Необходимо выбрать размер и распространения перечисленных в [Linux accelerated сети](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создается виртуальная машина с именем *myVM* UbuntuLTS изображения и размер, который поддерживает Accelerated сети (*Standard_DS4_v2*):

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

Список всех размеров виртуальных Машин и характеристики см. в разделе [размеры виртуальных Машин Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

После создания виртуальной Машины, возвращается результат, аналогичный приведенному в следующем примере вывод. Обратите внимание **publicIpAddress**. Этот адрес используется для доступа к ВМ в последующих шагах.

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

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Убедитесь, что включено ускорителем сети

Используйте следующую команду для создания сеанса SSH с виртуальной машиной. Замените `<your-public-ip-address>` с общедоступный IP-адрес, назначенный виртуальной машины, которую вы создали и замените *azureuser* Если вы использовали другое значение для `--admin-username` при создании виртуальной Машины.

```bash
ssh azureuser@<your-public-ip-address>
```

В оболочке Bash введите `uname -r` и убедитесь, что версия ядра одно из следующих версий или выше:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Подтвердите устройства Mellanox VF предоставляется на виртуальную машину с `lspci` команды. Данные, возвращенные похожа на следующий результат:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Проверить действие на VF (виртуальная функция) `ethtool -S eth0 | grep vf_` команды. При получении вывода примерно в следующем примере вывода ускорителем сети включен и работает.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Ускорение работы в сети теперь включено на вашей виртуальной машине.