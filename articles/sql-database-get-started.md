<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="Создание и подготовка" pageTitle="Начало работы с базой данных SQL — Azure" metaKeywords="" description="Начало работы с созданием и управлением базами данных SQL в Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Начало работы с базой данных SQL Azure" authors=""  solutions="" writer="" manager="" editor=""  />





#Начало работы с базой данных SQL Azure

В этом учебном курсе описываются основы управления базой данных SQL Azure с использованием только портала управления Azure. Если вы не знакомы с администрированием базы данных, можно выполнить эти занятия для приобретения основных навыков. Потребуется около 30 минут.
 

Учебный курс не предполагает наличия опыта работы с SQL Server или базой данных SQL Azure. После прохождения учебного курса у вас появится пример базы данных в Azure и понимание того, как выполнять основные административные задачи с помощью портала управления.

Вы создадите и подготовите пример базы данных в Azure, систему запросов и пользовательские данные, используя Excel и другие приложения.


##Оглавление##

* [Шаг 1. Создание учетной записи Azure](#Subscribe)
* [Шаг 2. Подключение к Azure и создание базы данных](#Connect)
* [Шаг 3. Настройка брандмауэра](#ConfigFirewall)
* [Шаг 4. Добавление данных и схемы с помощью скрипта Transact-SQL](#AddData)
* [Шаг 5. Создание схемы](#createschema)
* [Шаг 6. Добавление данных](#insertData)
* [Шаг 7. Пример запроса и системные данные для базы данных SQL на портале управления](#QueryDBSysData)
* [Шаг 8. Создание учетных данных для входа в базу данных и назначение разрешений](#DBLogin)
* [Шаг 9. Подключение из других приложений](#ClientConnection)
* [Шаг 10. Настройка синхронизации данных SQL](#ConfigureDataSync)


<h2><a id="Subscribe"></a>Шаг 1. Создание учетной записи Azure</h2>

1. Откройте веб-браузер и перейдите на страницу [http://www.windowsazure.com](http://www.windowsazure.com).
Чтобы начать работу с бесплатной учетной записью, щелкните бесплатное пробное использование в правом верхнем углу и следуйте инструкциям.

2. Ваша учетная запись создана. Вы готовы начать работу.


<h2><a id="Connect"></a>Шаг 2. Подключение к Azure и создание базы данных</h2>


1. Выполните вход на [портал управления](http://manage.windowsazure.com). Должна появиться область навигации, выглядящая следующим образом. 

	![рисунок 1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2. В нижней части страницы щелкните **Создать**. При нажатии кнопки **Создать** на экране появляется список, показывающий, что можно создать.

3. Щелкните **База данных SQL**, затем выберите **Настраиваемое создание**. 

	![рисунок 2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)

	Выбор этого варианта позволяет одновременно создать новый сервер, с вами в качестве администратора. Будучи системным администратором, можно выполнить дополнительные задачи, включая подключение к порталу управления для базы данных SQL, которое будет выполнять позже в данном учебном курсе.  

4.  При нажатии кнопки **Настраиваемое создание** появляется страница настроек базы данных. Эта страница предназначена для ввода основной информации, используемой для создания на сервере пустой базы данных. Добавление таблиц и данных будет выполнено позднее. 

	Заполните страницу настроек базы данных следующим образом:

	![рисунок 3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)

	* Введите **School** в качестве имени базы данных. 

	* Используйте для выпуска, максимального размера и параметров сортировки значения по умолчанию. 

	* Выберите **Создать сервер базы данных SQL** При выборе создания нового сервера появляется вторая страница, которая будет использоваться для задания учетной записи администратора и региона. 

	* Закончив, нажмите кнопку со стрелкой, чтобы перейти к следующей странице.


5. Введите настройки сервера следующим образом: 

	![рисунок 4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)

	* Введите имя администратора одним словом, без пробелов. Для проверки удостоверения пользователя база данных SQL использует проверку подлинности SQL с помощью шифрованного подключения. Будет создана новая учетная запись для проверки подлинности SQL Server, обладающая разрешениями администратора и использующая введенное имя. Имя администратора не может быть именем пользователя Windows, при этом оно не должно быть идентификатором Windows Live ID. Проверка подлинности Windows для базы данных SQL не поддерживается.

	* Введите надежный пароль, который будет содержать более восьми символов, использовать сочетание значений в верхнем и нижнем регистре, а также цифры и знаки.

	* Выберите регион. Регион определяет географическое расположение сервера. Изменение региона достаточно трудоемко, поэтому выберите регион, имеющий смысл для этого сервера. Выберите местоположение, максимально близкое к вам и вашим пользователям. Хранение приложения и базы данных Azure в том же регионе снижает расходы на пропускную способности и задержку данных.

	* Проследите, чтобы флажок **Разрешить службам Azure доступ к этому серверу** остался установленным, чтобы можно было подключиться к этой базе данных, используя портал управления для базы данных SQL, Excel в Office 365 или службу отчетов SQL Reporting в Azure.

	* Закончив, щелкните значок галочки внизу страницы.

	Обратите внимание, что имя сервера не было задано. Так как сервер базы данных SQL должен быть доступен по всему миру, база данных SQL при создании сервера настраивает соответствующие записи DNS. Созданное имя обеспечивает отсутствие конфликтов имен с другими записями DNS. Невозможно изменить имя сервера баз данных SQL.

	На следующем шаге будет настроен брандмауэр, чтобы приложениям, запущенным на компьютере, был разрешен доступ к базам данных на сервере баз данных SQL.



<h2><a id="ConfigFirewall"></a>Шаг 3. Настройка брандмауэра</h2>

Для разрешения подключений через брандмауэр понадобится ввести данные на странице сервера.

**Примечание.** Служба базы данных SQL доступна только с TCP-портом 1433, используемым протоколом TDS, поэтому убедитесь, что брандмауэр в сети и на локальном компьютере разрешает исходящий трафик TCP для порта 1433. Дополнительные сведения см. в разделе [Брандмауэр базы данных SQL](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx)


1. Слева в области навигации щелкните **Базы данных SQL**.

2. В верхней части страницы щелкните **Серверы**. Затем выберите только что созданный сервер, чтобы появилась белая стрелка вправо. Щелкните стрелку, чтобы открыть страницу сервера.

	![рисунок 5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3. На странице сервера выберите **Настроить**, чтобы открыть параметры конфигурации брандмауэра, и определите следующее правило: 

	![рисунок 6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)


	* Скопируйте текущий IP-адрес клиента. Это IP-адрес, прослушиваемый вашим маршрутизатором или прокси-сервером. База данных SQL определяет IP-адрес, используемый текущим подключением, чтобы можно создать правило брандмауэра для принятия запросов на подключение от этого устройства. 
	
	* Вставьте этот IP-адрес в начало и конец диапазона. Позже в случае ошибки подключения, сообщающей о слишком узком диапазоне, можно изменить это правило, расширяя диапазон.

	* Введите имя для правила брандмауэра, например, имя компьютера или компании.

	* Щелкните значок галочки, чтобы сохранить правило.

	После сохранения правила страница будет похожей на следующий снимок экрана.

	![рисунок 7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)


4. Чтобы завершить шаг, нажмите кнопку **Сохранить** в нижней части страницы. Если кнопка **Сохранить** не видна, обновите страницу браузера.

Теперь у вас есть сервер баз данных SQL в Azure, правило брандмауэра, разрешающее доступ к серверу, объект базы данных и учетная запись администратора. Далее для запуска скрипта Transact-SQL с целью создания предварительно определенной базы данных будет использовано окно запроса в портале управления для базы данных SQL.

С ростом навыков захочется исследовать дополнительные способы создания базы данных, в том числе программные подходы или конструктор в SQL Server Data Tools. При наличии существующей базы данных SQL Server, работающей на локальном сервере баз данных, несложно перенести эту базу данных на только что настроенный сервер Azure. Воспользуйтесь ссылками в конце этого учебного курса, чтобы узнать, как это делается. 



<h2><a id="AddData"></a>Шаг 4. Добавление данных и схемы с помощью скрипта Transact-SQL</h2>

На этом шаге выполняются два скрипта. Первый из них создает схему, определяющую таблицы, столбцы и связи. Второй скрипт добавляет данные. Каждое действие выполняется независимо друг от друга в отдельном подключении. При наличии опыта создания баз данных в SQL Server можно заметить ряд различий по сравнению с базой данных SQL, одним из которых является необходимость выполнения команд CREATE и INSERT в разных пакетах. База данных SQL налагает это требование, чтобы свести к минимуму атаки на данные во время их передачи. 

**Примечание.** Схема и значения данных взяты из этой [статьи MSDN](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee621790.aspx "MSDN article") и изменены для работы с базой данных SQL.

1. Перейти на главную страницу. На [портале управления](http://manage.windowsazure.com), база данных **School** появляется в списке элементов на главной странице. 

	![рисунок 8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)


2. Щелкните **School**, чтобы появилась белая стрелка вправо. Щелкните стрелку, чтобы открыть страницу базы данных.

	![рисунок 9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3. В нижней части страницы нажмите кнопку **Управление**. Если она отсутствует, обновите окно браузера. Открывается портал управления для базы данных SQL. Этот портал не связан с порталом управления Azure. Он будет использоваться для выполнения запросов и команд Transact-SQL. 
	
	![рисунок 10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)


4. Введите имя и пароль администратора. Это имя учетной записи администратора, указанное при создании сервера.

	![рисунок 11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)


5. Нажмите кнопку **Создать запрос** на портале управления для базы данных SQL. В рабочей области открывается пустое окно запроса. На следующем шаге в это окно будут последовательно копироваться ряд предопределенных скриптов, добавляющих структуры и данные в пустую базу данных.

	![рисунок 12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)
	 

<h2><a id="createschema"></a>Шаг 5. Создание схемы</h2>

На этом шаге с помощью следующего сценария будет создана схема. Скрипт сначала проверяет, нет ли уже таблицы с тем же именем, чтобы убедиться в отсутствии конфликта имен, и создает таблицу с помощью инструкции [CREATE TABLE](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336258.aspx). Далее, этот сценарий использует инструкцию [ALTER TABLE](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336286.aspx), чтобы определить первичный ключ и связи таблицы.

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



<h2><a id="insertData"></a>Шаг 6. Добавление данных</h2>

Открыть новое окно запроса и вставьте в него следующий скрипт. Выполните этот скрипт, чтобы вставить данные. Этот скрипт использует инструкцию [INSERT](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336284.aspx), чтобы добавить значения в каждый столбец.

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
	-- Insert data into the Department table.
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
	GO
	-- Insert data into the Course table.
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1050, 'Chemistry', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1061, 'Physics', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1045, 'Calculus', 4, 7);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2030, 'Poetry', 2, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2021, 'Composition', 3, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2042, 'Literature', 4, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4022, 'Microeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4041, 'Macroeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4061, 'Quantitative', 2, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (3141, 'Trigonometry', 4, 7);
	GO
	-- Insert data into the OnlineCourse table.
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2030, 'http://www.fineartschool.net/Poetry');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2021, 'http://www.fineartschool.net/Composition');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
	--Insert data into OnsiteCourse table.
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1050, '123 Smith', 'MTWH', '11:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1061, '234 Smith', 'TWHF', '13:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1045, '121 Smith','MWHF', '15:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4061, '22 Williams', 'TH', '11:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (2042, '225 Adams', 'MTWH', '11:00');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4022, '23 Williams', 'MWF', '9:00');
	-- Insert data into the CourseInstructor table.
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1050, 1);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1061, 31);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1045, 5);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2030, 4);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2021, 27);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2042, 25);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4022, 18);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4041, 32);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4061, 34);
	GO
	--Insert data into the OfficeAssignment table.
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (1, '17 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (4, '29 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (5, '37 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (18, '143 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (25, '57 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (27, '271 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (31, '131 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (32, '203 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (34, '213 Smith');
	-- Insert data into the StudentGrade table.
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 2, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 2, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 3, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 3, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 6, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 6, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 7, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 7, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 9, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 10, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 11, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 15, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 16, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 17, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 19, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 20, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 21, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 22, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 22, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 22, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 23, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 23, 1.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 24, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 25, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 26, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 26, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 27, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 28, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 28, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 29, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 30, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 30, 4);
	GO
</pre></div>


<h2><a id="QueryDBSysData"></a>Шаг 7. Пример запроса и системные данные для базы данных SQL на портале управления</h2>

Чтобы проверить свою работу, выполните запрос, возвращающий только что введенные данные. Можно также выполнить встроенные хранимые процедуры и использовать представления управления данными, содержащие сведения о базах данных, работающих на сервере баз данных SQL.

<h4><a id="QueryDB"></a>Запрос части данных для примера</h4>

Скопируйте в новое окно запроса и выполните следующий скрипт Transact-SQL, чтобы получить некоторые из только что добавленных данных.


<div style="width:auto; height:auto; overflow:auto"><pre>
	SELECT
		Course.Title as "Course Title"
  		,Department.Name as "Department"
  		,Person.LastName as "Instructor"
  		,OnsiteCourse.Location as "Location"
  		,OnsiteCourse.Days as "Days"
  		,OnsiteCourse.Time as "Time"
	FROM
 	 Course
 	 INNER JOIN Department
  	  ON Course.DepartmentID = Department.DepartmentID
 	 INNER JOIN CourseInstructor
 	   ON Course.CourseID = CourseInstructor.CourseID
 	 INNER JOIN Person
 	   ON CourseInstructor.PersonID = Person.PersonID
 	 INNER JOIN OnsiteCourse
		ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
</pre></div>

Появившийся результирующий набор должен быть похож на следующую иллюстрацию.

![рисунок 13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)


<h4><a id="QuerySys"></a>Запрос системных данных</h4>

Для получения информации с сервера также можно использовать системные представления и встроенные хранимые процедуры. Для целей этого учебного курса будет задействовано лишь несколько команд.

Выполните следующую команду, чтобы узнать, какие базы данных доступны на сервере. 

	SELECT * FROM sys.databases  

Выполните эту команду для получения списка пользователей, подключенных к серверу в данный момент.

	SELECT user_name(),suser_sname()

Выполните эту хранимую процедуру для возвращения списка объектов в базе данных **School**.

	EXEC SP_help

Не закрывайте подключение портала к базе данных **School**. Оно снова понадобится через несколько минут.



<h2><a id="DBLogin"></a>Шаг 8. Создание учетных данных для входа в базу данных и назначение разрешений</h2>

В базе данных SQL можно создать учетные записи и предоставить разрешения, используя Transact-SQL. В этом занятии с помощью Transact-SQL можно будет выполнить три действия: создать учетную запись проверки подлинности SQL Server, создать пользователя базы данных и предоставить разрешения с помощью добавления роли.

Учетная запись проверки подлинности SQL Server используется для подключения к серверу. Все пользователи для доступа к базе данных на сервере баз данных SQL указывает имя и пароль учетной записи проверки подлинности SQL Server. 

Чтобы создать учетную запись, сначала необходимо подключиться к базе данных **master**.

<h4><a id="CreateLogin"></a>Создание учетной записи проверки подлинности SQL Server</h4>

1. На [портале управления](http://manage.windowsazure.com) выберите **Базы данных SQL**, щелкните **Серверы**, выберите сервер и щелкните белую стрелку, чтобы открыть страницу сервера. 

	![рисунок 5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2. На странице быстрого запуска щелкните **Управление сервером**, чтобы открыть новое подключение к порталу управления для базы данных SQL. 

3. Введите имя и пароль администратора. Это имя учетной записи администратора, указанное при создании сервера.

	![рисунок 20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)


4. Портал управления базой данных SQL откроется в новом окне браузера. Нажмите вверху кнопку **Выбрать базу данных** и щелкните **master**

	![рисунок 14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5. Если на странице появляется сообщение об ошибке, похожее на следующее, игнорируйте его. Нажмите кнопку **Создать запрос**, чтобы открыть окно запроса, позволяющее выполнять команды Transact-SQL для базы данных **master**.

	![рисунок 15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6. Скопируйте следующую команду и вставьте ее в окно запроса.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7. Выполните эту команду, чтобы создать новую учетную запись SQL Server с именем "SQLDBLogin".


<h4><a id="CreateDBuser"></a>Создание пользователя базы данных и назначение разрешений</h4>

После создания учетной записи проверки подлинности SQL Server, следующим шагом является назначение этой учетной записи базы данных и уровней разрешений. С этой целью для каждой базе данных создается **пользователь базы данных**.

1. Вернитесь на страницу портала управления базой данных SQL, которая подключается к базе данных **School**. Если окно браузера было закрыто, откройте новое подключение к базе данных **School**, используя действия из предыдущего занятия, "Добавление данных и схемы с помощью скрипта Transact-SQL". 

	На странице портала управления базой данных SQL в верхнем левом углу появляется имя базы данных **School**.

	![рисунок 12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2. Нажмите кнопку **Создать запрос**, чтобы открыть новое окно запроса, и скопируйте в него следующую инструкцию. 

	    CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3. Выполните скрипт. Этот School создает нового пользователя базы данных на основе учетной записи.

	Далее, нужно назначить разрешения, используя роль db_datareader. Пользователи базы данных, которым назначена эта роль, могут читать все данные из всех пользовательских таблиц в базе данных. 

4. Откройте новое окно запроса, затем введите и выполните следующую инструкцию. Эта инструкция вызывает встроенную хранимую процедуру, которая назначает роль db_datareader только что созданному новому пользователю. 

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

	Теперь создана новая учетная запись проверки подлинности SQL Server с правами "только чтение" для базы данных **School**. С помощью этих действий можно создать другие учетные записи проверки подлинности SQL Server, чтобы разрешить различные уровни доступа к данным.




<h2><a id="ClientConnection"></a>Шаг 9. Подключение из других приложений</h2>

Теперь, когда есть рабочая база данных, можно подключиться к ней из книги Excel.

<h4>Подключение из Excel</h4>


Если на компьютере установлено приложение Excel 2010, для подключения к учебной базе данных можно использовать следующие действия.

1. В Microsoft Excel, на вкладке "Данные" нажмите кнопку **Из других источников** и выберите **Из SQL Server**

2. В мастере подключения к данным введите полное доменное имя своего сервера базы данных SQL, а затем учетную запись проверки подлинности SQL Server, обладающую разрешением на доступ к этой базе данных. 

  Имя сервера можно найти на странице **Базы данных**, в разделе **Быстрые ссылки**. Имя сервера также можно найти на портале управления Azure, база данных SQL, страница "Сервер", Панель мониторинга, **Управление URL-адресом**.

  Имя сервера состоит из последовательности букв и цифр, за которыми следует ".database.windows.net". Укажите это имя в мастере подключения к базе данных. При задании имени не следует добавлять префикс http:// или https://.

  Войдите, используя учетную запись проверки подлинности SQL Server. Для тестирования можно использовать учетную запись администратора, созданную при установке сервера. Для регулярного доступа к данным используйте пользователя базы данных, похожего на только что созданного.

	![рисунок 16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)


3.  На следующей странице выберите базы данных **School**, а затем выберите **Course**. Нажмите кнопку **Готово**

	![рисунок 17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

4. Откроется диалоговое окно "Импорт данных", предлагающее выбрать способ и место для импорта данных. Не меняя настроек по умолчанию, нажмите кнопку **ОК**

	![рисунок 19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)


5. На лисье должна появиться страница, аналогичная следующей. 
	
	![рисунок 18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

При использовании только Excel одновременно можно импортировать лишь одну таблицу. Лучше использовать надстройку PowerPivot для Excel, которая позволяет импортировать несколько таблиц и работать с ними, как с одним набором данных. Работа с PowerPivot выходит за рамки данного учебного курса, но дополнительные сведения можно найти на этом [веб-сайте Microsoft](http://www.microsoft.com/ru-ru/bi/powerpivot.aspx).

<h2><a id="ConfigureDataSync"></a>Шаг 10. Настройка синхронизации данных SQL</h2>

<h4>Синхронизация данных SQL</h4>

Теперь, после создания экземпляров базы данных SQL, можно использовать синхронизацию данных SQL, чтобы поддерживать синхронизацию важных данных для нескольких местоположений.

Синхронизация данных SQL — это компонент базы данных SQL, позволяющий синхронизировать выбранные данные, либо по расписанию, либо по требованию, без написания кода или скриптов. Синхронизация данных SQL поддерживает синхронизацию между экземплярами базы данных SQL или гибридными топологиями, содержащими базы данных SQL и экземпляры SQL Server.

Дополнительные сведения о синхронизации данных SQL см. в разделе [Начало работы с синхронизацией данных SQL](http://go.microsoft.com/fwlink/?LinkId=274959).

<h2><a id="NextSteps"></a>Следующие шаги</h2>

Теперь, познакомившись с базой данных SQL и порталами управления, можно попробовать другие средства и приемы, используемые администраторами баз данных SQL Server.

Чтобы активно управлять новой базой данных, рассмотрите возможность установки и использования среды SQL Server Management Studio. Management Studio — это главное средство администрирования для управления базами данных SQL Server, включая выполняемые в Azure. В среде Management Studio можно сохранять запросы для дальнейшего использования, добавлять новые таблицы и хранимые процедуры, а также отточить свое мастерство Transact-SQL в среде использования скриптов с широкими возможностями, включая проверку синтаксиса, Intellisense и шаблоны. Чтобы начать работу, следуйте инструкциям раздела [Управление базами данных SQL с помощью среды SQL Server Management Studio](http://www.windowsazure.com/ru-ru/develop/net/common-tasks/sql-azure-management/)

Хорошее владение запросами Transact-SQL и языком определения данных крайне важно для администраторов баз данных. Новичкам в языке Transact-SQL рекомендуется для начала использовать [Учебный курс. Написание инструкций Transact-SQL](http://msdn.microsoft.com/ru-ru/library/ms365303.aspx), чтобы познакомиться с некоторыми основными навыками.

Существуют и другие методы для перемещения локальной базы данных в базу данных SQL. При наличии существующих баз данных или загруженных учебных баз данных попробуйте применить следующие альтернативные подходы.

* [Перенос баз данных в базу данных SQL](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee730904.aspx)
* [Копирование баз данных в базу данных SQL](http://msdn.microsoft.com/ru-ru/library/windowsazure/ff951624.aspx)


























