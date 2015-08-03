<properties 
	pageTitle="Введение в фабрику данных Azure" 
	description="Узнайте, как использовать службу фабрики данных Azure для формирования обработки данных, хранилища данных и служб перемещения данных, чтобы создавать конвейеры, производящие надежные данные." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Введение в службу фабрики данных Azure
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


**Фабрика данных Azure** является полностью управляемой системой для создания служб хранения, обработки и перемещения данных и превращения этих служб в упрощенные, масштабируемые и надежные конвейеры производства данных. Служба фабрики данных дает вам следующие возможности.

- Создание управляемых данными рабочих процессов (конвейеров), которые комбинируют, статистически обрабатывают и преобразуют данные, полученные из локальных, облачных и интернет-хранилищ. 
- Преобразование частично структурированных, неструктурированных и структурированных данных из самых разнообразных источников в достоверные сведения.
- Создание данных в удобной для дальнейшего использования форме с помощью бизнес-аналитики, средств анализа и других приложений. 
- Настройка процесса обработки сложных данных с помощью простых скриптов JSON.
- Мониторинг и управление конвейерами благодаря широким визуальным возможностям, предоставляемым на портале предварительной версии Azure.  

В следующем видеоролике представлен быстрый обзор службы фабрики данных Azure.


- [Видео. Знакомство с фабрикой данных Azure](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## Обзор
Обычно проекты интеграции данных развивались вокруг создания процессов извлечения, преобразования и загрузки (Extract-Transform-Load — ETL). То есть данные извлекаются из различных источников данных внутри организации, преобразовываются для соответствия целевой схеме корпоративного хранилища данных (Enterprise Data Warehouse — EDW) и загружаются в EDW. Впоследствии EDW используется в качестве единого источника истины для решений бизнес-аналитики.

![Традиционные ETL][image-data-factory-introduction-traditional-ETL]

Современный ландшафт корпоративных данных продолжает расти экспоненциально в объеме, разнообразии и сложности. Он более разнообразный, чем когда-либо, так как присутствуют локальные и облачные данные различных форм и скоростей. Обработка данных должна выполняться между разными географическими расположениями и включает в себя сочетание программного обеспечения с открытым исходным кодом, коммерческих решений и пользовательских служб обработки; это ресурсоемкие процессы, которые сложно интегрировать и обслуживать. Гибкость, необходимая для адаптации к современному меняющемуся ландшафту данных большого размера, — это возможность слияния традиционных хранилищ EDW с потребностями современных систем производства информации.

![Обзор современного разнообразия технологий обработки][image-data-factory-introduction-todays-diverse-processing-landspace]

Служба **фабрики данных Azure** — это сложная платформа для организации работы с традиционными EDW и в условиях меняющейся среды данных. Она предоставляет предприятиям возможность использовать все доступные им данные для принятия решений на основе этих данных. Служба фабрики данных Azure позволяет предприятиям использовать все это разнообразие, предоставляя платформу для организации процессов обработки, хранения и перемещения данных и объединения их в конвейеры производства информации, а также для управления массивами достоверных данных.

Вот какие возможности дает вам служба фабрики данных Azure:

- **Комфортная работа с различными системами хранения и обработки данных.** Служба фабрики данных позволяет создавать конвейеры производства информации, которые перемещают и обрабатывают как локальные (например, SQL Server), так и облачные источники данных, такие как база данных SQL Azure, таблица Azure и большие двоичные объекты. 
- **Преобразование данных в достоверную информацию.** Служба фабрики данных поддерживает обработку Hive, Pig и C#, а также ключевые функции обработки, такие как автоматическое управление кластером Hadoop (HDInsight), повторные попытки при временных ошибках, настраиваемые политики времени ожидания и предупреждения.  
- **Мониторинг конвейеров данных в одном окне.** Служба фабрики данных предоставляет полное и достоверное представление о службах хранения, обработки и перемещения данных. Она помогает быстро оценить работоспособность конвейера данных от начала до конца, выявить проблемы и при необходимости предпринять действия по исправлению. Вы также можете в визуальном режиме отслеживать происхождение данных и взаимосвязи между разными данными во всех ваших источниках, а также просматривать журналы по выполнению заданий, работоспособности системы и зависимостям, не покидая одной панели мониторинга.
- **Делайте информированные выводы, опираясь на преобразованные данные**. Служба фабрики данных позволяет создавать конвейеры данных, производящие достоверные данные, которые могут использоваться бизнес-аналитикой, средствами анализа и другими приложениями.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Модель приложения
На следующей схеме показана модель приложения, поддерживаемая службой фабрики данных Azure.

![Модель приложения][image-data-factory-application-model]

Существуют три этапа производства информации в фабрике данных Azure.

- **Подключение и сбор**. На этом этапе данные из различных источников импортируются в центры данных. Конвейер в фабрике данных может иметь одну или несколько задач. Используйте одну или несколько задач **копирования** в конвейере данных, чтобы осуществить сбор данных из исходных хранилищ данных и с помощью центра данных перенести их в конечное хранилище для дальнейшей обработки. Кластер HDInsight (вычисление) и связанное с ним хранилище больших двоичных объектов (хранение) вместе образуют центр данных HDInsight. Для использования центра данных HDInsight скопируйте все исходные данные в связанное с HDInsight хранилище больших двоичных объектов Azure, чтобы эти данные могли обрабатываться кластером HDInsight. Конвейер потребляет вычислительные ресурсы центра данных, такого как кластер HDInsight.      
- **Преобразование и обогащение**. На этом этапе выполняется обработка собранных данных. Например, **действие HDInsight** в конвейере может обрабатывать данные, расположенные в связанном хранилище больших двоичных объектов Azure, с помощью преобразований, используя скрипты Hive и Pig для получения достоверных сведений. Конвейеры можно объединить в цепочку (как показано на схеме) таким образом, что наборы данных, выходящие из одного конвейера, становятся входящими для другого конвейера, расположенного как в том же, так и в другом центре данных.  
- **Публикации**. На этом этапе данные публикуются, чтобы их можно было использовать в инструментах бизнес-аналитики и других приложениях. Например, действие копирования в конвейере может копировать выходные данные, полученные в ходе обработки на этапе преобразования и обогащения, в хранилище данных (например, локальную систему SQL Server), на основе которого уже можно создавать решения бизнес-аналитики.   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##Дальнейшие действия
1. [Приступая к работе с фабрикой данных][datafactory-getstarted]. Эта статья содержит пошаговый учебник с примером создания фабрики данных Azure, которая копирует данные из большого двоичного объекта Azure в базу данных SQL Azure.
2. [Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных][adf-tutorial] В этой статье на **пошаговом примере** показано, как при помощи фабрики данных Azure реализовать **реалистичный сценарий** преобразования данных файлов журнала в подробные сведения.

## См. также
- [Фабрика данных — терминология][adf-terminology]. В этой статье разъясняется терминология, использующаяся при создании фабрик данных при помощи службы фабрики данных Azure 
- [Фабрика данных — часто задаваемые вопросы][adf-faq]. В данной статье приведен список часто задаваемых вопросов и ответов на них.
- [Распространенные сценарии использования фабрики данных Azure][adf-common-scenarios]. В этой статье описано несколько популярных сценариев использования службы фабрики данных Azure. 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png



 

<!---HONumber=July15_HO4-->