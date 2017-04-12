---
title: "Общие сведения о настройке требуемого состояния для Azure | Документация Майкрософт"
description: "Эта статья содержит общие сведения о работе с расширением Desired State Configuration PowerShell с помощью обработчика расширений Microsoft Azure. В ней описаны обязательные требования, архитектура, командлеты и многое другое."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c05c2d541a5f526f362f9cd72fe6d878374112b6
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Общие сведения об обработчике расширения Desired State Configuration в Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Агент VM Azure и связанные расширения являются частью служб инфраструктуры Microsoft Azure. Расширения виртуальной машины — это программные компоненты, которые расширяют функциональные возможности виртуальной машины и упрощают различные операции управления ею. Например, расширение VMAccess позволяет сбросить пароль администратора, а расширение пользовательских сценариев можно использовать для выполнения сценария на виртуальной машине.

В этой статье описано расширение PowerShell Desired State Configuration (DSC) для виртуальных машин Azure, которое входит в пакет SDK для Azure PowerShell. С помощью новых командлетов вы можете передать и применить конфигурацию DSC PowerShell на виртуальной машине Azure, на которой включено расширение DSC PowerShell. Расширение DSC PowerShell вызывает DSC PowerShell, чтобы применить полученную конфигурацию DSC к виртуальной машине. Эта функция доступна также на портале Azure.

## <a name="prerequisites"></a>Предварительные требования
**Локальный компьютер.** Для взаимодействия с расширением виртуальной машины Azure нужно использовать портал Azure или пакет SDK для Azure PowerShell. 

**Гостевой агент.** Виртуальная машина Azure, на которой применяется конфигурация DSC, должна работать под управлением ОС, которая поддерживает Windows Management Framework (WMF) 4.0 или 5.0. Полный список поддерживаемых версий ОС можно найти в [журнале версий расширения DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Термины и основные понятия
Для изучения этого руководства нужно знать приведенные ниже понятия.

Конфигурация — документ конфигурации DSC. 

Узел — целевой объект для конфигурации DSC. Слово "узел" в этом документе всегда означает виртуальную машину Azure.

Данные конфигурации — PSD1-файл, содержащий данные среды для конфигурации.

## <a name="architectural-overview"></a>Основные сведения об архитектуре
Расширение DSC Azure использует платформу агента Azure, чтобы доставлять и применять конфигурации DSC виртуальных машин Azure, а также сообщать об этих конфигурациях. Расширению DSC требуется ZIP-файл, содержащий по крайней мере документ конфигурации, а также набор параметров, которые передаются через пакет SDK для Azure PowerShell или портал Azure.

Когда расширение вызывается впервые, оно запускает процесс установки. Этот процесс устанавливает определенную версию Windows Management Framework (WMF), следуя приведенной ниже логике.

1. Если виртуальная машина Azure работает под управлением Windows Server 2016, никакие действия не требуются, потому что в Windows Server 2016 уже установлена последняя версия PowerShell.
2. Если указано свойство `wmfVersion`, то устанавливается соответствующая версия WMF (за исключением случаев, когда эта версия несовместима с ОС виртуальной машины).
3. Если свойство `wmfVersion` не указано, то устанавливается последняя применимая версия WMF.

Для установки WMF требуется перезагрузка. После перезагрузки расширение скачивает ZIP-файл, указанный в свойстве `modulesUrl` . Если это расположение находится в хранилище BLOB-объектов Azure, то для доступа к файлу в свойстве `sasToken` можно указать маркер SAS. После скачивания и распаковки ZIP-файла функция конфигурации, определенная в `configurationFunction` , запускается и создает MOF-файл. Затем расширение выполняет командлет `Start-DscConfiguration -Force` с созданным MOF-файлом. Расширение фиксирует выходные данные и записывает их в канал состояний Azure. С этого момента локальный диспетчер конфигураций DSC выполняет мониторинг и обрабатывает исправления в обычном режиме. 

## <a name="powershell-cmdlets"></a>Командлеты PowerShell
Командлеты PowerShell можно использовать с классической моделью развертывания или моделью развертывания с помощью Azure Resource Manager, чтобы упаковывать, публиковать и отслеживать развертывания расширений DSC. Ниже приведены командлеты для классической модели развертывания, но для использования модели Azure Resource Manager часть "Azure" следует заменить на "AzureRM". Например, `Publish-AzureVMDscConfiguration` использует классическую модель развертывания, а `Publish-AzureRmVMDscConfiguration` — модель с Azure Resource Manager. 

`Publish-AzureVMDscConfiguration` принимает файл конфигурации, проверяет его на наличие зависимых ресурсов DSC и создает ZIP-файл, содержащий ресурсы конфигурации и ресурсы DSC, необходимые для применения конфигурации. Этот командлет может также создать пакет локально с помощью параметра `-ConfigurationArchivePath` или опубликовать ZIP-файл в хранилище BLOB-объектов Azure и защитить его маркером SAS.

На корневом уровне папки архива у ZIP-файла, созданного этим командлетом, есть скрипт конфигурации в формате PS1. Предназначенная для ресурсов папка модуля находится в папке архива. 

Командлет `Set-AzureVMDscExtension` внедряет параметры, необходимые расширению DSC PowerShell, в объект конфигурации виртуальной машины. В классической модели развертывания изменения виртуальной машины применяются к виртуальной машине Azure с помощью `Update-AzureVM`. 

`Get-AzureVMDscExtension` извлекает состояние расширения DSC определенной виртуальной машины. 

`Get-AzureVMDscExtensionStatus` получает состояние конфигурации DSC, которое применил обработчик расширений DSC. Это действие может выполняться с одной виртуальной машиной или с группой виртуальных машин.

`Remove-AzureVMDscExtension` удаляет обработчик расширений из определенной виртуальной машины. Это **не** приводит к удалению конфигурации, удалению WMF или изменению примененных параметров на виртуальной машине. Удаляется только обработчик расширений. 

**Основные отличия между командлетами для ASM и Azure Resource Manager**

* Командлеты Azure Resource Manager выполняются синхронно. а командлеты ASM — асинхронными.
* ResourceGroupName, VMName, ArchiveStorageAccountName, Version, Location — это обязательные параметры в Azure Resource Manager.
* ArchiveResourceGroupName — новый необязательный параметр для Azure Resource Manager. Этот параметр можно указать, если ваша учетная запись хранения не принадлежит к той группе ресурсов, в которой создана виртуальная машина.
* ConfigurationArchive — это ArchiveBlobName в Azure Resource Manager.
* ContainerName — это ArchiveContainerName в Azure Resource Manager.
* StorageEndpointSuffix — это ArchiveStorageEndpointSuffix в Azure Resource Manager.
* В Azure Resource Manager добавлен параметр AutoUpdate, который позволяет автоматически обновлять обработчик расширений до последней версии сразу, когда она становится доступной. Обратите внимание, что после выпуска новой версии WMF возможна перезагрузка виртуальной машины из-за данного параметра. 

## <a name="azure-portal-functionality"></a>Использование портала Azure
Перейдите к виртуальной машине. В разделе "Параметры" > "Общие" щелкните "Расширения". Будет создана новая панель. Щелкните "Добавить" и выберите DSC PowerShell.

Порталу нужны входные данные.
**Configuration Modules or Script**(Модули или сценарий конфигурации) — обязательное поле. Здесь следует указать PS1-файл, содержащий сценарий конфигурации, или ZIP-файл со сценарием конфигурации в формате PS1 в корне и всеми зависимыми ресурсами в папках модулей. Его можно создать с помощью командлета `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` , входящего в пакет SDK для Azure PowerShell. Этот ZIP-файл передается в хранилище BLOB-объектов пользователя, защищенный маркером SAS. 

**Configuration Data PSD1 File**(PSD1-файл данных конфигурации) — необязательное поле. Если для вашей конфигурации требуется PSD1-файл данных конфигурации, выберите его с помощью этого поля и передайте в хранилище BLOB-объектов, где он будет защищен маркером SAS. 

**Module-Qualified Name of Configuration**(Полное имя модуля конфигурации) — PS1-файлы могут включать в себя несколько функций конфигурации. Введите имя сценария конфигурации (в формате PS1), символ \', а затем — имя функции конфигурации. Например, если PS1-файл сценария называется configuration.ps1, а имя конфигурации — IisInstall, введите: `configuration.ps1\IisInstall`

**Configuration Arguments** (Аргументы конфигурации). Если функция конфигурации принимает аргументы, введите их здесь в формате `argumentName1=value1,argumentName2=value2`. Обратите внимание, что этот формат отличается от того, в котором аргументы конфигурации принимаются через командлеты PowerShell или шаблоны Resource Manager. 

## <a name="getting-started"></a>Приступая к работе
Расширение DSC Azure получает документы конфигурации DSC и применяет их на виртуальных машинах Azure. Ниже приведен простой пример конфигурации. Сохраните его локально как файл IisInstall.ps1.

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Ниже показано, как разместить сценарий IisInstall.ps1 на указанной виртуальной машине, выполнить конфигурацию и сообщить о статусе.
###<a name="classic-model"></a>Классическая модель
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Модель Azure Resource Manager

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>Ведение журналов
Журналы размещаются в следующем каталоге:

C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[номер версии]

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений о DSC PowerShell [посетите центр документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Изучите [шаблон Azure Resource Manager для расширения DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Чтобы получить дополнительные функциональные возможности, которыми можно управлять с помощью DSC PowerShell, [найдите в коллекции PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) дополнительные материалы по DSC.

Сведения о передаче конфиденциальных параметров в конфигурации см. в статье [Передача учетных данных в обработчик расширений DSC Azure](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


