---
title: "Автоматизация развертывания приложений с помощью расширений виртуальной машины | Документация Майкрософт"
description: "Руководство по .NET Core для виртуальных машин Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 79c91304-6c1b-4354-a185-fecc129b139d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 2d60af167b8d7805e6f01264de84fb1351d85f98
ms.lasthandoff: 03/16/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-windows-vms"></a>Развертывание приложений с использованием шаблонов Azure Resource Manager для виртуальных машин Windows

Как только все инфраструктурные требования Azure будут определены и на их основе будет создан шаблон развертывания, можно приступать фактическому развертыванию приложения. Развертывание приложения в данном случае сводится к установке фактических двоичных файлов приложения в ресурсы Azure. Чтобы развернуть приложение магазина музыки, на каждой виртуальной машине необходимо установить и настроить .NET Core и IIS. Вам потребуется установить двоичные файлы магазина музыки на виртуальную машину и предварительно создать базу данных музыкального магазина.

В этом документе описана автоматизация развертывания и настройки приложений на виртуальных машинах Azure с помощью расширений виртуальных машин. Здесь будут описаны все зависимости и уникальные настройки. Чтобы оптимизировать процесс, заранее разверните экземпляр решения в подписке Azure, а затем установите шаблон Azure Resource Manager. Полный шаблон можно найти [здесь](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="configuration-script"></a>Скрипт настройки
Расширения виртуальных машин — это специальные программы, которые выполняются на виртуальных машинах для автоматизации настройки. Расширения используются в различных целях, например для автоматизации антивирусных программ, настройки ведения журнала и конфигурации Docker. Расширение пользовательских скриптов можно использовать для запуска любого скрипта на виртуальной машине. В примере магазина музыки расширение пользовательских скриптов используется для настройки виртуальных машинах Windows и установки приложения магазина музыки.

Прежде чем перейти к подробному описанию того, как расширения виртуальных машин объявляются в шаблоне Azure Resource Manager, изучите выполняющийся скрипт. Он настраивает виртуальную машину Windows для размещения приложения магазина музыки. Во время выполнения скрипт устанавливает все необходимое программное обеспечение, устанавливает приложение магазина музыки из системы управления версиями и выполняет подготовку базы данных. 

> Этот пример предназначен только для демонстрации.

```PowerShell
<#
    .SYNOPSIS
        Downloads and configures .Net Core Music Store application sample across IIS and Azure SQL DB.
#>

Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# Firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# Folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# Install iis
Install-WindowsFeature web-server -IncludeManagementTools

# Install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# Download music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music

# Azure SQL connection sting in environment variable
[Environment]::SetEnvironmentVariable("Data:DefaultConnection:ConnectionString", "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30",[EnvironmentVariableTarget]::Machine)

# Pre-create database
$env:Data:DefaultConnection:ConnectionString = "Server=$sqlserver;Database=MusicStore;Integrated Security=False;User Id=$user;Password=$password;MultipleActiveResultSets=True;Connect Timeout=30"
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# Configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Расширение скриптов виртуальной машины
Расширения ВМ можно запустить на виртуальной машине во время сборки, включив ресурс расширения в шаблон Azure Resource Manager. Чтобы добавить расширение, можно использовать мастер добавления ресурсов в Visual Studio или вставить в шаблон развертывания правильно определенный объект JSON. Ресурс расширения скрипта вложен в ресурс виртуальной машины. Это можно увидеть в следующем примере.

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager: [расширение скрипта виртуальной машины](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Обратите внимание, что в объекте JSON, приведенном ниже, скрипт хранится в GitHub. Этот скрипт также может храниться в хранилище BLOB-объектов Azure. Кроме того, шаблоны Azure Resource Manager позволяют создать строку выполнения скрипта так, чтобы значения параметров шаблона можно было использовать в качестве параметров для выполнения скрипта. В этом случае данные указываются при развертывании шаблонов, и эти значения затем можно использовать при выполнении скрипта.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Как упоминалось выше, пользовательские сценарии можно также сохранять в хранилище BLOB-объектов Azure. Существуют два варианта хранения ресурсов сценария в хранилище BLOB-объектов. Можно сделать контейнер и сценарий общедоступными и применить подход, описанный выше. Можно также хранить сценарий в частном хранилище BLOB-объектов, что потребует указать storageAccountName и storageAccountKey в определении ресурсов CustomScriptExtension.

В приведенном ниже примере мы пошли дальше. Хотя во время развертывания можно предоставить имя учетной записи хранения и ключ в качестве параметра или переменной, в шаблонах Resource Manager доступна функция `listKeys`, которая может программно получить ключ учетной записи хранения и вставить его в шаблон во время развертывания.

В приведенном ниже примере определения ресурса CustomScriptExtension пользовательский сценарий уже передан в учетную запись хранения Azure `mystorageaccount9999`, которая существует в группе ресурсов `mysa999rgname`. При развертывании шаблона, содержащего этот ресурс, функция `listKeys` программно получает ключ учетной записи хранения `mystorageaccount9999` в группе ресурсов `mysa999rgname` и вставляет его в шаблон.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://mystorageaccount9999.blob.core.windows.net/container/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]",
      "storageAccountName": "mystorageaccount9999",
      "storageAccountKey": "[listKeys(resourceId('mysa999rgname','Microsoft.Storage/storageAccounts', mystorageaccount9999), '2015-06-15').key1]"
    }
  }
}
```

Основным преимуществом такого подхода является то, что он не требует изменять шаблон или параметры развертывания в случае изменения ключа учетной записи хранения.

Дополнительные сведения об использовании пользовательского расширения сценария см. в статье [Использование расширения пользовательских сценариев для виртуальных машин Linux с шаблонами Azure Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-step"></a>Дальнейшее действие
<hr>

[Ознакомьтесь с другими шаблонами Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)


