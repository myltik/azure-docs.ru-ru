---
title: "Балансировка нагрузки в конфигурациях с несколькими IP-адресами с помощью интерфейса командной строки Azure | Документация Майкрософт"
description: "Узнайте, как назначить виртуальной машине несколько IP-адресов с использованием интерфейса командной строки Azure."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 57df4ab0b2a1df6631eb6e67a90f69cebb1dfe75
ms.openlocfilehash: 64748a540b20bbd4b354f0b4e1d7de4a969381c6


---
# <a name="load-balancing-on-multiple-ip-configurations"></a>Балансировка нагрузки в конфигурациях с несколькими IP-адресами

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](load-balancer-multiple-ip-cli.md)
>

В этой статье описывается, как использовать Azure Load Balancer в конфигурации, в которой у каждого виртуального сетевого интерфейса (сетевой карты) имеется несколько IP-адресов. Поддержка нескольких IP-адресов для сетевой карты в настоящее время находится на этапе предварительной версии. Дополнительные сведения см. в разделе [Ограничения](#limitations) этой статьи. Приведенный ниже сценарий иллюстрирует, как эта функция работает с Azure Load Balancer.

В данном сценарии имеются две виртуальные машины под управлением Windows, каждая из которых имеет одну сетевую карту. Для каждой сетевой карты настроено несколько IP-адресов. Каждая виртуальная машина содержит веб-сайты contoso.com и fabrikam.com. Каждый веб-сайт привязан к одной из IP-конфигураций сетевой карты. Мы используем подсистему балансировки нагрузки, чтобы предоставить два интерфейсных IP-адреса, по одному для каждого веб-сайта. Это позволяет направлять трафик в соответствующую IP-конфигурацию для веб-сайта. В данном сценарии используется одинаковый номер порта для обоих внешних интерфейсов, как и для обоих IP-адресов внутренних пулов.

![Схема балансировки нагрузки для сценария](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>Ограничения

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Чтобы зарегистрироваться для получения предварительной версии, отправьте сообщение электронной почты с темой [Несколько IP-адресов](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) , указав идентификатор подписки и описание предполагаемого использования.


## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Инструкции по балансировке нагрузки в конфигурациях с несколькими IP-адресами

Выполните следующие действия, чтобы реализовать сценарий, описанный в этой статье.

1. [Установите и настройте интерфейс командной строки Azure](../xplat-cli-install.md), следуя инструкциям в соответствующей статье, а затем войдите в свою учетную запись Azure.
2. [Создайте группу ресурсов](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) *contosofabrikam*, как описано выше.

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. [Создайте группу доступности](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) для двух виртуальных машин. В рамках данного сценария воспользуйтесь следующей командой.

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Создайте виртуальную сеть](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) *myVNet* и подсеть *mySubnet*.

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [Создайте подсистему балансировки нагрузки](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-and-ip-pools) *mylb*.

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Создайте два динамических общедоступных IP-адреса для интерфейсных IP-конфигураций подсистемы балансировки нагрузки.

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Создайте две интерфейсные IP-конфигурации, *contosofe* и *fabrikamfe*.

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Создайте внутренние пулы адресов, *contosopool* и *fabrikampool*, [пробу](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-health-probe)  - *HTTP* и правила балансировки нагрузки, *HTTPc* и *HTTPf*.

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Выполните приведенную ниже команду и проверьте выходные данные, чтобы убедиться, что [подсистема балансировки нагрузки](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#verify-the-load-balancer) создана правильно.

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Создайте общедоступный IP-адрес](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address) *myPublicIp* и [учетную запись хранения](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) *mystorageaccont1* для первой виртуальной машины VM1, как показано ниже.

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Создайте сетевой интерфейс](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-nic-to-use-with-the-linux-vm) *VM1-NIC* для VM1, добавьте вторую IP-конфигурацию, *VM1-ipconfig2*, и [создайте виртуальную машину](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms), как показано ниже.

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-name myNic --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Повторите шаги 10–11 для второй виртуальной машины.

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount3426
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-name myNic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Наконец, необходимо настроить записи ресурсов DNS, чтобы они указывали на соответствующие интерфейсные IP-адреса подсистемы балансировки нагрузки. Домены можно разместить в Azure DNS. Дополнительные сведения об использовании Azure DNS с подсистемой балансировки нагрузки см. в разделе [Использование Azure DNS с другими службами Azure](../dns/dns-for-azure-services.md).



<!--HONumber=Dec16_HO1-->


