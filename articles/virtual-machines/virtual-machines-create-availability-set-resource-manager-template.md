<properties
	pageTitle="Создание группы доступности с использованием шаблонов диспетчера ресурсов Azure"
	description="Описывает, как использовать шаблон группы доступности, и содержит синтаксис шаблона"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="kathydav"/>

# Создание группы доступности с использованием шаблонов диспетчера ресурсов Azure

Вы можете легко создать группу доступности для виртуальной машины с помощью Azure PowerShell или командной строки Azure и шаблона диспетчера ресурсов. Этот шаблон позволяет создать группу доступности.

Перед началом работы убедитесь, что Azure PowerShell и Azure CLI настроены и готовы к работе.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## Создание группы доступности с помощью шаблона диспетчера ресурсов

Выполните указанные ниже действия, чтобы с помощью Azure PowerShell создать группу доступности для виртуальной машины, используя шаблон диспетчера ресурсов в репозитории шаблонов Github.

### Шаг 1. Скачивание JSON-файла

Назначьте локальную папку в качестве расположения для файлов шаблонов JSON и создайте ее (например, с путем C:\Azure\Templates\availability).

Замените имя папки, а затем скопируйте его и выполните следующие команды.

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

### Шаг 2. Сбор сведений для обязательных параметров

При использовании шаблона вам потребуется указать некоторые сведения, например расположение и имя набора. Чтобы узнать, какие параметры являются обязательными для шаблона, выполните одно из указанных ниже действий.

- Просмотрите список параметров [здесь](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/).
- Откройте JSON-файл в каком-нибудь средстве или текстовом редакторе по вашему усмотрению. В верхней части файла найдите раздел parameters. Он содержит список параметров, необходимых шаблону для настройки виртуальной машины.

Соберите сведения, которые необходимо ввести. При выполнении команды развертывания шаблона вам будет предложено ввести эти сведения.

### Шаг 3. Создание группы доступности

В разделах ниже показано, как сделать это с использованием Azure PowerShell и интерфейса командной строки Azure.

### Использование Azure PowerShell

Введите имена развертывания Azure и группы ресурсов, а также расположение Azure и имя папки, в которой находится сохраненный JSON-файл, и выполните следующие команды.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

При выполнении команды **New-AzureResourceGroupDeployment** вам будет предложено ввести значения параметров в разделе **parameters** JSON-файла. После того как вы введете значения, команда создаст группу ресурсов и группу доступности.

Вот пример набора команд PowerShell для шаблона.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Должно отобразиться примерно следующее:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Чтобы удалить эту группу ресурсов и все ее ресурсы (учетную запись хранения, виртуальную машину и виртуальную сеть), используйте следующую команду.

	Remove-AzureResourceGroup –Name "<resource group name>"


## Использование интерфейса командной строки Azure

Выполните эти действия, чтобы создать группу доступности с использованием шаблона диспетчера ресурсов в репозитории шаблонов Github с помощью команды интерфейса командной строки Azure.

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json

<!---HONumber=July15_HO4-->