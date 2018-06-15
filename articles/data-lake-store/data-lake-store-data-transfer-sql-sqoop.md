---
title: Копирование данных между Data Lake Store и базой данных SQL Azure с помощью Sqoop | Документация Майкрософт
description: Использование Sqoop для копирования данных между базой данных SQL Azure и хранилищем озера данных
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 4f0c20b7d18d9b03ae7127e6cdcdc269a3c4b404
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624171"
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Копирование данных между хранилищем озера данных и базой данных SQL Azure с помощью Sqoop
Узнайте, как использовать Apache Sqoop для импорта и экспорта данных между базой данных SQL Azure и хранилищем озера данных.

## <a name="what-is-sqoop"></a>Что такое Sqoop?
Приложения для работы с большими объемами данных являются естественным выбором для обработки неструктурированных и частично структурированных данных, таких как журналы и файлы. Однако может существовать необходимость обработки структурированных данных, хранимых в реляционных базах данных.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) — это средство, предназначенное для передачи данных между реляционными базами данных и репозиторием больших данных, например Data Lake Store. Его можно использовать для импорта данных из системы управления реляционными базами данных (RDBMS), такой как база данных SQL Azure, в хранилище озера данных. Затем данные можно преобразовать и проанализировать с помощью рабочих нагрузок больших данных, а после этого экспортировать их обратно в RDBMS. В этом учебнике в качестве реляционной базы данных для импорта и экспорта используется база данных SQL Azure.

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись хранения озера данных Azure**. Инструкции по созданию учетной записи см. в статье [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Store. См. статью [Создание кластера HDInsight с Data Lake Store с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md). В этой статье предполагается, что у вас есть кластер HDInsight на платформе Linux с доступом к хранилищу озера данных.
* **База данных SQL Azure**. Инструкции по созданию базы данных см. в статье [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../sql-database/sql-database-get-started.md).

## <a name="do-you-learn-fast-with-videos"></a>Учитесь быстрее с помощью видео?
[Просмотрите это видно](https://mix.office.com/watch/1butcdjxmu114) об использовании Distcp для копирования данных между большими двоичными объектами службы хранилища Azure и хранилищем озера данных Azure.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Создание образцов таблиц в базе данных SQL Azure
1. Для начала создайте два образца таблиц в базе данных SQL Azure. Используйте [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio для подключения к базе данных SQL Azure и выполните приведенные ниже запросы.

    **Создание Table1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Создание Table2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. В таблицу **Table1**добавьте несколько примеров данных. Оставьте таблицу **Table2** пустой. Мы будем импортировать данные из таблицы **Table1** в хранилище озера данных. Затем мы будем экспортировать данные из хранилища озера данных в таблицу **Table2**. Выполните следующий фрагмент кода.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Использование Sqoop из кластера Azure HDInsight с доступом к хранилищу озера данных
В кластере HDInsight уже имеются доступные пакеты Sqoop. Если кластер HDInsight настроен для использования хранилища озера данных в качестве дополнительного хранилища, можно использовать Sqoop (без изменения конфигурации) для импорта и экспорта данных между реляционной базой данных (в данном примере это база данных SQL Azure) и учетной записью хранилища озера данных.

1. В этом учебнике предполагается, что вы создали кластер Linux, поэтому для подключения к нему следует использовать SSH. См. раздел [Подключение к кластеру HDInsight на основе Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Проверьте, доступна ли учетная запись хранилища озера данных из кластера. Выполните следующую команду из командной строки SSH:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Она должна вывести список файлов и папок в учетной записи хранилища озера данных.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Импорт данных из базы данных SQL Azure в хранилище озера данных
1. Перейдите в каталог, где доступны пакеты Sqoop. Как правило, это будет каталог `/usr/hdp/<version>/sqoop/bin`.
2. Импортируйте данные из таблицы **Table1** в учетную запись хранилища озера данных. Используйте следующий синтаксис:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Обратите внимание, что заполнитель **sql-database-server-name** представляет имя сервера, на котором запущена база данных SQL Azure. **sql-database-name** представляет реальное имя базы данных.

    Например,


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Убедитесь, что данные были переданы в учетную запись хранилища озера данных. Выполните следующую команду:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Вы должны увидеть следующие выходные данные.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Каждый файл **part-m-*** соответствует строке в исходной таблице **Table1**. Чтобы проверить это, просмотрите содержимое файлов part-m-*.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Экспорт данных из хранилища озера данных в базу данных SQL Azure
1. Экспортируйте данные из учетной записи Data Lake Store в пустую таблицу **Table2**в базе данных SQL Azure. Используйте приведенный ниже синтаксис.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Например,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Убедитесь, что данные были отправлены в таблицу базы данных SQL. С помощью [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio подключитесь к базе данных SQL Azure, а затем выполните следующий запрос.

        SELECT * FROM TABLE2

    Вы должны увидеть следующие выходные данные.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Рекомендации по производительности при использовании Sqoop

Сведения о настройке производительности задания Sqoop для копирования данных в Data Lake Store см. в записи блога, посвященной [производительности Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>См. также
* [Copy data from Azure Storage Blobs to Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
* [Использование аналитики озера данных Azure с хранилищем озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)
