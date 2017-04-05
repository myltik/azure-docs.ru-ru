---
title: "Средства сообщества. Перенос классических ресурсов в развертывание с помощью Azure Resource Manager | Документы Microsoft"
description: "В этой статье перечислены инструменты, которые были предоставлены сообществом для упрощения переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5e2ad5e5eae97645368797c8cdf848d88719bb64
ms.lasthandoff: 03/29/2017


---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Инструменты сообщества для переноса ресурсов IaaS из классической модели в модель Azure Resource Manager
В этой статье перечислены инструменты, которые были предоставлены сообществом, чтобы упростить перенос ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager.

> [!NOTE]
> Эти инструменты официально не поддерживаются службой поддержки Майкрософт. Они представлены на GitHub с открытым кодом, и мы охотно принимаем запросы на включение внесенных изменений, связанные с исправлениями или дополнительными сценариями. Чтобы сообщить о проблеме, воспользуйтесь вкладкой "Issues" (Проблемы) на сайте GitHub.
> 
> Перенос с использованием этих инструментов вызовет простой классической виртуальной машины. Если вы хотите выполнить поддерживаемый платформой перенос, см. следующие статьи: 
> 
> * [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
> * [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
Это модуль сценария PowerShell для миграции **отдельной** виртуальной машины из классической модели развертывания в модель развертывания с помощью Azure Resource Manager. 

[Ссылка на документацию по инструменту](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
Инструмент migAz — это еще один способ перенести полный набор классических ресурсов IaaS в ресурсы IaaS Azure Resource Manager. Такой перенос может выполняться в пределах одной подписки или между разными подписками и типами подписок (например: подписки CSP).

[Ссылка на документацию по инструменту](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)


