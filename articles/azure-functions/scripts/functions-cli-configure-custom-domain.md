---
title: "Пример сценария Azure CLI. Сопоставление личного домена с приложением-функцией | Документация Майкрософт"
description: "Пример сценария Azure CLI для сопоставления личного домена с приложением-функцией в Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
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
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 87b79d6222f40e3dc1306ecace51bae50b06e484
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Сопоставление личного домена с приложением-функцией

Этот пример сценария создает приложение-функцию со связанными ресурсами, а затем сопоставляет с ним `www.<yourdomain>`. Для сопоставления с личным доменом приложение-функция должно быть создано в плане службы приложений, а не в плане потребления. Функции Azure поддерживают сопоставление личного домена только с помощью записи A.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, это должен быть Azure CLI версии 2.0 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Сопоставление личного домена с приложением-функцией")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии используются следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Создает учетную запись хранения, необходимую для приложения-функции. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Создает план службы приложений, необходимый для сопоставления личного домена. |
| [az functionapp create]() | Создает приложение-функцию. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#az_appservice_web_config_hostname_add) | Сопоставляет личный домен с приложением-функцией. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для Функций см. в [документации по Функциям Azure]().

