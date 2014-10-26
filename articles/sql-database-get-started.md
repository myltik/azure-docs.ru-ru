<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create and provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="loclar"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="loclar"></tags>

# Приступая к работе с базой данных SQL Microsoft Azure

В этом учебнике описываются основы управления базой данных SQL Microsoft Azure с использованием портала управления Azure. Если вы не знакомы с администрированием базы данных, можно выполнить эти занятия для приобретения основных навыков. Потребуется около 30 минут.

Учебник не предполагает наличия опыта работы с SQL Server или базой данных SQL Azure. После прохождения учебника у вас появится пример базы данных в Azure и понимание того, как выполнять основные административные задачи с помощью портала управления.

Вы создадите и подготовите пример базы данных на платформе Azure, систему запросов и пользовательские данные, используя Excel.

## Оглавление

-   [Шаг 1: создание учетной записи Microsoft Azure][Шаг 1: создание учетной записи Microsoft Azure]
-   [Шаг 2: подключение к Azure и создание базы данных][Шаг 1: создание учетной записи Microsoft Azure]
-   [Шаг 3: настройка брандмауэра][Шаг 3: настройка брандмауэра]
-   [Шаг 4: добавление данных и схемы с помощью сценария Transact-SQL][Шаг 4: добавление данных и схемы с помощью сценария Transact-SQL]
-   [Шаг 5: создание схемы][Шаг 5: создание схемы]
-   [Шаг 6: добавление данных][Шаг 6: добавление данных]
-   [Шаг 7: запрос данных для примера и системных данных для базы данных SQL на портале управления][Шаг 7: запрос данных для примера и системных данных для базы данных SQL на портале управления]
-   [Шаг 8: создание учетных данных для входа в базу данных и назначение разрешений][Шаг 8: создание учетных данных для входа в базу данных и назначение разрешений]
-   [Шаг 9: подключение из других приложений][Шаг 9: подключение из других приложений]

## Шаг 1. Создание учетной записи Microsoft Azure

1.  Откройте браузер и перейдите по адресу [][]<http://azure.microsoft.com></a>.
    Чтобы начать работу с бесплатной учетной записью, щелкните бесплатное пробное использование в правом верхнем углу и следуйте инструкциям.

2.  Ваша учетная запись создана. Вы готовы начать работу.

## Шаг 2. Подключение к Azure и создание базы данных

1.  Выполните вход на [Портал управления][Портал управления]. Должна появиться область навигации, выглядящая следующим образом.

    ![Область навигации][Область навигации]

2.  В нижней части страницы щелкните **Создать**. При нажатии кнопки **Создать** на экране появляется список, показывающий, что можно создать.

3.  Щелкните **База данных SQL**, затем выберите **Настраиваемое создание**.

    ![Область навигации][1]

Выбор этого варианта позволяет одновременно создать новый сервер и базу данных SQL, с вами в качестве администратора. Будучи системным администратором, можно выполнить дополнительные задачи, включая подключение к порталу управления для базы данных SQL, которое будет выполнять позже в данном учебном курсе.

1.  При нажатии кнопки **Настраиваемое создание** появляется страница настроек базы данных. Эта страница предназначена для ввода основной информации, используемой для создания на сервере пустой базы данных SQL. Добавление таблиц и данных будет выполнено позднее.

    Заполните страницу настроек базы данных следующим образом:

    ![Область навигации][2]

-   Введите **School** в качестве имени базы данных.

-   Используйте для выпуска, максимального размера и параметров сортировки значения по умолчанию.

-   Выберите **Создать сервер базы данных SQL**. При выборе создания нового сервера появляется вторая страница, которая будет использоваться для задания учетной записи администратора и региона.

-   Закончив, нажмите кнопку со стрелкой, чтобы перейти к следующей странице.

1.  Введите настройки сервера следующим образом:

    ![Область навигации][3]

-   Введите имя администратора одним словом, без пробелов. Для проверки удостоверения пользователя база данных SQL использует проверку подлинности SQL с помощью шифрованного подключения. Будет создана новая учетная запись для проверки подлинности SQL Server, обладающая разрешениями администратора и использующая введенное имя. Имя администратора не может быть именем пользователя Windows, при этом оно не должно быть идентификатором Live ID. Проверка подлинности Windows для базы данных SQL не поддерживается.

-   Укажите надежный пароль, который содержит более восьми знаков, в том числе символы в верхнем и нижнем регистре, а также цифры и символы. Используйте всплывающую справку для получения информации о сложности пароля.

-   Выберите регион. Регион определяет географическое расположение сервера. Изменение региона достаточно трудоемко, поэтому выберите регион, имеющий смысл для этого сервера. Выберите местоположение, максимально близкое к вам. Хранение приложения и базы данных Azure в том же регионе снижает расходы на пропускную способность и задержку данных.

-   Проследите, чтобы флажок **Разрешить службам Azure доступ к этому серверу** остался установленным, чтобы можно было подключиться к этой базе данных, используя портал управления для базы данных SQL, Excel в Office 365 или службу отчетов SQL Reporting в Azure.

-   Закончив, щелкните флажок внизу страницы.

Обратите внимание, что имя сервера не было задано. Так как сервер базы данных SQL должен быть доступен по всему миру, база данных SQL при создании сервера настраивает соответствующие записи DNS. Созданное имя обеспечивает отсутствие конфликтов имен с другими записями DNS. Невозможно изменить имя сервера баз данных SQL.

Чтобы просмотреть имя сервера, на котором размещается только что созданная база данных **School**, щелкните **Базы данных SQL** в левой области навигации, затем щелкните базу данных **School** в представлении списка **Базы данных SQL**. На странице **Быстрый запуск** прокрутите вниз, чтобы просмотреть имя сервера.

На следующем шаге будет настроен брандмауэр, чтобы приложениям, запущенным на компьютере, был разрешен доступ к базам данных на сервере баз данных SQL.

## Шаг 3. Настройка брандмауэра

Для разрешения подключений через брандмауэр понадобится ввести данные на странице сервера.

**Примечание.** Служба базы данных SQL доступна только с TCP-портом 1433, используемым протоколом TDS, поэтому убедитесь, что брандмауэр в сети и на локальном компьютере разрешает исходящий трафик TCP для порта 1433. Дополнительную информацию см. в разделе [Брандмауэр базы данных SQL][Брандмауэр базы данных SQL].

1.  Слева в области навигации щелкните **Базы данных SQL**.

2.  В верхней части страницы щелкните **Серверы**. Затем щелкните только что созданный сервер, чтобы открыть страницу сервера.

3.  Чтобы открыть настройки **Разрешенных IP-адресов**, на странице сервера щелкните **Настройка**, а затем щелкните ссылку **Добавить в разрешенные IP-адреса**. В результате будет создано новое правило брандмауэра, чтобы разрешить запросы подключения от маршрутизатора или прокси-сервера, прослушиваемые вашим устройством.

4.  Вы можете создавать дополнительные правила брандмауэра, задавая имя правила, а также начальное и конечное значения диапазона IP-адресов.

5.  Чтобы включить взаимодействия между этим серверром и другими службами Azure, щелкните **Да** для параметра **Службы Microsoft Azure**.

6.  Чтобы сохранить изменения, в нижней части страницы щелкните **СОХРАНИТЬ**.

7.  После сохранения правила страница будет похожей на следующий снимок экрана.

    ![Область навигации][4]

Теперь у вас есть сервер баз данных SQL в Azure, правило брандмауэра, разрешающее доступ к серверу, объект базы данных и учетная запись администратора. Но у вас пока нет работающей базы данных, в которой можно выполнить запрос. Для этого ваша база данных должна иметь схему и фактические данные.

Так как в этом учебнике используются только имеющиеся в наличии инструменты, для запуска сценария Transact-SQL с целью создания предопределенной базы данных будет использовано окно запроса на портале управления для базы данных SQL.

С ростом навыков вам захочется исследовать дополнительные способы создания базы данных, в том числе программные подходы или рабочую область конструирования в SQL Server Data Tools. При наличии существующей базы данных SQL Server, работающей на локальном сервере баз данных, несложно перенести эту базу данных на только что настроенный сервер Azure. Воспользуйтесь ссылками в конце этого учебного курса, чтобы узнать, как это делается.

## Шаг 4. Добавление данных и схемы с помощью сценария Transact-SQL

На этом шаге выполняются два скрипта. Первый из них создает схему, определяющую таблицы, столбцы и связи. Второй скрипт добавляет данные. Каждое действие выполняется независимо друг от друга в отдельном подключении. При наличии опыта создания баз данных в SQL Server можно заметить ряд различий по сравнению с базой данных SQL, одним из которых является необходимость выполнения команд CREATE и INSERT в разных пакетах. База данных SQL налагает это требование, чтобы свести к минимуму атаки на данные во время их передачи.

**Примечание.** Схема и значения данных взяты из этой [статьи MSDN][статьи MSDN] и изменены для работы с базой данных SQL.

1.  Перейти на главную страницу. На [портале управления][Портал управления], база данных **School** появляется в списке элементов на главной странице.

    ![Область навигации][5]

2.  Щелкните базу данных **School**, чтобы выбрать ее, затем в нижней части страницы щелкните **Управление**. Открывается портал управления для базы данных SQL. Этот портал не связан с порталом управления Azure. Он будет использоваться для выполнения запросов и команд Transact-SQL.

3.  Чтобы войти в базу данных **School**, введите имя пользователя и пароль администратора. Это имя учетной записи администратора, указанное при создании сервера.

4.  На портале управления для базы данных SQL щелкните **Создать запрос** на ленте. В рабочей области откроется пустое окно запроса. На следующем шаге в это окно будут последовательно копироваться ряд предопределенных скриптов, добавляющих структуры и данные в пустую базу данных.

## Шаг 5. Создание схемы

На этом шаге с помощью следующего сценария будет создана схема. Сценарий сначала проверяет, нет ли уже таблицы с тем же именем, чтобы убедиться в отсутствии конфликта имен, и создает таблицу с помощью инструкции [CREATE TABLE][CREATE TABLE]. Далее, этот сценарий использует инструкцию [ALTER TABLE][ALTER TABLE], чтобы определить первичный ключ и связи таблицы.

Скопируйте скрипт и вставьте его в окно запроса. Чтобы выполнить сценарий, нажмите кнопку **Выполнить** в верхней части окна.

<div style="width:auto; height:600px; overflow:auto"><pre>
    -- Create the Department table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Department](
        [DepartmentID] [int] NOT NULL,
        [Name] [nvarchar](50) NOT NULL,
        [Budget] [money] NOT NULL,
        [StartDate] [datetime] NOT NULL,
        [Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
    [DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Create the Person table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Person](
        [PersonID] [int] IDENTITY(1,1) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [HireDate] [datetime] NULL,
        [EnrollmentDate] [datetime] NULL,
     CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED   
    (
    [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the OnsiteCourse table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnsiteCourse](
        [CourseID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Days] [nvarchar](50) NOT NULL,
        [Time] [smalldatetime] NOT NULL,
     CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the OnlineCourse table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnlineCourse](
        [CourseID] [int] NOT NULL,
        [URL] [nvarchar](100) NOT NULL,
     CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    --Create the StudentGrade table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[StudentGrade](
        [EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
        [CourseID] [int] NOT NULL,
        [StudentID] [int] NOT NULL,
        [Grade] [decimal](3, 2) NULL,
     CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
    (
        [EnrollmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the CourseInstructor table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[CourseInstructor](
        [CourseID] [int] NOT NULL,
        [PersonID] [int] NOT NULL,
     CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC,
        [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    GO

    -- Create the Course table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Course](
        [CourseID] [int] NOT NULL,
        [Title] [nvarchar](100) NOT NULL,
        [Credits] [int] NOT NULL,
        [DepartmentID] [int] NOT NULL,
     CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Create the OfficeAssignment table.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OfficeAssignment](
        [InstructorID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Timestamp] [timestamp] NOT NULL,
     CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
    (
        [InstructorID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    GO

    -- Define the relationship between OnsiteCourse and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
    ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
    GO

    -- Define the relationship between OnlineCourse and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
    ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
    GO
    -- Define the relationship between StudentGrade and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
    GO

    --Define the relationship between StudentGrade and Student.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))   
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
    GO

    -- Define the relationship between CourseInstructor and Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
     WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
     AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
     CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
      CONSTRAINT [FK_CourseInstructor_Course];
    GO

    -- Define the relationship between CourseInstructor and Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
      CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
     CONSTRAINT [FK_CourseInstructor_Person];
    GO

    -- Define the relationship between Course and Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
    REFERENCES [dbo].[Department] ([DepartmentID]);
    GO
    ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
    GO

    --Define the relationship between OfficeAssignment and Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
      AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
    ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
      CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
    REFERENCES [dbo].[Person] ([PersonID]);
    GO
    ALTER TABLE [dbo].[OfficeAssignment] CHECK 
     CONSTRAINT [FK_OfficeAssignment_Person];
    GO
</pre></div>

## Шаг 6. Добавление данных

Открыть новое окно запроса и вставьте в него следующий скрипт. Выполните этот скрипт, чтобы вставить данные. Этот сценарий использует инструкцию [INSERT][INSERT], чтобы добавить значения в каждый столбец.

<div style="width:auto; height:600px; overflow:auto"><pre>
    -- Insert data into the Person table.
    SET IDENTITY_INSERT dbo.Person ON;
    GO
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (6, 'Li', 'Yan', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (28, 'White', 'Anthony', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
    GO
    SET IDENTITY_INSERT dbo.Person OFF;
    GO
    
</pre></div>

## Шаг 7. Запрос данных для примера и системных данных для базы данных SQL на портале управления

Чтобы проверить свою работу, выполните запрос, возвращающий только что введенные данные. Можно также выполнить встроенные хранимые процедуры и использовать представления управления данными, содержащие сведения о базах данных, работающих на сервере баз данных SQL.

#### Запрос части данных для примера

Скопируйте в новое окно запроса и выполните следующий скрипт Transact-SQL, чтобы получить некоторые из только что добавленных данных.

<div style="width:auto; height:auto; overflow:auto"><pre>
    SELECT * From Person
</pre></div>

Вы должны увидеть результирующий набор с 34 строками из таблицы Person, включая PersonID, LastName, FirstName, HireDate и EnrollmentDate.

#### Запрос системных данных

Для получения информации с сервера также можно использовать системные представления и встроенные хранимые процедуры. Для целей этого учебного курса будет задействовано лишь несколько команд.

Выполните следующую команду, чтобы узнать, какие базы данных доступны на сервере.

    SELECT * FROM sys.databases  

Выполните эту команду для получения списка пользователей, подключенных к серверу в данный момент.

    SELECT user_name(),suser_sname()

Выполните эту хранимую процедуру для возвращения списка объектов в базе данных **School**.

    EXEC SP_help

Не закрывайте подключение портала к базе данных **School**. Оно снова понадобится через несколько минут.

## Шаг 8. Создание учетных данных для входа в базу данных и назначение разрешений

В базе данных SQL можно создать учетные записи и предоставить разрешения, используя Transact-SQL. В этом занятии с помощью Transact-SQL вы сможете выполнить три действия:

1.  Создание учетной записи проверки подлинности SQL Server
2.  создать пользователя базы данных и
3.  предоставить разрешения с помощью добавления роли.

Учетная запись проверки подлинности SQL Server используется для подключения к серверу. Все пользователи для доступа к базе данных на сервере баз данных SQL указывает имя и пароль учетной записи проверки подлинности SQL Server.

Чтобы создать учетную запись, сначала необходимо подключиться к базе данных **master**.

#### Создание учетной записи проверки подлинности SQL Server

1.  На [портале управления][Портал управления] выберите **Базы данных SQL**, щелкните **Серверы**, выберите сервер и щелкните белую стрелку, чтобы открыть страницу
    сервера.

2.  На странице быстрого запуска щелкните **Управление сервером**, чтобы открыть новое подключение к порталу управления для базы данных SQL.

3.  Выбирая базу данных для подключения, укажите **master**, затем войдите, используя свои имя пользователя и пароль. Это имя учетной записи администратора, указанное при создании сервера.

4.  Портал управления базой данных SQL откроется в новом окне браузера, и вы будете подключены к **master**.

5.  Если на странице появляется сообщение об ошибке, похожее на следующее, игнорируйте его. Нажмите кнопку **Создать запрос**, чтобы открыть окно запроса, позволяющее выполнять команды Transact-SQL для базы данных **master**.

    ![Область навигации][6]

6.  Скопируйте следующую команду и вставьте ее в окно запроса.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  Выполните эту команду, чтобы создать новую учетную запись SQL Server с именем "SQLDBLogin".

#### Создание пользователя базы данных и назначение разрешений

После создания учетной записи проверки подлинности SQL Server, следующим шагом является назначение этой учетной записи базы данных и уровней разрешений. С этой целью для каждой базе данных создается **пользователь базы данных**.

1.  Вернитесь на страницу портала управления базой данных SQL, которая подключается к базе данных **School**. Если окно браузера было закрыто, откройте новое подключение к базе данных **School**, используя действия из предыдущего занятия, "Добавление данных и схемы с помощью скрипта Transact-SQL".

    На странице портала управления базой данных SQL в верхнем левом углу появляется имя базы данных **School**.

    ![Область навигации][7]

2.  Нажмите кнопку **Создать запрос**, чтобы открыть новое окно запроса, и скопируйте в него следующую инструкцию.

        CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  Выполните скрипт. Этот School создает нового пользователя базы данных на основе учетной записи.

    Далее, нужно назначить разрешения, используя роль db\_datareader. Пользователи базы данных, которым назначена эта роль, могут читать все данные из всех пользовательских таблиц в базе данных.

1.  Откройте новое окно запроса, затем введите и выполните следующую инструкцию. Эта инструкция вызывает встроенную хранимую процедуру, которая назначает роль db\_datareader только что созданному новому пользователю.

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

Теперь создана новая учетная запись проверки подлинности SQL Server с правами "только чтение" для базы данных **School**. С помощью этих действий можно создать другие учетные записи проверки подлинности SQL Server, чтобы разрешить различные уровни доступа к данным.

## Шаг 9. Подключение из других приложений

Теперь, когда есть рабочая база данных, можно подключиться к ней из книги Excel.

#### Подключение из Excel

Если на компьютере установлено приложение Microsoft Excel, то вот как вы можете подключиться к своей учебной базе данных.

1.  В Microsoft Excel, на вкладке "Данные" нажмите кнопку **Из других источников** и выберите **Из SQL Server**

2.  В мастере подключения к данным введите полное доменное имя своего сервера базы данных SQL, а затем учетную запись проверки подлинности SQL Server, обладающую разрешением на доступ к этой базе данных.

    Имя сервера можно найти на портале управления Azure, в базе данных SQL, на странице «Сервер», на панели мониторинга, в разделе **Управление URL-адресом**. Имя сервера состоит из последовательности букв и цифр, за которыми следует ".database.windows.net". Укажите это имя в мастере подключения к базе данных. При задании имени не следует добавлять префикс http:// или https://.

    Войдите, используя учетную запись проверки подлинности SQL Server. Для тестирования можно использовать учетную запись администратора, созданную при установке сервера. Для регулярного доступа к данным используйте пользователя базы данных, похожего на только что созданного.

    ![Область навигации][8]

3.  На следующей странице выберите базу данных **School**, а затем выберите **Person**. Нажмите кнопку **Готово** При появлении запроса данных входа, введите их и нажмите кнопку **ОК**.

2.  Откроется диалоговое окно "Импорт данных", предлагающее выбрать способ и место для импорта данных. Не меняя настроек по умолчанию, нажмите кнопку **ОК**

    ![Область навигации][9]

3.  На листе вы должны увидеть таблицу с результирующим набором, в который входит 34 строки из таблицы Person, включая PersonID, LastName, FirstName, HireDate и EnrollmentDate, по аналогии с результатами запроса из шага 7.

При использовании только Excel одновременно можно импортировать лишь одну таблицу. Лучше использовать надстройку PowerPivot для Excel, которая позволяет импортировать несколько таблиц и работать с ними, как с одним набором данных. Работа с PowerPivot выходит за рамки данного учебника, но дополнительную информацию по этой теме вы можете найти в разделе [PowerPivot для Excel][PowerPivot для Excel].

## Дальнейшие действия

Теперь, познакомившись с базой данных SQL и порталами управления, можно попробовать другие средства и приемы, используемые администраторами баз данных SQL Server.

Чтобы активно управлять новой базой данных, рассмотрите возможность установки и использования среды SQL Server Management Studio. Management Studio — это главное средство администрирования для управления базами данных SQL Server, включая выполняемые в Azure. В среде Management Studio можно сохранять запросы для дальнейшего использования, добавлять новые таблицы и хранимые процедуры, а также отточить свое мастерство Transact-SQL в среде использования скриптов с широкими возможностями, включая проверку синтаксиса, Intellisense и шаблоны. Чтобы начать работу, следуйте инструкциям раздела [Управление базами данных SQL с помощью среды SQL Server Management Studio][Управление базами данных SQL с помощью среды SQL Server Management Studio]

Хорошее владение запросами Transact-SQL и языком определения данных крайне важно для администраторов баз данных. Если вы новичок в языке Transact-SQL, рекомендуем для начала использовать [Учебник. Написание инструкций Transact-SQL][Учебник. Написание инструкций Transact-SQL], чтобы познакомиться с некоторыми основными навыками.

Существуют и другие методы для перемещения локальной базы данных в базу данных SQL. При наличии существующих баз данных или загруженных учебных баз данных попробуйте применить следующие альтернативные подходы.

-   [Перенос баз данных в базу данных SQL][Перенос баз данных в базу данных SQL]
-   [Копирование баз данных в базу данных SQL][Копирование баз данных в базу данных SQL]
-   [Развертывание базы данных SQL Server на виртуальной машине Azure][Развертывание базы данных SQL Server на виртуальной машине Azure]

  [Шаг 1: создание учетной записи Microsoft Azure]: #Subscribe
  [Шаг 3: настройка брандмауэра]: #ConfigFirewall
  [Шаг 4: добавление данных и схемы с помощью сценария Transact-SQL]: #AddData
  [Шаг 5: создание схемы]: #createschema
  [Шаг 6: добавление данных]: #insertData
  [Шаг 7: запрос данных для примера и системных данных для базы данных SQL на портале управления]: #QueryDBSysData
  [Шаг 8: создание учетных данных для входа в базу данных и назначение разрешений]: #DBLogin
  [Шаг 9: подключение из других приложений]: #ClientConnection
  []: http://azure.microsoft.com
  [Портал управления]: http://manage.windowsazure.com
  [Область навигации]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
  [1]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
  [2]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
  [3]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
  [Брандмауэр базы данных SQL]: http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-ru-ru.aspx
  [4]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
  [статьи MSDN]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee621790.aspx "статьи MSDN"
  [5]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
  [CREATE TABLE]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336258.aspx
  [ALTER TABLE]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336286.aspx
  [INSERT]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336284.aspx
  [6]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
  [7]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
  [8]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
  [9]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
  [PowerPivot для Excel]: http://go.microsoft.com/fwlink/?LinkId=396969
  [Управление базами данных SQL с помощью среды SQL Server Management Studio]: http://www.azure.microsoft.com/ru-ru/documentation/articles/sql-database-manage-azure-ssms/
  [Учебник. Написание инструкций Transact-SQL]: http://msdn.microsoft.com/ru-ru/library/ms365303.aspx
  [Перенос баз данных в базу данных SQL]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee730904.aspx
  [Копирование баз данных в базу данных SQL]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ff951624.aspx
  [Развертывание базы данных SQL Server на виртуальной машине Azure]: http://msdn.microsoft.com/ru-ru/library/dn195938(v=sql.120).aspx
