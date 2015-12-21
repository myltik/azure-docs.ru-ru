<properties
 pageTitle="Управление кэшем Redis для Azure с использованием Azure PowerShell | Microsoft Azure"
 description="Узнайте, как осуществлять администрирование кэша Redis для Azure с помощью Azure PowerShell."
 services="redis-cache"
   documentationCenter=""
   authors="Rick-Anderson"
   manager="wpickett"
   editor="v-lincan"/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple"
   ms.date="12/03/2015"
   ms.author="riande"/>

# Управление кэшем Redis для Azure с использованием Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

В этом разделе показано, как создавать, обновлять и удалять кэш Redis для Azure.

## Предварительные требования ##

Чтобы использовать Windows PowerShell с диспетчером ресурсов Azure, необходимо следующее:

- Windows PowerShell версии 3.0 или 4.0. Чтобы найти версию Windows PowerShell, введите `$PSVersionTable` и убедитесь, что значение `PSVersion` — 3.0 или 4.0. Чтобы установить совместимую версию, см. статью [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- Azure PowerShell версии 0.0.8 или более поздней. Чтобы установить последнюю версию и связать ее со своей подпиской Azure, см. статью [Как установить и настроить Azure PowerShell](../powershell-install-configure.md);

Этот учебник предназначен для начинающих пользователей Windows PowerShell. Дополнительные сведения о Windows PowerShell см. в разделе [Начало работы с Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Чтобы получить подробную справку для любого командлета, встречающегося в этом учебнике, используйте командлет Get-Help.

	Get-Help <cmdlet-name> -Detailed

Например, чтобы получить справку для командлета Add-AzureAccount, введите:

	Get-Help Add-AzureAccount -Detailed

## Простой скрипт Azure PowerShell для кэша Redis  ##

Следующий скрипт демонстрирует создание, обновление и удаление кэша Redis для Azure.

		
		$VerbosePreference = "Continue"

    	# Create a new cache with date string to make name unique.
		$cacheName = "MovieCache" + $(Get-Date -Format ('ddhhmm'))
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache service is provisioned.
		
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
		
		# Update the access keys.
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -Name $cacheName -ResourceGroupName $resourceGroupName -RedisConfiguration @{"maxmemory-policy" = "AllKeys-LRU"}
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force

## Дальнейшие действия

Дополнительные сведения об использовании Windows PowerShell с Azure см. в следующих ресурсах:

- [Документация по командлету кэша Redis для Azure на MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Командлеты диспетчера ресурсов Azure](http://go.microsoft.com/fwlink/?LinkID=394765): узнайте, как использовать командлеты в модуле диспетчера ресурсов Azure.
- [Использование групп ресурсов для управления ресурсами Azure](../azure-portal/resource-group-portal.md): узнайте, как создавать группы ресурсов и управлять ими на портале Azure.
- [Блог Azure](http://blogs.msdn.com/windowsazure): узнайте о новых возможностях в Azure.
- [Блог Windows PowerShell](http://blogs.msdn.com/powershell): узнайте о новых возможностях в Windows PowerShell.
- [Блог "Hey, Scripting Блог](http://blogs.technet.com/b/heyscriptingguy/): реальные советы и рекомендации от сообщества Windows PowerShell.

<!---HONumber=AcomDC_1210_2015-->