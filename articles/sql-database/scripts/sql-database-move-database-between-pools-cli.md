---
title: "Скрипт Azure CLI. Перемещение базы данных SQL и эластичных пулов | Документация Майкрософт"
description: "Пример сценария Azure CLI для перемещения базы данных SQL между эластичными пулами."
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/16/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 46c5ec2a546bc475551f2922facb8cf7c568a1fb
ms.lasthandoff: 03/17/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-the-azure-cli"></a>Создание эластичных пулов и перемещение баз данных между пулами и из пула с помощью Azure CLI

Данный пример сценария интерфейса командной строки создает два эластичных пула и перемещает базу данных из одного пула в другой, а затем перемещает базу данных из этого эластичного пула на уровень производительности отдельной базы данных. 

При необходимости установите Azure CLI с помощью инструкции, приведенной в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), а затем выполните команду `az login`, чтобы создать подключение к Azure.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI для Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Перемещение базы данных между пулами")]

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
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Создает логический сервер, на котором размещена база данных или эластичный пул. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Создает эластичный пул на логическом сервере. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Создает на логическом сервере отдельную базу данных или базу данных в составе пула. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Обновляет свойства базы данных или перемещает базу данных в эластичный пул, из него или между эластичными пулами. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).



