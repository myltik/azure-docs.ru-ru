---
title: "Пример сценария Azure CLI. Сопоставление личного домена с приложением-функцией | Документация Майкрософт"
description: "Пример сценария Azure CLI для сопоставления личного домена с приложением-функцией в Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cd7ab0bbe92fa32d23a841b0b17bee8510f6b406
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Сопоставление личного домена с приложением-функцией

Этот пример сценария создает приложение-функцию со связанными ресурсами, а затем сопоставляет с ним `www.<yourdomain>`. Для сопоставления с личным доменом приложение-функция должно быть создано в плане службы приложений, а не в плане потребления. Функции Azure поддерживают сопоставление личного домена только с помощью записи A.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Сопоставление личного домена с приложением-функцией")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Создает учетную запись хранения, необходимую для приложения-функции. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений, необходимый для сопоставления личного домена. |
| [az functionapp create]() | Создает приложение-функцию. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Сопоставляет личный домен с приложением-функцией. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций см. в [документации по Функциям Azure]().

