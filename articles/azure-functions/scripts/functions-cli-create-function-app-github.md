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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f87cf7d300b4c2b89ad692aadcda958e9747c7f9
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Создание приложения-функции и развертывание кода функции из GitHub

Этот пример сценария создает приложение-функцию с помощью [плана потребления](../functions-scale.md#consumption-plan) со связанными ресурсами и развертывает код функции из открытого репозитория GitHub (без непрерывного развертывания). Для непрерывной доставки кода функции из GitHub см. статью [Создание службы приложений](functions-cli-create-function-app-github-continuous.md).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

Этот пример создает приложения-функцию Azure и развертывает код функции из GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Создание приложения-функции с развертыванием из GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Создает приложение-функцию Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Связывает приложение-функцию с репозиторием Git или Mercurial. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).

