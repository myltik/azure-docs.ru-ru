<properties 
	pageTitle="Удаление службы заданий эластичной базы данных" 
	description="Удаление службы заданий эластичной базы данных" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh"/>

# Удаление компонентов службы заданий эластичной базы данных

Если во время установки службы заданий эластичной базы данных произойдет ошибка, удалите группу ресурсов службы.

## Удаление компонентов службы

1. Войдите на [портал предварительной версии Azure](https://ms.portal.azure.com/).
2. Откройте подписку, в которой создана служба заданий эластичной базы данных.
3. Последовательно щелкните **Обзор** и **Группы ресурсов**.
4. Выберите группу ресурсов с именем __ElasticDatabaseJob.
5. Удалите ее.

Также можно воспользоваться приведенным ниже сценарием PowerShell.

1. Откройте окно [Microsoft Azure PowerShell](../powershell-install-configure.md). 
2. Убедитесь, что вы используете пакет SDK для PowerShell не ниже версии 0.8.10.
3. Выполните такой сценарий:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## Дальнейшие действия

Сведения о повторной установке службы см. в статье [Установка компонентов службы заданий эластичной базы данных](sql-database-elastic-jobs-service-installation.md).

Общие сведения о службе см. в статье [Обзор службы заданий эластичной базы данных](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/

<!---HONumber=58--> 