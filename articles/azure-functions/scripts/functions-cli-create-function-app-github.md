---
title: "Создание приложения-функции и развертывание кода функции из GitHub | Документация Майкрософт"
description: "Пример сценария Azure CLI для создания приложения-функции и развертывание кода функции из GitHub."
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: d67e85f91c80efe464fceb1105243bedfba83a0f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017

---
<a id="create-a-function-app-and-deploy-function-code-from-github" class="xliff"></a>

# Создание приложения-функции и развертывание кода функции из GitHub

Этот пример сценария создает приложение-функцию с помощью [плана потребления](../functions-scale.md#consumption-plan) со связанными ресурсами и развертывает код функции из открытого репозитория GitHub (без непрерывного развертывания). Для непрерывной доставки кода функции из GitHub см. статью [Создание службы приложений](functions-cli-create-function-app-github-continuous.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

<a id="sample-script" class="xliff"></a>

## Пример скрипта

Этот пример создает приложения-функцию Azure и развертывает код функции из GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Создание приложения-функции с развертыванием из GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

<a id="script-explanation" class="xliff"></a>

## Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Создает приложение-функцию Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Связывает приложение-функцию с репозиторием Git или Mercurial. |

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).

