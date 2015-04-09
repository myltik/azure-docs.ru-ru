<properties 
	pageTitle="Управление кэшем Redis для Azure с использованием Azure PowerShell" 
	description="Узнайте, как осуществлять администрирование кэша Redis для Azure с помощью Azure PowerShell." 
	services="redis-cache" 
	documentationCenter="" 
	authors="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="all"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="multiple" 
	ms.date="03/06/2015"
	ms.author="riande"/>

# Управление кэшем Redis для Azure с использованием Azure PowerShell

Этот учебник содержит краткое руководство по созданию, обновлению и удалению кэша Redis для Azure.

## Предварительные требования ##

Чтобы использовать Windows PowerShell с диспетчером ресурсов, необходимо иметь следующее:

- Windows PowerShell версии 3.0 или 4.0. Чтобы найти версию Windows PowerShell, введите:`$PSVersionTable` и убедитесь, что для `PSVersion` указано значение 3.0 или 4.0. Чтобы установить совместимую версию, см. статью [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
	
- Azure PowerShell версии 0.0.8 или более поздней. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. статью [Установка и настройка Azure PowerShell](powershell-install-configure.md).

Этот учебник предназначен для начинающих пользователей Windows PowerShell. Дополнительные сведения о Windows PowerShell см. в статье [Приступая к работе с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Add-AzureAccount, введите:

	Get-Help Add-AzureAccount -Detailed

## Простой скрипт Azure PowerShell для кэша Redis  ##

Следующий скрипт демонстрирует создание, обновление и удаление кэша Redis для Azure.

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## Дальнейшие действия
Дополнительные сведения об использовании Windows Azure PowerShell см. в статье:
 
- [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): сведения об использовании командлетов в модуле AzureResourceManager.
- [Управление ресурсами Azure с помощью групп ресурсов](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups): узнайте, как создавать группы ресурсов на портале управления Azure и управлять ими.
- [Блог Azure](http://blogs.msdn.com/windowsazure): сведения о новых возможностях в Azure.
- [Блог Windows PowerShell](http://blogs.msdn.com/powershell): сведения о новых возможностях в Windows PowerShell.
- [Блог "Эй, сценарист!" ](http://blogs.technet.com/b/heyscriptingguy/): реальные советы и рекомендации от сообщества Windows PowerShell.




<!--HONumber=49-->