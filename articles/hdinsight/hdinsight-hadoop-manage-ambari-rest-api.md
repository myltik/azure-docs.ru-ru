<properties
   pageTitle="Мониторинг кластеров HDInsight и управление ими с помощью интерфейса REST API Ambari | Microsoft Azure"
   description="Узнайте, как использовать Ambari для наблюдения за кластерами HDInsight на платформе Linux и управления их функционированием. В этом документе рассказывается об использовании интерфейса REST API Ambari, предоставляемого с кластерами HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2015"
   ms.author="larryfr"/>

#Управление кластерами HDInsight с помощью REST API Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari упрощает управление кластером Hadoop и его мониторинг за счет удобного пользовательского веб-интерфейса и интерфейса REST API. Ambari предоставляется с кластерами HDInsight под управлением Linux и используется для мониторинга кластера и внесения изменений в его конфигурацию. Из этого документа вы узнаете основные принципы работы с REST API Ambari, выполняя стандартные задачи, такие как поиск полного доменного имени узлов кластера или поиск учетной записи хранения по умолчанию, используемой кластером.

> [AZURE.NOTE]Информация в этой статье относится только к кластерам HDInsight под управлением Linux. Для кластеров HDInsight под управлением Windows через REST API Ambari доступен только ограниченный набор функций мониторинга. См. статью [Отслеживание кластеров Hadoop в HDInsight с помощью API Ambari для ОС Windows](hdinsight-monitor-use-ambari-api.md).

##Предварительные требования

* [cURL](http://curl.haxx.se/). cURL — это кроссплатформенная служебная программа, которая может использоваться для работы с интерфейсами REST API из командной строки. В этом документе она используется для взаимодействия с REST API Ambari.
* [jq](https://stedolan.github.io/jq/). jq — это кроссплатформенная служебная программа командной строки для работы с документами JSON. В этом документе она используется для анализа документов JSON, возвращенных из REST API Ambari.

##<a id="whatis"></a>Что такое Ambari

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> упрощает управление Hadoop, предоставляя простой в использовании веб-интерфейс, который можно использовать для подготовки и мониторинга кластеров Hadoop, а также управления их функционированием. Разработчики могут интегрировать эти возможности в своих приложениях с помощью <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">интерфейсов API REST Ambari</a>.

Ambari предоставляется по умолчанию с кластерами HDInsight на платформе Linux.

##Интерфейс REST API

Базовый универсальный код ресурса (URI) для интерфейса REST API Ambari в HDInsight: https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, где __CLUSTERNAME__ — это имя кластера.

> [AZURE.IMPORTANT]Подключение к Ambari в HDInsight выполняется по протоколу HTTPS. Кроме того, необходимо выполнить вход в Ambari с помощью имени учетной записи администратора (по умолчанию используется имя __admin__) и пароля, указанных при создании кластера.

Ниже приведен пример использования cURL для выполнения запроса GET к REST API:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Если выполнить эту команду, заменив __PASSWORD__ паролем администратора для кластера, а __CLUSTERNAME__ — именем кластера, можно получить документ JSON, который начинается со следующих сведений:

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

Так как это сведения в формате JSON, для получения данных удобнее использовать средство синтаксического анализа JSON. Например, если вы хотите узнать количество оповещений, содержащихся в элементе __Host/host\_status/ALERT__, вы можете использовать следующую команду, чтобы сразу получить нужное значение:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
Эта команда получает документ JSON, а затем передает выходные данные в jq. `'.Clusters.health_report."Host/host_status/ALERT"'` указывает в JSON-документе элемент, который требуется получить.

> [AZURE.NOTE]Элемент __Host/host\_status/ALERT__ заключен в кавычки, которые указывают, что / является частью имени элемента. Дополнительные сведения об использовании программы jq см. на [веб-сайте jq](https://stedolan.github.io/jq/).

##Пример. Получение полного доменного имени для узлов кластера

При работе с HDInsight вам может потребоваться полное доменное имя (FQDN) узла кластера. FQDN можно легко получить для различных узлов в кластере с помощью следующих команд:

* __Головные узлы__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Рабочие узлы__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Узлы Zookeeper__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Обратите внимание, что в каждой из этих команд используется один и тот же шаблон запроса компонента, который выполняется на этих узлах, и последующего извлечения элементов `host_name`, которые содержат полное доменное имя для этих узлов.

Элемент `host_components` документа возврата содержит несколько элементов. Используйте `.host_components[]`, а затем укажите путь внутри элемента, чтобы пройтись по каждому элементу и извлечь значение из конкретного пути. Если требуется только одно значение, например первая запись FQDN, можно вернуть элементы в виде коллекции и выбрать определенную запись:

    jq '[.host_components[].HostRoles.host_name][0]'

Эта команда возвращает первое полное доменное имя из коллекции.

##Пример. Получение учетной записи хранения и контейнера по умолчанию

При создании кластера HDInsight необходимо использовать учетную запись хранения Azure и контейнер больших двоичных объектов в качестве хранилища по умолчанию для кластера. Для получения этой информации после создания кластера можно использовать Ambari. Например, это можно сделать, если требуется программно записать данные непосредственно в контейнер.

Следующая команда получит универсальный код ресурса (URI) WASB хранилища кластеров по умолчанию:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]Эта команда возвращает первую конфигурацию, применяемую к серверу (`service_config_version=1`), который будет содержать эти сведения. Чтобы получить значение, которое было изменено после создания кластера, может потребоваться перечислить версии конфигурации и получить последнюю из них.

Будет возвращено значение, аналогичное приведенному ниже, где __CONTAINER__ — это контейнер по умолчанию, а __ACCOUNTNAME__ — имя учетной записи хранения Azure.

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Затем эти сведения можно использовать с [интерфейсом командной строки Azure](../xplat-cli-install.md) для отправки или загрузки данных из контейнера. Например:

1. Получите группу ресурсов для учетной записи хранения. Замените __ACCOUNTNAME__ именем учетной записи хранения, полученным из Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Эта команда возвращает имя группы ресурсов для учетной записи.
    
    > [AZURE.NOTE]Если эта команда не возвращает данные, может потребоваться изменить режим интерфейса командной строки Azure на режим диспетчера ресурсов Azure и запустить команду еще раз. Чтобы переключиться в режим диспетчера ресурсов Azure, выполните следующую команду:
    >
    > `azure config mode arm`
    
2. Получите ключ для учетной записи хранения. Замените __GROUPNAME__ именем группы ресурсов, полученным на предыдущем шаге. Замените __ACCOUNTNAME__ именем учетной записи хранения:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Эта команда возвращает первичный ключ для учетной записи.
    
3. Используйте команду upload, чтобы сохранить файл в контейнере:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Замените __ACCOUNTNAME__ именем учетной записи хранения. Замените __ACCOUNTKEY__ ключом, полученным ранее. __FILEPATH__ — это путь к файлу, который необходимо передать, а __BLOBPATH__ — путь в контейнере.

    Например, если файл должен отображаться в HDInsight по адресу wasb://example/data/filename.txt, путь __BLOBPATH__ будет выглядеть так: `example/data/filename.txt`.

##Дальнейшие действия

Полный справочник по REST API см. в статье [Справочник по API Ambari версии 1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE]Так как интерфейсом Ambari управляет облачная служба HDInsight, некоторые функции (например, добавление узлов в кластер, удаление их из него или добавление новых служб) отключены.

<!---HONumber=AcomDC_0107_2016-->