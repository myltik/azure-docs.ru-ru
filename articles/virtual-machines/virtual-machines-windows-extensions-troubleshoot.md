---
title: "Устранение неполадок расширений виртуальной машины Windows | Документация Майкрософт"
description: "Узнайте об устранении неполадок в работе расширений виртуальной машины Windows в Azure."
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd50ff64b538232a9a1945efaffacef3a06cfcdb


---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Troubleshooting Azure Windows VM extension failures
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Просмотр состояния расширения
Шаблоны Azure Resource Manager можно выполнять из Azure PowerShell. После выполнения шаблона состояние расширения можно узнать в обозревателе ресурсов Azure или с помощью средств командной строки.

Пример:

Azure PowerShell.

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Пример выходных данных:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Устранение неполадок расширений
### <a name="re-running-the-extension-on-the-vm"></a>Повторное выполнение расширения на виртуальной машине
При выполнении сценариев на виртуальной машине с помощью расширения пользовательских сценариев может возникать ошибка, указывающая на то, что виртуальная машина создана успешно, но сценарий не выполнен. В этом случае рекомендуется удалить соответствующее расширение и выполнить шаблон еще раз.
Примечание. В будущем эта функция будет усовершенствована, что позволит устранить необходимость в удалении расширения.

#### <a name="remove-the-extension-from-azure-powershell"></a>Удаление расширения с помощью Azure PowerShell
    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

После удаления расширения шаблон можно выполнить повторно, чтобы запустить скрипты на виртуальной машине.




<!--HONumber=Nov16_HO3-->


