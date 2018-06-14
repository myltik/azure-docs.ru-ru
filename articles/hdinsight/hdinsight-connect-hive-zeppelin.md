---
title: Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin | Документы Майкрософт
description: Сведения о выполнении запросов Hive с помощью Zeppelin.
keywords: hdinsight hadoop, hive, interactive query, LLAP
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: c8fe65d2eadaede1d99befbf76c4d06fab9598fc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202617"
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin 

Кластеры Interactive Query HDInsight включают записные книжки Zeppelin, которые можно использовать для выполнения интерактивных запросов Hive. В этой статье вы узнаете, как использовать Zeppelin для выполнения запросов Hive в Azure HDInsight. 

## <a name="prerequisites"></a>предварительным требованиям
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер Interactive Query HDInsight**. Чтобы создать кластер HDInsight, обратитесь к разделу [Создание кластера](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).  Выберите тип кластера Interactive Query. 

## <a name="create-a-zeppelin-note"></a>Создание заметки Zeppelin

1. Перейдите на следующий URL-адрес:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Замените **CLUSTERNAME** именем кластера.

2. Введите имя пользователя и пароль Hadoop. На странице Zeppelin можно создать новую заметку или открыть существующие заметки. В разделе HiveSample находятся примеры запросов Hive.  

    ![Заметка Zeppelin для кластера Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Щелкните **Создать новую заметку**.
4. Введите или выберите следующие значения:

    - Название заметки: введите название заметки.
    - Интерпретатор по умолчанию: выберите **JDBC**.

5. Щелкните **Создать заметку**.
6. Выполните следующий запрос Hive:

        %jdbc(hive)
        show tables

    ![Выполнение запроса заметки Zeppelin для кластера Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Инструкция **%jdbc(hive)** в первой строке означает, что записная книжка должна использовать интерпретатор JDBC Hive.

    Запрос должен вернуть одну таблицу Hive с именем *hivesampletable*.

    Ниже приведены два дополнительных запроса Hive, которые можно выполнить с таблицей hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    По сравнению с обычным Hive, результаты запроса возвращаются намного быстрее.


## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Визуализация данных Hive с помощью Microsoft Power BI в Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Hadoop с помощью Power Query](hadoop/apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./hdinsight-upload-data.md)
