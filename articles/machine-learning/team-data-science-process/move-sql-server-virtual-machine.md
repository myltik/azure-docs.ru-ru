---
title: Перемещение данных в SQL Server на виртуальной машине | Документация Майкрософт
description: Перемещение данных из неструктурированных файлов или из локального SQL Server в SQL Server на виртуальной машине Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 229e2c07a3e8d83fc01dc5f1542fd250cb4678f7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838982"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Перемещение данных в SQL Server на виртуальной машине Azure
В этой статье описаны варианты перемещения данных из неструктурированных файлов (в формате CSV или TSV) или с локального сервера SQL Server на сервер SQL Server на виртуальной машине Azure. Эти задачи перемещения данных в облако являются этапом процесса обработки и анализа данных группы.

Описание вариантов перемещения данных в базу данных SQL Azure для машинного обучения см. в [этой статье](move-sql-azure.md).

Приведенное ниже **меню** содержит ссылки на разделы, описывающие прием данных в других целевых средах, где они могут храниться и обрабатываться процессом обработки и анализа данных группы (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

В следующей таблице перечислены варианты перемещения данных в SQL Server на виртуальной машине Azure.

| <b>ИСТОЧНИК</b> | <b>НАЗНАЧЕНИЕ: SQL Server на виртуальной машине Azure</b> |
| --- | --- |
| <b>Неструктурированный файл</b> |1. <a href="#insert-tables-bcp">Служебная программа командной строки для массового копирования (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">SQL-запрос на массовую вставку</a><br> 3. <a href="#sql-builtin-utilities">Графические служебные программы, встроенные в SQL Server.</a> |
| <b>Локальный сервер SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Мастер развертывания базы данных SQL Server на виртуальной машине Microsoft Azure</a><br> 2. <a href="#export-flat-file">Экспорт в неструктурированный файл</a><br> 3. <a href="#sql-migration">Мастер миграции баз данных SQL</a> <br> 4. <a href="#sql-backup">Архивация и восстановление базы данных</a><br> |

Обратите внимание на то, что в этом документе предполагается выполнение команд SQL в среде SQL Server Management Studio или в обозревателе базы данных Visual Studio.

> [!TIP]
> Вы также можете воспользоваться [фабрикой данных Azure](https://azure.microsoft.com/services/data-factory/), чтобы создать и назначить конвейер, который переносит данные в виртуальную машину SQL Server в среде Azure. Дополнительные сведения см. в статье [Перемещение данных с помощью действия копирования](../../data-factory/v1/data-factory-data-movement-activities.md).
>
>

## <a name="prereqs"></a>Предварительные требования
Для выполнения действий, описанных в этом учебнике, вам необходимо следующее.

* **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
* **Azure storage account**. Учетная запись хранения Azure будет использоваться для хранения данных в этом учебнике. Если у вас ее нет, см. раздел [Создание учетной записи хранения](../../storage/common/storage-create-storage-account.md#create-a-storage-account). После создания учетной записи хранения необходимо получить ключ, используемый для доступа к хранилищу. Ознакомьтесь с разделом [Управление ключами доступа к хранилищу](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Подготовленный **SQL Server на виртуальной машине Azure**. Инструкции см. в статье [Настройка SQL Server на виртуальной машине Azure как сервера IPython Notebook для расширенной аналитики](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Установленная и настроенная локальная среда **Azure PowerShell**. Инструкции см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Перемещение данных из неструктурированного файла на сервер SQL Server на виртуальной машине Azure
Если данные (упорядоченные по строкам и столбцам) хранятся в неструктурированном файле, этот файл можно переместить в виртуальную машину SQL Server в Azure с помощью следующих средств:

1. [Служебная программа командной строки для массового копирования (BCP)](#insert-tables-bcp)
2. [SQL-запрос на массовую вставку ](#insert-tables-bulkquery)
3. [Графические служебные программы, встроенные в SQL Server (импорт или экспорт, службы SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Служебная программа командной строки для массового копирования (BCP)
BCP — это служебная программа командной строки, устанавливаемая вместе с SQL Server, которая предоставляет один из самых быстрых способов перемещения данных. Она работает во всех трех вариантах SQL Server (в локальном SQL Server, SQL Azure и виртуальной машине SQL Server в Azure).

> [!NOTE]
> **Где следует размещать данные для программы BCP?**  
> Это не обязательно, но если файлы, содержащие источник данных, расположены на одном компьютере с целевым сервером SQL, это позволяет ускорить перемещение (скорость сети и скорость ввода-вывода на локальном диске). Перемещать неструктурированные файлы, содержащие данные, на компьютер с SQL Server можно с помощью различных инструментов копирования файлов, таких как [AZCopy](../../storage/common/storage-use-azcopy.md), [обозреватель хранилищ Azure](http://storageexplorer.com/) или средства копирования и вставки по протоколу удаленного рабочего стола (RDP) ОС Windows.
>
>

1. Убедитесь, что базы данных и таблицы создаются в целевой базе данных SQL Server. В примере ниже показано, как выполнить эту задачу с помощью команд `Create Database` и `Create Table`.

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Создайте файл форматирования с описанием схемы для таблицы, выполнив следующую команду в командной строке компьютера, на котором установлена программа BCP.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Вставьте данные в базу данных с помощью команды BCP, как показано ниже. Запуск выполняется из командной строки при условии, что на этом компьютере установлен SQL Server:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Оптимизация операций вставки BCP.** Подробные сведения об оптимизации таких операций вставки см. в статье [Рекомендации по оптимизации массового импорта данных](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx).
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Распараллеливание операций вставки для ускорения перемещения данных
Вы можете ускорить процесс перемещения данных большого размера, выполняя несколько команд BCP одновременно в сценарии PowerShell.

> [!NOTE]
> **Прием данных большого размера.** Чтобы оптимизировать загрузку больших и очень больших наборов данных, разбейте таблицы логических и физических баз данных на разделы, используя несколько групп файлов и секционированных таблиц. Дополнительные сведения о создании секционированных таблиц и загрузке в них данных см. в статье [Параллельный массовый импорт данных с использованием таблиц секционирования SQL](parallel-load-sql-partitioned-tables.md).
>
>

В примере сценария PowerShell ниже показаны параллельные операции вставки при использовании программы BCP.

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
      }


    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }


    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }

    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>SQL-запрос на массовую вставку
[SQL-запрос на массовую вставку](https://msdn.microsoft.com/library/ms188365) можно использовать для импорта в базу данных информации из файлов на основе строк или столбцов (поддерживаемые типы описаны в статье [Подготовка данных к массовому экспорту или импорту (SQL Server)](https://msdn.microsoft.com/library/ms188609)).

Ниже приведены некоторые примеры команд для массовой вставки:  

1. Перед импортом проанализируйте данные, задайте все необходимые пользовательские настройки и убедитесь, что в базе данных SQL Server для всех специальных полей, таких как дата, задан одинаковый формат. Ниже показано, как задать формат даты "год-месяц-день" (если в данных содержатся даты в формате "год-месяц-день"):

        SET DATEFORMAT ymd;    
2. Импорт данных с помощью операторов массового импорта:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Служебные программы, встроенные в SQL Server
Службы SQL Server Integrations Services (SSIS) можно использовать для импорта данных из неструктурированного файла в виртуальную машину SQL Server в Azure.
Службы SSIS доступны в двух средах Studio. Дополнительные сведения см. в статье [Разработка Integration Services (SSIS) и средства управления](https://technet.microsoft.com/library/ms140028.aspx):

* сведения о SQL Server Data Tools см. в статье [Скачать SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/data/tools.aspx);  
* сведения о мастере импорта и экспорта см. в статье [Импорт и экспорт источников данных, поддерживаемых SQL Server](https://msdn.microsoft.com/library/ms141209.aspx).

## <a name="sqlonprem_to_sqlonazurevm"></a>Перемещение данных с локального сервера SQL Server на сервер SQL Server на виртуальной машине Azure
Вы также можете использовать следующие стратегии миграции:

1. [Мастер развертывания Базы данных SQL Server на виртуальной машине Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Экспорт в неструктурированный файл](#export-flat-file)
3. [Мастер миграции баз данных SQL](#sql-migration)
4. [Архивация и восстановление базы данных](#sql-backup)

Описание каждого из этих способов см. ниже.

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Мастер развертывания Базы данных SQL Server на виртуальной машине Microsoft Azure
**Мастер развертывания базы данных SQL Server на виртуальной машине Microsoft Azure** представляет собой простой и рекомендуемый способ перемещения данных из локального экземпляра SQL Server на сервер SQL Server на виртуальной машине Azure. Подробные инструкции, а также описание других вариантов см. в статье [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Экспорт в неструктурированный файл
Для массового экспорта данных с локального сервера SQL Server можно использовать различные способы, которые описаны в разделе [Массовый импорт и экспорт данных (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . В этом документе в качестве примера описано использование программы массового копирования (BCP). После экспорта данных в неструктурированный файл этот файл можно импортировать в другой SQL Server с помощью операции массового импорта.

1. Экспортируйте данные из локального SQL Server в файл с помощью служебной программы BCP, используя следующую команду:

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Создайте базу данных и таблицу в виртуальной машине SQL Server в Azure с помощью команд `create database` и `create table` для схемы таблицы, экспортированной на шаге 1.
3. Создайте файл форматирования для описания схемы таблицы экспортируемых или импортируемых данных. Информация о файле форматирования приведена в разделе [Создание файла форматирования (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Создание файла форматирования при запуске служебной программы BCP на компьютере с SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Создание файла форматирования при удаленном запуске BCP для SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. С помощью любого из методов, описанных в разделе [Перемещение данных из файла источника](#filesource_to_sqlonazurevm) , переместите данные неструктурированных файлов в SQL Server.

### <a name="sql-migration"></a>Мастер миграции баз данных SQL
[Мастер миграции баз данных SQL Server](http://sqlazuremw.codeplex.com/) представляет собой удобный способ перемещения данных между двумя экземплярами SQL Server. Пользователь может сопоставлять схемы данных между источниками и таблицами назначения, выбирать типы столбцов и выполнять другие функции. Этот мастер использует программу массового копирования (BCP). Ниже приведен снимок экрана приветствия мастера миграции баз данных SQL.  

![Мастер миграции SQL Server][2]

### <a name="sql-backup"></a>Архивация и восстановление базы данных
SQL Server поддерживает:

1. [Архивацию и восстановление базы данных](https://msdn.microsoft.com/library/ms187048.aspx) (экспорт в локальный файл и экспорт BACPAC-файла в большой двоичный объект) и [приложения уровня данных](https://msdn.microsoft.com/library/ee210546.aspx) (с использованием BACPAC-файла).
2. Непосредственное создание виртуальных машин SQL Server в Azure с использованием скопированной базы данных или копирование в существующую базу данных SQL Azure. Дополнительные сведения см. в статье [Использование мастера копирования базы данных](https://msdn.microsoft.com/library/ms188664.aspx).

Ниже приведен снимок экрана параметров архивации и восстановления базы данных в среде SQL Server Management Studio.

![Средство импорта данных из SQL Server][1]

## <a name="resources"></a>Ресурсы
[Миграция базы данных в SQL Server на виртуальной машине Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Приступая к работе с SQL Server в виртуальных машинах Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
