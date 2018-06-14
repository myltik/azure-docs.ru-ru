---
title: Обновление кластера Azure Service Fabric для использования общего имени сертификата | Документация Майкрософт
description: Сведения о переключении кластера Service Fabric с использования отпечатков сертификатов на общее имя сертификата.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 39dc5800edd743cdc950c7a96f7633fb4c0a7c45
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210347"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Переход с отпечатка на общее имя сертификата для кластера
Два сертификата не могут иметь один и тот же отпечаток. Это затрудняет смену сертификатов кластера и управление им. Тем не менее несколько сертификатов могут иметь одно общее имя или тему.  Переключение развернутого кластера с использования отпечатков сертификата на использование общих имен сертификатов упрощает управление им. В этой статье описывается обновление выполняющегося кластера Service Fabric для использования общего имени сертификата вместо отпечатка сертификата.
 
## <a name="get-a-certificate"></a>Получение сертификата
Сначала получите сертификат в [центре сертификации (ЦС)](https://wikipedia.org/wiki/Certificate_authority).  Общее имя сертификата должно быть именем узла кластера.  Например, myclustername.southcentralus.cloudapp.azure.com.  

В целях тестирования можно получить сертификат, подписанный ЦС, из бесплатного или открытого центра сертификации.

> [!NOTE]
> Самозаверяющие сертификаты, включая созданные при развертывании кластера Service Fabric на портале Azure, не поддерживаются.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Отправка сертификата и его установка в масштабируемом наборе
В Azure кластер Service Fabric развертывается в масштабируемом наборе виртуальных машин.  Отправьте сертификат в хранилище ключей, а затем установите его на масштабируемом наборе виртуальных машин, в котором запускается кластер.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgropu"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

## <a name="download-and-update-the-template-from-the-portal"></a>Скачивание и обновление шаблона с портала
Сертификат установлен в базовом масштабируемом наборе, но вам также необходимо обновить кластер Service Fabric для использования этого сертификата и его общего имени.  Теперь загрузите шаблон для развертывания кластера.  Войдите на [портал Azure](https://portal.azure.com) и перейдите к группе ресурсов, в которой размещен кластер.  В разделе **Настройки** выберите **Развертывания**.  Выберите последние развертывания и щелкните **Просмотреть шаблон**.

![Просмотр шаблонов][image1]

Скачайте шаблон и файлы JSON с параметрами на локальный компьютер.

Сначала откройте файл с параметрами в текстовом редакторе и добавьте следующее значение параметра:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Затем откройте файл шаблона в текстовом редакторе и создайте три обновления для поддержки общего имени сертификата.

1. В разделе **параметров** добавьте параметр *certificateCommonName*:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Кроме того, рассмотрите возможность удаления *certificateThumbprint*, так как он больше не понадобится.

2. В ресурсе **Microsoft.Compute/virtualMachineScaleSets** обновите расширение виртуальной машины, чтобы использовать общее имя в параметрах сертификата вместо отпечатка.  В параметрах **virtualMachineProfile**->**extenstionProfile**->**extensions**->**properties**->**settings**->**certificate** добавьте `"commonNames": ["[parameters('certificateCommonName')]"],` и удалите `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
    "virtualMachineProfile": {
              "extensionProfile": {
                "extensions": [
                  {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": true,
                      "protectedSettings": {
                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                      },
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[variables('vmNodeType0Name')]",
                        "dataPath": "D:\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "enableParallelJobs": true,
                        "nicPrefixOverride": "[variables('subnet0Prefix')]",
                        "certificate": {
                          "commonNames": ["[parameters('certificateCommonName')]"],                          
                          "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
                      "typeHandlerVersion": "1.0"
                    }
                  },
    ```

3.  В ресурсе **Microsoft.ServiceFabric/clusters** обновите версию API до версии "2018-02-01".  Кроме того, добавьте параметр **certificateCommonNames**, свойство **commonNames** и удалите параметр **certificate** (со свойством отпечатка), как в следующем примере:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Развертывание обновленного шаблона
Повторно разверните обновленный шаблон после внесения изменений.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"

New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" -Verbose
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о безопасности кластеров см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).
* Дополнительные сведения о [выделении сертификата кластера](service-fabric-cluster-rollover-cert-cn.md).
* [Добавление и удаление сертификатов для кластера Service Fabric в Azure](service-fabric-cluster-security-update-certs-azure.md).

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png