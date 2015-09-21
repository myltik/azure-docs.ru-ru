<properties
	pageTitle="Использование R в HDInsight для настройки кластеров | Microsoft Azure"
	description="Узнайте, как установить и использовать R для настройки кластеров Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/20/2015"
	ms.author="larryfr"/>

# Установка и использование R на кластерах HDInsight Hadoop

R можно установить в любой тип кластера Hadoop в HDInsight с помощью настройки кластера **Действие сценария**. Благодаря R специалисты по обработке и анализу данных и аналитики имеют возможность задействовать мощную платформу программирования MapReduce и YARN для обработки больших объемов данных в кластерах Hadoop, развернутых в HDInsight.

> [AZURE.NOTE]Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Сведения об использовании R с кластером под управлением Windows см. в статье [Установка и использование R в кластерах HDInsight Hadoop (Windows)](hdinsight-hadoop-r-scripts.md).

## Что такое R

<a href="http://www.r-project.org/" target="_blank">Проект R для статистических вычислений</a> — это язык программирования с открытым исходным кодом и программная среда для статистических вычислений. R предоставляет сотни встраиваемых статистических функций и собственный язык программирования, который сочетает аспекты функционального и объектно-ориентированного программирования. Этот проект также обеспечивает обширные графические возможности. Большинство профессиональных статистиков и ученых, работающих в целом ряде областей, отдает предпочтение программной среде R.

В службе HDInsight скрипты R могут выполняться на кластерах Hadoop, которые во время создания были настроены с помощью параметра действия скриптов для установки среды R. R совместим с хранилищем больших двоичных объектов Azure (WASB). Это дает возможность обрабатывать находящиеся там данные, используя R в HDInsight.

## Что делает сценарий

Действие сценария, используемое для установки R в кластер HDInsight, включает установку следующих пакетов Ubuntu, которые обеспечивают базовую установку R:

* [r-base](http://packages.ubuntu.com/precise/r-base): базовый пакет R GNU
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): дополнительные пакеты R GNU

Также устанавливаются следующие пакеты RHadoop, которые обеспечивают интеграцию с MapReduce и HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): позволяет разработчикам R использовать функцию MapReduce Hadoop
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): позволяет разработчикам R использовать Hadoop HDFS (WASB для HDInsight)

Также устанавливаются следующие пакеты R:

| пакет R | Что он содержит |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Низкоуровневый интерфейс R для Java |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | Интеграция R и C++ |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Сериализация и десериализация объектов R в JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Функции побитовых операций над векторами целых чисел. |
| [сводка] Создание криптографической сводки хэша для объектов R | Создание криптографической сводки хэша для объектов R |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) | Каррирование, композиция и другие функции высшего порядка |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Гибкая реструктуризация и статистическая обработка данных. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Простые согласованные оболочки для типичных операций над строками. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Инструменты для разбиения, применения и объединения данных |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Инструменты для перемещения статистики окна, GIF, Base64, ROC AUC и т. д. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Функции приближенного сопоставления строк и расстояния строк. |

## Установка R с помощью действий сценария

Для установки R в кластере HDInsight используется действие сценария [https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh). Этот раздел содержит указания по использованию сценария при подготовке кластера с помощью портала Azure.

> [AZURE.NOTE]Для создания кластера с помощью этого сценария также можно использовать Azure PowerShell или пакет SDK HDInsight для .NET. Дополнительную информацию об использовании этих методов см. в статье [Настройка кластеров HDInsight с помощью действий сценария](hdinsight-hadoop-customize-cluster-linux.md).

1. Начните подготовку кластера с помощью действий, описанных в разделе [Подготовка кластеров HDInsight под управлением Linux](hdinsight-provision-linux-clusters.md#portal), но не завершайте ее.

2. В колонке **Дополнительная настройка** выберите **Действия сценария** и введите следующие сведения:

	* __ИМЯ__: введите понятное имя для действия сценария.
	* __URI-АДРЕС СЦЕНАРИЯ__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
	* __ЗАГОЛОВОК__: установите флажок.
	* __РАБОЧАЯ РОЛЬ__: установите флажок.
	* __ZOOKEEPER__: установите этот флажок для установки на узле Zookeeper.
	* __ПАРАМЕТРЫ__: оставьте это поле пустым.

3. В нижней части раздела **Действия сценария** нажмите кнопку **Выбрать**, чтобы сохранить конфигурацию. Наконец, нажмите кнопку **Выбрать** в нижней части колонки **Дополнительная настройка**, чтобы сохранить дополнительные настройки.

4. Продолжите подготовку кластера, как описано в разделе [Подготовка кластеров HDInsight под управлением Linux](hdinsight-provision-linux-clusters.md#portal).

## Запуск сценариев R

После завершения подготовки кластера выполните следующие действия для выполнения операции MapReduce в кластере с помощью R.

1. Подключитесь к кластеру HDInsight с помощью протокола SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Дополнительные сведения об использовании SSH с HDInsight см. в следующих статьях:

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. В командной строке `username@headnode1:~$` введите следующую команду для запуска интерактивного сеанса R:

		R

3. Введите следующую программу R: Она формирует числа от 1 до 100 и умножает их на 2.

		library(rmr2)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))


	В первой строке вызывается библиотека RHadoop rmr2, которая используется для операций MapReduce.

	Во второй строке создаются значения 1–100, которые затем сохраняются в файловой системе Hadoop с помощью `to.dfs`.

	В третьей строке с помощью функций, предоставляемых rmr2, создается процесс MapReduce, который начинает обработку. После начала обработки на экране появятся несколько строк.

4. Затем используйте следующую команду для просмотра временного пути, по которому были сохранены выходные данные MapReduce:

		print(calc())

	Он должен быть похож на `/tmp/file5f615d870ad2`. Чтобы просмотреть сами выходные данные, используйте следующую команду:

		print(from.dfs(calc))

	Выходные данные должны выглядеть так:

		[1,]  1 2
		[2,]  2 4
		.
		.
		.
		[98,]  98 196
		[99,]  99 198
		[100,] 100 200

5. Для выхода из R введите следующее:

		q()


## Дальнейшие действия

- [Установка и использование Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Hue — это веб-интерфейс, который позволяет легко создавать, запускать и сохранять задания Pig и Hive, а также просматривать содержимое хранилища по умолчанию для вашего кластера HDInsight.

- Статья [Установка и использование Spark в кластерах HDInsight][hdinsight-install-spark] содержит указания по применению настройки кластера для установки и использования Spark в кластерах HDInsight Hadoop. Spark — это платформа параллельной обработки с открытым исходным кодом, которая поддерживает обработку в памяти, чтобы повысить производительность приложений для анализа больших объемов данных.

- [Установка Giraph в кластерах HDInsight](../hdinsight-hadoop-giraph-install). Используйте настройки кластера для установки Giraph в кластерах HDInsight Hadoop. Giraph позволяет выполнять обработку графов с использованием Hadoop и может использоваться с Azure HDInsight.

- [Установка Solr в кластерах HDInsight](../hdinsight-hadoop-solr-install). Используйте настройки кластера для установки Solr в кластерах HDInsight Hadoop. Solr позволяет вести расширенный поиск по хранимым данным.

- [Установка Hue в кластерах HDInsight](hdinsight-hadoop-hue-linux.md). Установить Hue в кластерах HDInsight Hadoop можно при помощи настройки кластера. Hue — это набор веб-приложений, используемых для взаимодействия с кластером Hadoop.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=Sept15_HO2-->