---
title: "Общие сведения об Operations Management Suite (OMS) | Документация Майкрософт"
description: "Microsoft Operations Management Suite (OMS) — это облачное решение Майкрософт для управления ИТ-средой, которое помогает управлять локальной и облачной инфраструктурой и защищать ее.  В этой статье определяются различные службы в составе OMS и приводятся ссылки на подробные сведения об этих службах."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 4f14a461b842649de977d2c4c80edfc197e15657
ms.openlocfilehash: 14274c7d94213681b3779b97a44296e6eaf90be9


---
# <a name="what-is-operations-management-suite-oms"></a>Что такое Operations Management Suite (OMS)?
Microsoft Operations Management Suite (OMS) — это облачное решение Майкрософт для управления ИТ-средой, которое помогает управлять локальной и облачной инфраструктурой и защищать ее.  Набор OMS реализован как облачная служба, поэтому время, затрачиваемое на подготовку к работе, минимально, как и вложения в службы инфраструктуры.  Новые возможности предоставляются автоматически, что обеспечивает экономию затрат на текущее обслуживание и обновление.

Помимо предоставления ценных служб, OMS поддерживает интеграцию с компонентами System Center, такими как System Center Operations Manager, позволяя распространить существующие инвестиции в управление на облачную среду.  System Center и OMS поддерживают взаимодействие для формирования полнофункциональной гибридной среды управления.

В следующем разделе приводится общее описание различных преимуществ OMS и реализующих их служб.  Перед ознакомлением с подробной документацией по компонентам OMS можно обратиться к статье, посвященной архитектуре OMS, в которой приводится краткий обзор различных компонентов.

## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![Анализ и аналитика](media/operations-management-suite-overview/icon-insight-analytics.png) Анализ и аналитика
Служба [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) помогает собирать и сопоставлять данные журналов и производительности, созданные операционными системами и приложениями, а также выполнять поиск и определенные действия на их основе. Она предоставляет аналитические данные по работе систем в режиме реального времени, используя встроенный поиск и настраиваемые панели мониторинга для быстрого анализа миллионов записей по всем рабочим нагрузкам и серверам независимо от их физического местонахождения.

В службу Log Analytics можно добавить решения для определения собираемых данных и логики для их анализа.  Решения могут включать дополнительные функции, которые автоматически доставляться агентам с минимальными настройками или вообще без них.  В дополнение к использованию инструментов анализа, предоставляемых отдельными решениями, можно выполнить настраиваемый поиск по всему набору данных для сопоставления данных между системами и приложениями.  

## <a name="automation-controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation-control"></a>![Автоматизация и контроль](media/operations-management-suite-overview/icon-automation-control.png) Автоматизация и контроль
Служба автоматизации Azure позволяет автоматизировать административные процессы с помощью [модулей Runbook](../automation/automation-runbook-types.md), основанных на PowerShell и выполняемых в облаке Azure.  Модули Runbook могут получать доступ к любому продукту или службе, которыми можно управлять с помощью PowerShell, включая ресурсы в других облачных средах, таких как Amazon Web Services (AWS).  Модули Runbook также могут выполняться на сервере в локальном центре обработки данных для управления локальными ресурсами.

Служба автоматизации Azure предоставляет функции управления конфигурацией с помощью [PowerShell DSC](../automation/automation-dsc-overview.md).  Вы можете создавать ресурсы DSC, размещенные в Azure, управлять ими и назначать их облачным и локальным системам для определения и автоматического применения конфигурации.

## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![Защита и восстановление](media/operations-management-suite-overview/icon-protection-recovery.png) Защита данных и аварийное восстановление
[Служба архивации Azure](http://azure.microsoft.com/documentation/services/backup) защищает данные приложений и хранит их в течение нескольких лет без каких-либо капитальных затрат и с минимальными операционными затратами.  Она может выполнять резервное копирование данных с физических и виртуальных серверов Windows в дополнение к рабочим нагрузкам приложений, таких как SQL Server и SharePoint.  Кроме того, она может использоваться с System Center Data Protection Manager (DPM) для репликации защищенных данных в Azure для обеспечения избыточности и долговременного хранения.

[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) помогает реализовать стратегию непрерывности бизнес-процессов и аварийного восстановления (BCDR), обеспечивая оркестрацию репликации, отработки отказа и восстановления локальных виртуальных машин Hyper-V, виртуальных машин VMware и физических серверов Windows и Linux. Виртуальные машины можно реплицировать в дополнительный центр обработки данных или расширить имеющийся центр обработки данных, реплицировав их в Azure. Azure Site Recovery также предоставляет простые процедуры отработки отказа и восстановления для рабочих нагрузок. Она интегрируется с механизмами аварийного восстановления, такими как SQL Server AlwaysOn, и предоставляет планы восстановления для простой отработки отказа рабочих нагрузок, которые распределены между несколькими компьютерами.

## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![Безопасность и соответствие требованиям OMS](media/operations-management-suite-overview/icon-security-compliance.png)  Служба "Безопасность и соответствие требованиям"
Служба "Безопасность и соответствие требованиям" помогает выявить, оценить и устранить риски безопасности для вашей инфраструктуры.  Эти функции OMS реализуются с помощью нескольких решений в службе Log Analytics, которые выполняют анализ данных журнала и конфигурации из систем агентов, помогая вам обеспечивать текущую безопасность среды.

* [Решение "Безопасность и аудит"](oms-security-getting-started.md) выполняет сбор и анализ событий безопасности в управляемых системах для выявления подозрительных действий.
* [Антивредоносное решение](../log-analytics/log-analytics-malware.md) предоставляет отчеты о состоянии защиты от вредоносных программ в управляемых системах.  
* Решение для системных обновлений проводит анализ обновлений безопасности и других обновлений в управляемых системах, чтобы помочь вам определить системы, требующие установки исправлений.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics)
* Дополнительные сведения о [службе автоматизации Azure](../automation/automation-intro.md)
* Дополнительные сведения о [службе архивации Azure](http://azure.microsoft.com/documentation/services/backup).
* Дополнительные сведения об [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).




<!--HONumber=Nov16_HO4-->


