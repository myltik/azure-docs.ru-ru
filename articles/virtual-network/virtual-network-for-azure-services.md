---
title: Виртуальная сеть для служб Azure | Документация Майкрософт
description: Сведения о преимуществах развертывания ресурсов в виртуальной сети. Ресурсы в виртуальной сети могут взаимодействовать друг с другом и локальным ресурсом, не передавая трафик через Интернет.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: ecfe3fb6db6b0fb0561e31b3c8aa70b74785b807
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781169"
---
# <a name="virtual-network-integration-for-azure-services"></a>Интеграция виртуальной сети для служб Azure

Интеграция служб Azure в виртуальной сети Azure обеспечивает закрытый доступ из экземпляров службы, развернутой в виртуальной сети.

Службы Azure можно интегрировать с виртуальной сетью такими способами:
- Непосредственное развертывание выделенных экземпляров службы в виртуальной сети. Закрытый доступ к выделенным экземплярам этих служб может осуществляться в виртуальной сети и локальных сетей.
- Расширение виртуальной сети за счет добавления конечной точки службы. Конечные точки позволяют защитить ресурсы отдельной службы в виртуальной сети.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Развертывание служб Azure в виртуальных сетях

С большинством ресурсов Azure можно взаимодействовать в Интернете через общедоступные IP-адреса. При развертывании служб Azure в [виртуальной сети](virtual-networks-overview.md) вы можете взаимодействовать с ресурсами служб в частном порядке через частные IP-адреса.

![Службы, развернутые в виртуальной сети](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Развертывание служб в виртуальной сети обеспечивает следующие возможности:

- Ресурсы в виртуальной сети могут взаимодействовать друг с другом в частном порядке через частные IP-адреса, например непосредственно передавать данные между службой HDInsight и сервером SQL Server, запущенным на виртуальной машине в виртуальной сети.
- Локальные ресурсы могут обращаться к ресурсам в виртуальной сети с помощью частных IP-адресов через [VPN-подключение типа "сеть —сеть" (VPN-шлюз)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) или [канал ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальные сети могут создавать [пиринговую связь](virtual-network-peering-overview.md), что позволяет ресурсам в виртуальной сети взаимодействовать друг с другом через частные IP-адреса.
- Служба Azure полностью управляет экземплярами служб в виртуальной сети. Это позволяет отслеживать работоспособность экземпляров и выполнять необходимое масштабирование на основе нагрузки.
- Экземпляры служб развертываются в выделенной подсети в виртуальной сети. Входящий и исходящий доступ к подсети должен быть открыт посредством [групп безопасности сети](security-overview.md#network-security-groups) для подсети согласно рекомендациям для служб.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Службы, которые можно развернуть в виртуальной сети

Каждая служба, непосредственно развернутая в виртуальной сети, имеет особые требования к маршрутизации и типам разрешенного входящего и исходящего трафика подсетей. Дополнительные сведения можно найти в разделе  
 
- Виртуальные машины: [Windows](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Среда службы приложений](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Кэш Redis](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Управление API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN-шлюз](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Шлюз приложений (внешний)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Служба Azure Kubernetes (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Обработчик службы контейнеров Azure](https://github.com/Azure/acs-engine) с [подключаемым модулем](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI для виртуальной сети Azure
- [Доменные службы Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): только виртуальная сеть (классическая).
- [Пакетная служба Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Облачные службы](https://msdn.microsoft.com/library/azure/jj156091): только виртуальная сеть (классическая).

Вы можете развернуть [внутренний Azure Load Balancer ](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), чтобы балансировать нагрузку приведенных выше ресурсов. В некоторых случаях служба автоматически создает и развертывает подсистему балансировки нагрузки при создании ресурса.

## <a name="service-endpoints-for-azure-services"></a>Конечные точки служб Azure

Некоторые службы Azure невозможно развернуть в виртуальной сети. Вы можете предоставить доступ к некоторым ресурсам службы только определенным подсетям виртуальной сети, включив конечные точки виртуальной сети. Дополнительные сведения о конечных точках служб виртуальной сети см. [здесь](virtual-network-service-endpoints-overview.md).

Сейчас конечные точки службы поддерживаются для следующих служб: 
- **служба хранилища Azure**: [защита учетных записей хранения Azure в виртуальных сетях](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json);
- **База данных SQL Azure**: [защита учетных записей хранения Azure в виртуальных сетях](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Интеграция виртуальной сети с несколькими службами Azure

Вы можете развернуть службы Azure в подсети в виртуальной сети и защитить в ней важные ресурсы. Например, вы можете развернуть в виртуальной сети службу HDInsight и защитить учетную запись хранения в подсети HDInsight.





