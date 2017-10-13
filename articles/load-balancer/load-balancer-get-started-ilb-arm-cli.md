---
title: "Создание внутренней подсистемы балансировки нагрузки с помощью Azure CLI | Документация Майкрософт"
description: "Узнайте, как создать внутренний балансировщик нагрузки в Resource Manager с помощью Azure CLI."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 83cf027d95018de61ea906268d8f24700203e0c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Создание внутреннего балансировщика нагрузки с помощью Azure CLI

> [!div class="op_single_selector"]
> * [портале Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Шаблон](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье описывается использование модели развертывания c помощью Resource Manager. Для большинства новых развертываний мы рекомендуем использовать эту модель вместо [классической](load-balancer-get-started-ilb-classic-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Развертывание решения с помощью интерфейса командной строки Azure (Azure CLI)

Ниже описана процедура создания балансировщика нагрузки для Интернета с помощью Azure Resource Manager и интерфейса командной строки. Azure Resource Manager позволяет по отдельности создавать и настраивать ресурсы, после чего на их основе создается единый ресурс.

Чтобы развернуть балансировщик нагрузки, необходимо создать и настроить следующие объекты:

* **Конфигурация интерфейсных IP-адресов**: содержит общедоступные IP-адреса для входящего сетевого трафика.
* **Пул внутренних адресов**: содержит сетевые интерфейсы (сетевые карты), которые позволяют виртуальным машинам получать трафик от балансировщика нагрузки.
* **Правила балансировки нагрузки**: содержат правила сопоставления общего порта в балансировщике нагрузки с портом в пуле внутренних адресов.
* **Правила NAT для входящего трафика**: содержат правила сопоставления общего порта в балансировщике нагрузки с портом на конкретной виртуальной машине в пуле внутренних адресов.
* **Пробы**: содержат пробы работоспособности, которые используются для проверки доступности экземпляров виртуальных машин в пуле внутренних адресов.

Дополнительные сведения см. в статье [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Настройка интерфейса командной строки для использования Resource Manager

1. Если вы никогда не использовали Azure CLI, см. статью [Установка Azure CLI](../cli-install-nodejs.md). Следуйте инструкциям до того момента, где выбираются учетная запись и подписка Azure.
2. Выполните команду **azure config mode** , чтобы переключиться в режим Resource Manager, как показано ниже.

    ```azurecli
    azure config mode arm
    ```

    Ожидаемые выходные данные:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Пошаговая инструкция по созданию внутреннего балансировщика нагрузки

1. Войдите в Azure.

    ```azurecli
    azure login
    ```

    При появлении запроса введите свои учетные данные Azure.

2. Измените командные инструменты на режим Azure Resource Manager.

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Все ресурсы в Azure Resource Manager связаны с группой ресурсов. Если вы еще этого не сделали, создайте группу ресурсов.

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>Создание набора внутренних балансировщиков нагрузки

1. Создание внутреннего балансировщика нагрузки

    В следующем сценарии группа ресурсов с именем nrprg создается в восточном регионе США.

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > Все ресурсы для внутреннего балансировщика нагрузки, такие как виртуальные сети и подсети виртуальных сетей, должны находиться в той же группе ресурсов и в том же регионе.

2. Создайте интерфейсный IP-адрес для внутреннего балансировщика нагрузки.

    Используемый IP-адрес должен быть в диапазоне подсети виртуальной сети.

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. Создайте пул внутренних адресов.

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    После определения интерфейсного IP-адреса и пула внутренних адресов можно создать правила балансировщика нагрузки, правила NAT для входящего трафика и настроенные пробы работоспособности.

4. Создайте правило балансировщика нагрузки для внутреннего балансировщика нагрузки.

    Во время выполнения описанных выше действий команда создает правило балансировщика нагрузки для прослушивания порта 1433 в интерфейсном пуле и отправки сетевого трафика со сбалансированной нагрузкой во внутренний пул адресов, также используя порт 1433.

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. Создайте правило NAT для входящих подключений.

    Правила NAT для входящего трафика используются для создания конечных точек в балансировщике нагрузки, которые будут направлены к конкретному экземпляру виртуальной машины. Предыдущие действия создали два правила NAT для удаленного рабочего стола.

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. Создайте пробы работоспособности для балансировщика нагрузки.

    Проба работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность.

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > Для различных сценариев администрирования платформа Microsoft Azure использует статические общедоступные маршрутизируемые IPv4-адреса. IP-адрес — 168.63.129.16. Не блокируйте этот IP-адрес брандмауэрами, поскольку это может привести к непредвиденному поведению.
    > Во внутреннем балансировщике нагрузки Azure этот IP-адрес используется пробами мониторинга для определения состояния работоспособности виртуальных машин в наборе балансировки нагрузки. Если группа безопасности сети используется для ограничения трафика, поступающего на виртуальные машины Azure в наборе внутренней балансировки нагрузки, или если она применяется к подсети виртуальной сети, то убедитесь в наличии правила сетевой безопасности, разрешающего поступление сетевого трафика с адреса 168.63.129.16.

## <a name="create-nics"></a>Создание сетевых адаптеров

Вам необходимо создать сетевые адаптеры (или изменить существующие) и связать их с правилами NAT, правилами балансировщика нагрузки и пробами.

1. Создайте сетевую карту *lb-nic1-be*, а затем свяжите ее с правилом NAT *rdp1* и внутренним пулом адресов *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
    ```

    Ожидаемые выходные данные:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Создайте сетевую карту *lb-nic2-be*, а затем свяжите ее с правилом NAT *rdp2* и внутренним пулом адресов *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. Создайте виртуальную машину *DB1*, а затем свяжите ее с сетевой картой *lb-nic1-be*. Учетная запись хранения *web1nrp* создается перед выполнением следующей команды:

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > Виртуальные машины в балансировщике нагрузки должны находиться в одной группе доступности. Создайте группу доступности с помощью команды `azure availset create` .

4. Создайте виртуальную машину *DB2*, а затем свяжите ее с сетевой картой *lb-nic2-be*. Учетная запись хранения *web1nrp* была создана перед выполнением следующей команды:

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>Удаление балансировщика нагрузки

Чтобы удалить балансировщик нагрузки, используйте следующую команду:

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>Дальнейшие действия

[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

