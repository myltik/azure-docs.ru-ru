<properties
	pageTitle="Использование действия сценария для установки Solr в кластере HDInsight на основе Linux | Microsoft Azure"
	description="Узнайте, как устанавливать Solr в кластерах HDInsight Hadoop на основе Linux с помощью действий сценария."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="larryfr"/>

# Установка и использование Solr на кластерах HDInsight Hadoop

В этой статье описано, как установить Solr в Azure HDInsight с помощью действия сценария. Solr представляет собой многофункциональную платформу поиска и предоставляет возможности поиска корпоративного уровня на основе данных, управляемых Hadoop. После установки Solr в кластере HDInsight вы также узнаете, как искать данные с помощью Solr.

> [AZURE.NOTE] Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Сведения об использовании Solr с кластером под управлением Windows см. в разделе [Установка и использование Solr на кластерах HDInsight Hadoop (Windows)](hdinsight-hadoop-solr-install.md).

Пример сценария, используемый в данном разделе, создает кластер Solr с определенной конфигурацией. Если вы хотите настроить кластер Solr на использование других коллекций, сегментов, схем, реплик и т. п., необходимо соответствующим образом изменить скрипт и двоичные файлы Solr.

## <a name="whatis"></a>Что такое Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) — это корпоративная платформа поиска, предоставляющая эффективные инструменты полнотекстового поиска данных. Если Hadoop обеспечивает хранение огромных объемов данных и управление ими, то Apache Solr предоставляет возможности поиска для быстрого извлечения этих данных. Этот раздел содержит инструкции по настройке кластера HDInsight для установки Solr.

> [AZURE.WARNING] Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Техническая поддержка Майкрософт поможет вам выявить и решить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты, такие как Solr, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/ru-RU/home?forum=hdinsight) и [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты проектов по адресу [http://apache.org](http://apache.org), например для [Hadoop](http://hadoop.apache.org/).

## Что делает сценарий

Этот сценарий вносит следующие изменения в кластер HDInsight:

* Устанавливает Solr в `/usr/hdp/current/solr`.
* Создает нового пользователя с именем __solrusr__, который используется для запуска службы Solr.
* Делает пользователя __solrusr__ владельцем `/usr/hdp/current/solr`.
* Добавляет конфигурацию [Upstart](http://upstart.ubuntu.com/), которая запускает Solr при перезапуске узла кластера. Solr также автоматически запускается на узлах кластера после установки.

## <a name="install"></a>Установка Solr с помощью действий сценария

Пример сценария для установки Solr в кластер HDInsight доступен по следующему адресу.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Этот раздел содержит инструкции по использованию примера сценария при создании нового кластера с помощью портала Azure.

> [AZURE.NOTE] Для выполнения действий этого сценария также можно использовать Azure PowerShell, Azure CLI, пакет SDK .NET для HDInsight или шаблоны Azure Resource Manager. Действия сценария также можно применять к уже работающим кластерам. Дополнительные сведения см. в статье [Настройка кластеров HDInsight с помощью действий сценария](hdinsight-hadoop-customize-cluster-linux.md).

1. Начните подготовку кластера с помощью действий, описанных в разделе [Подготовка кластеров HDInsight под управлением Linux](hdinsight-provision-linux-clusters.md#portal), но не завершайте ее.

2. В колонке **Необязательная конфигурация** выберите **Действия сценария** и введите следующие сведения:

	* __ИМЯ__: введите понятное имя для действия сценария.
	* __УНИВЕРСАЛЬНЫЙ КОД РЕСУРСА (URI) СКРИПТА__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh.
	* __ГОЛОВНОЙ__: установите флажок.
	* __РАБОЧАЯ РОЛЬ__: установите флажок.
	* __ZOOKEEPER__: установите этот флажок для установки на узле Zookeeper.
	* __ПАРАМЕТРЫ__: оставьте это поле пустым.

3. В нижней части раздела **Действия сценария** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, нажмите кнопку **Выбрать** в нижней части колонки **Необязательная конфигурация**, чтобы сохранить дополнительные настройки.

4. Продолжите подготовку кластера к работе, как описано в статье [Подготовка кластеров HDInsight на основе Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>Как использовать Solr в HDInsight

### Индексирование данных

Необходимо начать с индексирования системой Solr нескольких файлов данных. Затем можно использовать Solr для выполнения запросов поиска в индексированных данных. Выполните следующие действия, чтобы добавить пример данных в Solr и создать запрос к ним.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	> [AZURE.IMPORTANT] В действиях, описанных ниже, для подключения к веб-интерфейсу Solr используется туннель SSL. Чтобы выполнить эти действия, установите туннель SSL и настройте свой браузер для его использования.
	>
	> Дополнительные сведения см. в статье [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, ResourceManager, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).

2. Используйте следующие команды, чтобы получить образец данных индекса Solr:

		cd /usr/hdp/current/solr/example/exampledocs
		java -jar post.jar solr.xml monitor.xml

	В консоли будут выведены такие выходные данные:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	Служебная программа post.jar индексирует Solr с использованием двух примеров документов — **solr.xml** и **monitor.xml**. Они сохраняются в каталоге __collection1__ в Solr.

3. Используйте следующую команду, чтобы создать запрос к интерфейсу REST API, который предоставляется Solr:

		curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

	В результате создается запрос к __collection1__ на получение всех документов, соответствующих условию __\*:\*__ (в строке запроса принимает вид \*%3A\*), с возвратом ответа в формате JSON. Ответ должен выглядеть примерно так:

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

### Использование панели мониторинга Solr

Панель мониторинга Solr — это веб-интерфейс для работы с Solr через веб-браузер. Прямой доступ к панели мониторинга Solr через Интернет из кластера HDInsight невозможен. Для этого следует использовать туннель SSH. Дополнительные сведения об использовании туннеля SSH см. в статье [Использование туннелирования SSH для доступа к веб-интерфейсу Ambari, ResourceManager, JobHistory, NameNode, Oozie и другим веб-интерфейсам](hdinsight-linux-ambari-ssh-tunnel.md).

Установив туннель SSH, выполните следующие действия, чтобы начать использовать панель мониторинга Solr.

1. Определите имя основного головного узла:

    1. Используйте SSH, чтобы подключиться к кластеру через порт 22. Например, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` где __USERNAME__ — это имя пользователя SSH, а __CLUSTERNAME__ — имя кластера.

        Дополнительные сведения об использовании SSH см. в следующих документах:

        * [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
    3. Чтобы получить полное имя узла, используйте следующую команду:

            hostname -f

        Вы получите приблизительно такой результат:

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
        Это имя узла, которое будет использоваться дальше.
    
1. В окне браузера введите адрес __http://HOSTNAME:8983/solr/#/__, где __HOSTNAME\_\_ — это имя, определенное на предыдущих этапах.

    Этот запрос должен перенаправляться через SSH-туннель на головной узел вашего кластера HDInsight. Вы должны увидеть страницу, аналогичную показанной ниже:

	![Изображение панели мониторинга Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. В левой части окна в раскрывающемся списке **Core Selector** (Выбор ядра) выберите **collection1**. В разделе __collection1__ будет отображено несколько записей.

3. В разделе __collection1__ выберите элемент __Query__ (Запрос). На странице поиска введите следующие значения:

	* В текстовом поле **q** введите **\*:**\*. Это позволяет возвратить все документы, индексированные в Solr. Если требуется найти в документах конкретную строку, ее также можно ввести в этом поле.

	* В текстовом поле **wt** выберите формат выходных данных. По умолчанию используется **JSON**.

	Наконец, нажмите кнопку **Execute Query** (Выполнить запрос) в нижней части панели поиска.

	![Использование действия сценария для настройки кластера](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

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

### Запуск и остановка Solr

Если вы хотите вручную остановить или запустить Solr, используйте следующие команды:

	sudo stop solr

	sudo start solr

## Резервное копирование индексированных данных

Опыт показывает, что стоит выполнять резервное копирование индексированных данных с узлов кластера Solr в хранилище больших двоичных объектов Azure. Для этого выполните следующие действия:

1. Подключитесь к кластеру с помощью SSH, а затем используйте следующую команду, чтобы получить имя головного узла:

        hostname -f
        
2. Чтобы создать моментальный снимок индексированных данных, используйте следующую команду. Замените __HOSTNAME__ именем, полученным от предыдущей команды:

		curl http://HOSTNAME:8983/solr/replication?command=backup

	Вы должны получить примерно следующий ответ:

		<?xml version="1.0" encoding="UTF-8"?>
		<response>
		  <lst name="responseHeader">
		    <int name="status">0</int>
		    <int name="QTime">9</int>
		  </lst>
		  <str name="status">OK</str>
		</response>

2. Затем перейдите в каталог __/usr/hdp/current/solr/example/solr__. Здесь будут размещаться подкаталоги для каждой коллекции. Каждый каталог коллекции содержит каталог __data__, в котором хранится моментальный снимок этой коллекции.

	Например, если вы создали индекс для образцов документов с помощью описанных выше действий, в каталоге __/usr/hdp/current/solr/example/solr/collection1/data__ появится каталог с именем __snapshot.###########__, где вместо символов # указываются дата и время создания моментального снимка.

3. Создайте сжатый архив папки с моментальным снимком, используя команду, подобную следующей:

		tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

	В результате будет создан новый архив с именем __snapshot.20150806185338855.tgz__, в который будет помещено содержимое каталога __snapshot.20150806185338855__.

3. Впоследствии вы сможете сохранить архив в основном хранилище кластера, используя следующую команду:

	hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

	> [AZURE.NOTE] Возможно, вам понадобится создать отдельный каталог для хранения моментальных снимков Solr. Пример: `hadoop fs -mkdir /solrbackup`.

Дополнительные сведения о резервном копировании и восстановлении Solr см. в статье [Создание и восстановление резервных копий SolrCore](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores).


## Дополнительные материалы

- [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Hue — это веб-интерфейс, который позволяет легко создавать, запускать и сохранять задания Pig и Hive, а также просматривать содержимое хранилища по умолчанию для вашего кластера HDInsight.

- [Установка R в кластерах HDInsight][hdinsight-install-r]. Используйте настройки кластера для установки R в кластерах HDInsight Hadoop. R — это открытый язык программирования и свободная программная среда для статистических вычислений. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.

- [Установка Giraph в кластерах HDInsight](hdinsight-hadoop-giraph-install-linux.md). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.

- [Установка Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Установить Hue в кластерах HDInsight Hadoop можно при помощи настройки кластера. Hue — это набор веб-приложений, используемых для взаимодействия с кластером Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0921_2016-->