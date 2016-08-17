<properties
	pageTitle="Управление базой данных SQL Azure с помощью PowerShell | Microsoft Azure"
	description="Управление базой данных SQL Azure с помощью PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="sstein"/>

# Управление базой данных SQL Azure с помощью PowerShell


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

В этой статье представлены команды PowerShell для выполнения различных задач Базы данных SQL Azure.

[AZURE.INCLUDE [Запуск сеанса PowerShell](../../includes/sql-database-powershell.md)]


## Создание группы ресурсов

Создайте группу ресурсов, которая будет содержать сервер. Следующую команду можно изменить, указав любое допустимое расположение.

Список допустимых расположений серверов базы данных SQL можно получить, выполнив следующий командлет.

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

Если у вас уже есть группа ресурсов, вы можете перейти к следующему шагу ("Создание сервера") или изменить и выполнить команду ниже, чтобы создать новую группу ресурсов.

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Создание сервера

Чтобы создать новый сервер версии 12, воспользуйтесь командлетом [New-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Замените *server12* именем своего сервера. Если имя сервера уже используется, отобразится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. После успешного создания сервера будут отображены сведения о сервере и командная строка PowerShell. Команду можно изменить, указав любое допустимое расположение.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

При выполнении этой команды отображается запрос на ввод имени пользователя и пароля. Не указывайте здесь свои учетные данные Azure. Вместо этого введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.

## Создание правила брандмауэра для сервера

Чтобы создать правило брандмауэра для доступа к серверу, воспользуйтесь командой [New-AzureRMSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Выполните следующую команду, заменив начальный и конечный IP-адреса значениями для своего клиента.

Если сервер должен предоставлять доступ к другим службам Azure, добавьте параметр **- AllowAllAzureIPs**. Он добавит специальное правило брандмауэра и разрешит всему трафику Azure доступ к серверу.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Создание базы данных SQL

Для создания базы данных используйте команду [New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Для создания базы данных необходим сервер. В следующем примере создается база данных SQL, с именем TestDB12. Эта база данных создается с уровнем производительности Standard S1.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Изменение уровня производительности базы данных SQL

Для масштабирования базы данных воспользуйтесь командой [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). В следующем примере база данных с именем TestDB12 масштабируется с текущего уровня производительности на уровень Standard S3.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Удаление базы данных SQL

Для удаления базы данных SQL воспользуйтесь командой [Remove-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). В следующем примере удаляется база данных SQL с именем TestDB12.

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Удаление сервера

Сервер также можно удалить командой [Remove-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx). В следующем примере удаляется сервер с именем server12.


>[AZURE.NOTE]  Операция удаления является асинхронной и может занять некоторое время. Убедитесь, что эта операция завершена, прежде чем выполнять любые дополнительные операции, связанные с сервером, который полностью удаляется (например, создание сервера с тем же именем).


	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"




## Дальнейшие действия

Объедините и автоматизируйте команды. Например, чтобы создать сервер, правило брандмауэра и базу данных, замените все содержимое внутри кавычек, включая знаки < и >, соответствующими значениями.


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Связанные сведения

- [Командлеты Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0803_2016-->