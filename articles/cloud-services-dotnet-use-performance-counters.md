<properties urlDisplayName="Performance Profiling" pageTitle="Использование счетчиков производительности в Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Узнайте, как включить и обеспечить сбор данных от счетчиков производительности в приложениях Azure. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Использование счетчиков производительности в Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Использование счетчиков производительности в Azure

В приложении Azure счетчики производительности можно использовать
для сбора данных, которые позволят определить узкие места системы
и точно оптимизировать производительность системы и приложения. Счетчики производительности, доступные для Windows Server 2008, Windows Server 2012, IIS и ASP.NET, могут быть собраны и использованы для определения работоспособности приложения Azure.

Счетчики производительности можно настроить перед развертыванием или во время выполнения в Visual Studio 2012 или Visual Studio 2013 с помощью пакета Azure SDK 2.0 или более поздней версии. Дополнительную информацию см. в разделе [Настройка системы диагностики Azure][Настройка системы диагностики Azure]. Сведения о ручной настройке счетчиков производительности в приложении см. в разделе [Настройка счетчиков производительности][Настройка счетчиков производительности].

Подробнее о создании стратегии ведения журналов и трассировки и об использовании средств диагностики и других методик для устранения неполадок см. в разделе [Советы и рекомендации по устранению неполадок при разработке приложений Azure][Советы и рекомендации по устранению неполадок при разработке приложений Azure].

## <a name="nextsteps"> </a> Дальнейшие действия

Следуйте этим ссылкам, чтобы узнать, как внедрить более сложные сценарии поиска и устранения неисправностей.

-   [Тестирование производительности облачной службы][Тестирование производительности облачной службы]
-   [Советы и рекомендации по устранению неполадок при разработке приложений Azure][Советы и рекомендации по устранению неполадок при разработке приложений Azure]
-   [Мониторинг облачных служб][Мониторинг облачных служб]
-   [Практическое руководство по использованию блока приложения автомасштабирования][Практическое руководство по использованию блока приложения автомасштабирования]
-   [Создание гибких и устойчивых облачных приложений][Создание гибких и устойчивых облачных приложений]

## <a name="additional"> </a> Дополнительные ресурсы

-   [Включение диагностики в Azure][Включение диагностики в Azure]
-   [Сбор данных журнала с помощью системы диагностики Azure][Сбор данных журнала с помощью системы диагностики Azure]
-   [Отладка приложения Azure][Отладка приложения Azure]

  [Настройка системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dn186185.aspx
  [Настройка счетчиков производительности]: http://msdn.microsoft.com/ru-ru/library/azure/dn535595.aspx
  [Советы и рекомендации по устранению неполадок при разработке приложений Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh771389.aspx
  [Тестирование производительности облачной службы]: http://msdn.microsoft.com/ru-ru/library/azure/hh369930.aspx
  [Мониторинг облачных служб]: http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-how-to-monitor/
  [Практическое руководство по использованию блока приложения автомасштабирования]: http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [Создание гибких и устойчивых облачных приложений]: http://msdn.microsoft.com/ru-ru/library/hh680949(PandP.50).aspx
  [Включение диагностики в Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-dotnet-diagnostics/
  [Сбор данных журнала с помощью системы диагностики Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433048.aspx
  [Отладка приложения Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405479.aspx
