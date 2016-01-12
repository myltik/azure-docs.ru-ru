<properties
   pageTitle="Создание хранилища данных SQL с помощью Powershell | Microsoft Azure"
   description="Создание хранилища данных SQL с помощью Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="lodipalm"/>

# Создание хранилища данных SQL с помощью Powershell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]Чтобы использовать Microsoft Azure Powershell с хранилищем данных SQL, требуется версия 0.9.4 или выше. Узнать версию можно путем запуска командлета (Get-Module Azure).Version в Powershell.

## Получение и запуск командлетов Azure PowerShell
Если вы еще не настроили Powershell, можно выполнить следующие действия.

1. Чтобы загрузить модуль Azure PowerShell, запустите [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Чтобы запустить модуль, введите в окне запуска **Microsoft Azure PowerShell**.
3. Если вы еще не добавили свою учетную запись на компьютер, запустите следующий командлет. (Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell][]).

            Add-AzureAccount

4. Также потребуется запустить PowerShell в режиме ARM. Для переключения в этот режим можно выполнить следующую команду:

            switch-azuremode AzureResourceManager

## Создание хранилища данных SQL
Убедившись, что среда Powershell успешно настроена для вашей учетной записи, вы можете выполнить следующую команду, чтобы развернуть новое хранилище данных SQL:

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

Ниже приведены необходимые параметры для этого командлета.

 + **RequestedServiceObjectiveName**: объем запрашиваемых единиц DWU в формате DWXXX. В настоящее время поддерживаются следующие значения: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: имя создаваемого хранилища данных SQL.
 + **ServerName**: имя сервера, который используется для создания (должен быть версии 12).
 + **ResourceGroupName**: используемая группа ресурсов. Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
 + **Edition**: необходимо задать выпуск DataWarehouse, чтобы создать хранилище данных SQL. 

## Дальнейшие действия
После завершения подготовки хранилища данных SQL вы можете [загрузить демонстрационные данные][] или ознакомиться с возможностями [разработки][], [загрузки][] или [переноса][].

Если вы хотите подробнее узнать о том, как программно управлять хранилищем данных SQL, обратитесь к нашей документации по [Powershell][] или [REST API][].



<!--Image references-->

<!--Article references-->
[переноса]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-data-warehouse-overview-migrate/
[разработки]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-data-warehouse-overview-develop/
[загрузки]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-data-warehouse-overview-load/
[загрузить демонстрационные данные]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/ru-RU/documentation/articles/sql-database-configure-firewall-settings/
[Установка и настройка Azure PowerShell]: powershell-install-configure.md

<!---HONumber=AcomDC_0107_2016-->