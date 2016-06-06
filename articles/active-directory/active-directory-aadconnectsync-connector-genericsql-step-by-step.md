<properties
   pageTitle="Синхронизация Azure AD Connect: пошаговое руководство по использованию универсального соединителя Generic SQL | Microsoft Azure"
   description="В этом документе содержится пошаговое руководство по работе с простой системой отдела кадров с использованием универсального соединителя SQL."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/24/2016"
   ms.author="andkjell"/>

# Универсальный соединитель SQL: пошаговое руководство
Этот документ является пошаговым руководством. С его помощью будет создан простой образец базы данных отдела кадров, который станет использоваться для импорта некоторых пользователей и их членства в группе.

## Подготовка образца базы данных
На сервере под управлением SQL Server запустите сценарий SQL, который находится в [Приложении A](#appendix-a). Будет создан образец базы данных с именем GSQLDEMO. Объектная модель для созданной базы данных будет выглядеть следующим образом:

![Объектная модель](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Также создайте пользователя, который будет использоваться для подключения к базе данных. В этом пошаговом руководстве пользователю задано имя FABRIKAM\\SQLUser, и он находится в домене.

## Создание файла подключения ODBC
Универсальный соединитель SQL использует ODBC для подключения к удаленному серверу. Сначала необходимо создать файл с данными подключения ODBC.

1. Запустите программу управления ODBC на сервере: ![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Откройте вкладку **Файл DSN**. Нажмите кнопку **Добавить...** ![ODBC 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Стандартный драйвер будет работать нормально, поэтому выберите его и нажмите кнопку **Далее >**. ![ODBC 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Укажите имя для файла, например **GenericSQL**. ![ODBC 3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Нажмите кнопку **Готово**. ![ODBC 4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Теперь нужно настроить подключение. Введите понятное описание для источника данных и укажите имя сервера, где выполняется SQL Server. ![ODBC 5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Выберите способ проверки подлинности с помощью SQL. В этом случае используется проверка подлинности Windows. ![ODBC 6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Укажите имя образца базы данных — **GSQLDEMO**. ![ODBC 7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. На этом экране оставьте настройки по умолчанию. Нажмите кнопку **Готово**. ![ODBC 8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Чтобы убедиться, что все работает правильно, нажмите кнопку **Проверить источник данных**. ![ODBC 9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Убедитесь, что проверка прошла успешно. ![ODBC 10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. Файл конфигурации ODBC должен появиться на вкладке "Файл DSN". ![ODBC 11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Теперь у нас есть нужный файл, и можно приступить к созданию соединителя.

## Создание универсального соединителя SQL

1. В пользовательском интерфейсе диспетчера службы синхронизации выберите **Соединители** и нажмите кнопку **Создать**. Выберите **Универсальный SQL (Майкрософт)** и присвойте ему описательное имя. ![Соединитель 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Найдите файл DSN, созданный в предыдущем разделе, и отправьте его на сервер. Укажите учетные данные для подключения к базе данных. ![Соединитель 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. В этом пошаговом руководстве мы упростим процесс и предположим, что существует два типа объектов: **User** и **Group**. ![Соединитель 3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. При поиске атрибутов нам нужно, чтобы соединитель обнаружил их при просмотре таблицы. Поскольку **Users** является зарезервированным словом в SQL, его необходимо заключить в квадратные скобки []. ![Соединитель 4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Теперь нужно определить атрибут привязки и атрибут различаемого имени. Для **Users** мы будем использовать сочетание двух атрибутов — username и EmployeeID. Для **Group** мы будем использовать GroupName (не слишком реалистично для обычной жизни, но подойдет для данного пошагового руководства). ![Соединитель 5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. В базе данных SQL могут быть обнаружены только некоторые типы атрибутов. В частности, нельзя обнаружить тип ссылочного атрибута. Для типа объекта Group необходимо изменить OwnerID и MemberID на ссылку. ![Соединитель 6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Для атрибутов, выбранных как ссылочные атрибуты на предыдущем шаге, теперь требуется тип объекта, ссылкой на который они являются. В нашем случае это будет тип объекта User. ![Соединитель 7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. На странице "Глобальные параметры" выберите **Предел** в качестве стратегии изменений. В поле формата даты и времени введите **гггг-ММ-дд ЧЧ:мм:сс**. ![Соединитель 8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. В разделе **Настройка разделов** выберите оба типа объектов. ![Соединитель 9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. На странице **Выбор типов объектов** и **Выбор атрибутов** выберите оба типа объектов и все атрибуты. На странице **Настройка привязки** нажмите кнопку **Готово**.

## Создание профилей выполнения

1. В пользовательском интерфейсе диспетчера службы синхронизации выберите **Соединители** и щелкните **Настроить профили выполнения**. Щелкните **Создать профиль**. Мы начнем с профиля **Полный импорт**. ![Профиль выполнения 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Выберите тип **Полный импорт (только демонстрация)**. ![Профиль выполнения 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Выберите раздел — **OBJECT=User**. ![Профиль выполнения 3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Выберите **Table** и введите **[USERS]**. Прокрутите вниз до раздела "Многозначный тип объекта" и введите данные, как показано ниже. Нажмите кнопку **Готово**, чтобы сохранить данные на этом шаге. ![Профиль выполнения 4а](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png) ![Профиль выполнения 4б](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Выберите **Новый шаг**. На этот раз выберите **OBJECT=Group**. На последней странице используйте приведенные ниже настройки. Нажмите кнопку **Готово**. ![Профиль выполнения 5а](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png) ![Профиль выполнения 5б](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. Необязательно: если требуется, можно настроить дополнительные профили выполнения. В данном пошаговом руководстве используется только профиль "Полный импорт".
7. Нажмите кнопку **ОК**, чтобы завершить изменение профилей выполнения.

## Добавление некоторых тестовых данных и тестирование импорта

Введите тестовые данные в образец базы данных. Когда будете готовы, щелкните **Выполнить** и **Полный импорт**.

Здесь мы видим пользователя с двумя телефонными номерами и группу с несколькими членами. ![Пространство соединителя 1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png) ![Пространство соединителя 2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)

## приложении А
**Сценарий SQL для создания образца базы данных**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
	[MemberID] [int] NOT NULL,
	[Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
	[GroupID] [int] NOT NULL,
	[GROUPNAME] [nvarchar](200) NOT NULL,
	[DESCRIPTION] [nvarchar](200) NULL,
	[WATERMARK] [datetime] NULL,
	[OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
	[USER_ID] [int] NULL,
	[Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
	[USERID] [int] NOT NULL,
	[USERNAME] [nvarchar](200) NOT NULL,
	[FirstName] [nvarchar](100) NULL,
	[LastName] [nvarchar](100) NULL,
	[DisplayName] [nvarchar](100) NULL,
	[ACCOUNTDISABLED] [bit] NULL,
	[EMPLOYEEID] [int] NOT NULL,
	[WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
	[USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```

<!---HONumber=AcomDC_0525_2016-->