---
title: Использование локального шлюза данных для источников данных виртуальной сети Azure | Документация Майкрософт
description: Узнайте, как настроить сервер, чтобы использовать шлюз для источников данных в виртуальной сети.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7cb1fb7bcd3a50532d5ff994afac56e226fb2018
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596912"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Использование шлюза для источников данных в виртуальной сети Azure

В этой статье описывается свойство сервера **AlwaysUseGateway**, которое следует использовать при размещении источников данных в [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Доступ сервера к источникам данных виртуальной сети

Если доступ к источникам данных осуществляется через виртуальную сеть, сервер служб Azure Analysis Services должен подключаться к этим источникам данных так, как если бы они размещались в вашей локальной среде. Свойство сервера **AlwaysUseGateway** позволяет указать сервер для доступа ко всем источникам данных через [локальный шлюз](analysis-services-gateway.md). 

> [!NOTE]
> Это свойство действует только в тех случаях, если установлен и настроен [локальный шлюз данных](analysis-services-gateway.md). Шлюз может находиться в виртуальной сети.

## <a name="configure-alwaysusegateway-property"></a>Настройка свойства AlwaysUseGateway

1. Последовательно выберите SSMS > сервер > **Свойства** > **Общие** > **Показать дополнительные (все) свойства**.
2. Для параметра **ASPaaS\AlwaysUseGateway** выберите **true**.

    ![Свойство AlwaysUseGateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>См. также
[Подключение к локальным источникам данных](analysis-services-gateway.md)   
[Установка и настройка локального шлюза данных](analysis-services-gateway-install.md)   
[Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md)   

