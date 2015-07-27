<properties
   pageTitle="Расширение Custom Script в ОС Windows"
   description="Автоматизация задач настройки виртуальной машины Azure с помощью расширения Custom Script в ОС Windows"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2015"
   ms.author="kundanap"/>

# Расширение Custom Script в ОС Windows

В этой статье описано использование расширения Custom Script в ОС Windows с помощью командлетов Azure Powershell.


Расширения виртуальной машины разработаны корпорацией Майкрософт и доверенными сторонними компаниями для расширения функциональных возможностей виртуальной машины. Подробный обзор расширений виртуальных машин см. в <a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">документации на сайте MSDN</a>.

## Обзор расширения Custom Script

Расширение Custom Script для ОС Windows позволяет запускать сценарии Powershell на удаленной виртуальной машине без выполнения входа. Сценарии могут запускаться после подготовки виртуальной машины к работе или в любое другое время в течение ее жизненного цикла без необходимости открывать на виртуальной машине какие-либо дополнительные порты. Обычно Custom Script используется для установки и настройки дополнительного программного обеспечения на виртуальной машине после ее подготовки к работе.

### Предварительные требования для запуска расширения Custom Script

1. Установите командлеты Azure PowerShell версии 0.8.0 или более поздней с <a href="http://azure.microsoft.com/downloads" target="_blank">этого сайта</a>.
2. Если сценарии будут запускаться на существующей виртуальной машине, убедитесь, что на ней включен агент виртуальной машины. Если агент не включен, установите его, следуя указаниям в этой <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">статье</a>.
3. Передайте сценарии, которые требуется запускать на виртуальной машине, в службу хранилища Azure. Сценарии могут поступать как из одного, так и из нескольких контейнеров хранилища.
4. Сценарий должен быть создан таким образом, чтобы запущенный с помощью расширения первый сценарий запускал в свою очередь другие сценарии.

## Сценарии использования расширения Custom Script

 ### Передача файлов в контейнер по умолчанию. Если у вас есть сценарии в контейнере хранилища учетной записи подписки по умолчанию, ознакомьтесь с приведенным ниже фрагментом командлета, который демонстрирует их запуск на виртуальной машине. В этом примере параметр ContainerName указывает расположение, в которое передаются сценарии. Учетную запись хранения по умолчанию можно проверить с помощью командлета Get-AzureSubscription –Default.

В этом примере создается виртуальная машина, но все описанные действия можно выполнить и на существующей виртуальной машине.

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

### Передача файлов в контейнеры хранилища, не являющиеся контейнерами по умолчанию.

В этом примере показано, как использовать хранилище, не используемое по умолчанию, в той же или другой подписке для передачи сценариев или файлов. Мы будем использовать существующую виртуальную машину, но все описанные действия можно выполнить и при создании новой виртуальной машины.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM
  ### Передача сценариев в несколько контейнеров в разных учетных записях хранения. Если файлы сценариев хранятся в разных контейнерах, сейчас для их запуска необходимо указать полный подписанный URL-адрес (SAS) к этим файлам.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Добавление расширения Custom Script на портале.
Перейдите к виртуальной машине на <a href="https://portal.azure.com/ " target="_blank">портале предварительной версии Azure </a> и добавьте это расширение, указав нужный файл сценария. ![][5]

  ### Удаление расширения Custom Script.

Чтобы удалить расширение Custom Script с виртуальной машины, воспользуйтесь приведенным ниже командлетом.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Скоро

Вскоре мы планируем добавить статью, посвященную использованию Custom Script для ОС Linux, которая будет содержать соответствующие примеры. Следите за нашими новостями.

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png
 

<!---HONumber=July15_HO3-->