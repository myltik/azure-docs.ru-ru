---
title: "Разные способы создания виртуальной машины Windows в Azure | Документация Майкрософт"
description: "Содержит перечисление различных способов создания виртуальной машины Windows с помощью диспетчера ресурсов."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 809ba8f4-b54e-43c5-bbe3-8e710c49971f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5e51c49aac01a22d86c7c1a12b2f2ca7ddc056bc
ms.lasthandoff: 03/31/2017


---
# <a name="different-ways-to-create-a-windows-virtual-machine"></a>Различные способы создания виртуальной машины Windows

Поскольку виртуальные машины подходят для различных пользователей и целей, в Azure их можно создавать несколькими способами. Это означает, что вам потребуется выбрать виртуальную машину и способ ее создания. В настоящей статье рассматриваются эти варианты и приводятся ссылки на инструкции.

## <a name="azure-portal"></a>Портал Azure
Портал Azure — это простой способ опробовать виртуальную машину, особенно если вы только начинаете работать с Azure. 

[Создание виртуальной машины под управлением Windows на портале](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="template"></a>Шаблон
Для виртуальных машин требуется сочетание ресурсов (таких как группы доступности и учетные записи хранения). Вместо развертывания и управления каждым ресурсом по отдельности можно создать шаблон Azure Resource Manager, который развертывает и подготавливает все ресурсы в ходе одной скоординированной операции.

* [Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell
Если вы предпочитаете работать в командной строке, можно использовать Azure PowerShell.

* [Создание виртуальной машины Windows с помощью PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="visual-studio"></a>Visual Studio
Используйте Visual Studio для создания и развертывания виртуальных машин, а также для управления ими, с помощью инструментов Azure для Visual Studio и пакета SDK Azure.

[Инструменты Azure для Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)


