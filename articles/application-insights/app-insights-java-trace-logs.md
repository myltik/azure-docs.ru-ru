---
title: "Изучение журналов трассировки Java в Azure Application Insights | Документация Майкрософт"
description: "Поиск данных трассировки Log4J или Logback в Application Insights"
services: application-insights
documentationcenter: java
author: CFreemanwa
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 086091bb3be6659436ec8e371acbe4b8e86bfacb
ms.openlocfilehash: 3ef5f6240d7e4704118b86fb0cfee8cf502859ff
ms.contentlocale: ru-ru
ms.lasthandoff: 12/13/2016


---
# <a name="explore-java-trace-logs-in-application-insights"></a>Просмотр журналов трассировки Java в Application Insights
Если вы используете Logback или Log4J (версия 1.2 или 2.0) для трассировки, можно настроить автоматическую пересылку журналов в Application Insights, где вы сможете их изучить.

## <a name="install-the-java-sdk"></a>Установка пакета SDK для Java

Установите пакет [SDK Application Insights для Java][java], если это еще не сделано.

(Если не нужно отслеживать HTTP-запросы, можно опустить большую часть XML-файла конфигурации, но необходимо по крайней мере добавить элемент `InstrumentationKey`. Кроме того, следует вызвать метод `new TelemetryClient()` для инициализации пакета SDK.)


## <a name="add-logging-libraries-to-your-project"></a>Добавление в проект библиотеки ведения журналов
*Выберите подходящий метод для проекта.*

#### <a name="if-youre-using-maven"></a>Если вы используете Maven...
Если проект уже настроен для сборки с использованием Maven, добавьте один из следующих фрагментов кода в файл pom.xml.

Затем обновите зависимости проекта, чтобы загрузить двоичные файлы.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J версии 2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J версии 1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[1.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Если вы используете Gradle...
Если проект уже настроен для сборки с использованием Gradle, добавьте одну из следующих строк в группу `dependencies` в файле build.gradle.

Затем обновите зависимости проекта, чтобы загрузить двоичные файлы.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '1.0.+'
```

**Log4J версии 2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '1.0.+'
```

**Log4J версии 1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '1.0.+'
```

#### <a name="otherwise-"></a>В противном случае...
Загрузите и извлеките соответствующий аппендер, а затем добавьте подходящую библиотеку в проект:

| Средство ведения журнала | Загрузить | Библиотека |
| --- | --- | --- |
| Logback |[Пакет SDK с аппендером Logback](https://aka.ms/xt62a4) |applicationinsights-logging-logback |
| Log4J версии 2.0 |[Пакет SDK с аппендером Log4J версии 2.0](https://aka.ms/qypznq) |applicationinsights-logging-log4j2 |
| Log4J версии 1.2 |[Пакет SDK с аппендером Log4J версии 1.2](https://aka.ms/ky9cbo) |applicationinsights-logging-log4j1_2 |

## <a name="add-the-appender-to-your-logging-framework"></a>Добавление аппендера в платформу ведения журнала
Чтобы начать трассировку, добавьте соответствующий фрагмент кода в файл конфигурации Log4J или Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J версии 2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.Log4j">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J версии 1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

К аппендерам Application Insights может обращаться любое сконфигурированное средство ведения журнала, а не только корневое средство ведения журнала (как показано в примерах выше).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Просмотр данных трассировки на портале Application Insights
После настройки проекта для передачи данных трассировки в Application Insights можно выполнять поиск и просмотр этих данных на портале Application Insights в колонке [Поиск][diagnostic].

![На портале Application Insights откройте колонку "Поиск".](./media/app-insights-java-trace-logs/10-diagnostics.png)

## <a name="next-steps"></a>Дальнейшие действия
[Поиск по журналу диагностики][diagnostic]

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md



