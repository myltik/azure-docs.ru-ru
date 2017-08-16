---
title: "Пример для CLI. Масштабирование эластичного пула SQL в Базе данных SQL Azure | Документация Майкрософт"
description: "Пример сценария Azure CLI для масштабирования эластичного пула SQL в Базе данных SQL Azure."
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 888d2b7b7c118fede82d39881570a3b3d7b09961
ms.contentlocale: ru-ru
ms.lasthandoff: 08/05/2017

---

# <a name="use-cli-to-scale-a-sql-elastic-pool-in-azure-sql-database"></a>Масштабирование эластичного пула SQL в Базе данных SQL Azure с помощью интерфейса командной строки

Этот пример сценария Azure CLI создает эластичные пулы SQL и перемещает базы данных в составе пулов, а также изменяет уровни производительности эластичных пулов. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Перемещение базы данных между пулами")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, логического сервера, базы данных SQL и правил брандмауэра этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Создает логический сервер, на котором размещена база данных SQL. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#create) | Создает пул эластичных баз данных на логическом сервере. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Создает базу данных SQL на логическом сервере. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pool#update) | Обновляет пул эластичных баз данных. В этом примере также изменяется назначенная eDTU. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).

