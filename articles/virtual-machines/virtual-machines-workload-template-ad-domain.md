<properties
	pageTitle="Шаблон ARM высокодоступных доменных служб Active Directory | Microsoft Azure"
	description="Информация о том, как легко развернуть два сервера в качестве контроллеров домена доменных служб Active Directory с помощью шаблона диспетчера ресурсов и портала предварительной версии Azure, Azure PowerShell или Azure CLI."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>


# Развертывание высокодоступного домена доменных служб Active Directory с помощью шаблона диспетчера ресурсов Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания. Создать ресурс с помощью классической модели развертывания невозможно.

Используйте указания в этой статье для развертывания высокодоступного домена Active Directory с помощью шаблона диспетчера ресурсов. Этот шаблон создает две виртуальные машины в новой виртуальной сети в той же подсети.

![](./media/virtual-machines-workload-template-ad-domain/two-server-ad.png)

Шаблон можно запустить с помощью портала предварительной версии Azure, Azure PowerShell или интерфейса командной строки Azure.

## Портал предварительной версии Azure

Для развертывания этой рабочей нагрузки с помощью шаблона диспетчера ресурсов и портала предварительной версии Azure щелкните [здесь](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-ad-domain/azure-portal-template.png)

1.	В области **Шаблон** нажмите кнопку **Сохранить**.
2.	Щелкните **Параметры**. В области **Параметры** введите новые значения, выберите среди допустимых значений или примите значения по умолчанию, а затем нажмите кнопку **ОК**.
3.	При необходимости щелкните **Подписка** и выберите правильную подписку Azure.
4.	Щелкните **Группа ресурсов** и выберите существующую группу ресурсов. Или щелкните **Или создать новую**, чтобы создать группу для этой рабочей нагрузки.
5.	При необходимости щелкните **Расположение группы ресурсов** и укажите правильное расположение Azure.
6.	При необходимости выберите **Юридические условия**, чтобы ознакомиться с условиями и соглашением об использовании шаблона.
7.	Щелкните **Создать**.

В зависимости от шаблона для создания рабочей нагрузки в Azure может потребоваться некоторое время. По завершении выполнения шаблона вы получите новый двухсерверный домен Active Directory в существующей или новой группе ресурсов.

## Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Введите имя развертывания Azure, новое имя группы ресурсов и расположение центра обработки данных Azure в следующем наборе команд. Удалите все содержимое кавычек, включая символы < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Ниже приведен пример.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json"
	New-AzureRmResourceGroup -Name $RGName -Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Далее выполните свой блок команд в командной строке Azure PowerShell.

При выполнении команды **New-AzureRmResourceGroupDeployment** появится запрос на ввод значений для ряда параметров. После указания всех значений параметров команда **New-AzureRmResourceGroupDeployment** создает и настраивает виртуальные машины.

По завершении выполнения шаблона вы получите новую конфигурацию двухсерверного домена Active Directory в новой группе ресурсов.

## Инфраструктура CLI Azure

Прежде чем начать, убедитесь, что у вас установлена подходящая версия интерфейса командной строки Azure, выполнен вход и включен новый режим диспетчера ресурсов. Дополнительные сведения см. [здесь](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

В первую очередь создается новая группа ресурсов. Используйте следующую команду и укажите имя группы и местоположение центра обработки данных Azure, в который требуется выполнить развертывание.

	azure group create <group name> <location>

Затем используйте следующую команду и укажите имя группы ресурсов и имя развертывания Azure.

	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json <group name> <deployment name>

Ниже приведен пример.

	azure group create adtestbed eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/active-directory-new-domain-ha-2-dc/azuredeploy.json adtestbed wldevtest

При выполнении команды **azure group deployment create** появится запрос на ввод значений для ряда параметров. После указания всех значений параметров Azure создает и настраивает виртуальные машины.

По завершении выполнения шаблона вы получите новый двухсерверный домен доменных служб Active Directory в новой группе ресурсов.


## Дополнительные ресурсы

[Развертывание виртуальных машин и управление ими с использованием шаблонов диспетчера ресурсов Azure и Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Поставщики вычислительных и сетевых ресурсов, а также ресурсов хранения Azure в диспетчере ресурсов Azure](virtual-machines-azurerm-versus-azuresm.md)

[Общие сведения о диспетчере ресурсов Azure](../resource-group-overview.md)

[Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Документация по виртуальным машинам](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Установка и настройка Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Oct15_HO4-->