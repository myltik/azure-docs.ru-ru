---
title: "Руководства по Azure | Документация Майкрософт"
description: "Рекомендации и руководства для Azure"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Руководства по Azure
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Команда разработчиков шаблонов и рекомендаций Майкрософт является частью команды консультирования клиентов Azure. Наша цель — способствовать успешной работе разработчиков, архитекторов и ИТ-специалистов на платформе Microsoft Azure. Мы разрабатываем руководство с рекомендациями по созданию облачных решений в Azure.

## <a name="checklists"></a>Checklists
Приведенный ниже перечень — это краткий справочник для ознакомления с фундаментальными аспектами доступности и масштабируемости. 

* [Контрольный список для обеспечения доступности][AvailabilityChecklist] 
  
    Сводка рекомендаций по обеспечению доступности.
* [Контрольный список для обеспечения масштабируемости][ScalabilityChecklist]
  
    Сводка рекомендаций по проектированию и реализации масштабируемых служб и управлению обработкой данных.

## <a name="best-practices-articles"></a>Статьи с рекомендациями
В этих статьях подробно рассматриваются важные понятия, часто связываемые с облачными вычислениями. 

* [Руководство по проектированию API][APIDesign] 
  
    Обсуждение проблем проектирования, которые следует учитывать при разработке веб-API.
* [Руководство по реализации API][APIImplementation] 
  
    Набор рекомендаций по реализации и публикации веб-API.
* [Руководство по безопасности API](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    Обсуждение вопросов аутентификации и авторизации (например, типов маркеров, протоколов авторизации, механизмов авторизации и минимизации угроз).
* [Руководство по автоматическому масштабированию][AutoscalingGuidance] 
  
    Сводка рекомендаций по масштабированию решений без ручного вмешательства.
* [Руководство по фоновым заданиям][BackgroundJobsGuidance] 
  
    Описание доступных вариантов и рекомендаций по реализации задач, которые должны выполняться в фоновом режиме, независимо от любых операций переднего плана или интерактивных операций.
* [Руководство по сети доставки содержимого (CDN)][CDNGuidance] 
  
    Общее руководство и рекомендации по использованию CDN, позволяющие свести к минимуму загрузку приложений и обеспечить их максимальную доступность и производительность.
* [Руководство по кэшированию][CachingGuidance] 
  
    Сводка указаний по применению кэширования для повышения производительности и масштабируемости системы.
* [Руководство по секционированию данных][DataPartitioningGuidance]
  
    Стратегии секционирования данных, позволяющие улучшить масштабируемость, уменьшить количество конфликтов и оптимизировать производительность.
* [Руководство по мониторингу и диагностике][MonitoringandDiagnosticsGuidance] 
  
    Руководство по отслеживанию использования системы пользователями, использования ресурсов, а также мониторингу общей работоспособности и производительности системы.
* [Рекомендуемые соглашения об именовании для ресурсов Azure][naming-conventions] 
  
    Рекомендуемые соглашения об именовании для ресурсов Azure.
* [Общие рекомендации по повторным попыткам][RetryGeneralGuidance] 
  
    Обсуждение общих принципов обработки временных сбоев.
* [Конкретные рекомендации по использованию механизма повторов][RetryServiceSpecificGuidance]
  
    Сводка по функциям повторения попыток для многих служб Azure, включая сведения, помогающие использовать, адаптировать или расширять механизм повторения для такой службы.

## <a name="scenario-guides"></a>Руководства по сценариям
* [Руководство по работе с ElasticSearch в Azure][elasticsearch] 
  
    Elasticsearch — это высокомасштабируемая поисковая система и база данных с открытым кодом. Ее можно использовать в сценариях, требующих быстрого анализа и обнаружения информации, хранящейся в больших наборах данных. В этом руководстве рассматриваются некоторые ключевые аспекты, которые следует учитывать при проектировании кластера Elasticsearch.
* [Управление удостоверениями мультитенантных приложений в Microsoft Azure][identity-multitenant] 
  
    Мультитенантной называется архитектура, в которой несколько клиентов совместно используют приложение, но изолированы друг от друга. В этом руководстве показано, как управлять удостоверениями пользователей в мультитенантном приложении, используя [Azure Active Directory][AzureAD] для обработки операций входа и аутентификации.
* [Разработка решений для работы большими данными](https://msdn.microsoft.com/library/dn749874.aspx)
  
    В этом руководстве рассматривается использование HDInsight в таких сценариях, как итеративное исследование, в качестве хранилища данных для процессов ETL, и интеграция с существующими системами бизнес-аналитики. Оно также включает в себя указания к основным понятиям больших данных, планированию и проектированию решений для работы с большими данными и реализации этих решений.

## <a name="patterns"></a>Шаблоны
* [Конструктивные шаблоны облачных решений. Указания по созданию архитектуры для облачных приложений](https://msdn.microsoft.com/library/dn568099.aspx)
  
    Конструктивные шаблоны облачных решений представляют собой библиотеку конструктивных шаблонов и связанных разделов руководства. В этой библиотеке разъясняются преимущества применения шаблонов и демонстрируется роль каждого элемента в архитектурах облачных приложений.
* [Оптимизация производительности для облачных приложений](https://github.com/mspnp/performance-optimization)
  
    В этом руководстве изучаются распространенные неправильные шаблоны, которые затрудняют масштабирование приложений под нагрузкой. Оно включает в себя примеры, показывающие восемь неправильных шаблонов, [основы анализа производительности](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) и руководство по [оценке производительности по ключевым метрикам](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Эталонные архитектуры
Наши эталонные архитектуры упорядочены по сценариям.
Каждая отдельная архитектура включает в себя рекомендации, предписания и исполняемый компонент, в котором воплощены эти рекомендации.

Текущая версия библиотеки эталонных архитектур доступна по адресу: [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Руководство по обеспечению устойчивости
В этих разделах описывается, как проектировать приложения, устойчивые к сбоям в распределенной облачной среде.   

* [Проектирование устойчивых приложений для Azure][ResiliencyOvervew]
  
     Описывается, как на платформе Azure создавать приложения, которые могут восстанавливаться после сбоев и продолжать функционировать. Рассматривается структурный подход к обеспечению устойчивости, начиная с этапа проектирования и заканчивая этапами реализации, развертывания и эксплуатации.
* [Контрольный список для обеспечения устойчивости][resiliency-checklist]
  
    Контрольный список рекомендаций, которые помогут спланировать различные возможные режимы сбоя.
* [Анализ режима сбоя][resiliency-fma] 
  
    Анализ режимов сбоя (FMA) — это процесс реализации устойчивости в системе за счет определения возможных точек сбоя. В качестве отправной точки для процесса FMA эта статья содержит каталог потенциальных режимов сбоя и способов их устранения. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Dec16_HO1-->


