<properties
	pageTitle="Миграция в Resource Manager с помощью PowerShell | Microsoft Azure"
	description="В этой статье последовательно описывается поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью команд Azure PowerShell."
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
	ms.date="09/16/2016"
	ms.author="danlep"/>

# Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью Azure PowerShell

Ниже последовательно описано, как использовать команды Azure PowerShell для переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager. В инструкциях вам следует заполнять пробелы параметрами своей пользовательской среды. Подставьте в команды собственные значения для переменных (в строках, которые начинаются со знака $).

Если требуется, можно также перенести ресурсы с помощью [интерфейса командной строки Azure (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md).

Общие сведения о поддерживаемых сценариях миграции см. в разделе [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md). Подробное руководство и инструкции по миграции см. в разделе [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md).

## Шаг 1. Планирование миграции

Ниже приведено несколько рекомендаций для оценки переноса ресурсов IaaS из классической модели в модель Resource Manager.

- Ознакомьтесь с [поддерживаемыми и неподдерживаемыми компонентами и конфигурациями](virtual-machines-windows-migration-classic-resource-manager.md). Если у вас есть виртуальные машины, которые используют неподдерживаемые конфигурации или компоненты, мы рекомендуем отложить перенос до того момента, пока не будет заявлено об их поддержке. Также вы можете удалить такую функцию или вынести ее за пределы конфигурации, чтобы выполнить перенос.
-	Если у вас есть текущие автоматизированные сценарии, которые развертывают инфраструктуру и приложения, попробуйте создать аналогичную программу установки для миграции с помощью этих сценариев. Вы можете также настроить примеры среды с помощью портала Azure.

>[AZURE.IMPORTANT]В настоящее время не поддерживается перенос шлюзов виртуальной сети (шлюзов типа "сеть — сеть", Azure ExpressRoute, шлюзов приложений, шлюзов типа "точка — сеть") из классической модели в модель Resource Manager. Чтобы перенести классическую виртуальную сеть со шлюзом, удалите шлюз перед выполнением операции фиксации для перемещения сети. (Этап подготовки можно выполнить, не удаляя шлюз.) Затем, после завершения переноса, повторно подключите шлюз в Azure Resource Manager.

## Шаг 2. Установка последней версии Azure PowerShell

Есть два основных способа установки Azure PowerShell — с помощью [коллекции PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) и [установщика веб-платформы (WebPI)](http://aka.ms/webpi-azps). Обновления для установщика веб-платформы выпускаются ежемесячно. Обновления для коллекции PowerShell выпускаются на постоянной основе. В этой статье используется Azure PowerShell 2.1.0.

Инструкции по установке см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Шаг 3. Настройка подписки и регистрация для миграции

Сначала запустите командную строку PowerShell. Для сценариев миграции следует настроить среду для классической модели и модели Resource Manager.

Войдите в учетную запись для модели Resource Manager.

	Login-AzureRmAccount

Получите доступные подписки с помощью следующей команды.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Задайте подписку Azure для текущего сеанса. Замените все содержимое внутри кавычек, включая знаки < и >, правильным именем.

	Select-AzureRmSubscription –SubscriptionName "<subscription name>"

>[AZURE.NOTE] Регистрация — однократное действие, но, прежде чем выполнять миграцию, вам нужно зарегистрироваться. Если вы не зарегистрируетесь, отобразится такое сообщение об ошибке:

>	*BadRequest : Subscription is not registered for migration.* 

Выполните регистрацию в поставщике ресурсов миграции с помощью приведенной ниже команды.
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Подождите пять минут для завершения регистрации. Состояние утверждения регистрации можно проверить, выполнив следующую команду.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

Убедитесь, что RegistrationState имеет значение `Registered`, прежде чем продолжить.

Теперь войдите в учетную запись для классической модели.

	Add-AzureAccount

Получите доступные подписки с помощью следующей команды.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Задайте подписку Azure для текущего сеанса. Замените все содержимое внутри кавычек, включая знаки < и >, правильным именем.

	Select-AzureSubscription –SubscriptionName "<subscription name>"

## Шаг 4. Проверка наличия достаточного числа ядер виртуальной машины Azure Resource Manager в регионе Azure текущего развертывания или виртуальной сети

Чтобы проверить текущее количество ядер в Azure Resource Manager, можно использовать приведенную ниже команду PowerShell. Чтобы узнать больше о квотах ядер, ознакомьтесь с разделом [Ограничения и диспетчер ресурсов Azure](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

```
Get-AzureRmVMUsage -Location "<Your VNET or Deployment's Azure region"
```

## Шаг 5. Выполнение команд для переноса ресурсов IaaS

>[AZURE.NOTE] Все операции, описанные здесь, являются идемпотентными. Если вы столкнетесь с какой-либо проблемой, не связанной с неподдерживаемой функцией или ошибкой конфигурации, мы рекомендуем повторить подготовку, прервать или зафиксировать текущую операцию. Платформа попытается повторить это действие.

### Перенос виртуальных машин в облачную службу (не в виртуальную сеть)

Получите список облачных служб, выполнив следующую команду, а затем выберите облачную службу для переноса. Если виртуальные машины в облачной службе размещены в виртуальной сети или им назначены веб-роли или рабочие роли, то команда возвращает сообщение об ошибке.

	Get-AzureService | ft Servicename

Получите имя развертывания для облачной службы, используя следующую команду.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

Подготовьте к переносу виртуальные машины в облачной службе. Возможно два варианта.

* **Вариант 1. Миграция виртуальных машин в виртуальную сеть, созданную платформой**

	Во-первых, проверьте возможность переноса облачной службы с помощью следующей команды.

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

* **Вариант 2. Миграция виртуальных машин в существующую виртуальную сеть в модели развертывания с помощью Resource Manager**

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	Во-первых, проверьте возможность переноса облачной службы с помощью следующей команды:

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

После успешного завершения операции подготовки для одного из перечисленных выше вариантов запросите состояние миграции виртуальных машин. Убедитесь, что они находятся в состоянии `Prepared`.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

Проверьте конфигурацию для подготовленных ресурсов с помощью PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, выполните следующую команду.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName

### Перенос виртуальных машин в виртуальную сеть

Для миграции виртуальных машин в виртуальной сети переносится сама виртуальная сеть. Виртуальные машины автоматически переносятся вместе с ней. Выберите виртуальную сеть, в которую будете переносить ресурсы.

	$vnetName = "<Virtual Network Name>"

>[AZURE.NOTE] Если в виртуальной сети есть виртуальные машины, веб-роли или рабочие роли с неподдерживаемыми конфигурациями, то отображается сообщение об ошибке проверки.

Во-первых, проверьте возможность переноса виртуальной сети с помощью следующей команды.

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

Приведенная выше команда отображает все предупреждения и ошибки, которые мешают переносу. Если проверка выполнена успешно, то можно переходить к шагу подготовки ниже.
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

Проверьте конфигурацию для подготовленных виртуальных машин с помощью Azure PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, выполните следующую команду.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### Перенос учетной записи хранения

После миграции виртуальных машин рекомендуется перенести учетную запись хранения.

Подготовьте каждую учетную запись хранения к переносу, используя следующую команду.

	$storageAccountName = "<storage account name>"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

Проверьте конфигурацию для подготовленной учетной записи хранения с помощью Azure PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуть предыдущее состояние, выполните следующую команду.

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## Дальнейшие действия

- Дополнительные сведения о миграции см. в разделе [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md).
- Чтобы перенести дополнительные сетевые ресурсы в Resource Manager с помощью PowerShell, выполните аналогичные действия, используя **Move-AzureNetworkSecurityGroup**, **Move-AzureReservedIP** и **Move-AzureRouteTable**.
- Сценарии с открытым кодом, которые можно использовать для переноса ресурсов Azure из классической модели в модель Resource Manager, описаны в разделе [Инструменты сообщества для переноса ресурсов из управления службами Azure в Azure Resource Manager](virtual-machines-windows-migration-scripts.md).

<!---HONumber=AcomDC_0921_2016-->