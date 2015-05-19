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
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Создание группы доступности с использованием шаблонов диспетчера ресурсов Azure

Вы можете с легкостью создать группу доступности для виртуальной машины с помощью Azure PowerShell или xplat-cli и шаблона диспетчера ресурсов. Этот шаблон позволяет создать группу доступности.
 
Перед началом работы убедитесь, что Azure PowerShell и xplat-cli настроены и готовы к работе.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## [Выполнение действия] с использованием шаблона диспетчера ресурсов с помощью Azure PowerShell

Выполните следующие шаги, чтобы [выполнить действие] с использованием шаблона диспетчера ресурсов в репозитории шаблонов Github с помощью Azure PowerShell.

### Шаг 1. Скачивание JSON-файла

Назначьте локальную папку в качестве расположения для JSON-файлов шаблонов и создайте ее \(например, с путем C:\\Azure\\Templates\[объект\_создания\]\).

Замените имя папки, а затем скопируйте его и выполните следующие команды.

	$folderName="<folder name, such as C:\Azure\Templates\[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Шаг 2. Просмотр параметров \(необязательно\)

Если вам необходимо [выполнить действие] с использованием шаблона, следует указать набор параметров конфигурации. Чтобы просмотреть параметры, которые необходимо указать для шаблона в локальном JSON-файле, перед выполнением команды для создания виртуальной машины, откройте JSON-файл через инструмент или текстовый редактор по своему усмотрению. В верхней части файла найдите раздел parameters. Он содержит список параметров, необходимых шаблону для настройки виртуальной машины. Вот как выглядит раздел **parameters** шаблона azuredeploy.json:

[Примечание для авторов. Вставьте раздел parameters шаблона azuredeploy.json и форматируйте его как код.]

### Шаг 3. Получение [информации, необходимой для завершения шаблона]

[Примечание для авторов. Дополнительный раздел для получения значений параметров, если они необходимы.]

### Шаг 4. [Выполнение действия] с использованием шаблона

Введите имена развертывания Azure и группы ресурсов, а также расположение Azure и имя папки, в которой находится сохраненный JSON-файл, и выполните следующие команды.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\[thing]>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

При выполнении команды **New-AzureResourceGroupDeployment** отобразится запрос на ввод значений для параметров раздела **parameters**, который находится в JSON-файле. После того, как вы введете значения, команда создаст группу ресурсов и группу доступности.

Вот пример набора команд PowerShell для шаблона.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates\[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Должно отобразиться примерно следующее:

[Примечание для авторов. Вставьте изображение PowerShell, где запрашиваются несколько первых параметров, вместо этого:]

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Чтобы удалить эту группу ресурсов и все ее ресурсы \(учетную запись хранения, виртуальную машину и виртуальную сеть\), используйте следующую команду.

	Remove-AzureResourceGroup –Name "<resource group name>"


## [Выполнение действия] с использованием шаблона диспетчера ресурсов с помощью xplat-cli

Выполните следующие шаги, чтобы [выполнить действие] с использованием шаблона диспетчера ресурсов в репозитории шаблонов Github с помощью команд xplat-cli.

### Шаг 1. Скачивание JSON-файла для шаблона

Назначьте локальную папку в качестве расположения для JSON-файлов шаблонов и создайте ее \(например, с путем C:\\Azure\\Templates\[объект\_создания\]\).

Введите имя папки, а затем скопируйте его и выполните следующие команды.

[команды xplat для скачивания файла шаблона]

### Шаг 2. Просмотр параметров шаблона \(необязательно\)

Если вам необходимо [выполнить действие] с использованием шаблона, следует указать набор параметров конфигурации. Чтобы просмотреть параметры, которые необходимо указать для шаблона в локальном JSON-файле, перед выполнением команды для создания виртуальной машины, откройте JSON-файл через инструмент или текстовый редактор по своему усмотрению. В верхней части файла найдите раздел parameters. Он содержит список параметров, необходимых шаблону для настройки виртуальной машины. Вот как выглядит раздел **parameters** шаблона azuredeploy.json:

[Примечание для авторов. Вставьте раздел parameters шаблона azuredeploy.json и форматируйте его как код.]

### Шаг 3. Получение [информации, необходимой для завершения шаблона]

[Примечание для авторов. Дополнительный раздел для получения значений параметров, если они необходимы.]

### Шаг 4. [Выполнение действия] с использованием шаблона

Введите \[необходимая информация} и выполните следующие команды.

[команды xplat для запуска файла шаблона]

[описание того, как xplat запускает шаблон]


Вот пример набора команд xplat-cli для шаблона.

[пример команды xplat]

Должно отобразиться примерно следующее:

[Примечание для авторов. Вставьте изображение xplat, где запрашиваются несколько первых параметров]


Чтобы удалить эту группу ресурсов и все ее ресурсы \([состав группы ресурсов]\), используйте следующую команду.

[команда xplat]




<!--HONumber=52-->
