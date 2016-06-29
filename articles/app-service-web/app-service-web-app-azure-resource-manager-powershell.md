<properties
	pageTitle="Команды Azure PowerShell на основе Azure Resource Manager для веб-приложений Azure | Microsoft Azure"
	description="Узнайте, как использовать новые команды PowerShell на основе Azure Resource Manager для управления веб-приложениями Azure."
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="aelnably"/>

# Управление веб-приложениями Azure с помощью PowerShell на основе Azure Resource Manager#

В версии Microsoft Azure PowerShell 1.0.0 добавлены новые команды, которые позволяют использовать команды PowerShell на основе Azure Resource Manager для управления веб-приложениями.

Дополнительные сведения об управлении группами ресурсов см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

Полный список параметров и возможностей для командлетов PowerShell на основе Azure Resource Manager для управления веб-приложениями см. [здесь](https://msdn.microsoft.com/library/mt619237.aspx).

## Управление планами службы приложений ##

### Создание плана службы приложений ###
Чтобы создать новый план службы приложений, используйте командлет **New-AzureRmAppServicePlan**.

Ниже описаны его параметры.

- 	**Name**: имя плана службы приложений.
- 	**Location**: расположение плана службы.
- 	**ResourceGroupName**: группа ресурсов, в которую входит новый план службы приложений.
- 	**Tier**: желаемая ценовая категория. По умолчанию используется значение Free; также допустимы варианты Shared, Basic, Standard и Premium.
- 	**WorkerSize**: размер рабочих ролей. По умолчанию Small, если для параметра Tier указано значение Basic, Standard или Premium; также допустимы варианты Medium и Large.
- 	**NumberofWorkers**: число рабочих служб в плане службы приложений. По умолчанию имеет значение 1. 

Пример использования этого командлета:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### Создание плана службы приложений в среде служб приложений ###
Чтобы создать план службы приложений в среде службы приложений, вы можете использовать уже известную вам команду **New-AzureRmAppServicePlan** с дополнительными параметрами. Эти параметры определяют имя среды службы приложений и имя группы ресурсов, к которой она принадлежит.

Пример использования этого командлета:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Дополнительные сведения о среде службы приложений см. в статье [Введение в среду службы приложений](app-service-app-service-environment-intro.md).

### Получение списка существующих планов службы приложений ###

Чтобы получить полный список существующих планов службы приложений, используйте командлет **Get-AzureRmAppServicePlan**.

Чтобы получить список всех планов службы приложений в конкретной подписке, используйте следующую команду:

    Get-AzureRmAppServicePlan

Чтобы получить список всех планов службы приложений для определенной группы ресурсов, используйте такую команду:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Чтобы получить сведения о конкретном плане службы приложений, используйте следующую команду:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### Настройка существующего плана службы приложений ###

Чтобы изменить параметры существующего плана службы приложений, используйте командлет **Set-AzureRmAppServicePlan**. Вы можете изменить ценовую категорию, размер и количество рабочих ролей.

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### Масштабирование плана службы приложений ####

Чтобы масштабировать существующий план службы приложений, используйте команду:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### Изменение размера рабочих ролей для плана службы приложений ####

Чтобы изменить размер рабочих ролей для существующего плана службы приложений, используйте следующую команду:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### Изменение ценовой категории для плана службы приложений ####

Чтобы изменить ценовую категорию для существующего плана службы приложений, используйте следующую команду:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### Удаление существующего плана службы приложений ###

Чтобы удалить существующий план службы приложений, сначала следует переместить или удалить из него все назначенные веб-приложения. После этого удалите план службы приложений с помощью командлета **Remove-AzureRmAppServicePlan**.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## Управление веб-приложениями службы приложений ##

### Создание нового веб-приложения ###

Чтобы создать новое веб-приложение, используйте командлет **New-AzureRmWebApp**.

Ниже описаны его параметры.

- **Name**: имя нового веб-приложения.
- **AppServicePlan**: имя плана службы приложений, в котором будет размещено веб-приложение.
- **ResourceGroupName**: группа ресурсов, в которую входит этот план службы приложений.
- **Location**: расположение веб-приложения.

Пример использования этого командлета:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### Создание веб-приложения в среде службы приложений ###

Чтобы создать веб-приложение в среде службы приложений, вы можете использовать уже известную вам команду **New-AzureRmWebApp** с дополнительными параметрами. Эти параметры определяют имя среды службы приложений и имя группы ресурсов, к которой она принадлежит.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Дополнительные сведения о среде службы приложений см. в статье [Введение в среду службы приложений](app-service-app-service-environment-intro.md).

### Удаление существующего веб-приложения ###

Чтобы удалить существующее веб-приложение, можно использовать командлет **Remove-AzureRmWebApp**, указав имя веб-приложения и имя группы ресурсов.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### Список существующих веб-приложений ###

Чтобы получить список существующих веб-приложений, используйте командлет **Get-AzureRmWebApp**.

Чтобы получить список всех веб-приложений в вашей подписке, используйте следующую команду:

    Get-AzureRmWebApp

Чтобы получить список всех веб-приложений для определенной группы ресурсов, используйте такую команду:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Чтобы получить сведения о конкретном веб-приложении, используйте следующую команду:

    Get-AzureRmWebApp -Name ContosoWebApp

### Настройка существующего веб-приложения ###

Чтобы изменить параметры и конфигурацию существующего веб-приложения, используйте командлет **Set-AzureRmWebApp**. Полный список параметров см. на [странице описания командлета](https://msdn.microsoft.com/library/mt652487.aspx).

Пример 1. Использование командлета для изменения строк подключения:

	$connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Пример 2. Пример добавления параметров приложения:

	$appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Пример 3. Настройка веб-приложения для запуска в 64-разрядном режиме:

	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### Изменение состояния существующего веб-приложения ###

#### Перезапуск веб-приложения ####

Чтобы перезапустить веб-приложение, нужно указать имя веб-приложения и его группу ресурсов.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Остановка веб-приложения ####

Чтобы остановить веб-приложение, нужно указать имя веб-приложения и его группу ресурсов.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Запуск веб-приложения ####

Чтобы запустить веб-приложение, нужно указать имя веб-приложения и его группу ресурсов.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Управление профилями публикации веб-приложений ###

У каждого веб-приложения есть профиль публикации, который можно использовать для публикации приложений. Эти профили поддерживают несколько операций.

#### Получение профиля публикации ####

Чтобы получить профиль публикации для веб-приложения, используйте следующую команду:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Обратите внимание, что команда выводит профиль публикации в командную строку и текстовый файл.

#### Сброс профиля публикации ####

Чтобы сбросить для веб-приложения пароль публикации для FTP и веб-развертывания, используйте такую команду:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Управление сертификатами веб-приложения ###

Дополнительные сведения об управлении сертификатами веб-приложения см. в статье [Привязка SSL-сертификатов с помощью PowerShell](app-service-web-app-powershell-ssl-binding.md).



### Дальнейшие действия ###
- Сведения о поддержке PowerShell в Azure Resource Manager см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).
- Сведения о средах службы приложений см. в статье [Введение в среду службы приложений](app-service-app-service-environment-intro.md).
- Сведения об управлении SSL-сертификатами службы приложений с помощью PowerShell см. в статье [Привязка SSL-сертификатов с помощью PowerShell](app-service-web-app-powershell-ssl-binding.md).
- Полный список командлетов PowerShell на основе Azure Resource Manager, которые можно использовать для управления веб-приложениями, см. [здесь](https://msdn.microsoft.com/library/mt619237.aspx).

<!---HONumber=AcomDC_0615_2016-->