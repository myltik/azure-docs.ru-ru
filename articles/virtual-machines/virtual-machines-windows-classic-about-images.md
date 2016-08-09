<properties
	pageTitle="Образы виртуальных машин Windows | Microsoft Azure"
	description="Узнайте о том, как использовать образы с виртуальными машинами Windows в Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Образы виртуальных машин

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## Работа с образами

Для управления образами, доступными в вашей подписке Azure, вы можете использовать модуль Azure PowerShell. Некоторые операции с образами можно выполнить на классическом портале Azure, однако командная строка предоставляет больше возможностей.


-	**Получить все образы**. `Get-AzureVMImage` возвращает список всех образов, доступных в вашей текущей подписке,— ваших, а также предоставленных Azure или партнерами. Это значит, что вы можете получить большой список. В следующих примерах показано, как сократить список.
-	**Получить семейства образов**. `Get-AzureVMImage | select ImageFamily` возвращает список семейств образов, отображая свойство строк **ImageFamily**.
-	**Получить все образы из указанного семейства**. `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**Найти образы виртуальных машин**. `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` проводит фильтрацию по свойству DataDiskConfiguration, которое применяется только к образам виртуальных машин. В этом примере выходные данные также фильтруются, и выводятся только метка и имя образа.
-	**Сохранить обобщенный образ**. `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Сохранить специализированный образ**. `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Параметр OSState является обязательным, если вы хотите создать образ виртуальной машины, содержащий диски с данными, а также диск операционной системы. Если не указать этот параметр, командлет создает образ ОС. Значение параметра указывает, является ли образ обобщенным или специальным и основывается ли он на диске операционной системы, подготовленном для повторного использования.
-	**Удалить образ**. `Remove-AzureVMImage –ImageName "MyOldVmImage"`


## Дальнейшие действия

Вы можете [создать виртуальную машину Windows с помощью классического портала](virtual-machines-windows-classic-tutorial.md).

<!---HONumber=AcomDC_0727_2016-->