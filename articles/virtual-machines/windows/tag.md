---
title: Как пометить тегом ресурс виртуальной машины Windows в Azure | Документация Майкрософт
description: Узнайте, как добавлять теги к виртуальной машине Windows, созданной в Azure с помощью модели развертывания на основе диспетчера ресурсов.
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 5044bda855b6ac88eb5784f257686bf8a1838222
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Пометка виртуальной машины Windows в Azure
В этой статье описываются разные способы назначения тегов виртуальной машине Windows в Azure с использованием модели развертывания Resource Manager. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящий момент Azure поддерживает до 15 тегов на ресурс или группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Обратите внимание, что теги поддерживаются только для тех ресурсов, которые созданы с помощью модели развертывания Resource Manager. Чтобы назначить тег виртуальной машине Linux, см. статью [Пометка виртуальной машины Linux в Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Маркировка с помощью PowerShell
Чтобы создавать, добавлять и удалять теги с помощью PowerShell, необходимо сначала настроить [среду PowerShell для работы с Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Завершив настройку, вы можете добавлять теги к ресурсам вычисления, сети и хранения во время создания или после него с помощью PowerShell. Эта статья в основном посвящена просмотру и редактированию тегов виртуальных машин.

Для начала перейдите к виртуальной машине с помощью командлета `Get-AzureRmVM` .

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Если виртуальная машина уже содержит теги, вы сможете просмотреть все теги на ресурсе:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Для добавления тегов с помощью PowerShell можно использовать команду `Set-AzureRmResource` . Обратите внимание, что при изменении тегов с помощью PowerShell обновляются все теги. Поэтому, если вы добавляете один тег к ресурсу, который уже содержит теги, вам потребуется включить все теги, которые нужно добавить к ресурсу. Ниже представлен пример добавления тегов к ресурсу с помощью командлетов PowerShell.

Первый командлет определяет переменную *$tags* для всех тегов в виртуальной машине *MyTestVM* с помощью свойств `Get-AzureRmResource` и `Tags`.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Вторая команда выводит на экран теги для данной переменной.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

Третья команда добавляет дополнительный тег к переменной *$tags* . Обратите внимание, что оператор **+=** используется для присоединения пары "ключ — значение" к списку *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

Четвертая команда устанавливает все теги, определенные в переменной *$tags* , на данном ресурсе. В нашем примере это MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Пятая команда выводит на экран все теги на ресурсе. Как видите, теперь *Расположение* определяется как тег со значением *MyLocation*.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Дополнительные сведения об отметке тегами с помощью PowerShell см. в статье [Командлеты ресурсов Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager][Azure Resource Manager Overview] и [Использование тегов для организации ресурсов в Azure][Using Tags to organize your Azure Resources].
* Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure][Understanding your Azure Bill] и [Получение ценных сведений о потреблении ресурсов Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
