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
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>Пошаговые руководства по процессу обработки и анализа данных
Представленные здесь **пошаговые руководства** демонстрируют этапы процесса обработки и анализа данных группы для **конкретных сценариев**. В них показано, как объединить облачные и локальные средства и службы в единый рабочий процесс или конвейер, чтобы создать **интеллектуальное приложение**. Руководства сгруппированы по используемой **платформе**: 

- Spark с PySpark и Scala;
- HDInsight (Hadoop);
- Azure Data Lake; 
- SQL Server;
- Хранилище данных SQL. 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark с использованием PySpark и Scala

- В пошаговом руководстве [Общие сведения об обработке и анализе данных с помощью платформы Spark в Azure HDInsight](machine-learning-data-science-spark-overview.md) показано использование процесса обработки и анализа данных группы в сценарии, где [кластер Azure HDInsight со Spark](https://azure.microsoft.com/services/hdinsight/) используется для хранения данных, их изучения и конструирования признаков. Данные взяты из общедоступного набора данных о поездках и тарифах в такси Нью-Йорка.

- В пошаговом руководстве [Обработка и анализ данных с использованием Scala и Spark в Azure](machine-learning-data-science-process-scala-walkthrough.md) показано, как с помощью Scala выполнять контролируемые задачи машинного обучения, используя библиотеку машинного обучения Spark (MLlib) и пакеты SparkML в кластере Azure HDInsight со Spark. Здесь подробно расписаны задачи, которые образуют [процесс обработки и анализа данных](http://aka.ms/datascienceprocess): прием и исследование данных, визуализация, проектирование признаков, моделирование и использование моделей. В моделях используются логистическая и линейная регрессия, случайные леса и градиентный бустинг деревьев.


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

- В пошаговом руководстве [Процесс обработки и анализа данных группы на практике: использование кластеров Azure HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md) мы используем [кластер Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) для хранения данных, их изучения и конструирования признаков. Данные взяты из общедоступного набора данных о поездках и тарифах в такси Нью-Йорка.

- В пошаговом руководстве [Процесс обработки и анализа данных группы на практике: использование кластера Azure HDInsight Hadoop с набором данных объемом 1 ТБ](machine-learning-data-science-process-hive-criteo-walkthrough.md) представлен сценарий, в котором [кластер Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) используется для хранения данных, их изучения, конструирования признаков и сокращения выборки данных из общедоступного набора данных [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/).


## <a name="azure-data-lake"></a>Озеро данных Azure

- В статье [Полное пошаговое руководство по масштабируемому анализу данных с помощью Azure Data Lake](machine-learning-data-science-process-data-lake-walkthrough.md) на примере набора данных такси Нью-Йорка показано, как использовать Azure Data Lake для выполнения задач по исследованию и двоичной классификации данных, чтобы спрогнозировать вероятность уплаты чаевых клиентом. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server и хранилище данных SQL 

- В пошаговом руководстве [Процесс обработки и анализа данных группы на практике: использование хранилища данных SQL](machine-learning-data-science-process-sqldw-walkthrough.md) показано создание и развертывание модели машинного обучения с использованием хранилища данных SQL для общедоступного набора данных о поездках и тарифах в такси Нью-Йорка.

- В пошаговом руководстве [Процесс обработки и анализа данных группы на практике: использование SQL Server](machine-learning-data-science-process-sql-walkthrough.md) показано создание и развертывание модели машинного обучения с использованием SQL Server для общедоступного набора данных о поездках и тарифах в такси Нью-Йорка.

- Руководство [End-to-end data science walkthrough for R and SQL Server](https://msdn.microsoft.com/library/mt612857.aspx) (Комплексное пошаговое руководство по обработке и анализу данных с использованием R и SQL Server) позволяет специалистам по обработке и анализу данных использовать сочетание кода R, данных SQL Server и настраиваемых функций SQL для создания и развертывания модели R на сервере SQL Server.

- В пошаговом руководстве [In-database R analytics for SQL developers (tutorial)](https://msdn.microsoft.com/library/mt683480.aspx) (Аналитика на R в базе данных для SQL-разработчиков), предназначенном для программистов SQL, описан практический опыт создания решений расширенной аналитики с помощью Transact-SQL и служб SQL Server R Services для ввода в эксплуатацию решения R.

- В пошаговом руководстве [In-Database Python Analytics for SQL Developers](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) (Аналитика на Python в базе данных для разработчиков SQL), предназначенном для программистов SQL, описан практический опыт создания решения машинного обучения в SQL Server. Демонстрируется, как внедрить Python в приложение, добавив код Python в хранимые процедуры.

## <a name="whats-next"></a>Что дальше?
Обзор разделов с пошаговыми руководствами по задачам, которые входят в процесс обработки и анализа данных в Azure, см. на странице [Процесс обработки и анализа данных группы](http://aka.ms/datascienceprocess). 


