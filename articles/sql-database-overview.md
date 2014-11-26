

# Управление базами данных SQL в Azure

В этом руководстве показано, как выполнять административные задачи для логических серверов и экземпляров баз данных в базе данных SQL Azure. 

##Что такое база данных SQL?

База данных SQL предоставляет службы управления реляционной базой данных в Azure и основана на технологии SQL Server. С базой данных SQL можно легко подготовить и развернуть экземпляры баз данных и воспользоваться преимуществами распределенного центра обработки данных, который предоставляет преимущества доступности, масштабируемости и безопасности корпоративного класса вместе со встроенной защитой данных и возможностями самовосстановления. 

##Оглавление

* [Вход в Azure][Вход в Azure] 
* [Настройка базы данных SQL][Настройка базы данных SQL]
* [Подключение с помощью среды Management Studio][Подключение с помощью среды Management Studio]
* [Развертывание базы данных в Azure][Развертывание базы данных в Azure]
* [Добавление имен входа и пользователей][Добавление имен входа и пользователей]
* [Масштабирование решения базы данных SQL][Масштабирование решения базы данных SQL]
* [Мониторинг логических серверов и экземпляров баз данных][Мониторинг логических серверов и экземпляров баз данных]
* [Дальнейшие действия][Дальнейшие действия]


<h2><a id="PreReq1"></a>Вход в Azure</h2>

База данных SQL предоставляет службы хранения реляционных данных, доступа к ним и управления ими в Azure. Для ее использования необходима подписка Azure.

1. Откройте веб-браузер и перейдите по адресу [http://www.windowsazure.com](http://www.windowsazure.com). Чтобы начать работу с бесплатной учетной записью, щелкните бесплатное пробное использование в правом верхнем углу и следуйте инструкциям.

2. Ваша учетная запись создана. Вы готовы начать работу.


<h2><a id="PreReq2"></a>Создание и настройка базы данных SQL</h2>

Далее будут пошагово выполнены создание и настройка логического сервера. На портале управления Azure (предварительная версия) исправленные рабочие процессы позволяют сначала создать базу данных, а затем создать сервер. 

В этом руководстве сначала будет создан сервер. Этот подход может быть предпочтительнее при наличии существующих баз данных SQL Server, которые планируется отправить в Azure.

<h3><a id="createsrvr"></a>Создание логического сервера</h3>

1. Выполните вход на сайт [http://www.windowsazure.com](http://www.windowsazure.com).

2. Щелкните **База данных SQL**, а затем выберите **СЕРВЕРЫ** на главной странице базы данных SQL.

4. В нижней части страницы нажмите кнопку **Добавить**. 

5. В разделе "Параметры сервера" введите имя администратора одним словом, без пробелов. 

	База данных SQL использует проверку подлинности SQL с помощью шифрованного подключения. Будет создана новая учетная запись для проверки подлинности SQL Server, назначенная фиксированной серверной роли sysadmin и использующая введенное имя. 

	Именем входа не может быть адрес электронной почты, учетная запись пользователя Windows или идентификатор Windows Live ID. Для базы данных SQL не поддерживается ни проверка подлинности на основе утверждений, ни проверка подлинности Windows.

6. Введите надежный пароль, который будет содержать более восьми знаков, в том числе значения в верхнем и нижнем регистре, а также цифры и символы.

7. Выберите регион. Регион определяет географическое расположение сервера. Изменение региона достаточно трудоемко, поэтому выберите регион, имеющий смысл для этого сервера. Выберите местоположение, максимально близкое к вам. Хранение приложения и базы данных Azure в том же регионе снижает расходы на пропускную способность и задержку данных.

8. Убедитесь, что установлен флажок **Разрешить службы**, чтобы можно было подключиться к этой базе данных, используя портал управления для базы данных SQL, службы хранилища и другие службы Azure. 

9. Закончив, щелкните значок галочки внизу страницы.

Обратите внимание, что имя сервера не было задано. База данных SQL автоматически создает имя сервера, гарантируя отсутствие повторяющихся записей DNS. Имя сервера является буквенно-цифровой строкой, состоящей из 10 знаков. Невозможно изменить имя сервера баз данных SQL.

На следующем шаге будет настроен брандмауэр, чтобы разрешить подключение для приложений, работающих в вашей сети.

<h3><a id="configFWLogical"></a>Настройка брандмауэра для логического сервера</h3>

1. Выберите **Базы данных SQL**, **Серверы**, а затем только что созданный сервер.

2. Нажмите кнопку **Настройка**. 

3. Скопируйте текущий IP-адрес клиента. В случае подключения из сети это IP-адрес, прослушиваемый вашим маршрутизатором или прокси-сервером. База данных SQL определяет IP-адрес, используемый текущим подключением, чтобы можно создать правило брандмауэра для принятия запросов на подключение от этого устройства. 

4. Вставьте этот IP-адрес в начало и конец диапазона. Позже в случае ошибки подключения, сообщающей о слишком узком диапазоне, можно изменить это правило, расширяя диапазон.

	Если клиентские компьютеры используют динамически назначаемые IP-адреса, необходимо указать диапазон, достаточно широкий для включения IP-адресов, назначенных компьютерам в сети. Начните с узкого диапазона и расширяйте его только в случае необходимости.

5. Введите имя для правила брандмауэра, например имя компьютера или компании.

6. Щелкните значок галочки, чтобы сохранить правило.

7. Чтобы завершить этот шаг, нажмите кнопку **Сохранить**. Если кнопка **Сохранить** не видна, обновите страницу браузера.

Теперь у вас есть логический сервер, правило брандмауэра, разрешающее входящие подключения с вашего IP-адреса, и учетная запись администратора. На следующем шаге для выполнения оставшихся действий по настройке вы переключитесь на локальный компьютер.

**Примечание.** Только что созданный логический сервер является временным и будет динамически размещаться на физических серверах в центре обработки данных. При удалении сервера следует учитывать, что это действие является необратимым. Не забудьте создать резервную копию всех баз данных, которые впоследствии понадобится загрузить на сервер. 



<h2><a id="PreReq3"></a>Подключение с помощью среды Management Studio</h2>

Среда Management Studio является средством администрирования, которое позволяет управлять несколькими экземплярами SQL Server и серверами в одной рабочей области. Если у вас уже есть локальный экземпляр SQL Server, можно открыть подключение к локальному экземпляру и логическому серверу в Azure для параллельного выполнения задач.

Среда Management Studio включает в себя функции, которые в настоящее время недоступны на портале управления, например проверку синтаксиса и возможность сохранять скрипты и именованные запросы для повторного использования. База данных SQL является просто конечной точкой потока табличных данных (TDS). Для операций с базой данных SQL подходят любые средства, которые работают с TDS, включая среду Management Studio. Скрипты, разрабатываемые для локального сервера, запускаются на логическом сервере базы данных SQL. 

В следующем шаге вы будете использовать среду Management Studio для подключения к логическому серверу в Azure. Для выполнения этого шага требуется SQL Server Management Studio версии 2008 R2 или 2012. Если вам необходима помощь с загрузкой или подключением к среде Management Studio, см. раздел [Управление базой данных SQL с помощью SQL Server Management Studio][Управление базой данных SQL с помощью SQL Server Management Studio] на этом сайте.

Перед подключением иногда бывает необходимо создать исключение брандмауэра, которое разрешает исходящие запросы через порт 1433 на локальном компьютере. У компьютеров с конфигурацией защиты по умолчанию порт 1433 обычно закрыт. 

<h3><a id="configFWOnPremise"></a>Настройка брандмауэра для локального сервера</h3>

1. Создайте в брандмауэре Windows в режиме повышенной безопасности новое правило для исходящего трафика.

2. Выберите **Порт**, укажите "TCP 1433", укажите **Разрешить подключение** и убедитесь, что выбран профиль **Открытый**.

3. Укажите понятное имя, например *WindowsAzureSQLDatabase (tcp-out) port 1433*. 


<h3><a id="logical"></a>Подключение к логическому серверу</h3>

1. Убедитесь, что в области "Подключение к серверу" среды Management Studio выбран "Компонент Database Engine", а затем введите имя логического сервера в следующем формате: *имя_сервера*.database.widnows.net.

   Также можно получить полное имя сервера на портале управления, в панели мониторинга сервера, в области "УПРАВЛЕНИЕ URL-АДРЕСАМИ".

2. В поле "Проверка подлинности" выберите **Проверка подлинности SQL Server**, а затем введите имя входа администратора, созданное при настройке логического сервера.

3. Щелкните элемент **Параметры**. 

4. В поле "Подключение к базе данных" укажите **master**.


<h3><a id="premise"></a>Подключение к локальному серверу</h3>

1. Убедитесь, что в области "Подключение к серверу" среды Management Studio выбран "Компонент Database Engine", а затем введите имя локального экземпляра в следующем формате: *имя_сервера*\\*имя_экземпляра*. Если сервер является локальным экземпляром по умолчанию, введите *localhost*.

2. В поле "Проверка подлинности" выберите **Проверка подлинности Windows**, а затем введите учетную запись Windows, которая является членом роли sysadmin.


<h2><a id="HowTo1"></a>Развертывание базы данных в Azure</h2>

Существует множество подходов к перемещению локальной базы данных SQL Server в Azure. В этой задаче для отправки образца базы данных вы будете использовать мастер развертывания базы данных в базе данных SQL.

Образец базы данных School удобен и прост — все его объекты совместимы с базой данных SQL, что устраняет потребность в изменении базы данных или подготовке ее к переносу. Поскольку вы только начинаете работать в качестве администратора, попробуйте сначала развернуть простую базу данных, чтобы изучить всю процедуру, и только потом переходите к использованию собственных баз данных. 

**Примечание.** Просмотрите руководство по переносу баз данных SQL, чтобы получить подробные инструкции о подготовке локальной базы данных к переносу в Azure. Кроме того, рекомендуется загрузить учебный набор Azure. Он включает в себя лабораторную работу, демонстрирующую альтернативный способ переноса локальной базы данных.

<h3><a id="CreateDB"></a>Создание базы данных "school" на локальном сервере</h3>

Скрипты для создания этой базы данных можно найти в разделе [Приступая к администрированию базы данных SQL][Приступая к администрированию базы данных SQL]. В рамках данного руководства вам предстоит запустить эти скрипты в среде Management Studio, чтобы создать локальную версию базы данных school.

1. В среде Management Studio подключитесь к локальному серверу. Щелкните правой кнопкой мыши элемент **Базы данных**, выберите пункт **Создать базу данных** и введите *school*.

2. Щелкните правой кнопкой мыши элемент *school* и выберите пункт **Создать запрос**. 

3. Скопируйте и выполните скрипт "Создание схемы" из учебника. 

<div style="width:auto; height:300px; overflow:auto"><pre>
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

Затем скопируйте и выполните скрипт "Добавление данных".

<div style="width:auto; height:300px; overflow:auto"><pre>
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

   Теперь у вас есть локальная база данных, которую можно экспортировать в Azure. Далее вам предстоит запустить мастер, который создает файл BACPAC, загружает его в Azure и импортирует в базу данных SQL.


<h3><a id="DeployDB"></a>Развертывание в базе данных SQL</h3>

1. В среде Management Studio подключитесь к локальному экземпляру SQL Server с базой данных, которую требуется перенести.

2. Щелкните правой кнопкой мыши только что созданную базу данных "school", наведите указатель на пункт **Задачи** и выберите команду **Развертывание базы данных в базе данных SQL**.

3. В параметрах развертывания введите имя базы данных, например *school*. 

4. Щелкните **Подключить**.

5. В поле "Имя сервера" введите имя сервера из 10 символов и добавьте к нему ".databases.windows.net".

6. В поле "Проверка подлинности" выберите значение **Проверка подлинности SQL Server**.

7. Введите имя входа и пароль администратора, указанные при создании логического сервера базы данных SQL.

8. Щелкните элемент **Параметры**.

9. В поле "Подключение к базе данных" свойств подключения введите слово **master**.

10. Щелкните **Подключить**. После выполнения этого шага вы завершаете настройку подключения и возвращаетесь в мастер.


11. Нажмите кнопку **Далее**, затем кнопку **Готово** для запуска мастера.

<h3><a id="VerifyDBMigration"></a>Проверка развертывания базы данных</h3>

1. В среде Management Studio подключитесь к логическому серверу. При наличии открытого подключения можно закрыть его и открыть новое подключение. Существующее подключение показывает только те базы данных, которые были запущены на момент его установки.

   Инструкции по подключению к логическому серверу см. в разделе [Подключение с помощью среды Management Studio][Подключение с помощью среды Management Studio] этого документа. 

2. Разверните папку "Базы данных". В списке должна присутствовать база данных "school".

3. Щелкните базу данных "school" правой кнопкой мыши и выберите пункт **Создать запрос**.

4. Выполните следующий запрос, чтобы убедиться в доступности данных.

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


<h2><a id="HowTo2"></a>Добавление имен входа и пользователей</h2>

После развертывания базы данных необходимо настроить имена входа и назначить разрешения. На следующем шаге выполняются два скрипта.

Для первого скрипта можно подключиться к базе данных "master" и выполнить сценарий, создающий имена входа. Имена входа будут использоваться для поддержки операций чтения и записи и для делегирования OLE_LINK1OLE_LINK2 операционных задач, таких как возможность выполнения запросов системы без разрешений OLE_LINK1OLE_LINK2"sa".

Создаваемые имена входа должны быть именами входа для проверки подлинности SQL Server. Если имеются уже готовые скрипты, использующие удостоверения пользователей Windows или удостоверения утверждений, этот скрипт не будет работать для базы данных SQL.

Второй скрипт присваивает разрешения пользователей базы данных. Для этого скрипта вы будете подключаться к базе данных, уже загруженной в Azure.

<h3><a id="CreateLogins"></a>Создание имен входа</h3>

1. В среде Management Studio подключитесь к логическому серверу в Azure, разверните папку "Базы данных", щелкните правой кнопкой мыши элемент **master** и выберите **Создать запрос**.

2. Используйте следующие инструкции Transact-SQL для создания имен входа. Замените пароль на действительный пароль. Выберите каждый из элементов по отдельности, а затем щелкните **Выполнить**. Повторите для остальных имен входа.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


<h3><a id="CreateDBUsers"></a>Создание пользователей базы данных</h3>

1. Разверните папку "Базы данных", щелкните правой кнопкой мыши элемент **school**и выберите **Создать запрос**.

2. Используйте следующие инструкции Transact-SQL для добавления пользователей базы данных. Замените пароль на действительный пароль. 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

<h3><a id="ViewLogins"></a>Представления и тестовые имена входа</h3>

1. В окне нового запроса подключитесь к базе данных **master** и выполните следующую инструкцию: 

        SELECT * from sys.sql_logins;

2. В Management Studio щелкните базу данных **school** правой кнопкой мыши и выберите пункт **Создать запрос**.

3. В меню "Запрос" выберите пункт **Подключение** и щелкните элемент **Изменить подключение**.

4. Выполните вход с именем *sqlreader*.

5. Скопируйте и попробуйте запустить следующую инструкцию. Вы должны получить сообщение о том, что объект не существует.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. Откройте второе окно запроса и измените контекст подключения на *sqlwriter*. Теперь этот запрос должен выполняться успешно.

Вы создали и протестировали несколько имен входа. Дополнительные сведения см. в разделах [Управление базами данных и учетными записями в базе данных SQL][Управление базами данных и учетными записями в базе данных SQL] и [Мониторинг базы данных SQL с помощью динамических административных представлений][Мониторинг базы данных SQL с помощью динамических административных представлений].


<h2><a id="HowTo3"></a>Мониторинг логических серверов и экземпляров баз данных</h2>

Те средства и методы мониторинга, которые вы привыкли использовать на локальном сервере, например аудит входов, выполнение трассировок и использование счетчиков производительности, недоступны для базы данных SQL. В Azure для мониторинга емкости данных, проблем запросов и текущих подключений используются представления управления данными. 

Дополнительные сведения см. в разделе [Мониторинг базы данных SQL с помощью динамических административных представлений][Мониторинг базы данных SQL с помощью динамических административных представлений].


<h2><a id="HowTo4"></a>Масштабирование решения базы данных SQL</h2>

В Azure масштабируемость базы данных является синонимом горизонтального масштабирования, при котором рабочая нагрузка распределяется между несколькими рядовыми серверами в центре обработки данных. Горизонтальное масштабирование — это стратегия решения проблем, связанных с объемом данных или производительностью. Очень большая и быстро растущая база данных со временем потребует задействовать стратегию масштабирования независимо от того, работает ли с ней лишь несколько или множество пользователей.

Горизонтальное масштабирование в Azure лучше всего реализуется с помощью федерации. Федерация базы данных SQL основана на горизонтальном разделении, при котором одна или несколько таблиц разделяются по строкам, и образовавшиеся части распределяются между несколькими участниками федерации. 

Федерация не является единственным решением для всех проблем масштабируемости. Иногда характеристики данных или требования приложения указывают на более простые подходы. В следующем списке возможные решения представлены в порядке их сложности.

**Увеличение размера базы данных**

Базы данных создаются с фиксированным размером, ограниченным максимальным значением для соответствующего выпуска. Для выпуска Web можно увеличить базу данных максимум до 5 гигабайт. Для выпуска Business максимальный размер составляет 150 гигабайт. Самым очевидным способом увеличить емкость данных является изменение выпуска и максимального размера:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**Использование нескольких баз данных и распределение пользователей**

В некоторых сценариях можно создать копии базы данных, а затем распределять учетные записи и пользователей между копиями базы данных. До появления федерации это был распространенный подход к перераспределению рабочей нагрузки. Этот способ подходит для баз данных, используемых на краткосрочной основе с последующим объединением в главную базу данных, хранящуюся локально, и для решений, предоставляющих данные только для чтения.

**Использование федераций**

Федерации в базе данных SQL используются для улучшения масштабируемости и производительности. Одна или несколько таблиц в базе данных разделяются по строками и распределяются между несколькими базами данных (участниками федерации). Этот тип горизонтального OLE_LINK3OLE_LINK4разбиения часто называют OLE_LINK3OLE_LINK4"сегментированием". Основными сценариями, для которых полезен этот способ, являются сценарии, в которых нужно обеспечить масштабирование и производительность или управлять емкостью. 

Федерации поддерживаются в выпуске Business. Дополнительные сведения см. в статьях [Федерации в базе данных SQL][Федерации в базе данных SQL] и [Учебный курс по федерациям базы данных SQL — администратор базы данных][Учебный курс по федерациям базы данных SQL — администратор базы данных].

**Другие виды хранения**

Не забывайте, что Azure поддерживает несколько видов хранения данных, включая хранилища таблиц и хранилища BLOB-объектов. Если реляционные возможности не требуются, хранилище таблиц или BLOB-объектов может быть более экономичным решением. 

<h2><a id="NextSteps"></a>Дальнейшие действия</h2>

Вы изучили основные сведения об администрировании базы данных SQL. Дополнительные сведения о более сложных административных задачах можно найти по следующим ссылкам.

* Просмотрите статью [База данных SQL][База данных SQL] на сайте MSDN
* Посетите [вики-сайт TechNet о базе данных SQL][вики-сайт TechNet о базе данных SQL]



[Вход в Azure]: #PreReq1
[Настройка базы данных SQL]: #PreReq2
[Подключение с помощью среды Management Studio]: #PreReq3
[Развертывание базы данных в Azure]: #HowTo1
[Добавление имен входа и пользователей]: #HowTo2
[Мониторинг логических серверов и экземпляров баз данных]: #HowTo3
[Масштабирование решения базы данных SQL]: #HowTo4
[Дальнейшие действия]: #NextSteps

[База данных SQL]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg619386

[Вики-сайт TechNet о базе данных SQL]: http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-ru-ru.aspx

[Использование базы данных SQL]: http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/sql-azure/
[Федерации в базе данных SQL]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh597452.aspx
[Учебный курс по федерациям базы данных SQL — администратор базы данных]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh778416.aspx
[Управление базой данных SQL с помощью SQL Server Management Studio]: http://www.windowsazure.com/ru-ru/develop/net/common-tasks/sql-azure-management/
[Мониторинг базы данных SQL с помощью динамических административных представлений]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ff394114.aspx
[Введение в георепликацию для хранилищ Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
[Создание учетной записи хранения для подписки Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433066.aspx
[Загрузка пакета Azure SDK]: http://www.microsoft.com/ru-ru/download/details.aspx?id=15658
[Средства управления Azure]: http://wapmmc.codeplex.com/
[Приступая к администрированию базы данных SQL]: http://www.windowsazure.com/ru-ru/manage/tutorials/sql-azure-management/  
[Управление базами данных и учетными записями в базе данных SQL]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee336235.aspx
[Использование службы хранения больших двоичных объектов]: https://www.windowsazure.com/ru-ru/develop/net/how-to-guides/blob-storage/
[Клиент службы импорта-экспорта приложения уровня данных базы данных SQL версии 1.5]: http://sqldacexamples.codeplex.com/releases/view/85948


[Adventure Works для базы данных SQL]: http://msftdbprodsamples.codeplex.com/releases/view/37304


