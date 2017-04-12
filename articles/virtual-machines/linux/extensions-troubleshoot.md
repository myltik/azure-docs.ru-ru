---
title: "Устранение неполадок расширения виртуальной машины Linux | Документация Майкрософт"
description: "Узнайте об устранении неполадок в расширении виртуальной машины Linux в Azure."
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: top-support-issue,azure-resource-manager
ms.assetid: f05d93f3-42fc-4a09-9798-d92f7929c417
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 589890de379d0b729de1f1ba9e604e0ec0496f50
ms.lasthandoff: 04/03/2017


---
# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Устранение неполадок расширения виртуальной машины Linux
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Просмотр состояния расширения
Шаблоны Azure Resource Manager можно выполнять из Azure CLI. После выполнения шаблона состояние расширения можно узнать в обозревателе ресурсов Azure или с помощью средств командной строки.

Пример:

Azure CLI:

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>Устранение сбоев в расширениях
### <a name="re-running-the-extension-on-the-vm"></a>Повторное выполнение расширения на виртуальной машине
При выполнении сценариев на виртуальной машине с помощью расширения пользовательских сценариев может возникать ошибка, указывающая на то, что виртуальная машина создана успешно, но сценарий не выполнен. В этом случае рекомендуется удалить соответствующее расширение и выполнить шаблон еще раз.
Примечание. В будущем эта функция будет усовершенствована, что позволит устранить необходимость в удалении расширения.

#### <a name="remove-the-extension-from-azure-cli"></a>Удаление расширения с помощью Azure CLI
      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

В данном коде publisher-name соответствует типу расширения из выходных данных azure vm get-instance-view, а имя представляет собой имя ресурса расширения из шаблона.

После удаления расширения шаблон можно выполнить повторно, чтобы запустить скрипты на виртуальной машине.


