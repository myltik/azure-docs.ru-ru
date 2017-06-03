---
title: "Создание функции Azure, которая подключается к службе хранилища Azure | Документация Майкрософт"
description: "Пример скрипта Azure CLI для создания функции Azure, которая подключается к службе хранилища Azure."
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 3925c1a3d2c5a9fc3a82e8823dbdeffabeb38a3a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---
# <a name="integrate-function-app-into-azure-storage-account"></a>Интеграция приложения-функции в учетную запись хранения Azure

Этот пример скрипта создает приложение-функцию и учетную запись хранения.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

Этот пример создает приложение-функцию Azure и добавляет строку подключения хранилища в параметры приложения.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Интеграция приложения-функции в учетную запись хранения Azure")]


## <a name="clean-up-deployment"></a>Очистка развертывания

Выполнив пример сценария, вы можете удалить группу ресурсов, приложение службы приложений и все связанные ресурсы с помощью следующей команды:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Вход в Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создайте группу ресурсов с расположением. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Создайте учетную запись хранения. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Создайте приложение-функцию. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Очистка |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций Azure см. в [документации по Функциям Azure](../functions-cli-samples.md).

