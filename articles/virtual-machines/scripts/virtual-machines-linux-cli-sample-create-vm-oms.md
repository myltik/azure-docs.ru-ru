---
title: "Пример скрипта Azure CLI. Создание виртуальной машины Linux с использованием мониторинга OMS | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание виртуальной машины Linux с использованием мониторинга OMS"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f7298fbe6080c2de1440dd275c8fcf06ffc39409
ms.lasthandoff: 03/21/2017

---

# <a name="monitor-a-vm-with-operations-management-suite"></a>Мониторинг виртуальной машины с помощью Operations Management Suite

Этот скрипт создает виртуальную машину Azure, устанавливает агент Operations Management Suite и регистрирует систему в рабочей области OMS. После выполнения скрипта виртуальная машина отобразится в консоли OMS.

При необходимости установите Azure CLI с помощью инструкции, приведенной в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), а затем выполните команду `az login`, чтобы создать подключение к Azure. Кроме того, необходимо обновить идентификатор и ключ рабочей области OMS.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.sh "Быстрое создание виртуальной машины")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Выполняет расширение виртуальной машины на виртуальной машине. В этом случае расширение агента Operations Management Suite используется для установки агента OMS и регистрации виртуальной машины в рабочей области OMS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

