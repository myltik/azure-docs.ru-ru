---
title: "Пример сценария Azure CLI. Развертывание стека LAMP в масштабируемом наборе виртуальных машин с балансировкой нагрузки | Документация Майкрософт"
description: "Использование расширения пользовательских сценариев для развертывания стека LAMP в масштабируемом наборе виртуальных машин с балансировкой нагрузки в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 68cb08a8b40220639f880c83a44014b81598d45a
ms.lasthandoff: 04/06/2017

---

# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>Развертывание стека LAMP в масштабируемом наборе виртуальных машин с балансировкой нагрузки

В этом примере создается масштабируемый набор виртуальных машин и применяется расширение, которое запускает пользовательский сценарий для развертывания стека LAMP на каждой виртуальной машины в масштабируемом наборе.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Создание масштабируемого набора виртуальных машин со стеком LAMP")]

## <a name="connect"></a>Подключение

Используйте этот код, чтобы узнать, как подключаться к своим виртуальным машинам и масштабируемому набору.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Доступ к масштабируемому набору виртуальных машин")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор, виртуальные машины и все связанные с ней ресурсы.

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной машины, группы доступности, балансировщика нагрузки и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#create) | Создает масштабируемый набор виртуальных машин. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) | Добавляет конечную точку с балансировкой нагрузки. |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension#set) | Создает расширение, которое запускает пользовательский сценарий при развертывании виртуальной машины. |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss#update-instances) | Запускает пользовательский сценарий на экземплярах виртуальной машины, которые были развернуты до применения расширения к масштабируемому набору. |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#scale) | Увеличивает масштабируемый набор, добавляя экземпляры виртуальной машины. После развертывания на этих экземплярах выполняется пользовательский сценарий. |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#list) | Выводит IP-адреса виртуальных машин, созданных в примере. |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb#show) | Выводит интерфейсный и внутренний порты, используемые подсистемой балансировки нагрузки. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

