---
title: Общие сведения о настройке требуемого состояния для Azure | Документация Майкрософт
description: Узнайте, как использовать обработчик расширений Microsoft Azure для настройки требуемого состояния (DSC). В статье приведены необходимые компоненты, архитектура и командлеты.
services: virtual-machines-windows
documentationcenter: ''
author: mgreenegit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: b6bfe48df685952d2b465d9549e2f1c086c1c490
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192933"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Общие сведения об обработчике расширения Desired State Configuration в Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Агент виртуальной машины Azure и связанные расширения являются частью служб инфраструктуры Microsoft Azure. Расширения виртуальной машины — это программные компоненты, которые расширяют функциональные возможности виртуальной машины и упрощают различные операции управления ею.

Основной вариант использования расширения Azure Desired State Configuration (DSC) — это начальная загрузка виртуальной машины в [службе Azure Automation DSC](../../automation/automation-dsc-overview.md). [Преимущества](https://docs.microsoft.com/powershell/dsc/metaconfig#pull-service) этого включают регулярное управление конфигурацией виртуальной машины и интеграцию с другими рабочими инструментами, например со службой мониторинга Azure.

Расширение DSC можно использовать независимо от службы Automation DSC. Однако это отдельное действие, которое выполняется во время развертывания. Регулярная отправка отчетов или управление конфигурацией возможно на виртуальной машине только в локальной среде.

В этой статье предоставлены сведения для двух сценариев — расширения DSC для подключения службы автоматизации и использования расширения DSC как инструмента для назначения конфигураций виртуальным машинам с помощью пакета SDK Azure.

## <a name="prerequisites"></a>предварительным требованиям

- **Локальный компьютер.** Для взаимодействия с расширением виртуальной машины Azure нужно использовать портал Azure или пакет SDK для Azure PowerShell.
- **Гостевой агент.** Виртуальная машина Azure, настроенная с помощью конфигурации DSC, должна работать под управлением ОС, которая поддерживает Windows Management Framework (WMF) версии 4.0 или более поздней. Полный список поддерживаемых версий ОС см. в [журнале версий расширения DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Термины и основные понятия

Для изучения этого руководства нужно знать приведенные ниже понятия.

- **Конфигурация**. Документ конфигурации DSC.
- **Узел**. Целевой объект для конфигурации DSC. Слово *узел* в этом документе всегда означает виртуальную машину Azure.
- **Данные конфигурации**. PSD1-файл, содержащий данные среды для конфигурации.

## <a name="architecture"></a>Архитектура

Расширение DSC Azure использует платформу агента Azure, чтобы доставлять и применять конфигурации DSC виртуальных машин Azure, а также сообщать об этих конфигурациях. Расширение DSC принимает документ конфигурации и набор параметров. Если файл не предоставлен, внедряется [скрипт конфигурации по умолчанию](#default-configuration-script) с расширением. Скрипт конфигурации по умолчанию используется только для задания метаданных в [локальном диспетчере конфигураций](https://docs.microsoft.com/powershell/dsc/metaconfig).

Когда расширение вызывается впервые, с его помощью устанавливается определенная версия WMF, следуя приведенной ниже логике:

* Если виртуальная машина Azure работает под управлением Windows Server 2016, никакие действия не требуются, потому что в Windows Server 2016 уже установлена последняя версия PowerShell.
* Если указано свойство **wmfVersion**, то устанавливается соответствующая версия WMF (за исключением случаев, когда эта версия несовместима с ОС виртуальной машины).
* Если свойство **wmfVersion** не указано, то устанавливается последняя применимая версия WMF.

Для установки WMF требуется перезагрузка. После перезапуска расширения скачивается ZIP-файл, указанный в свойстве **modulesUrl**, если оно предоставлено. Если это расположение находится в хранилище BLOB-объектов Azure, то для доступа к файлу в свойстве **sasToken** можно указать маркер SAS. После скачивания и распаковки ZIP-файла функция конфигурации, определенная в **configurationFunction**, запускается и создает MOF-файл. Затем расширение выполняет **Start-DscConfiguration -Force** с использованием созданного MOF-файла. Расширение фиксирует выходные данные и записывает их в канал состояний Azure.

### <a name="default-configuration-script"></a>Скрипт конфигурации по умолчанию

Расширение DSC Azure включает скрипт конфигурации по умолчанию, предназначенный для использования при подключении виртуальной машины к DSC службы автоматизации Azure. Параметры сценария сопоставляются с настраиваемыми свойствами [локального диспетчера конфигураций](https://docs.microsoft.com/powershell/dsc/metaconfig). Параметры скрипта см. в разделе [Скрипт конфигурации по умолчанию](extensions-dsc-template.md#default-configuration-script) статьи [Расширение Desired State Configuration (DSC) с использованием шаблонов Azure Resource Manager](extensions-dsc-template.md). Полный скрипт см. в [шаблоне быстрого запуска Azure на GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Использование расширения DSC в шаблонах Resource Manager

В большинстве сценариев для работы с расширением DSC используются шаблоны развертывания Azure Resource Manager. Ознакомиться с дополнительными сведениями и примерами включения расширения DSC в шаблоны развертывания Azure Resource Manager можно с помощью статьи [Расширение Desired State Configuration (DSC) с использованием шаблонов Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Командлеты PowerShell для расширения DSC

Командлеты PowerShell, которые используются для управления расширением DSC, лучше подходят для сценариев интерактивного устранения неполадок и сбора данных. Эти командлеты можно использовать для упаковки, публикации и отслеживания развертываний расширения DSC. Обратите внимание, что командлеты для расширения DSC еще не обновлены для работы со [скриптом конфигурации по умолчанию](#default-configuration-script).

Командлет **Publish-AzureRMVMDscConfiguration** принимает файл конфигурации, сканирует его на наличие ресурсов, зависимых от DSC, а затем создает ZIP-файл. ZIP-файл содержит конфигурацию и ресурсы DSC, которые необходимы для применения конфигурации. Командлет также может создать пакет локально, используя параметр *-OutputArchivePath*, или опубликовать ZIP-файл в хранилище BLOB-объектов Azure, а затем защитить его маркером SAS.

На корневом уровне папки архива у ZIP-файла, созданного этим командлетом, есть скрипт конфигурации в формате PS1. Предназначенная для ресурсов папка модуля находится в папке архива.

Командлет **Set-AzureRMVMDscExtension** внедряет параметры, которые необходимы расширению PowerShell DSC, в объект конфигурации виртуальной машины.

Командлет **Get-AzureRMVMDscExtension** извлекает состояние расширения DSC определенной виртуальной машины.

Командлет **Get-AzureRMVMDscExtensionStatus** извлекает состояние конфигурации DSC, которое применил обработчик расширений DSC. Это действие может выполняться с одной виртуальной машиной или с группой виртуальных машин.

Командлет **Remove-AzureRMVMDscExtension** удаляет обработчик расширений из определенной виртуальной машины. Этот командлет *не* приводит к удалению конфигурации, удалению WMF или изменению примененных параметров на виртуальной машине. Удаляется только обработчик расширений. 

Важная информация о командлетах расширения DSC для Resource Manager:

- Командлеты Azure Resource Manager выполняются синхронно.
- *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*, *Location* — обязательные параметры.
- *ArchiveResourceGroupName* — необязательный параметр. Этот параметр можно указать, если ваша учетная запись хранения не принадлежит к той группе ресурсов, в которой создана виртуальная машина.
- Параметр **AutoUpdate** позволяет автоматически обновлять обработчик расширений до последней версии сразу, когда она становится доступной. Обратите внимание, что после выпуска новой версии WMF возможна перезагрузка виртуальной машины из-за данного параметра.

### <a name="get-started-with-cmdlets"></a>Начало работы с командлетами

Расширение DSC Azure может использовать документы конфигурации DSC для непосредственной настройки виртуальных машин Azure во время развертывания. Этот шаг не регистрирует узел для службы автоматизации. Этот узел *не* управляется централизовано.

Ниже показан пример простой конфигурации. Сохраните конфигурацию локально как скрипт IisInstall.ps1.

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

Следующие команды размещают скрипт IisInstall.ps1 на определенной виртуальной машине, а также выполняют конфигурацию и возвращают сведения о состоянии.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Использование портала Azure

Для настройки DSC на портале:

1. Перейдите к виртуальной машине. 
2. В разделе **Параметры** > **Общие** выберите **Расширения**. 
3. В созданной области выберите **Добавить**, а потом — **PowerShell DSC**.

Для работы с данным руководством требуется указать следующее:

* **Configuration Modules or Script** (Модули конфигурации или скрипт). Это поле является обязательным (для [скрипта конфигурации по умолчанию](#default-configuration-script) форма не была обновлена). Модулям и скриптам конфигурации требуется PS1-файл, содержащий скрипт конфигурации, или ZIP-файл со скриптом конфигурации PS1 в корне. При использовании ZIP-файла все зависимые ресурсы должны быть включены в его папки модуля. ZIP-файл можно создать с помощью командлета **Publish-AzureVMDscConfiguration -OutputArchivePath**, включенного в пакет SDK для Azure PowerShell. Этот ZIP-файл передается в хранилище BLOB-объектов пользователя и защищается маркером SAS.

* **Configuration Data PSD1 File**(PSD1-файл данных конфигурации) — необязательное поле. Если для вашей конфигурации требуется PSD1-файл данных конфигурации, выберите его с помощью этого поля и передайте в хранилище BLOB-объектов, где он будет защищен маркером SAS.

* **Module-Qualified Name of Configuration**(Полное имя модуля конфигурации). В PS1-файл можно включить несколько функций конфигурации. Введите имя скрипта конфигурации (в формате PS1), символ \\, а затем — имя функции конфигурации. Например, если PS1-файл скрипта называется configuration.ps1, а имя конфигурации — **IisInstall**, введите **configuration.ps1\IisInstall**.

* **Configuration Arguments** (Аргументы конфигурации). Если функция конфигурации принимает аргументы, введите их здесь в формате **argumentName1=value1,argumentName2=value2**. Этот формат отличается от того, в котором аргументы конфигурации принимаются в командлетах PowerShell или шаблонах Resource Manager.

## <a name="logs"></a>Журналы
Разместите журналы в этом расположении:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о DSC PowerShell можно найти в [центре документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
* Изучите [шаблон Resource Manager для расширения DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Дополнительные функции, которыми можно управлять с помощью DSC PowerShell, и ресурсы DSC можно найти в [коллекции PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
* Сведения о передаче конфиденциальных параметров в конфигурации см. в статье [Передача учетных данных в обработчик расширений DSC Azure](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

