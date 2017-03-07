---
title: "Пример скрипта Azure CLI. Быстрое создание виртуальной машины Windows Server 2016 | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Быстрое создание виртуальной машины Windows Server 2016"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rickstercdn
translationtype: Human Translation
ms.sourcegitcommit: 82d40c30c92f5da090e7ec4e2f25ead3908cc603
ms.openlocfilehash: 0ec8c8437e283f49cef27f241fe3d2059fc33898
ms.lasthandoff: 03/02/2017

---

# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Быстрое создание виртуальной машины с помощью Azure CLI

Приведенный здесь пример скрипта позволяет создать виртуальную машину Azure с операционной системой Windows Server 2016. После успешного выполнения скрипта можно получить доступ к виртуальной машине по протоколу RDP.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`. Кроме того, в начале скрипта следует изменить переменную $AdminPassword таким образом, чтобы она была уникальной и соответствовала требованиям к уровню сложности пароля.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI для Windows см. в статье [Использование Azure CLI в Windows](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-quick/create-windows-vm-quick.sh "Быстрое создание виртуальной машины")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Windows](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

