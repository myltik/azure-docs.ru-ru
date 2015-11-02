<properties
	pageTitle="Просмотр данных в таблицах Hive с помощью запросов Hive | Microsoft Azure"
	description="Просмотр данных в таблицах Hive с помощью запросов Hive."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="hangzh;bradsev" />

# Просмотр данных в таблицах Hive с помощью запросов Hive 

Это **меню** содержит ссылки на разделы, описывающие использование средств для просмотра данных из различных сред хранения. Эта задача является одним из этапов процесса аналитики Кортаны (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## Введение

В этом документе представлено несколько примеров сценариев Hive, которые используются для просмотра данных в таблицах Hive.

## Предварительные требования
В этой статье предполагается, что вы:

* Создали учетную запись хранения Azure. Инструкции можно найти в статье [Создание учетной записи хранения в Azure](../hdinsight-get-started.md#storage).
* Подготовили настраиваемый кластер Hadoop с помощью службы HDInsight. Инструкции см. в статье [Настройка кластеров Azure HDInsight Hadoop для расширенного процесса обработки аналитических данных](machine-learning-data-science-customize-hadoop-cluster.md).
* Отправили данные в таблицы Hive, которые находятся в кластерах Azure HDInsight Hadoop. Если данные не загружены, необходимо предварительно загрузить их в таблицы Hive, воспользовавшись инструкциями из раздела [Создание данных и их загрузка в таблицы Hive](machine-learning-data-science-move-hive-tables.md).
* Включили удаленный доступ к кластеру. Инструкции можно найти в разделе [Доступ к головному узлу в кластере Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Инструкции по отправке запросов Hive см. в разделе [Отправка запросов Hive](machine-learning-data-science-move-hive-tables.md#submit).

## Пример сценариев запроса Hive для просмотра данных

1. Получение количества наблюдений на раздел `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Получение количества наблюдений в день `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Получение уровней в столбце категорий `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Получение числа уровней в сочетании двух столбцов категорий `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Получение распределения для числовых столбцов `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Извлечение записей из двух объединенных таблиц

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## Дополнительные сценарии запросов для сценариев данных о поездках такси

Примеры запросов, использующихся для сценариев наподобие [Данные о поездках такси по Нью-Йорку](http://chriswhong.com/open-data/foil_nyc_taxi/), также приведены в [репозитории Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Для этих запросов уже задана схема данных, и они готовы к отправке и запуску.

 

<!---HONumber=Oct15_HO4-->