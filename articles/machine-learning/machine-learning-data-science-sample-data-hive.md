<properties
	pageTitle="Выборка данных в таблицах Azure HDInsight Hive | Microsoft Azure"
	description="Уменьшение выборки данных в таблицах Hive Azure HDInsight (Hadopop)"
	services="machine-learning,hdinsight"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="hangzh;bradsev" />

# Выборка данных в таблицах Azure HDInsight Hive

В этой статье мы опишем, как уменьшить размер выборки данных, хранящихся в таблицах Azure HDInsight Hive, с помощью запросов Hive. Мы охватим три распространенных метода выборки:

* Универсальная случайная выборка
* Случайная выборка по группам
* Стратифицированная выборка

**Для чего нужна выборка данных?** Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Это способствует пониманию данных, их исследованию и проектированию характеристик. Роль этой операции в процессе обработки и анализа данных группы состоит в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

**Меню** ниже содержит ссылки на разделы, описывающие выборку данных из различных сред хранения.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## Отправка запросов Hive
Запросы Hive можно отправлять из окна командной строки Hadoop на головном узле кластера Hadoop. Для этого войдите в головной узел кластера Hadoop, откройте окно командной строки Hadoop и отправьте оттуда запросы Hive. Инструкции по отправке запросов Hive в консоли командной строки Hadoop см. в статье [Отправка запросов Hive](machine-learning-data-science-process-hive-tables.md#submit).

## <a name="uniform"></a> Универсальная случайная выборка
Универсальная случайная выборка означает, что каждая строка в наборе данных может попасть в выборку с одинаковой вероятностью. Это можно реализовать, добавив дополнительное поле rand() в набор данных во внутреннем запросе "select", а также во внешнем запросе "select" с условной зависимостью от этого случайного поля.

Вот пример запроса:

	SET sampleRate=<sample rate, 0-1>;
	select
		field1, field2, …, fieldN
	from
		(
		select
			field1, field2, …, fieldN, rand() as samplekey
		from <hive table name>
		)a
	where samplekey<='${hiveconf:sampleRate}'

Здесь `<sample rate, 0-1>` указывает долю записей, которые пользователи желают включить в выборку.

## <a name="group"></a> Случайная выборка по группам

При выборке данных о категориях может быть необходимо либо включить, либо исключить все экземпляры определенного значения переменной категории. Это называется "выборкой по группам". Например, если имеется переменная категории "Штат" со значениями NY, MA, CA, NJ, PA и т. д., необходимо, чтобы записи одного и того же штата всегда были вместе, независимо от их включения в выборку.

Вот пример запроса с выборкой по группам:

	SET sampleRate=<sample rate, 0-1>;
    select
		b.field1, b.field2, …, b.catfield, …, b.fieldN
	from
		(
		select
			field1, field2, …, catfield, …, fieldN
		from <table name>
		)b
	join
		(
		select
			catfield
		from
			(
			select
				catfield, rand() as samplekey
			from <table name>
			group by catfield
			)a
		where samplekey<='${hiveconf:sampleRate}'
		)c
	on b.catfield=c.catfield

## <a name="stratified"></a>Стратифицированная выборка

Случайная выборка является стратифицированной по отношению к переменной категории, когда выбранные данные имеют значения этой категории, содержащиеся в такой же пропорции, как и в родительской популяции, из которой была получена выборка. Используя тот же вышеупомянутый пример, предположим, что в данных имеются субпопуляции по штатам, например для NJ имеется 100 наблюдений, для NY — 60 наблюдений, для WA — 300. Если указать коэффициент стратифицированной выборки 0,5, то в полученной выборке должно быть примерно по 50, 30 и 150 наблюдений из штатов NJ, NY и WA соответственно.

Вот пример запроса:

	SET sampleRate=<sample rate, 0-1>;
    select
		field1, field2, field3, ..., fieldN, state
	from
		(
		select
			field1, field2, field3, ..., fieldN, state,
			count(*) over (partition by state) as state_cnt,
      		rank() over (partition by state order by rand()) as state_rank
      	from <table name>
		) a
	where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Сведения о дополнительных методах выборки, доступных в Hive, см. в статье [Руководство по языку: выборка](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
 

<!---HONumber=AcomDC_0921_2016-->