<properties 
	pageTitle="Отслеживание зависимостей, исключений и времени выполнения в веб-приложениях Java" 
	description="Расширенный мониторинг веб-сайта Java с помощью Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# Отслеживание зависимостей, исключений и времени выполнения в веб-приложениях Java

*Доступна только предварительная версия компонента Application Insights.*

[Инструментирование веб-приложения Java с помощью Application Insights][java] позволяет получать более подробную информацию без изменения кода, используя для этого агент для Java.

* **Удаленные зависимости:** данные о вызовах, отправляемых вашим приложением через драйвер [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), такой как MySQL, SQL Server, PostgreSQL или SQLite.
* **Перехваченные исключения:** данные об исключениях, обработанных вашим кодом.
* **Время выполнения метода:** данные о времени, которое потребовалось для выполнения определенных методов.

Чтобы использовать агент для Java, его необходимо установить на сервере. Веб-приложения необходимо оснастить [пакетом SDK для Java Application Insights][java].

## Установка агента Application Insights для Java

1. [Загрузите агент](http://go.microsoft.com/fwlink/?LinkId=618633) на компьютере с сервером Java.
2. Измените скрипт запуска сервера приложений, добавив следующую виртуальную машину Java:

    `javaagent:` *полный путь к файлу агента JAR*

    Например, в Tomcat на компьютере Linux:

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. Перезапустите сервер приложений.

## Настройка агента

Создайте файл с именем `AI-Agent.xml` и поместите его в ту же папку, где находится JAR-файл агента.

Настройка содержимого XML-файла. Измените приведенный ниже пример, включив необходимые функции или убрав ненужные.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true"/>

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
              signature="(Ljava/lang/String:I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

Необходимо включить прием отчетов и контроль времени выполнения отдельных методов.

По умолчанию `reportExecutionTime` имеет значение true, а `reportCaughtExceptions` — значение false.

## Просмотр данных

В ресурсе Application Insights сводные данные по удаленным зависимостям и времени выполнения методов появятся [под плиткой производительности][metrics].

Для поиска отдельных экземпляров отчетов по зависимостям, исключениям и методам откройте [Поиск][diagnostic].

[Дополнительные сведения о диагностировании проблем зависимостей](app-insights-dependencies.md#diagnosis).

## Вопросы? Проблемы?

[Устранение неполадок Java](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=August15_HO6-->