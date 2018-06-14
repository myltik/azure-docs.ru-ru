---
title: Пример скрипта Azure CLI. Создание ресурса Служб мультимедиа Azure | Документация Майкрософт
description: В скрипте Azure CLI в этой статье показано, как создать ресурс Cлужб мультимедиа для отправки в него содержимого.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: 41dfafe7ce8287d1bacac672209824c1c32067e2
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161301"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Пример скрипта CLI. Создание учетной записи Служб мультимедиа Azure

В скрипте Azure CLI в этой статье показано, как создать ресурс Cлужб мультимедиа для отправки в него содержимого.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="next-steps"></a>Дополнительная информация

См. [другие примеры Azure CLI](../cli-samples.md).
