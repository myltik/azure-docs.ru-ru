---
title: Мониторинг и управление Hadoop с помощью Ambari REST API в Azure HDInsight | Документация Майкрософт
description: Сведения об использовании Ambari для отслеживания и администрирования кластеров Hadoop в Azure HDInsight. В этом документе рассказывается об использовании интерфейса REST API Ambari, предоставляемого с кластерами HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: 55017b139ce89fa74a8105da05792024ecee86b2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177960"
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Управление кластерами HDInsight с помощью REST API Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Узнайте, как использовать Ambari REST API для отслеживания и администрирования кластеров Hadoop в Azure HDInsight.

Apache Ambari упрощает управление кластером Hadoop и его мониторинг за счет удобного пользовательского веб-интерфейса и интерфейса REST API. Ambari предоставляется с кластерами HDInsight, которые работают под управлением операционной системы Linux. Ambari можно использовать для мониторинга кластера и внесения изменений в его конфигурацию.

## <a id="whatis"></a>Что такое Ambari

[Apache Ambari](http://ambari.apache.org) предоставляет веб-интерфейс, который можно использовать для мониторинга кластеров Hadoop и управления ими. Разработчики могут интегрировать эти возможности в своих приложениях с помощью [интерфейсов REST API Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari предоставляется по умолчанию с кластерами HDInsight на платформе Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Как использовать интерфейс REST API Ambari

> [!IMPORTANT]
> Чтобы использовать сведения и примеры, описанные в этом документе, нужен кластер HDInsight с операционной системой Linux. Дополнительные сведения см. в статье [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Этот документ содержит примеры для оболочек Bourne (Bash) и PowerShell. Примеры Bash протестированы с помощью GNU Bash версии 4.3.11, но они должны работать и с другими оболочками Unix. Примеры PowerShell проверялись с помощью PowerShell 5.0, но они должны работать с PowerShell 3.0 или более поздней версии.

Если вы используете __оболочку Bourne__ (Bash), установите следующие компоненты:

* [cURL](http://curl.haxx.se/) — это служебная программа для работы с интерфейсами REST API из командной строки. В этом документе она используется для взаимодействия с REST API Ambari.

Если вы используете Bash или PowerShell, установите также [jq](https://stedolan.github.io/jq/). jq — это служебная программа для работы с документами JSON. Она используется во **всех** примерах Bash и в **одном** примере PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>Базовый универсальный код ресурса для REST API Ambari

Базовый URI для REST API Ambari в HDInsight — https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, где **CLUSTERNAME** — это имя вашего кластера.

> [!IMPORTANT]
> Хотя имя кластера в полном доменном имени в URI (CLUSTERNAME.azurehdinsight.net) указывается без учета регистра, другие вхождения этого URI учитывают регистр. Например, если кластер называется `MyCluster`, допустимые URI выглядят так:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Приведенные ниже URI возвратят ошибку, так как второе вхождение имени имеет неправильный регистр.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Authentication

Подключение к Ambari в HDInsight выполняется по протоколу HTTPS. Используйте имя учетной записи администратора (по умолчанию — **admin**) и пароль, указанные при создании кластера.

## <a name="examples-authentication-and-parsing-json"></a>Примеры. Проверка подлинности и анализ JSON

В следующих примерах показано, как отправить запрос GET к базовому интерфейсу REST API Ambari:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Примеры Bash в этом документе основаны на следующих допущениях.
>
> * Имя входа в кластер по умолчанию имеет значение `admin`.
> * `$CLUSTERNAME` содержит имя кластера. Это значение можно задать с помощью команды `set CLUSTERNAME='clustername'`.
> * При появлении запроса введите пароль, чтобы войти на кластер (администратор).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Примеры PowerShell в этом документе основаны на следующих допущениях.
>
> * `$creds` — объект учетных данных, который содержит имя входа администратора и пароль для кластера. Это значение можно задать с помощью команды `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"`, а затем указать учетные данные при появлении запроса.
> * `$clusterName` — строка, которая содержит имя кластера. Это значение можно задать с помощью команды `$clusterName="clustername"`.

Оба примера возвращают документ JSON, который начинается со следующих сведений:

```json
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
    ...
```

### <a name="parsing-json-data"></a>Анализ данных JSON

В следующем примере используется программа `jq` для анализа документа ответа JSON и отображения из результатов только сведений `health_report`.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

В PowerShell 3.0 и более поздних версий доступен командлет `ConvertFrom-Json`, преобразующий документ JSON в объект, который лучше работает с PowerShell. В следующем примере используется программа `ConvertFrom-Json` для отображения из результатов только сведений `health_report`.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Хотя большинство примеров в этом документе используют `ConvertFrom-Json` для отображения элементов из документа ответа, пример [конфигурации обновления Ambari ](#example-update-ambari-configuration) использует jq. jq используется в этом примере для создания нового шаблона из документа ответа JSON.

Полный справочник по REST API см. [здесь](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Пример. Получение полного доменного имени для узлов кластера

При работе с HDInsight вам может потребоваться полное доменное имя (FQDN) узла кластера. FQDN можно легко получить для разных узлов в кластере с помощью следующих команд.

* **Все узлы**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **Головные узлы**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Рабочие узлы**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Узлы Zookeeper**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Пример. Получение внутреннего IP-адреса узлов кластера

> [!IMPORTANT]
> IP-адреса, возвращенные в примерах из этого раздела, недоступны напрямую через Интернет. Они доступны только в пределах виртуальной сети Azure, содержащей кластер HDInsight.
>
> Дополнительные сведения о работе с виртуальными сетями и HDInsight см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](hdinsight-extend-hadoop-virtual-network.md).

Чтобы найти IP-адрес, необходимо знать внутренние полные доменные имена (FQDN) узлов кластера. Если у вас есть полное доменное имя, вы можете получить IP-адрес узла. В следующих примерах у Ambari сначала запрашивается полное доменное имя для всех узлов, а затем — IP-адрес каждого узла.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> В предыдущих примерах запрашивается пароль. В этом примере показано многократное выполнение команды `curl`, поэтому пароль указывается как `$PASSWORD` во избежание вывода нескольких запросов.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Пример. Получение хранилища по умолчанию

При создании кластера HDInsight в качестве хранилища по умолчанию для кластера необходимо использовать учетную запись хранения Azure или Data Lake Store. Для получения этой информации после создания кластера можно использовать Ambari. Например, если нужно выполнить операции чтения или записи в контейнере за пределами HDInsight.

Следующие примеры извлекают конфигурацию хранилища, используемого в кластере по умолчанию.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Эти примеры возвращают первую конфигурацию, применяемую к серверу (`service_config_version=1`), который содержит эти сведения. Чтобы получить значение, которое было изменено после создания кластера, может потребоваться перечислить версии конфигурации и получить последнюю из них.

Возвращаемое значение будет выглядеть, как один из следующих примеров:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` — это значение указывает, что кластер использует учетную запись хранения Azure в качестве хранилища по умолчанию. Значение `ACCOUNTNAME` — имя учетной записи хранилища. Часть `CONTAINER` — это имя контейнера больших двоичных объектов в учетной записи хранения. Контейнер является корнем совместимого хранилища HDFS для кластера.

* `adl://home` — это значение указывает, что кластер использует Azure Data Lake Store в качестве хранилища по умолчанию.

    Чтобы найти имя учетной записи Data Lake Store, используйте следующие примеры:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Возвращается похожее значение: `ACCOUNTNAME.azuredatalakestore.net`, где `ACCOUNTNAME` — имя учетной записи Data Lake Store.

    Чтобы найти каталог в Data Lake Store, содержащий хранилище кластера, используйте следующие примеры:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Вернется похожее значение: `/clusters/CLUSTERNAME/`. Это значение представляет путь в учетной записи Data Lake Store. Путь является корнем совместимой файловой системы HDFS для кластера. 

> [!NOTE]
> Командлет `Get-AzureRmHDInsightCluster`, доступный в [Azure PowerShell](/powershell/azure/overview), также возвращает сведения о хранилище для кластера.


## <a name="example-get-configuration"></a>Пример. Получение конфигурации

1. Получите конфигурации, которые доступны для кластера.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Этот пример возвращает документ JSON с текущей конфигурацией (идентифицируется по значению *tag*) для компонентов, установленных в кластере. Ниже приведен пример фрагмента данных, возвращаемых из типа кластера Spark.
   
   ```json
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
   ```

2. Извлеките конфигурацию для интересующего вас компонента. В следующем примере замените `INITIAL` значением тега, возвращенным из предыдущего запроса.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Этот пример возвращает документ JSON, содержащий текущую конфигурацию для компонента `core-site`.

## <a name="example-update-configuration"></a>Пример. Обновление конфигурации

1. Получите текущую конфигурацию, которую Ambari хранит в виде "требуемой конфигурации":

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Этот пример возвращает документ JSON с текущей конфигурацией (идентифицируется по значению *tag*) для компонентов, установленных в кластере. Ниже приведен пример фрагмента данных, возвращаемых из типа кластера Spark.
   
    ```json
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
    ```
   
    Из этого списка нужно скопировать имя компонента (например, **spark\_thrift\_sparkconf**) и значение **tag**.

2. Извлеките конфигурацию для компонента и значение tag с помощью следующих команд:
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Замените **spark-thrift-sparkconf** и **INITIAL** на компонент и значение tag, по которым хотите получить конфигурацию.
   
    jq используется для преобразования данных, извлеченных из HDInsight в новый шаблон конфигурации. В частности, эти примеры выполняют следующие действия.
   
    * Создает уникальное значение, содержащее строку version и дату, хранящуюся в `newtag`.

    * Создает корневой документ для новой требуемой конфигурации.

    * Возвращает содержимое массива `.items[]` и добавляет его в элемент **desired_config**.

    * Удаляет элементы `href`, `version` и `Config`, так как они не нужны для отправки новой конфигурации.

    * Добавляет элемент `tag` со значением `version#################`. Числовая часть основана на текущей дате. Каждая конфигурация должна иметь уникальное значение tag.
     
    Наконец, данные сохраняются в документе `newconfig.json`. Структура документа должна выглядеть следующим образом:
     
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

3. Откройте документ `newconfig.json` и измените или добавьте значения в объекте `properties`. Следующий пример изменяет значение `"spark.yarn.am.memory"` с `"1g"` на `"3g"`. Он также добавляет `"spark.kryoserializer.buffer.max"` со значением `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    После завершения работы сохраните файл.

4. Выполните следующие команды, чтобы отправить обновленную конфигурацию в Ambari:
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Эти команды передают содержимое файла **newconfig.json** в кластер в качестве новой требуемой конфигурации. Запрос возвращает документ JSON. Элемент **versionTag** в этом документе должен совпадать с отправленной версией, а объект **configs** содержит запрошенные изменения конфигурации.

### <a name="example-restart-a-service-component"></a>Пример. Перезапуск компонента службы

На этом этапе веб-интерфейс Ambari указывает на то, что вам необходимо перезапустить службу Spark, чтобы новая конфигурация вступила в силу. Для перезапуска службы выполните приведенные ниже действия.

1. Выполните следующую команду, чтобы перевести службу Spark в режим обслуживания.

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Эти команды отправляют документ JSON на сервер, который включает режим обслуживания. Проверить, находится ли служба в этом режиме, можно с помощью следующего запроса.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    Возвращается значение `ON`.

2. Затем выполните следующую команду, чтобы отключить службу.

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    Ответ будет выглядеть примерно так:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > Значение `href` , возвращенное этим универсальным кодом ресурса (URI), использует внутренний IP-адрес узла кластера. Чтобы использовать его извне кластера, замените часть "10.0.0.18:8080" на полное доменное имя кластера. 
    
    Например, следующие команды получают состояние запроса:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Ответ `COMPLETED` указывает, что запрос завершен.

3. После завершения предыдущего запроса используйте следующую команду для запуска службы.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    Теперь служба использует новую конфигурацию.

4. Наконец, используйте следующую команду для отключения режима обслуживания.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Дополнительная информация

Полный справочник по REST API см. [здесь](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

