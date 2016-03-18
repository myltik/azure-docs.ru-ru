<properties
	pageTitle="Обновление сценария PowerShell для проекта группы ресурсов Azure | Microsoft Azure"
	description="Статья описывает шаги, необходимые для ручного обновления сценария PowerShell, входящего в состав проекта развертывания группы ресурсов Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/04/2016"
	ms.author="tarcher" />

# Обновление сценария PowerShell для проекта группы ресурсов Azure

Проект развертывания группы ресурсов Azure в Visual Studio использует для подготовки и развертывания ресурсов в Azure сценарий Azure PowerShell. Этот сценарий разработан для Azure PowerShell версии 0.9.8. Но уже выпущена новая версия — Azure PowerShell 1.0.

Эта новая версия Azure PowerShell предоставляет новые возможности и повышает качество работы с диспетчером ресурсов Azure. К сожалению, она несовместима со сценариями для более ранней версии из-за изменений, внесенных в некоторые функции командлетов. Вы можете устранить эту проблему, изменив и запустив сценарий вне Visual Studio в окне командной строки PowerShell. Эта статья предоставит вам информацию о том, какие изменения и в каких местах следует сделать. Помимо описания отдельных правок, в конце статьи вы найдете полный текст измененного сценария.

## Варианты решения

После установки нового Azure PowerShell в вашем проекте группы ресурсов Azure проблемы могут проявиться в двух местах.

- Диалоговое окно развертывания в Visual Studio не сможет найти новые командлеты Azure и предложит вам установить их. Но эти модули в новой версии командлетов имеют другие имена, поэтому Visual Studio не сможет их установить. Поэтому попытка установить их в Visual Studio приведет к замкнутому циклу.

- Если вы запустите сценарий вне Visual Studio в окне командной строки PowerShell, то можете увидеть следующую ошибку.

	*Термин «Switch-AzureMode» не распознан как имя командлета, функции, файла сценария или действующей программы. Проверьте правильность написания имени, а если был задан путь, убедитесь, что он указан правильно, и повторите попытку.*

Если новый PowerShell Azure не позволяет развернуть проект группы ресурсов Azure, вы можете решить эту проблему описанными далее способами.

- Вы можете удалить последнюю версию Azure PowerShell и повторно установить предыдущую версию (0.9.8) с помощью [Установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).

- Вы можете внести в сценарий PowerShell проекта развертывания несколько точечных изменений и запустить этот сценарий вручную в окне командной строки PowerShell. Измененный сценарий не удастся запустить через пункт меню **Развернуть** в Visual Studio. Далее в этой статье перечислены действия по обновлению сценария PowerShell, а также приводится полный текст обновленного сценария.

## Обновление сценария PowerShell
В каждой инструкции указан номер соответствующей строки. Чтобы включить в Visual Studio нумерацию строк, изучите статью [Практическое руководство. Отображение номеров строк в редакторе](https://msdn.microsoft.com/library/ms165340.aspx).

1. В строке 61 замените следующий код:

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	на:

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
	```

1. В строке 87 замените следующий код:

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	на:

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. В строке 95 замените следующий код:

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	на новый код:

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	New-AzureRMResourceGroupDeployment `
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Verbose -Force
	```

## Обновленный сценарий
Далее приводится измененный сценарий, в который уже внесены все описанные выше изменения.

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-Verbose -Force

```

<!---HONumber=AcomDC_0218_2016-->