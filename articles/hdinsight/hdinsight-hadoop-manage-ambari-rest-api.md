---
title: "Мониторинг кластеров HDInsight и управление ими с помощью интерфейса REST API Apache Ambari | Документация Майкрософт"
description: "Узнайте, как использовать Ambari для наблюдения за кластерами HDInsight на платформе Linux и управления их функционированием. В этом документе рассказывается об использовании интерфейса REST API Ambari, предоставляемого с кластерами HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 11523a95a8eac687d391c23cc1e29fa0684a40ae
ms.openlocfilehash: 2723716eb1ec823809d34ce1698d25a0ed9d012b


---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Управление кластерами HDInsight с помощью REST API Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari упрощает управление кластером Hadoop и его мониторинг за счет удобного пользовательского веб-интерфейса и интерфейса REST API. Ambari предоставляется с кластерами HDInsight под управлением Linux и используется для мониторинга кластера и внесения изменений в его конфигурацию. В этом документе вы узнаете об основных принципах работы с REST API Ambari, выполнив типичные задачи с помощью cURL.

> [!IMPORTANT]
> Примеры, использованные в этом документе, были проверены с помощью PowerShell в Windows 10 и Bash. Во многих случаях одна и та же команда работает в обоих системах. В тех случаях, где есть различия, приводятся отдельные примеры для PowerShell и Bash.

## <a name="prerequisites"></a>Предварительные требования

* [cURL.](http://curl.haxx.se/) Это кроссплатформенная служебная программа для работы с интерфейсами REST API из командной строки. В этом документе она используется для взаимодействия с REST API Ambari.

    > [!WARNING]
    > Если вы используете PowerShell, то необходимо удалить псевдоним по умолчанию для команды `curl` с помощью команды `remove-item alias:curl`. Если не удалить псевдоним, то будут возникать ошибки, подобные этой: "Не удается обработать параметр, так как имя параметра "u" неоднозначно".

* [jq.](https://stedolan.github.io/jq/) Это кроссплатформенная служебная программа командной строки для работы с документами JSON. В этом документе она используется для анализа документов JSON, возвращенных из REST API Ambari.

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) (предварительная версия) — это кроссплатформенная служебная программа командной строки для работы со службами Azure.
  
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## <a name="a-idwhatisawhat-is-ambari"></a><a id="whatis"></a>Что такое Ambari

[Apache Ambari](http://ambari.apache.org) упрощает управление Hadoop, предоставляя простой в использовании веб-интерфейс, который можно использовать для подготовки и мониторинга кластеров Hadoop, а также управления их функционированием. Разработчики могут интегрировать эти возможности в своих приложениях с помощью [интерфейсов REST API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari предоставляется по умолчанию с кластерами HDInsight на платформе Linux.

## <a name="rest-api"></a>Интерфейс REST API

Базовый универсальный код ресурса (URI) для REST API Ambari в кластерах HDInsight выглядит следующим образом: https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, где **CLUSTERNAME** — это имя вашего кластера.

> [!IMPORTANT]
> Хотя имя кластера в полном доменном имени в URI (CLUSTERNAME.azurehdinsight.net) указывается без учета регистра, другие вхождения этого URI учитывают регистр. Например, если кластер называется MyCluster, допустимые URI имеют следующий вид:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Приведенные ниже URI возвратят ошибку, так как второе вхождение имени имеет неправильный регистр.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Подключение к Ambari в HDInsight выполняется по протоколу HTTPS. При проверке подлинности подключения необходимо использовать имя учетной записи администратора (по умолчанию используется **admin**) и пароль, указанные при создании кластера.

Ниже приведен пример использования cURL для выполнения запроса GET к REST API. Замените **PASSWORD** паролем администратора кластера. Замените **CLUSTERNAME** именем кластера.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"

В результате возвращается документ JSON, который начинается со следующих сведений:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Так как это сведения в формате JSON, для работы с данными удобнее использовать средство синтаксического анализа JSON. Например, в следующем примере используется jq для отображения только элемента `health_report`.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Пример. Получение полного доменного имени для узлов кластера

При работе с HDInsight вам может потребоваться полное доменное имя (FQDN) узла кластера. FQDN можно легко получить для различных узлов в кластере с помощью следующих команд:

* **Головные узлы:** `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`

* **Рабочие узлы:** `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`

* **Узлы Zookeeper:** `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Обратите внимание, что в каждом из этих примеров используется тот же шаблон:

1. Отправка запроса к компоненту, выполняющемуся на этих узлах.

2. Получение элементов `host_name` , содержащих полное доменное имя для этих узлов.

Элемент `host_components` документа возврата содержит несколько элементов. Используйте `.host_components[]`, а затем укажите путь внутри элемента, чтобы пройтись по каждому элементу и извлечь значение из конкретного пути. Если требуется только одно значение, например первая запись FQDN, можно вернуть элементы в виде коллекции и выбрать определенную запись:

    jq '[.host_components[].HostRoles.host_name][0]'

Эта команда возвращает первое полное доменное имя из коллекции.

## <a name="example-get-the-default-storage-account-and-container"></a>Пример. Получение учетной записи хранения и контейнера по умолчанию

При создании кластера HDInsight необходимо использовать учетную запись хранения Azure и контейнер больших двоичных объектов в качестве хранилища по умолчанию для кластера. Для получения этой информации после создания кластера можно использовать Ambari. Например, это можно сделать, если требуется программно записать данные непосредственно в контейнер.

Следующая команда получит универсальный код ресурса (URI) WASB хранилища кластеров по умолчанию:

```bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

В PowerShell правила использования одинарных и двойных кавычек слегка отличаются. Используйте следующую команду из PowerShell:

```PowerShell
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["""fs.defaultFS"""] | select(. != null)'
```

> [!NOTE]
> Эта команда возвращает первую конфигурацию, применяемую к серверу (`service_config_version=1`), который содержит эти сведения. Чтобы получить значение, которое было изменено после создания кластера, может потребоваться перечислить версии конфигурации и получить последнюю из них.

В результате возвращается значение, аналогичное приведенному ниже, где **CONTAINER** — это контейнер по умолчанию, а **ACCOUNTNAM** — имя учетной записи хранения Azure.

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Затем эти сведения можно использовать с [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) для передачи или скачивания данных из контейнера.

1. Для получения уникального идентификатора для учетной записи хранения воспользуйтесь следующей командой. В следующей команде замените **ACCOUNTNAME** именем учетной записи хранения, полученным из Ambari.

        az storage account list --query "[?name=='ACCOUNTNAME'].id" --out list

2. Для получения ключа для учетной записи хранения воспользуйтесь следующей командой. Замените **STORAGEID** идентификатором учетной записи хранения.

        az storage account keys list --ids STORAGEID --query "keys[?keyName=='key1'].value" --out list

    Эта команда возвращает первичный ключ для учетной записи.

3. Используйте команду upload, чтобы сохранить файл в контейнере:
   
        az storage blob upload --account-name ACOUNTNAME --account-key ACCOUNTKEY -f FILEPATH -c CONTAINER -n BLOBPATH
   
    Замените **ACCOUNTNAME** именем учетной записи хранения. Замените **ACCOUNTKEY** ключом, полученным ранее. Замените __CONTAINER__ именем контейнера. **FILEPATH** — это путь к файлу, который необходимо передать, а **BLOBPATH** — путь в контейнере.
   
    Например, если файл должен отображаться в HDInsight по адресу wasbs://example/data/filename.txt, путь **BLOBPATH** будет выглядеть так: `example/data/filename.txt`.

## <a name="example-update-ambari-configuration"></a>Пример: изменение конфигурации Ambari

1. Получите текущую конфигурацию, которую Ambari хранит в виде "требуемой конфигурации":
   
        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
   
    Этот пример возвращает документ JSON с текущей конфигурацией (идентифицируется по значению *tag*) для компонентов, установленных в кластере. Ниже приведен пример фрагмента данных, возвращаемых из типа кластера Spark.
   
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }
   
    Из этого списка нужно скопировать имя компонента (например, **spark\_thrift\_sparkconf**) и значение **tag**.

2. Извлеките конфигурацию для компонента и значение tag с помощью следующей команды: Замените **spark-thrift-sparkconf** и **INITIAL** на компонент и значение tag, по которым хотите получить конфигурацию.
   
    ```bash
    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```PowerShell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```
   
    Curl извлекает документ JSON, а затем, чтобы внести изменения в данные для создания шаблона, используется jq. Созданный шаблон применяется для добавления и изменения значений конфигурации. В частности, он выполняет следующие действия:
   
    * Создает уникальное значение, содержащее строку version и дату, хранящуюся в **newtag**.

    * Создает корневой документ для новой требуемой конфигурации.

    * Возвращает содержимое массива `.items[]` и добавляет его в элемент **desired_config**.

    * Удаляет элементы **href**, **version** и **Config**, так как они не нужны для отправки новой конфигурации.

    * Добавляет новый элемент **tag** и задает для него значение **version#################**. Числовая часть основана на текущей дате. Каждая конфигурация должна иметь уникальное значение tag.
     
        Наконец, данные сохраняются в документе **newconfig.json**. Структура документа должна выглядеть следующим образом:
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Откройте документ **newconfig.json** и измените или добавьте значения в объекте **properties**. Следующий пример кода изменяет значение **spark.yarn.am.memory** с **1g** на **3g** и добавляет новый элемент для **spark.kryoserializer.buffer.max** со значением **256m**.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    После завершения работы сохраните файл.
4. Выполните следующую команду, чтобы отправить измененную конфигурацию в Ambari:
   
        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
   
    Эта команда передает содержимое файла **newconfig.json** в cURL-запрос, который отправляет его в кластер в качестве новой требуемой конфигурации. Запрос cURL возвращает документ JSON. Элемент **versionTag** в этом документе должен совпадать с отправленной версией, а объект **configs** содержит запрошенные изменения конфигурации.

### <a name="example-restart-a-service-component"></a>Пример. Перезапуск компонента службы

Если на этом этапе взглянуть на веб-интерфейс Ambari, вы узнаете, что необходимо перезапустить службу Spark, чтобы новая конфигурация вступила в силу. Для перезапуска службы выполните приведенные ниже действия.

1. Выполните следующую команду, чтобы перевести службу Spark в режим обслуживания.
   
        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
   
    Она отправляет документ JSON на сервер (содержится в инструкции `echo` ) для включения режима обслуживания.
    Проверить, находится ли служба в этом режиме, можно с помощью следующего запроса.
   
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
   
    При этом возвращается значение `"ON"`.

2. Затем выполните следующую команду, чтобы отключить службу.
    
    ```
    echo '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
    ```
    
    Эта команда возвращает ответ следующего вида:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    Значение `href` , возвращенное этим универсальным кодом ресурса (URI), использует внутренний IP-адрес узла кластера. Чтобы использовать его извне кластера, замените часть "10.0.0.18:8080" на полное доменное имя кластера. Например, следующая команда получает состояние запроса.
    
    ```
    curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    ```
    
    Если это значение возвращает `"COMPLETED"` , то запрос завершен.

3. После завершения предыдущего запроса используйте следующую команду для запуска службы.
   
        echo '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
   
    После перезапуска службы будут использоваться новые параметры конфигурации.

4. Наконец, используйте следующую команду для отключения режима обслуживания.
   
        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

## <a name="next-steps"></a>Дальнейшие действия

Полный справочник по REST API см. [здесь](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).




<!--HONumber=Dec16_HO1-->


