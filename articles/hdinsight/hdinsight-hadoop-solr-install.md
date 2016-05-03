<properties
	pageTitle="Использование действия сценария для установки Solr в кластере Hadoop | Microsoft Azure"
	description="Узнайте, как настроить кластер HDInsight с Solr помощью действия сценария."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="nitinme"/>

# Установка и использование Solr на кластерах HDInsight Hadoop

Научитесь настраивать кластер HDInsight на основе Windows с Solr с помощью сценария действия и использовать Solr для поиска данных. Сведения об использовании Solr с кластером под управлением Linux см. в разделе [Установка и использование Solr в кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
Solr можно установить в кластере любого типа (Hadoop, Storm, HBase, Spark) в HDInsight в Azure, воспользовавшись *Действием сценария*. Пример сценария для установки Solr в кластере HDInsight доступен в большом двоичном объекте службы хранилища Azure (доступ только для чтения): [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

Пример скрипта работает только с кластером HDInsight версии 3.1. Дополнительную информацию о версиях кластера HDInsight см. в статье [Новые возможности версий кластеров Hadoop, предоставляемых HDInsight](hdinsight-component-versioning.md).

Пример скрипта, используемый в данном разделе, создает кластер Solr на основе Windows с определенной конфигурацией. Если вы хотите настроить кластер Solr на использование других коллекций, сегментов, схем, реплик и т. п., необходимо соответствующим образом изменить скрипт и двоичные файлы Solr.

**Связанные статьи**

- [Установка и использование Solr в кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md). Общие сведения о создании кластеров HDInsight
- [Настройка кластеров HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария
- [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md)


## Что такое Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> — это корпоративная платформа поиска, предоставляющая многофункциональные инструменты полнотекстового поиска данных. Если Hadoop обеспечивает хранение огромных объемов данных и управление ими, то Apache Solr предоставляет возможности поиска для быстрого извлечения этих данных.

## Установка Solr с помощью портала

1. Начните создание кластера с помощью параметра **НАСТРАИВАЕМОЕ СОЗДАНИЕ**, как описано в разделе [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md#portal).
2. На странице **Действия сценариев** мастера щелкните **Добавить действие сценария** для предоставления информации о данном действии сценария, как показано ниже:

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Использование действия сценария для настройки кластера")

	<table border='1'>
		<tr><th>Свойство</th><th>Значение</th></tr>
		<tr><td>Имя</td>
			<td>Укажите имя для действия сценария. Например, <b>Установить Solr</b>.</td></tr>
		<tr><td>URI-адрес сценария</td>
			<td>Укажите URI для сценария, который вызывается для настройки кластера. Например, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i>.</td></tr>
		<tr><td>Тип узла</td>
			<td>Укажите узлы, на которых выполняется сценарий настройки. Можно выбрать одно из трех значений: <b>Все узлы</b>, <b>Только головные узлы</b> или <b>Только рабочие узлы</b>.
		<tr><td>Параметры</td>
			<td>Укажите параметры, если они требуются для сценария. Сценарий для установки Solr не требует никаких параметров, поэтому можно оставить это поле пустым.</td></tr>
	</table>

	Можно добавить несколько действий сценария для установки нескольких компонентов в кластере. После добавления скриптов щелкните флажок, чтобы начать создание кластера.


## Использование Solr

Необходимо начать с индексирования системой Solr нескольких файлов данных. Затем можно использовать Solr для выполнения запросов поиска в индексированных данных. Выполните следующие действия, чтобы использовать Solr в кластере HDInsight.

1. **Используйте протокол удаленного рабочего стола (RDP) для удаленного подключения к кластеру HDInsight с установленной Solr**. На портале Azure включите удаленный рабочий стол для созданного кластера с установленной Solr, а затем подключитесь к кластеру. Указания см. в разделе [Подключение к кластерам HDInsight с помощью RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Выполните индексирование Solr, передав файлы данных**. При индексировании системы Solr вы помещаете в нее документы, по которым может потребоваться выполнить поиск. Чтобы выполнить индексирование Solr, подключитесь к кластеру по протоколу удаленного рабочего стола, перейдите на рабочий стол, откройте командную строку Hadoop и перейдите к **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**. Выполните следующую команду:

		java -jar post.jar solr.xml monitor.xml

	В консоли будут выведены такие выходные данные:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	Служебная программа post.jar индексирует Solr с использованием двух примеров документов — **solr.xml** и **monitor.xml**. Служебная программа post.jar и примеры документов входят в состав установки Solr.

3. **Воспользуйтесь панелью мониторинга Solr для поиска по индексированным документам**. В сеансе связи с кластером HDInsight по протоколу удаленного рабочего стола откройте Internet Explorer и запустите панель мониторинга Solr по адресу **http://headnodehost:8983/solr/#/**. В левой области в раскрывающемся списке **Базовый селектор** выберите **collection1**, а затем щелкните **Запрос**. Например, чтобы выбрать и возвратить все документы в Solr, укажите следующие значения:

	* В текстовом поле **q** введите ***:***. Это позволяет возвратить все документы, индексированные в Solr. Если требуется найти в документах конкретную строку, ее также можно ввести в этом поле.
	
	* В текстовом поле **wt** выберите формат выходных данных. По умолчанию используется **JSON**. Щелкните **Выполнить запрос**.

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Выполнение запроса на панели мониторинга Solr")
	
	В выходных данных возвращаются два документа, которые использовались при индексировании Solr. Результат выглядит следующим образом:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }


4. **Рекомендуется создать резервную копию индексированных данных из Solr в хранилище больших двоичных объектов Azure, связанном с кластером HDInsight**. Опыт показывает, что стоит выполнять резервное копирование индексированных данных с узлов кластера Solr в хранилище больших двоичных объектов Azure. Для этого выполните следующие действия:

	1. В сеансе связи по протоколу RDP откройте Internet Explorer и выберите следующий URL-адрес:

			http://localhost:8983/solr/replication?command=backup

		Вы должны получить примерно следующий ответ:

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. В удаленном сеансе перейдите к {SOLR\_HOME}\\{Collection}\\data. Для кластера, созданного с помощью примера сценария, это должна быть папка **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**. В этом расположении вы увидите папку моментальных снимков с именем, похожим на **snapshot.*метка\_времени***.

	3. Запакуйте эту папку моментальных снимков и отправьте ее в хранилище больших двоичных объектов Azure. В командной строке Hadoop перейдите к расположению папки моментальных снимков, используя следующую команду:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Эта команда копирует моментальный снимок в папку /example/data/ в контейнере учетной записи хранения по умолчанию, связанной с этим кластером.

## Установка Solr с помощью Azure PowerShell

Обратитесь к статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell). В этом примере показано, как установить Spark с помощью Azure PowerShell. Необходимо изменить сценарий для использования [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## Установка Solr с помощью пакета SDK для .NET

Обратитесь к статье [Настройка кластеров HDInsight с помощью действия сценария](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). В этом примере показано, как установить Spark с помощью пакета SDK для .NET. Необходимо изменить сценарий для использования [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## См. также

- [Установка и использование Solr в кластерах HDInsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Создание кластеров Hadoop в HDInsight](hdinsight-provision-clusters.md). Общие сведения о создании кластеров HDInsight
- [Настройка кластеров HDInsight с помощью действия сценария][hdinsight-cluster-customize]. Общая информация о настройке кластеров HDInsight с помощью действия сценария
- [Разработка скриптов действия сценария для HDInsight](hdinsight-hadoop-script-actions.md)
- [Установка и использование Spark в HDInsight][hdinsight-install-spark]. Пример действия сценария для установки Spark
- [Установка R в кластерах HDInsight][hdinsight-install-r]. Пример действия сценария для установки R
- [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install.md). Пример действия сценария для установки Giraph


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=AcomDC_0420_2016-->