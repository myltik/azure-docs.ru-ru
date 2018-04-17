---
title: Пример для CLI. Мониторинг и масштабирование отдельной базы данных SQL Azure | Документация Майкрософт
description: Пример сценария Azure CLI для отслеживания и масштабирования отдельной базы данных SQL Azure.
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: janeng
ms.openlocfilehash: da1b50a450f83bbcea1491c224260381e49ef66f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Мониторинг и масштабирование отдельной базы данных SQL с помощью интерфейса командной строки

Этот пример сценария Azure CLI масштабирует отдельную базу данных SQL Azure до другого уровня производительности после запроса на получение сведений о размере базы данных. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Список операций, выполняемых в базе данных, можно получить при помощи [az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list). Чтобы отменить операцию обновления в базе данных, воспользуйтесь [az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel).

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Создает логический сервер, на котором размещена база данных. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_show_usage) | Отображает сведения об используемом размере базы данных. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | Обновляет свойства базы данных (например, уровень служб или уровень производительности) или перемещает базу данных в эластичный пул, из него или между эластичными пулами. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).
