---
title: "Служба архивации в Azure для государственных организаций | Документация Майкрософт"
description: "В этой статье содержится обзор функций службы архивации Azure, доступных в Azure для государственных организаций."
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154


---
# <a name="azure-government-backup"></a>Служба архивации в Azure для государственных организаций

В этой статье содержится обзор службы архивации Azure и представлены функции резервного копирования, доступные в Azure для государственных организаций. Служба архивации Azure — это служба на платформе Azure, используемая для резервного копирования (защиты) данных в Microsoft Cloud. Защита данных в Azure подразумевает не только создание резервной копии в облаке, но и восстановление данных в облаке или в локальной среде. Служба архивации Azure предоставляет следующие ключевые преимущества:

- Автоматическое управление хранилищем
- Неограниченное масштабирование
- Несколько вариантов хранения
- Отсутствие ограничений на передачу данных
- Шифрование данных
- Согласованные с приложениями резервные копии
- Длительный период удержания

Если вы только начали использовать службу архивации Azure и хотите узнать больше о доступных функциях, см. [эту статью](../backup/backup-introduction-to-azure-backup.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Компоненты службы архивации Azure, доступные в службе архивации Azure для государственных организаций

Службу архивации Azure можно использовать для защиты файлов, папок, томов, виртуальных машин, приложений и рабочих нагрузок. Выбор компонентов службы архивации Azure зависит от того, что необходимо защитить и где находятся данные. В следующих разделах есть ссылки на статьи в общедоступной документации по службе архивации Azure для каждого компонента. В одних разделах приведены сведения для классического портала, а в других — для портала Azure. Воспользуйтесь версией для портала Azure, если вы планируете использовать развертывания Resource Manager.

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>Использование Windows Server и компьютеров Windows на портале Azure

- [Архивация сервера или клиентского компьютера Windows в Azure с использованием классической модели развертывания](../backup/backup-configure-vault.md)
- [Восстановление файлов на сервере Windows Server или клиентском компьютере Windows с помощью модели развертывания Resource Manager](../backup/backup-azure-restore-windows-server.md)
- [Мониторинг хранилищ и серверов служб восстановления Azure для компьютеров Windows и управление ими](../backup/backup-azure-manage-windows-server.md)
- [Развертывание резервного копирования в Azure для Windows Server или клиента Windows и управление им с помощью PowerShell](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>Использование Windows Server и компьютеров Windows на классическом портале

- [Архивация сервера или клиентского компьютера Windows в Azure с использованием классической модели развертывания](../backup/backup-configure-vault-classic.md)
- [Восстановление файлов на сервере Windows Server или клиентском компьютере Windows с помощью модели развертывания Resource Manager](../backup/backup-azure-restore-windows-server-classic.md)
- [Мониторинг хранилищ и серверов служб восстановления Azure для компьютеров Windows и управление ими](../backup/backup-azure-manage-windows-server-classic.md)
- [Развертывание резервного копирования в Azure для Windows Server или клиента Windows и управление им с помощью PowerShell](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>Использование виртуальных машин на портале Azure

- [Подготовка среды для резервного копирования виртуальных машин Azure](../backup/backup-azure-arm-vms-prepare.md)
- [Настройка виртуальных машин](../backup/backup-azure-vms-first-look-arm.md)
- [Восстановление виртуальных машин](../backup/backup-azure-arm-restore-vms.md)
- [Управление резервными копиями виртуальных машин Azure и их мониторинг](../backup/backup-azure-manage-vms.md)
- [Развертывание резервной копии виртуальной машины Azure с помощью PowerShell и управление ею](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>Использование виртуальных машин на классическом портале

- [Подготовка среды для резервного копирования виртуальных машин Azure](../backup/backup-azure-vms-prepare.md)
- [Настройка виртуальных машин](../backup/backup-azure-vms-first-look.md)
- [Восстановление виртуальных машин](../backup/backup-azure-restore-vms.md)
- [Управление резервными копиями виртуальных машин Azure и их мониторинг](../backup/backup-azure-manage-vms-classic.md)
- [Развертывание резервной копии виртуальной машины Azure с помощью PowerShell и управление ею](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>Использование System Center Data Protection Manager на портале Azure

- [Подготовка к архивированию рабочих нагрузок в Azure с помощью DPM](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>Использование System Center Data Protection Manager на классическом портале

- [Подготовка к архивированию рабочих нагрузок в Azure с помощью DPM](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>Использование сервера резервного копирования Azure на портале Azure

Сервер резервного копирования Azure — это компонент службы архивации Azure, который работает аналогично System Center Data Protection Manager с единственным исключением, заключающимся в том, что он не сохраняет данные на ленту. Сервер резервного копирования Azure обеспечивает защиту рабочих нагрузок приложения, таких как виртуальные машины Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange и клиенты Windows, в облаке с помощью единой консоли. Лицензия System Center для сервера резервного копирования Azure не требуется.

- [Сервер службы архивации Azure](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>Использование сервера резервного копирования Azure на классическом портале

- [Сервер службы архивации Azure](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>Дальнейшие действия

Если вы не знаете, с чего начать, начните со статьи [Архивация сервера или клиентского компьютера Windows в Azure с использованием классической модели развертывания](../backup/backup-configure-vault-classic.md). В этом руководстве приведены пошаговые инструкции по настройке проекта резервного копирования на компьютере или в Windows Server.

Если вы уже знаете, что будете использовать службу архивации Azure, но хотите узнать ее стоимость, см. [страницу с ценами на архивацию](http://azure.microsoft.com/pricing/details/backup/). Есть также список часто задаваемых вопросов, в которых можно найти полезные сведения. Кроме того, обратите внимание, что в раскрывающемся меню **Регион** есть два региона Azure для государственных организаций.



<!--HONumber=Jan17_HO1-->


