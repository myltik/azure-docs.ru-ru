---
title: "Открытие портов для виртуальной машины Linux с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как открыть порт или создать конечную точку для виртуальной машины Windows, используя модель развертывания с помощью Resource Manager и портал Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 979260cdb1ce7f3eaa4b7e05be4c34fce06d0399


---
# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Открытие портов для виртуальной машины в Azure с помощью портала Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Быстрые команды
Эти действия также можно [выполнить с помощью Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Сначала создайте группу безопасности сети. Выберите группу ресурсов на портале, щелкните **Добавить**, а затем найдите и выберите "Группа безопасности сети".

![Добавление группы безопасности сети](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Введите имя группы безопасности сети, выберите или создайте группу ресурсов и выберите расположение. По завершении нажмите кнопку **Создать**.

![Создание группы безопасности сети](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Выберите созданную группу безопасности сети. Выберите "Правила безопасности для входящего трафика" и нажмите кнопку **Добавить**, чтобы создать правило.

![Добавление правила для входящих подключений](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Введите имя для нового правила. По умолчанию уже указан порт 80. В приведенной ниже колонке можно изменить источник, протокол и назначение при добавлении дополнительных правил группы безопасности сети. Нажмите кнопку **ОК** , чтобы создать правило.

![Создание правила для входящих подключений](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

Последний шаг — связывание группы безопасности сети с подсетью или определенным сетевым интерфейсом. Давайте свяжем группу безопасности сети с подсетью. Выберите "Подсети", а затем нажмите кнопку **Привязать**.

![Связывание группы безопасности сети с подсетью](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Выберите виртуальную сеть, а затем — соответствующую подсеть.

![Связывание группы безопасности сети с виртуальной сетью](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Вы создали группу безопасности сети, создали правило для входящих подключений, которое пропускает трафик через порт 80, и связали эту группу с подсетью. Любые виртуальные машины, подключаемые к этой подсети, будут доступны через порт 80.

## <a name="more-information-on-network-security-groups"></a>Дополнительная информация о группах безопасности сети
Приведенные здесь быстрые команды позволят настроить трафик, поступающий в виртуальную машину. Группы безопасности сети предоставляют множество полезных функций и всевозможные настройки для управления доступом к ресурсам. [Здесь](../virtual-network/virtual-networks-create-nsg-arm-ps.md)вы можете больше прочитать о создании группы безопасности сети и правил ACL.

Группы безопасности сети и правила ACL можно также определять в шаблонах Azure Resource Manager. Узнайте больше о [создании групп безопасности сети с помощью шаблонов](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Если необходимо использовать перенаправление портов, чтобы сопоставить уникальный внешний порт с внутренним портом на своей виртуальной машине, то воспользуйтесь балансировщиком нагрузки и правилами преобразования сетевых адресов (NAT). Например, может потребоваться открыть TCP-порт 8080 для доступа извне и направить трафик на TCP-порт 80 виртуальной машины. Вы можете узнать о [создании балансировщика нагрузки с выходом в Интернет](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Дальнейшие действия
В этом примере создано простое правило, разрешающее трафик HTTP. Информацию о создании более детализированных сред можно найти в следующих статьях.

* [Общие сведения об Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Группа безопасности сети](../virtual-network/virtual-networks-nsg.md)
* [Поддержка диспетчера ресурсов Azure для подсистемы балансировки нагрузки](../load-balancer/load-balancer-arm.md)




<!--HONumber=Nov16_HO3-->


