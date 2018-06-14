---
title: Управление облачными службами Azure с помощью службы автоматизации Azure | Документация Майкрософт
description: Способы использования службы автоматизации Azure для управления облачными службами Azure при масштабировании.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: 6b5acac1b8647c324988c316cd5602b3dba98a1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22997989"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Управление облачными службами Azure с помощью службы автоматизации Azure
В этом руководстве будет представлена служба автоматизации Azure и способы ее использования для упрощения управления облачными службами Azure.

## <a name="what-is-azure-automation"></a>Что такое служба автоматизации Azure
[Служба автоматизации Azure](https://azure.microsoft.com/services/automation/) — это служба Azure для упрощения управления облаком путем автоматизации процессов. С помощью службы автоматизации Azure можно автоматизировать длительные, выполняемые вручную, ненадежные и часто повторяющиеся задачи для повышения надежности, эффективности и ускорения вывода продукта на рынок.

Служба автоматизации Azure предоставляет высоконадежную и высокодоступную подсистему выполнения рабочих процессов с масштабированием в соответствии с требованиями организации по мере ее роста. В службе автоматизации Azure процессы можно запустить вручную, в сторонних системах или по расписанию, чтобы все задачи выполнялись в нужное время.

Уменьшите операционные затраты и освободите ИТ-сотрудников и специалистов по разработке и операциям для работы над повышением бизнес-ценности ПО и автоматизации задач управления облаком в службе автоматизации Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Как служба автоматизации Azure помогает управлять облачными службами Azure?
Облачными службами Azure можно управлять в службе автоматизации Azure, используя командлеты PowerShell, доступные в разделе [Инструменты Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Служба автоматизации Azure предоставляет командлеты PowerShell облачных служб Azure в готовом виде, чтобы вы могли выполнять все задачи управления облачными службами, не выходя из службы. Вы также можете связать эти командлеты в службе автоматизации Azure с командлетами для других служб Azure, чтобы автоматизировать сложные задачи в службах Azure и системах сторонних производителей.

В некоторых примерах используется служба автоматизации Azure для управления облачными службами Azure, включая следующее:

* [непрерывное развертывание облачной службы всякий раз, когда в хранилище BLOB-объектов Azure обновляется CSCFG- или CSPKG-файл;](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [параллельная перезагрузка экземпляров облачной службы, по одному домену обновления за раз.](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы познакомились с основами службы автоматизации Azure и способах ее использования для управления облачными службами Azure, пройдите по ссылкам, чтобы получить дополнительные сведения о службе автоматизации Azure.

* [Обзор службы автоматизации Azure](../automation/automation-intro.md)
* [Мой первый Runbook](../automation/automation-first-runbook-graphical.md)
* [План обучения работе со службой автоматизации Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
