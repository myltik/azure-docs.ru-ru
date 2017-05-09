---
title: "Пример скрипта Azure CLI. Создание виртуальной машины Linux с помощью виртуального жесткого диска | Документы Майкрософт"
description: "Пример скрипта Azure CLI. Создание виртуальной машины с помощью виртуального жесткого диска."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c654db0142df904acc5212b0f53991c54cbf2087
ms.contentlocale: ru-ru
ms.lasthandoff: 04/06/2017

---

# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Создание виртуальной машины с помощью виртуального жесткого диска

Этот пример создает виртуальную машину с помощью виртуального жесткого диска.
Он создает группу ресурсов, учетную запись хранение и контейнер, а затем создает виртуальную машину путем отправки виртуального жесткого диска в контейнер.
Он заменяет открытый ключ SSH вашим открытым ключом, чтобы предоставить вам доступ к виртуальной машине.

Потребуется загрузочный виртуальный жесткий диск.
Можно скачать VHD-диск, который мы использовали, со страницы https://azclisamples.blob.core.windows.net/vhds/sample.vhd или использовать собственный виртуальный жесткий диск. Скрипт ищет `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[основной](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Создание виртуальной машины с помощью виртуального жесткого диска")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины, группы доступности, балансировщика нагрузки и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#list) | Выводит список учетных записей хранения. |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#check-name) | Проверяет допустимость имени учетной записи хранения и что она еще не существует. |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#list) | Выводит список ключей для учетных записей хранения. |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#exists) | Проверяет, существует ли BLOB-объект. |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#create) | Создает контейнер в учетной записи хранения. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#upload) | Создает BLOB-объект в контейнере путем отправки виртуального жесткого диска. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | Используется с `--query`, чтобы проверить, используется ли имя виртуальной машины. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | Создает виртуальные машины. |
| [az vm access set-linux-user](https://docs.microsoft.com/cli/azure/vm/access#set-linux-user) | Сбрасывает ключ SSH для предоставления текущему пользователю прав доступа к виртуальной машине. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | Возвращает IP-адрес созданной виртуальной машины. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

