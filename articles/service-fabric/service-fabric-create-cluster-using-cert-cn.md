---
title: Создание кластера Azure Service Fabric с использованием общего имени сертификата | Документация Майкрософт
description: Подробные сведения о создании кластера Service Fabric с помощью общего имени сертификата на основе шаблона.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 8725dd1931b120b0369d0810fa49108a00c71e8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211071"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Развертывание кластера Service Fabric, использующего вместо отпечатка общее имя сертификата
Два сертификата не могут иметь один и тот же отпечаток. Это затрудняет смену сертификатов кластера и управление им. Тем не менее несколько сертификатов могут иметь одно общее имя или тему.  Кластер, использующий общие имена сертификата, упрощает управление сертификатами. В этой статье описывается развертывание кластера Service Fabric для использования общего имени сертификата вместо отпечатка сертификата.
 
## <a name="get-a-certificate"></a>Получение сертификата
Сначала получите сертификат в [центре сертификации (ЦС)](https://wikipedia.org/wiki/Certificate_authority).  Общее имя сертификата должно быть именем узла кластера.  Например, myclustername.southcentralus.cloudapp.azure.com.  

В целях тестирования можно получить сертификат, подписанный ЦС, из бесплатного или открытого центра сертификации.

> [!NOTE]
> Самозаверяющие сертификаты, включая созданные при развертывании кластера Service Fabric на портале Azure, не поддерживаются.

## <a name="upload-the-certificate-to-a-key-vault"></a>Отправка сертификата в хранилище ключей
В Azure кластер Service Fabric развертывается в масштабируемом наборе виртуальных машин.  Отправьте сертификат в хранилище ключей.  При развертывании кластера сертификат устанавливается на масштабируемом наборе виртуальных машин, в котором запускается кластер.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

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
```

## <a name="download-and-update-a-sample-template"></a>Загрузка и обновление образца шаблона
В этой статье используется пример шаблона [защищенного кластера с 5 узлами](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) и его параметры. Скачайте на компьютер файлы *azuredeploy.json* и *azuredeploy.parameters.json*.

### <a name="update-parameters-file"></a>Обновление файла параметров
Сначала откройте файл *azuredeploy.parameters.json* в текстовом редакторе и добавьте следующее значение параметра:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Затем задайте для параметров *certificateCommonName*, *sourceVaultValue* и *certificateUrlValue* значения, возвращенные предыдущим скриптом:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Обновление файла шаблона
Затем откройте файл *azuredeploy.json* в текстовом редакторе и создайте три обновления для поддержки общего имени сертификата.

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

2. Задайте значение "2018-02-01" для переменной *sfrpApiVersion*:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. В ресурсе **Microsoft.Compute/virtualMachineScaleSets** обновите расширение виртуальной машины, чтобы использовать общее имя в параметрах сертификата вместо отпечатка.  В параметрах **virtualMachineProfile**->**extenstionProfile**->**extensions**->**properties**->**settings**->**certificate** добавьте `"commonNames": ["[parameters('certificateCommonName')]"],` и удалите `"thumbprint": "[parameters('certificateThumbprint')]",`.
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

4.  В ресурсе **Microsoft.ServiceFabric/clusters** обновите версию API до версии "2018-02-01".  Кроме того, добавьте параметр **certificateCommonNames**, свойство **commonNames** и удалите параметр **certificate** (со свойством отпечатка), как в следующем примере:
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
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о безопасности кластеров см. в статье [Сценарии защиты кластера Service Fabric](service-fabric-cluster-security.md).
* Дополнительные сведения о [выделении сертификата кластера](service-fabric-cluster-rollover-cert-cn.md).
* [Добавление и удаление сертификатов для кластера Service Fabric в Azure](service-fabric-cluster-security-update-certs-azure.md).

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png