---
title: Пример сценария Azure CLI. Публикация ресурса | Документация Майкрософт
description: Для публикации ресурса используйте сценарий Azure CLI.
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
ms.date: 05/11/2018
ms.author: juliako
ms.openlocfilehash: 5394ec12e0a11ac68585dcd64dc5255c38249937
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="cli-example-publish-an-asset"></a>Пример CLI. Публикация ресурса

В этой статье показано, как создать потоковый указатель и вернуть URL-адрес для потоковой передачи с помощью сценария Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Пример сценария

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>Дополнительная информация

См. [другие примеры Azure CLI](../cli-samples.md).
