---
title: "Как добавить тег к виртуальной машине Linux | Документация Майкрософт"
description: "Узнайте, как добавлять теги к виртуальной машине Linux, созданной в Azure с помощью модели развертывания на основе диспетчера ресурсов."
services: virtual-machines-linux
documentationcenter: 
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6e39b13de1808ebb1d0571ab0c1c620261046d0d


---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Пометка виртуальной машины Linux в Azure
В этой статье описываются разные способы назначения тегов виртуальной машине Linux в Azure с использованием модели развертывания Resource Manager. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящий момент Azure поддерживает до 15 тегов на ресурс или группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Обратите внимание, что теги поддерживаются только для тех ресурсов, которые созданы с помощью модели развертывания Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Отметка тегами с помощью интерфейса командной строки Azure
Сначала [установите и настройте интерфейс командной строки Azure](../xplat-cli-azure-resource-manager.md). При этом обязательно включите режим Resource Manager (`azure config mode arm`).

Вы можете просмотреть все свойства определенной виртуальной машины, включая теги, с помощью следующей команды:

        azure vm show -g MyResourceGroup -n MyTestVM

Чтобы добавить новый тег виртуальной машины через интерфейс командной строки Azure, можно применить команду `azure vm set` с параметром тега **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Чтобы удалить все теги, используйте в команде `azure vm set` параметр **–T**.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Теперь, когда мы применили теги к ресурсам с помощью Azure CLI и портала, рассмотрим сведения об использовании, чтобы увидеть теги на портале выставления счетов.

[!INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения о диспетчере ресурсов Azure][Общие сведения об Azure Resource Manager] и [Использование тегов для организации ресурсов в Azure][Использование тегов для организации ресурсов в Azure]
* Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure][Расшифровка счета за использование Microsoft Azure] и [Получение ценных сведений о потреблении ресурсов Microsoft Azure][Получение ценных сведений о потреблении ресурсов Microsoft Azure].

[Интерфейс командной строки Azure]: ./xplat-cli-azure-resource-manager.md
[Общие сведения об Azure Resource Manager]: ../azure-resource-manager/resource-group-overview.md
[Использование тегов для организации ресурсов в Azure]: ../resource-group-using-tags.md
[Расшифровка счета за использование Microsoft Azure]: ../billing/billing-understand-your-bill.md
[Получение ценных сведений о потреблении ресурсов Microsoft Azure]: ../billing-usage-rate-card-overview.md



<!--HONumber=Nov16_HO3-->


