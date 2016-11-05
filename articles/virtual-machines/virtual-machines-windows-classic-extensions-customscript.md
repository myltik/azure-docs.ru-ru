---
title: Расширение Custom Script на виртуальной машине под управлением Windows | Microsoft Docs
description: Автоматизируйте процесс настройки виртуальных машин Azure с помощью расширения Custom Script для выполнения сценариев PowerShell на удаленной виртуальной машине под управлением Windows
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/06/2015
ms.author: kundanap

---
# Расширение Custom Script для виртуальных машин под управлением Windows
В этой статье представлен обзор использования расширения пользовательских сценариев на виртуальных машинах Windows с помощью командлетов Azure PowerShell и интерфейсов API управления службами Azure.

Расширения виртуальной машины разработаны корпорацией Майкрософт и доверенными сторонними компаниями для расширения функциональных возможностей виртуальной машины. Общие сведения о расширениях виртуальных машин см. в статье 
[Расширения и компоненты виртуальных машин Azure](virtual-machines-windows-extensions-features.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Узнайте, как [выполнить эти действия с помощью модели Resource Manager](virtual-machines-windows-extensions-customscript.md).

## Общие сведения о расширении Custom Script
С помощью расширения пользовательских сценариев для Windows можно выполнять сценарии PowerShell на удаленной виртуальной машине без выполнения входа. Сценарии можно выполнить после подготовки виртуальной машины к работе или в любое другое время в течение ее жизненного цикла без необходимости открывать на этой виртуальной машине какие-либо дополнительные порты. Обычно расширение пользовательских сценариев используется для установки и настройки дополнительного программного обеспечения на виртуальной машине после ее подготовки к работе.

### Предварительные требования к выполнению расширения пользовательских сценариев
1. Установите <a href="http://azure.microsoft.com/downloads" target="_blank">командлеты Azure PowerShell</a> 0.8.0 или более поздней версии.
2. Если вы хотите выполнять сценарии на существующей виртуальной машине, убедитесь, что на ней включен агент виртуальной машины. Если он не установлен, выполните следующие [действия](virtual-machines-windows-classic-agents-and-extensions.md). Если виртуальная машина создана на портале Azure, то на ней по умолчанию установлен агент виртуальной машины.
3. Передайте сценарии, которые требуется запускать на виртуальной машине, в службу хранилища Azure. Сценарии могут поступать как из одного, так и из нескольких контейнеров хранилища.
4. Сценарий должен быть создан таким образом, чтобы запущенный с помощью расширения первый сценарий запускал остальные сценарии.

## Сценарии использования расширения Custom Script
### Передача файлов в контейнер по умолчанию
В приведенном ниже примере показано, как выполнить сценарии на виртуальной машине, если они находятся в контейнере хранилища учетной записи подписки по умолчанию. Сценарии передаются в контейнер ContainerName. Учетную запись хранения по умолчанию можно проверить с помощью команды **Get-AzureSubscription –Default**.

Приведенный ниже пример создает виртуальную машину, однако этот же сценарий может выполняться и на существующей виртуальной машине.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Передача файлов в контейнеры хранилища, не являющиеся контейнерами по умолчанию
В этом сценарии показано, как использовать контейнер хранилища, не используемый по умолчанию и расположенный в той же или другой подписке, для передачи сценариев и файлов. В примере показана существующая виртуальная машина, но все описанные действия можно выполнить и при создании виртуальной машины.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Передача сценариев в несколько контейнеров в разных учетных записях хранения
  Если файлы сценариев хранятся в нескольких контейнерах, то для их выполнения необходимо указать полные подписанные URL-адреса (SAS) этих файлов.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Добавление расширения пользовательских сценариев с портала Azure
Перейдите к виртуальной машине на <a href="https://portal.azure.com/ " target="_blank">портале Azure </a> и добавьте расширение, указав файл выполняемого сценария.

  ![Указание файл сценария][5]

### Удаление расширения пользовательских сценариев
Чтобы удалить расширение пользовательских сценариев с виртуальной машины, выполните указанную ниже команду.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Использование расширения пользовательских сценариев с шаблонами
Чтобы узнать об использовании расширения пользовательских сценариев с шаблонами Azure Resource Manager, ознакомьтесь с разделом [Использование расширения пользовательских сценариев для виртуальных машин Windows с шаблонами Azure Resource Manager](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png

<!---HONumber=AcomDC_0824_2016-->