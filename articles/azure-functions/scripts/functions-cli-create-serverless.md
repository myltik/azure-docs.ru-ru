---
title: "Пример сценария Azure CLI. Создание приложения-функции для выполнения без сервера | Документация Майкрософт"
description: "Пример сценария Azure CLI для создания приложения-функции для выполнения без сервера."
services: functions
documentationcenter: functions
author: syntaxc4
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/04/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 9436cafa775f2ad658c0c9901f7f8eae769c7052
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-app-for-serverless-execution"></a>Создание приложения-функции для выполнения без сервера

Этот пример сценария создает приложение-функцию Azure, которое является контейнером для ваших функций. Приложение-функция создается с помощью [плана потребления](../functions-scale.md#consumption-plan), который идеально подходит для рабочих нагрузок, выполняемых на основе событий без использования сервера.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот скрипт создает приложение-функцию Azure с помощью [плана потребления](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Create an Azure Function on a consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Создает учетную запись хранения Azure. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Создает приложение-функцию. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).
