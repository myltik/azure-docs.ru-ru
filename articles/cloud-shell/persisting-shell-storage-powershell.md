---
title: Сохранение файлов с помощью PowerShell в Azure Cloud Shell (предварительная версия) | Документация Майкрософт
description: Пошаговое руководство по сохранению файлов в Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154404"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Как PowerShell работает в Azure Cloud Shell (предварительная версия)
PowerShell в Cloud Shell (предварительная версия) позволяет сохранять файлы с помощью приведенного ниже метода. 
* Подключение выбранного файлового ресурса Azure как `clouddrive` в каталоге `$Home`. Это позволяет взаимодействовать с ним напрямую.

## <a name="list-clouddrive-azure-file-shares"></a>Выведение списка общих файловых ресурсов Azure `clouddrive`
Команда `Get-CloudDrive` извлекает сведения о файловых ресурсах Azure, которые сейчас подключены к `clouddrive` в Cloud Shell. <br>
![Выполнение Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>Отключение `clouddrive`
Файловый ресурс Azure, подключенный к Cloud Shell, можно отключить в любое время. В случае удаления файлового ресурса Azure вам будет предложено создать и подключить файловый ресурс Azure в следующем сеансе.

Команда `Dismount-CloudDrive` отключает файловый ресурс Azure в текущей учетной записи хранения. Отключение `clouddrive` приводит к завершению текущего сеанса. Пользователю будет предложено создать и подключить файловый ресурс Azure во время следующего сеанса.
![Выполнение Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Дополнительная информация
[Краткое руководство по PowerShell](quickstart-powershell.md) <br>
[Введение в хранилище Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Использование тегов для организации ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>