---
title: "Сохранение файлов с помощью PowerShell в Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Пошаговое руководство по сохранению файлов в Azure Cloud Shell."
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: d0bc16bc951fce17235d8070012de44ebab89888
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Как PowerShell работает в Azure Cloud Shell (предварительная версия)
PowerShell в Cloud Shell (предварительная версия) позволяет сохранять файлы с помощью приведенного ниже метода. 
* Подключение выбранного файлового ресурса Azure как `clouddrive` в каталоге `$Home`. Это позволяет взаимодействовать с ним напрямую.

## <a name="list-cloud-drive-azure-file-shares"></a>Вывод списка файловых ресурсов Azure на облачном диске
Команда `Get-CloudDrive` извлекает сведения о файловых ресурсах Azure, которые сейчас подключены к облачному диску в Cloud Shell. <br>
![Выполнение Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Отключение облачного диска
Файловый ресурс Azure, подключенный к Cloud Shell, можно отключить в любое время. В случае удаления файлового ресурса Azure вам будет предложено создать и подключить файловый ресурс Azure в следующем сеансе.

Команда `Dismount-CloudDrive` отключает файловый ресурс Azure в текущей учетной записи хранения. Отключение облачного диска приводит к завершению текущего сеанса. Пользователю будет предложено создать и подключить файловый ресурс Azure во время следующего сеанса.
![Выполнение Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по PowerShell](quickstart-powershell.md) <br>
[Введение в хранилище Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Использование тегов для организации ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>