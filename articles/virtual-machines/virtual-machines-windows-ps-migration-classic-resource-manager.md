<properties
	pageTitle="Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell"
	description="В этой статье рассматриваются возможности поддерживаемой платформой службы миграции из управления службами в Azure Resource Manager с помощью скриптов PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell

В документе описано, как использовать команды Azure PowerShell для переноса ресурсов IaaS из классической модели в модель Resource Manager. В инструкциях вам следует заполнять пробелы параметрами своей пользовательской среды. Для этого достаточно подставить в команды собственные значения для переменных (в строках, которые начинаются с символа $).

## Шаг 1. Подготовка к переносу

Ниже приведено несколько рекомендаций для этапа оценки переноса ресурсов IaaS из классической модели в модель Resource Manager.

- Список неподдерживаемых конфигураций и функций см. [здесь](virtual-machines-windows-migration-classic-resource-manager.md). Если у вас есть виртуальные машины, которые используют неподдерживаемые конфигурации или функции, мы рекомендуем отложить перенос до того момента, пока не будет заявлено об их поддержке. Также вы можете удалить такую функцию или вынести ее за пределы конфигурации, чтобы выполнить перенос.
-	Если у вас уже есть автоматизированные скрипты для развертывания инфраструктуры и приложений, попробуйте создать с их помощью тестовую среду с аналогичной конфигурацией для проверки процесса переноса. Также вы можете использовать среду, созданную с помощью образцов на портале Azure.
- Так как служба работает в режиме общедоступной предварительной версии, тестовая среда для переноса должна быть изолирована от рабочей среды. Не используйте в тестовой и рабочей средах одни и те же учетные записи хранения, виртуальные сети или другие общие ресурсы.

## Шаг 2. Установка последней версии Azure PowerShell

Есть два основных способа установки — с помощью [коллекции PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) или [установщика веб-платформы](http://aka.ms/webpi-azps). Обновления для установщика веб-платформы будут выпускаться ежемесячно. Обновления для коллекции PowerShell будут выпускаться на постоянной основе.

Дополнительные сведения см. в статье [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/).

## Шаг 3. Настройка подписки и регистрация для использования общедоступной предварительной версии миграции

Сначала запустите командную строку PowerShell. Для сценариев миграции следует настроить среду для классической модели и модели Resource Manager.

Войдите в учетную запись для модели Resource Manager.

	Login-AzureRmAccount

Получите доступные подписки с помощью следующей команды.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Задайте подписку Azure для текущего сеанса. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Зарегистрируйтесь для использования общедоступной предварительной версии с помощью следующей команды.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Подождите пять минут для завершения регистрации. Состояние регистрации можно проверить, выполнив следующую команду. Прежде чем продолжить, убедитесь, что параметр RegistrationState получил значение Registered.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Теперь войдите в учетную запись для классической модели.

	Add-AzureAccount

Получите доступные подписки с помощью следующей команды.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Задайте подписку Azure для текущего сеанса. Замените все содержимое внутри кавычек, включая символы < and >, на правильные имена.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## Шаг 4. Команды для переноса ресурсов IaaS

>[AZURE.NOTE] Обратите внимание, что все описанные ниже действия являются идемпотентными. Если вы столкнетесь с любой проблемой, кроме сообщений о неподдерживаемых функциях или ошибках конфигурации, мы рекомендуем повторить подготовку, отменить или зафиксировать текущую операцию. Платформа автоматически повторит попытку.

### Перенос виртуальных машин в облачную службу (не в виртуальную сеть)

Получите список облачных служб, выполнив следующую команду, а затем выберите облачную службу для переноса. Обратите внимание: если виртуальные машины в облачной службе входят в виртуальную сеть или для них назначены веб-роли или рабочие роли, вы получите сообщение об ошибке.

	Get-AzureService | ft Servicename

Получите имя развертывания для облачной службы, используя следующую команду.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Подготовьте к переносу виртуальные машины в облачной службе. Здесь у вас есть два варианта.

Если вы хотите перенести виртуальные машины в виртуальную сеть, автоматически созданную платформой, используйте следующую команду.

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Если перенос будет осуществляться в существующую виртуальную сеть с помощью модели развертывания Resource Manager, используйте следующую команду.

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

Когда операция подготовки успешно завершится, вы можете получить состояние миграции виртуальной машины, чтобы проверить, что все виртуальные машины находятся в состоянии Prepared.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Проверьте конфигурацию подготовленных ресурсов с помощью PowerShell или портала Azure. Если вы не готовы к миграции и решите вернуться к прежнему состоянию, используйте следующую команду.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### Перенос виртуальных машин в виртуальную сеть

Выберите виртуальную сеть, в которую будете переносить ресурсы. Обратите внимание: если в виртуальной сети есть виртуальные машины, веб-роли или рабочие роли с неподдерживаемой конфигурацией, вы получите сообщение об ошибке проверки.

Подготовьте виртуальную сеть для миграции, используя следующую команду.

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Проверьте конфигурацию подготовленных виртуальных машин с помощью PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуться к прежнему состоянию, используйте следующую команду.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Если подготовленная конфигурация вас устраивает, можете продолжать процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## Ссылки

- [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
- [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Клонирование классической виртуальной машины в Azure Resource Manager с помощью сценариев PowerShell](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0511_2016-->