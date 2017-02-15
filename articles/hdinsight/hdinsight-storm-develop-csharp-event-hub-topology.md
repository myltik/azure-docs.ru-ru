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
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (C#)
Служба концентраторов событий Azure позволяет обрабатывать значительные объемы данных из веб-сайтов, приложений и устройств. Воронка службы концентраторов событий упрощает использование Apache Storm в HDInsight для анализа этих данных в режиме реального времени. Вы также можете записывать данные в службу концентраторов событий из Storm с помощью сита службы концентраторов событий.

В этом руководстве вы узнаете, как с помощью шаблонов Visual Studio, установленных вместе со средствами HDInsight для Visual Studio, создать две топологии для работы с концентраторами событий Azure.

* **EventHubWriter**— случайным образом генерирует данные и записывает их в службу концентраторов событий
* **EventHubReader** считывает данные из концентраторов событий и записывает их в журналы Storm.

> [!NOTE]
> Хотя действия, описанные в этой статье, зависят от среды разработки Windows и Visual Studio, скомпилированный проект можно отправить в кластер HDInsight под управлением Windows или Linux. Топологии SCP.NET поддерживаются только теми кластерами под управлением Linux, которые созданы после 28 октября 2016 г.
> 
> Обновите пакет NuGet Microsoft.SCP.Net.SDK, использующийся в проекте, до версии 0.10.0.6 или выше, чтобы использовать топологию C# с кластером под управлением Linux. Версия пакета также должна соответствовать основной версии Storm, установленной на HDInsight. Например, для Storm в HDInsight версии 3.3 и 3.4 используйте Storm версии 0.10.x, а HDInsight 3.5 использует Storm 1.0.x.
> 
> Для топологии C# на кластерах под управлением Linux используется .NET 4.5. В кластере HDInsight они запускаются с помощью Mono. Большинство возможностей будут работать. Тем не менее вам следует просмотреть документ о [совместимости Mono](http://www.mono-project.com/docs/about-mono/compatibility/), чтобы определить потенциальные проблемы с совместимостью.
> 
> Java-версию этого проекта, работающую также с кластером под управлением Linux или Windows, можно найти в статье [Обработка событий из службы концентраторов событий Azure с помощью Storm в HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* [Кластер Apache Storm в HDInsight](hdinsight-apache-storm-tutorial-get-started.md)
* [Концентратор событий Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Пакет SDK Azure .NET](http://azure.microsoft.com/downloads/)
* [Средства HDInsight для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>Завершенный проект
Вы можете загрузить полную версию проекта, созданного в этом учебнике, с портала GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Но вам нужно будет указать параметры конфигурации, выполнив действия, описанные в этом учебнике.

## <a name="event-hubs-spout-and-bolt"></a>Воронка и сито службы концентраторов событий
Воронка и сито службы концентраторов событий — это компоненты Java, которые позволяют работать со службой концентраторов событий из Apache Storm. Хотя эти компоненты написаны на языке Java, средства HDInsight для Visual Studio позволяют создавать гибридные топологии, сочетающие в себе компоненты C# и Java.

Воронка и сито распространяются в виде отдельного файла архива Java (JAR) с именем **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, где #.# — это версия файла.

### <a name="download-the-jar-file"></a>Скачивание файла JAR
Последняя версия файла содержится в проекте с [примерами HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples), который размещен в папке **lib/eventhubs**. Чтобы скачать файл, используйте один из следующих методов.

> [!NOTE]
> Воронка и сито были переданы для включения в проект Apache Storm. Дополнительные сведения см. на странице портала GitHub [STORM 583: первоначальный возврат для концентраторов событий storm](https://github.com/apache/storm/pull/336/files).
> 
> 

* **Скачивание ZIP-файла**. На странице с [примерами HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples) нажмите на правой панели кнопку **Download ZIP** (Скачать ZIP-файл), чтобы скачать ZIP-файл с проектом.
  
    ![кнопка загрузки ZIP-файла](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    После загрузки извлеките архив, чтобы поместить файл в каталог **lib** .
* **Клонирование проекта**. Если у вас установлен [Git](http://git-scm.com/), выполните следующую команду для локального клонирования репозитория, а затем найдите файл в каталоге **lib**.
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

## <a name="configure-event-hubs"></a>Настройка концентраторов событий Azure
Служба концентраторов событий используется в качестве источника данных для этого примера. Используйте сведения в разделе **Создание концентратора событий** статьи [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Создав концентратор событий, просмотрите его колонку на портале Azure и щелкните **Политики общего доступа**. Чтобы добавить следующие политики, щелкните **+ Add** (+ Добавить).
   
   | Имя | Разрешения |
   | --- | --- |
   | writer |Отправка |
   | reader |Прослушивание |
   
    ![политики](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)
2. Выберите политики **reader** и **writer**. Скопируйте и сохраните значения **первичного ключа** для обеих политик, так как они будут использоваться позже.

## <a name="configure-the-eventhubwriter"></a>Настройка EventHubWriter
1. Если вы еще не установили последнюю версию средств HDInsight для Visual Studio, см. статью [Начало работы со средствами HDInsight для Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).
2. Скачайте решение из [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Откройте решение, чтобы просмотреть код для проекта **EventHubWriter**. Это может занять несколько минут.
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
1. Откройте проект **EventHubReader**, чтобы просмотреть код. Это может занять несколько минут.
2. Для **EventHubWriter** откройте файл **App.config**. Используйте сведения из концентратора событий, настроенного ранее, чтобы указать значения следующих ключей:
   
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
2. В диалоговом окне **Submit Topology** (Отправка топологии) выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Java File Paths** (Пути к файлам Java), элемент **...** и укажите каталог, содержащий скачанный ранее файл **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. Теперь нажмите кнопку **Отправить**.
   
    ![изображение диалогового окна отправки](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. После отправки топологии отобразится **средство просмотра топологий Storm** . Выберите топологию **EventHubReader** в левой части диалогового окна, чтобы просмотреть статистику для топологии. Сейчас она должна быть пустой, так как в службу концентраторов событий еще не записано ни одно событие.
   
    ![пример представления хранилища](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. В **обозревателе решений** щелкните правой кнопкой мыши проект **EventHubWriter** и выберите пункт **Submit to Storm on HDInsight** (Отправить в Storm в HDInsight).
5. В диалоговом окне **Submit Topology** (Отправка топологии) выберите свой **кластер Storm**. Разверните узел **Дополнительные конфигурации**, выберите **Java File Paths** (Пути к файлам Java), элемент **...** и укажите каталог, содержащий скачанный ранее файл **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. Теперь нажмите кнопку **Отправить**.
6. После отправки топологии обновите список топологий в **средстве просмотра топологий Storm** , чтобы убедиться, что обе топологии выполняются в кластере.
7. В **средстве просмотра топологий Storm** выберите топологию **EventHubReader**.
8. В графическом представлении дважды щелкните компонент **LogBolt**. Для сита откроется страница **Component Summary** (Сводка компонентов).
9. В разделе **Executors** (Исполнители) выберите одну из ссылок в столбце **Порт**. Отобразятся данные, записанные компонентом. Записанные данные аналогичны приведенным ниже:
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>Остановка топологий
Чтобы остановить топологии, выберите каждую из них в **средстве просмотра топологий Storm**, а затем выберите элемент **Прервать**.

![изображение уничтожения топологии](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Удаление кластера
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="notes"></a>Примечания
### <a name="checkpointing"></a>Контрольные точки
EventHubSpout периодически передает информацию о своем состоянии на узел Zookeeper, который сохраняет текущее смещение для прочитанных сообщений из очереди. Это позволяет компоненту принимать сообщения при сохраненном смещении в следующих сценариях:

* Работа экземпляра компонента завершена аварийно, и он будет перезапущен.
* Расширение или сжатие путем добавления или удаления узлов кластера.
* Топология удалена и перезапущена **под тем же именем**.

Вы также можете экспортировать и импортировать сохраненные контрольные точки в WASB (хранилище Azure в кластере HDInsight.) Сценарии для этого находятся в топологии Storm в кластере HDInsight: **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

> [!NOTE]
> Номер версии в пути может отличаться, так как версия Storm, установленная в кластере, может в будущем измениться.
> 
> 

Сценарии в этом каталоге:

* **stormmeta_import.cmd**: импорт всех метаданных Storm из контейнера хранилища кластера по умолчанию в Zookeeper.
* **stormmeta_export.cmd**: экспорт всех метаданных Storm из Zookeeper в контейнер хранилища кластера по умолчанию.
* **stormmeta_delete.cmd**: удаление всех метаданных Storm из Zookeeper.

Экспорт и импорт позволяют сохранять данные контрольных точек, если понадобится удалить кластер, но нужно будет возобновить обработку из текущего смещения в концентраторе при повторном развертывании нового кластера в Интернет.

> [!NOTE]
> Поскольку данные сохраняются в контейнере хранилища по умолчанию, новый кластер **должен** использовать учетную запись хранения и контейнер предыдущего кластера.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
В этом документе рассмотрено использование воронки и сита Java службы концентраторов событий из топологии C# для работы с данными в концентраторе событий Azure. Дополнительные сведения о создании топологий C# см. в следующих статьях.

* [Разработка топологий для Apache Storm в HDInsight на C# с помощью Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Руководство по программированию для SCP](hdinsight-storm-scp-programming-guide.md)
* [Примеры топологий для Storm в HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


