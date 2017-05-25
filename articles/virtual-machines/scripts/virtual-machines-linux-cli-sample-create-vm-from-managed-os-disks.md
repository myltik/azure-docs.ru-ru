---
title: "Пример скрипта Azure CLI. Создание виртуальной машины путем подключения управляемого диска в качестве диска ОС | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание виртуальной машины путем подключения управляемого диска в качестве диска ОС"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 674b8bc1db7bccdb99e89510d18a4ae10bf1329b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017

---

# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Создание виртуальной машины с помощью существующего управляемого диска ОС с использованием CLI

Этот скрипт создает виртуальную машину путем подключения существующего управляемого диска как диска ОС. Используйте этот скрипт в таких сценариях:
* создание виртуальной машины из существующего управляемого диска ОС, скопированного из управляемого диска в другой подписке;
* создание виртуальной машины из существующего управляемого диска, который был создан из специального файла VHD; 
* создание виртуальной машины из существующего управляемого диска ОС, который был создан из моментального снимка. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Создание виртуальной машины из управляемого диска")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды для получения свойств управляемого диска, его подключения к новой виртуальной машине и создания виртуальной машины. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Команда | Примечания |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | Получает свойства управляемого диска, используя имя диска и имя группы ресурсов. Свойство идентификатора используется для подключения управляемого диска к новой виртуальной машине. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Создает виртуальную машину с помощью управляемого диска ОС |
## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

