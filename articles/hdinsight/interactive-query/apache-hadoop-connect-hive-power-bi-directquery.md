---
title: Визуализация данных Hive из кластера Interactive Query с помощью Power BI в Azure HDInsight | Документация Майкрософт
description: Сведения о визуализации данных Hive из кластера Interactive Query, обрабатываемых Azure HDInsight, с помощью Microsoft Power BI.
keywords: hdinsight,hadoop,hive,интерактивный запрос,interactive hive,LLAP,directquery
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
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: b8da1f17b9e477caf9031cf94ee14f3a181e247e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407968"
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Визуализация данных Hive из кластера Interactive Query с Microsoft Power BI с использованием прямого запроса в Azure HDInsight

В этой статье приведены сведения о подключении Microsoft Power BI к кластерам Interactive Query Azure HDInsight и визуализации данных Hive с использованием прямого запроса. В рамках этого руководства вы загрузите данные из таблицы Hive с именем hivesampletable в Power BI. Эта таблица содержит некоторые данные об использовании мобильного телефона. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Можно использовать [драйвер Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) для импорта с помощью универсального соединителя ODBC в Power BI Desktop. Но этот драйвер не рекомендуется для рабочих нагрузок бизнес-аналитик, с учетом того что ядро запросов Hive не является интерактивным. Для лучшей производительности используйте [соединитель интерактивных запросов HDInsight ](./apache-hadoop-connect-hive-power-bi-directquery.md) и [соединитель HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect).

## <a name="prerequisites"></a>предварительным требованиям
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер HDInsight.** Это может быть кластер HDInsight с Hive или новый кластер Interactive Query. Сведения о создании кластеров см. в [этом разделе](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

Таблица Hive hivesampletable поставляется с кластерами HDInsight.

1. Войдите в Power BI Desktop.
2. Перейдите на вкладку **Главная**, на ленте **Внешние данные** щелкните **Получить данные**, а затем выберите **Дополнительно**.

    ![Открытие данных HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. На панели **Получение данных** введите **hdinsight** в поле поиска. Если **HDInsight Interactive Query (Beta)** (HDInsight Interactive Query (бета-версия)) не отображается, необходимо обновить Power BI Desktop до последней версии.
4. Щелкните **HDInsight Interactive Query (Beta)** (HDInsight Interactive Query (бета-версия)), а затем нажмите кнопку **Подключиться**.
5. Нажмите кнопку **Продолжить**, чтобы закрыть диалоговое окно с предупреждением **Предварительная версия соединителя**.
6. В разделе **HDInsight Interactive Query** выберите или введите следующие сведения:

    - **Сервер**: введите имя кластера Interactive Query, например *myiqcluster.azurehdinsight.net*.
    - **База данных**: для работы с этим руководством введите **по умолчанию**.
    - **Режим подключения к данным**: для работы с этим руководством выберите **DirectQuery**.

    ![HDInsight interactive query, power bi, подключение с помощью прямого запроса](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Последовательно выберите **ОК**.
8. Введите учетные данные пользователя HTTP и нажмите кнопку **ОК**.  Имя пользователя по умолчанию — **admin**.
9. В левой области выберите **hivesampletale**, а затем нажмите кнопку **Загрузить**.

    ![HDInsight interactive query power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Визуализируйте данные

Продолжите из последней процедуры.

1. В области "Визуализации" выберите **Карта**.  Это значок глобуса.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. В области "Поля" выберите **страна** и **devicemake**. На карте отобразятся данные.
3. Разверните карту.

## <a name="next-steps"></a>Дополнительная информация
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Visualize Hive data with Microsoft Power BI using ODBC in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md) (Визуализация данных Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight). 
* [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./../hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Hadoop с помощью Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
