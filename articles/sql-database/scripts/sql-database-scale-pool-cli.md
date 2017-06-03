---
title: "Скрипт Azure CLI. Масштабирование эластичного пула | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Масштабирование пула эластичных баз данных"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5ca7befc4b7ea031287b81ae6834496579b480fd
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Масштабирование эластичного пула в базе данных SQL Azure с помощью Azure CLI

Этот пример сценария интерфейса командной строки создает эластичные пулы и перемещает базы данных в составе пулов, а также изменяет уровни производительности. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Создает пул эластичных баз данных на логическом сервере. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Создает базу данных SQL на логическом сервере. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | Обновляет пул эластичных баз данных. В этом примере также изменяется назначенная eDTU. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).

