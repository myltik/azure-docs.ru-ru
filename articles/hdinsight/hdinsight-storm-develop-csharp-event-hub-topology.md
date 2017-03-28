---
title: "Обработка событий из концентраторов событий с помощью Storm в HDInsight | Документация Майкрософт"
description: "Узнайте, как обрабатывать данные службы концентраторов событий с использованием топологии C# Storm, созданной в Visual Studio с помощью средств HDInsight для Visual Studio."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 23bdde763de6f437a0dec74c51722cbcfc19b141
ms.lasthandoff: 03/07/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)

Служба концентраторов событий Azure позволяет обрабатывать значительные объемы данных из веб-сайтов, приложений и устройств. Воронка службы концентраторов событий упрощает использование Apache Storm в HDInsight для анализа этих данных в режиме реального времени. Вы также можете записывать данные в службу концентраторов событий из Storm с помощью сита службы концентраторов событий.

В этом руководстве вы узнаете, как с помощью шаблонов Visual Studio, установленных вместе со средствами HDInsight для Visual Studio, создать две топологии для работы с концентраторами событий Azure.

* **EventHubWriter**— случайным образом генерирует данные и записывает их в службу концентраторов событий
* **EventHubReader** считывает данные из концентраторов событий и записывает их в журналы Storm.

> [!NOTE] 
> Этот же проект на языке Java рассматривается в статье [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="scpnet"></a>SCP.NET

Эти проекты используют SCP.NET. Это пакет NuGet, который упрощает создание топологий и компонентов C# для использования со Storm в HDInsight.

> [!IMPORTANT]
> Хотя действия, описанные в этой статье, зависят от среды разработки Windows и Visual Studio, готовый скомпилированный проект можно отправить в Storm в кластере HDInsight под управлением Linux. __Топологии SCP.NET поддерживаются только теми кластерами под управлением Linux, которые созданы после 28 октября 2016 года.__

### <a name="cluster-versioning"></a>Управление версиями кластера

Пакет NuGet Microsoft.SCP.Net.SDK, используемый в проекте, должен соответствовать основному номеру версии Storm, установленной в кластере HDInsight. В HDInsight версий 3.3 и 3.4 используется Storm 0.10.x, следовательно, для таких кластеров нужно использовать версию SCP.NET 0.10.x.x. В кластере HDInsight версии 3.5 используется Storm 1.0.x., следовательно для таких кластеров нужно использовать версию SCP.NET 1.0.x.x.

> [!IMPORTANT]
> В HDInsight 3.4 и более поздних версий используется только операционная система Linux. См. дополнительные сведения о [прекращении сопровождения HDInsight в ОС Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

В HDInsight 3.4 и более поздних версий использует Mono для запуска топологий C#. Большинство возможностей будут нормально работать с Mono. Тем не менее вам следует просмотреть документ о [совместимости Mono](http://www.mono-project.com/docs/about-mono/compatibility/), чтобы определить потенциальные проблемы с совместимостью.

Топологии C# должны быть рассчитаны на использование с .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Работа с концентраторами событий

Корпорация Майкрософт предоставляет набор компонентов Java для взаимодействия с концентраторами событий из топологии Storm. JAR-файл, содержащий последнюю версию этих компонентов, можно найти по адресу [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

> [!IMPORTANT]
> Эти компоненты написаны на языке Java, но их можно свободно использовать из топологии C#.

В этом примере используются следующие компоненты:

* __EventHubSpout__: считывает данные из концентраторов событий Azure;
* __EventHubBolt__: записывает данные в концентраторы событий;
* __EventHubSpoutConfig__: используется для настройки EventHubSpout;
* __EventHubBoltConfig__: используется для настройки EventHubBolt;
* __UnicodeEventDataScheme__: позволяет настроить для объекта spout использование кодировки UTF-8 при чтении из концентраторов событий. Кодировка по умолчанию — String.

### <a name="example-spout-usage"></a>Пример использования объекта spout

SCP.NET предоставляет методы для добавления EventHubSpout в топологию. С ними добавить объект spout будет проще, чем с использованием универсальных методов для добавления компонента Java. В следующем примере показано, как создается объект spout с использованием методов __SetEventHubSpout__ и EventHubSpoutConfig, предоставляемых в SCP.NET.

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

В предыдущем примере создается новый компонент spout с именем __EventHubSpout__ и настраивается его взаимодействие с концентратором событий. Для этого компонента задается указание параллелизма, равное числу разделов в концентраторе событий. Это позволяет Storm создать по экземпляру компонента для каждой секции.

> [!WARNING]
> Начиная с 1 января 2017 г. методы SetEventHubSpout и EventHubSpoutConfig создают объект spout, использующий кодировку String для считывания данных из концентраторов событий.

Также для создания объекта spout можно использовать универсальный метод JavaComponentConstructor. Ниже приведен пример создания объекта spout с помощью метода JavaComponentConstructor. Также он демонстрирует, как настроить объект spout для использования кодировки UTF-8 вместо String при чтении данных.

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> В компонентах концентратора событий доступна только версия UnicodeEventDataScheme 9.5; ее можно найти на странице [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

### <a name="example-bolt-usage"></a>Пример использования объекта bolt

Для создания экземпляра bolt необходимо использовать метод JavaComponentConstructor. Следующий пример демонстрирует создание и настройку нового экземпляра EventHubBolt:

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> В этом примере используется выражение Clojure, передаваемое в виде строки, вместо создания объектов EventHubBoltConfig с помощью JavaComponentConstructor, как было предложено выше в примере с объектом spout. Работает каждый из этих методов. Можно использовать любой из этих методов по своему усмотрению.

## <a name="download-the-completed-project"></a>Скачивание полного примера проекта

Вы можете загрузить полную версию проекта, созданного в этом учебнике, с портала GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Но вам нужно будет указать параметры конфигурации, выполнив действия, описанные в этом учебнике.

### <a name="prerequisites"></a>Предварительные требования

* [Кластер Apache Storm в HDInsight версии 3.5](hdinsight-apache-storm-tutorial-get-started.md).

    > [!WARNING]
    > Для примера, используемого в этом документе, требуется Storm в HDInsight версии 3.5. Это не будет работать с более старыми версиями HDInsight из-за критических изменений имени класса. Версия этого примера, которая работает с более старыми кластерами, доступна здесь: [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Концентратор событий Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Пакет SDK Azure .NET](http://azure.microsoft.com/downloads/)

* [Средства HDInsight для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

* Java JDK 1.7 или более поздней версии, в зависимости от используемой среды разработки. Скачиваемые файлы JDK доступны на странице [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * Переменная среды **JAVA_HOME** должна указывать на каталог, содержащий Java.
  * В этом каталоге должен быть вложенный каталог **%JAVA_HOME%/bin**.

## <a name="download-the-event-hub-components"></a>Скачивание компонентов концентратора событий

Воронка и сито распространяются в виде отдельного файла архива Java (JAR) с именем **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, где #.# — это версия файла.

Для использования этого решения HDInsight 3.5 используйте JAR-файл версии 0.9.5 со страницы [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

Создайте каталог `eventhubspout` и сохраните в него файл.

## <a name="configure-event-hubs"></a>Настройка концентраторов событий Azure

Служба концентраторов событий используется в качестве источника данных для этого примера. Используйте сведения в разделе **Создание концентратора событий** статьи [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Создав концентратор событий, просмотрите его колонку на портале Azure и щелкните **Политики общего доступа**. Воспользуйтесь ссылкой **+ Добавить**, чтобы добавить следующие политики.
   
   | Имя | Разрешения |
   | --- | --- |
   | writer |Отправка |
   | reader |Прослушивание |
   
    ![политики](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. Выберите политики **reader** и **writer**. Скопируйте и сохраните значения **первичного ключа** для обеих политик, так как они будут использоваться позже.

## <a name="configure-the-eventhubwriter"></a>Настройка EventHubWriter

1. Если вы еще не установили последнюю версию средств HDInsight для Visual Studio, см. статью [Начало работы со средствами HDInsight для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Скачайте решение из [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. В проекте **EventHubWriter** откройте файл **App.config**. Используйте сведения из концентратора событий, настроенного ранее, чтобы указать значения следующих ключей:
   
   | Ключ | Значение |
   | --- | --- |
   | EventHubPolicyName |writer (если для политики с разрешением на *отправку* указано другое имя, используйте его). |
   | EventHubPolicyKey |Ключ для политики writer |
   | EventHubNamespace |Пространство имен, содержащее концентратор событий |
   | EventHubName |Имя концентратора событий |
   | EventHubPartitionCount |Число разделов в концентраторе событий |

4. Сохраните и закройте файл **App.config**.

## <a name="configure-the-eventhubreader"></a>Настройка EventHubReader

1. Откройте проект **EventHubReader**.

2. Для **EventHubReader** откройте файл **App.config**. Используйте сведения из концентратора событий, настроенного ранее, чтобы указать значения следующих ключей:
   
   | Ключ | Значение |
   | --- | --- |
   | EventHubPolicyName |reader (если для политики с разрешением на *прослушивание* указано другое имя, используйте его). |
   | EventHubPolicyKey |Ключ для политики reader |
   | EventHubNamespace |Пространство имен, содержащее концентратор событий |
   | EventHubName |Имя концентратора событий |
   | EventHubPartitionCount |Число разделов в концентраторе событий |

3. Сохраните и закройте файл **App.config**.

## <a name="deploy-the-topologies"></a>Развертывание топологий

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubReader** и выберите пункт **Submit to Storm on HDInsight** (Отправить в Storm в HDInsight).
   
    ![отправить в storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. В диалоговом окне **Submit Topology** (Отправка топологии) выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Java File Paths** (Пути к файлам Java), щелкните **...** и укажите каталог, содержащий скачанный ранее JAR-файл. Теперь нажмите кнопку **Отправить**.
   
    ![изображение диалогового окна отправки](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. После отправки топологии отобразится **средство просмотра топологий Storm**. Чтобы просмотреть статистику для топологии, выберите топологию **EventHubReader** в левой панели.
   
    ![пример представления хранилища](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubWriter** и выберите пункт **Submit to Storm on HDInsight** (Отправить в Storm в HDInsight).

5. В диалоговом окне **Submit Topology** (Отправка топологии) выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Java File Paths** (Пути к файлам Java), щелкните **...** и укажите каталог, содержащий скачанный вами ранее JAR-файл. Теперь нажмите кнопку **Отправить**.

6. После отправки топологии обновите список топологий в **средстве просмотра топологий Storm** , чтобы убедиться, что обе топологии выполняются в кластере.

7. В **средстве просмотра топологий Storm** выберите топологию **EventHubReader**.

8. Чтобы открыть страницу **Сводка компонентов** для объекта bolt, дважды щелкните компонент **LogBolt** на схеме.

9. В разделе **Executors** (Исполнители) выберите одну из ссылок в столбце **Порт**. Отобразятся данные, записанные компонентом. Записанные данные аналогичны приведенным ниже:
   
        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Остановка топологий

Чтобы остановить топологии, выберите каждую из них в **средстве просмотра топологий Storm**, а затем выберите элемент **Прервать**.

![изображение уничтожения топологии](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом документе рассмотрено использование воронки и сита Java службы концентраторов событий из топологии C# для работы с данными в концентраторе событий Azure. Дополнительные сведения о создании топологий C# см. в следующих статьях.

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Руководство по программированию для SCP](hdinsight-storm-scp-programming-guide.md)
* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)


