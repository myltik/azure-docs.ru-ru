---
title: Расширение виртуальной машины Key Vault для Windows | Документы Майкрософт
description: С помощью расширения виртуальной машины вы можете развернуть агент, выполняющий автоматическое обновление секретов Key Vault на виртуальных машинах.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944756"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины Key Vault для Windows

## <a name="overview"></a>Обзор

Расширение виртуальной машины Key Vault обеспечивает автоматическое обновление секретов, хранящихся в Azure Key Vault. В частности, расширение отслеживает список наблюдаемых сертификатов, хранящихся в хранилищах ключей, и при обнаружении изменения извлекает и устанавливает соответствующие сертификаты. Сейчас корпорация Майкрософт поддерживает и публикует расширение виртуальной машины Key Vault на виртуальных машинах Windows. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины Key Vault для Windows. 

## <a name="prerequisites"></a>предварительным требованиям

Чтобы пройти проверку подлинности в службе Key Vault, расширение виртуальной машины Key Vault зависит от расширения виртуальной машины управляемого удостоверения службы. Более подробные сведения см. в документации по расширению виртуальной машины MSI.

### <a name="operating-system"></a>Операционная система

Сейчас расширение виртуальной машины Key Vault поддерживает все версии Windows.

| Дистрибутив | Version (версия) |
|---|---|
| Windows | Core |

### <a name="internet-connectivity"></a>Подключение к Интернету

Для расширения виртуальной машины Key Vault для Windows требуется, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-schema"></a>Схема расширения

В следующем JSON-файле показана схема для расширения виртуальной машины Key Vault. Расширению не требуются защищенные параметры, так как все его параметры считаются информационными и не влияют на безопасность. Для расширения необходим список отслеживаемых секретов, указание частоты опроса и конечное хранилище сертификатов. В частности:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2016-10-01 | дата |
| publisher | Microsoft.Azure.KeyVault.Edp | строка |
| Тип | KeyVaultForWindows | строка |
| typeHandlerVersion | 0,0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | строка |
| certificateStoreLocation  | LOCAL_MACHINE | строка |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | массив строк


## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих обновления сертификатов, выполняемого после развертывания. Расширения можно развертывать на отдельных виртуальных машинах или в масштабируемых наборах виртуальных машин. Для обоих типов шаблонов используются общие схема и конфигурация. 

Файл конфигурации JSON для расширения виртуальной машины должен быть вложен во фрагмент ресурса виртуальной машины в шаблоне, в частности в объект `"resources": []` виртуальной машины.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Развертывание с помощью Azure PowerShell

Azure PowerShell можно использовать для развертывания расширения виртуальной машины Key Vault на имеющейся виртуальной машине или в масштабируемых наборах виртуальных машин. 

* Развертывание расширения на виртуальной машине:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Развертывание расширения в масштабируемом наборе виртуальных машин, например в составе кластера Service Fabric:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

Ознакомьтесь со следующими ограничениями и требованиями.
- Развертывание должно быть выполнено в юго-центральном регионе США.
- Ограничения, касающиеся решения Key Vault:
    - оно должно существовать во время развертывания; 
    - должно располагаться в том же регионе и группе ресурсов, что и развертывание;
    - должно быть включено для развертывания и развертывания с помощью шаблона.

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Образцы развертывания расширения виртуальной машины Key Vault с помощью Azure CLI будут доступны в ближайшее время. 

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертываний расширения можно получить на портале Azure, а также с помощью Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure PowerShell.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
