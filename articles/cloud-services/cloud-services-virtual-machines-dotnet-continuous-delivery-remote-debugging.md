---
title: "Включение удаленной отладки при использовании непрерывной доставки | Документация Майкрософт"
description: "Узнайте, как можно включить удаленную отладку при использовании непрерывной доставки, чтобы выполнять развертывание на Azure"
services: cloud-services
documentationcenter: .net
author: TomArcher
manager: douge
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7a9ef4098fec7b464e654a429c8c854ed4bb73fd
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Включение удаленной отладки при использовании непрерывной доставки для публикации на Azure
Когда для публикации в Azure используется [непрерывная доставка](cloud-services-dotnet-continuous-delivery.md), в службе Azure можно включить удаленную отладку для облачных служб или виртуальных машин. Для этого следует выполнить рассмотренную ниже процедуру.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Включение удаленной отладки для облачных служб
1. В агенте сборки настройте начальную среду для Azure, как описано в разделе [Сборка с помощью командной строки для Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Так как для пакета требуется среда выполнения удаленной отладки (msvsmon.exe), установите **инструменты удаленной отладки для Visual Studio 2013**.

    * [Инструменты удаленной отладки для Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
    * [Инструменты удаленной отладки для Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
    * [Инструменты удаленной отладки для Visual Studio 2013 с обновлением 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
    В качестве альтернативы можно скопировать двоичные файлы удаленной отладки из системы, где установлена Visual Studio.

3. Создайте сертификат, как описано в разделе [Общие сведения о сертификатах для облачных служб Azure](cloud-services-certs-create.md). Оставьте .pfx и отпечаток сертификата RDP, и загрузите сертификат в целевую облачную службу.
4. Для сборки и упаковки с включенной функцией удаленной отладки используйте следующие параметры в командной строке MSBuild. (Подставьте фактические пути к системным и проектным файлам для элементов, заключенных в угловые скобки).
   
        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   
    `VSX64RemoteDebuggerPath` — путь к папке, содержащей msvsmon.exe и инструменты удаленной отладки для Visual Studio.
    `RemoteDebuggerConnectorVersion` — это версия пакета Azure SDK в облачной службе. Она должна соответствовать версии, установленной с Visual Studio.
5. Опубликуйте в целевой облачной службе с помощью пакета и файла .cscfg, созданного на предыдущем шаге.
6. Импортируйте сертификат (файл .pfx) на машину, на которой имеется Visual Studio с установленным пакетом Azure SDK для .NET. Импортировать в хранилище сертификатов `CurrentUser\My` обязательно, иначе вы не сможете присоединить отладчик в Visual Studio.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Включение удаленной отладки для виртуальных машин
1. Создайте виртуальную машину Azure. См. статью [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) или [Создание виртуальных машин Windows и управление ими в Visual Studio](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. На [странице классического портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851)откройте панель мониторинга виртуальной машины и найдите **ОТПЕЧАТОК СЕРТИФИКАТА RDP**виртуальной машины. Он используется для `ServerThumbprint` значения в конфигурации расширения.
3. Создайте сертификат клиента, как описано в разделе [Общие сведения о сертификатах для облачных служб Azure](cloud-services-certs-create.md) (оставьте PFX-файл и отпечаток сертификата RDP).
4. Установите Azure Powershell (0.7.4 или более поздней версии), как описано в разделе [Установка и настройка Azure PowerShell](/powershell/azure/overview).
5. Выполните следующий скрипт, чтобы включить расширение RemoteDebug. Замените пути и персональные данные своими собственными (например, укажите имя своей подписки, имя службы и отпечаток).
   
   > [!NOTE]
   > Этот сценарий настроен для Visual Studio 2015. Если вы используете Visual Studio 2013 или Visual Studio 2017, измените назначения `$referenceName` и `$extensionName` ниже, чтобы использовать `RemoteDebugVS2013` или `RemoteDebugVS2017`.

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Импортируйте сертификат (.pfx) на машину, на которой имеется Visual Studio с установленным пакетом Azure SDK для .NET.


