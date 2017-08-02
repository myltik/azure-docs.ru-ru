---
title: "Пошаговые руководства по процессу обработки и анализа данных | Документация Майкрософт"
description: "В пошаговых руководствах показано, как объединить облачные и локальные средства и службы в единый рабочий процесс или конвейер, чтобы создать интеллектуальное приложение."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 53fbfec76b7370ca972e4d637705f5f5a5cb27c5
ms.contentlocale: ru-ru
ms.lasthandoff: 07/11/2017


---
# <a name="team-data-science-process-walkthroughs"></a>Пошаговые руководства по процессу обработки и анализа данных
Представленные здесь **пошаговые руководства** демонстрируют этапы процесса обработки и анализа данных группы для **конкретных сценариев**. В них показано, как объединить облачные и локальные средства и службы в единый рабочий процесс или конвейер, чтобы создать **интеллектуальное приложение**. Пошаговые руководства сгруппированы по используемым **платформам**: Spark, HDInsight (Hadoop), Azure Data Lake и SQL Server.


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark с использованием PySpark и Scala

### <a name="data-science-using-python-with-spark-on-azure"></a>Обработка и анализ данных с использованием Python и Spark в Azure
В пошаговом руководстве [Общие сведения об обработке и анализе данных с помощью платформы Spark в Azure HDInsight](machine-learning-data-science-spark-overview.md) показано использование процесса обработки и анализа данных группы в сценарии, где [кластер Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) используется для хранения, изучения и реконструирования характеристик данных из общедоступного набора данных о поездках и тарифах в такси Нью-Йорка. 

### <a name="data-science-using-scala-with-spark-on-azure"></a>Обработка и анализ данных с использованием Scala и Spark в Azure
В пошаговом руководстве [Обработка и анализ данных с использованием Scala и Spark в Azure](machine-learning-data-science-process-scala-walkthrough.md) показано, как с помощью Scala выполнять контролируемые задачи машинного обучения, используя библиотеку машинного обучения Spark (MLlib) и пакеты SparkML в кластере Azure HDInsight Spark. Здесь подробно расписаны задачи, которые образуют [процесс обработки и анализа данных](http://aka.ms/datascienceprocess): прием и исследование данных, визуализация, проектирование признаков, моделирование и использование моделей. В моделях используются логистическая и линейная регрессия, случайные леса и градиентный бустинг деревьев.


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

### <a name="use-hdinsight-hadoop-clusters"></a>Использование кластеров HDInsight Hadoop
В пошаговом руководстве [Процесс аналитики Кортаны в действии: использование кластеров HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md) мы используем [кластер Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) для хранения, изучения и определения характеристик данных из общедоступного набора данных поездок и платежей такси Нью-Йорка.

### <a name="use-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>Использование кластеров Azure HDInsight Hadoop для набора данных объемом 1 ТБ
В пошаговом руководстве [Процесс обработки и анализа данных группы на практике: использование кластера Azure HDInsight Hadoop с набором данных объемом 1 ТБ](machine-learning-data-science-process-hive-criteo-walkthrough.md) представлен сценарий, в котором [кластер Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) используется для хранения, изучения, реконструирования характеристик и сокращения выборки данных из общедоступного набора данных [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/).


## <a name="azure-data-lake"></a>Озеро данных Azure

### <a name="use-azure-data-lake-store-and-analytics"></a>Использование Azure Data Lake Store и Azure Data Lake Analytics
В статье [Полное пошаговое руководство по масштабируемому анализу данных в Azure Data Lake](machine-learning-data-science-process-data-lake-walkthrough.md) на примере набора данных такси Нью-Йорка показано, как использовать Azure Data Lake для выполнения задач по исследованию и двоичной классификации данных, чтобы спрогнозировать вероятность уплаты чаевых клиентом. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server и хранилище данных SQL 

### <a name="use-sql-data-warehouse"></a>Использование хранилища данных SQL
В пошаговом руководстве [Процесс Cortana Analytics в действии: использование хранилища данных SQL](machine-learning-data-science-process-sqldw-walkthrough.md) показано создание и развертывание модели машинного обучения с использованием хранилища данных SQL для общедоступного набора данных поездок и платежей такси Нью-Йорка.

### <a name="use-sql-server"></a>Использование сервера SQL
В пошаговом руководстве [Процесс аналитики Кортаны в действии: использование SQL Server](machine-learning-data-science-process-sql-walkthrough.md) показано создание и развертывание модели машинного обучения с использованием сервера SQL и общедоступного набора данных поездок и платежей такси Нью-Йорка.

### <a name="use-r-with-sql-server-r-services"></a>Использование языка R с службами R SQL сервера
Статья [Data Science End-to-End Walkthrough](https://msdn.microsoft.com/library/mt612857.aspx) (Комплексное пошаговое руководство по обработке и анализу данных) позволяет специалистам по обработке и анализу данных использовать сочетание кода R, данных SQL Server и настраиваемых функций SQL для создания и развертывания модели R на сервере SQL Server.

### <a name="use-t-sql-with-sql-server-r-services"></a>Использование T-SQL с службами R SQL сервера
В пошаговом руководстве [Дополнительные аналитические функции в базе данных для разработчиков SQL](https://msdn.microsoft.com/library/mt683480.aspx), предназначенном для программистов SQL, описан практический опыт создания решений расширенной аналитики с помощью Transact-SQL и служб R сервера SQL Server для ввода в эксплуатацию решения R.


### <a name="use-t-sql-with-sql-server-python-services"></a>Использование T-SQL со службами Python для SQL Server
В пошаговом руководстве [In-Database Python Analytics for SQL Developers](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) (Аналитические функции Python в базе данных для разработчиков SQL), предназначенном для программистов SQL, описан практический опыт создания решения машинного обучения в SQL Server. Демонстрируется, как внедрить Python в приложение, добавив код Python в хранимые процедуры.

## <a name="whats-next"></a>Что дальше?
Обзор разделов с пошаговыми руководствами по задачам, которые входят в процесс обработки и анализа данных в Azure, см. на странице [Процесс обработки и анализа данных группы](http://aka.ms/datascienceprocess). 


