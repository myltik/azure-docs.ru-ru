---
title: Расширение виртуальной машины OMS Azure для Windows | Документация Майкрософт
description: Разверните агент OMS на виртуальной машине Windows, используя расширение виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danis
ms.openlocfilehash: c365c43eb5abb975bf77e28ad061ff091f5ec627
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="oms-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины OMS для Windows

Operations Management Suite (OMS) предоставляет возможности мониторинга, оповещений и внесения исправлений в соответствии с оповещениями для облачных и локальных ресурсов. Расширение виртуальной машины агента OMS для Windows публикуется и поддерживается корпорацией Майкрософт. Это расширение устанавливает агент OMS на виртуальных машинах Azure и регистрирует виртуальные машины в существующей рабочей области OMS. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины OMS для Windows.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="operating-system"></a>Операционная система

Расширение агента OMS для Windows может выполняться для выпусков Windows Server 2008 R2, 2012, 2012 R2 и 2016.

### <a name="azure-security-center"></a>Центр безопасности Azure

Центр безопасности Azure автоматически подготавливает агент OMS и подключает его к рабочей области Log Analytics по умолчанию в подписке Azure. Если вы используете центр безопасности Azure, не выполняйте инструкции в этом документе. Это приведет к перезаписи настроенной рабочей области и разрыву подключения с центром безопасности Azure.

### <a name="internet-connectivity"></a>Подключение к Интернету
Для расширения агента OMS для Windows требуется, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-schema"></a>Схема расширения

В следующем объекте JSON показана схема для расширения агента OMS. Расширение требует идентификатор и ключ из целевой рабочей области OMS, которые находятся на портале OMS. Так как ключ рабочей области должен рассматриваться в качестве конфиденциальных данных, его следует хранить в защищенной конфигурации параметров. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине. Обратите внимание, что в **workspaceId** и **workspaceKey** учитывается регистр знаков.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Значения свойств

| ИМЯ | Значение и пример |
| ---- | ---- |
| версия_API | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| Тип | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceID (пример) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (пример) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения агента OMS во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе. Пример шаблона, включающего в себя расширение виртуальной машины агента OMS, можно найти в [коллекции быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

JSON для расширения виртуальной машины можно вложить в ресурс виртуальной машины или поместить в корень или на верхний уровень JSON-файла шаблона Resource Manager. Размещение JSON влияет на значения имени и типа ресурса. Дополнительные сведения см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

В следующем примере предполагается, что расширение OMS вложено в ресурс виртуальной машины. При вложении ресурса расширения JSON помещается в объект `"resources": []` виртуальной машины.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

При размещении JSON расширения в корне шаблона имя ресурса содержит ссылку на родительскую виртуальную машину, а тип отражает вложенную конфигурацию. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell

Команду `Set-AzureRmVMExtension` можно использовать для развертывания расширения виртуальной машины агента OMS на существующей виртуальной машине. Перед выполнением команды необходимо сохранить открытые и закрытые конфигурации в хэш-таблице PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также с помощью модуля Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в модуле Azure PowerShell.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
