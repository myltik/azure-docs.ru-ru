<properties
   pageTitle="Советы по использованию Hadoop в HDInsight на платформе Linux | Microsoft Azure"
   description="Советы по использованию кластеров HDInsight (Hadoop) на базе Linux в привычной среде Linux, выполняемой в облаке Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2015"
   ms.author="larryfr"/>

# Сведения об использовании HDInsight в Linux (предварительная версия)

Кластеры Azure HDInsight под управлением Linux предоставляют Hadoop в привычной среде Linux, выполняемой в облаке Azure. Для большинства задач они должны работать так же, как и любые другие установки Hadoop в Linux. В этом документе рассматриваются определенные отличия, которые при этом следует учитывать.

## Имена доменов

При подключении к кластеру следует использовать полное доменное имя (FQDN) **&lt;имя_кластера>.azurehdinsight.net** или (только для SSH) **&lt;имя_кластера>.aurehdinsight.net**.

## Удаленный доступ к службам

* **Ambari (веб-версия)** — https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE]Выполните аутентификацию, а затем войдите в Ambari. Используйте для этого имя пользователя и пароль администратора кластера.
	>
	> При аутентификации используется открытый текст. Всегда используйте протокол HTTPS, чтобы обеспечить безопасное подключение.

	Хотя Ambari для кластера доступен напрямую через Интернет, некоторые функциональные возможности зависят от доступа к узлам по внутреннему доменному имени, используемому в кластере. Так как это внутреннее доменное имя, а не общедоступное, вы получите ошибку «сервер не найден», если попытаетесь получить доступ к некоторым функциям сервера через Интернет.

	Чтобы обойти эту проблему, используйте туннель SSH для проксирования веб-трафика на головном узле кластера. Используйте раздел **Туннелирование SSH** следующей статьи, чтобы создать туннель SSH от порта на локальном компьютере до кластера.

	* [Использование SSH с Hadoop под управлением Linux в HDInsight на платформе Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md) — шаги по созданию туннеля SSH с помощью команды `ssh`.

	* [Использование SSH с Hadoop под управлением Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows) — шаги по созданию PuTTY для создания туннеля SSH.

* **Ambari (REST)** — https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Выполните аутентификацию, используя имя пользователя и пароль администратора кластера.
	>
	> При аутентификации используется открытый текст. Всегда используйте протокол HTTPS, чтобы обеспечить безопасное подключение.

* **WebHCat (Templeton)** — https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Выполните аутентификацию, используя имя пользователя и пароль администратора кластера.
	>
	> При аутентификации используется открытый текст. Всегда используйте протокол HTTPS, чтобы обеспечить безопасное подключение.

* **SSH** — &lt;имя_кластера>-ssh.azurehdinsight.net через порт 22

	> [AZURE.NOTE]Доступ к головному узлу кластера можно получить только по протоколу SSH с клиентского компьютера. После подключения с головного узла можно получить доступ к рабочим узлам по протоколу SSH.

## Местоположения файлов

Связанные с Hadoop файлы можно найти на узлах кластера в папке `/usr/hdp/current`.

Примеры данных и JAR-файлы можно найти в распределенной файловой системе Hadoop или в хранилище больших двоичных объектов Azure в папке /example или wasb:///example.

## Рекомендации при работе с распределенной файловой системой Hadoop, хранилищем больших двоичных объектов Azure, а также рекомендации по использованию хранилища

В большинстве дистрибутив Hadoop распределенная файловая система Hadoop реализуется на базе локального хранилища на компьютерах в кластере. Несмотря на эффективность, в случае облачного решения с почасовой платой за вычислительные ресурсы такой подход может оказаться весьма затратным.

HDInsight использует хранилище больших двоичных объектов Azure как хранилище по умолчанию, что дает следующие преимущества:

* недорогое долговременное хранение;

* доступность из внешних служб, например веб-сайтов, служебных программ для отправки или скачивания файлов, пакетов SDK для различных языков и веб-браузеров.

Поскольку это хранилище по умолчанию для HDInsight, в большинстве случаев его можно использовать без какой-либо специальной подготовки. Например, следующая команда отображает список файлов в папке **/example/data**, которая хранится в хранилище больших двоичных объектов Azure:

	hadoop fs -ls /example/data

Возможно, для некоторых команд нужно будет указать, что вы используете хранилище больших двоичных объектов. Для этого можно добавить к команде префикс **WASB://**.

HDInsight также позволяет связать несколько учетных записей хранилища больших двоичных объектов с кластером. Для доступа к данным из учетной записи хранилища больших двоичных объектов, отличной от заданной по умолчанию, можно использовать формат **WASB://&lt;container-name>@&lt;имя_учетной_записи>.blob.core.windows.net/**. Например, ниже будет перечислено содержимое каталога **/example/data** для указанного контейнера и учетной записи хранилища больших двоичных объектов.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Какие хранилища больших двоичных объектов используются в кластере

При создании кластера вы выбираете существующие учетную запись хранения Azure и контейнер, или можете создать новые. Если вы забудете, что именно выбрали, Учетную запись хранения и контейнер можно найти следующими способами.

**Ambari API**

1. Для получения информации о конфигурации HDFS используйте следующую команду:

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. В возвращенных данных JSON найдите запись `fs.defaultFS`. В ней содержатся контейнер по умолчанию и имя учетной записи хранения в формате, аналогичному следующему:

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP]Если вы установили [jq](http://stedolan.github.io/jq/), следующее можно использовать только для возврата записи `fs.defaultFS`:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`
	
3. Чтобы найти ключ, используемый для проверки подлинности учетной записи хранения, или найти все вторичные учетные записи хранения, связанные с кластером, используйте следующую команду:

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"
		
4. В возвращенных данных JSON найдите запись, которая начинается с `fs.azure.account.key`. Остаток имени записи является именем учетной записи хранения. Например, `fs.azure.account.key.mystorage.blob.core.windows.net`. Значение, хранящееся в этой записи, является ключом, используемым для проверки подлинности учетной записи хранения.

	> [AZURE.TIP]Если вы установили [jq](http://stedolan.github.io/jq/), вы можете использовать следующую команду, чтобы вернуть список ключей и значений:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`


**Портал Azure**

1. На [портале Azure](https://manage.windowsazure.com/) выберите свой кластер HDInsight.

2. Выберите **панель мониторинга** в верхней части страницы.

3. Учетные записи хранения и контейнеры перечислены на странице в разделе **связанные ресурсы**.

	![связанные ресурсы](./media/hdinsight-hadoop-linux-information/storageportal.png)

### Как получить доступ к хранилищу больших двоичных объектов

Получить доступ к большим двоичным объектам можно не только с помощью команды Hadoop из кластера, но и множеством других способов:

* [CLI Azure для Mac, Linux и Windows](../xplat-cli.md) — это набор кроссплатформенных команд для работы с Azure. После установки используйте команду `azure storage` для получения информации по использованию хранилища, а команду `azure blob` — для получения информации о больших двоичных объектах.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage) — cценарий Python для работы с большими двоичными объектами в хранилище Azure.

* Различные пакеты SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [REST API хранилища](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## Дальнейшие действия

* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование заданий MapReduce с HDInsight](hdinsight-use-mapreduce.md)
 

<!---HONumber=July15_HO2-->