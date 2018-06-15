---
title: Использование Управляемых дисков с масштабируемыми наборами виртуальных машин Azure | Документация Майкрософт
description: Узнайте, как и для чего используются управляемые диски с масштабируемыми наборами виртуальных машин.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 82fa518e6c0498a13f950ce33c51be8581918f9b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201144"
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Масштабируемые наборы виртуальных машин Azure и управляемые диски

[Масштабируемые наборы виртуальных машин](/azure/virtual-machine-scale-sets/) Azure теперь поддерживают виртуальные машины с управляемыми дисками. Использование управляемых дисков с масштабируемыми наборами имеет ряд преимуществ, в частности следующие:

* вам больше не требуется предварительно создавать учетные записи хранения и управлять ими, чтобы хранить диски ОС для виртуальных машин из масштабируемых наборов;

* вы можете подключать управляемые диски данных к масштабируемому набору;

* благодаря управляемым дискам емкость масштабируемого набора может достигать 1000 виртуальных машин (на базе образа платформы) или 300 виртуальных машин (на базе пользовательского образа).

## <a name="get-started"></a>Начало работы

Самый простой способ начать работу с масштабируемыми наборами, использующими управляемые диски, — это развернуть такой набор с портала Azure. Дополнительные сведения см. в [этой статье](./virtual-machine-scale-sets-portal-create.md). Другой простой способ — развернуть масштабируемый набор с помощью [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2). В следующем примере показано создание масштабируемого набора на базе Ubuntu с 10 виртуальными машинами, каждая из которых имеет диск данных емкостью 50 ГБ и 100 ГБ:

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Кроме того, вы можете просмотреть [репозиторий GitHub с шаблонами быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates), в котором находятся папки, содержащие `vmss`. Ознакомьтесь с примерами готовых шаблонов, которые используются для развертывания масштабируемых наборов. Список шаблонов, в которых уже используются управляемые диски, см. [здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управляемых дисках в целом см. в [этой статье](../virtual-machines/windows/managed-disks-overview.md).

Чтобы узнать, как преобразовать шаблон Resource Manager для подготовки масштабируемых наборов с управляемыми дисками, см. [эту статью](./virtual-machine-scale-sets-convert-template-to-md.md). Такие же изменения в шаблонах Resource Manager применяются к Azure REST API.

Дополнительные сведения об использовании управляемых дисков данных с масштабируемыми наборами см. в [этой статье](./virtual-machine-scale-sets-attached-disks.md).

Чтобы начать работу с большими масштабируемыми наборами, ознакомьтесь с [этой статьей](./virtual-machine-scale-sets-placement-groups.md).


