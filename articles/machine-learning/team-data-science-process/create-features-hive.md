---
title: Создание признаков для данных в кластере Hadoop с помощью запросов Hive | Документация Майкрософт
description: Примеры запросов Hive, формирующие характеристики в данных, хранящихся в кластере Azure HDInsight Hadoop.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 0e46ce327bb4beffd631ef6369864a0888580c11
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836669"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Создание признаков для данных в кластере Hadoop с помощью запросов Hive
В этом документе показано, как создавать компоненты для данных, хранящихся в кластере Azure HDInsight Hadoop, используя запросы Hive. Эти запросы Hive используют внедренные пользовательские функции, сценарии для которых предоставлены.

Операции, необходимые для создания функций, могут требовать большого объема памяти. В таких случаях производительность запросов Hive становится более значимой и может быть повышена за счет настройки определенных параметров. Эти параметры обсуждаются в последнем разделе.

Кроме того, в [репозитории GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) приведены примеры запросов, использующихся для сценариев наподобие [Данные о поездках в такси по Нью-Йорку](http://chriswhong.com/open-data/foil_nyc_taxi/). Для этих запросов уже задана схема данных, и они готовы к отправке и запуску. В последнем разделе также рассматриваются параметры, настроив которые можно повысить производительность запросов Hive.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Это **меню** содержит ссылки на статьи, описывающие создание характеристик для данных в различных средах. Эта задача является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что вы:

* Создали учетную запись хранения Azure. Инструкции см. в разделе [Создание учетной записи хранения](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Подготовили настраиваемый кластер Hadoop с помощью службы HDInsight.  Инструкции см. в статье [Настройка кластеров Azure HDInsight Hadoop для процесса обработки и анализа данных группы](customize-hadoop-cluster.md).
* Отправили данные в таблицы Hive, которые находятся в кластерах Azure HDInsight Hadoop. Если данные не переданы, необходимо предварительно передать их в таблицы Hive, воспользовавшись инструкциями из раздела [Создание таблиц Hive и загрузка данных из хранилища BLOB-объектов Azure](move-hive-tables.md).
* Включили удаленный доступ к кластеру. Инструкции можно найти в разделе [Доступ к головному узлу в кластере Hadoop](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Создание признаков
В этом разделе описано несколько примеров создания характеристик с помощью запросов Hive. После создания дополнительных признаков вы можете добавить их в виде столбцов к существующей таблице или создать новую таблицу с дополнительными возможностями и первичный ключ, который затем можно объединить с исходной таблицей. Здесь представлены следующие примеры.

1. [Возможность создания функций на основе частоты](#hive-frequencyfeature)
2. [Риски категориальных переменных в двоичной классификации](#hive-riskfeature)
3. [Извлечение признаков из полей даты и времени](#hive-datefeatures)
4. [Извлечение признаков из текстовых полей](#hive-textfeatures)
5. [Вычисления расстояния между координатами GPS](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Создание признаков на основе частоты
Часто бывает полезным вычислить частоту уровней категориальной переменной или частоту сочетания уровней нескольких категориальных переменных. Вот какой скрипт вы можете использовать для вычисления этих типов частоты.

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Риски категориальных переменных в двоичной классификации
В двоичной классификации нужно преобразовать нечисловые категориальные переменные в числовые функции, тогда как используемые модели принимают только числовые функции. Чтобы выполнить это преобразование, нужно заменить каждый нечисловой уровень числовым риском. В этом разделе показаны некоторые общие запросы Hive, с помощью которых вы можете вычислить значения риска категориальной переменной.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

В этом примере переменные `smooth_param1` и `smooth_param2` задаются для сглаживания значений рисков, вычисленных на основе данных. Риски имеют диапазон от -Inf до Inf. Риск > 0 означает, что возможность того, что цель будет равной 1, превышает 0.5.

После вычисления таблицы рисков риски можно назначить таблице, объединив ее с таблицей рисков. Запрос Hive на объединение предоставлен в предыдущем разделе.

### <a name="hive-datefeatures"></a>Извлечение признаков из полей даты и времени
Язык Hive включает в себя набор функций, определяемых пользователем, для обработки полей даты и времени. По умолчанию формат даты и времени в Hive выглядит так: yyyy-MM-dd 00:00:00 (например, 1970-01-01 12:21:32). В этом разделе приведены примеры того, как из поля даты и времени извлекать день месяца и месяц, и другие примеры, в которых строка даты и времени в формате не по умолчанию преобразуется в строку в формате по умолчанию.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

В этом запросе Hive предполагается, что значение в поле *<datetime field>* указано в стандартном формате даты и времени.

Если поле даты и времени приведено не в формате по умолчанию, нужно преобразовать его в метку времени Unix, а затем преобразовать метку времени в строку даты и времени в формате по умолчанию. Когда дата и время указаны в формате по умолчанию, вы можете применять определяемые пользователями функции даты и времени для извлечения функций.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Если в этом запросе значение в поле *<datetime field>* указано в формате *03/26/2015 12:04:39*, значение поля *<pattern of the datetime field>'* должно выглядеть так: `'MM/dd/yyyy HH:mm:ss'`. Чтобы проверить шаблон поля даты и времени, можно выполнить такую команду:

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

Компонент *hivesampletable* этого запроса по умолчанию предустановлен во всех подготовленных кластерах Azure HDInsight Hadoop.

### <a name="hive-textfeatures"></a>Извлечение признаков из текстовых полей
Если таблица Hive содержит текстовое поле, содержащее строку слов, разделенных пробелами, следующий запрос извлекает длину строки и число слов в строке.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Вычисление расстояния между координатами GPS
Запрос, приведенный в этом разделе, вы можете напрямую применить к данным о поездках в такси в Нью-Йорке. Цель этого запроса — продемонстрировать, как применить внедренную математическую функцию в Hive, чтобы создать компоненты.

Поля в этом запросе — это координаты GPS, обозначающие точки посадки и высадки пассажиров. Они называются так: *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* и *dropoff\_latitude*. Ниже приведены запросы, которые рассчитывают расстояние между координатами посадки и высадки.

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Математические уравнения, с помощью которых рассчитывается расстояние между двумя координатами GPS, можно найти на сайте <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> (создатель веб-сайта — Петер Лапису (Peter Lapisu)). В его коде JavaScript функция `toRad()` имеет значение *lat_or_lon*pi/180*, преобразующее градусы в радианы. Здесь *lat_or_lon* — это широта или долгота. Так как в Hive нет функции `atan2`, но есть функция `atan`, функция `atan2` в приведенном выше запросе Hive реализована функцией `atan` с помощью определения из <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Википедии</a>.

![Создание рабочей области](./media/create-features-hive/atan2new.png)

Полный список внедренных в Hive функций, определяемых пользователями, можно найти в разделе **Built-in Functions** (Встроенные функции) на <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">вики-сайте Apache Hive</a>.  

## <a name="tuning"></a> Дополнительные разделы: настройка параметров Hive для ускорения обработки запросов
Настройки по умолчанию кластера Hive могут не подойти для запросов Hive и данных, обрабатываемых этими запросами. В этом разделе речь идет о некоторых параметрах, которые вы можете настроить, чтобы повысить производительность запросов Hive. Сначала необходимо добавлять запросы настройки параметров, а затем запросы обработки данных.

1. **Куча Java**: при работе с запросами на объединение крупных наборов данных или обработку длинных записей часто возникает ошибка, указывающая на то, что **в куче не хватает памяти**. Чтобы избежать этой ошибки, задайте нужные значения для параметров *mapreduce.map.java.opts* и *mapreduce.task.io.sort.mb*. Вот пример: 
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Этот параметр выделяет 4 ГБ памяти куче Java и, кроме того, выделяя на сортировку больший объем памяти, повышает эффективность сортировки. Мы рекомендуем заняться настройкой того, сколько памяти куда выделяется, если произошли сбои задач, связанные с размером кучи.

1. **Размер блока DFS**: этот параметр задает минимальную единицу измерения данных файловой системы. Например, если размер блока DFS меньше 128 МБ, то любые данные размером до 128 МБ хранятся в отдельном блоке. Для данных, размер которых превышает 128 МБ, выделяются дополнительные блоки. 
2. Выбор маленького размера блока может привести к тому, что в Hadoop будет задействовано много служебных данных, так как узлу имени, чтобы найти блок, соответствующий файлу, придется обрабатывать гораздо больше запросов. При работе с гигабайтами (или большими объемами) данных рекомендуем такую настройку:

        set dfs.block.size=128m;

2. **Оптимизация операций объединения в Hive**: так как операции объединения на платформе Map/Reduce обычно выполняются на этапе сокращения, в некоторых случаях эффективность этих операций можно существенно повысить, запланировав объединения на этапе сопоставления (они также называются "объединениями при сопоставлении"). Чтобы объединение в Hive выполнялось всегда по мере возможности, задайте следующую настройку:
   
       set hive.auto.convert.join=true;

3. **Определение количества модулей сопоставления в Hive**: хотя Hadoop позволяет задать число модулей сокращения, число модулей сопоставления, как правило, задать нельзя. Этим числом можно в некоторой степени управлять, задав значения переменных Hadoop *mapred.min.split.size* и *mapred.max.split.size*, так как размер каждой задачи сопоставления определяется следующим образом.
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Как правило, значение по умолчанию
    
    - *mapred.min.split.size* равно 0,
    - *mapred.max.split.size* — **Long.MAX**, а 
    - *dfs.block.size* — 64 МБ.

    Как видно из размера данных, настройка этих параметров позволяет изменять количество используемых модулей сопоставления.

4. Ниже приведены некоторые другие **дополнительные возможности** по оптимизации производительности Hive. Они позволяют указывать объем памяти, выделяемой на сопоставление и уменьшение задач. Кроме того, с их помощью вы можете изменять производительность. Не забывайте, что значение *mapreduce.reduce.memory.mb* не может превышать объем физической памяти каждого рабочего узла в кластере Hadoop.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

