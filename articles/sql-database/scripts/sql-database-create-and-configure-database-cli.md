---
title: "Скрипт Azure CLI. Создание базы данных SQL | Документация Майкрософт"
description: "Пример сценария Azure CLI для создания базы данных SQL."
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
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 4a7343ebc5090dc82f6c7b470c697167536315ca
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-single-sql-database-and-configure-a-firewall-rule-using-the-azure-cli"></a>Создание отдельной базы данных SQL и настройка правила брандмауэра с помощью Azure CLI

Этот пример сценария интерфейса командной строки создает отдельную базу данных SQL Azure и настраивает правило брандмауэра уровня сервера. После успешного выполнения скрипта доступ к базе данных SQL можно получить из всех служб Azure, а также по настроенному IP-адресу. 

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`. 

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI для Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Создание базы данных SQL")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az sql server create](/cli/azure/sql/server#create) | Создает логический сервер, на котором размещена база данных SQL. |
| [az sql server firewall create](/cli/azure/sql/server/firewall#create) | Создает правило брандмауэра, чтобы разрешить доступ ко всем базам данных SQL на сервере по введенному диапазону IP-адресов. |
| [az sql db create](/cli/azure/sql/db#create) | Создает базу данных SQL на логическом сервере. |
| [az group delete](/cli/azure/resource#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).


