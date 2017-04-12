---
title: "Повторное развертывание виртуальных машин Windows в Azure | Документация Майкрософт"
description: "Повторное развертывание виртуальных машин Windows в Azure для устранения проблем с подключением к удаленному рабочему столу."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 7ca51ce26e009d18b4519a5e7ba16df64ec984ef
ms.lasthandoff: 03/31/2017


---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Повторное развертывание виртуальной машины Windows на новом узле Azure
Если вам не удается подключиться к удаленному рабочему столу или получить доступ к приложению на виртуальной машине Windows Azure, можно попробовать повторно развернуть виртуальную машину. При повторном развертывании виртуальная машина перемещается на новый узел в рамках инфраструктуры Azure. Там она снова включается с сохранением всех параметров конфигурации и связанных ресурсов. В этой статье показано, как повторно развернуть виртуальную машину с помощью Azure PowerShell или портала Azure.

> [!NOTE]
> После развертывания временный диск будет удален, а связанные с виртуальным сетевым интерфейсом динамические IP-адреса будут обновлены. 


## <a name="using-azure-powershell"></a>Использование Azure PowerShell
Убедитесь, что на компьютере установлена последняя версия Azure PowerShell 1.x. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).

В следующем примере развертывается виртуальная машина `myVM` в группе ресурсов `myResourceGroup`.

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Дальнейшие действия
При проблемах с подключением к виртуальной машине ознакомьтесь со статьями [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) и [Подробная диагностика подключений к удаленному рабочему столу виртуальной машины Azure под управлением Windows](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). При проблемах с доступом к приложению, выполняющемуся на виртуальной машине, ознакомьтесь со статьей [Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


