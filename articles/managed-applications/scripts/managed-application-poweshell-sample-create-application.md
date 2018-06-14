---
title: Пример скрипта Azure PowerShell. Развертывание управляемого приложения | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Развертывание управляемого приложения
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 2429d561beffed5bc171b9dbc2c2c9c88eba3313
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2017
ms.locfileid: "23637044"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Развертывание управляемого приложения для каталога служб с помощью Azure PowerShell

С помощью этого скрипта определение управляемого приложения развертывается из каталога служб.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используется следующая команда для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplication) | Позволяет создать управляемое приложение. Укажите идентификатор определения и параметры для шаблона. |


## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
