---
title: "Проектирование первой базы данных SQL Azure | Документация Майкрософт"
description: "Проектирование первой базы данных SQL Azure."
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/30/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 0d02954829ebac9275c014f7dac7e1ec423b0fc1
ms.lasthandoff: 04/21/2017


---

# <a name="design-your-first-azure-sql-database"></a>Проектирование первой базы данных SQL Azure

В этом руководстве приведены сведения о создании базы данных для университета, чтобы отслеживать успеваемость студентов и регистрацию на курсы. Здесь также показано, как создать базу данных SQL Azure на логическом сервере базы данных SQL Azure, добавлять таблицы в базу данных, загружать данные в таблицы и выполнять запросы к базе данных на [портале Azure](https://portal.azure.com/) и в [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS). Кроме того, здесь объясняется, как использовать возможности [восстановления до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore) в базе данных SQL, чтобы выполнить восстановление до более ранней точки во времени.

Для работы с этим руководством у вас должна быть установлена последняя версия [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). 

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database-in-azure"></a>Создание пустой базы данных SQL в Azure

База данных Azure SQL создается с определенным набором [вычислительных ресурсов и ресурсов хранения](sql-database-service-tiers.md). База данных создается в пределах [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md) и [логического сервера базы данных SQL Azure](sql-database-features.md). 

Чтобы создать пустую базу данных SQL, выполните приведенные ниже действия. 

1. Щелкните **Создать** в верхнем левом углу портала Azure.

2. Выберите **Базы данных** на странице **создания** и щелкните **База данных SQL** на странице **Базы данных**. 

    ![Создание пустой базы данных](./media/sql-database-design-first-database/create-empty-database.png)

3. Заполните форму базы данных SQL, указав следующую информацию, как показано на предыдущем рисунке.     

   - имя базы данных — **mySampleDatabase**;
   - группа ресурсов — **myResourceGroup**;
   - Источник: **пустая база данных**.

4. Щелкните **Сервер**, чтобы создать и настроить новый сервер для новой базы данных. Заполните **форму для создания сервера**, указав глобально уникальное имя сервера, имя для входа администратора сервера и выбранный вами пароль. 

    ![Создание базы данных — сервер](./media//sql-database-design-first-database/create-database-server.png)
5. Нажмите кнопку **Выбрать**.

6. Щелкните **Ценовая категория**, чтобы указать уровень производительности и уровень служб для новой базы данных. Для этого руководства выберите значения **20 DTU** и **250** ГБ хранилища.

    ![Создание базы данных — s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Нажмите кнопку **Применить**.  

8. Нажмите кнопку **Создать**, чтобы подготовить базу данных. Подготовка занимает около полутора минут. 

9. На панели инструментов щелкните **Уведомления**, чтобы отслеживать процесс развертывания.

    ![уведомление](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>создадим правило брандмауэра на уровне сервера;

Базы данных SQL Azure защищены брандмауэром. По умолчанию все подключения к серверу и базам данных на сервере отклоняются. Выполните приведенные ниже действия, чтобы создать для сервера [правило брандмауэра уровня сервера базы данных SQL](sql-database-firewall-configure.md), разрешающее подключения с IP-адреса клиента. 

1. По завершении развертывания щелкните раздел **Базы данных SQL** в меню слева и выберите новую базу данных **mySampleDatabase** на странице **баз данных SQL**. После этого откроется страница обзора базы данных, где будет указано полное имя сервера (например, **mynewserver-20170313.database.windows.net**) и предоставлены параметры для дальнейшей настройки.

      ![правило брандмауэра для сервера](./media/sql-database-design-first-database/server-firewall-rule.png) 

2. Щелкните **Настройка брандмауэра для сервера** на панели инструментов, как показано на предыдущем рисунке. Откроется страница **параметров брандмауэра** для сервера базы данных SQL. 

3. Нажмите кнопку **Добавить IP-адрес клиента** на панели инструментов, а затем щелкните **Сохранить**. Для текущего IP-адреса будет создано правило брандмауэра уровня сервера.

      ![Настройка правила брандмауэра для сервера](./media/sql-database-design-first-database/server-firewall-rule-set.png) 

4. Нажмите кнопку **ОК**, а затем щелкните **X**, чтобы закрыть страницу **Параметры брандмауэра**.

Теперь можно подключиться к базе данных и ее серверу с помощью SQL Server Management Studio или другого средства по своему усмотрению.

> [!NOTE]
> База данных SQL обменивается данными через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу базы данных SQL Azure. Для этого ваш ИТ-отдел должен открыть порт 1433.
>

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите полное имя сервера для сервера базы данных SQL Azure на портале Azure. Используйте полное имя сервера, чтобы подключиться к серверу с помощью SQL Server Management Studio.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице портала Azure вашей базы данных в области **Основные компоненты** найдите и скопируйте **имя сервера**.

    ![Сведения о подключении](./media/sql-database-connect-query-ssms/connection-information.png) 

## <a name="connect-to-your-database-using-sql-server-management-studio"></a>Подключение к базе данных с помощью SQL Server Management Studio

Используйте [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) для подключения к серверу базы данных SQL Azure.

1. Откройте среду SQL Server Management Studio.

2. В диалоговом окне **Подключение к серверу** введите следующие значения.
   - **Тип сервера** — укажите ядро СУБД.
   - **Имя сервера** — введите полное имя сервера, например **mynewserver20170313.database.windows.net**.
   - **Проверка подлинности** — укажите проверку подлинности SQL Server.
   - **Имя входа** — введите учетную запись администратора сервера.
   - **Пароль** — введите пароль учетной записи администратора сервера.


   <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. В диалоговом окне **Подключение к серверу** щелкните **Параметры**. В разделе **Подключение к базе данных** введите **mySampleDatabase**, чтобы подключиться к этой базе данных.

   ![Подключение к базе данных на сервере](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Щелкните **Подключить**. Откроется окно обозревателя объектов в SSMS. 

5. В обозревателе объектов разверните **базы данных**, затем выберите **mySampleDatabase**, чтобы просмотреть объекты в образце базы данных.

   ![Объекты базы данных](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Создание таблиц в базе данных 

Создайте схему базы данных с четырьмя таблицами, моделирующими систему управления студентами для университетов, с помощью [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Person
- Курс
- Студент
- Актив, моделирующий систему управления студентами для университетов

На приведенной ниже схеме показано, как эти таблицы связаны друг с другом. Некоторые из этих таблиц ссылаются на столбцы в других таблицах. Например, таблица Student ссылается на столбец **PersonId** таблицы **Person**. Изучите схему, чтобы понять, как таблицы в этом руководстве связаны друг с другом. Подробные сведения о создании эффективных таблиц баз данных см. в [этой статье](https://msdn.microsoft.com/library/cc505842.aspx). Дополнительные сведения о выборе типов данных см. в [этой статье](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Для создания и проектирования таблиц можно также использовать [конструктор таблиц в SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx). 

![Связи между таблицами](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. В обозревателе объектов щелкните правой кнопкой мыши **mySampleDatabase** и выберите пункт **Новый запрос**. Откроется пустое окно запроса, подключенное к базе данных.

2. Чтобы создать в базе данных четыре таблицы, в окне запроса выполните следующий запрос: 

   ```sql 
   -- Create Person table

    CREATE TABLE Person
    (
      PersonId      INT IDENTITY PRIMARY KEY,
      FirstName     NVARCHAR(128) NOT NULL,
      MiddelInitial NVARCHAR(10),
      LastName      NVARCHAR(128) NOT NULL,
      DateOfBirth   DATE NOT NULL
    )
   
   -- Create Student table
 
    CREATE TABLE Student
    (
      StudentId INT IDENTITY PRIMARY KEY,
      PersonId  INT REFERENCES Person (PersonId),
      Email     NVARCHAR(256)
    )
    
   -- Create Course table
 
    CREATE TABLE Course
    (
      CourseId  INT IDENTITY PRIMARY KEY,
      Name      NVARCHAR(50) NOT NULL,
      Teacher   NVARCHAR(256) NOT NULL
    ) 

   -- Create Credit table
 
    CREATE TABLE Credit
    (
      StudentId   INT REFERENCES Student (StudentId),
      CourseId    INT REFERENCES Course (CourseId),
      Grade       DECIMAL(5,2) CHECK (Grade <= 100.00),
      Attempt     TINYINT,
      CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
      (
        StudentId, CourseId, Grade, Attempt
      )
    )
   ```

![создание таблиц.](./media/sql-database-design-first-database/create-tables.png)

3. В обозревателе объектов SQL Server Management Studio разверните узел tables, чтобы просмотреть созданные таблицы.

   ![Создание таблиц в SSMS](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Загрузка данных в таблицу

1. В папке скачиваний создайте папку с именем **SampleTableData** для хранения примеров данных базы данных. 

2. Щелкните правой кнопкой мыши приведенные ниже ссылки и сохраните их в папку **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Откройте окно командной строки и перейдите в папку SampleTableData.

4. Выполните приведенную ниже команду, которая вставляет пример данных в таблицы.Укажите значения **ServerName**, **DatabaseName**, **UserName** и **Password**, соответствующие вашей среде.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Итак, вы загрузили пример данных в созданные ранее таблицы.

## <a name="query-the-tables"></a>Выполнение запросов к таблицам

Чтобы извлечь сведения из таблиц базы данных, выполните приведенные ниже запросы. Дополнительные сведения о создании запросов SQL см. в [этой статье](https://technet.microsoft.com/library/bb264565.aspx). Первый запрос объединяет четыре таблицы для поиска всех студентов, посещающих занятия у преподавателя Dominick Pope и оценки которых выше, чем у 75 % учащихся в этом классе. Второй запрос объединяет четыре таблицы и находит все курсы, на которые когда-либо записывался Noe Coleman.

1. В окне запроса SQL Server Management Studio выполните следующий запрос:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

    SELECT  person.FirstName,
        person.LastName,
        course.Name,
        credit.Grade
    FROM  Person AS person
        INNER JOIN Student AS student ON person.PersonId = student.PersonId
        INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
        INNER JOIN Course AS course ON credit.CourseId = course.courseId
    WHERE course.Teacher = 'Dominick Pope' 
        AND Grade > 75
   ```

2. В окне запроса SQL Server Management Studio выполните следующий запрос:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

    SELECT  course.Name,
        course.Teacher,
        credit.Grade
    FROM  Course AS course
        INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
        INNER JOIN Student AS student ON student.StudentId = credit.StudentId
        INNER JOIN Person AS person ON person.PersonId = student.PersonId
    WHERE person.FirstName = 'Noe'
        AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Восстановление базы данных до предыдущей точки во времени 

Представьте, что вы случайно удалили таблицу. Восстановить ее будет не просто. База данных SQL Azure позволяет вернуться в любой момент времени в течение последних 35 дней и восстановить данные на определенный момент времени в новой базе данных. С помощью этой базы данных можно восстановить удаленные данные. Ниже приведены действия по восстановлению базы данных до точки во времени, когда были созданы таблицы.

1. На странице "База данных SQL" на панели инструментов щелкните **Восстановить**. Откроется страница **Восстановление**.

   ![Восстановление](./media/sql-database-design-first-database/restore.png)

2. Заполните форму **Восстановление**, указав следующие сведения.
    * Имя базы данных. Укажите имя базы данных. 
    * Момент времени. Выберите вкладку **Момент времени** в форме восстановления. 
    * Точка восстановления. Выберите время до того момента, когда была изменена база данных.
    * Целевой сервер. Это значение нельзя изменить при восстановлении базы данных. 
    * Пул эластичных баз данных. Выберите вариант **Нет**.  
    * Ценовая категория. Выберите **20 DTU** и хранилище объемом **250 ГБ**.

   ![Точка восстановления](./media/sql-database-design-first-database/restore-point.png)

3. Чтобы [восстановить базу данных до точки во времени](sql-database-recovery-using-backups.md#point-in-time-restore) перед добавлением таблиц, нажмите кнопку **OК**. Восстановление базы данных до точки во времени создает копию базы данных на том же сервере, где расположена исходная база данных, с состоянием на момент указанной точки во времени (в пределах срока хранения, установленного для вашего [уровня обслуживания](sql-database-service-tiers.md)).

## <a name="next-steps"></a>Дальнейшие действия 

Примеры PowerShell для выполнения стандартных задач см. в статье [ Примеры Azure PowerShell для базы данных SQL Azure](sql-database-powershell-samples.md).

