---
title: Инструменты сообщества для переноса ресурсов из управления службами Azure в Azure Resource Manager
description: В этой статье перечислены инструменты, которые были предоставлены сообществом для помощи с выполнением переноса ресурсов IaaS из управления службами Azure в стек Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: singhkay

---
# Инструменты сообщества для переноса ресурсов из управления службами Azure в Azure Resource Manager
В этой статье перечислены инструменты, которые были предоставлены сообществом для помощи с выполнением переноса ресурсов IaaS из управления службами Azure в стек Azure Resource Manager.

> [!NOTE]
> Эти инструменты официально не поддерживаются службой поддержки Майкрософт. Они представлены на Github с открытым исходным кодом, и мы охотно принимаем запросы на включение внесенных изменений, связанные с исправлениями или дополнительными сценариями. Чтобы сообщить о проблеме, воспользуйтесь вкладкой Issues (Проблемы) на сайте Github.
> 
> Перенос с использованием этих инструментов вызовет простой классической виртуальной машины. Если вы хотите выполнить поддерживаемый платформой перенос, см. следующие статьи:
> 
> * [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
> * [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## ASM2ARM
Это модуль сценария PowerShell для выполнения переноса **одной** виртуальной машины из стека управления службами Azure в стек Azure Resource Manager.

[Ссылка на документацию по инструменту](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## migAz
Инструмент migAz — это еще один способ перенести полный набор ресурсов IaaS управления службами Azure в ресурсы IaaS Azure Resource Manager. Такой перенос может выполняться в пределах одной подписки или между разными подписками и типами подписок (например: подписки CSP).

[Ссылка на документацию по инструменту](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)

<!---HONumber=AcomDC_0831_2016-->