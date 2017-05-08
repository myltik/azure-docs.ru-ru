---
title: "Добавление шлюза виртуальной сети в виртуальную сеть для канала ExpressRoute с помощью PowerShell в Azure | Документация Майкрософт"
description: "В этой статье рассматривается добавление шлюза виртуальной сети в уже созданную виртуальную сеть Resource Manager для ExpressRoute."
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0fb6b5efc8ad4d40e1b0f41aad678eca6da9798d
ms.lasthandoff: 04/27/2017


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Классическая модель: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

В ней описывается добавление, изменение размера и удаление шлюза виртуальной сети для существующей виртуальной сети. Приведенные действия по настройке предназначены для виртуальных сетей, созданных с помощью модели развертывания Resource Manager, которые будут использоваться в конфигурации ExpressRoute. Дополнительные сведения о шлюзах виртуальных сетей и параметрах конфигурации шлюза для ExpressRoute см. в разделе [Сведения о шлюзах виртуальных сетей ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Подготовка
Убедитесь, что у вас установлена последняя версия командлетов Azure PowerShell. Если вы еще не установили ее, необходимо сделать это перед началом настройки. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
После создания шлюза виртуальной сети вы можете связать виртуальную сеть с каналом ExpressRoute. Ознакомьтесь со статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).


