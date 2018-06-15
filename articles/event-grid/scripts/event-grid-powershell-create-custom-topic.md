---
title: Пример скрипта Azure PowerShell. Создание пользовательского раздела | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Создание пользовательского раздела
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2948f1eabe93d092d905965429ff73eaa6c443cd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31426069"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Создание пользовательского раздела службы "Сетка событий" с помощью PowerShell

С помощью этого скрипта создается пользовательский раздел службы "Сетка событий".

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать пользовательский раздел, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmEventGridTopic](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridtopic) | Создание пользовательского раздела службы "Сетка событий". |

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
