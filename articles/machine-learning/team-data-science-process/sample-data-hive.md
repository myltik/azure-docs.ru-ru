---
title: Выборка данных в таблицах Azure HDInsight Hive | Документация Майкрософт
description: Уменьшение выборки данных в таблицах Hive Azure HDInsight (Hadopop)
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 558f7d684453c8b5040f586820bd2a8a9ac0f9c8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838438"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Выборка данных в таблицах Azure HDInsight Hive
Из этой статье вы узнаете, как уменьшить выборку данных, хранящихся в таблицах Hive в Azure HDInsight. Для этих целей мы будем использовать запросы Hive, с помощью которых мы уменьшим размер данных для более удобного их анализа. Мы рассмотрим три распространенных метода выборки:

* Универсальная случайная выборка
* Случайная выборка по группам
* Стратифицированная выборка

**Меню** ниже содержит ссылки на разделы, в которых описана выборка данных из различных сред хранения.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Для чего нужна выборка данных?**
Если размер набора данных, который планируется проанализировать, слишком большой, обычно рекомендуется уменьшить выборку данных до размера, который останется репрезентативным и будет более управляемым. Уменьшение выборки способствует пониманию данных, их исследованию и проектированию признаков. Роль этой операции в процессе обработки и анализа данных группы состоит в том, чтобы сделать возможным быстрое прототипирование функций обработки данных и моделей машинного обучения.

Эта задача выборки является одним из этапов [процесса обработки и анализа данных группы (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Отправка запросов Hive
Запросы Hive можно отправлять из командной строки Hadoop на головном узле кластера Hadoop. Для этого войдите на головной узел кластера Hadoop и откройте командную строку Hadoop. После этого вы сможете отправлять запросы Hive. Инструкции по отправке запросов Hive из командной строки Hadoop см. в разделе [Отправка запросов Hive](move-hive-tables.md#submit).

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
При выборке данных о категориях может быть необходимо либо включить, либо исключить все экземпляры определенного значения переменной категории. Такого рода выборка называется выборкой по группам. Например, у вас есть переменная категории *Штат* со значениями NY, MA, CA, NJ и PA и вы хотите, чтобы записи для одного и того же штата были вместе, независимо от их включения в выборку.

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
Случайная выборка является стратифицированной по отношению к переменной категории, когда выбранные данные имеют значения этой категории, содержащиеся в такой же пропорции, как и в родительской популяции. Используя тот же вышеупомянутый пример, предположим, что в данных имеются следующие наблюдения по штатам: для NJ есть 100 наблюдений, для NY — 60, для WA — 300. Если указать коэффициент стратифицированной выборки 0,5, то в полученной выборке должно быть примерно по 50, 30 и 150 наблюдений из штатов NJ, NY и WA соответственно.

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


Сведения о дополнительных методах выборки, доступных в Hive, см. на странице [руководства по выборке](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

