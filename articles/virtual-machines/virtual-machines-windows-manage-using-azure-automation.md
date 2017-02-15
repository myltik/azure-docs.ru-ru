---
title: "Управление виртуальными машинами с помощью службы автоматизации Azure | Документация Майкрософт"
description: "Способы использования службы автоматизации Azure для управления виртуальными машинами Azure при масштабировании."
services: virtual-machines-windows, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: ce49f83a-f409-42ee-af74-a8ea2caa9ae8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d9c161b58419bb85c7ec473d853db4cbc40dd1


---
# <a name="managing-azure-virtual-machines-using-azure-automation"></a>Управление виртуальными машинами Azure с помощью службы автоматизации Azure
В этом руководстве представлена служба автоматизации Azure и описано, как ее использовать, чтобы упростить управление виртуальными машинами Azure.

## <a name="what-is-azure-automation"></a>Что такое служба автоматизации Azure
[Служба автоматизации Azure](https://azure.microsoft.com/services/automation/) — это служба Azure для упрощения управления облаком путем автоматизации процессов. С помощью службы автоматизации Azure можно автоматизировать длительные, выполняемые вручную, ненадежные и часто повторяющиеся задачи для повышения надежности, эффективности и ускорения вывода продукта на рынок.

Служба автоматизации Azure предоставляет высоконадежную и высокодоступную подсистему выполнения рабочих процессов с масштабированием в соответствии с требованиями организации по мере ее роста. В службе автоматизации Azure процессы можно запустить вручную, в сторонних системах или по расписанию, чтобы все задачи выполнялись в нужное время.

Вы можете уменьшить операционные затраты и освободить сотрудников отделов ИТ и разработки и операций для работы над повышением бизнес-ценности ПО и автоматизации задач управления облаком в службе автоматизации Azure.

## <a name="how-can-azure-automation-help-manage-azure-virtual-machines"></a>Как служба автоматизации Azure может помочь управлять виртуальными машинами Azure
Виртуальными машинами можно управлять с помощью службы автоматизации Azure, используя [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Служба автоматизации Azure включает в себя командлеты PowerShell, позволяющие выполнять все задачи управления виртуальными машинами при помощи этой службы. Вы также можете связать эти командлеты в службе автоматизации Azure с командлетами для других служб Azure, чтобы автоматизировать сложные задачи в службах Azure и системах сторонних производителей.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, ознакомившись с основами службы автоматизации Azure и способами ее использования для управления виртуальными машинами, вы можете узнать больше:

* [Обзор службы автоматизации Azure](../automation/automation-intro.md)
* [Мой первый Runbook](../automation/automation-first-runbook-graphical.md)
* [План обучения работе со службой автоматизации Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)




<!--HONumber=Nov16_HO3-->


