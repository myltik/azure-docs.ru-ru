---
title: "Пошаговое руководство по использованию универсального соединителя SQL | Документация Майкрософт"
description: "В этом документе содержится пошаговое руководство по работе с простой системой отдела кадров с использованием универсального соединителя SQL."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 28c1cc60-24fd-4d0d-a36d-b4aba6de86e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 91eb50a06bbf2e6c5510c42bd1d3ae961fd0bddd
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="generic-sql-connector-step-by-step"></a>Универсальный соединитель SQL: пошаговое руководство
Эта статья является пошаговым руководством. Она поможет вам создать простую базу данных для отдела кадров, которая предназначена для импорта некоторых пользователей и их членства в группе.

## <a name="prepare-the-sample-database"></a>Подготовка образца базы данных
На сервере под управлением SQL Server запустите сценарий SQL, который находится в [Приложении A](#appendix-a). Этот сценарий создает пример базы данных с именем GSQLDEMO. Объектная модель для созданной базы данных выглядит так:   
![Объектная модель](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/objectmodel.png)

Также создайте пользователя, который будет использоваться для подключения к базе данных. В этом пошаговом руководстве пользователю присвоено имя FABRIKAM\SQLUser, и он находится в домене.

## <a name="create-the-odbc-connection-file"></a>Создание файла подключения ODBC
Универсальный соединитель SQL использует ODBC для подключения к удаленному серверу. Сначала необходимо создать файл с данными подключения ODBC.

1. Запустите программу управления ODBC на сервере:   
   ![ODBC](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc.png)
2. Откройте вкладку **File DSN**(Файловый DSN). Щелкните **Add...**(Добавить).  
   ![ODBC 1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc1.png)
3. Стандартный драйвер работает нормально, поэтому выберите его и нажмите кнопку **Next>** (Далее>).  
   ![ODBC 2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc2.png)
4. Укажите имя файла, например **GenericSQL**.  
   ![ODBC 3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc3.png)
5. Нажмите кнопку **Готово**  
   ![ODBC 4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc4.png)
6. Теперь нужно настроить подключение. Введите понятное описание источника данных и укажите имя сервера, на котором выполняется SQL Server.  
   ![ODBC 5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc5.png)
7. Выберите способ проверки подлинности с помощью SQL. В нашем примере используется проверка подлинности Windows.  
   ![ODBC 6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc6.png)
8. Укажите имя образца базы данных — **GSQLDEMO**.  
   ![ODBC 7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc7.png)
9. На этом экране оставьте настройки по умолчанию. Нажмите кнопку **Готово**  
   ![ODBC 8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc8.png)
10. Чтобы проверить, все ли работает правильно, нажмите кнопку **Test Data Source**(Проверить источник данных).  
    ![ODBC 9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc9.png)
11. Убедитесь, что проверка прошла успешно.  
    ![ODBC 10](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc10.png)
12. Файл конфигурации ODBC должен появиться на вкладке "File DSN" (Файловый DSN).  
    ![ODBC 11](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc11.png)

Теперь у нас есть нужный файл, и можно приступить к созданию соединителя.

## <a name="create-the-generic-sql-connector"></a>Создание универсального соединителя SQL
1. В пользовательском интерфейсе Synchronization Service Manager выберите **Connectors** (Соединители) и нажмите кнопку **Create** (Создать). Выберите **Generic SQL (Microsoft)** (Универсальный SQL (Майкрософт)) и присвойте описательное имя.  
   ![Соединитель 1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector1.png)
2. Найдите файл DSN, созданный в предыдущем разделе, и отправьте его на сервер. Укажите учетные данные для подключения к базе данных.  
   ![Соединитель 2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector2.png)
3. В этом пошаговом руководстве мы упростим процесс и предположим, что существует два типа объектов: **User** и **Group**.
   ![Соединитель 3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector3.png)
4. При поиске атрибутов нам нужно, чтобы соединитель обнаружил их при просмотре таблицы. Так как объект **Users** является зарезервированным словом в SQL, его необходимо заключить в квадратные скобки [].  
   ![Соединитель 4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector4.png)
5. Теперь нужно определить атрибут привязки и атрибут различаемого имени. Для **Users**мы используем сочетание двух атрибутов — username и EmployeeID. Для **Group**мы используем GroupName (не слишком реалистично для обычной жизни, но подойдет для этого руководства).
   ![Соединитель 5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector5.png)
6. В базе данных SQL могут быть обнаружены только некоторые типы атрибутов. В частности, нельзя обнаружить тип ссылочного атрибута. Для типа объекта Group необходимо изменить значения OwnerID и MemberID на ссылку.  
   ![Соединитель 6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector6.png)
7. Для атрибутов, выбранных как ссылочные атрибуты на предыдущем этапе, теперь требуется тип объекта, ссылкой на который они являются. В нашем случае это тип объекта User.  
   ![Соединитель 7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector7.png)
8. На странице "Глобальные параметры" выберите **Watermark** (Водяной знак) в качестве стратегии изменений. В поле формата даты и времени введите **гггг-ММ-дд ЧЧ:мм:сс**.
   ![Соединитель 8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector8.png)
9. На странице **Configure Partitions and Hierarchies** (Настройка секций и иерархий) выберите оба типа объектов.
   ![Соединитель 9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector9.png)
10. На странице **Выбор типов объектов** и **Выбор атрибутов** выберите оба типа объектов и все атрибуты. На странице **Configure Anchors** (Настройка привязки) нажмите кнопку **Готово**.

## <a name="create-run-profiles"></a>Создание профилей выполнения
1. В пользовательском интерфейсе Synchronization Service Manager выберите **Connectors** (Соединители) и щелкните **Configure Run Profiles** (Настроить профили выполнения). Щелкните **New Profile**(Новый профиль). Мы начнем с профиля **Full Import**(Полный импорт).  
   ![Профиль выполнения 1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile1.png)
2. Выберите тип **Full Import (Stage Only)**(Полный импорт (только демонстрация)).  
   ![Профиль выполнения 2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile2.png)
3. Выберите раздел **OBJECT=User**.  
   ![Профиль выполнения 3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile3.png)
4. Выберите **Table** и введите **[USERS]**. Прокрутите вниз до раздела многозначного типа объекта и введите данные, как показано ниже. Нажмите кнопку **Finish** (Готово), чтобы сохранить данные на этом этапе.  
   ![Профиль выполнения 4а](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4a.png)  
   ![Профиль выполнения 4б](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4b.png)  
5. Выберите **New Step**(Новый шаг). На этот раз выберите **OBJECT=Group**. На последней странице используйте конфигурацию, изображенную на рисунке ниже. Нажмите кнопку **Finish**(Готово).  
   ![Профиль выполнения 5а](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5a.png)  
   ![Профиль выполнения 5б](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5b.png)  
6. Можно настроить дополнительные профили выполнения, но это необязательно. В этом пошаговом руководстве используется только профиль "Full Import" (Полный импорт).
7. Нажмите кнопку **ОК** , чтобы завершить изменение профилей выполнения.

## <a name="add-some-test-data-and-test-the-import"></a>Добавление некоторых тестовых данных и тестирование импорта
Введите тестовые данные в образец базы данных. Когда будете готовы, щелкните **Run** (Выполнить) и **Full import** (Полный импорт).

Здесь мы видим пользователя с двумя телефонными номерами и группу с несколькими участниками.  
![Пространство соединителя 1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs1.png)  
![Пространство соединителя 2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs2.png)  

## <a name="appendix-a"></a>Приложении A
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
