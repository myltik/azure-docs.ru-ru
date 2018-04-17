---
title: Вычислительные среды, поддерживаемые фабрикой данных Azure | Документация Майкрософт
description: Сведения о вычислительных средах, которые можно использовать в конвейерах фабрики данных Azure (например, Azure HDInsight) для преобразования или обработки данных.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: fe4a4962acce06a6448cef8d5c1af398e3965a33
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Вычислительные среды, поддерживаемые фабрикой данных Azure
В этой статье описываются различные среды вычислений, которые можно использовать для обработки и преобразования данных. Здесь содержатся также сведения о различных конфигурациях (конфигурациях по запросу и ваших собственных), которые поддерживаются фабрикой данных при настройке связанных служб, связывающих эти среды вычислений с фабрикой данных Azure.

Следующая таблица содержит список вычислительных сред, поддерживаемых фабрикой данных, и доступных в них действий. 

| Вычислительная среда                      | Действия                               |
| ---------------------------------------- | ---------------------------------------- |
| [Кластер HDInsight по запросу](#azure-hdinsight-on-demand-linked-service) или [собственный кластер HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [потоковая передача Hadoop](transform-data-using-hadoop-streaming.md) |
| [Пакетная служба Azure](#azure-batch-linked-service) | [Настраиваемая](transform-data-using-dotnet-custom-activity.md) |
| [машинное обучение Azure](#azure-machine-learning-linked-service) | [Действия машинного обучения: выполнение пакета и обновление ресурса](transform-data-using-machine-learning.md) |
| [Аналитика озера данных Azure](#azure-data-lake-analytics-linked-service) | [Аналитика озера данных U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [хранилище данных Azure SQL](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Хранимая процедура](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Среда вычислений по запросу
В конфигурации такого типа среда вычислений полностью управляется службой фабрики данных Azure. Среда автоматически создается службой фабрики данных перед отправкой задания для обработки данных и удаляется после его выполнения. Вы можете создать связанную службу для среды вычислений по запросу, настроить ее и управлять детализированными параметрами выполнения задания, управления кластером и параметрами действий начальной загрузки.

> [!NOTE]
> Конфигурации по запросу в настоящее время поддерживаются только для кластеров Azure HDInsight.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Связанная служба Azure HDInsight по запросу
Для обработки данных служба фабрики данных Azure автоматически создает кластер HDInsight по запросу. Кластер создается в том же регионе, что и учетная запись хранения (свойство linkedServiceName в JSON), связанная с кластером. Это должна быть стандартная универсальная учетная запись хранения Azure. 

Обратите внимание на следующие **важные** моменты, касающиеся связанной службы HDInsight по запросу.

* Кластер HDInsight по запросу создается в вашей подписке Azure. Сразу после запуска кластер отобразится на портале Azure. 
* Журналы заданий, которые выполняются в кластере HDInsight по запросу, копируются в учетную запись хранения, связанную с кластером HDInsight. ClusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword, заданные в определении связанной службы, используются для входа в кластер, чтобы выполнить глубокую диагностику в течение жизненного цикла кластера. 
* Вы оплачиваете только время, когда кластер HDInsight работает и выполняет задания.

> [!IMPORTANT]
> Подготовка к работе кластера HDInsight Azure по запросу обычно занимает от **20 минут**.
>
> 

### <a name="example"></a>Пример
Представленный ниже код JSON определяет связанную службу HDInsight по запросу под управлением Linux. Служба фабрики данных автоматически создает кластер HDInsight **на основе Linux** для обработки необходимых действий. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Кластер HDInsight создает **контейнер по умолчанию** в хранилище BLOB-объектов, указанном в коде JSON (**linkedServiceName**). При удалении кластера HDInsight этот контейнер не удаляется. В этом весь замысел. Если используется связанная служба HDInsight по запросу, кластер HDInsight создается всякий раз, когда нужно обработать срез данных (если не используется динамический кластер**timeToLive**), после чего кластер удаляется. 
>
> По мере выполнения действий количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Имена этих контейнеров указаны по шаблону `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Для удаления контейнеров в хранилище BLOB-объектов Azure используйте такие инструменты, как [Microsoft Storage Explorer](http://storageexplorer.com/) .
>
> 

### <a name="properties"></a>properties
| Свойство                     | ОПИСАНИЕ                              | Обязательно |
| ---------------------------- | ---------------------------------------- | -------- |
| Тип                         | Свойству type необходимо присвоить значение **HDInsightOnDemand**. | Yes      |
| clusterSize                  | Общее количество рабочих узлов и узлов данных в кластере. Кластер HDInsight создается с 2 головными узлами и количеством рабочих узлов, заданным в этом свойстве. Узлы имеют размер Standard_D3 с 4 ядрами, то есть кластер с 4 рабочими узлами использует 24 ядра (4\*4 = 16 для рабочих узлов + 2\*4 = 8 для головных узлов). Дополнительные сведения см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Yes      |
| linkedServiceName            | Связанная служба хранилища Azure, которую кластер по запросу должен использовать для хранения и обработки данных. Кластер HDInsight создается в том же регионе, что и учетная запись хранения Azure. Azure HDInsight имеет ограничение на общее количество ядер, которые можно использовать в каждом поддерживаемом регионе Azure. Убедитесь, что у вас есть достаточное количество квот ядер в необходимом регионе Azure в соответствии с необходимым размером кластера. Дополнительные сведения см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).<p>В настоящее время недоступно создание кластера HDInsight по запросу, который использует в качестве хранилища Azure Data Lake Store. Чтобы сохранить данные результатов обработки HDInsight в Azure Data Lake Store, воспользуйтесь действием копирования и скопируйте данные из хранилища BLOB-объектов Azure в Azure Data Lake Store. </p> | Yes      |
| clusterResourceGroup         | В этой группе ресурсов создается кластер HDInsight. | Yes      |
| timeToLive                   | Допустимое время простоя кластера HDInsight по запросу. Указывает, как долго кластер HDInsight по запросу остается активным после выполнения действия, если в кластере нет других активных заданий. Минимальное допустимое значение — 5 минут (00:05:00).<br/><br/>Например, если выполнение действия занимает 6 минут, а значение свойства timetolive равно 5 минутам, кластер остается активным в течение 5 минут по истечении 6-минутного выполнения действия. Если в течение этих 6 минут выполняется другое действие, оно обрабатывается в том же кластере.<br/><br/>Создание кластера HDInsight по запросу является ресурсоемкой операцией и может занять некоторое время. При необходимости используйте этот параметр для повышения производительности фабрики данных путем повторного использования кластера HDInsight по запросу.<br/><br/>Если значение timetolive равно 0, кластер удаляется сразу после выполнения действия. С другой стороны, если задать большое значение, кластер может простаивать и вы не сможете войти в систему, чтобы устранить неполадки. Тем не менее это может привести к большим затратам. Поэтому необходимо установить соответствующее значение в соответствии со своими потребностями.<br/><br/>Если значение свойства timetolive задано правильно, один и тот же экземпляр кластера HDInsight по запросу могут совместно использовать несколько конвейеров. | Yes      |
| clusterType                  | Тип создаваемого кластера HDInsight. Допустимые значения: Hadoop и Spark. Если не указано другое, по умолчанию используется значение Hadoop. | Нет        |
| версия                      | Версия кластера HDInsight. Если не указано другое, используется текущая заданная версия HDInsight по умолчанию. | Нет        |
| hostSubscriptionId           | Идентификатор подписки Azure, используемый для создания кластера HDInsight. Если не указано другое, будет использоваться идентификатор подписки контекста входа в Azure. | Нет        |
| clusterNamePrefix           | Префикс имени кластера HDI. Метка времени автоматически добавляется в конец имени кластера.| Нет        |
| sparkVersion                 | Версия Spark, если используется тип кластера Spark. | Нет        |
| additionalLinkedServiceNames | Указывает дополнительные учетные записи хранения для связанной службы HDInsight, чтобы служба фабрики данных могла регистрировать их от вашего имени. Эти учетные записи хранения должны находиться в том же регионе, что и кластер HDInsight, который создается в одном регионе с учетной записью хранения, указанной параметром linkedServiceName. | Нет        |
| osType                       | Тип операционной системы. Допустимые значения: Linux и Windows (только для HDInsight 3.3). Значение по умолчанию — Linux. | Нет        |
| hcatalogLinkedServiceName    | Имя связанной службы SQL Azure, указывающие на базу данных HCatalog. При создании кластера HDInsight по запросу используется база данных SQL Azure в качестве хранилища метаданных. | Нет        |
| connectVia                   | Среда выполнения интеграции, используемая для отправки действий в связанную службу HDInsight. Для связанной службы HDInsight по запросу поддерживается только среда выполнения интеграции Azure. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет        |
| clusterUserName                   | Имя пользователя для доступа к кластеру. | Нет        |
| clusterPassword                   | Пароль в строке защищенного типа для доступа к кластеру. | Нет        |
| clusterSshUserName         | Имя пользователя для установки удаленного подключения SSH к узлу кластера (для Linux). | Нет        |
| clusterSshPassword         | Пароль в строке защищенного типа для установки удаленного подключения SSH к узлу кластера (для Linux). | Нет        |


> [!IMPORTANT]
> HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты. Каждая из версий создает конкретную версию платформы HortonWorks Data Platform (HDP) и набор компонентов, содержащихся в этой версии. Список поддерживаемых версий HDInsight продолжает обновляться, чтобы предоставлять новейшие компоненты и исправления для экосистемы Hadoop. Ознакомьтесь с актуальной информацией о [поддерживаемой версии HDInsight и типе ОС](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), чтобы убедиться, что используется поддерживаемая версия HDInsight. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>Пример кода JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

Для связанной службы HDInsight по запросу необходимо выполнить проверку подлинности субъекта-службы, чтобы создать кластеры HDInsight от вашего имени. Чтобы использовать проверку подлинности субъекта-службы, зарегистрируйте сущность приложения в Azure Active Directory (Azure AD) и предоставьте ей роль **участника** подписки или группы ресурсов, в которой создан кластер HDInsight. Подробные инструкции см. в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Запишите следующие значения, которые используются для определения связанной службы:

- Идентификатор приложения
- Ключ приложения 
- Tenant ID

Используйте проверку подлинности на основе субъекта-службы, указав следующие свойства:

| Свойство                | ОПИСАНИЕ                              | Обязательно |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Укажите идентификатора клиента приложения.     | Yes      |
| **servicePrincipalKey** | Укажите ключ приложения.           | Yes      |
| **tenant**              | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Yes      |

### <a name="advanced-properties"></a>Дополнительные свойства

Для детализированной настройки кластера HDInsight по запросу можно также указать следующие свойства.

| Свойство               | ОПИСАНИЕ                              | Обязательно |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Задает параметры конфигурации ядра (как в файле core-site.xml) для создаваемого кластера HDInsight. | Нет        |
| hBaseConfiguration     | Задает основные параметры конфигурации HBase (hbase-site.xml) для кластера HDInsight. | Нет        |
| hdfsConfiguration      | Задает основные параметры конфигурации HDFS (hdfs-site.xml) для кластера HDInsight. | Нет        |
| hiveConfiguration      | Задает основные параметры конфигурации Hive (hive-site.xml) для кластера HDInsight. | Нет        |
| mapReduceConfiguration | Задает параметры конфигурации MapReduce (mapred-site.xml) для кластера HDInsight. | Нет        |
| oozieConfiguration     | Задает параметры конфигурации Oozie (oozie-site.xml) для кластера HDInsight. | Нет        |
| stormConfiguration     | Задает параметры конфигурации Storm (storm-site.xml) для кластера HDInsight. | Нет        |
| yarnConfiguration      | Задает параметры конфигурации Yarn (yarn-site.xml) для кластера HDInsight. | Нет        |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Пример: конфигурация кластера HDInsight по запросу с дополнительными свойствами

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Размеры узлов
Вы можете указать размеры головных узлов, узлов данных и узлов zookeeper, используя следующие свойства. 

| Свойство          | ОПИСАНИЕ                              | Обязательно |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Указывает размер головного узла. Значение по умолчанию: Standard_D3. Дополнительные сведения см. в разделе **Указание размеров узлов**. | Нет        |
| dataNodeSize      | Задает размер узла данных. Значение по умолчанию: Standard_D3. | Нет        |
| zookeeperNodeSize | Задает размер узла Zoo Keeper. Значение по умолчанию: Standard_D3. | Нет        |

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
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>properties
| Свойство          | ОПИСАНИЕ                              | Обязательно |
| ----------------- | ---------------------------------------- | -------- |
| Тип              | Свойству type необходимо присвоить значение **HDInsight**. | Yes      |
| clusterUri        | Универсальный код ресурса (URI) кластера HDInsight.        | Yes      |
| Имя пользователя          | Укажите имя пользователя, которое будет использоваться для подключения к существующему кластеру HDInsight. | Yes      |
| password          | Укажите пароль для учетной записи пользователя.   | Yes      |
| linkedServiceName | Имя связанной службы для службы хранилища Azure, которая обращается к хранилищу BLOB-объектов Azure, используемому кластером HDInsight. <p>В настоящее время для этого свойства невозможно указать связанную службу Azure Data Lake Store. Вы можете получать доступ к данным в Azure Data Lake Store из сценариев Hive или Pig, если кластер HDInsight имеет доступ к Data Lake Store. </p> | Yes      |
| connectVia        | Среда выполнения интеграции, используемая для отправки действий в связанную службу. Вы можете использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет        |

> [!IMPORTANT]
> HDInsight поддерживает несколько версий кластера Hadoop, которые могут быть развернуты. Каждая из версий создает конкретную версию платформы HortonWorks Data Platform (HDP) и набор компонентов, содержащихся в этой версии. Список поддерживаемых версий HDInsight продолжает обновляться, чтобы предоставлять новейшие компоненты и исправления для экосистемы Hadoop. Ознакомьтесь с актуальной информацией о [поддерживаемой версии HDInsight и типе ОС](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), чтобы убедиться, что используется поддерживаемая версия HDInsight. 
>

## <a name="azure-batch-linked-service"></a>Связанная пакетная служба Azure

Чтобы зарегистрировать пакетный пул виртуальных машин (ВМ) в фабрике данных, можно создать связанную пакетную службу Azure. Вы можете выполнить пользовательские действия с помощью пакетной службы Azure.

Если вы еще не знакомы с пакетной службой Azure, см. следующие статьи.

* [Основные сведения о пакетной службе Azure](../batch/batch-technical-overview.md) — общие сведения о пакетной службе Azure.
* Статья о командлете [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) со сведениями о создании учетной записи пакетной службы Azure или статья о [портале Azure](../batch/batch-account-create-portal.md) со сведениями о создании учетной записи пакетной службы Azure с помощью портала Azure. Подробные инструкции по использованию этого командлета см. в статье [Using PowerShell to manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) (Использование PowerShell для управления учетной записью пакетной службы Azure).
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) со сведениями о создании пула пакетной службы Azure.

### <a name="example"></a>Пример

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>properties
| Свойство          | ОПИСАНИЕ                              | Обязательно |
| ----------------- | ---------------------------------------- | -------- |
| Тип              | Свойству type необходимо присвоить значение **AzureBatch**. | Yes      |
| accountName       | Имя учетной записи пакетной службы Azure         | Yes      |
| accessKey         | Ключ доступа к учетной записи пакетной службы Azure.  | Yes      |
| batchUri          | URL-адрес учетной записи пакетной службы Azure в формате https://*batchaccountname.region*.batch.azure.com. | Yes      |
| poolName          | Имя пула виртуальных машин.    | Yes      |
| linkedServiceName | Имя связанной службы хранилища Azure, которая ассоциируется с этой связанной пакетной службой Azure. Эта связанная служба используется для промежуточных файлов, необходимых для выполнения действий. | Yes      |
| connectVia        | Среда выполнения интеграции, используемая для отправки действий в связанную службу. Вы можете использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет        |

## <a name="azure-machine-learning-linked-service"></a>Связанная служба Машинного обучения Azure
Создайте связанную службу Машинного обучения Azure, чтобы зарегистрировать конечную точку пакетной оценки показателей машинного обучения оценки в фабрике данных.

### <a name="example"></a>Пример

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>properties
| Свойство               | ОПИСАНИЕ                              | Обязательно                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | Свойству type необходимо присвоить значение **AzureML**. | Yes                                      |
| mlEndpoint             | URL-адрес пакетной оценки.                   | Yes                                      |
| apiKey                 | API модели опубликованной рабочей области.     | Yes                                      |
| updateResourceEndpoint | URL-адрес обновления ресурса для конечной точки веб-службы "Машинное обучение Azure", используемый для обновления прогнозной веб-службы с помощью файла обученной модели. | Нет                                        |
| servicePrincipalId     | Укажите идентификатора клиента приложения.     | Требуется, если задано свойство updateResourceEndpoint |
| servicePrincipalKey    | Укажите ключ приложения.           | Требуется, если задано свойство updateResourceEndpoint |
| tenant                 | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Требуется, если задано свойство updateResourceEndpoint |
| connectVia             | Среда выполнения интеграции, используемая для отправки действий в связанную службу. Вы можете использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет                                        |

## <a name="azure-data-lake-analytics-linked-service"></a>Связанная служба Azure Data Lake Analytics
Можно создать связанную службу **Azure Data Lake Analytics** , чтобы связать службу вычислений Azure Data Lake Analytics с фабрикой данных Azure. Действие U-SQL Data Lake Analytics в конвейере ссылается на эту связанную службу. 

### <a name="example"></a>Пример

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>properties

| Свойство             | ОПИСАНИЕ                              | Обязательно                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| Тип                 | Свойству type необходимо присвоить значение **AzureDataLakeAnalytics**. | Yes                                      |
| accountName          | Имя учетной записи аналитики озера данных Azure.  | Yes                                      |
| dataLakeAnalyticsUri | Универсальный код ресурса (URI) аналитики озера данных Azure.           | Нет                                        |
| subscriptionId       | Идентификатор подписки Azure                    | Нет (если не указан, используется подписка фабрики данных). |
| имя_группы_ресурсов    | Имя группы ресурсов Azure                | Нет (если не указано, используется группа ресурсов фабрики данных). |
| servicePrincipalId   | Укажите идентификатора клиента приложения.     | Yes                                      |
| servicePrincipalKey  | Укажите ключ приложения.           | Yes                                      |
| tenant               | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Yes                                      |
| connectVia           | Среда выполнения интеграции, используемая для отправки действий в связанную службу. Вы можете использовать среду выполнения интеграции Azure или локальную среду выполнения интеграции. Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. | Нет                                        |



## <a name="azure-sql-database-linked-service"></a>Связанная служба базы данных SQL Azure
Связанная служба SQL Azure создается и применяется к [действию хранимой процедуры](transform-data-using-stored-procedure.md) для вызова хранимой процедуры из конвейера фабрики данных. Дополнительную информацию см. в статье о [связанной службе SQL Azure](connector-azure-sql-database.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Связанная служба хранилища данных SQL Azure
Связанная служба хранилища данных SQL Azure создается и применяется к [действию хранимой процедуры](transform-data-using-stored-procedure.md) для вызова хранимой процедуры из конвейера фабрики данных. Дополнительные сведения об этой связанной службе см. в соответствующем разделе статьи [Перемещение данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure](connector-azure-sql-data-warehouse.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Связанная служба SQL Server
Связанная служба SQL Server создается и применяется к [действию хранимой процедуры](transform-data-using-stored-procedure.md) для вызова хранимой процедуры из конвейера фабрики данных. Дополнительные сведения о связанной службе SQL Server см. в соответствующем разделе статьи [Перемещение данных в базу данных SQL Server и обратно на локальных компьютерах и виртуальных машинах Azure IaaS с помощью фабрики данных Azure](connector-sql-server.md#linked-service-properties).

## <a name="azure-data-factory---naming-rules"></a>Фабрика данных Azure — правила именования
В следующей таблице приведены правила именования для артефактов фабрики данных.

| ИМЯ                             | Уникальность имени                          | Проверки                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Фабрика данных                     | Уникально в рамках Microsoft Azure. Регистр в именах не учитывается, т. е. `MyDF` и `mydf` ссылаются на одну и ту же фабрику данных. | <ul><li>Каждая фабрика данных привязывается ровно к одной подписке Azure.</li><li>Имена объектов должны начинаться с буквы или цифры и могут содержать только буквы, цифры и дефисы (-).</li><li>Перед каждым знаком тире (-) и после него должна стоять буква или цифра. Последовательные тире в именах контейнеров использовать нельзя.</li><li>Имя может содержать от 3 до 63 символов.</li></ul> |
| Связанные службы, таблицы, конвейеры | Уникально в рамках фабрики данных. Регистр в именах не учитывается. | <ul><li>Максимальное количество символов в имени таблицы: 260.</li><li>Имена объектов должны начинаться с буквы, цифры или символа подчеркивания (_).</li><li>Следующие символы не допускаются: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\".</li></ul> |
| Группа ресурсов                   | Уникально в рамках Microsoft Azure. Регистр в именах не учитывается. | <ul><li>Максимальное количество символов: 1000.</li><li>Имя может содержать буквы, цифры и следующие символы: "-" (дефис), "_" (подчеркивание), "," (запятая), "." (точка).</li></ul> |

## <a name="next-steps"></a>Дополнительная информация
Список действий преобразования данных, поддерживаемых фабрикой данных Azure, см. [в этой статье](transform-data.md).
