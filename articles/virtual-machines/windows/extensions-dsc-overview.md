---
title: "Общие сведения о настройке требуемого состояния для Azure | Документация Майкрософт"
description: "Эта статья содержит общие сведения о работе с расширением Desired State Configuration PowerShell с помощью обработчика расширений Microsoft Azure. В ней описаны обязательные требования, архитектура, командлеты и многое другое."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Общие сведения об обработчике расширения Desired State Configuration в Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Агент VM Azure и связанные расширения являются частью служб инфраструктуры Microsoft Azure.
Расширения виртуальной машины — это программные компоненты, которые расширяют функциональные возможности виртуальной машины и упрощают различные операции управления ею.

Основной вариант использования расширения Desired State Configuration — изначально загрузить виртуальную машину в [службу DSC службы автоматизации Azure](../../automation/automation-dsc-overview.md), [преимущества](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service) которой включают регулярное управление конфигурацией виртуальной машины и интеграцию с другими рабочими инструментами, например со службой мониторинга Azure.

Кроме того, можно использовать расширение DSC независимо от DSC службы автоматизации Azure. Однако это отдельное действие, которое выполняется во время развертывания, а регулярная отправка отчетов или управление конфигурацией возможно на виртуальной машине только в локальной среде.

В этой статье содержатся сведения для двух сценариев — расширения DSC для подключения службы автоматизации Azure и использования расширения DSC как инструмента для назначения конфигураций виртуальным машинам с помощью пакета SDK Azure.

## <a name="prerequisites"></a>предварительным требованиям

- **Локальный компьютер.** Для взаимодействия с расширением виртуальной машины Azure нужно использовать портал Azure или пакет SDK для Azure PowerShell.
- **Гостевой агент.** Виртуальная машина Azure, настроенная с помощью конфигурации DSC, должна работать под управлением ОС, которая поддерживает Windows Management Framework (WMF) версии 4.0 или более поздней. Полный список поддерживаемых версий ОС можно найти на [странице версий расширения DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Термины и основные понятия

Для изучения этого руководства нужно знать приведенные ниже понятия.

- **Конфигурация** — документ конфигурации DSC.
- **Узел** — целевой объект для конфигурации DSC. Слово "узел" в этом документе всегда означает виртуальную машину Azure.
- **Данные конфигурации** — PSD1-файл, содержащий данные среды для конфигурации.

## <a name="architectural-overview"></a>Основные сведения об архитектуре

Расширение DSC Azure использует платформу агента Azure, чтобы доставлять и применять конфигурации DSC виртуальных машин Azure, а также сообщать об этих конфигурациях.
Расширение DSC принимает документ конфигурации и набор параметров.
Если файл не указан, [сценарий конфигурации по умолчанию](###default-configuration-script) внедряется с расширением, которое используется только для метаданных настройки в [локальном диспетчере конфигураций](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Когда расширение вызывается впервые, с его помощью устанавливается определенная версия Windows Management Framework (WMF), следуя приведенной ниже логике.

1. Если виртуальная машина Azure работает под управлением Windows Server 2016, никакие действия не требуются, потому что в Windows Server 2016 уже установлена последняя версия PowerShell.
2. Если указано свойство `wmfVersion`, то устанавливается соответствующая версия WMF (за исключением случаев, когда эта версия несовместима с ОС виртуальной машины).
3. Если свойство `wmfVersion` не указано, то устанавливается последняя применимая версия WMF.

Для установки WMF требуется перезагрузка.
После перезагрузки расширение скачивает ZIP-файл, указанный в свойстве `modulesUrl`, если оно есть.
Если это расположение находится в хранилище BLOB-объектов Azure, то для доступа к файлу в свойстве `sasToken` можно указать маркер SAS.
После скачивания и распаковки ZIP-файла функция конфигурации, определенная в `configurationFunction`, запускается и создает MOF-файл.
Затем расширение выполняет командлет `Start-DscConfiguration -Force` с созданным MOF-файлом.
Расширение фиксирует выходные данные и записывает их в канал состояний Azure.

### <a name="default-configuration-script"></a>Сценарий конфигурации по умолчанию

Расширение DSC Azure включает сценарий конфигурации по умолчанию, предназначенный для использования при подключении виртуальной машины к DSC службы автоматизации Azure.
Параметры сценария сопоставляются с настраиваемыми свойствами [локального диспетчера конфигураций](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).
Параметры сценария [записываются в документ](extensions-dsc-template.md##default-configuration-script). Полный сценарий доступен в [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-arm-templates"></a>Расширение DSC в шаблонах ARM

В большинстве сценариев для работы с расширением DSC используются шаблоны развертывания Azure Resource Manager (ARM).
Ознакомиться с дополнительными сведениями и примерами включения расширения DSC в шаблоны развертывания ARM можно с помощью статьи [Настройка масштабируемых наборов виртуальных машин Windows и Desired State Configuration с помощью шаблонов Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Командлеты PowerShell в расширении DSC

Командлеты PowerShell для управления расширением DSC лучше подходят для сценариев интерактивного устранения неполадок и сбора данных.
Эти командлеты можно использовать для упаковки, публикации и отслеживания развертываний расширения DSC.
Обратите внимание, что командлеты для расширения DSC еще не обновлены для работы со [сценарием конфигурации по умолчанию](###default-configuration-script).

`Publish-AzureRMVMDscConfiguration` принимает файл конфигурации, проверяет его на наличие зависимых ресурсов DSC и создает ZIP-файл, содержащий ресурсы конфигурации и ресурсы DSC, необходимые для применения конфигурации.
Этот командлет может также создать пакет локально с помощью параметра `-ConfigurationArchivePath`
или опубликовать ZIP-файл в хранилище BLOB-объектов Azure и защитить его маркером SAS.

На корневом уровне папки архива у ZIP-файла, созданного этим командлетом, есть скрипт конфигурации в формате PS1.
Предназначенная для ресурсов папка модуля находится в папке архива.

Командлет `Set-AzureRMVMDscExtension` внедряет параметры, необходимые расширению DSC PowerShell, в объект конфигурации виртуальной машины.

`Get-AzureRMVMDscExtension` извлекает состояние расширения DSC определенной виртуальной машины.

`Get-AzureRMVMDscExtensionStatus` получает состояние конфигурации DSC, которое применил обработчик расширений DSC.
Это действие может выполняться с одной виртуальной машиной или с группой виртуальных машин.

`Remove-AzureRMVMDscExtension` удаляет обработчик расширений из определенной виртуальной машины.
Это **не** приводит к удалению конфигурации, удалению WMF или изменению примененных параметров на виртуальной машине.
Удаляется только обработчик расширений. 

Важные сведения о командлетах расширения DSC AzureRM.

- Командлеты Azure Resource Manager выполняются синхронно.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version, Location — обязательные параметры.
- ArchiveResourceGroupName — это необязательный параметр. Этот параметр можно указать, если ваша учетная запись хранения не принадлежит к той группе ресурсов, в которой создана виртуальная машина.
- Параметр AutoUpdate позволяет автоматически обновлять обработчик расширений до последней версии сразу, когда она становится доступной. Обратите внимание, что после выпуска новой версии WMF возможна перезагрузка виртуальной машины из-за данного параметра.

### <a name="getting-started-with-cmdlets"></a>Начало работы с командлетами

Расширение DSC Azure может использовать документы конфигурации DSC непосредственно для настройки виртуальных машин Azure во время развертывания, но так как при этом узел не регистрируется в службе автоматизации Azure, централизованное управление узлом *невозможно*.

Ниже приведен простой пример конфигурации.
Сохраните его локально как файл IisInstall.ps1.

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

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Использование портала Azure

Перейдите к виртуальной машине. В разделе "Параметры" > "Общие" щелкните "Расширения".
Будет создана новая панель.
Щелкните "Добавить" и выберите DSC PowerShell.

Порталу нужны входные данные.
**Configuration Modules or Script** (Модули конфигурации или сценарий). Это поле является обязательным (для [сценария конфигурации по умолчанию](###default-configuration-script) форма не была обновлена).
Здесь следует указать PS1-файл, содержащий сценарий конфигурации, или ZIP-файл со сценарием конфигурации в формате PS1 в корне и всеми зависимыми ресурсами в папках модулей.
Его можно создать с помощью командлета `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` , входящего в пакет SDK для Azure PowerShell.
Этот ZIP-файл передается в хранилище BLOB-объектов пользователя, защищенный маркером SAS.

**Configuration Data PSD1 File**(PSD1-файл данных конфигурации) — необязательное поле.
Если для вашей конфигурации требуется PSD1-файл данных конфигурации, выберите его с помощью этого поля и передайте в хранилище BLOB-объектов, где он будет защищен маркером SAS.

**Module-Qualified Name of Configuration**(Полное имя модуля конфигурации) — PS1-файлы могут включать в себя несколько функций конфигурации.
Введите имя сценария конфигурации (в формате PS1), символ \', а затем — имя функции конфигурации.
Например, если PS1-файл сценария называется configuration.ps1, а имя конфигурации — IisInstall, введите: `configuration.ps1\IisInstall`

**Configuration Arguments** (Аргументы конфигурации). Если функция конфигурации принимает аргументы, введите их здесь в формате `argumentName1=value1,argumentName2=value2`.
Обратите внимание, что этот формат отличается от того, в котором аргументы конфигурации принимаются через командлеты PowerShell или шаблоны Resource Manager.

## <a name="logging"></a>Ведение журналов
Журналы размещаются в следующем каталоге:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>Дополнительная информация
Для получения дополнительных сведений о DSC PowerShell [посетите центр документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Изучите [шаблон Azure Resource Manager для расширения DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Чтобы получить дополнительные функциональные возможности, которыми можно управлять с помощью DSC PowerShell, [найдите в коллекции PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) дополнительные материалы по DSC.

Сведения о передаче конфиденциальных параметров в конфигурации см. в статье [Передача учетных данных в обработчик расширений DSC Azure](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
