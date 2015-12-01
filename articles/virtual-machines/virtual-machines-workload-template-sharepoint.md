<properties
	pageTitle="Развертывание ферм SharePoint с помощью шаблонов ARM | Microsoft Azure"
	description="Простое развертывание трех- или девятисерверной фермы SharePoint с помощью шаблонов диспетчера ресурсов и портала предварительной версии Azure, Azure PowerShell или интерфейса командной строки Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Развертывание ферм SharePoint с помощью шаблонов диспетчера ресурсов Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания. Создать ресурс с помощью классической модели развертывания невозможно.

Используйте указания в этой статье для развертывания новой трех- или девятисерверной фермы SharePoint Server 2013 с помощью шаблонов диспетчера ресурсов.

## Развертывание трехсерверной фермы SharePoint

Для базовой фермы SharePoint Server 2013 шаблон диспетчера ресурсов создает три виртуальные машины в новой виртуальной сети в трех разных подсетях.

![](./media/virtual-machines-workload-template-sharepoint/three-server-sharepoint-farm.png)

Шаблон можно запустить с помощью портала предварительной версии Azure, Azure PowerShell или интерфейса командной строки Azure.

> [AZURE.NOTE]Кроме того, эту конфигурацию можно создать с помощью элемента [SharePoint 2013 non-HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/) в Azure Marketplace на портале предварительной версии Azure.

### Портал предварительной версии Azure

Чтобы развернуть эту рабочую нагрузку с помощью шаблона диспетчера ресурсов и портала предварительной версии Azure, щелкните [здесь](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Щелкните элемент **Параметры**. В области **Параметры** введите новые значения, выберите нужные из допустимых значений или примите значения по умолчанию, а затем нажмите кнопку **ОК**.
2.	При необходимости щелкните элемент **Подписка** и выберите нужную подписку Azure.
3.	Щелкните элемент **Группа ресурсов** и выберите существующую группу ресурсов. Либо щелкните элемент **Или создайте новую**, чтобы создать группу для этой рабочей нагрузки.
4.	При необходимости щелкните элемент **Расположение группы ресурсов** и укажите правильное расположение в Azure.
6.	Выберите элемент **Юридические условия**, чтобы ознакомиться с условиями и соглашением об использовании шаблона, и нажмите кнопку **Купить**.
7.	Щелкните **Создать**.

В зависимости от шаблона для создания рабочей нагрузки в Azure может потребоваться некоторое время. По завершении вы получите новую трехсерверную ферму SharePoint в существующей или новой группе ресурсов.

### Azure PowerShell

> [AZURE.NOTE]Эта статья содержит команды для предварительной версии Azure PowerShell 1.0. Для запуска этих команд в Azure PowerShell 0.9.8 и более ранних версиях замените значение **New-AzureRMResourceGroup** значением **New-AzureResourceGroup**, значение **New-AzureRMResourceGroupDeployment** — значением **New-AzureResourceGroupDeployment** и добавьте команду **Switch-AzureMode AzureResourceManager** перед командой **New-AzureResourceGroup**. Дополнительные сведения см. в статье [Предварительная версия Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

Введите имя развертывания Azure, новое имя группы ресурсов и расположение центра обработки данных Azure в следующем наборе команд. Удалите все содержимое кавычек, включая символы < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Ниже приведен пример.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Далее выполните свой блок команд в командной строке Azure PowerShell.

При выполнении команды **New-AzureRMResourceGroupDeployment** появится запрос на ввод значений для ряда параметров. После указания всех значений параметров команда **New-AzureRMResourceGroupDeployment** создает и настраивает виртуальные машины.

По завершении выполнения шаблона вы получите новую трехсерверную ферму SharePoint в новой группе ресурсов.

### Инфраструктура CLI Azure

Прежде чем начать, убедитесь, что у вас установлена подходящая версия интерфейса командной строки Azure, выполнен вход и включен новый режим диспетчера ресурсов. Дополнительные сведения см. [здесь](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

В первую очередь создается новая группа ресурсов. Используйте следующую команду и укажите имя группы и местоположение центра обработки данных Azure, в который требуется выполнить развертывание.

	azure group create <group name> <location>

Затем используйте следующую команду и укажите имя группы ресурсов и имя развертывания Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json <group name> <deployment name>

Ниже приведен пример.

	azure group create sp3serverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json sp3serverfarm spdevtest

При выполнении команды **azure group deployment create** появится запрос на ввод значений для ряда параметров. После указания всех значений параметров Azure создает и настраивает виртуальные машины.

Теперь у вас есть новая трехсерверная ферма SharePoint в новой группе ресурсов.

## Развертывание девятисерверной фермы SharePoint

Для широкодоступной фермы SharePoint Server 2013 шаблон диспетчера ресурсов создает девять виртуальных машин в новой виртуальной сети в четырех разных подсетях.

![](./media/virtual-machines-workload-template-sharepoint/nine-server-sharepoint-farm.png)

> [AZURE.NOTE]Кроме того, эту конфигурацию можно создать с помощью элемента [SharePoint 2013 HA Farm](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/) в Azure Marketplace на портале предварительной версии Azure.

### Портал предварительной версии Azure

Чтобы развернуть эту рабочую нагрузку с помощью шаблона диспетчера ресурсов и портала предварительной версии Azure, щелкните [здесь](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-server-farm-ha%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	Щелкните элемент **Параметры**. В области **Параметры** введите новые значения, выберите нужные из допустимых значений или примите значения по умолчанию, а затем нажмите кнопку **ОК**.
2.	При необходимости щелкните элемент **Подписка** и выберите нужную подписку Azure.
3.	Щелкните элемент **Группа ресурсов** и выберите существующую группу ресурсов. Либо щелкните элемент **Или создайте новую**, чтобы создать группу для этой рабочей нагрузки.
4.	При необходимости щелкните элемент **Расположение группы ресурсов** и укажите правильное расположение в Azure.
5.	Выберите элемент **Юридические условия**, чтобы ознакомиться с условиями и соглашением об использовании шаблона, и нажмите кнопку **Купить**.
6.	Щелкните **Создать**.

В зависимости от шаблона для создания рабочей нагрузки в Azure может потребоваться некоторое время. По завершении вы получите новую девятисерверную ферму SharePoint в существующей или новой группе ресурсов.

### Azure PowerShell

> [AZURE.NOTE]Эта статья содержит команды для предварительной версии Azure PowerShell 1.0. Для запуска этих команд в Azure PowerShell 0.9.8 и более ранних версиях замените значение **New-AzureRMResourceGroup** значением **New-AzureResourceGroup**, значение **New-AzureRMResourceGroupDeployment** — значением **New-AzureResourceGroupDeployment** и добавьте команду **Switch-AzureMode AzureResourceManager** перед командой **New-AzureResourceGroup**. Дополнительные сведения см. в статье [Предварительная версия Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

Введите имя развертывания Azure, новое имя группы ресурсов и расположение центра обработки данных Azure в следующем наборе команд. Удалите все содержимое кавычек, включая символы < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Ниже приведен пример.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Далее выполните свой блок команд в командной строке Azure PowerShell.

При выполнении команды **New-AzureRMResourceGroupDeployment** появится запрос на ввод значений для ряда параметров. После указания всех значений параметров команда **New-AzureRMResourceGroupDeployment** создает и настраивает виртуальные машины.

По завершении выполнения шаблона вы получите новую девятисерверную ферму SharePoint в новой группе ресурсов.

### Инфраструктура CLI Azure

Прежде чем начать, убедитесь, что у вас установлена подходящая версия интерфейса командной строки Azure, выполнен вход и включен новый режим диспетчера ресурсов. Дополнительные сведения см. [здесь](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

В первую очередь создается новая группа ресурсов. Используйте следующую команду и укажите имя группы и местоположение центра обработки данных Azure, в который требуется выполнить развертывание.

	azure group create <group name> <location>

Затем используйте следующую команду и укажите имя группы ресурсов и имя развертывания Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json <group name> <deployment name>

Ниже приведен пример.

	azure group create sphaserverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json sphaserverfarm spdevtest

При выполнении команды **azure group deployment create** появится запрос на ввод значений для ряда параметров. После указания всех значений параметров Azure создает и настраивает виртуальные машины.

По завершении выполнения шаблона вы получите новую девятисерверную ферму SharePoint Server 2013 в новой группе ресурсов.


## Дополнительные ресурсы

[Фермы SharePoint, размещенные в службах инфраструктуры Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Развертывание виртуальных машин и управление ими с использованием шаблонов диспетчера ресурсов Azure и Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)

[Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Установка и настройка Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=AcomDC_1125_2015-->