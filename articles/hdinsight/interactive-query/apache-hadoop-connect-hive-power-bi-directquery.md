---
title: "Визуализация данных Hive интерактивного запроса с помощью Power BI в Azure HDInsight | Документы Microsoft"
description: "Сведения об использовании Microsoft Power BI для визуализации данных Hive интерактивных запросов, обрабатываемых Azure HDInsight."
keywords: "hdinsight hadoop, hive, интерактивный запрос интерактивного hive, LLAP, directquery"
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
ms.date: 12/19/2017
ms.author: jgao
ms.openlocfilehash: 290e600b7be4a6f9fb57afa50bb771e42e6a0624
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Визуализация данных интерактивного запроса Hive с помощью прямых запросов в Azure HDInsight Microsoft Power BI

Дополнительные сведения о подключение Microsoft Power BI к кластерам интерактивных запросов Azure HDInsight и визуализации данных Hive, с помощью прямых запросов. В рамках этого руководства вы загрузите данные из таблицы Hive с именем hivesampletable в Power BI. Эта таблица содержит некоторые данные об использовании мобильного телефона. Затем вы отобразите эти данные на карте мира:

![Отчет карты HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Способ подключения к Hive, с использованием ODBC см. в разделе [Hive визуализация данных с помощью Microsoft Power BI с использованием ODBC в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 

## <a name="prerequisites"></a>Технические условия
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер HDInsight.** Кластер может представлять кластер HDInsight с Hive или кластере выпущенном интерактивных запросов. Сведения о создании кластеров см. в [этом разделе](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Копию этой программы можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Загрузка данных из HDInsight

Таблица Hive hivesampletable поставляется с кластерами HDInsight.

1. Войдите в Power BI Desktop.
2. Перейдите на вкладку **Главная**, на ленте **Внешние данные** щелкните **Получить данные**, а затем выберите **Дополнительно**.

    ![Открытие данных HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Из **получить данные** введите **hdinsight** в поле поиска. Если вы не видите **HDInsight интерактивных запросов (бета-версия)**, необходимо обновить до последней версии Power BI Desktop.
4. Нажмите кнопку **HDInsight интерактивных запросов (бета-версия)**, а затем нажмите кнопку **Connect**.
5. Нажмите кнопку **Продолжить** закрыть **предварительной версии соединителя** диалоговое окно с предупреждением.
6. Из **HDInsight интерактивных запросов**выберите или введите следующие сведения:

    - **Сервер**: Введите имя кластера интерактивных запросов, например *myiqcluster.azurehdinsight.net*.
    - **База данных**: для этого учебника введите **по умолчанию**.
    - **Режим подключения к данным**: для этого учебника выберите **DirectQuery**.

    ![HDInsight интерактивных запросов power bi directquery подключения](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Последовательно выберите **ОК**.
8. Введите учетные данные пользователя HTTP и нажмите кнопку **ОК**.  Имя пользователя по умолчанию является **администратора**
9. В левой области выберите **hivesampletale**, а затем нажмите кнопку **нагрузки**.

    ![HDInsight интерактивных запросов power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Визуализируйте данные

Продолжите из последней процедуры.

1. В области "Визуализации" выберите **Карта**.  Это значок глобуса.

    ![Настройка отчета HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. В области "Поля" выберите **страна** и **devicemake**. На карте отобразятся данные.
3. Разверните карту.

## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью Power BI.  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Визуализация данных Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./../hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Hadoop с помощью Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./../hdinsight-upload-data.md)
