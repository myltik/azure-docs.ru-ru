---
title: "Пример сценария Azure CLI. Создание приложения-функции для выполнения без сервера | Документация Майкрософт"
description: "Пример сценария Azure CLI для создания приложения-функции для выполнения без сервера."
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a951b11689de3abc93e9933221ecf76ce44a7a6e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-function-app-for-serverless-execution"></a>Создание приложения-функции для выполнения без сервера

Этот пример сценария создает приложение-функцию Azure, которое является контейнером для ваших функций. Приложение-функция будет создано с помощью плана потребления, который идеально подходит для рабочих нагрузок, выполняемых на основе событий без использования сервера.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

Создание примера приложения

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Создание функции Azure на основе плана потребления")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполнив пример скрипта, вы можете удалить группу ресурсов, приложение службы приложений и все связанные ресурсы с помощью следующей команды.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Создает функцию Azure. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).
