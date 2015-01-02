<properties urlDisplayName="How to create and provision" pageTitle="Приступая к работе с базами данных SQL - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="jeffryg"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="jeffreyg" />


#Приступая к работе с базой данных SQL Microsoft Azure

В этом учебнике описываются основы управления базой данных SQL Microsoft Azure с использованием портала управления Azure. Если вы не знакомы с администрированием базы данных, можно выполнить эти занятия для приобретения основных навыков. Потребуется около 30 минут. 

Учебник не предполагает наличия опыта работы с SQL Server или базой данных SQL Azure. После прохождения учебника у вас появится пример базы данных в Azure и понимание того, как выполнять основные административные задачи с помощью портала управления.

Вы создадите и подготовите пример базы данных на платформе Azure, систему запросов и пользовательские данные, используя Excel.


##Оглавление

* [Шаг 1. Создание учетной записи Microsoft Azure](#Subscribe)
* [Шаг 2. Подключение к Azure и создание базы данных](#Subscribe)
* [Шаг 3. Настройка брандмауэра](#ConfigFirewall)
* [Шаг 4. Добавление данных и схемы с помощью сценария Transact-SQL](#AddData)
* [Шаг 5. Создание схемы](#createschema)
* [Шаг 6. Добавление данных](#insertData)
* [Шаг 7. Запрос данных для примера и системных данных для базы данных SQL на портале управления](#QueryDBSysData)
* [Шаг 8. Создание учетных данных для входа в базу данных и назначение разрешений](#DBLogin)
* [Шаг 9. Подключение из приложения](#ClientConnection)


<h2 id="Subscribe">Шаг 1. Создание учетной записи Microsoft Azure</h2>

1. Откройте браузер и перейдите по адресу [http://azure.microsoft.com](http://azure.microsoft.com).
Чтобы начать работу с бесплатной учетной записью, щелкните бесплатное пробное использование в правом верхнем углу и следуйте инструкциям.

2. Ваша учетная запись создана. Вы готовы начать работу.


<h2 id="Connect">Шаг 2. Подключение к Azure и создание базы данных</h2>


1. Войдите на [Портал управления](http://manage.windowsazure.com). Должна появиться область навигации, выглядящая следующим образом.

	![Navigation pane][Image1]

2. В нижней части страницы щелкните **Создать**. При нажатии кнопки **Создать** на экране появится список с данными о том, что можно создать.

3. Щелкните **База данных SQL**, затем выберите **Настраиваемое создание**. 

	![Navigation pane][Image2]

Выбор этого варианта позволяет одновременно создать новый сервер и базу данных SQL, с вами в качестве администратора. Будучи системным администратором, можно выполнить дополнительные задачи, включая подключение к порталу управления для базы данных SQL, которое будет выполнять позже в данном учебном курсе.  

4.  При нажатии кнопки **Настраиваемое создание** появится страница настроек базы данных. Эта страница предназначена для ввода основной информации, используемой для создания на сервере пустой базы данных SQL. Добавление таблиц и данных будет выполнено позднее. 

    Заполните страницу настроек базы данных следующим образом:

	![Navigation pane][Image3]

* Введите **School** в качестве имени базы данных. 

* Используйте для выпуска, максимального размера и параметров сортировки значения по умолчанию. 

* Выберите **Создать сервер базы данных SQL**. При выборе создания нового сервера появляется вторая страница, которая будет использоваться для задания учетной записи администратора и региона. 

* Закончив, нажмите кнопку со стрелкой, чтобы перейти к следующей странице.


7. Введите настройки сервера следующим образом: 

	![Navigation pane][Image4]

* Введите имя администратора одним словом, без пробелов. Для проверки удостоверения пользователя база данных SQL использует проверку подлинности SQL с помощью шифрованного подключения. Будет создана новая учетная запись для проверки подлинности SQL Server, обладающая разрешениями администратора и использующая введенное имя. Имя администратора не может быть именем пользователя Windows, при этом оно не должно быть идентификатором Live ID. Проверка подлинности Windows для базы данных SQL не поддерживается.

* Укажите надежный пароль, который содержит более восьми знаков, в том числе символы в верхнем и нижнем регистре, а также цифры и символы. Используйте всплывающую справку для получения информации о сложности пароля.

* Выберите регион. Регион определяет географическое расположение сервера. Изменение региона достаточно трудоемко, поэтому выберите регион, имеющий смысл для этого сервера. Выберите местоположение, максимально близкое к вам. Хранение приложения и базы данных Azure в том же регионе снижает расходы на пропускную способность и задержку данных.

* Проследите, чтобы флажок **Разрешить службам Azure доступ к этому серверу** остался установленным. Это даст возможность подключаться к этой базе данных, используя портал управления для базы данных SQL, Excel в Office 365 или службу Azure SQL Reporting.

* Закончив, щелкните флажок внизу страницы.

Обратите внимание, что имя сервера не было задано. Так как сервер базы данных SQL должен быть доступен по всему миру, база данных SQL при создании сервера настраивает соответствующие записи DNS. Созданное имя обеспечивает отсутствие конфликтов имен с другими записями DNS. Невозможно изменить имя сервера баз данных SQL.

Чтобы просмотреть имя сервера, на котором размещается только что созданная база данных **School**, щелкните **Базы данных SQL** в области навигации слева, затем выберите базу данных **School** в представлении списка **Базы данных SQL**. На странице **Быстрый запуск** перейдите вниз, чтобы просмотреть имя сервера.

На следующем шаге будет настроен брандмауэр, чтобы приложениям, запущенным на компьютере, был разрешен доступ к базам данных на сервере баз данных SQL.



<h2 id="ConfigFirewall">Шаг 3. Настройка брандмауэра</h2>

Для разрешения подключений через брандмауэр понадобится ввести данные на странице сервера.

**Примечание.** Служба базы данных SQL доступна только с TCP-портом 1433, используемым протоколом TDS, поэтому убедитесь, что брандмауэр в сети и на локальном компьютере разрешает исходящий трафик TCP для порта 1433. Дополнительную информацию см. в статье [Брандмауэр базы данных SQL](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-ru-ru.aspx).


1. Слева в области навигации щелкните **Базы данных SQL**.

2. В верхней части страницы щелкните **Серверы**. Затем щелкните только что созданный сервер, чтобы открыть страницу сервера.

3. Чтобы открыть настройки **Разрешенных IP-адресов**, на странице сервера щелкните **Настройка**, а затем **щелкните ссылку **Добавить в разрешенные IP-адреса. В результате будет создано новое правило брандмауэра, чтобы разрешить запросы подключения от маршрутизатора или прокси-сервера, прослушиваемые вашим устройством.

4. Вы можете создавать дополнительные правила брандмауэра, задавая имя правила, а также начальное и конечное значения диапазона IP-адресов.

5. Чтобы включить взаимодействия между этим сервером и другими службами Azure, щелкните **Да** для параметра **Службы Microsoft Azure**. 

7. Чтобы сохранить изменения, в нижней части страницы щелкните **СОХРАНИТЬ**.

6. После сохранения правила страница будет похожей на следующий снимок экрана.

	![Navigation pane][Image7]

Теперь у вас есть сервер баз данных SQL в Azure, правило брандмауэра, разрешающее доступ к серверу, объект базы данных и учетная запись администратора. Но у вас пока нет работающей базы данных, в которой можно выполнить запрос. Для этого ваша база данных должна иметь схему и фактические данные.

Так как в этом учебнике используются только имеющиеся в наличии инструменты, для запуска сценария Transact-SQL с целью создания предопределенной базы данных будет использовано окно запроса на портале управления для базы данных SQL.

С ростом навыков вам захочется исследовать дополнительные способы создания базы данных, в том числе программные подходы или рабочую область конструирования в SQL Server Data Tools. При наличии существующей базы данных SQL Server, работающей на локальном сервере баз данных, несложно перенести эту базу данных на только что настроенный сервер Azure. Воспользуйтесь ссылками в конце этого учебного курса, чтобы узнать, как это делается. 



<h2 id="AddData">Шаг 4. Добавление данных и схемы с помощью сценария Transact-SQL</h2>

На этом шаге выполняются два скрипта. Первый из них создает схему, определяющую таблицы, столбцы и связи. Второй скрипт добавляет данные. Каждое действие выполняется независимо друг от друга в отдельном подключении. При наличии опыта создания баз данных в SQL Server можно заметить ряд различий по сравнению с базой данных SQL, одним из которых является необходимость выполнения команд CREATE и INSERT в разных пакетах. База данных SQL налагает это требование, чтобы свести к минимуму атаки на данные во время их передачи. 

**Примечание.** Схема и значения данных взяты из этой [статьи MSDN](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee621790.aspx "MSDN article") и изменены для работы с базой данных SQL.

1. Перейти на главную страницу. На [портале управления](http://manage.windowsazure.com) в списке элементов на главной странице появится база данных **School**.

	![Navigation pane][Image8]

2. Щелкните базу данных **School**, чтобы выбрать ее, затем в нижней части страницы щелкните **Управление**. Открывается портал управления для базы данных SQL. Этот портал не связан с порталом управления Azure. Он будет использоваться для выполнения запросов и команд Transact-SQL.

3. Чтобы войти в базу данных **School**, введите имя пользователя и пароль администратора. Это имя учетной записи администратора, указанное при создании сервера.

4. На портале управления для базы данных SQL щелкните **Создать запрос** на ленте. В рабочей области откроется пустое окно запроса. На следующем шаге в это окно будут последовательно копироваться ряд предопределенных скриптов, добавляющих структуры и данные в пустую базу данных.



<h2 id="createschema">Шаг 5. Создание схемы</h2>

На этом шаге с помощью следующего сценария будет создана схема. Сценарий сначала проверяет, нет ли уже таблицы с тем же именем, чтобы убедиться в отсутствии конфликта имен, и создает таблицу с помощью инструкции [CREATE TABLE](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336258.aspx) . Далее, этот сценарий использует инструкцию [ALTER TABLE],(http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336286.aspx) чтобы определить первичный ключ и связи таблицы.

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



<h2 id="insertData">Шаг 6. Добавление данных</h2>

Открыть новое окно запроса и вставьте в него следующий скрипт. Выполните этот скрипт, чтобы вставить данные. Этот сценарий использует инструкцию [INSERT](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336284.aspx) для добавления значений в каждый столбец.

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


<h2 id="QueryDBSysData">Шаг 7. Запрос данных для примера и системных данных для базы данных SQL на портале управления</h2>

Чтобы проверить свою работу, выполните запрос, возвращающий только что введенные данные. Можно также выполнить встроенные хранимые процедуры и использовать представления управления данными, содержащие сведения о базах данных, работающих на сервере баз данных SQL.

<h4 id="QueryDB">Запрос части данных для примера</h4>

Скопируйте в новое окно запроса и выполните следующий скрипт Transact-SQL, чтобы получить некоторые из только что добавленных данных.


<div style="width:auto; height:auto; overflow:auto"><pre>
	SELECT * From Person
</pre></div>

Вы должны увидеть результирующий набор с 34 строками из таблицы Person, включая PersonID, LastName, FirstName, HireDate и EnrollmentDate.


<h4 id="QuerySys">Запрос системных данных</h4>

Для получения информации с сервера также можно использовать системные представления и встроенные хранимые процедуры. Для целей этого учебного курса будет задействовано лишь несколько команд.

Выполните следующую команду, чтобы узнать, какие базы данных доступны на сервере. 

	SELECT * FROM sys.databases  

Выполните эту команду для получения списка пользователей, подключенных к серверу в данный момент.

	SELECT user_name(),suser_sname()

Выполните эту хранимую процедуру, возвращающую список объектов в базе данных **School**.

	EXEC SP_help

Не закрывайте подключение портала к базе данных **School**. Оно снова понадобится через несколько минут.



<h2 id="DBLogin">Шаг 8. Создание учетных данных для входа в базу данных и назначение разрешений</h2>

В базе данных SQL можно создать учетные записи и предоставить разрешения, используя Transact-SQL. В этом занятии с помощью Transact-SQL вы сможете выполнить три действия:


1. создать учетную запись проверки подлинности SQL Server;
2. создать пользователя базы данных;
3. предоставить разрешения с помощью добавления роли.

Учетная запись проверки подлинности SQL Server используется для подключения к серверу. Все пользователи для доступа к базе данных на сервере баз данных SQL указывает имя и пароль учетной записи проверки подлинности SQL Server. 

Чтобы создать учетную запись, сначала необходимо подключиться к базе данных **master**.

<h4 id="CreateLogin">Создание учетной записи проверки подлинности SQL Server</h4>

1. На [портале управления](http://manage.windowsazure.com)выберите **Базы данных SQL**, щелкните **Серверы**, выберите сервер и щелкните белую стрелку, чтобы открыть
страницу сервера.

2. На странице быстрого запуска щелкните **Управление сервером**, чтобы открыть новое подключение к порталу управления для базы данных SQL. 

3. Выбирая базу данных для подключения, укажите **master**, затем войдите, используя свои имя пользователя и пароль. Это имя учетной записи администратора, указанное при создании сервера.

4. Портал управления базой данных SQL откроется в новом окне браузера, и вы будете подключены к **master**.

5. Если на странице появляется сообщение об ошибке, похожее на следующее, игнорируйте его. Нажмите кнопку **Создать запрос**, чтобы открыть окно запроса, позволяющее выполнять команды Transact-SQL для базы данных **master**.

	![Navigation pane][Image15]

6. Скопируйте следующую команду и вставьте ее в окно запроса.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7. Выполните эту команду, чтобы создать новую учетную запись SQL Server с именем "SQLDBLogin".


<h4 id="CreateDBuser">Создание пользователя базы данных и назначение разрешений</h4>

После создания учетной записи проверки подлинности SQL Server, следующим шагом является назначение этой учетной записи базы данных и уровней разрешений. С этой целью для каждой базы данных создается **пользователь базы данных**.

1. Вернитесь на страницу портала управления базой данных SQL, которая подключается к базе данных **School**. Если окно браузера было закрыто, откройте новое подключение к базе данных **School**, выполнив действия из предыдущего занятия "Добавление данных и схемы с помощью сценария Transact-SQL". 

	На странице портала управления базой данных SQL в верхнем левом углу появится имя базы данных **School**.

	![Navigation pane][Image12]

2. Нажмите кнопку **Создать запрос**, чтобы открыть новое окно запроса, и скопируйте в него следующую инструкцию. 

	    CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3. Выполните скрипт. Этот School создает нового пользователя базы данных на основе учетной записи.

   Далее, нужно назначить разрешения, используя роль db_datareader. Пользователи базы данных, которым назначена эта роль, могут читать все данные из всех пользовательских таблиц в базе данных. 

4. Откройте новое окно запроса, затем введите и выполните следующую инструкцию. Эта инструкция вызывает встроенную хранимую процедуру, которая назначает роль db_datareader только что созданному новому пользователю. 

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

Теперь создана новая учетная запись аутентификации SQL Server с правами "только чтение" для базы данных **School**. С помощью этих действий можно создать другие учетные записи проверки подлинности SQL Server, чтобы разрешить различные уровни доступа к данным.


<h2 id="ClientConnection">Шаг 9. Подключение из приложения</h2>

Для подключения к базе данных SQL Microsoft Azure можно использовать ADO.NET. В отличие от локального подключения здесь необходимо учитывать регулирование или другие ошибки службы, которые могут привести к прерыванию подключения или временной блокировке новых подключений. Это состояние называется временной ошибкой. Для управления временными ошибками необходимо реализовать стратегию повторов. При подключении к базе данных SQL Azure в модуле [Transient Fault Handling Application Block](http://go.microsoft.com/fwlink/?LinkId=519356), входящем в состав библиотеки Enterprise Library 6 (апрель 2013 г.), применяются стратегии обнаружения, позволяющие определить состояние временной ошибки.

<h4>Образец консольного приложения C#</h4>


	static void Main(string[] args)
    {
        //NOTE: Use appropriate exception handling in a production application.

        //Replace
        //  builder["Server"]: {servername} = Your Azure SQL Database server name
        //  builder["User ID"]: {username}@{servername} = Your Azure SQL Database user name and server name
        //  builder["Password"]: {password} = Your Azure SQL Database password

        System.Data.SqlClient.SqlConnectionStringBuilder builder = new System.Data.SqlClient.SqlConnectionStringBuilder();
        builder["Server"] = "{servername}";
        builder["User ID"] = "{username}@{servername}";
        builder["Password"] = "{password}";

        builder["Database"] = "AdventureWorks2012";
        builder["Trusted_Connection"] = false;
        builder["Integrated Security"] = false;
        builder["Encrypt"] = true;

        //1. Define an Exponential Backoff retry strategy for Azure SQL Database throttling (ExponentialBackoff Class). An exponential back-off strategy will gracefully back off the load on the service.
        int retryCount = 4;
        int minBackoffDelayMilliseconds = 2000;
        int maxBackoffDelayMilliseconds = 8000;
        int deltaBackoffMilliseconds = 2000;

        ExponentialBackoff exponentialBackoffStrategy = 
          new ExponentialBackoff("exponentialBackoffStrategy",
              retryCount,
              TimeSpan.FromMilliseconds(minBackoffDelayMilliseconds), 
              TimeSpan.FromMilliseconds(maxBackoffDelayMilliseconds),
              TimeSpan.FromMilliseconds(deltaBackoffMilliseconds));

        //2. Set a default strategy to Exponential Backoff.
        RetryManager manager = new RetryManager(new List<RetryStrategy>
        {  
            exponentialBackoffStrategy 
        }, "exponentialBackoffStrategy");

        //3. Set a default Retry Manager. A RetryManager provides retry functionality, or if you are using declarative configuration, you can invoke the RetryPolicyFactory.CreateDefault
            RetryManager.SetDefault(manager);

        //4. Define a default SQL Connection retry policy and SQL Command retry policy. A policy provides a retry mechanism for unreliable actions and transient conditions.
        RetryPolicy retryConnectionPolicy = manager.GetDefaultSqlConnectionRetryPolicy();
        RetryPolicy retryCommandPolicy = manager.GetDefaultSqlCommandRetryPolicy();

        //5. Create a function that will retry the connection using a ReliableSqlConnection.
        retryConnectionPolicy.ExecuteAction(() =>
        {
            using (ReliableSqlConnection connection = new ReliableSqlConnection(builder.ConnectionString))
            {
                connection.Open();

                IDbCommand command = connection.CreateCommand();
                command.CommandText = "SELECT Name FROM Production.Product";

                //6. Create a function that will retry the command calling ExecuteCommand() from the ReliableSqlConnection
                retryCommandPolicy.ExecuteAction(() =>
                {
                    using (IDataReader reader = connection.ExecuteCommand<IDataReader>(command))
                    {
                        while (reader.Read())
                        {
                            string name = reader.GetString(0);

                            Console.WriteLine(name);
                        }
                    }
                });                  
            }
        });

        Console.ReadLine();
    }



<h2 id="NextSteps">Дальнейшие действия</h2>

Теперь, познакомившись с базой данных SQL и порталами управления, можно попробовать другие средства и приемы, используемые администраторами баз данных SQL Server.

Чтобы активно управлять новой базой данных, рассмотрите возможность установки и использования среды SQL Server Management Studio. Management Studio - это главное средство администрирования для управления базами данных SQL Server, включая выполняемые в Azure. В среде Management Studio можно сохранять запросы для дальнейшего использования, добавлять новые таблицы и хранимые процедуры, а также отточить свое мастерство Transact-SQL в среде использования скриптов с широкими возможностями, включая проверку синтаксиса, Intellisense и шаблоны. Чтобы начать работу, следуйте указаниям в статье [Managing SQL Databases Using SQL Server Management Studio] (Управление базами данных SQL с помощью среды SQL Server Management Studio)(http://www.azure.microsoft.com/ru-ru/documentation/articles/sql-database-manage-azure-ssms/).

Хорошее владение запросами Transact-SQL и языком определения данных крайне важно для администраторов баз данных. Если вы новичок в языке Transact-SQL, рекомендуем для начала пройти [учебник "Составление инструкций Transact-SQL"].(http://msdn.microsoft.com/ru-ru/library/ms365303.aspx) Изучив этот учебник, вы получите необходимые базовые навыки.

Существуют и другие методы для перемещения локальной базы данных в базу данных SQL. При наличии существующих баз данных или загруженных учебных баз данных попробуйте применить следующие альтернативные подходы.

* [Перенос баз данных в базу данных SQL](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee730904.aspx)
* [Копирование баз данных в базу данных SQL](http://msdn.microsoft.com/ru-ru/library/windowsazure/ff951624.aspx)
* [Развертывание базы данных SQL Server на виртуальной машине Azure](http://msdn.microsoft.com/ru-ru/library/dn195938(v=sql.120)(ASPX)



[Image1]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
[Image2]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
[Image3]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
[Image4]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
[Image5]: ./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG
[Image6]: ./media/sql-database-get-started/6DBConfigFirewall_SQLTut.PNG
[Image7]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
[Image8]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
[Image9]: ./media/sql-database-get-started/9dblistschool_SQLTut.PNG
[Image10]: ./media/sql-database-get-started/10dbportalmanagebutton_SQLTut.PNG
[Image11]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
[Image12]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
[Image13]: ./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG
[Image14]: ./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG
[Image15]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
[Image16]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
[Image17]: ./media/sql-database-get-started/17ExcelSelect_SQLTut.PNG
[Image18]: ./media/sql-database-get-started/18ExcelTable_SQLTut.PNG
[Image19]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
[Image20]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG


<!--HONumber=35_1-->
