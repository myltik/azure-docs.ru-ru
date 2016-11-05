---
title: Анализ журналов веб-сайта с помощью аналитики озера данных Azure | Microsoft Docs
description: 'Информация об анализе журналов веб-сайта с помощью аналитики озера данных Azure. '
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Руководство: анализ журналов веб-сайта с помощью службы аналитики озера данных Azure
Сведения об анализе журналов веб-сайтов с помощью службы аналитики озера, а также информация об источниках ссылок, которые столкнулись с ошибками во время попытки посетить веб-сайт.

> [!NOTE]
> Если вы просто хотите, чтобы приложение работало, сэкономьте время и ознакомьтесь со статьей [Интерактивные учебники по использованию аналитики озера данных Azure](data-lake-analytics-use-interactive-tutorials.md). Настоящее руководство основано на том же сценарии и том же коде. Цель этого руководства — познакомить разработчиков с процессом создания и запуска приложения аналитики озера данных от начала до конца.
> 
> 

## Предварительные требования:
* **Visual Studio 2015, Visual Studio 2013 с обновлением 4 или Visual Studio 2012 с установленным Visual C++**
* **Microsoft Azure SDK для .NET (версии 2.5 или выше)**. Вы можете установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* **[Средства озера данных для Visual Studio](http://aka.ms/adltoolsvs)**.
  
    После установки средств озера данных для Visual Studio вы увидите меню **Озеро данных** в Visual Studio:
  
    ![Меню U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Базовые знания об аналитике озера данных и средствах озера данных для Visual Studio**. Чтобы начать работу, см. следующие статьи.
  
  * [Начало работы с аналитическим модулем озера данных Azure при помощи портала Azure](data-lake-analytics-get-started-portal.md).
  * [Разработка сценария U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Учетная запись аналитики озера данных.** См. статью [Создание учетной записи аналитики озера данных Azure](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
  
    Средства озера данных не поддерживают создание учетных записей аналитики озера данных. Поэтому ее необходимо создать с помощью портала Azure, Azure PowerShell, пакета SDK .NET или интерфейса командной строки Azure.
* **Передача образца данных в учетную запись аналитики озера данных.** См. статью [Передача SearchLog.tsv в учетную запись хранения аналитики озера данных по умолчанию](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).
  
    Чтобы выполнить задание аналитики озера данных, потребуются некоторые данные. Несмотря на то, что средства озера данных поддерживают передачу данных, чтобы упростить работу с этим руководством, для передачи примера данных мы используем портал.

## Подключение к Azure
Прежде чем можно будет скомпилировать и протестировать любой сценарий U-SQL, необходимо сначала подключиться к Azure.

**Подключение к аналитике озера данных**

1. Откройте Visual Studio.
2. В меню **Озеро данных** щелкните пункт **Параметры и настройки**.
3. Выберите пункт **Вход** или **Изменить пользователя**, если кто-то уже выполнил вход, и выполните инструкции для входа.
4. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно параметров и настроек.

**Просмотр учетных записей аналитики озера данных**

1. В Visual Studio откройте **обозреватель серверов**, нажав клавиши **CTRL + ALT + S**.
2. В **обозревателе серверов** разверните **Azure**, а затем — **Аналитика озера данных**. Будет выведен список учетных записей аналитики озера данных, если они есть. Создать учетную запись аналитики озера данных в Studio невозможно. Описание создания учетной записи см. в статье [Начало работы со службой аналитики озера данных Azure с использованием портала Azure](data-lake-analytics-get-started-portal.md) или [Начало работы со службой аналитики озера данных Azure с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## Разработка приложения U-SQL
Приложение U-SQL представляет собой главным образом сценарий U-SQL. Дополнительные сведения о языке U-SQL см. в статье [Приступая к работе с U-SQL](data-lake-analytics-u-sql-get-started.md).

Вы можете добавить в приложение пользовательские операторы сложения. Дополнительную информацию см. в статье [Разработка пользовательских операторов U-SQL для заданий аналитики озера данных](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Создание и передача задания аналитики озера данных**

1. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
2. Выберите тип «Проект U-SQL».
   
    ![Новый проект U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Нажмите кнопку **ОК**. Visual Studio создает решение с помощью файла Script.usql.
4. Скопируйте следующий сценарий в файл Script.usql.
   
        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;
   
        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN
   
            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;
   
        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS
   
        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;
   
    Для знакомства с U-SQL см. статью [Начало работы с языком U-SQL для аналитики озера данных Azure](data-lake-analytics-u-sql-get-started.md).
5. Добавьте в проект новый скрипт U-SQL и введите следующую команду:
   
        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;
   
        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Вернитесь к первому скрипту U-SQL и рядом с кнопкой **Отправить** укажите свою учетную запись аналитики.
7. В **обозревателе решений** щелкните правой кнопкой мыши файл **Script.usql** и выберите команду **Создать скрипт**. Проверьте результаты на панели вывода.
8. В **обозревателе решений** щелкните правой кнопкой мыши файл **Script.usql** и выберите команду **Отправить скрипт**.
9. Убедитесь, что для запуска задания выбрана правильная **учетная запись аналитики**, и нажмите кнопку **Отправить**. Результаты отправки и ссылка на задание появятся в окне результатов средств озера данных для Visual Studio после завершения отправки.
10. Дождитесь успешного завершения задания. Если задание не выполнено, скорее всего, отсутствует исходный файл. См. раздел «Предварительные требования» этого руководства. Дополнительные сведения об устранении неполадок см. в статье [Мониторинг и устранение неполадок заданий аналитики озера данных Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
    
    После выполнения задания вы должны увидеть такие результаты:
    
    ![аналитика озера данных анализ веб-журналов журналы веб-сайтов](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Теперь повторите шаги 7–10 для файла **Script1.usql**.

> [!NOTE]
> Вы не можете осуществлять чтение или запись в таблице U-SQL, которая была создана или изменена в рамках того же скрипта. Поэтому для этого примера используется два скрипта.
> 
> 

**Просмотр выходных данных задания**

1. В **обозревателе сервера** разверните узлы **Azure** и **Аналитика озера данных**, разверните учетную запись аналитики озера данных и **учетные записи хранения**, а затем щелкните правой кнопкой мыши учетную запись хранения озера данных по умолчанию и выберите команду **Обозреватель**.
2. Дважды щелкните **Примеры**, чтобы открыть папку с примерами, и дважды щелкните **Выходные данные**.
3. Дважды щелкните файл **UnsuccessfulResponsees.log**.
4. Кроме того, вы можете дважды щелкнуть выходной файл в графическом представлении задания для перехода непосредственно к выходным данным.

## Дополнительные материалы
Для начала работы с аналитикой озера данных с использованием различных средств см. следующие статьи.

* [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
* [Начало работы с аналитикой озера данных с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Начало работы с аналитикой озера данных с помощью пакета SDK .NET.](data-lake-analytics-get-started-net-sdk.md)

Другие ресурсы по разработке

* [Разработка сценариев U-SQL с помощью средств озера данных для Visual Studio.](data-lake-analytics-data-lake-tools-get-started.md)
* [Начало работы с языком U-SQL в службе аналитики озера данных Azure.](data-lake-analytics-u-sql-get-started.md)
* [Разработка пользовательских операторов U-SQL для заданий аналитики озера данных.](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0914_2016-->