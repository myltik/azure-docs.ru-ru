<properties
   pageTitle="Устранение проблем совместимости базы данных SQL Server перед переносом в базу данных SQL | Microsoft Azure"
   description="База данных SQL Microsoft Azure, миграция базы данных, совместимость, мастер миграции SQL Azure, SSDT"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Использование SQL Server Data Tools для Visual Studio для переноса базы данных SQL Server в базу данных SQL Azure 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Помощник по обновлению](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

В этой статье вы узнаете, как найти и устранить проблемы совместимости базы данных SQL Server с помощью SQL Server Data Tools для Visual Studio, прежде чем выполнить миграцию в базу данных SQL Azure.

## Использование SQL Server Data Tools для Visual Studio

SQL Server Data Tools для Visual Studio (SSDT) позволяет импортировать схему базы данных в проект базы данных Visual Studio для анализа. Для анализа укажите целевую платформу для проекта как Базу данных SQL версии 12, а затем соберите проект. Если построение выполнено успешно, база данных совместима. Если построение завершается неудачей, вы можете устранить ошибки SSDT (или одним из других средств, рассмотренных в этом разделе). После успешного построения проекта, вы можете опубликовать его в качестве копии базы данных-источника, а затем использовать функцию сравнения данных в SSDT, чтобы скопировать данные из базы данных-источника в базу данных, совместимую с SQL Azure версии 12. Затем вы можете выполнить миграцию обновленной базы данных. Чтобы использовать этот параметр, загрузите [последнюю версию SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Схема переноса VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Если требуется перенести только схему, можно опубликовать ее в Базе данных SQL Azure непосредственно из Visual Studio. Этот метод используется, если схема баз данных требует больше изменений, чем может обработать мастер миграции.

## Обнаружение проблем совместимости с помощью SQL Server Data Tools для Visual Studio
   
1.	Откройте **обозреватель объектов SQL Server** в Visual Studio. Выберите **Добавить SQL Server** для подключения к экземпляру SQL Server, содержащему базу данных для переноса. Найдите базу данных в обозревателе, щелкните ее правой кнопкой мыши и выберите команду **Создать новый проект**.
    
	![Новый проект](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)
   
2.	В настройках параметров импорта установите флажок **Импортировать только объекты области приложения**. Снимите флажки для импорта упоминаемых имена входа, разрешений и параметров базы данных.

    ![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Нажмите кнопку **Начать**, чтобы приступить к импорту базы данных и создать проект, который будет содержать файл сценария T-SQL для каждого объекта в базе данных. Файлы сценариев вложены в папки проекта.

    ![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	В обозревателе решений Visual Studio щелкните правой кнопкой мыши проект базы данных и выберите "Свойства". После этого откроется страница **Параметры проекта**, на которой необходимо настроить целевую платформу для Базы данных SQL Microsoft Azure V12.
    
    ![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)
    
5.	Щелкните проект правой кнопкой мыши и выберите пункт **Сборка** для сборки проекта.
    
	![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)
    
6.	**Список ошибок** отображает каждую несовместимость. В этом случае имя пользователя NT AUTHORITY\\NETWORK SERVICE является несовместимым. Поскольку оно несовместимо, его можно закомментировать или удалить (и разрешить последствия удаления данного имени входа и роли из решения базы данных).
    
	![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
    
## Устранение проблем совместимости с помощью SQL Server Data Tools для Visual Studio        
  
1.	Дважды щелкните первый скрипт, чтобы открыть сценарий в окне запроса, закомментировать его, а затем выполнить. ![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.	Повторяйте эту процедуру для каждого сценария, имеющего несовместимости, пока все ошибки не будут устранены. ![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)  
 
3.	Устранив все ошибки, щелкните правой кнопкой мыши проект и выберите команду **Опубликовать**, чтобы создать и опубликовать базу данных в копии базы данных-источника (настоятельно рекомендуется использовать именно копию, по крайней мере первоначально).
 - В зависимости от версии SQL Server источника (более ранней, чем SQL Server 2014) перед публикацией может потребоваться сбросить целевую платформу проекта, чтобы обеспечить возможность развертывания.
 - Если вы переносите старую базу данных SQL Server, вам не следует добавлять в проект какие-либо функции, которые не поддерживается исходным сервером SQL Server, за исключением случаев, когда база данных сначала была перенесена в более новую версию SQL Server.

    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
    	![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
    
4.	В обозревателе объектов SQL Server щелкните правой кнопкой мыши базу данных и команду **Сравнение данных** для сравнения проекта с базой данных-источником, чтобы понять, какие изменения были внесены мастером. Выберите базу данных Azure SQL версии 12 и нажмите кнопку **Готово**.
    
	![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)
    
	![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)
    
5.	Просмотрите обнаруженные различия и нажмите кнопку **Обновить цель** для переноса данных из базы данных-источника в базу данных Azure SQL версии 12.
    
	![замещающий текст](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)
    
6.	Выберите методы развертывания. Ознакомьтесь с разделом [Перенос совместимой базы данных SQL Server в Базу данных SQL](sql-database-cloud-migrate.md).

## Дальнейшие действия

- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Дополнительные ресурсы

- [База данных SQL версии 12.](sql-database-v12-whats-new.md)
- [Частично или полностью неподдерживаемые функции Transact-SQL](sql-database-transact-sql-information.md).
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->