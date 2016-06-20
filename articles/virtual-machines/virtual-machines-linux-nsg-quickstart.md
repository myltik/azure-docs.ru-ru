<properties
   pageTitle="Разрешение внешнего доступа к виртуальной машине Linux | Microsoft Azure"
   description="Узнайте, как открыть порт или создать конечную точку, которая обеспечивает внешний доступ к виртуальной машине Linux, используя модель развертывания с помощью Resource Manager и Azure CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="iainfou"/>

# Разрешение внешнего доступа к виртуальной машине
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Быстрые команды
Для создания группы безопасности сети и правил потребуется [Azure CLI](../xplat-cli-install.md) в режиме Resource Manager (`azure config mode arm`).

Создайте группу безопасности сети, указав собственные имена и расположение.

```
azure network nsg create --resource-group TestRG --name TestNSG --location westus
```

Добавьте правило, разрешающее трафик HTTP для вашего веб-сервера (его можно настроить для собственного сценария; это может быть доступ по протоколу SSH или подключение к базе данных).

```
azure network nsg rule create --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow --resource-group TestRG --nsg-name TestNSG --name AllowHTTP
```

Свяжите группу безопасности сети с сетевым интерфейсом виртуальной машины.

```
azure network nic set --resource-group TestRG --name TestNIC --network-security-group-name TestNSG
```

Кроме того, можно связать группу безопасности сети с подсетью виртуальной сети, а не только с сетевым интерфейсом на отдельной виртуальной машине.

```
azure network vnet subnet set --resource-group TestRG --name TestSubnet --network-security-group-name TestNSG
```

## Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и степеней детализации для управления доступом к ресурсам. [Здесь](../virtual-network/virtual-networks-create-nsg-arm-cli.md) вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Группы безопасности сети и правила ACL можно также определить в шаблонах Azure Resource Manager. Узнайте больше о [создании групп безопасности сети с помощью шаблонов](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Если необходимо использовать перенаправление портов, чтобы сопоставить уникальный внешний порт с внутренним портом на своей виртуальной машине, нужно воспользоваться балансировщиком нагрузки и правилами преобразования сетевых адресов (NAT). Например, может потребоваться открыть TCP-порт 8080 для доступа извне и направить трафик на TCP-порт 80 виртуальной машины. Вы можете узнать о [создании балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## Дальнейшие действия
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

- [Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)
- [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md)
- [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](../load-balancer2 /load-balancer-arm.md)

<!---HONumber=AcomDC_0608_2016-->