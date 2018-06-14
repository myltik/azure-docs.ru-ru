---
title: Экспорт групп ресурсов Azure, которые содержат расширения виртуальной машины | Документация Майкрософт
description: Экспорт шаблонов Resource Manager, которые включают расширения виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: danis
ms.openlocfilehash: 3c54b77f52dfc7acf10dc26d4c00e9c14a296774
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944806"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Экспорт групп ресурсов, которые содержат расширения виртуальной машины

Вы можете экспортировать группы ресурсов Azure в новый шаблон Resource Manager, а затем развернуть этот шаблон. Процесс экспорта оценивает существующие ресурсы и создает шаблон Resource Manager, при развертывании которого создается аналогичная группа ресурсов. Если вы применяете операцию экспорта группы ресурсов к группе ресурсов, содержащей расширения виртуальной машины, важно учитывать несколько дополнительных аспектов, включая совместимость расширений и защищенные параметры.

В этой статье подробно описано, как процесс экспорта группы ресурсов обрабатывает расширения виртуальной машины, какие расширения он поддерживает и что происходит с защищенными данными.

## <a name="supported-virtual-machine-extensions"></a>Поддерживаемые расширения виртуальной машины

Доступно много разных расширений виртуальной машины. Не все из них могут быть экспортированы в шаблон Resource Manager с помощью функции "Сценарий автоматизации". Если расширение виртуальной машины не поддерживается, его следует включить в экспортированный шаблон вручную.

Скрипт службы автоматизации поддерживает следующие расширения.

| Добавочный номер ||||
|---|---|---|---|
| Резервное копирование Acronis | Агент Datadog для Windows | Исправления для ОС Linux | Моментальные снимки виртуальной машины Linux
| Резервное копирование Acronis для Linux | Расширение Docker | Агент Puppet |
| Сведения о BG | Расширение DSC | Site 24x7 для Apm Insight |
| Агент BMC CTM для Linux | Dynatrace для Linux | Site 24x7 для сервера Linux |
| Агент BMC CTM для Windows | Dynatrace для Windows | Site 24x7 для сервера Windows Server |
| Клиент Chef | Защитник приложений HPE Security | Trend Micro DSA |
| Custom Script | Защита от вредоносных программ IaaS | Trend Micro DSA для Linux |
| Расширение пользовательских сценариев | Диагностика IaaS | VM Access для Linux |
| Custom Script для Linux | Клиент Chef для Linux | VM Access для Linux |
| Агент Datadog для Linux | Диагностика Linux | Моментальный снимок виртуальной машины |

## <a name="export-the-resource-group"></a>Экспорт группы ресурсов

Чтобы экспортировать группу ресурсов в шаблон для повторного использования, сделайте следующее.

1. Выполните вход на портал Azure.
2. Щелкните "Группы ресурсов" в главном меню.
3. Выберите в списке целевую группу ресурсов.
4. В колонке "Группа ресурсов" щелкните "Сценарий автоматизации".

![Экспорт шаблона](./media/export-templates/template-export.png)

Сценарий автоматизации Azure Resource Manager создаст шаблон Resource Manager, файл параметров и несколько примеров сценариев развертывания, например для PowerShell и Azure CLI. На этом этапе экспортированный шаблон можно загрузить, нажав соответствующую кнопку, добавить в библиотеку шаблонов или развернуть с помощью кнопки развертывания.

## <a name="configure-protected-settings"></a>Настройка защищенных конфигураций

Многие расширения виртуальной машины Azure поддерживают использование защищенных конфигураций, при котором шифруются конфиденциальные данные, например учетные данные и строки конфигурации. Сценарий автоматизации не экспортирует защищенные конфигурации. При необходимости защищенные конфигурации можно заново добавить в экспортированный шаблон.

### <a name="step-1---remove-template-parameter"></a>Шаг 1. Удаление параметра шаблона

Когда экспортируется группа ресурсов, создается один параметр шаблона, содержащий значения для экспортируемых защищенных конфигураций. Этот параметр можно удалить. Для этого просмотрите список параметров и удалите параметр, который похож на представленный здесь пример JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Шаг 2. Получение свойств защищенных конфигураций

Поскольку у каждой защищенной конфигурации есть набор обязательных свойств, необходимо собрать список этих свойств. Все параметры защищенных конфигураций можно найти в [схеме Azure Resource Manager на GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Эта схема включает только наборы параметров для расширений, перечисленных в обзорном разделе этой статьи. 

В репозитории схемы найдите нужное расширение, например `IaaSDiagnostics`. Найдя объект `protectedSettings`, запишите каждый параметр. В примере с расширением `IaasDiagnostic` обязательными являются параметры `storageAccountName`, `storageAccountKey` и `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Шаг 3. Повторное создание защищенной конфигурации

В экспортированном шаблоне найдите `protectedSettings` и замените экспортированный объект защищенной конфигурации новым, который содержит обязательные параметры расширения и значения для каждого из них.

Для расширения `IaasDiagnostic` новая защищенная конфигурация представлена в следующем примере:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Окончательный вид ресурса для расширения будет примерно таким, как в этом примере JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Если вы используете параметры шаблона, чтобы определить значения свойств, их следует создать. Создавая параметры шаблона для значений защищенной конфигурации, обязательно используйте тип параметра `SecureString`, чтобы защитить конфиденциальные значения. Дополнительную информацию об использовании параметров см. в статье [Создание шаблонов Azure Resource Manager](../../resource-group-authoring-templates.md).

Для расширения `IaasDiagnostic` будут создаваться следующие параметры в разделе параметров шаблона Resource Manager.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

Теперь шаблон можно развернуть с помощью любого удобного метода развертывания.
