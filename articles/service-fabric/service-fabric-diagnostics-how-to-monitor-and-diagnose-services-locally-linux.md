<properties
   pageTitle="Локальный мониторинг и диагностика служб, созданных с помощью Azure Service Fabric | Microsoft Azure"
   description="Узнайте, как осуществлять мониторинг и диагностику состояния служб с использованием платформы Microsoft Azure Service Fabric на локальном компьютере для разработки."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>



# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Мониторинг и диагностика состояния служб в локальной среде разработки


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Благодаря возможностям мониторинга состояния, а также выявления, диагностики и устранения неполадок службы могут работать практически без перерывов. Мониторинг и диагностика критически важны в фактически развернутой рабочей среде. Внедрение аналогичной модели при разработке служб гарантирует работу конвейера диагностики при переходе в рабочую среду. Платформа Service Fabric позволяет использовать средства диагностики, которые одинаково хорошо работают как в среде разработки на одном локальном компьютере, так и в условиях реального рабочего кластера.


## <a name="debugging-service-fabric-java-applications"></a>Отладка приложений Java в Service Fabric

Для приложений Java доступно [несколько платформ ведения журналов](http://en.wikipedia.org/wiki/Java_logging_framework) . Так как `java.util.logging` является параметром по умолчанию в среде JRE, он также используется для [примеров кода в GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Далее в этой статье описывается настройка платформы `java.util.logging` . 
 
С помощью java.util.logging журналы приложения можно перенаправлять в память, потоки вывода, файлы консоли или сокеты. Для каждого из этих вариантов существуют обработчики по умолчанию, входящие в состав платформы. Чтобы настроить обработчик файлов для приложения, который будет перенаправлять все журналы в локальный файл, можно создать файл `app.properties` . 

Пример конфигурации приведен в следующем фрагменте кода: 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Файл `app.properties` должен указывать на существующую папку. Затем, после создания файла `app.properties`, необходимо также изменить сценарий точки входа `entrypoint.sh` в папке `<applicationfolder>/<servicePkg>/Code/`, указав в качестве значения свойства `java.util.logging.config.file` файл `app.propertes`. Запись должна выглядеть примерно так:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
В результате применения этой конфигурации журналы будут циклически собираться в папку `/tmp/servicefabric/logs/`. Параметры **%u** и **%g** позволяют создавать дополнительные файлы с именами mysfapp0.log, mysfapp1.log и так далее, а также mysfapplog0.log mysfapp1.log и так далее. Если обработчик не настроен явно, по умолчанию регистрируется обработчик консоли. Просмотреть журналы в системном журнале можно в папке /var/log/syslog.
 
Дополнительные сведения см. на странице с [примерами кода на GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  



## <a name="next-steps"></a>Дальнейшие действия
Код трассировки, добавленный в приложение, также можно использовать для диагностики приложения в кластере Azure. Ознакомьтесь с этими статьями, в которых рассматриваются различные варианты инструментов и описывается, как их настроить.
* [Сбор журналов с помощью системы диагностики Azure](service-fabric-diagnostics-how-to-setup-lad.md)



<!--HONumber=Oct16_HO2-->


