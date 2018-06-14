---
title: Использование представлений Ambari для работы с Hive в HDInsight (Hadoop) — Azure | Документы Майкрософт
description: Узнайте, как использовать представление Hive в веб-браузере для отправки запросов Hive. Представление Hive — это компонент веб-интерфейса Ambari, поставляемого с кластером HDInsight на основе Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 6c07e9a45cbfbc5e6ed6787277dcfa1bf4cf4b2b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201498"
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Использование представления Hive с Hadoop в HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Узнайте, как выполнять запросы Hive с помощью представления Hive в Ambari. Представление Hive позволяет создавать, оптимизировать и выполнять запросы Hive из веб-браузера.

## <a name="prerequisites"></a>предварительным требованиям

* Hadoop в кластере HDInsight версии 3.4 или выше на платформе Linux.

  > [!IMPORTANT]
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* веб-браузера;

## <a name="run-a-hive-query"></a>Выполнение запроса Hive

1. Откройте [портал Azure](https://portal.azure.com).

2. Выберите свой кластер HDInsight, а затем в разделе **Быстрые ссылки** выберите **Просмотры Ambari**.

    ![Раздел портала с быстрыми ссылками](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    Если запрашивается проверка подлинности, используйте имя пользователя и пароль учетной записи входа в кластер (по умолчанию — `admin`), указанные при создании кластера.

3. В списке представлений выберите __Представление Hive__.

    ![Выбрано представление Hive](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Страница представления Hive выглядит следующим образом:

    ![Изображение листа запроса для представления Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. На вкладке __Запрос__ вставьте в лист следующие инструкции HiveQL:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Эти операторы выполняют следующие действия:

   * `DROP TABLE` — удаляет таблицу и файл данных, если таблица уже существует.

   * `CREATE EXTERNAL TABLE` — создает "внешнюю" таблицу в Hive.
   Внешние таблицы хранят только определение таблицы в Hive. Данные остаются в исходном расположении.

   * `ROW FORMAT` — показывает настройку форматирования данных. В данном случае поля всех журналов разделены пробелом.

   * `STORED AS TEXTFILE LOCATION` — показывает место хранения данных и их формат (текст).

   * `SELECT` — выбирает подсчет количества строк, в которых столбец t4 содержит значение [ERROR].

    > [!IMPORTANT]
    > Оставьте для параметра __База данных__ значение __по умолчанию__. В примерах в этом документе используется база данных по умолчанию, входящая в состав HDInsight.

5. Чтобы выполнить запрос, нажмите кнопку **Выполнить** под листом. Кнопка станет оранжевой, а текст изменится на **Остановить**.

6. Когда запрос будет выполнен, на вкладке **Результаты** появятся результаты этой операции. Вот пример результата запроса:

        loglevel       count
        [ERROR]        3

    Просмотреть сведения, регистрируемые в процессе выполнения задания, можно на вкладке **Журналы**.

   > [!TIP]
   > Скачайте или сохраните результаты в диалоговом окне с раскрывающимся списком **Save results** (Сохранение результатов) в верхнем левом углу раздела **Query Process Results** (Результаты обработки запроса).

### <a name="visual-explain"></a>Визуальное объяснение

Чтобы отобразить визуализацию плана запроса, выберите под листом вкладку **Visual Explain** (Визуальное пояснение).

Представление запроса **Visual Explain** (Визуальное объяснение) помогает разобраться в потоке сложных запросов. Вы можете просмотреть текстовый эквивалент этого представления, нажав кнопку **Explain** (Объяснить) в редакторе запросов.

### <a name="tez-ui"></a>Пользовательский интерфейс Tez

Чтобы отобразить пользовательский интерфейс Tez для запроса, выберите под листом вкладку **Tez**.

> [!IMPORTANT]
> Tez используется не для всех запросов. Многие запросы можно разрешить и без применения Tez. 

Если Tez использовался для разрешения запроса, отображается направленный ациклический граф (DAG) . Чтобы просмотреть DAG для ранее выполненных запросов или выполнить отладку процесса Tez, используйте [представление Tez](../hdinsight-debug-ambari-tez-view.md).

## <a name="view-job-history"></a>Просмотр журнала заданий

На вкладке __Задания__ отображается журнал запросов Hive.

![Изображение журнала заданий](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Таблицы базы данных

Вкладку __Таблицы__ можно использовать для работы с таблицами в базе данных Hive.

![Изображение вкладки "Таблицы"](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Сохраненные запросы

На вкладке **Запрос** можно при желании сохранять запросы. После сохранения запроса можно повторно использовать его из вкладки __Saved Queries__ (Сохраненные запросы).

![Изображение вкладки "Сохраненные запросы"](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]
> Запросы сохраняются в системе хранения данных кластера по умолчанию. Сохраненные запросы можно найти в следующем расположении: `/user/<username>/hive/scripts`. Они хранятся в виде обычных текстовых файлов `.hql`.
>
> Если вы удалите кластер, но сохраните хранилище, для извлечения запросов можно использовать такую служебную программу, как [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) или обозреватель хранилища Data Lake (на [портале Azure](https://portal.azure.com)).

## <a name="user-defined-functions"></a>Определяемые пользователем функции

Инфраструктуру Hive можно расширить с помощью определяемых пользователем функций (UDF). Они позволяют реализовать функции или логику, сложно моделируемые в HiveQL.

Объявлять и сохранять наборы определяемых пользователем функций можно с помощью вкладки **UDF** вверху представления Hive. Эти функции могут использоваться в **редакторе запросов**.

![Изображение вкладки "Определяемая пользователем функция"](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

После добавления в представление Hive UDF в нижней части **редактора запросов** появляется кнопка **Insert udfs** (Вставить определяемые пользователем функции). Нажав эту кнопку, вы увидите раскрывающийся список функций, определенных в представлении Hive. Выбирая определяемую пользователем функцию, вы добавляете в запрос соответствующие инструкции HiveQL.

Например, вы определили функцию со следующими свойствами:

* имя ресурса — myudfs;

* путь к ресурсу — /myudfs.jar;

* имя определяемой пользователем функции — myawesomeudf;

* имя класса определяемой пользователем функции — com.myudfs.Awesome.

Нажав кнопку **Insert udfs** (Вставить определяемые пользователем функции), вы увидите запись с именем **myudfs**, содержащую раскрывающийся список для каждой функции, определяемой для этого ресурса. В данном случае это **myawesomeudf**. Если вы выберете эту запись, в начало запроса будет добавлен следующий код:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Затем вы можете использовать эту функцию в своем запросе. Например, `SELECT myawesomeudf(name) FROM people;`.

Дополнительные сведения об использовании определяемых пользователем функций с Hive в HDInsight см. в следующих статьях:

* [Использование Python с Hive и Pig в HDInsight](python-udf-hdinsight.md)
* [Добавление пользовательских UDF Hive в HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Параметры Hive

Вы можете изменять различные настройки Hive. Например, для изменения механизма выполнения для Hive с Tez (значение по умолчанию) на MapReduce.

## <a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)
