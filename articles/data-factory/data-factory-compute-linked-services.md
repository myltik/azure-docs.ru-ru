---
title: "Вычислительные среды, поддерживаемые фабрикой данных Azure | Документация Майкрософт"
description: "Сведения о вычислительных средах, которые можно использовать в конвейерах фабрики данных Azure для преобразования и обработки данных."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 97e40e0081e1dcce0ed42748a053c46cecf569ba
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017

---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Вычислительные среды, поддерживаемые фабрикой данных Azure
В этой статье описываются различные среды вычислений, которые можно использовать для обработки и преобразования данных. Здесь содержатся также сведения о различных конфигурациях (конфигурациях по запросу и ваших собственных), которые поддерживаются фабрикой данных при настройке связанных служб, связывающих эти среды вычислений с фабрикой данных Azure.

Следующая таблица содержит список вычислительных сред, поддерживаемых фабрикой данных, и доступных в них действий. 

| Вычислительная среда | Действия |
| --- | --- |
| [Кластер HDInsight по запросу](#azure-hdinsight-on-demand-linked-service) или [собственный кластер HDInsight](#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [потоковая передача Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Пакетная служба Azure](#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Машинное обучение Azure](#azure-machine-learning-linked-service) |[Действия машинного обучения: выполнение пакета и обновление ресурса](data-factory-azure-ml-batch-execution-activity.md) |
| [Аналитика озера данных Azure](#azure-data-lake-analytics-linked-service) |[Аналитика озера данных U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [хранилище данных Azure SQL](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) |[Хранимая процедура](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Версии HDInsight, поддерживаемые в фабрике данных Azure
Azure HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты в любое время. Каждая из версий создает конкретную версию платформы HortonWorks Data Platform (HDP) и набор компонентов, содержащихся в этой версии. Корпорация Майкрософт продолжает обновлять список поддерживаемых версий HDInsight, чтобы предоставлять последние компоненты и исправления для экосистемы Hadoop. Версия HDInsight 3.2 объявлена нерекомендуемой 01.04.2017 Дополнительные сведения см. в разделе [Поддерживаемые версии HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

Это влияет на существующие фабрики данных Azure, выполняющие действия с кластерами HDInsight 3.2. Мы рекомендуем пользователям следовать указаниям в следующем разделе, чтобы обновить затронутые фабрики данных.

### <a name="for-linked-services-pointing-to-your-own-hdinsight-clusters"></a>Связанные службы, указывающие на ваши кластеры HDInsight
* **Связанные службы HDInsight, указывающие на ваши кластеры HDInsight 3.2 или более ранней версии:**

  Фабрика данных Azure поддерживает отправку заданий в кластеры HDInsight из HDI 3.1 для [последней поддерживаемой версии HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Тем не менее, после 01.04.2017 вы больше не сможете создавать кластеры HDInsight 3.2 согласно политике рекомендуемых версий, описанной в разделе о [поддерживаемых версиях HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).  

  **Рекомендации** 
  * Выполните тесты, чтобы убедиться в совместимости действий, использующих эти связанные службы, с [последней поддерживаемой версией HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), воспользовавшись информацией, приведенной в разделах [Компоненты Hadoop, доступные в разных версиях HDInsight](../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) и [Заметки о выпуске Hortonworks, связанные с версиями HDInsight](../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).
  * Обновите кластер HDInsight 3.2 до [последней поддерживаемой версии HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) для получения последних компонентов и исправлений экосистемы Hadoop. 

* **Связанные службы HDInsight, указывающие на ваши кластеры HDInsight 3.3 или более поздней версии:**

  Фабрика данных Azure поддерживает отправку заданий в кластеры HDInsight из HDI 3.1 для [последней поддерживаемой версии HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). 
  
  **Рекомендации** 
  * Никаких действий для фабрики данных не требуется. Тем не менее, если используется более ранняя версия HDInsight, мы все равно рекомендуем обновить ее до [последней поддерживаемой версии HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), чтобы иметь возможность получать последние компоненты и исправления экосистемы Hadoop.

### <a name="for-hdinsight-on-demand-linked-services"></a>Связанные службы кластера HDInsight по запросу
* **В определении JSON связанных служб HDInsight по запросу указана версия 3.2 или более ранняя версия:**
  
  Фабрика данных Azure будет поддерживать создание кластеров HDInsight по запросу версии 3.3 или выше с **15.05.2017**. И прекращение поддержки существующих связанных служб кластера HDInsight по запросу версии 3.2 будет отложено до **15.07.2017**.  

  **Рекомендации** 
  * Выполните тесты, чтобы убедиться в совместимости действий, использующих эти связанные службы, с [последней поддерживаемой версией HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), воспользовавшись информацией, приведенной в разделах [Компоненты Hadoop, доступные в разных версиях HDInsight](../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) и [Заметки о выпуске Hortonworks, связанные с версиями HDInsight](../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).
  * До **15.07.2017** обновите свойство Version в определении JSON связанной службы HDI по запросу, указав [последнюю поддерживаемую версию HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), чтобы иметь возможность получать последние компоненты и исправления экосистемы Hadoop. Подробное описание определения JSON приведено в [примере связанной службы Azure HDInsight по запросу](#azure-hdinsight-on-demand-linked-service). 

* **Версия в связанных службах HDInsight по запросу не указана:**
  
  Фабрика данных Azure будет поддерживать создание кластеров HDInsight по запросу версии 3.3 или выше с **15.05.2017**. И прекращение поддержки существующих связанных служб кластера HDInsight по запросу версии 3.2 будет отложено до **15.07.2017**. 

  Если до **15.07.2017** оставить это значение пустым, то будут использоваться следующие значения по умолчанию свойств Version и osType. 

  | Свойство | По умолчанию | Обязательно |
  | --- | --- | --- |
  Версия   | HDI 3.1 для кластера Windows и HDI 3.2 для кластера Linux.| Нет
  osType | Значение по умолчанию — Windows. | Нет

  Если после **15.07.2017** оставить это значение пустым, то будут использоваться следующие значения по умолчанию свойств Version и osType.

  | Свойство | По умолчанию | Обязательно |
  | --- | --- | --- |
  Версия   | HDI 3.3 для кластера Windows и HDI 3.5 для кластера Linux.    | Нет
  osType | Значение по умолчанию — Linux. | Нет

  **Рекомендации** 
  * До **15.07.2017** выполните тесты, чтобы убедиться в совместимости действий, использующих эти связанные службы, с [последней поддерживаемой версией HDInsight](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), воспользовавшись информацией, приведенной в разделах [Компоненты Hadoop, доступные в разных версиях HDInsight](../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) и [Заметки о выпуске Hortonworks, связанные с версиями HDInsight](../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).  
  * После **15.07.2017** убедитесь, что вы явно указали значения версии osType, если вы хотите переопределить параметры по умолчанию. 

>[!Note]
>В настоящее время фабрика данных Azure не поддерживает кластеры HDInsight, использующие Azure Data Lake Store в качестве основного хранилища. Используйте службу хранилища Azure в качестве основного хранилища для кластеров HDInsight. 
>  
>  

## <a name="on-demand-compute-environment"></a>Среда вычислений по запросу
В конфигурации такого типа среда вычислений полностью управляется службой фабрики данных Azure. Среда автоматически создается службой фабрики данных перед отправкой задания для обработки данных и удаляется после его выполнения. Вы можете создать связанную службу для среды вычислений по запросу, настроить ее и управлять детализированными параметрами выполнения задания, управления кластером и параметрами действий начальной загрузки.

> [!NOTE]
> Конфигурации по запросу в настоящее время поддерживаются только для кластеров Azure HDInsight.
> 
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Связанная служба Azure HDInsight по запросу
Для обработки данных служба фабрики данных Azure автоматически создает кластер HDInsight под управлением Windows/Linux по запросу. Кластер создается в том же регионе, что и учетная запись хранения (свойство linkedServiceName в JSON), связанная с кластером.

Обратите внимание на следующие **важные** моменты, касающиеся связанной службы HDInsight по запросу.

* Кластер HDInsight, созданный по запросу в подписке Azure, не отображается. Служба фабрики данных Azure управляет кластером HDInsight по запросу от вашего имени.
* Журналы заданий, которые выполняются в кластере HDInsight по запросу, копируются в учетную запись хранения, связанную с кластером HDInsight. Доступ к этим журналам можно получить на портале Azure в колонке **Подробности о выполнении операции**. Дополнительные сведения см. в статье [Мониторинг конвейеров фабрики данных Azure и управление ими](data-factory-monitor-manage-pipelines.md).
* Вы оплачиваете только время, когда кластер HDInsight работает и выполняет задания.

> [!IMPORTANT]
> Подготовка к работе кластера HDInsight Azure по запросу обычно занимает от **20 минут**.
> 
> 

### <a name="example"></a>Пример
Представленный ниже код JSON определяет связанную службу HDInsight по запросу под управлением Linux. При обработке среза данных служба фабрики данных автоматически создает кластер HDInsight **под управлением Linux** . 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Чтобы использовать кластер HDInsight под управлением Windows, для **osType** выберите значение **windows** либо не используйте это свойство, так как значение по умолчанию — windows.  

> [!IMPORTANT]
> Кластер HDInsight создает **контейнер по умолчанию** в хранилище BLOB-объектов, указанном в коде JSON (**linkedServiceName**). При удалении кластера HDInsight этот контейнер не удаляется. В этом весь замысел. Если используется связанная служба HDInsight по запросу, кластер HDInsight создается всякий раз, когда нужно обработать срез данных (если не используется динамический кластер**timeToLive**), после чего кластер удаляется. 
> 
> По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Имена этих контейнеров указаны по шаблону `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Для удаления контейнеров в хранилище BLOB-объектов Azure используйте такие инструменты, как [Microsoft Storage Explorer](http://storageexplorer.com/) .
> 
> 

### <a name="properties"></a>Свойства
| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Свойству type необходимо присвоить значение **HDInsightOnDemand**. |Да |
| clusterSize |Общее количество рабочих узлов и узлов данных в кластере. Кластер HDInsight создается с 2 головными узлами и количеством рабочих узлов, заданным в этом свойстве. Узлы имеют размер Standard_D3 с 4 ядрами, то есть кластер с 4 рабочими узлами использует 24 ядра (4\*4 = 16 для рабочих узлов + 2\*4 = 8 для головных узлов). Дополнительные сведения об уровне Standard_D3 см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). |Да |
| timeToLive |Допустимое время простоя кластера HDInsight по запросу. Указывает, как долго кластер HDInsight по запросу остается активным после выполнения действия, если в кластере нет других активных заданий.<br/><br/>Например, если выполнение действия занимает 6 минут, а значение свойства timetolive равно 5 минутам, кластер остается активным в течение 5 минут по истечении 6-минутного выполнения действия. Если в течение этих 6 минут выполняется другое действие, оно обрабатывается в том же кластере.<br/><br/>Создание кластера HDInsight по запросу является ресурсоемкой операцией и может занять некоторое время. При необходимости используйте этот параметр для повышения производительности фабрики данных путем повторного использования кластера HDInsight по запросу.<br/><br/>Если значение timetolive равно 0, кластер удаляется сразу после выполнения действия. С другой стороны, если задать большое значение, кластер может простаивать без необходимости, что приведет к большим затратам. Поэтому необходимо установить соответствующее значение в соответствии со своими потребностями.<br/><br/>Если значение свойства timetolive задано правильно, один и тот же экземпляр кластера HDInsight по запросу могут совместно использовать несколько конвейеров.  |Да |
| версия |Версия кластера HDInsight. Значение по умолчанию равно 3.1 для кластера Windows и 3.2 для кластера Linux. |Нет |
| linkedServiceName | Связанная служба хранилища Azure, которую кластер по запросу должен использовать для хранения и обработки данных. Кластер HDInsight создается в том же регионе, что и учетная запись хранения Azure.<p>В настоящее время недоступно создание кластера HDInsight по запросу, который использует в качестве хранилища Azure Data Lake Store. Чтобы сохранить данные результатов обработки HDInsight в Azure Data Lake Store, воспользуйтесь действием копирования и скопируйте данные из хранилища BLOB-объектов Azure в Azure Data Lake Store. </p>  | Да |
| additionalLinkedServiceNames |Указывает дополнительные учетные записи хранения для связанной службы HDInsight, чтобы служба фабрики данных могла регистрировать их от вашего имени. Эти учетные записи хранения должны находиться в том же регионе, что и кластер HDInsight, который создается в одном регионе с учетной записью хранения, указанной параметром linkedServiceName. |Нет |
| osType |Тип операционной системы. Допустимые значения: Windows (по умолчанию) и Linux. |Нет |
| hcatalogLinkedServiceName |Имя связанной службы SQL Azure, указывающие на базу данных HCatalog. При создании кластера HDInsight по запросу используется база данных SQL Azure в качестве хранилища метаданных. |Нет |

#### <a name="additionallinkedservicenames-json-example"></a>Пример кода JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Дополнительные свойства
Для детализированной настройки кластера HDInsight по запросу можно также указать следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| coreConfiguration |Задает параметры конфигурации ядра (как в файле core-site.xml) для создаваемого кластера HDInsight. |Нет |
| hBaseConfiguration |Задает основные параметры конфигурации HBase (hbase-site.xml) для кластера HDInsight. |Нет |
| hdfsConfiguration |Задает основные параметры конфигурации HDFS (hdfs-site.xml) для кластера HDInsight. |Нет |
| hiveConfiguration |Задает основные параметры конфигурации Hive (hive-site.xml) для кластера HDInsight. |Нет |
| mapReduceConfiguration |Задает параметры конфигурации MapReduce (mapred-site.xml) для кластера HDInsight. |Нет |
| oozieConfiguration |Задает параметры конфигурации Oozie (oozie-site.xml) для кластера HDInsight. |Нет |
| stormConfiguration |Задает параметры конфигурации Storm (storm-site.xml) для кластера HDInsight. |Нет |
| yarnConfiguration |Задает параметры конфигурации Yarn (yarn-site.xml) для кластера HDInsight. |Нет |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Пример: конфигурация кластера HDInsight по запросу с дополнительными свойствами

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Размеры узлов
Вы можете указать размеры головных узлов, узлов данных и узлов zookeeper, используя следующие свойства. 

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| headNodeSize |Указывает размер головного узла. Значение по умолчанию: Standard_D3. Дополнительные сведения см. в разделе **Указание размеров узлов**. |Нет |
| dataNodeSize |Задает размер узла данных. Значение по умолчанию: Standard_D3. |Нет |
| zookeeperNodeSize |Задает размер узла Zoo Keeper. Значение по умолчанию: Standard_D3. |Нет |

#### <a name="specifying-node-sizes"></a>Указание размеров узлов
Сведения о строковых значениях, необходимых для задания указанных выше свойств, см. в статье [Размеры виртуальных машин в Azure](../virtual-machines/linux/sizes.md). Значения должны соответствовать указанным в статье **командлетам и API**. Как указано в статье, узел данных большого размера (по умолчанию) имеет 7 ГБ памяти, которых может быть недостаточно для вашего сценария. 

Если вы хотите создать головные узлы и рабочие узлы размера D4, укажите **Standard_D4** в качестве значения для свойств headNodeSize и dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Если для этих свойств указано неверное значение, может возникнуть следующая **ошибка:** Не удалось создать кластер. Исключение: не удается завершить операцию создания кластера. Операция завершилась ошибкой с кодом 400. Оставшееся состояние кластера: "Ошибка". Сообщение: "PreClusterCreationValidationFailure". При появлении этой ошибки убедитесь, что вы используете имя **командлета или API** из таблицы в статье [Размеры виртуальных машин в Azure](../virtual-machines/linux/sizes.md).  

## <a name="bring-your-own-compute-environment"></a>Использование собственной среды вычислений
В конфигурации такого типа вы можете зарегистрировать уже существующую среду вычислений как связанную службу в фабрике данных. Вы будете управлять средой вычислений, а служба фабрики данных — использовать ее для выполнения действий.

Такая конфигурация поддерживается в следующих средах вычислений:

* Azure HDInsight
* Пакетная служба Azure
* Машинное обучение Azure
* Аналитика озера данных Azure
* База данных SQL Azure, хранилища данных SQL Azure, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Связанная служба Azure HDInsight
Чтобы зарегистрировать собственный кластер HDInsight в фабрике данных, вы можете создать связанную службу Azure HDInsight.

### <a name="example"></a>Пример

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Свойства
| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Свойству type необходимо присвоить значение **HDInsight**. |Да |
| clusterUri |Универсальный код ресурса (URI) кластера HDInsight. |Да |
| Имя пользователя |Укажите имя пользователя, которое будет использоваться для подключения к существующему кластеру HDInsight. |Да |
| пароль |Укажите пароль для учетной записи пользователя. |Да |
| linkedServiceName (имя связанной службы) | Имя связанной службы для службы хранилища Azure, которая обращается к хранилищу BLOB-объектов Azure, используемому кластером HDInsight. <p>В настоящее время для этого свойства невозможно указать связанную службу Azure Data Lake Store. Вы можете получать доступ к данным в Azure Data Lake Store из сценариев Hive или Pig, если кластер HDInsight имеет доступ к Data Lake Store. </p>  |Да |

## <a name="azure-batch-linked-service"></a>Связанная пакетная служба Azure
Чтобы зарегистрировать пакетный пул виртуальных машин (ВМ) в фабрике данных, можно создать связанную пакетную службу Azure. Пользовательские действия .NET можно выполнять с помощью пакетной службы Azure или службы Azure HDInsight.

Если вы еще не знакомы с пакетной службой Azure, см. следующие статьи.

* [Основные сведения о пакетной службе Azure](../batch/batch-technical-overview.md) — общие сведения о пакетной службе Azure.
* Статья о командлете [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) со сведениями о создании учетной записи пакетной службы Azure или статья о [портале Azure](../batch/batch-account-create-portal.md) со сведениями о создании учетной записи пакетной службы Azure с помощью портала Azure. Подробные инструкции по использованию этого командлета см. в статье [Using PowerShell to manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) (Использование PowerShell для управления учетной записью пакетной службы Azure).
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) со сведениями о создании пула пакетной службы Azure.

### <a name="example"></a>Пример

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Добавьте текст **.\<имя_региона\>** к имени учетной записи пакетной службы для свойства **accountName**. Пример:

```json
"accountName": "mybatchaccount.eastus"
```

Другой вариант — указать конечную точку batchUri, как показано в следующем примере.

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Свойства
| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Свойству type необходимо присвоить значение **AzureBatch**. |Да |
| accountName |Имя учетной записи пакетной службы Azure |Да |
| accessKey |Ключ доступа к учетной записи пакетной службы Azure. |Да |
| poolName |Имя пула виртуальных машин. |Да |
| linkedServiceName |Имя связанной службы хранилища Azure, которая ассоциируется с этой связанной пакетной службой Azure. Эта связанная служба используется для промежуточных файлов, необходимых для выполнения действий и хранения журналов выполнения действий. |Да |

## <a name="azure-machine-learning-linked-service"></a>Связанная служба машинного обучения Azure
Создайте связанную службу Машинного обучения Azure, чтобы зарегистрировать конечную точку пакетной оценки показателей машинного обучения оценки в фабрике данных.

### <a name="example"></a>Пример

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Свойства
| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Свойству type необходимо присвоить значение **AzureML**. |Да |
| mlEndpoint |URL-адрес пакетной оценки. |Да |
| apiKey |API модели опубликованной рабочей области. |Да |

## <a name="azure-data-lake-analytics-linked-service"></a>Связанная служба аналитики озера данных Azure
Можно создать связанную службу **аналитики озера данных Azure** , чтобы связать службу вычислений аналитики озера данных Azure с фабрикой данных Azure, прежде чем использовать [действие U-SQL аналитики озера данных](data-factory-usql-activity.md) в конвейере.

В следующем примере представлено определение JSON для связанной службы аналитики озера данных Azure.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

В следующей таблице приведены описания свойств из определения JSON.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| Тип |Свойству type необходимо присвоить значение **AzureDataLakeAnalytics**. |Да |
| accountName |Имя учетной записи аналитики озера данных Azure. |Да |
| dataLakeAnalyticsUri |Универсальный код ресурса (URI) аналитики озера данных Azure. |Нет |
| authorization |Код авторизации извлекается автоматически после нажатия кнопки **Авторизовать** в редакторе фабрики данных и выполнения входа с авторизацией OAuth. |Да |
| subscriptionId |Идентификатор подписки Azure |Нет (если не указан, используется подписка фабрики данных). |
| имя_группы_ресурсов |Имя группы ресурсов Azure |Нет (если не указано, используется группа ресурсов фабрики данных). |
| sessionid |Идентификатор сеанса из сеанса авторизации OAuth. Каждый идентификатор сеанса является уникальным и используется только один раз. Этот ИД создается автоматически в редакторе фабрики данных. |Да |

Срок действия кода авторизации, созданного с помощью кнопки **Авторизовать**, через некоторое время истекает. Сроки действия для различных типов учетных записей пользователей см. в следующей таблице. По истечении **срока действия маркера** проверки подлинности может появиться следующее сообщение об ошибке: "Произошла ошибка при операции с учетными данными: invalid_grant — AADSTS70002: ошибка при проверке учетных данных". AADSTS70008: срок действия предоставленных прав доступа истек или они были отозваны. Идентификатор отслеживания: d18629e8-af88-43c5-88e3-d8419eb1fca1 Идентификатор корреляции: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Временная отметка: 2015-12-15 21:09:31Z".

| Тип пользователя | Срок действия |
|:--- |:--- |
| Учетные записи пользователей, которые не управляются с помощью Azure Active Directory (например, @hotmail.com, @live.com, @outlook.com). |12 часов |
| Учетные записи пользователей, которые управляются Azure Active Directory (AAD) |14 дней после последнего запуска среза. <br/><br/>90 дней, если срез, основанный на связанной службе на основе OAuth, выполняется по крайней мере раз в 14 дней. |

Чтобы избежать этой ошибки или исправить ее, вам потребуется повторно авторизоваться с помощью кнопки **Авторизовать** и повторно развернуть связанную службу, когда **срок действия маркера истечет**. Значения свойств sessionId и authorization также можно задавать программно с помощью кода, приведенного в следующем разделе. 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Программное создание значений свойств sessionId и authorization
Следующий код создает значения свойств**sessionId** и **authorization**.  

```CSharp

if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Для использования класса WindowsFormsWebAuthenticationDialog следует добавить ссылку на Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll. 

Подробные сведения о классах фабрики данных, используемых в коде, см. в статьях [AzureDataLakeStoreLinkedService — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) и [AuthorizationSessionGetResponse — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). 

## <a name="azure-sql-linked-service"></a>Связанная служба SQL Azure
Связанная служба SQL Azure создается и применяется к [действию хранимой процедуры](data-factory-stored-proc-activity.md) для вызова хранимой процедуры из конвейера фабрики данных. Дополнительную информацию см. в статье о [связанной службе SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Связанная служба хранилища данных SQL Azure
Связанная служба хранилища данных SQL Azure создается и применяется к [действию хранимой процедуры](data-factory-stored-proc-activity.md) для вызова хранимой процедуры из конвейера фабрики данных. Дополнительные сведения об этой связанной службе см. в соответствующем разделе статьи [Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Связанная служба SQL Server
Связанная служба SQL Server создается и применяется к [действию хранимой процедуры](data-factory-stored-proc-activity.md) для вызова хранимой процедуры из конвейера фабрики данных. Дополнительные сведения о связанной службе SQL Server см. в соответствующем разделе статьи [Перемещение данных в базу данных SQL Server и обратно на локальных компьютерах и виртуальных машинах Azure IaaS с помощью фабрики данных Azure](data-factory-sqlserver-connector.md#linked-service-properties).


