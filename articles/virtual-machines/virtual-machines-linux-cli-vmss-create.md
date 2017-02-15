---
title: "Что такое наборы масштабирования виртуальных машин? | Документация Майкрософт"
description: "Узнайте о наборах масштабирования виртуальных машин."
keywords: "Виртуальная машина Linux, наборы масштабирования виртуальных машин"
services: virtual-machines-linux
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba1aedb6-49cb-4546-8b8b-da97aba8e42d
ms.service: virtual-machine-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9ee2d1d4e50a5bf4c146c48024aae5f10cea32ef


---
# <a name="what-are-virtual-machine-scale-sets"></a>Что такое наборы масштабирования виртуальных машин?
Масштабируемые наборы виртуальных машин позволяют управлять несколькими виртуальными машинами как единым набором. Если не вдаваться в подробности, масштабируемые наборы имеют следующие преимущества и недостатки:

Преимущества:

1. обеспечение высокой доступности; Каждый набор масштабирования помещает свои виртуальные машины в группу доступности с 5 доменами сбоя (FD) и 5 доменами обновления (UD) для обеспечения доступности (дополнительные сведения о доменах сбоя и обновления см. в статье [Доступность виртуальных машин](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). 
2. Простая интеграция с балансировщиком нагрузки Azure и шлюзом приложений.
3. Простая интеграция с функцией автоматического масштабирования Azure.
4. Упрощение развертывания, удаления виртуальных машин и управления ими.
5. Поддержка распространенных выпусков Windows и Linux, а также пользовательских образов.

Недостатки:

1. Невозможность подключения дисков данных к экземплярам виртуальных машин в масштабируемом наборе. Вместо этого следует использовать хранилище BLOB-объектов, службу файлов Azure, таблицы Azure или другое решение для хранения.

## <a name="quick-create-using-azure-cli"></a>Быстрое создание с помощью Azure CLI
[!INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
Общие сведения см. на [главной целевой странице по наборам масштабирования](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Документацию см. на [основной странице документации по масштабируемым наборам](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Чтобы найти примеры шаблонов Resource Manager с использованием наборов масштабирования, выполните поиск строки "vmss" в [репозитории GitHub с шаблонами быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates).




<!--HONumber=Nov16_HO3-->


