---
title: "Создание кластера Service Fabric на платформе Windows в Azure | Документация Майкрософт"
description: "Узнайте, как развернуть кластер Service Fabric на платформе Windows в существующей виртуальной сети с помощью PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 7cee4f8d68062dcfd2b6f61d55319160a2a80a98
ms.contentlocale: ru-ru
ms.lasthandoff: 09/27/2017

---

# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Развертывание кластера Service Fabric на платформе Windows в виртуальной сети Azure
Это руководство представляет первую часть цикла. Вы узнаете, как развернуть кластер Service Fabric на платформе Windows в подсети существующей виртуальной сети с помощью PowerShell. После окончания этого учебника у вас будет кластер в облаке, в который можно разворачивать приложения.  Создание кластера Linux с помощью Azure CLI описывается в разделе [Развертывание безопасного кластера Service Fabric на платформе Linux в виртуальной сети Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью PowerShell;
> * создание хранилища ключей и передача сертификата;
> * создание защищенного кластера Service Fabric в Azure с помощью PowerShell;
> * Защита кластера с помощью сертификата X.509
> * Подключение к кластеру с помощью PowerShell
> * Удаление кластера

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * создание защищенного кластера в Azure;
> * [развертывание службы управления API с помощью Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Установите [пакет SDK для Service Fabric и модуль PowerShell](service-fabric-get-started.md)
- Установите модуль [Azure PowerShell версии 4.1 или более поздней](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Ниже приведены процедуры для создания кластера Service Fabric с пятью узлами. Чтобы рассчитать затраты, связанные с запуском кластера Service Fabric в Azure, используйте [калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Вход в Azure и выбор подписки
В этом руководстве используется Azure PowerShell. При запуске нового сеанса PowerShell войдите в свою учетную запись Azure и выберите подписку перед выполнением команд Azure.
 
Выполните приведенный ниже сценарий, чтобы войти в учетную запись Azure и выбрать подписку.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов для развертывания и задайте для нее имя и расположение.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroup -Name $ResourceGroupName -Location centralus
```

## <a name="deploy-the-network-topology"></a>Развертывание топологии сети
Настройте топологию сети, в которой будут развернуты служба управления API и кластер Service Fabric. Шаблон Resource Manager [Network.json][network-arm] настроен для создания виртуальной сети, а также подсети и группы безопасности сети (NSG) для Service Fabric и подсети и группы безопасности сети для службы управления API. Дополнительные сведения о виртуальных сетях, подсетях и NSG можно получить [здесь](../virtual-network/virtual-networks-overview.md).

Файл параметров [network.parameters.json][network-parameters-arm] содержит имена подсетей и групп безопасности сети, в которых будут развернуты Service Fabric и служба управления API.  Развертывание службы управления API описывается в [этом руководстве](service-fabric-tutorial-deploy-api-management.md). В рамках этого руководства изменять значения параметров не нужно. Эти значения используются в шаблонах Resource Manager для Service Fabric.  Если изменить эти значения здесь, потребуется изменить их и в других шаблонах Resource Manager, используемых в этом руководстве и [руководстве по развертыванию службы управления API](service-fabric-tutorial-deploy-api-management.md). 

Скачайте шаблон Resource Manager и файл параметров:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Используйте следующую команду PowerShell для развертывания шаблона Resource Manager и файла параметров для настройки сети:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="create-a-key-vault-and-upload-a-certificate"></a>создание хранилища ключей и передача сертификата;
Шаблон Resource Manager кластера Service Fabric, используемый на следующем шаге, настроен для создания безопасного кластера с сертификатом безопасности. Сертификат используется, чтобы обеспечить для кластера безопасный обмен данными между узлами, а также чтобы управлять доступом пользователя к кластеру Service Fabric. Служба управления API также использует этот сертификат, чтобы получить доступ к службе именования Service Fabric для обнаружения служб. Для этого требуется наличие сертификата в хранилище ключей для обеспечения безопасности кластера.

Приведенный ниже сценарий создает хранилище ключей в Azure, создает самозаверяющий сертификат и отправляет этот сертификат в хранилище ключей.  Если вы хотите использовать существующий сертификат, задайте для параметра **$CreateSelfSignedCertificate** значение $false и укажите расположение сертификата в параметре **$ExistingPfxFilePath**.

```powershell
$VaultResourceGroupName = 'ryanwikeyvaultgroup'
$VaultName= 'ryanwikeyvault'
$Location = "westus"
$CertificateName = "ryanwicertificate1"
$Password = 'mypa$$word!'
$DnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$OutputPath = "C:\MyCertificates" # location where you want the .PFX to be stored
$CreateSelfSignedCertificate = $true
$ExistingPfxFilePath = 'C:\MyCertificates\ryanwicertificate1.pfx'

$ErrorActionPreference = 'Stop'

Write-Host "Switching context to SubscriptionId $SubscriptionId"
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# New-AzureRmResourceGroup is idempotent as long as the location matches
Write-Host "Ensuring ResourceGroup $VaultResourceGroupName in $Location"
New-AzureRmResourceGroup -Name $VaultResourceGroupName -Location $Location -Force | Out-Null
$resourceId = $null

try
{
    $existingKeyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName
    $resourceId = $existingKeyVault.ResourceId

    Write-Host "Using existing vault $VaultName in $($existingKeyVault.Location)"
}
catch
{
}

if(!$existingKeyVault)
{
    Write-Host "Creating new vault $VaultName in $location"
    $newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName -Location $Location -EnabledForDeployment
    $resourceId = $newKeyVault.ResourceId
}

if($CreateSelfSignedCertificate)
{
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force

    $NewPfxFilePath = Join-Path $OutputPath $($CertificateName+".pfx")

    Write-Host "Creating new self signed certificate at $NewPfxFilePath"
    
    ## Changes to PSPKI version 3.5.2 New-SelfSignedCertificate replaced by New-SelfSignedCertificateEx
    $PspkiVersion = (Get-Module PSPKI).Version
    if($PSPKIVersion.Major -ieq 3 -And $PspkiVersion.Minor -ieq 2 -And $PspkiVersion.Build -ieq 5) {
        New-SelfsignedCertificateEx -Subject "CN=$DnsName" -EKU "Server Authentication", "Client authentication" -KeyUsage "KeyEncipherment, DigitalSignature" -Path $NewPfxFilePath -Password $securePassword -Exportable
    }
    else {
        New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName $DnsName | Export-PfxCertificate -FilePath $NewPfxFilePath -Password $securePassword | Out-Null
    }

    $ExistingPfxFilePath = $NewPfxFilePath
}

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $Password

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $Password
   } | ConvertTo-Json

    $contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
    $content = [System.Convert]::ToBase64String($contentbytes)

    $secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

Write-Host "Writing secret to $CertificateName in vault $VaultName"
$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertificateName -SecretValue $secretValue

$output = @{};
$output.SourceVault = $resourceId;
$output.CertificateURL = $secret.Id;
$output.CertificateThumbprint = $cert.Thumbprint;

Write-Host "Source vault: " $output.SourceVault
Write-Host "Certificate URL: " $output.CertificateURL
Write-Host "Certificate Thumbprint: " $output.CertificateThumbprint
```

## <a name="deploy-the-service-fabric-cluster"></a>Развертывание кластера Service Fabric
Когда сетевые ресурсы развернуты, необходимо развернуть кластер Service Fabric в виртуальной сети в подсети и группе безопасности сети, используемых для кластера Service Fabric. Для развертывания кластера в существующих виртуальной сети и подсети (развернутых ранее в этой статье) требуется шаблон Resource Manager.  Дополнительные сведения см. в разделе [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). В рамках этого цикла руководств шаблон предварительно настроен для использования имен виртуальной сети, подсети и группы безопасности сети, настроенных на предыдущем шаге.  Скачайте шаблон Resource Manager и файл параметров:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Заполните пустые параметры **clusterName**, **adminUserName**, **adminPassword**, **certificateThumbprint**,  **certificateUrlValue** и **sourceVaultValue** в файле *cluster.parameters.json* для развертывания.  Если у вас есть сертификат, переданный ранее в хранилище ключей, заполните значения **certificateThumbprint**, **certificateUrlValue** и **sourceVaultValue** для этого сертификата.

Используйте следующую команду PowerShell для развертывания шаблона Resource Manager и файла параметров для создания кластера Service Fabric:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
```

## <a name="connect-to-the-secure-cluster"></a>Подключение к безопасному кластеру
Подключитесь к кластеру с помощью модуля Service Fabric PowerShell, который установлен вместе с пакетом SDK для Service Fabric.  Сначала установите сертификат в личное хранилище (Мое хранилище) текущего пользователя компьютера.  Выполните следующую команду PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Теперь все готово для подключения к защищенному кластеру.

Модуль PowerShell **Service Fabric** предоставляет многие командлеты для управления кластерами, приложениями и службами Service Fabric.  Для подключения к безопасному кластеру используйте командлет [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Сведения об отпечатке сертификата и конечной точке подключения можно найти в выходных данных из предыдущего шага.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Убедитесь, что подключение установлено и кластер находится в работоспособном состоянии, выполнив командлет [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Кластер, который вы только что создали, используется в других статьях этого цикла руководств. Если вы не собираетесь немедленно приступить к следующей статье, то можете удалить кластер, чтобы за него не взималась плата. Чтобы удалить кластер и все ресурсы, который он использует, проще всего удалить группу ресурсов.

Войдите в Azure и выберите идентификатор подписки, в которой вы хотите удалить кластер.  Идентификатор подписки можно узнать, войдя на [портал Azure](http://portal.azure.com). Удалите группу ресурсов и все ресурсы кластера с помощью командлета [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * создание виртуальной сети в Azure с помощью PowerShell;
> * создание хранилища ключей и передача сертификата;
> * Создание защищенного кластера Service Fabric в Azure с помощью PowerShell
> * Защита кластера с помощью сертификата X.509
> * Подключение к кластеру с помощью PowerShell
> * Удаление кластера

Теперь перейдите к следующему руководству, из которого вы узнаете, как развернуть службу управления API с помощью Service Fabric.
> [!div class="nextstepaction"]
> [Развертывание службы управления API](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

