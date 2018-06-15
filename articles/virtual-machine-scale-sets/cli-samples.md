---
title: Примеры для Azure CLI 2.0 | Документация Майкрософт
description: Примеры для Azure CLI 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 69fc81d4d0569ee7a66fbda5ab500ef2ee15c694
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246395"
---
# <a name="azure-cli-20-samples-for-virtual-machine-scale-sets"></a>Примеры для Azure CLI 2.0 для масштабируемых наборов виртуальных машин

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI 2.0.

| | |
|---|---|
|**Создание масштабируемого набора и управление им**||
| [Создание масштабируемого набора виртуальных машин](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Создает масштабируемый набор виртуальных машин с минимальной конфигурацией. |
| [Создание масштабируемого набора из пользовательского образа виртуальной машины](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Создает масштабируемый набор виртуальных машин, использующий пользовательский образ виртуальной машины. |
| [Установка приложений в масштабируемом наборе](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Чтобы установить простое веб-приложение в масштабируемом наборе, используйте расширение пользовательских скриптов Azure. |
|**Управление хранилищем**||
| [Создание и подключение дисков к масштабируемому набору](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Создает масштабируемый набор виртуальных машин с подключенными дисками данных. |
|**Управление масштабированием и избыточностью**||
| [Включение автомасштабирования на основе узла](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Создает масштабируемых набор виртуальных машин, настроенный на автоматическое масштабирование в зависимости от загрузки ЦП. |
| [Создание однозонного масштабируемого набора](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Создает масштабируемый набор виртуальных машин, который использует одну зону доступности. |
| [Создание масштабируемого набора, избыточного в пределах зоны](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Создает масштабируемый набор виртуальных машин, который использует несколько зон доступности. |
| | |