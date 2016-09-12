.<properties
	pageTitle="Перенос ресурсов IaaS из классического развертывания в развертывание с помощью Azure Resource Manager с использованием Azure PowerShell | Microsoft Azure"
	description="В этой статье последовательно описывается поддерживаемый платформой перенос ресурсов из классической модели в модель Azure Resource Manager с помощью сценариев PowerShell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="danlep"/>

# Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell

Ниже последовательно описано, как использовать команды Azure PowerShell для переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager. В инструкциях вам следует заполнять пробелы параметрами своей пользовательской среды. Подставьте в команды собственные значения для переменных (в строках, которые начинаются со знака $).

## Шаг 1. Подготовка к переносу

Ниже приведено несколько рекомендаций для оценки переноса ресурсов IaaS из классической модели в модель Resource Manager.

- Прочитайте [список неподдерживаемых конфигураций и компонентов](virtual-machines-windows-migration-classic-resource-manager.md). Если у вас есть виртуальные машины, которые используют неподдерживаемые конфигурации или компоненты, мы рекомендуем отложить перенос до того момента, пока не будет заявлено об их поддержке. Также вы можете удалить такую функцию или вынести ее за пределы конфигурации, чтобы выполнить перенос.
-	Если у вас есть текущие автоматизированные сценарии, которые развертывают инфраструктуру и приложения, попробуйте создать аналогичную программу установки для миграции с помощью этих сценариев. Вы можете также настроить примеры среды с помощью портала Azure.

## Шаг 2. Установка последней версии Azure PowerShell

Есть два основных способа установки — с помощью [коллекции PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) и [установщика веб-платформы (WebPI)](http://aka.ms/webpi-azps). Обновления для установщика веб-платформы выпускаются ежемесячно. Обновления для коллекции PowerShell выпускаются на постоянной основе.

Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Шаг 3. Настройка подписки и регистрация для миграции

Сначала запустите командную строку PowerShell. Для сценариев миграции следует настроить среду для классической модели и модели Resource Manager.

Войдите в учетную запись для модели Resource Manager.

	Login-AzureRmAccount

Получите доступные подписки с помощью следующей команды.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Задайте подписку Azure для текущего сеанса. Замените все содержимое внутри кавычек, включая знаки < и >, правильными именами.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

>[AZURE.NOTE] Регистрация — однократное действие, но, прежде чем выполнять миграцию, вам нужно зарегистрироваться. Если вы не зарегистрируетесь, отобразится такое сообщение об ошибке:

>	*BadRequest : Subscription is not registered for migration.* 

Зарегистрируйтесь в поставщике ресурсов миграции с помощью такой команды:
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Подождите пять минут для завершения регистрации. Состояние утверждения регистрации можно проверить, выполнив следующую команду. Убедитесь, что RegistrationState имеет значение `Registered`, прежде чем продолжить.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Теперь войдите в учетную запись для классической модели.

	Add-AzureAccount

Получите доступные подписки с помощью следующей команды.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Задайте подписку Azure для текущего сеанса. Замените все содержимое внутри кавычек, включая знаки < и >, правильными именами.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## Шаг 4. Выполнение команд для переноса ресурсов IaaS

>[AZURE.NOTE] Все операции, описанные здесь, являются идемпотентными. Если вы столкнетесь с какой-либо проблемой, не связанной с неподдерживаемой функцией или ошибкой конфигурации, мы рекомендуем повторить подготовку, прервать или зафиксировать текущую операцию. Платформа попытается повторить это действие.

### Перенос виртуальных машин в облачную службу (не в виртуальную сеть)

Получите список облачных служб, выполнив следующую команду, а затем выберите облачную службу для переноса. Если виртуальные машины в облачной службе размещены в виртуальной сети или им назначены веб-роли или рабочие роли, то команда возвращает сообщение об ошибке.

	Get-AzureService | ft Servicename

Получите имя развертывания для облачной службы, используя следующую команду.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Подготовьте к переносу виртуальные машины в облачной службе. Возможно два варианта.

1. Вы хотите перенести виртуальные машины в виртуальную сеть, автоматически созданную платформой.

	Во-первых, проверьте возможность переноса облачной службы с помощью следующей команды:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

2. Вы хотите перенести виртуальные машины в существующую виртуальную сеть в модели развертывания с помощью Resource Manager.

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	Во-первых, проверьте возможность переноса облачной службы с помощью следующей команды:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

Когда операция подготовки успешно завершится, вы сможете запросить состояние переноса виртуальных машин, чтобы убедиться, что все они находятся в состоянии `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Проверьте конфигурацию для подготовленных ресурсов с помощью PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, используйте следующую команду.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### Перенос виртуальных машин в виртуальную сеть

Выберите виртуальную сеть, в которую будете переносить ресурсы.

	$vnetName = "VNET-Name"

>[AZURE.NOTE] Если в виртуальной сети есть виртуальные машины, веб-роли или рабочие роли с неподдерживаемыми конфигурациями, то отображается сообщение об ошибке проверки.

Во-первых, проверьте возможность переноса виртуальной сети с помощью следующей команды:

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Проверьте конфигурацию для подготовленных виртуальных машин с помощью PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, используйте следующую команду.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Перенос учетной записи хранения

После переноса виртуальных машин рекомендуется перенести учетную запись хранения.

Подготовьте учетную запись хранения к переносу, используя следующую команду:

	$storageAccountName = "storagename"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Проверьте конфигурацию для подготовленной учетной записи с помощью PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, используйте следующую команду.

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Дальнейшие действия

- [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Клонирование классической виртуальной машины в Azure Resource Manager с помощью сценариев PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0831_2016-->