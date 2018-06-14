---
title: Повторное развертывание виртуальных машин Linux в Azure | Документация Майкрософт
description: Повторное развертывание виртуальных машин Linux в Azure для устранения проблем подключения по протоколу SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: 484e23f7f5800faf4a1d83c2386e0c766c3a1f2d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29849313"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Повторное развертывание виртуальной машины Linux на новом узле Azure
Если у вас возникли сложности при устранении неполадок подключения SSH или получения доступа к приложению на виртуальной машине Linux в Azure, можно попробовать повторно развернуть виртуальную машину. При повторном развертывании виртуальная машина перемещается на новый узел в рамках инфраструктуры Azure, где она повторно включается. При этом сохраняются все параметры конфигурации и связанные ресурсы. В этой статье показано, как повторно развернуть виртуальную машину с помощью интерфейса командной строки Azure или портала Azure.

> [!NOTE]
> После развертывания временный диск будет удален, а связанные с виртуальным сетевым интерфейсом динамические IP-адреса будут обновлены. 

Можно повторно развернуть виртуальную машину с помощью одного из приведенных способов. Необходимо выбрать один из вариантов для повторного развертывания виртуальной машины:

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [портал Azure](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Использование Azure CLI 2.0
Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему со своей учетной записью Azure, выполнив команду [az login](/cli/azure/reference-index#az_login).

Повторно разверните виртуальную машину, выполнив команду [az vm redeploy](/cli/azure/vm#az_vm_redeploy). В следующем примере повторно развертывается виртуальная машина с именем *myVM*, входящая в группу ресурсов с именем *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Использование Azure CLI 1.0
Установите последнюю версию [Azure CLI 1.0](../../cli-install-nodejs.md) и войдите в систему со своей учетной записью Azure. Убедитесь, что режим Resource Manager включен (`azure config mode arm`).

В следующем примере повторно развертывается виртуальная машина с именем *myVM*, входящая в группу ресурсов с именем *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Дополнительная информация
При проблемах с подключением к виртуальной машине ознакомьтесь со статьями [Устранение неполадок SSH-подключения к виртуальной машине Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и [Подробное описание устранения неполадок SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). При проблемах с доступом к приложению, выполняющемуся в виртуальной машине, ознакомьтесь со статьей [Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

