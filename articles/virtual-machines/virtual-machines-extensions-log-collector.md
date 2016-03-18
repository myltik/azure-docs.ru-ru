<properties
   pageTitle="Расширение виртуальной машины AzureLogCollector | Microsoft Azure"
   description="Как использовать расширение виртуальной машины AzureLogCollector для сбора и переноса всех файлов журналов в единое расположение в хранилище Azure."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="powershell"
   ms.topic="article"
   ms.tgt_pltfrm="nad"
   ms.workload="infrastructure"
   ms.date="11/12/2015"
   ms.author="rasquill"/>


# Расширение AzureLogCollector

Диагностика неполадок в облачной службе Microsoft Azure предусматривает сбор файлов журналов служб на виртуальных машинах по мере возникновения проблем. Вы можете использовать запускаемое по запросу расширение AzureVMLogCollector для однократного выполнения операций по сбору журналов из одной или нескольких виртуальных машин облачной службы (из веб-ролей или рабочих ролей) и передачи собранных файлов в учетную запись хранения Azure. Все эти действия выполняются без удаленного входа на виртуальные машины.
> [AZURE.NOTE]Описание почти всей информации, которая записывается в журналы, см. в статье http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Существует два режима сбора данных для файлов разных типов. 1) Только журналы гостевого агента Azure (GA). В этом режиме собираются все журналы, связанные с гостевыми агентами и другими компонентами Azure. 2) Все журналы (Full). В этом режиме собираются все файлы, которые собираются в режиме GA, а также:

  - журналы событий системы и приложений;
  
  - журналы ошибок HTTP;
  
  - Журналы IIS
  
  - журналы установки;
  
  - другие системные журналы.

Для двух этих режимов сбора можно указать папки, в которые будут собираться дополнительные данные. Это можно сделать с помощью коллекции со следующей структурой.

- **Name**: имя коллекции, которое будет использоваться как имя вложенной папки в ZIP-файле с собранной информацией.

- **Location**: путь к папке на виртуальной машине, куда будет записан файл.

- **SearchPattern**: шаблон имен файлов, которые будут собраны. Значение по умолчанию — *.

- **Recursive**: параметр определяет, будут ли файлы собраны рекурсивно в рамках заданной папки.

## Предварительные требования

- Учетная запись, в которую расширение будет сохранять созданные ZIP-файлы.
- Командлеты Azure PowerShell 0.8.0 или более поздней версии. Дополнительные сведения см. на странице [загрузок Azure](https://azure.microsoft.com/downloads/).

## Добавление расширения

Добавить расширение AzureLogCollector можно с помощью командлетов [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) или интерфейсов [REST API управления службой](https://msdn.microsoft.com/library/ee460799.aspx).

Для облачных служб существует командлет Azure Powershell с именем **Set-AzureServiceExtension**. Его можно использовать для включения расширения в экземплярах роли облачной службы. При каждом включении расширения с помощью этого командлета запускается операция сбора журналов на указанных экземплярах выбранных ролей.

Для виртуальных машин существует командлет Azure Powershell **Set-AzureVMExtension**. Его можно использовать для включения расширения на виртуальных машинах. При каждом включении расширения с помощью этого командлета запускается операция сбора журналов на каждом экземпляре.

На внутреннем уровне расширение использует свойства PublicConfiguration и PrivateConfiguration на основе JSON. Ниже приведена примерная структура JSON для открытой и закрытой конфигурации.

### PublicConfiguration

    {
        "Instances":  "*",
        "Mode":  "Full",
        "SasUri":  "SasUri to your storage account with sp=wl",
        "AdditionalData":  
        [
          {
                  "Name":  "StorageData",
                  "Location":  "%roleroot%storage",
                  "SearchPattern":  "*.*",
                  "Recursive":  "true"
          },
          {
                "Name":  "CustomDataFolder2",
                "Location":  "c:\customFolder",
                "SearchPattern":  "*.log",
                "Recursive":  "false"
          },
        ]
    }

### PrivateConfiguration

    {
    
    }

> [AZURE.NOTE]Это расширение не требует наличия **privateConfiguration**. В качестве аргумента для **–PrivateConfiguration** вы можете предоставить пустую структуру.

Чтобы добавить AzureLogCollector в один или несколько экземпляров облачной службы или виртуальной машины для выбранных ролей, можно выполнить одну из двух описанных ниже процедур. Будет запущена операция сбора данных на каждой виртуальной машине с отправкой собранных файлов в указанную учетную запись Azure.

## Добавление в качестве расширения службы

1. Подключите Azure PowerShell к своей подписке в соответствии с инструкциями.

2. Укажите имя службы, слот, роли и экземпляры ролей, для которых вы хотите добавить и включить расширение AzureLogCollector.

        #Specify your cloud service name
        $ServiceName = 'extensiontest2'
        
        #Specify the slot. 'Production' or 'Staging'   
        $slot = 'Production'
        
        #Specified the roles on which the extension will be installed and enabled
        $roles = @("WorkerRole1","WebRole1")
        
        #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
        $instances = @("*")
        
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"

3. Укажите папку с дополнительными данными для сбора файлов (этот шаг является необязательным).

        #add one location
        $a1 = New-Object PSObject 
        
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"  
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
        
        $AdditionalDataList+= $a1
              #more locations can be added....
  
    > [AZURE.NOTE] Так как корневой диск роли не задан жестко, его можно указать с помощью токена `%roleroot%`.

4. Укажите имя и ключ учетной записи хранения Azure, в которую будут отправлены собранные файлы.

        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'

5. Вызовите файл SetAzureServiceLogCollector.ps1 (см. в конце статьи) указанным ниже образом, чтобы включить расширение AzureLogCollector для облачной службы. После выполнения отправленный файл будет размещен по адресу `https://YouareStorageAccountName.blob.core.windows.net/vmlogs`

        .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList

Ниже описаны параметры, которые принимает этот сценарий. (Эта часть кода дублируется ниже.)

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
      [Parameter(Mandatory=$true)]  
    [string]   $ServiceName,
      
    [Parameter(Mandatory=$false)] 
    [string[]] $Roles ,
      
    [Parameter(Mandatory=$false)] 
    [string[]] $Instances,
      
    [Parameter(Mandatory=$false)] 
    [string]   $Slot = 'Production',
      
    [Parameter(Mandatory=$false)] 
    [string]   $Mode = 'Full',
      
    [Parameter(Mandatory=$false)] 
    [string]   $StorageAccountName,
      
    [Parameter(Mandatory=$false)] 
    [string]   $StorageAccountKey,
      
    [Parameter(Mandatory=$false)] 
    [PSObject[]] $AdditionDataLocationList = $null
    )

- *ServiceName*: имя облачной службы.

- *Roles*: список ролей, например WebRole1 или WorkerRole1.

- *Instances*: список имен экземпляров ролей, разделенных запятыми. Чтобы включить все экземпляры роли, используйте строку с подстановочным знаком (*).

- *Slot*: имя слота. Возможные значения: Production или Staging.

- *Mode*: режим сбора данных. Возможные значения: Full или GA.

- *StorageAccountName*: имя учетной записи Azure для хранения собранных данных.

- *StorageAccountKey*: имя ключа учетной записи хранения Azure.

- *AdditionalDataLocationList*: список со следующей структурой:

      { String Name, String Location, String SearchPattern, Bool Recursive }
             
            
## Добавление в качестве расширения виртуальной машины

Подключите Azure PowerShell к своей подписке в соответствии с инструкциями.

1. Укажите имя службы, виртуальную машину и режим сбора данных.

        #Specify your cloud service name
        $ServiceName = 'YourCloudServiceName'
        
        #Specify the VM name
        $VMName = "'YourVMName'"
        
        #Specify the collection mode, "Full" or "GA"
        $mode = "GA"
        
        Specify the additional data folder for which files will be collected (this step is optional).
        
        #add one location
        $a1 = New-Object PSObject 
        
        $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
        $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"  
        $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
        $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"
        
        $AdditionalDataList+= $a1
              #more locations can be added....

2. Укажите имя и ключ учетной записи хранения Azure, в которую будут отправлены собранные файлы.

        $StorageAccountName = 'YourStorageAccountName'
        $StorageAccountKey  = ‘YouStorageAccountKey'
        
3. Вызовите файл SetAzureVMLogCollector.ps1 (см. в конце статьи) указанным ниже образом, чтобы включить расширение AzureLogCollector для облачной службы. После выполнения отправленный файл будет размещен по адресу https://YouareStorageAccountName.blob.core.windows.net/vmlogs

  
Ниже описаны параметры, которые принимает этот сценарий. (Эта часть кода дублируется ниже.)

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
        [Parameter(Mandatory=$true)]  
      [string]   $ServiceName,
        
      [Parameter(Mandatory=$false)] 
      [string] $VMName ,
        
        [Parameter(Mandatory=$false)] 
      [string]   $Mode = 'Full',
        
      [Parameter(Mandatory=$false)] 
      [string]   $StorageAccountName,
        
      [Parameter(Mandatory=$false)] 
      [string]   $StorageAccountKey,
        
      [Parameter(Mandatory=$false)] 
      [PSObject[]] $AdditionDataLocationList = $null
      )

- ServiceName: имя облачной службы.

- VMName: имя виртуальной машины.

- Mode: режим сбора данных. Возможные значения: Full или GA.

- StorageAccountName: имя учетной записи Azure для хранения собранных данных.

- StorageAccountKey: имя ключа учетной записи хранения Azure.

- AdditionalDataLocationList: список со следующей структурой:
      
```
      {
        String Name,
        String Location,
        String SearchPattern,
        Bool   Recursive  
      }
```

## Файлы сценариев PowerShell для расширения

SetAzureServiceLogCollector.ps1

    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
                  [Parameter(Mandatory=$true)]  
                  [string]   $ServiceName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string[]] $Roles ,
                  
                  [Parameter(Mandatory=$false)] 
                  [string[]] $Instances = '*',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Slot = 'Production',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Mode = 'Full',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountKey,
                  
                  [Parameter(Mandatory=$false)] 
                  [PSObject[]] $AdditionDataLocationList = $null
            )
    
    $publicConfig = New-Object PSObject
      
    if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
    {
        $instanceText = $Instances[0] 
        for ($i = 1;$i -lt $Instances.Count;$i++)
        { 
              $instanceText = $instanceText+ "," + $Instances[$i]  
          }
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
    }
    else  #For all instances if not specified.  The value should be a space or *
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
    }
    
    if ($Mode -ne $null ) 
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }
    
    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey 
      
    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName} 
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."  
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }
      
    $ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri
    
    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )  
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }
    
    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
    "publicConfig is:  $publicConfigJSON"
      
    #we just provide a empty privateConfig object
    $privateconfig = "{
    }"
    
    if ($Roles -ne $null)
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose 
    }
    else
    {
          Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose 
    }
    
    #
    #This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
    #
    $SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
    $SasUri = $SasUri + "&restype=container&comp=list"
    Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"


SetAzureVMLogCollector.ps1


    [CmdletBinding(SupportsShouldProcess = $true)]
    
    param (
                  [Parameter(Mandatory=$true)]  
                  [string]   $ServiceName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string] $VMName ,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $Mode = 'Full',
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountName,
                  
                  [Parameter(Mandatory=$false)] 
                  [string]   $StorageAccountKey,
                  
                  [Parameter(Mandatory=$false)] 
                  [PSObject[]] $AdditionDataLocationList = $null
            )
    
    $publicConfig = New-Object PSObject
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"
    
    if ($Mode -ne $null ) 
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
    }
    else
    {
        $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
    }
    
    #
    #we need to get the Sasuri from StorageAccount and containers
    #
    $context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey 
      
    $ContainerName = "azurelogcollectordata"
    $existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName} 
    if ($existingContainer -eq $null)
    {
        "Container ($ContainerName) doesn't exist. Creating it now.."  
        New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
    }
      
    $ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
    $SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
    $publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri
    
    #
    #Add AdditionalData to collect data from additional folders
    #
    if ($AdditionDataLocationList -ne $null )  
    {
      $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
    }
    
    #
    # Convert it to JSON format
    #
    $publicConfigJSON = $publicConfig | ConvertTo-Json
      
    Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"
    
    #
    #we just provide a empty privateConfig object
    #
    $privateconfig = "{
    }"
    
    if ($VMName -ne $null ) 
    {
          $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
          $VM.VM.OSVirtualHardDisk.OS
          
          if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
          {
                Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose 
                
                #
                #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
                #the presence of SubstatusList field.   
                #
                $Completed = $false
                while ($Completed -ne $true)
                {
                        $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                        $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}
                        
                        if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                        {
                            Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                              $Completed = $true
                        }
                        elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                        {
                              $Completed = $false
                              Write-Output "Waiting for operation to complete..."
                        }
                        else
                        {
                              $Completed = $true
                              Write-Output "Operation completed."
            
                        $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                              $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)
    
                      # 
                            # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                              #
                              $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                              $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context
    
                            Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"
                              
                              #
                              #This is an optional step:  Remove the extension after we are done
                              #
                              Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose  
                              
                        }
                        Start-Sleep -s 5
                }
          }
          else
          {
              Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
          }
          
    }
    else
    {
      Write-Output "VM name is not specified, the extension cannot be enabled"
    }
    
## Дальнейшие действия

Теперь вы можете анализировать или копировать журналы, собранные в одном расположении.

<!---HONumber=AcomDC_0128_2016-->