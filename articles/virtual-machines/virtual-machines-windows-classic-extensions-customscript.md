<properties
   pageTitle="Расширение Custom Script на виртуальной машине под управлением Windows | Microsoft Azure"
   description="Автоматизируйте процесс настройки виртуальных машин Azure с помощью расширения Custom Script для выполнения сценариев PowerShell на удаленной виртуальной машине под управлением Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Расширение Custom Script для виртуальных машин под управлением Windows

В этой статье представлен обзор использования расширения пользовательских сценариев на виртуальных машинах Windows с помощью командлетов Azure PowerShell и интерфейсов API управления службами Azure.

Расширения виртуальной машины разработаны корпорацией Майкрософт и доверенными сторонними компаниями для расширения функциональных возможностей виртуальной машины. Общие сведения о расширениях виртуальных машин см. в статье 
[Расширения и компоненты виртуальных машин Azure](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия с помощью Resource Manager](virtual-machines-windows-extensions-customscript.md).

## Общие сведения о расширении Custom Script

Расширение Custom Script для ОС Windows позволяет запускать сценарии Powershell на удаленной виртуальной машине без выполнения входа. Сценарии могут запускаться после подготовки виртуальной машины к работе или в любое другое время в течение ее жизненного цикла без необходимости открывать на виртуальной машине какие-либо дополнительные порты. Обычно расширение Custom Script используется для установки и настройки дополнительного программного обеспечения на виртуальной машине после ее подготовки к работе.

### Предварительные требования для запуска расширения Custom Script

1. Установите командлеты Azure PowerShell версии 0.8.0 или более поздней <a href="http://azure.microsoft.com/downloads" target="_blank">здесь</a>.
2. Если сценарии будут запускаться на существующей виртуальной машине, убедитесь, что на ней включен агент виртуальной машины. Если агент не установлен, установите его, следуя указаниям из этой [статьи](virtual-machines-windows-classic-agents-and-extensions.md). Если виртуальная машина создается на портале, агент устанавливается по умолчанию.
3. Передайте сценарии, которые требуется запускать на виртуальной машине, в службу хранилища Azure. Сценарии могут поступать как из одного, так и из нескольких контейнеров хранилища.
4. Сценарий должен быть создан таким образом, чтобы запущенный с помощью расширения первый сценарий запускал в свою очередь другие сценарии.

## Сценарии использования расширения Custom Script

### Передача файлов в контейнер по умолчанию

Если у вас есть сценарии в контейнере хранилища учетной записи подписки по умолчанию, ознакомьтесь с приведенным ниже примером, который демонстрирует их запуск на виртуальной машине. Сценарии будут загружаться в контейнер ContainerName. Учетную запись хранения по умолчанию можно проверить с помощью команды **Get-AzureSubscription –Default**.

Код в приведенном ниже примере создает новую виртуальную машину, однако тот же сценарий может выполняться и на существующей виртуальной машине.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Передача файлов в контейнеры хранилища, не являющиеся контейнерами по умолчанию

В этом сценарии показано, как использовать хранилище, не используемое по умолчанию, в той же или другой подписке для передачи сценариев и файлов. Мы будем использовать существующую виртуальную машину, но все описанные действия можно выполнить и при создании новой виртуальной машины.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Передача сценариев в несколько контейнеров в разных учетных записях хранения

  Если файлы сценариев хранятся в разных контейнерах, то для их запуска необходимо указать полный подписанный URL-адрес (SAS) этих файлов.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Добавление расширения Custom Script из портала Azure

Перейдите к этой виртуальной машине на <a href="https://portal.azure.com/ " target="_blank">портале Azure </a> и добавьте это расширение, указав нужный файл сценария.

  ![][5]


### Удаление расширения Custom Script

Чтобы удалить расширение Custom Script с виртуальной машины, воспользуйтесь указанной ниже командой.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Использование расширения Custom Script с шаблонами

Чтобы узнать об использовании расширения Custom Script с шаблонами диспетчера ресурсов Azure, обратитесь к документации [здесь](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png

<!---HONumber=AcomDC_0810_2016-->
