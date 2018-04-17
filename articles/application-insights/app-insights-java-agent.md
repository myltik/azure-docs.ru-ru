---
title: Мониторинг производительности веб-приложений Java в Azure Application Insights | Документация Майкрософт
description: Расширенный мониторинг производительности и использования веб-сайта Java с помощью Application Insights.
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: b327e7f062cdf3e6b1b34a9540461dcb18caf21c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Мониторинг зависимостей, перехваченных исключений и времени выполнения методов в веб-приложениях Java


[Инструментирование веб-приложения Java с помощью Application Insights][java] позволяет получать более подробную информацию без изменения кода, используя для этого агент для Java.

* **Зависимости** — данные о вызовах других компонентов в вашем приложении, включая:
  * **Вызовы REST** через HttpClient, OkHttp и RestTemplate (Spring).
  * Вызовы **Redis** через клиент Jedis.
  * **[Вызовы JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** — автоматически включаются команды MySQL, SQL Server и Oracle DB. Если для MySQL вызов выполняется дольше 10 с, агент сообщает о плане запроса.
* **Перехваченные исключения.** Сведения об исключениях, обработанных вашим кодом.
* **Время выполнения метода.** Сведения о времени, которое потребовалось для выполнения определенных методов.

Чтобы использовать агент для Java, его необходимо установить на сервере. Веб-приложения необходимо инструментировать [пакетом SDK для Java Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Установка агента Application Insights для Java
1. [Скачайте агент](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) на компьютер с сервером Java. Обязательно скачайте агент Java той же версии, что и основной пакет, а также веб-пакеты SDK для Java Application Insights.
2. Измените скрипт запуска сервера приложений, добавив следующую виртуальную машину Java:
   
    `javaagent:`*полный путь к файлу агента JAR*
   
    Например, в Tomcat на компьютере Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Перезапустите сервер приложений.

## <a name="configure-the-agent"></a>Настройка агента
Создайте файл с именем `AI-Agent.xml` и поместите его в ту же папку, где находится JAR-файл агента.

Настройка содержимого XML-файла. Измените приведенный ниже пример, включив необходимые функции или убрав ненужные.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

Необходимо включить прием отчетов и контроль времени выполнения отдельных методов.

По умолчанию `reportExecutionTime` имеет значение true, а `reportCaughtExceptions` — значение false.

## <a name="view-the-data"></a>Просмотр данных
В ресурсе Application Insights сводные данные по удаленным зависимостям и времени выполнения методов отображаются в [элементе "Производительность"][metrics].

Для поиска отдельных экземпляров отчетов по зависимостям, исключениям и методам откройте [Поиск][diagnostic].

[Дополнительные сведения о диагностировании проблем зависимостей](app-insights-asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Вопросы? Проблемы?
* Данные отсутствуют? [Настройте исключения брандмауэра](app-insights-ip-addresses.md)
* [Устранение неполадок Java](app-insights-java-troubleshoot.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
