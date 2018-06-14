---
title: Не удается удалить виртуальную сеть в Azure | Документация Майкрософт
description: Узнайте, как устранить проблему, когда невозможно удалить виртуальную сеть в Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: d3ffcd9981ff0c57f6d625efcede3e930acd17b5
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072115"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Устранение неполадок: не удалось удалить виртуальную сеть в Azure

При попытке удаления виртуальной сети в Microsoft Azure могут возникать ошибки. В этой статье приведены действия по устранению этой проблемы. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Рекомендации по устранению неполадок 

1. [Проверьте, работает ли шлюз виртуальной сети в виртуальной сети](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Проверьте, работает ли шлюз приложений в виртуальной сети](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Проверьте, включена ли доменная служба Active Directory Azure в виртуальной сети](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Проверьте, подключена ли виртуальная сеть к другому ресурсу](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Проверьте, работает ли виртуальная машина в виртуальной сети](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Проверьте, зависла ли виртуальная сеть в состоянии миграции](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Проверка работы шлюза виртуальной сети в виртуальной сети

Чтобы удалить виртуальную сеть, сначала необходимо удалить шлюз виртуальной сети.

Для классических виртуальных сетей перейдите на страницу **обзора** классической виртуальной сети на портале Azure. В разделе **VPN-подключения**, если шлюз работает в виртуальной сети, отобразится IP-адрес шлюза. 

![Проверка работы шлюза](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Для виртуальных сетей перейдите на страницу **обзора** виртуальной сети. Проверьте **подключенные устройства** шлюза виртуальной сети.

![Проверка подключенных устройств](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Прежде чем удалить шлюз, сначала необходимо удалить объекты **подключений** в шлюзе. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Проверка работы шлюза приложений в виртуальной сети

Перейдите на страницу **обзора** виртуальной сети. Проверьте **подключенные устройства** шлюза приложений.

![Проверка подключенных устройств](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Прежде чем удалить виртуальную сеть, необходимо удалить шлюз приложений (если имеется).

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Проверка, включена ли доменная служба Active Directory Azure в виртуальной сети

Если доменная служба Active Directory включена и подключена к виртуальной сети, вы не сможете удалить эту виртуальную сеть. 

![Проверка подключенных устройств](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Чтобы отключить службу, см. статью об [отключении доменных служб Azure Active Directory с помощью портала Azure](../active-directory-domain-services/active-directory-ds-disable-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Проверка, подключена ли виртуальная сеть к другому ресурсу

Проверьте наличие каналов связи, соединений и пирингов виртуальных сетей. Любой из этих элементов может вызвать сбой удаления виртуальной сети. 

Рекомендуемый порядок удаления выглядит следующим образом:

1. Подключения шлюза.
2. Шлюзы
3. IP-адреса.
4. Пиринги виртуальных сетей.
5. Среда службы приложений (ASE).

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Проверка работы виртуальной машины в виртуальной сети

Убедитесь, что в виртуальной сети нет виртуальной машины.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Проверка, зависла ли виртуальная сеть в состоянии миграции

Если виртуальная сеть зависла в состоянии миграции, ее нельзя удалить. Выполните следующую команду, чтобы прервать миграцию, а затем удалите виртуальную сеть.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Дополнительная информация

- [Виртуальная сеть Azure](virtual-networks-overview.md)
- [Виртуальная сеть Azure: часто задаваемые вопросы](virtual-networks-faq.md)
