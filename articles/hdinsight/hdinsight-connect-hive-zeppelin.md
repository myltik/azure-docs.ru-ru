---
title: "Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin | Документы Майкрософт"
description: "Сведения о выполнении запросов Hive с помощью Zeppelin."
keywords: hdinsight hadoop, hive, interactive query, LLAP
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 81f353e61cd696a00f79a6b31f5750d34899bbd1
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin 

Кластеры Interactive Query HDInsight включают записные книжки Zeppelin, которые можно использовать для выполнения интерактивных запросов Hive. В этой статье вы узнаете, как использовать Zeppelin для выполнения запросов Hive в Azure HDInsight. 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения действий, указанных в этой статье, вам потребуется следующее:

* **Кластер Interactive Query HDInsight**. Чтобы создать кластер HDInsight, обратитесь к разделу [Создание кластера](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).  Выберите тип кластера Interactive Query. 

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


## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Визуализация данных Hive с помощью Microsoft Power BI в Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC](./hdinsight-connect-excel-hive-odbc-driver.md).
* [Подключение Excel к Hadoop с помощью Power Query](./hdinsight-connect-excel-power-query.md).
* [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](./hdinsight-hadoop-visual-studio-tools-get-started.md).
* [Использование Visual Studio Code для Hive, LLAP и pySpark](hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./hdinsight-upload-data.md).

