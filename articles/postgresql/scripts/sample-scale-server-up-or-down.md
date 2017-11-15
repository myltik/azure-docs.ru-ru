---
title: "Скрипт Azure CLI. Масштабирование базы данных Azure для PostgreSQL | Документация Майкрософт"
description: "Здесь приведен пример скрипта Azure CLI, который масштабирует сервер базы данных Azure для PostgreSQL до нужного уровня производительности после выполнения запроса к метрикам."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/03/2017
ms.openlocfilehash: 9fe5b56d64d325f855b6030ef3dcc0ed65ad7afc
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Мониторинг и масштабирование отдельного сервера PostgreSQL с помощью Azure CLI
Этот пример скрипта CLI масштабирует отдельный сервер базы данных Azure для PostgreSQL до нужного уровня производительности после выполнения запроса к метрикам. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта измените выделенные строки, чтобы настроить имя и пароль администратора. Замените идентификатор подписки, используемый в командах мониторинга az, собственным идентификатором.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az postgres server create](/cli/azure/postgres/server#create) | Создает сервер PostgreSQL, на котором размещены базы данных. |
| [az monitor metrics list](/cli/azure/monitor/metrics#list) | Выводит список значений метрики для ресурсов. |
| [az group delete](/cli/azure/group#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).
- Попробуйте использовать другие скрипты на основе [примеров Azure CLI для базы данных Azure для PostgreSQL](../sample-scripts-azure-cli.md).
- Дополнительные сведения о масштабировании см. в статьях об [уровнях служб](../concepts-service-tiers.md) и [единицах вычислений и единицах хранения](../concepts-compute-unit-and-storage.md).
