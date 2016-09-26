<properties
	pageTitle="Установка и использование Giraph в кластерах HDInsight (Hadoop) под управлением Linux | Microsoft Azure"
	description="Узнайте, как устанавливать Giraph в кластерах HDInsight на основе Linux с помощью действий сценария. Действия сценария позволяют настроить кластер во время создания, изменяя его конфигурацию или устанавливая службы и служебные программы."
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
	ms.date="08/02/2016"
	ms.author="larryfr"/>

# Установка Giraph в кластерах HDInsight Hadoop и использование Giraph для обработки диаграмм больших объемов

Giraph можно установить в кластер Hadoop любого типа в Azure HDInsight, используя **действие сценария** для настройки кластера.

В этом разделе описано, как установить Giraph с помощью действия сценария. После установки Giraph вы также научитесь использовать Giraph для наиболее типичных приложений для обработки крупномасштабных графов.

> [AZURE.NOTE] Информация, приведенная в этой статье, относится только к кластерам HDInsight под управлением Linux. Сведения о работе R с кластером под управлением Windows см. в статье [Установка Giraph в кластерах HDInsight Hadoop (Windows)](hdinsight-hadoop-giraph-install.md).

## <a name="whatis"></a>Что такое Giraph

[Apache Giraph](http://giraph.apache.org/) позволяет обрабатывать графы с помощью Hadoop и может использоваться с Azure HDInsight. Графы моделируют взаимоотношения между объектами, такие как подключения между маршрутизаторами в большой сети, подобной Интернету, или взаимоотношения между людьми в социальных сетях (иногда называется социальным графом). Обработка графов дает возможность делать выводы о взаимоотношениях объектов в графе, таких как:

- определение потенциальных друзей на основе текущих взаимоотношений;
- определение кратчайшего маршрута между двумя компьютерами в сети;
- вычисление ранга страницы для веб-страниц.

> [AZURE.WARNING] Компоненты, предоставляемые вместе с кластером HDInsight, поддерживаются в полном объеме. Техническая поддержка Майкрософт поможет вам выявить и решить проблемы, связанные с этими компонентами.
>
> Настраиваемые компоненты, такие как Giraph, получают ограниченную коммерчески оправданную поддержку, способствующую дальнейшей диагностике проблемы. В результате проблема может быть устранена, либо вас могут попросить воспользоваться доступными каналами по технологиям с открытым исходным кодом, чтобы связаться с экспертами в данной области. Можно использовать ряд сайтов сообществ, например [форум MSDN по HDInsight](https://social.msdn.microsoft.com/Forums/azure/ru-RU/home?forum=hdinsight) и [http://stackoverflow.com](http://stackoverflow.com). Кроме того, для проектов Apache есть соответствующие сайты проектов по адресу [http://apache.org](http://apache.org), например для [Hadoop](http://hadoop.apache.org/).

##Что делает сценарий

Сценарий выполняет следующие действия:

* Устанавливает Giraph в `/usr/hdp/current/giraph`.
* Копирует файл `giraph-examples.jar` в хранилище по умолчанию (WASB) в кластере: `/example/jars/giraph-examples.jar`.

## <a name="install"></a>Установка Giraph с помощью действий сценария

Пример сценария для установки Giraph в кластер HDInsight доступен по следующему адресу.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Этот раздел содержит инструкции по использованию примера сценария при создании кластера с помощью портала Azure.

> [AZURE.NOTE] Для выполнения действий этого сценария также можно использовать Azure PowerShell, Azure CLI, пакет SDK .NET для HDInsight или шаблоны Azure Resource Manager. Действия сценария также можно применять к уже работающим кластерам. Дополнительные сведения см. в статье [Настройка кластеров HDInsight с помощью действий сценария](hdinsight-hadoop-customize-cluster-linux.md).

1. Приступите к созданию кластера с помощью действий, описанных в разделе [Создание кластеров HDInsight под управлением Linux](hdinsight-provision-linux-clusters.md#portal), но не завершайте создание.

2. В колонке **Необязательная конфигурация** выберите **Действия сценария** и введите следующие сведения.

	* __ИМЯ__: введите понятное имя для действия сценария.
	* __УНИВЕРСАЛЬНЫЙ КОД РЕСУРСА (URI) СКРИПТА__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh.
	* __ГОЛОВНОЙ__: установите флажок.
	* __РАБОЧАЯ РОЛЬ__: установите флажок.
	* __ZOOKEEPER__: установите этот флажок для установки на узле Zookeeper.
	* __ПАРАМЕТРЫ__: оставьте это поле пустым.

3. В нижней части раздела **Действия сценария** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, нажмите кнопку **Выбрать** в нижней части колонки **Необязательная конфигурация**, чтобы сохранить дополнительные настройки.

4. Продолжите создание кластера, как описано в разделе [Создание кластеров HDInsight под управлением Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Как использовать Giraph в HDInsight

После завершения создания кластера выполните следующие действия, чтобы запустить пример SimpleShortestPathsComputation (входит в Giraph). Он демонстрирует простую реализацию <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> для поиска кратчайшего пути между объектами в графе.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Создайте новый файл __tiny\_graph.txt__, используя эту команду:

		nano tiny_graph.txt

	Используйте следующее в качестве содержимого этого файла:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Эти данные описывают взаимоотношения между объектами в направленном графе с использованием формата [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Каждая строка представляет взаимоотношение между объектом **source\\_id** и одним или несколькими объектами **dest\\_id**. Значение **edge\\_value** (или вес) можно представить себе как силу или расстояние подключения между **source\_id** и **dest\\_id**.

	В визуальной форме и с использованием значения (или веса) в качестве расстояния между объектами указанные выше данные могут выглядеть следующим образом.

	![tiny\_graph.txt начерчен в виде кругов с линиями различной длины между](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Чтобы сохранить файл, нажмите __CTRL+X__, затем __Y__ и __ВВОД__.

3. Чтобы сохранить данные в основном хранилище в кластере HDInsight, используйте следующую команду:

		hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt

4. Запустите пример SimpleShortestPathsComputation, используя следующую команду:

		 yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

	В следующей таблице описаны параметры, используемые в этой команде.

	| Параметр | Действие |
	| --------- | ------------ |
	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | JAR-файл, содержащий примеры. |
	| `org.apache.giraph.GiraphRunner` | Класс, используемый для запуска примеров. |
	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | Пример, который будет выполнен. В данном случае будет вычислен кратчайший путь между ИД 1 и всеми другими ИД в графе. |
	| `-ca mapred.job.tracker=headnodehost:9010` | Головной узел кластера. |
	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Формат входных данных. |
	| `-vip /example/data/tiny_graph.txt` | Файл входных данных. |
	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Формат выходных данных. В данном случае — ИД и значение в виде обычного текста. |
	| `-op /example/output/shortestpaths` | Расположение выходных данных. |
	| `-w 2` | Количество используемых рабочих ролей. В данном случае — две роли. |

	Дополнительные сведения об этих и других параметрах, которые используются в примерах Giraph, см. в [кратком руководстве по Giraph](http://giraph.apache.org/quick_start.html).

5. После завершения задания результаты будут сохранены в каталоге __wasbs:///example/out/shotestpaths__. Созданные файлы будут начинаться с __part-m-\_\_ и заканчиваться числом, указывающим номер файла (первый, второй и т. д.). Для просмотра выходных данных используйте следующую команду:

		hdfs dfs -text /example/output/shortestpaths/*

	Результат должен выглядеть аналогично следующему:

		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	Пример SimpleShortestPathComputation жестко запрограммирован для запуска с объекта ID 1 и поиска кратчайшего пути к другим объектам. Таким образом, выходные данные должны считываться как `destination_id distance`, где расстояние представляет собой значение (или вес) переходов на пути между объектом ID 1 и целевым объектом ID.

	При визуализации можно проверить результаты, проходя кратчайшие пути между ID 1 и всеми другими объектами. Обратите внимание, что кратчайший путь между ID 1 и ID 4 — это 5. Это общее расстояние между объектами <span style="color:orange">ID 1 и ID 3</span> и между объектами <span style="color:red">ID 3 и ID 4</span>.

	![Представление объектов в виде кругов с кратчайшими путями между ними](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## Дальнейшие действия

- [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Hue — это веб-интерфейс, который позволяет легко создавать, запускать и сохранять задания Pig и Hive, а также просматривать содержимое хранилища по умолчанию для вашего кластера HDInsight.

- [Установка R в кластерах HDInsight](hdinsight-hadoop-r-scripts-linux.md). Инструкции по настройке кластера для установки и использования R в кластерах HDInsight Hadoop. R — это открытый язык программирования и свободная программная среда для статистических вычислений. Он предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности.

- [Установка Solr в кластерах HDInsight](hdinsight-hadoop-solr-install-linux.md). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.

<!---HONumber=AcomDC_0914_2016-->