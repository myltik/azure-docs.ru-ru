<properties linkid="dev-net-commons-tasks-diagnostics" urlDisplayName="Система диагностики" pageTitle="Использование системы диагностики (.NET) — руководство по компонентам Azure" metaKeywords="Мониторинг диагностики Azure, журналы, аварийные дампы, C#" description="Узнайте, как использовать диагностические данные в Azure для отладки, измерения производительности, мониторинга, анализа трафика и многих других задач." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Включение системы диагностики в Azure." authors=""  solutions="" writer="ryanwi" manager="" editor=""  />





# Включение системы диагностики в Azure

Система диагностики Azure позволяет собирать диагностические данные рабочей роли или веб-роли, запущенной в Azure. Диагностические данные можно использовать для отладки и устранения неполадок, измерения производительности, мониторинга использования ресурсов, анализа трафика и планирования производительности, а также в целях аудита.

Систему диагностики можно настроить перед развертыванием или во время выполнения в Visual Studio 2012 или Visual Studio 2013 с помощью пакета Azure SDK 2.0 или более поздней версии.  Дополнительные сведения см. в разделе [Настройка системы диагностики Azure][]. 

Подробнее о создании стратегии ведения журналов и трассировки и об использовании средств диагностики и других методик для устранения неполадок см. в разделе [Советы и рекомендации по устранению неполадок при разработке приложений Azure][].

Сведения о ручной настройке системы диагностики в приложении или об удаленном изменении конфигурации монитора диагностики см. в разделе [Сбор данных журнала с помощью диагностики Azure][].

<h2>Дальнейшие действия</h2>

-	[Удаленное изменение конфигурации монитора диагностики][] — после развертывания приложения вы можете изменить конфигурации диагностики.
-	[Мониторинг облачных служб] [] — вы можете отслеживать ключевые метрики производительности облачных служб на [портале управления Azure][].

<h2>Дополнительные ресурсы</h2> 

- [Сбор данных журнала с помощью системы диагностики Azure][Сбор данных журнала с помощью диагностики Azure]
- [Отладка приложения Azure][]
- [Настройка системы диагностики Azure][]

  [Советы и рекомендации по устранению неполадок при разработке приложений Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh771389.aspx
  

  [Использование счетчиков производительности в Azure]: ../cloud-services-performance-testing-visual-studio-profiler/
  [Мониторинг облачных служб]: ../cloud-services-how-to-monitor/
  [Класс DiagnosticMonitorTraceListener]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx
  [Использование файла конфигурации системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh411551.aspx
  [Справочник по добавлению трассировки невыполненных запросов в конфигурации служб IIS 7.0]: http://www.iis.net/ConfigReference/system.webServer/tracing/traceFailedRequests/add
  [Использование счетчиков производительности в Azure]: /ru-ru/develop/net/common-tasks/performance-profiling/
  [Настройка локальных ресурсов хранения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758708.aspx
  [Обзор ресурсов хранилища с помощью обозревателя сервера]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ff683677.aspx
  [Обозреватель хранилищ Azure]: http://azurestorageexplorer.codeplex.com/
  [Диспетчер диагностики Azure]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [Сбор данных журнала с помощью диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433048.aspx
  [Отладка приложения Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405479.aspx
  [Использование файла конфигурации системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh411551.aspx
  [Перечисление LogLevel]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.diagnostics.loglevel.aspx
  [Метод OnStart]: http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
  [Схема конфигурации службы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/gg593185.aspx
  [Использование службы хранилища таблиц]: http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/table-services/
  [Использование службы хранения больших двоичных объектов Azure]: http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/blob-storage/
  [портале управления Azure]: http://manage.windowsazure.com
  [Удаленное изменение конфигурации монитора диагностики]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg432992.aspx
  [Настройка системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn186185.aspx  
   

