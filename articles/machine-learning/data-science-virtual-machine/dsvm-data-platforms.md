---
title: Платформы данных для виртуальных машин для обработки и анализа данных в Azure | Документация Майкрософт
description: Платформы данных для виртуальных машин для обработки и анализа данных.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: b3f340006801287383c2afb2924706affbd77a51
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31411035"
---
# <a name="data-platforms"></a>Платформы данных

На виртуальной машине для обработки и анализа данных (DSVM) можно создавать аналитику на множестве платформ данных. Кроме интерфейсов для платформ удаленных данных DSVM предоставляет локальный экземпляр для быстрой разработки и создания прототипов. 

Ниже приведены средства платформы данных, поддерживаемые на DSVM. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition.

| | |
| ------------- | ------------- |
| Что это такое?   | Экземпляр локальной реляционной базы данных      |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Windows      |
| Распространенные способы применения      | Быстрая локальная разработка приложений с меньшим набором данных <br/> Запуск R внутри базы данных   |
| Ссылки на примеры      |    Небольшой пример набора данных поездок по Нью-Йорку загружается в базу данных SQL `nyctaxi`. <br/> Пример Jupyter, показывающий аналитику Microsoft R и внутреннюю аналитику, можно найти в следующем файле:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Дополнительные средства на виртуальной машине для обработки и анализа данных       | SQL Server Management Studio <br/> Драйверы ODBC и JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> Выпуск для разработчиков SQL Server 2016 предназначен только для разработки и тестирования. Для запуска в рабочей среде вам потребуется лицензия или одна из виртуальных машин SQL Server. 


### <a name="setup"></a>Настройка

Сервер базы данных уже предварительно настроен, и службы Windows, связанные с SQL Server (например, `SQL Server (MSSQLSERVER)`), настроены для автоматического запуска. Включить внутреннюю аналитику базы данных с использованием Microsoft R можно, только выполнив запуск вручную. Чтобы сделать это, выполните следующую команду как однократное действие в SQL Server Management Studio (SSMS) после входа в систему с правами администратора, откройте "Создать запрос" в среде SSMS, выберите базу данных `master`, а затем запустите: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Чтобы запустить SQL Server Management Studio, найдите среду "SQL Server Management Studio" в списке программ или воспользуйтесь поиском Windows, чтобы найти и запустить ее. При запросе учетных данных выберите "Проверка подлинности Windows" и используйте имя компьютера или ```localhost``` в поле имени сервера SQL. 

### <a name="how-to-use--run-it"></a>Запуск и использование  

Сервер базы данных со стандартным экземпляром базы данных выполняется автоматически по умолчанию. Чтобы получить доступ к базе данных SQL Server локально, на виртуальной машине можно использовать такие средства, как SQL Server Management Studio. Учетная запись локального администратора имеет права администратора на доступ к базе данных. 

Кроме того, на DSVM есть драйверы ODBC и JDBC для подключения к SQL Server, базам данных Azure SQL и хранилищу данных SQL Azure из приложений, написанных на нескольких языках, включая Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Настройка и установка на DSVM 

SQL Server устанавливается обычным образом. Его можно найти в `C:\Program Files\Microsoft SQL Server`. Экземпляр R внутри базы данных находится в `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. На DSVM также есть отдельный изолированный экземпляр Microsoft R Server, установленный в `C:\Program Files\Microsoft\R Server\R_SERVER`. Эти два экземпляра R используют разные библиотеки.


## <a name="apache-spark-2x-standalone"></a>Экземпляр Apache Spark 2.x (изолированный)

| | |
| ------------- | ------------- |
| Что это такое?   | Изолированный экземпляр (внутрипроцессный с одним узлом) популярной платформы Apache Spark, система для быстрой обработки данных большого объема и машинного обучения     |
| Поддерживаемые выпуски виртуальных машин для обработки и анализа данных (DSVM)      | Linux <br /> Windows (экспериментальная возможность)      |
| Распространенные способы применения      | * Быстрая разработка приложений Spark/PySpark локально с меньшим набором данных и последующее развертывание ее в больших кластерах Spark, таких как Azure HDInsight<br/> * Тестирование контекста Microsoft R Server Spark <br />* Использование библиотеки SparkML или Microsoft [MMLSpark](https://github.com/Azure/mmlspark) с открытым исходным кодом для создания приложений машинного обучения  |
| Ссылки на примеры      |    Пример Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (контекст Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Дополнительные средства на виртуальной машине для обработки и анализа данных       | PySpark, Scala<br/>Jupyter (ядра Spark/PySpark)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Использование
Запустить Spark можно, отправив задания Spark в командной строке, используя команды `spark-submit` или `pyspark`. Вы можете также создать записную книжку Jupyter с ядром Spark. 

Кроме того, можно использовать Spark из R с помощью таких библиотек, как SparkR, Sparklyr или Microsoft R Server, доступных на DSVM. См. указатели на примеры в предыдущей таблице. 

> [!NOTE]
> Запуск Microsoft R Server в контексте Spark DSVM поддерживается только в версии DSVM для Ubuntu Linux. 



### <a name="setup"></a>Настройка
Перед запуском в контексте Spark на Microsoft R Server в версии DSVM для Ubuntu Linux необходимо выполнить одноразовую настройку для включения локального кластера Hadoop HDFS с одним узлом и экземпляра Yarn. По умолчанию на виртуальной машине для обработки и анализа данных службы Hadoop установлены, но отключены. Чтобы их включить, при первом использовании необходимо выполнить следующие команды в качестве корня:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Вы можете остановить работу связанных служб Hadoop, когда они не нужны, выполнив следующую команду: ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn````. Пример, демонстрирующий, как разрабатывать и тестировать с использованием MRS в удаленном контексте Spark (являющимся изолированным экземпляром Spark на DSVM), доступен в каталоге `/dsvm/samples/MRS`. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Настройка и установка на DSVM 
|платформа|Размещение установки ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Библиотеки для доступа к данным из большого двоичного объекта или хранилища Azure Data Lake (ADLS) и библиотеки с использованием машинного обучения MMLSpark корпорации Майкрософт предварительно установлены в JAR-файлах $SPARK_HOME. Эти JAR-файлы автоматически загружаются при запуске Spark. По умолчанию Spark использует данные на локальном диске. Чтобы экземпляр Spark на DSVM получил доступ к данным, хранящимся в большом двоичном объекте Azure или ADLS, необходимо создать и настроить файл `core-site.xml` на основе шаблона, хранящегося в файле $SPARK_HOME/conf/core-site.xml.template (в котором находятся заполнители для конфигураций большого двоичного объекта и ADLS), с правильными учетными данными для большого двоичного объекта Azure и хранилища Azure Data Lake. Более подробные инструкции по созданию учетных данных службы ADLS можно найти [здесь](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). После ввода учетных данных для большого двоичного объекта Azure или ADLS в файле core-site.xml можно ссылаться на данные, хранящиеся в этих источниках данных, используя префикс универсального кода ресурса wasb:// или adl://. 

