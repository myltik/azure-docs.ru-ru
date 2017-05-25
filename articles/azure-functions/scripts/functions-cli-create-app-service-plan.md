---
title: "Пример сценария Azure CLI. Создание приложения-функции в плане службы приложений | Документация Майкрософт"
description: "Пример сценария Azure CLI для создания приложения-функции в плане службы приложений."
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 8c98e392c1c735458184bb782ed9c42f468f33de
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Создание приложения-функции в плане службы приложений

Этот пример сценария создает приложение-функцию Azure, которое является контейнером для ваших функций. Приложение-функция создается с помощью выделенного плана службы приложений. Это означает, что ресурсы сервера всегда включены.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

Этот скрипт создает приложение-функцию Azure с помощью выделенного [плана службы приложений](../functions-scale.md#app-service-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Создание функции Azure на основе плана службы приложений")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Создает учетную запись хранения Azure. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | Создает план службы приложений. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Создает приложение-функцию Azure. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).

