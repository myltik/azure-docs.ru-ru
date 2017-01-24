---
title: "Расширение виртуальной машины OMS Azure для Windows | Документация Майкрософт"
description: "Разверните агент OMS на виртуальной машине Windows, используя расширение виртуальной машины."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: d65505182267bec053fca0ab5045cbcae9381ac7
ms.openlocfilehash: 245e49d36142ef8e927a5e494113e4dd7809fc16


---
# <a name="oms-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины OMS для Windows

## <a name="overview"></a>Обзор

Operations Management Suite (OMS) предоставляет возможности мониторинга, оповещений и внесения исправлений в соответствии с оповещениями для облачных и локальных ресурсов. Расширение виртуальной машины агента OMS для Windows публикуется и поддерживается корпорацией Майкрософт. Это расширение устанавливает агент OMS на виртуальных машинах Azure и регистрирует виртуальные машины в существующей рабочей области OMS. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины OMS для Windows.

Общие сведения о расширениях виртуальной машины Azure см. в статье [Обзор расширений и компонентов виртуальной машины](./virtual-machines-windows-extensions-features.md).

Дополнительные сведения об Operations Management Suite см. на странице с [обзором Operations Management Suite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite).

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Расширение агента OMS для Windows может выполняться для выпусков Windows Server 2012, 2012 R2 и 2016.

### <a name="connectivity"></a>Соединение

Для расширения агента OMS для Windows требуется, чтобы целевая виртуальная машина была подключена к Интернету. 

## <a name="extension-configuration"></a>Конфигурация расширения

Для расширения виртуальной машины агента OMS для Windows требуется идентификатор и ключ из целевой рабочей области OMS. Так как ключ рабочей области должен рассматриваться в качестве конфиденциальных данных, он хранится в защищенной конфигурации. Данные защищенной конфигурации расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине. Открытые и закрытые конфигурации задаются во время развертывания, что описывается в следующих разделах этого документа.

### <a name="public-configuration"></a>Открытая конфигурация

Схема для открытой конфигурации:

- workspaceId: (требуется, строка) идентификатор рабочей области OMS, в которую внедряется виртуальная машина.

```json
{
  "workspaceId": "myWorkspaceId"
}
```

### <a name="private-configuration"></a>Закрытая конфигурация

Схема для открытой конфигурации:

- workspaceKey: (требуется, строка) общий первичный или вторичный ключ рабочей области.

```json
{
  "workspaceKey": "myWorkSpaceKey"
}
```

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих настройки после развертывания, например внедрения в OMS. Пример шаблона Resource Manager, включающего в себя расширение виртуальной машины агента OMS, можно найти в [коллекции быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

Этот пример можно развернуть из документа, используя эту кнопку:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-oms-extension-windows-vm%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

JSON-файл, используемый для развертывания расширения виртуальной машины агента OMS, выглядит следующим образом:

```json
{
    "type": "extensions",
    "name": "Microsoft.EnterpriseCloud.Monitoring",
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
            "workspaceId": "myWorkSpaceKey"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceId"
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
    -Location WestUS ` 
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

`
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
`

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/en-us/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).



<!--HONumber=Dec16_HO1-->


