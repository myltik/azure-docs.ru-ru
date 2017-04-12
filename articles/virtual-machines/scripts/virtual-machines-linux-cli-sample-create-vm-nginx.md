---
title: "Пример скрипта Azure CLI. Создание виртуальной машины Linux с помощью NGINX | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание виртуальной машины Linux с помощью NGINX"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: fc97b01c04be81b89cadf1f16c87dcb2069d7b64
ms.lasthandoff: 04/06/2017

---

# <a name="create-a-vm-with-nginx"></a>Создание виртуальной машины с помощью NGINX

Этот сценарий создает виртуальную машину в Azure, а затем использует расширение пользовательских сценариев для виртуальных машин Azure, чтобы установить NGINX. После выполнения сценария можно получить доступ к веб-сайту с демоверсиями по общедоступному IP-адресу виртуальной машины.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Быстрое создание виртуальной машины")]

## <a name="custom-script-extension"></a>Расширение пользовательских сценариев

Расширение пользовательских скриптов копирует этот скрипт на виртуальную машину. Затем скрипт выполняется для установки и настройки веб-сервера NGINX. 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

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
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Создает виртуальную машину. Эта команда также указывает образ виртуальной машины, который будет использоваться, и учетные данные администратора.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Создает правило группы безопасности сети, разрешающее входящий трафик. В этом примере открывается порт 80 для трафика HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Добавляет расширение виртуальной машины в виртуальную машину и выполняет его. В этом примере для установки NGINX используется расширение пользовательских скриптов.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

