<properties
	pageTitle="Включение удаленной отладки при использовании непрерывной доставки | Microsoft Azure"
	description="Узнайте, как можно включить удаленную отладку при использовании непрерывной доставки, чтобы выполнять развертывание на Azure"
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

# Включение удаленной отладки при использовании непрерывной доставки для публикации на Azure

Когда для публикации в Azure используется [непрерывная доставка](cloud-services-dotnet-continuous-delivery.md), в службе Azure можно включить удаленную отладку для облачных служб или виртуальных машин. Для этого следует выполнить рассмотренную ниже процедуру.

## Включение удаленной отладки для облачных служб

1. В агенте сборки настройте начальную среду для Azure, как описано в разделе [Сборка с помощью командной строки для Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Так как для пакета требуется среда выполнения удаленной отладки (msvsmon.exe), установите [инструменты удаленной отладки для Visual Studio 2015](http://www.microsoft.com/download/details.aspx?id=48155) (или [инструменты удаленной отладки для Microsoft Visual Studio 2013 с обновлением 5](https://www.microsoft.com/download/details.aspx?id=48156) в случае применения Visual Studio 2013). В качестве альтернативы можно скопировать двоичные файлы удаленной отладки из системы, где установлена Visual Studio.
3. Создайте сертификат, как описано в разделе [Общие сведения о сертификатах для облачных служб Azure](cloud-services-certs-create.md). Оставьте .pfx и отпечаток сертификата RDP, и загрузите сертификат в целевую облачную службу.
4. Для сборки и упаковки с включенной функцией удаленной отладки используйте следующие параметры в командной строке MSBuild. (Подставьте фактические пути к системным и проектным файлам для элементов, заключенных в угловые скобки).

		msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

	`VSX64RemoteDebuggerPath` — путь к папке, содержащей msvsmon.exe и инструменты удаленной отладки для Visual Studio.

5. Опубликуйте в целевой облачной службе с помощью пакета и файла .cscfg, созданного на предыдущем шаге.
6. Импортируйте сертификат (файл .pfx) на машину, на которой имеется Visual Studio с установленным пакетом Azure SDK для .NET.

## Включение удаленной отладки для виртуальных машин

1. Создайте виртуальную машину Azure. См. раздел [Создание виртуальной машины под управлением Windows Server](/virtual-machines/virtual-machines-windows-tutorial.md) или [Создание и управление виртуальными машинами Azure в Visual Studio](/vs-azure-tools-virtual-machines-create-manage.md).
2. На [странице классического портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851) откройте панель мониторинга виртуальной машины и найдите **ОТПЕЧАТОК СЕРТИФИКАТА RDP** виртуальной машины. Он используется для `ServerThumbprint` значения в конфигурации расширения.
3. Создайте сертификат клиента, как описано в разделе [Общие сведения о сертификатах для облачных служб Azure](cloud-services-certs-create.md) (оставьте PFX-файл и отпечаток сертификата RDP).
4. Установите Azure Powershell (0.7.4 или более поздней версии), как описано в разделе [Установка и настройка Azure PowerShell](/powershell-install-configure.md).
5. Выполните следующий скрипт, чтобы включить расширение RemoteDebug. Замените пути и персональные данные на свои собственные (например, имя своей подписки, имя службы и отпечаток).

	>[AZURE.NOTE] Этот сценарий настроен для Visual Studio 2015. Если вы используете Visual Studio 2013, измените присваивания `$referenceName` и `$extensionName` ниже, чтобы использовать `RemoteDebugVS2013` (вместо `RemoteDebugVS2015`).

	<pre>
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
</pre>

6. Импортируйте сертификат (.pfx) на машину, на которой имеется Visual Studio с установленным пакетом Azure SDK для .NET.

<!---HONumber=AcomDC_0204_2016-->