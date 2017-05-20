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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d887999d1118e498293fb13b9612828d914b0ae
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-function-app-in-an-app-service-plan"></a>Создание приложения-функции в плане службы приложений

Этот пример сценария создает приложение-функцию Azure, которое является контейнером для ваших функций. Приложение-функция будет создано с помощью выделенного плана службы приложений. Это означает, что ресурсы сервера всегда включены.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

Создание примера приложения

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Создание функции Azure на основе плана службы приложений")]

## <a name="clean-up-deployment"></a>Очистка развертывания

Выполнив пример скрипта, вы можете удалить группу ресурсов, приложение службы приложений и все связанные ресурсы с помощью следующей команды.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appserviceplan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | Создает план службы приложений. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Создает приложение-функцию Azure. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).
