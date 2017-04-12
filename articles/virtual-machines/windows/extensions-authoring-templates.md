---
title: "Создание шаблонов с помощью расширений виртуальной машины Windows | Документация Майкрософт"
description: "Узнайте о разработке шаблонов Azure Resource Manager с расширениями для виртуальных машин Windows."
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 418dd1f7-ded8-45ab-9a5a-a59d245e2555
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 338668df33783d8ef62c6710f4d96ce805296fe5
ms.lasthandoff: 03/31/2017


---
# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Authoring Azure Resource Manager templates with Windows VM extensions
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Выполните следующий командлет в Azure PowerShell:

      Get-AzureVMAvailableExtension


Данный командлет возвращает имя издателя, имя расширения и версию в следующем виде.

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Эти три свойства сопоставляются со значениями publisher, type и typeHandlerVersion в приведенном выше фрагменте шаблона.

> [!NOTE]
> Рекомендуется всегда использовать самую последнюю версию расширения для обеспечения максимальной функциональности.
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Определение схемы для параметров конфигурации расширения
Следующий шаг при создании шаблона расширения заключается в определении формата для предоставления параметров конфигурации. Каждое расширение поддерживает собственный набор параметров.

Чтобы ознакомиться с примером конфигурации для расширений Windows, см. [примеры расширений Windows](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ознакомьтесь со следующими разделами, чтобы получить полноценный шаблон с расширениями виртуальной машины.

[Расширение Custom Script на виртуальной машине Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

После разработки шаблон можно развернуть с помощью Azure PowerShell.


