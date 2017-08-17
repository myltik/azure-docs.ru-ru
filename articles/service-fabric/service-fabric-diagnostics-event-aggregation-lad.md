---
title: "Агрегирование событий Azure Service Fabric c помощью системы диагностики Azure для Linux | Документы Майкрософт"
description: "Ознакомьтесь со сведениями об агрегировании и сборе событий с использованием LAD для мониторинга и диагностики кластеров Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: bcc3a229369a065cfcfbd32eadbf3f6ae6fe0036
ms.contentlocale: ru-ru
ms.lasthandoff: 07/20/2017

---

# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Агрегирование и сбор событий с помощью системы диагностики Azure для Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Во время работы кластера Azure Service Fabric рекомендуется централизованно собирать журналы со всех узлов. Централизованное хранение журналов упрощает анализ и устранение неполадок в кластере, а также в приложениях и службах, работающих в этом кластере.

Один из способов отправки и сбора журналов заключается в использовании расширения системы диагностики Azure для Linux (LAD), которое отправляет журналы в службу хранилища Azure, а также может отправлять журналы в Azure Application Insights или концентраторы событий. Вы также можете использовать внешний процесс, чтобы считывать события из хранилища и передавать их в платформу обработки, например [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) или другое решение для анализа журналов.

## <a name="log-and-event-sources"></a>Источники журналов и событий

### <a name="service-fabric-platform-events"></a>События платформы Service Fabric
Service Fabric создает несколько готовых журналов посредством [LTTng](http://lttng.org), включая журналы операционных событий и событий среды выполнения. Эти журналы хранятся в расположении, указанном в шаблоне Resource Manager кластера. Чтобы получить или задать сведения об учетной записи хранения, выполните поиск по тегу **AzureTableWinFabETWQueryable** и найдите **StoreConnectionString**.

### <a name="application-events"></a>События приложения
 События, которые создаются кодом ваших приложений и служб согласно вашим указаниям во время инструментирования программного обеспечения. Вы можете использовать любое решение для ведения журналов, которое создает текстовые файлы журнала, например LTTng. Дополнительные сведения см. в описании трассировки вашего приложения в документации по LTTng.

[Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="deploy-the-diagnostics-extension"></a>Развертывание расширения системы диагностики
Первым шагом при сборе журналов является развертывание расширения системы диагностики на каждой виртуальной машине в кластере Service Fabric. Расширение системы диагностики собирает журналы на каждой виртуальной машине и отправляет их в указанную учетную запись хранения. Действия зависят от того, что вы используете — портал Azure или Azure Resource Manager.

Чтобы развернуть расширение системы диагностики на виртуальных машинах в кластере в ходе его создания, переключите параметр **Диагностика** в состояние **Вкл**. После создания кластера изменить этот параметр на портале нельзя.

Затем в системе диагностики Azure для Linux (LAD) настройте сбор файлов и их добавление в свою учетную запись хранения. Этот процесс объясняется в разделе "Сценарий 3. Передача файлов журнала" в статье [Использование диагностического расширения Linux для мониторинга данных о состоянии и производительности виртуальных машин под управлением Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Выполнив эту процедуру, вы получите доступ к трассировкам. Трассировки можно отправлять в средство визуализации по своему усмотрению.

Кроме того, расширение системы диагностики можно развернуть с помощью Azure Resource Manager. Для Windows и Linux применяется один и тот же процесс. Он описан в документации по кластерам Windows в статье [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md).

Также можно использовать Operations Management Suite, как описано в статье [Анализ журналов Operations Management Suite в Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

После завершения настройки агент LAD начнет отслеживать указанные файлы журнала. Каждый раз при добавлении новой стройки в файл он создает запись системного журнала, которая отправляется в указанное вами хранилище.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о том, какие события нужно анализировать при устранении неполадок, см. в [документации по LTTng](http://lttng.org/docs) и статье [Использование диагностического расширения Linux для мониторинга данных о состоянии и производительности виртуальных машин под управлением Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
