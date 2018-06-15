---
title: Пример скрипта Azure CLI. Установка IIS | Документация Майкрософт
description: Пример скрипта Azure CLI. Установка IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
ms.openlocfilehash: 691c04d002b345b532bc5d5a37ce7d8f1dcab124
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654272"
---
# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Быстрое создание виртуальной машины с помощью Azure CLI

Этот сценарий создает виртуальную машину Azure под управлением Windows Server 2016 и использует расширение пользовательских сценариев для виртуальных машин Azure, чтобы установить IIS. После выполнения сценария можно получить доступ к веб-сайту IIS по умолчанию по общедоступному IP-адресу виртуальной машины.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Создает правило группы безопасности сети, разрешающее входящий трафик. В этом примере открывается порт 80 для трафика HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Добавляет расширение виртуальной машины в виртуальную машину и выполняет его. В этом примере для установки IIS используется расширение пользовательских скриптов.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Windows](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
