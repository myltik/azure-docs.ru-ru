<properties linkid="cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging" urlDisplayName="Enable remote debugging with continuous delivery" pageTitle="Enable remote debugging with continuous delivery" metaKeywords="" description="Learn how to enable remote debugging when using continuous delivery to deploy to Azure." metaCanonical="" services="cloud-services,virtual machines" documentationCenter=".NET" title="Enable remote debugging when using continuous delivery to publish to Azure" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Включение удаленной отладки при использовании непрерывной доставки для публикации на Azure

При использовании [непрерывной доставки][непрерывной доставки] для публикации на Azure в службе Azure можно включить удаленную отладку. Для этого следует выполнить рассмотренную ниже процедуру.

Содержание раздела:

[Включение удаленной отладки для облачных служб][Включение удаленной отладки для облачных служб]

[Включение удаленной отладки для виртуальных машин][Включение удаленной отладки для виртуальных машин]

## <a name="cloudservice"></a>Включение удаленной отладки для облачных служб

1.  В агенте сборки настройте начальную среду для Azure, как описано в разделе [Сборка с помощью командной строки для Azure][Сборка с помощью командной строки для Azure].
2.  Так как для пакета требуется среда выполнения удаленной отладки (msvsmon.exe), установите [инструменты удаленной отладки для Visual Studio 2013][инструменты удаленной отладки для Visual Studio 2013] (или [инструменты удаленной отладки для Visual Studio 2012 с обновлением 4][инструменты удаленной отладки для Visual Studio 2012 с обновлением 4] в случае применения Visual Studio 2012). В качестве альтернативы можно скопировать двоичные файлы удаленной отладки из системы, где установлена Visual Studio.
3.  Создайте сертификат, как описано в разделе [Создание сертификата службы для Azure][Создание сертификата службы для Azure]. Оставьте .pfx и отпечаток сертификата RDP, и загрузите сертификат в целевую облачную службу.
4.  Для сборки и упаковки с включенной функцией удаленной отладки используйте следующие параметры в командной строке MSBuild. (Обновите пути для своих системных файлов и файлов проекта.)

    /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\\Remote Debugger\\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\\Users\\yourusername\\Documents\\visual studio 2013\\Projects\\WindowsAzure1\\WindowsAzure1.sln"

5.  Опубликуйте в целевой облачной службе с помощью пакета и файла .cscfg, созданного на предыдущем шаге.
6.  Импортируйте сертификат (файл .pfx) на машину, на которой имеется Visual Studio с установленным пакетом Azure SDK 2.4.

## <a name="virtualmachine"></a>Включение удаленной отладки для виртуальных машин

1.  Создайте виртуальную машину Azure. См. раздел [Создание виртуальной машины, работающей под управлением Windows Server][Создание виртуальной машины, работающей под управлением Windows Server] или [Создание виртуальных машин Azure в Visual Studio][Создание виртуальных машин Azure в Visual Studio].
2.  На [странице портала Azure][странице портала Azure] откройте панель мониторинга виртуальной машины и найдите «Отпечаток сертификата RDP» виртуальной машины. Он используется для значения ServerThumbprint в конфигурации расширения.
3.  Создайте сертификат клиента, как описано в разделе [Создание сертификата службы для Azure][Создание сертификата службы для Azure] (оставьте .pfx и отпечаток сертификата RDP).
4.  Загрузите [Azure Powershell][Azure Powershell] (версия 0.7.4 или выше) из центра загрузки Майкрософт и установите ее.
5.  Выполните следующий скрипт, чтобы включить расширение RemoteDebug. Замените персональные данные на свои собственные (например, имя своей подписки, имя службы и отпечаток). (ПРИМЕЧАНИЕ. Этот скрипт настроен для Visual Studio 2013. В случае использования Visual Studio 2012 используйте «RemoteDebugVS2013» для ReferenceName и ExtensionName.)

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

        $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
        $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
        $extensionName = "RemoteDebugVS2013"
        $version = "1.*"
        $publicConfiguration = "true56D7D1B25B472268E332F7FC0C87286458BFB6B2E7DCB00CB916C468CC3228261D6E4EE45C8ED3C63039831398"

        $vm | Set-AzureVMExtension `
        -ReferenceName $referenceName `
        -Publisher $publisher `
        -ExtensionName $extensionName `
        -Version $version `
        -PublicConfiguration $publicConfiguration

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

6.  Импортируйте сертификат (.pfx) на машину, на которой имеется Visual Studio с установленным пакетом Azure SDK для .NET 2.4.

  [непрерывной доставки]: http://azure.microsoft.com/ru-ru/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Включение удаленной отладки для облачных служб]: #cloudservice
  [Включение удаленной отладки для виртуальных машин]: #virtualmachine
  [Сборка с помощью командной строки для Azure]: http://msdn.microsoft.com/ru-ru/library/hh535755.aspx
  [инструменты удаленной отладки для Visual Studio 2013]: http://www.microsoft.com/ru-ru/download/details.aspx?id=40781
  [инструменты удаленной отладки для Visual Studio 2012 с обновлением 4]: http://www.microsoft.com/ru-ru/download/details.aspx?id=38184
  [Создание сертификата службы для Azure]: http://msdn.microsoft.com/library/azure/gg432987.aspx
  [Создание виртуальной машины, работающей под управлением Windows Server]: http://azure.microsoft.com/ru-ru/documentation/articles/virtual-machines-windows-tutorial/
  [Создание виртуальных машин Azure в Visual Studio]: http://msdn.microsoft.com/ru-ru/library/azure/dn569263.aspx
  [странице портала Azure]: http://go.microsoft.com/fwlink/p/?LinkID=269851
  [Azure Powershell]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
