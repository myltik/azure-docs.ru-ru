---
title: Использование настройки требуемого состояния с масштабируемыми наборами виртуальных машин | Документация Майкрософт
description: Использование наборов масштабирования виртуальных машин с помощью расширения Azure DSC
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a68a5f31952d636c054b66dc0bb6ec0579cd7192
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30909009"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Использование наборов масштабирования виртуальных машин с помощью расширения Azure DSC
[Масштабируемые наборы виртуальных машин](virtual-machine-scale-sets-overview.md) могут использоваться с обработчиком расширения [Настройка требуемого состояния (DSC) Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Масштабируемые наборы виртуальных машин позволяют развертывать большое количество виртуальных машин и управлять ими, а также гибко масштабировать ресурсы согласно нагрузке. Расширение DSC используется для настройки виртуальных машин при их подключении для работы с программным обеспечением в рабочей среде.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Различия при развертывании на виртуальных машинах и в масштабируемых наборах виртуальных машин
Базовая структура шаблона для масштабируемого набора виртуальных машин немного отличается от отдельной виртуальной машины. В частности, на одиночной виртуальной машине расширения развертываются в узле virtualMachines. Существует запись с типом extensions, где DSC добавляется в шаблон:

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Узел масштабируемого набора виртуальных машин содержит раздел properties с атрибутами VirtualMachineProfile и extensionProfile. DSC добавляется в раздел extensions:

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Поведение для масштабируемого набора виртуальных машин
Поведение при использовании масштабируемого набора виртуальных машин идентично поведению для отдельной виртуальной машины. При создании виртуальной машины она автоматически подготавливается с помощью расширения DSC. Если для расширения требуется новая версия WMF, то перед подключением виртуальная машина перезагружается. Когда виртуальная машина подключена, она загружает файл конфигурации DSC в формате ZIP и подготавливает его на виртуальной машине. Дополнительные сведения см. в статье [Общие сведения об обработчике расширения DSC в Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Дополнительная информация
Изучите [шаблон Azure Resource Manager для расширения DSC](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Узнайте, как [расширение DSC безопасно обрабатывает учетные данные](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Дополнительные сведения об обработчике расширений DSC см. в статье [Общие сведения об обработчике расширения для настройки требуемого состояния в Azure](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Для получения дополнительных сведений о DSC PowerShell [посетите центр документации PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

