---
title: "Виртуальная сеть для служб Azure | Документация Майкрософт"
description: "Сведения о преимуществах развертывания ресурсов в виртуальной сети. Ресурсы в виртуальной сети могут взаимодействовать друг с другом и локальным ресурсом, не передавая трафик через Интернет."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 36ff77a4205132d1dcc3b743f11791fc5e98e9e7
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="virtual-network-integration-for-azure-services"></a>Интеграция виртуальной сети для служб Azure

Интеграция служб Azure с виртуальной сетью Azure обеспечивает закрытый доступ к ним экземпляров, развернутых в этой виртуальной сети.

Службы Azure можно интегрировать с виртуальной сетью такими способами:
- Непосредственное развертывание выделенных экземпляров службы в виртуальной сети. Доступ к выделенным экземплярам этих служб может осуществляться в частном порядке в виртуальной сети и локальной среде.
- Расширение виртуальной сети за счет добавления конечной точки службы. Это позволяет защитить ресурсы отдельной службы в периметре безопасности виртуальной сети.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Развертывание служб Azure в виртуальных сетях

С большинством ресурсов Azure можно взаимодействовать в Интернете через общедоступные IP-адреса. При развертывании служб Azure в [виртуальной сети](virtual-networks-overview.md) вы можете взаимодействовать с ресурсами служб в частном порядке через частные IP-адреса.


![Службы, развернутые в виртуальной сети](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Развертывание служб в виртуальной сети обеспечивает следующие возможности:

- Ресурсы в виртуальной сети могут взаимодействовать друг с другом в частном порядке через частные IP-адреса, например непосредственно передавать данные между службой HDInsight и сервером SQL Server, запущенным на виртуальной машине в виртуальной сети.
- Локальные ресурсы могут обращаться к ресурсам в виртуальной сети с помощью частных IP-адресов через [VPN типа "сеть —сеть" (VPN-шлюз)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) или [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальные сети могут создавать [пиринговую связь](virtual-network-peering-overview.md), что позволяет ресурсам в виртуальной сети взаимодействовать друг с другом через частные IP-адреса.
- Служба Azure полностью управляет экземплярами служб в виртуальной сети. Это позволяет отслеживать работоспособность экземпляров и выполнять необходимое масштабирование на основе нагрузки.
- Экземпляры служб развертываются в выделенной подсети в виртуальной сети клиента. Входящий и исходящий доступ к подсети должен быть открыт через группы безопасности сети, согласно рекомендациям, предоставленным службами.


### <a name="list-of-services-that-can-be-deployed-into-a-virtual-network"></a>Список служб, которые можно развернуть в виртуальной сети

Каждая служба, непосредственно развернутая в виртуальной сети, имеет особые требования к маршрутизации и типам разрешенного входящего и исходящего трафика подсетей. Дополнительные сведения см. по следующим ссылкам: 
 
- Виртуальные машины: [Windows](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Среда службы приложений](../app-service-web/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Кэш Redis](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Управление API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN-шлюз](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Шлюз приложений (внешний)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Обработчик Службы контейнеров Azure](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json): служба контейнера Azure создает виртуальную сеть по умолчанию. Вы можете создать собственную виртуальную сеть с [обработчиком Службы контейнеров Azure](https://github.com/Azure/acs-engine/tree/master/examples/vnet).
- [Доменные службы Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): только виртуальная сеть (классическая).
- [Пакетная служба Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration): только виртуальная сеть (классическая).
- [Облачные службы](https://msdn.microsoft.com/library/azure/jj156091): только виртуальная сеть (классическая).

Вы можете развернуть [внутреннюю подсистему балансировки нагрузки Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), чтобы балансировать нагрузку приведенных выше ресурсов. В некоторых случаях служба автоматически создает и развертывает подсистему балансировки нагрузки при создании ресурса.

## <a name="service-endpoints-for-azure-services"></a>Конечные точки служб Azure

Некоторые службы Azure невозможно развернуть в виртуальной сети. Вы можете предоставить доступ к некоторым ресурсам службы только определенным подсетям виртуальной сети, включив конечные точки виртуальной сети. Дополнительные сведения о конечных точках служб виртуальной сети см. [здесь](virtual-network-service-endpoints-overview.md).

Сейчас конечные точки поддерживаются в следующих службах: 
- Azure Storage: [защита учетных записей хранения Azure в виртуальных сетях](https://docs.microsoft.com/azure/storage/common/storage-network-security).

- База данных SQL Azure: [защита учетных записей хранения Azure в виртуальных сетях](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Интеграция виртуальной сети с несколькими службами Azure

Вы можете развернуть службы Azure в подсети в виртуальной сети и защитить в ней важные ресурсы. 

Например, вы можете развернуть в виртуальной сети службу HDInsight и защитить учетную запись хранения в подсети HDInsight.






