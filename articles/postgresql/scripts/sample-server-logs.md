---
title: "Сценарий Azure CLI. Скачивание журналов сервера в базе данных Azure для PostgreSQL"
description: "Этот пример сценария Azure CLI демонстрирует, как включить и скачать журналы сервера базы данных Azure для PostgreSQL."
services: postgresql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 8bd6e193ca52401b16e141162a76d39bdef840f1
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Включение и загрузка журналов медленных запросов для сервера базы данных Azure для PostgreSQL с помощью Azure CLI
Этот пример сценария интерфейса командной строки позволяет скачать журналы медленных запросов на одном сервере базы данных Azure для PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим примером вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта измените выделенные строки, чтобы настроить имя и пароль администратора. Замените <log_file_name> в командах az monitor своим именем для файла журнала сервера.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | Создает сервер PostgreSQL, на котором размещены базы данных. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Перечисление значений конфигурации для сервера. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Обновление конфигурации сервера. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Выводит список файлов журнала для сервера. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Скачивание файлов журнала. |
| [az group delete](/cli/azure/group#az_group_delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).
- Попробуйте использовать другие скрипты на основе [примеров Azure CLI для базы данных Azure для PostgreSQL](../sample-scripts-azure-cli.md).
- [Настройка журналов сервера и получение к ним доступа с помощью портала Azure](../howto-configure-server-logs-in-portal.md)
