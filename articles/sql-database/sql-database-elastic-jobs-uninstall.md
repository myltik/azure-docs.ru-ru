<properties 
	pageTitle="Удаление инструмента заданий обработки эластичных баз данных" 
	description="Удаление инструмента заданий обработки эластичных баз данных" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="ddove"/>

#Удаление компонентов заданий обработки эластичных баз данных.
Компоненты **заданий обработки эластичных баз данных** можно удалить с помощью портала или PowerShell.

##Удаление компонентов заданий обработки эластичных баз данных с помощью портала Azure

1. Откройте [портал Azure](https://portal.azure.com/).
2. Перейдите к подписке, которая содержит компоненты **заданий обработки эластичных баз данных**, то есть подписку, в которой они были установлены.
3. Последовательно щелкните **Обзор** и **Группы ресурсов**.
4. Выберите группу ресурсов с именем \_\_ElasticDatabaseJob.
5. Удалите ее.

##Удаление компонентов заданий обработки эластичных баз данных с помощью PowerShell

1.	Откройте командную строку Microsoft Azure PowerShell и перейдите в подкаталог tools в папке Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: введите команду **cd tools**.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.	Запустите сценарий PowerShell .\\UninstallElasticDatabaseJobs.ps1.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

Или просто запустите следующий сценарий, если для установки компонентов использовались значения по умолчанию:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## Дальнейшие действия

Сведения о повторной установке службы можно найти в статье [Установка компонентов службы заданий обработки эластичных баз данных](sql-database-elastic-jobs-service-installation.md).

Общие сведения о заданиях обработки эластичных баз данных см. в статье [Обзор службы заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md).

<!--Image references-->

 

<!---HONumber=AcomDC_0615_2016-->