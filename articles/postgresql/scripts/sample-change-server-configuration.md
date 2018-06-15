---
title: Скрипт Azure CLI. Изменение конфигураций серверов
description: Этот пример сценария интерфейса командной строки выводит список всех доступных параметров конфигурации сервера и обновляет значение одного из этих параметров.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: f18150a7ebc76e29399502a801228db54c47fac4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29850843"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Вывод и обновление конфигураций сервера базы данных Azure для PostgreSQL с помощью Azure CLI
В этом примере сценария интерфейса командной строки выводятся все доступные параметры конфигурации и их допустимые значения для сервера базы данных Azure для PostgreSQL. Кроме того, он задает значение *log_retention_days*, отличное от значения по умолчанию.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили выполнять CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Проверьте версию, выполнив команду `az --version`. Дополнительные сведения об установке или обновлении версии Azure CLI 2.0 см. в [этой]( /cli/azure/install-azure-cli) статье. 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта отредактируйте выделенные строки, чтобы изменить имя пользователя и пароль администратора на собственные.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=18-19 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует команды, описанные в следующей таблице:

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Создает сервер PostgreSQL, на котором размещены базы данных. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Вывод конфигураций сервера базы данных Azure для PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Обновление конфигурации сервера базы данных Azure для PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Отображение конфигурации сервера базы данных Azure для PostgreSQL. |
| [az group delete](/cli/azure/group#az_group_delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).
- Попробуйте использовать другие скрипты на основе [примеров Azure CLI для базы данных Azure для PostgreSQL](../sample-scripts-azure-cli.md).
- Дополнительные сведения о параметрах сервера см. в статье [Настройка параметров конфигурации сервера с помощью Azure CLI](../howto-configure-server-parameters-using-portal.md).
