---
title: Управление хранилищем Azure RemoteApp с помощью службы автоматизации Azure
description: Способы использования службы автоматизации Azure для управления хранилищем Azure при масштабировании.
services: storage, automation
documentationcenter: ''
author: jodoglevy
manager: eamono
editor: ''
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059549"
---
# <a name="managing-azure-storage-using-azure-automation"></a>Управление хранилищем Azure RemoteApp с помощью службы автоматизации Azure
В этом руководстве представлена служба автоматизации Azure и описано, как ее использовать, чтобы упростить управление BLOB-объектами, таблицами и запросами хранилища Azure.

## <a name="what-is-azure-automation"></a>Что такое служба автоматизации Azure
[Служба автоматизации Azure](https://azure.microsoft.com/services/automation/) — это служба Azure для упрощения управления облаком путем автоматизации процессов. С помощью службы автоматизации Azure можно автоматизировать длительные, выполняемые вручную, ненадежные и часто повторяющиеся задачи, чтобы повысить надежность и эффективность, а также ускорить окупаемость инвестиций вашей организации.

Служба автоматизации Azure предоставляет высоконадежную и высокодоступную подсистему выполнения рабочих процессов с масштабированием в соответствии с требованиями организации по мере ее роста. В службе автоматизации Azure процессы можно запустить вручную, в сторонних системах или по расписанию, чтобы все задачи выполнялись в нужное время.

Уменьшите операционные затраты и освободите ИТ-сотрудников и специалистов по разработке и операциям для работы над повышением бизнес-ценности ПО и автоматизации задач управления облаком в службе автоматизации Azure.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Как служба автоматизации Azure может помочь управлять хранилищем Azure
Службой хранилища Azure можно управлять в службе автоматизации Azure с помощью командлетов PowerShell, которые доступны в [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). В службе автоматизации Azure эти командлеты PowerShell уже сразу доступны, поэтому все задачи управления BLOB-объектами, таблицами и запросами можно выполнять из службы. Вы также можете связать эти командлеты в службе автоматизации Azure с командлетами для других служб Azure, чтобы автоматизировать сложные задачи в службах Azure и системах сторонних производителей.

[Коллекция модулей Runbook службы автоматизации Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) содержит модули Runbook различных групп разработки продуктов и сообществ, позволяющие начать работу по автоматизации управления хранилищем Azure, другими службами Azure, а также системами сторонних производителей. Коллекция модулей Runbook содержит:

* [Удаление из службы хранилища Azure больших двоичных объектов, которым определенное число дней, с помощью службы автоматизации](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Скачивание большого двоичного объекта из службы хранилища Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Архивация всех дисков одной виртуальной машине Azure или всех виртуальных машин в облачной службе](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы познакомились с основами службы автоматизации Azure и способами ее использования для управления BLOB-объектами, таблицами и запроса хранилища Azure, пройдите по ссылкам, чтобы получить дополнительные сведения о службе автоматизации Azure.

См. руководство [Создание или импорт модуля Runbook в службе автоматизации Azure](../../automation/automation-creating-importing-runbook.md).

