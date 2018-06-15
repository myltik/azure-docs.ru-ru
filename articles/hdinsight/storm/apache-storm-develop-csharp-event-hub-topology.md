---
title: Обработка событий из концентраторов событий Azure с помощью Storm в HDInsight | Документация Майкрософт
description: Узнайте, как обрабатывать данные службы концентраторов событий Azure с использованием топологии C# Storm, созданной в Visual Studio с помощью средств HDInsight для Visual Studio.
services: hdinsight,notification hubs
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: ''
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 475a63937ed09b4ce875465c0eb3488ce92c38be
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31419546"
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)

Узнайте, как работать с концентраторами событий Azure из Apache Storm в HDInsight. В этом документе для чтения данных из концентраторов событий и записи в него используется топология Storm на C#.

> [!NOTE]
> Этот же проект на языке Java рассматривается в статье [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Действия, описанные в этом документе, используют SCP.NET. Это пакет NuGet, который упрощает создание топологий и компонентов C# для использования со Storm в HDInsight.

> [!IMPORTANT]
> Хотя действия, описанные в этой статье, зависят от среды разработки Windows и Visual Studio, готовый скомпилированный проект можно отправить в Storm в кластере HDInsight под управлением Linux. Топологии SCP.NET поддерживаются только теми кластерами под управлением Linux, которые созданы после 28 октября 2016 года.

В HDInsight 3.4 и более поздних версий использует Mono для запуска топологий C#. Для примера в этом документе используется HDInsight 3.6. Если вы планируете создавать собственные решения .NET для HDInsight, вам следует просмотреть документ о [совместимости Mono](http://www.mono-project.com/docs/about-mono/compatibility/), чтобы определить потенциальные проблемы с совместимостью.

### <a name="cluster-versioning"></a>Управление версиями кластера

Пакет Microsoft.SCP.Net.SDK NuGet, который вы используете для своего проекта, должен соответствовать основному номеру версии Storm, установленной в HDInsight. В HDInsight версий 3.5 и 3.6 используется Storm 1.x, следовательно, для таких кластеров нужно использовать версию SCP.NET 1.0.x.x.

> [!IMPORTANT]
> Пример в этом документе предполагает наличие кластера HDInsight 3.5 или 3.6.
>
> Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Топологии C# должны быть рассчитаны на использование с .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Работа с концентраторами событий

Корпорация Майкрософт предоставляет набор компонентов Java для взаимодействия с концентраторами событий из топологии Storm. Файл архива Java (JAR), который содержит версии этих компонентов, совместимые с HDInsight 3.6, можно найти по адресу [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Эти компоненты написаны на языке Java, но их можно свободно использовать из топологии C#.

В этом примере используются следующие компоненты:

* __EventHubSpout__: считывает данные из концентраторов событий Azure;
* __EventHubBolt__: записывает данные в концентраторы событий;
* __EventHubSpoutConfig__: используется для настройки EventHubSpout;
* __EventHubBoltConfig__: используется для настройки EventHubBolt;

### <a name="example-spout-usage"></a>Пример использования объекта spout

SCP.NET предоставляет методы для добавления EventHubSpout в топологию. С ними добавить объект spout будет проще, чем с использованием универсальных методов для добавления компонента Java. В следующем примере показано, как создается объект spout с использованием методов __SetEventHubSpout__ и **EventHubSpoutConfig**, предоставляемых в SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

В предыдущем примере создается новый компонент spout с именем __EventHubSpout__ и настраивается его взаимодействие с концентратором событий. Для этого компонента задается указание параллелизма, равное числу разделов в концентраторе событий. Это позволяет Storm создать по экземпляру компонента для каждой секции.

### <a name="example-bolt-usage"></a>Пример использования объекта bolt

Для создания экземпляра bolt необходимо использовать метод **JavaComponentConstructor**. Следующий пример демонстрирует создание и настройку нового экземпляра **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]
> В этом примере используется выражение Clojure, передаваемое в виде строки, вместо создания объектов **EventHubBoltConfig** с помощью **JavaComponentConstructor**, как было предложено выше в примере с объектом spout. Работает каждый из этих методов. Можно использовать любой из этих методов по своему усмотрению.

## <a name="download-the-completed-project"></a>Скачивание полного примера проекта

Вы можете загрузить полную версию проекта, созданного в этом руководстве, с сайта [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Но вам нужно будет указать параметры конфигурации, выполнив действия, описанные в этом учебнике.

### <a name="prerequisites"></a>предварительным требованиям

* [Кластер Apache Storm в HDInsight версии 3.5 или 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > Для примера, используемого в этом документе, требуется кластер Storm в HDInsight версии 3.5 или 3.6. Это не будет работать с более старыми версиями HDInsight из-за критических изменений имени класса. Версию этого примера, которая работает со старыми кластерами, можно найти на сайте [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* [Концентратор событий Azure](../../event-hubs/event-hubs-create.md).

* [Пакет Azure SDK для .NET](http://azure.microsoft.com/downloads/).

* [Средства HDInsight для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Пакет Java JDK 1.8 или более поздней версии, в зависимости от используемой среды разработки. Скачиваемые файлы JDK доступны на сайте [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * Переменная среды **JAVA_HOME** должна указывать на каталог, содержащий Java.
  * В этом каталоге должен быть вложенный каталог **%JAVA_HOME%/bin**.

## <a name="download-the-event-hubs-components"></a>Скачивание компонентов концентраторов событий

Скачайте компоненты bolt или spout службы "Концентраторы событий" по адресу [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Создайте каталог `eventhubspout` и сохраните в него файл.

## <a name="configure-event-hubs"></a>Настройка концентраторов событий Azure

Служба концентраторов событий используется в качестве источника данных для этого примера. Используйте сведения в разделе "Создание концентратора событий" блока о [начале работы с концентраторами событий](../../event-hubs/event-hubs-create.md).

1. Создав **концентратор событий**, просмотрите его параметры на портале Azure и щелкните **Политики общего доступа**. Воспользуйтесь ссылкой **+ Добавить**, чтобы добавить следующие политики.

   | ИМЯ | Разрешения |
   | --- | --- |
   | writer |Отправка |
   | reader |Прослушивание |

    ![Снимок экрана окна политик общего доступа](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Выберите политики **reader** и **writer**. Скопируйте и сохраните значение первичного ключа для обеих политик, так как они будут использоваться позже.

## <a name="configure-the-eventhubwriter"></a>Настройка EventHubWriter

1. Если вы еще не установили последнюю версию средств HDInsight для Visual Studio, см. статью [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Скачайте решение из [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. В проекте **EventHubWriter** откройте файл **App.config**. Используйте сведения из концентратора событий, настроенного ранее, чтобы указать значения следующих ключей:

   | Ключ | Значение |
   | --- | --- |
   | EventHubPolicyName |writer (если для политики с разрешением на *отправку* указано другое имя, используйте его). |
   | EventHubPolicyKey |Ключ для политики writer. |
   | EventHubNamespace |Пространство имен, содержащее концентратор событий. |
   | EventHubName |Имя концентратора событий. |
   | EventHubPartitionCount |Число разделов в концентраторе событий. |

4. Сохраните и закройте файл **App.config**.

## <a name="configure-the-eventhubreader"></a>Настройка EventHubReader

1. Откройте проект **EventHubReader**.

2. Для **EventHubReader** откройте файл **App.config**. Используйте сведения из концентратора событий, настроенного ранее, чтобы указать значения следующих ключей:

   | Ключ | Значение |
   | --- | --- |
   | EventHubPolicyName |reader (если для политики с разрешением на *прослушивание* указано другое имя, используйте его). |
   | EventHubPolicyKey |Ключ для политики reader. |
   | EventHubNamespace |Пространство имен, содержащее концентратор событий. |
   | EventHubName |Имя концентратора событий. |
   | EventHubPartitionCount |Число разделов в концентраторе событий. |

3. Сохраните и закройте файл **App.config**.

## <a name="deploy-the-topologies"></a>Развертывание топологий

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubReader** и выберите пункт **Отправить в Storm в HDInsight**.

    ![Снимок экрана с обозревателем решений с выделенной функцией "Отправить в Storm в HDInsight"](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. В диалоговом окне **Submit Topology** (Отправка топологии) выберите **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Java File Paths** (Пути к файлам Java), щелкните **...** и укажите каталог, содержащий скачанный ранее JAR-файл. Теперь нажмите кнопку **Отправить**.

    ![Снимок экрана с диалоговым окном Submit Topology (Отправка топологии)](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. После отправки топологии отобразится **средство просмотра топологий Storm**. Чтобы просмотреть статистику для топологии, выберите топологию **EventHubReader** в левой панели.

    ![Снимок экрана средства просмотра топологий Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubWriter** и выберите пункт **Отправить в Storm в HDInsight**.

5. В диалоговом окне **Submit Topology** (Отправка топологии) выберите **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Java File Paths** (Пути к файлам Java), щелкните **...** и укажите каталог, содержащий скачанный вами ранее JAR-файл. Теперь нажмите кнопку **Отправить**.

6. После отправки топологии обновите список топологий в **средстве просмотра топологий Storm** , чтобы убедиться, что обе топологии выполняются в кластере.

7. В **средстве просмотра топологий Storm** выберите топологию **EventHubReader**.

8. Чтобы открыть страницу сводки компонентов для объекта bolt, дважды щелкните компонент **LogBolt** на схеме.

9. В разделе **Executors** (Исполнители) выберите одну из ссылок в столбце **Порт**. Будет выведена информация, зарегистрированная компонентом. Записанные данные аналогичны приведенным ниже:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Остановка топологий

Чтобы остановить топологии, выберите каждую из них в **средстве просмотра топологий Storm**, а затем выберите элемент **Прервать**.

![Снимок экрана средства просмотра топологии Storm с выделенной кнопкой "Прервать"](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Удаление кластера

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом документе рассмотрено использование объектов spout и bolt Java службы концентраторов событий из топологии C# для работы с данными в концентраторе событий Azure. Дополнительные сведения о создании топологий C# см. в следующих статьях:

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Руководство по программированию для SCP](apache-storm-scp-programming-guide.md)
* [Примеры топологий для Storm в HDInsight](apache-storm-example-topology.md)
