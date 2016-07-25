<properties
	pageTitle="Обзор сценария развертывания проекта группы ресурсов Azure | Microsoft Azure"
	description="Описание механизма работы сценария PowerShell в проектах развертывания группы ресурсов Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="tfitzmac"
	manager="timlt"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="05/08/2016"
	ms.author="tomfitz" />

# Обзор сценария развертывания проекта группы ресурсов Azure

С помощью проектов по развертыванию группы ресурсов Azure вы сможете размещать и развертывать файлы и другие артефакты в Azure. При создании проекта развертывания диспетчера ресурсов Azure в Visual Studio к проекту добавляется сценарий PowerShell под названием **Deploy-AzureResourceGroup.ps1**. В этом разделе содержатся сведения о том, как работает этот сценарий и как использовать его независимо от того, входит ли он в Visual Studio.

## Как работает сценарий?

Сценарий Deploy-AzureResourceGroup.ps1 делает две вещи, которые важны для рабочего процесса развертывания.

- Отправка любых файлов и компонентов, необходимых для развертывания шаблона
- Развертывание шаблона

Первая часть сценария отправляет файлы и артефакты для развертывания, а последний командлет в сценарии выполняет фактическое развертывание шаблона. Например, если виртуальную машину нужно настроить с помощью сценария, сценарий развертывания сначала безопасно отправляет сценарий настройки в учетную запись хранения Azure. Это обеспечивает доступ к диспетчеру ресурсов Azure для настройки виртуальной машины во время подготовки.

Так как не всем развертываниям шаблона нужны дополнительные артефакты, которые подлежат отправке, выполняется оценка параметра переключателя *uploadArtifacts*. Если нужно отправить какие-либо артефакты, установите переключатель в положение *uploadArtifacts* при вызове сценария. Обратите внимание, что основной файл шаблона и файл параметров отправлять не нужно. Следует отправлять только другие файлы, такие как сценарии настройки, вложенные шаблоны развертывания и файлы приложения.

## Подробное описание сценария

Ниже приводится описание тех разделов Deploy-AzureResourceGroup.ps1, которые выполняет сценарий Azure PowerShell.

>[AZURE.NOTE] Эта статья содержит описание версии 1.0 сценария Deploy-AzureResourceGroup.ps1.

1.	Объявите параметры, необходимые для проекта развертывания диспетчера ресурсов Azure. Некоторые параметры имеют значения по умолчанию, установленные при создании проекта. Вы можете изменить эти значения по умолчанию в сценарии или добавить другие значения параметров перед его выполнением.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |Параметр|Описание|
    |---|---|
    |$ResourceGroupLocation|Регион или расположение центра обработки данных группы ресурсов, таких как **Запад США** или **Восточная Азия**.|
    |$ResourceGroupName|Имя группы ресурсов Azure.|
    |$UploadArtifacts|Двоичное значение, указывающее на то, нужно ли отправлять артефакты в Azure из вашей системы.|
    |$storageAccountName|Имя учетной записи хранения Azure, в которую вы отправляете артефакты.|
    |$StorageAccountResourceGroupName|Имя группы ресурсов Azure, которая содержит учетную запись хранения.|
    |$StorageContainerName|Имя контейнера хранилища, используемого для отправки артефактов.|
    |$TemplateFile|Путь к файлу развертывания (`<app name>.json`) в проекте группы ресурсов Azure.|
    |$TemplateParametersFile|Путь к файлу параметров (`<app name>.parameters.json`) в проекте группы ресурсов Azure.|
    |$ArtifactStagingDirectory|Путь к папке компьютера, в которую локально отправляются артефакты, включая корневую папку сценария PowerShell. Этот путь может быть абсолютным или относительным по отношению к расположению сценария.|
    |$AzCopyPath|Путь к папке, в которую средство AzCopy.exe копирует свои ZIP-файлы, включая корневую папку сценария PowerShell. Этот путь может быть абсолютным или относительным по отношению к расположению сценария.|
    |$DSCSourceFolder|Путь к исходной папке DSC (Настройка требуемого состояния), включая корневую папку сценария PowerShell. Этот путь может быть абсолютным или относительным по отношению к расположению сценария. Для получения дополнительной информации (если таковая имеется) см. статью [Введение в расширение Azure PowerShell DSC (Настройка требуемого состояния)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx).|

1.	Проверьте, нужно ли отправлять артефакты в Azure. Если нет, перейдите к шагу 11. В противном случае выполните следующие действия.

1.	Преобразуйте все переменные с относительными путями в абсолютные пути. Например, измените путь `..\Tools\AzCopy.exe` на `C:\YourFolder\Tools\AzCopy.exe`. Кроме того, инициализируйте переменные *ArtifactsLocationName* и *ArtifactsLocationSasTokenName*, установив значение Null. В качестве параметров для шаблона можно использовать *ArtifactsLocation* и *SaSToken*. Если после прочтения в файле параметров для них заданы значения Null, сценарий создает значения для них.

    Инструменты Azure Tools используют значения параметров *\_artifactsLocation* и *\_artifactsLocationSasToken* в шаблоне, чтобы управлять артефактами. Если сценарий PowerShell находит параметры с такими именами, но значения параметров не указаны, он отправляет артефакты и возвращает соответствующие значения для этих параметров. Затем он передает их в командлет через `@OptionsParameters`.

	|Переменная|Описание|
    |---|---|
    |ArtifactsLocationName|Путь к папке расположения артефактов Azure.|
    |ArtifactsLocationSasTokenName|Имя маркера SAS (подписанный URL-адрес), используемого сценарием при проверке подлинности в служебной шине. Дополнительные сведения см. в статье [Проверка подлинности подписи при общем доступе с помощью служебной шины](service-bus-shared-access-signature-authentication.md).|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	Этот раздел проверяет наличие в файле <имя\_приложения>.parameters.json (который называется файлом параметров) родительского узла с именем **parameters** (в блоке `else`). В противном случае родительский узел отсутствует. Допускается любой формат.
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	Выполните итерацию через набор параметров JSON. Если в качестве значения параметра задано *\_artifactsLocation* или *\_artifactsLocationSasToken*, то присвойте переменной *$OptionalParameters* это значение. Это помешает сценарию случайно перезаписать какие-либо значения параметров, указанные вами.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	Получите ключ учетной записи хранения и контекст для ресурсов учетной записи хранения, используемые для хранения артефактов для развертывания.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	Если вы используете PowerShell DSC для настройки виртуальной машины, расширение DSC требует, чтобы артефакты были сохранены в одном ZIP-файле. Поэтому нужно создать ZIP-файл архива для конфигурации DSC. Для этого проверьте, существует ли $DSCSourceFolder. Если существует конфигурация DSC, удалите ее и создайте сжатый файл с именем dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	Если путь для артефактов Azure не указан в файле параметров, задайте путь сценария PowerShell для использования при отправке артефактов. Чтобы сделать это, создайте путь, используя сочетание пути к конечной точке учетной записи хранения и имени контейнера хранилища. Затем обновите файл параметров, указав новый путь.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	Используйте служебную программу **AzCopy** (расположенную в папке **Средства** проекта развертывания группы ресурсов Azure) для копирования любых файлов из пути размещения локального хранилища в учетную запись хранилища Azure в Интернете. Если этот шаг завершается ошибкой, выйдите из сценария, так как развертывание, очевидно, не будет выполнено успешно без необходимых артефактов.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	Если маркер SAS для расположения артефактов не указан в файле параметров, создайте его, чтобы предоставить временный доступ только для чтения к контейнеру хранилища в Интернете. Затем передайте этот маркер SAS в командную строку как optionalParameter. Обратите внимание, что все параметры, передаваемые в командную строку, будут иметь приоритет над значениями, указанными в файле параметров.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Создайте группу ресурсов, если она еще не существует, и проверьте файл шаблона и параметров на наличие ошибок проверки, которые могут помешать успешному выполнению развертывания.

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. После этого разверните шаблон. Этот код создает уникальное имя для развертывания с помощью метки времени.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## Развертывание группы ресурсов

### Развертывание группы ресурсов в Visual Studio

1. В контекстном меню проекта группы ресурсов Azure последовательно выберите пункты **Развернуть** > **Новое развертывание**.

    ![][0]

1. В диалоговом окне **Развертывание в группе ресурсов** укажите существующую группу ресурсов для развертывания в раскрывающемся списке или выберите команду **&lt;Создать&gt;**, чтобы создать новую группу ресурсов.

    ![][1]

1. При появлении соответствующего запроса введите имя и расположение группы ресурсов в диалоговом окне **Создание группы ресурсов**, а затем нажмите кнопку **Создать**.

    ![][2]

1. Нажмите кнопку **Изменить параметры**, чтобы вызвать диалоговое окно **Изменение параметров**, а затем укажите недостающие значения параметров.

    ![][3]

	>[AZURE.NOTE] Если для каких-либо обязательных параметров необходимо указать значения, это окно отобразится автоматически в процессе развертывания.

    ![][4]

1. Закончив вводить значения параметров, нажмите кнопку **Сохранить**, а затем кнопку **Развернуть**.

    Сценарий развертывания (Deploy-AzureResourceGroup.ps1) будет выполняться, а ваш шаблон вместе с артефактами будет развернут в Azure.

### Развертывание группы ресурсов с помощью PowerShell

Если вы хотите запустить сценарий без использования команды «Развернуть» и пользовательского интерфейса Visual Studio, в контекстном меню сценария выберите параметр **Открыть с помощью PowerShell ISE**.

![][5]


## Примеры развертывания команд

### Развертывание с использованием значений по умолчанию

В этом примере показано, как запустить сценарий, используя значения параметров по умолчанию. (Для параметра расположения не задано значение по умолчанию, поэтому вам нужно его указать.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### Развертывание с переопределением значений по умолчанию

В этом примере показано, как запустить сценарий для развертывания файлов шаблонов и параметров, которые отличаются от значений по умолчанию.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### Развертывание с использованием параметра UploadArtifacts для промежуточного развертывания

В этом примере показано, как запустить сценарий для отправки артефактов из папки выпуска и развернуть шаблоны, не являющиеся шаблонами по умолчанию.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

В этом примере показано, как запустить сценарий в задаче Azure PowerShell в Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## Дальнейшие действия
Чтобы получить подробные сведения о диспетчере ресурсов Azure, ознакомьтесь с [общими сведениями о диспетчере ресурсов Azure](resource-group-overview.md).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

<!---HONumber=AcomDC_0713_2016-->