---
title: "Повторное развертывание виртуальных машин Linux в Azure | Документация Майкрософт"
description: "Повторное развертывание виртуальных машин Linux в Azure для устранения проблем подключения по протоколу SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 3295120664e409440641818b13dd1abab6f2f72f
ms.openlocfilehash: 3864148ecd09b5bc4199185bc0e5a781703f3fdb


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Повторное развертывание виртуальной машины Linux на новом узле Azure
Если у вас возникли сложности при устранении неполадок подключения SSH или получения доступа к приложению на виртуальной машине Linux в Azure, можно попробовать повторно развернуть виртуальную машину. При повторном развертывании виртуальная машина перемещается на новый узел в рамках инфраструктуры Azure. Там она снова включается с сохранением всех параметров конфигурации и связанных ресурсов. В этой статье показано, как повторно развернуть виртуальную машину с помощью интерфейса командной строки Azure или портала Azure.

> [!NOTE]
> После развертывания временный диск будет удален, а связанные с виртуальным сетевым интерфейсом динамические IP-адреса будут обновлены. 


## <a name="using-azure-cli"></a>Использование Azure CLI
На виртуальной машине должен быть [установлен Azure CLI последней версии](../xplat-cli-install.md). Его нужно переключить в режим Resource Manager (`azure config mode arm`).

В следующем примере повторно развертывается виртуальная машина `myVM` в группе ресурсов `myResourceGroup`.

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Дальнейшие действия
При проблемах с подключением к виртуальной машине ознакомьтесь со статьями [Устранение неполадок SSH-подключения к виртуальной машине Azure](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и [Подробное описание устранения неполадок SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). При проблемах с доступом к приложению, выполняющемуся в виртуальной машине, ознакомьтесь со статьей [Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Dec16_HO3-->


