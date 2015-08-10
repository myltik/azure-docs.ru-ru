<properties 
	pageTitle="Добавление пользователей в эластичный пул баз данных." 
	description="Необходимо добавить пользователя с правами доступа во все базы данных пула." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh" />

# Добавление пользователей в пул эластичных баз данных.

**Задания эластичной базы данных** позволяют выполнять один сценарий для всех баз данных в [пуле эластичных баз данных](sql-database-elastic-pool.md). Чтобы запустить сценарий, во все базы данных пула нужно добавить пользователя с соответствующими разрешениями. Дополнительные сведения см. в статье [Управление базами данных и учетными данными в базе данных SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396) или [Добавление пользователей в базу данных SQL Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/)

## Предварительные требования
* [Создайте пул эластичных баз данных (предварительная версия)](sql-database-elastic-pool-portal.md).
* Установите [компоненты заданий эластичной БД](sql-database-elastic-jobs-service-installation.md). 

## Добавление пользователей в базы данных

1.	Сначала подключитесь к **master** сервера базы данных SQL Azure, где находятся базы данных в пуле эластичных баз данных. Создайте новое имя пользователя с помощью тех же учетных данных, которые вы ввели во время установки **заданий эластичной базы данных**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Войдите в каждую базу данных в пуле и создайте пользователя, используя те же имя и пароль. У пользователя должно быть достаточно разрешений для выполнения задания. Этот код должен выполняться в каждой базе данных.

		CREATE USER admin1 FROM LOGIN login1;
		
3. Пользователь должен иметь разрешения, достаточные для выполнения сценария, указанного для задания. Используйте процедуру **sp\_addrolemember**, чтобы предоставить пользователю необходимые разрешения для успешного выполнения сценария.

## Дальнейшие действия

Выполните это задание для пула эластичных баз данных. См. статью [Создание заданий эластичной базы данных и управление ими](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO5-->