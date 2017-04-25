---
title: "Сценарий Azure CLI. Мониторинг и масштабирование отдельной базы данных SQL | Документация Майкрософт"
description: "Пример сценария Azure CLI для мониторинга и масштабирования отдельной базы данных SQL."
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: f29da889f90968a82dccaeb1fa7e3c20e6b44458
ms.lasthandoff: 03/30/2017

---

# <a name="monitor-and-scale-a-single-sql-database-using-the-azure-cli"></a>Мониторинг и масштабирование отдельной базы данных SQL с помощью Azure CLI

Этот пример сценария интерфейса командной строки масштабирует отдельную базу данных SQL Azure до другого уровня производительности после запроса на получение сведений о размере базы данных. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Мониторинг и масштабирование отдельной базы данных SQL")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Создает логический сервер, на котором размещена база данных. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#show-usage) | Отображает сведения об используемом размере базы данных. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Обновляет свойства базы данных (например, уровень служб или уровень производительности) или перемещает базу данных в эластичный пул, из него или между эластичными пулами. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).

