<properties 
	pageTitle="Управление ресурсами Базы данных SQL Azure с помощью PowerShell" 
	description="Управление ресурсами Базы данных SQL с PowerShell" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="vinsonyu"/>

# Управление ресурсами Базы данных SQL Azure с помощью PowerShell


В этой статье представлены команды PowerShell для выполнения различных задач Базы данных SQL Azure с использованием командлетов диспетчера ресурсов Azure.


## Предварительные требования

Для запуска командлетов PowerShell необходимо иметь установленный и запущенный Azure PowerShell и перевести его в режим диспетчера ресурсов, чтобы получить доступ к командлетам PowerShell диспетчера ресурсов Azure.

Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Командлеты для создания баз данных SQL Azure и управления ими находятся в модуле диспетчера ресурсов Azure. При запуске Azure PowerShell командлеты в модуле Azure импортируются по умолчанию. Для перехода в модуль диспетчера ресурсов Azure используйте командлет **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

На экране может появиться предупреждение «Командлет Switch-AzureMode является устаревшим и будет удален в следующих версиях». Его можно игнорировать и перейти к следующему разделу.

Дополнительную информацию можно узнать в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md).



## Настройка учетных данных

Для запуска командлетов PowerShell по подписке Azure необходимо сначала настроить доступ к учетной записи Azure. Выполните следующую команду; откроется окно входа, в котором необходимо ввести свои учетные данные. Используйте тот же адрес электронной почты и пароль, который вы используете для входа на портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


## Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор (**-SubscriptionId**) или имя подписки (**-SubscriptionName**). Идентификатор или имя можно скопировать из предыдущего шага или, если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов.

Выполните следующий командлет с данными о подписке для задания текущей подписки:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Следующие команды будет выполнены для заданной выше подписки.

## Создание группы ресурсов

Создайте группу ресурсов, которая будет содержать сервер. Следующую команду можно изменить, указав любое допустимое расположение.

Список допустимых расположений серверов баз данных SQL Azure можно получить, выполнив следующие командлеты:

	$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
	$AzureSQLLocations.Locations

Если у вас уже есть группа ресурсов, вы можете перейти к следующему шагу или выполнить команду ниже, чтобы создать новую группу ресурсов:

	New-AzureResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Создание сервера 

Для создания нового сервера V12 воспользуйтесь командой [New-AzureSqlServer](https://msdn.microsoft.com/library/mt163526.aspx). Замените server12 на имя вашего сервера. Это имя должно быть уникальным для серверов SQL Azure, поэтому, если оно уже занято, может появиться ошибка. Кроме того, выполнение этой команды может занять несколько минут. После успешного создания сервера будут запрошены сведения о сервере и PowerShell. Команду можно изменить, указав любое допустимое расположение.

	New-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

При выполнении этой команды появится окно, запрашивающее **Имя пользователя** и **Пароль**. Это не учетные данные Azure; введите имя пользователя и пароль, которые станут учетными данными администратора нового сервера.

## Создание правила брандмауэра для сервера

Чтобы создать правило брандмауэра для доступа к серверу, воспользуйтесь командой [New-AzureSqlServerFirewallRule](https://msdn.microsoft.com/library/mt125953.aspx). Выполните следующую команду, заменив начальный и конечный IP-адреса значениями для своего клиента.

Если ваш сервер должен предоставлять доступ к другим службам Azure, добавьте параметр **-AllowAllAzureIPs**, который добавит специальное правило брандмауэра и предоставит всему трафику Azure доступ к серверу.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Дополнительные сведения см. в статье [Брандмауэр Базы данных SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Создание базы данных SQL

Для создания базы данных используйте команду [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125915.aspx). Для создания базы данных необходим сервер. В следующем примере создается база данных SQL, с именем TestDB12. Эта база данных создается с уровнем производительности Standard S1.

	New-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Изменение уровня производительности базы данных SQL

Для масштабирования базы данных в сторону увеличения или уменьшения воспользуйтесь командой [Set-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125814.aspx). В следующем примере база данных с именем TestDB12 масштабируется с текущего уровня производительности на уровень Standard S3.

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Удаление базы данных SQL

Для удаления базы данных SQL воспользуйтесь командой [Remove-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125977.aspx). В следующем примере удаляется база данных SQL с именем TestDB12.

	Remove-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Удаление сервера

Также сервер можно удалить командой [Remove-AzureSqlServer](https://msdn.microsoft.com/library/mt125891.aspx). В следующем примере удаляется сервер с именем server12.

	Remove-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Если в будущем вы планируете снова создавать такие же или аналогичные SQL-ресурсы Azure, можно выполнить одно из двух действий:

- сохранить блок командлетов как файл скрипта PowerShell (*.ps1);
- сохранить его как Runbook службы автоматизации Azure в разделе «Автоматизация» на портале управления Azure. 

## Дальнейшие действия

Объедините и автоматизируйте команды. Например, замените все содержимое внутри кавычек, включая символы < and >, соответствующими значениями для создания сервера, правила брандмауэра и базы данных:


    New-AzureResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Сопутствующая информация

- [Командлеты диспетчера ресурсов Базы данных SQL Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Командлеты управления службой Базы данных SQL Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=August15_HO6-->