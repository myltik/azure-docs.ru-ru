---
title: Визуализация больших данных с помощью Power BI в Azure HDInsight | Документация Майкрософт
description: Сведения о визуализации данных Hive, обрабатываемых Azure HDInsight, с помощью Microsoft Power BI.
keywords: hdinsight,hadoop,hive,интерактивный запрос,interactive hive,LLAP,odbc
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
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 6f4efdebd6fc2833747b4cb72496447e0be03a58
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200002"
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Визуализация данных Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight

В этой статье приведены сведения о подключении Microsoft Power BI к Azure HDInsight с использованием ODBC и визуализации данных Hive. 

>[!IMPORTANT]
> Можно использовать драйвер Hive ODBC для импорта с помощью универсального соединителя ODBC в Power BI Desktop. Но этот драйвер не рекомендуется для рабочих нагрузок бизнес-аналитик, с учетом того что ядро запросов Hive не является интерактивным. Для лучшей производительности используйте [соединитель интерактивных запросов HDInsight ](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) и [соединитель HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect).

В рамках этого руководства вы загрузите данные из таблицы Hive с именем hivesampletable в Power BI. Эта таблица содержит некоторые данные об использовании мобильного телефона. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Эти сведения также относятся к новому типу кластера [интерактивных запросов](../interactive-query/apache-interactive-query-get-started.md). Сведения о подключении к HDInsight Interactive Query при помощи прямого запроса см. в статье [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive Interactive Query при помощи Microsoft Power BI с использованием прямого запроса в Azure HDInsight).



## <a name="prerequisites"></a>предварительным требованиям
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер HDInsight.** Это может быть кластер HDInsight с Hive или новый кластер интерактивных запросов. Сведения о создании кластеров см. в [этом разделе](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Создание источника данных Hive ODBC

Дополнительные сведения см. в разделе [Создание источника данных Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

Таблица Hive hivesampletable поставляется с кластерами HDInsight.

1. Войдите в Power BI Desktop.
2. Перейдите на вкладку **Главная**, на ленте **Внешние данные** щелкните **Получить данные**, а затем выберите **Дополнительно**.

    ![Открытие данных HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. В области **Получение данных** щелкните **Прочее** слева, **ODBC** справа и **Подключиться** вверху.
4. В области **Из ODBC** выберите имя источника данных, который вы создали в предыдущем разделе, и нажмите кнопку **ОК**.
5. В области **Навигатор** разверните **ODBC -> HIVE -> по умолчанию**, выберите **hivesampletable** и нажмите кнопку **Загрузить**.

## <a name="visualize-data"></a>Визуализируйте данные

Продолжите из последней процедуры.

1. В области "Визуализации" выберите **Карта**.  Это значок глобуса.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. В области "Поля" выберите **страна** и **devicemake**. На карте отобразятся данные.
3. Разверните карту.

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./../hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Hadoop с помощью Power Query](apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
