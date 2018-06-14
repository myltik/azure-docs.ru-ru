---
title: Архитектура Operations Management Suite (OMS) | Документация Майкрософт
description: Microsoft Operations Management Suite (OMS) — это облачное решение Майкрософт для управления ИТ-средой, которое помогает управлять локальной и облачной инфраструктурой и защищать ее.  В этой статье определяются различные службы в составе OMS и приводятся ссылки на подробные сведения об этих службах.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 40e41686-7e35-4d85-bbe8-edbcb295a534
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: fb4e9150c1069d48399fb217f865b294ccd317dc
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
ms.locfileid: "26683271"
---
# <a name="oms-architecture"></a>Архитектура OMS
[Operations Management Suite](https://azure.microsoft.com/documentation/services/operations-management-suite/) — это набор облачных служб для управления локальными и облачными средами.  В этой статье описаны различные локальные и облачные компоненты OMS, а также приведен обзор архитектуры облачных вычислений.  Более подробные сведения см. в документации для каждой службы.

## <a name="log-analytics"></a>Служба Log Analytics
Все данные, собранные службой [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), хранятся в репозитории OMS, который размещен в среде Azure.  Подключенные источники создают данные, которые собираются и передаются в хранилище OMS.  На данный момент поддерживается три типа подключенных источников.

* Агент, установленный на компьютере с [Windows](../log-analytics/log-analytics-windows-agent.md) или [Linux](../log-analytics/log-analytics-linux-agents.md), подключенный напрямую к OMS.
* Группа управления System Center Operations Manager (SCOM), [подключенная к Log Analytics](../log-analytics/log-analytics-om-agents.md) .  Агенты SCOM продолжают взаимодействовать с серверами управления, которые пересылают события и данные производительности в службу Log Analytics.
* [Учетная запись хранения Azure](../log-analytics/log-analytics-azure-storage.md), выполняющая сбор данных [системы диагностики Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) из рабочей роли, веб-роли или с виртуальной машины в Azure.

Источники данных определяют, какие данные Log Analytics будет собирать из подключенных источников, включая журналы событий и счетчики производительности.  Решения обеспечивают дополнительные функциональные возможности в OMS. Их можно добавить в рабочую область из [каталога решений OMS](../log-analytics/log-analytics-add-solutions.md).  Некоторые решения могут требовать наличия прямого подключения между службой Log Analytics и агентами SCOM, тогда как другие — установку дополнительного агента.

Служба Log Analytics включает в себя веб-портал, который можно использовать для управления ресурсами OMS, добавления и настройки решений OMS, а также просмотра и анализа данных в репозитории OMS.

![Общая архитектура Log Analytics](media/operations-management-suite-architecture/log-analytics.png)

## <a name="azure-automation"></a>Служба автоматизации Azure
[Модули Runbook службы автоматизации Azure](http://azure.microsoft.com/documentation/services/automation) выполняются в облаке Azure и могут получать доступ к ресурсам в Azure и других облачных службах; в свою очередь, они могут быть доступны Интернета.  Вы также можете выделить специальные локальные компьютеры в центре обработки данных, используя [гибридную рабочую роль Runbook](../automation/automation-hybrid-runbook-worker.md), чтобы модули Runbook могли обращаться к локальным ресурсам.

[Конфигурации DSC](../automation/automation-dsc-overview.md) , хранимые в службе автоматизации Azure, можно применить непосредственно к виртуальным машинам Azure.  Другие физические компьютеры и виртуальные машины могут запрашивать конфигурации с опрашивающего сервера DSC службы автоматизации Azure.

Служба автоматизации Azure включает решение OMS, которое выводит на экран статистические данные и ссылки для запуска портала Azure для всех операций.

![Общая архитектура службы автоматизации Azure](media/operations-management-suite-architecture/automation.png)

## <a name="azure-backup"></a>Служба архивации Azure
Защищенные данные в [службе архивации Azure](http://azure.microsoft.com/documentation/services/backup) хранятся в хранилище архивации, расположенном в определенном географическом регионе.  Как правило, данные реплицируются в том же регионе, однако (в зависимости от типа хранилища) репликация может выполняться в другом регионе для обеспечения дополнительной избыточности.

Служба архивации Azure предполагает три основные сценария.

* Компьютер Windows с агентом службы архивации Azure.  Этот сценарий позволяет создавать резервные копии файлов и папок из любой серверной или клиентской системы Windows непосредственно в хранилище резервных копий Azure.  
* System Center Data Protection Manager (DPM) или Microsoft Azure Backup Server. Этот сценарий позволяет использовать DPM или Microsoft Azure Backup Server для резервного копирования файлов и папок в дополнение к рабочим нагрузкам приложений, таких как SQL и SharePoint, в локальном хранилище с последующей репликацией в хранилище резервных копий Azure.
* Расширения виртуальной машины Azure.  Этот сценарий позволяет создавать резервные копии виртуальных машин Azure в хранилище резервных копий Azure.

Служба архивации Azure включает решение OMS, которое выводит на экран статистические данные и ссылки для запуска портала Azure для всех операций.

![Общая архитектура службы Azure Backup](media/operations-management-suite-architecture/backup.png)

## <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) обеспечивает оркестрацию репликации, отработки отказа и восстановления виртуальных машин и физических серверов. Обмен данными репликации осуществляется между узлами Hyper-V, низкоуровневыми оболочками VMware и физическими серверами в основных и дополнительных центрах обработки данных, а также между центром обработки данных и хранилищем Azure.  Azure Site Recovery хранит метаданные в хранилищах, расположенных в определенном географическом регионе Azure. Эта служба не хранит реплицированные данные.

Azure Site Recovery предполагает три основных сценария репликации.

**Репликация виртуальных машин Hyper-V**

* Если управление виртуальными машинами Hyper-V осуществляется в облаках VMM, их можно реплицировать в дополнительный центр обработки данных или в хранилище Azure.  Репликация в Azure выполняется через безопасное интернет-подключение.  Репликация в дополнительный центр обработки данных выполняется по локальной сети.
* Если управление виртуальными машинами Hyper-V выполняется не с помощью VMM, их можно реплицировать только в хранилище Azure.  Репликация в Azure выполняется через безопасное интернет-подключение.

**Репликация виртуальных машин VMWare**

* Виртуальные машины VMware можно реплицировать в дополнительный центр обработки данных под управлением VMware или в хранилище Azure.  Репликация в Azure может выполняться через подключение VPN типа "сеть-сеть", по каналам Azure ExpressRoute или через безопасное интернет-подключение. Репликация в дополнительный центр обработки данных выполняется по каналу данных InMage Scout.

**Репликация физических серверов Windows и Linux** 

* Физические серверы можно реплицировать в дополнительный центр обработки данных или в хранилище Azure. Репликация в Azure может выполняться через подключение VPN типа "сеть-сеть", по каналам Azure ExpressRoute или через безопасное интернет-подключение. Репликация в дополнительный центр обработки данных выполняется по каналу данных InMage Scout.  Azure Site Recovery включает решение OMS, которое выводит на экран некоторые статистические данные, однако для всех операций необходимо использовать портал Azure.

![Общая архитектура Azure Site Recovery](media/operations-management-suite-architecture/site-recovery.png)

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)
* Дополнительные сведения о [службе автоматизации Azure](https://azure.microsoft.com/documentation/services/automation)
* Дополнительные сведения о [службе архивации Azure](http://azure.microsoft.com/documentation/services/backup).
* Дополнительные сведения об [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).

