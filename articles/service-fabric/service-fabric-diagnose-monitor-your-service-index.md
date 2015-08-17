<properties
   pageTitle="Диагностика и устранение неполадок в службе Service Fabric"
   description="Учебники и тематические статьи по мониторингу, диагностике и устранению неполадок со службами Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/21/2015"
   ms.author="ryanwi"/>

# Мониторинг и диагностика состояния службы Service Fabric
Благодаря возможностям мониторинга состояния, а также выявления, диагностики и устранения неполадок службы могут работать практически без перерывов. Дополнительные сведения см. в следующих статьях:

- [Локальный мониторинг и диагностика состояния служб](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Настройка Application Insights для приложения Service Fabric](service-fabric-diagnostics-application-insights-setup.md)
- [Устранение неполадок при обновлении приложения](service-fabric-application-upgrade-troubleshooting.md)
- [Устранение неполадок при обновлении приложения под наблюдением](../service-fabric-application-monitored-upgrade-troubleshooting.md)
- [Диагностика и мониторинг производительности в модели надежных субъектов](service-fabric-reliable-actors-diagnostics.md)
- [Диагностика и мониторинг производительности в модели надежных служб](service-fabric-reliable-services-diagnostics.md)

## Устранение неполадок в работе кластера
Следующие материалы помогут вам устранить неполадки в работе локального кластера разработки:

- [Устранение неполадок в работе кластера локальной разработки](service-fabric-troubleshoot-local-cluster-setup.md)

## Модель обеспечения работоспособности
В Service Fabric используется модель обеспечения работоспособности с широкими и гибкими возможностями оценки состояния сущностей Service Fabric и создания отчетов по ним. Компоненты Service Fabric поддерживают формирование отчетов по всем сущностям без дополнительной настройки. Пользовательские службы могут добавить к сведениям о работоспособности информацию, которая относится к их логике, составляя отчеты о собственном состоянии или состоянии других сущностей в кластере. Дополнительные сведения см. в следующих статьях:

- [Общие сведения о мониторинге работоспособности в Service Fabric](service-fabric-health-introduction.md)
- [Просмотр отчетов о работоспособности Service Fabric](service-fabric-view-entities-aggregated-health.md)
- [Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Добавление настраиваемых отчетов о работоспособности Service Fabric](service-fabric-report-health.md)
 

<!---HONumber=August15_HO6-->