---
title: "Скрипт Azure CLI — изменение конфигураций серверов | Документация Майкрософт"
description: "В этом примере скрипта CLI перечисляются все доступные конфигурации серверов и обновляется значение innodb_lock_wait_timeout."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/03/2017
ms.openlocfilehash: 286b16148d28509714862714dcc5986496d9af44
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Перечисление и обновление конфигураций сервера базы данных Azure для MySQL с помощью Azure CLI
В этом примере скрипта CLI перечисляются все доступные параметры конфигурации и их допустимые значения для сервера базы данных Azure для MySQL. Кроме того, здесь задается значение *innodb_lock_wait_timeout*, отличное от значения по умолчанию.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта измените выделенные строки, чтобы настроить имя и пароль администратора.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az mysql server create](/cli/azure/mysql/server#create) | Создает сервер MySQL, на котором размещены базы данных. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#list) | Перечисляет конфигурации сервера базы данных Azure для MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#set) | Обновляет конфигурацию сервера базы данных Azure для MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#show) | Отображает конфигурацию сервера базы данных Azure для MySQL. |
| [az group delete](/cli/azure/group#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).
- Попробуйте использовать другие скрипты на основе [примеров Azure CLI для базы данных Azure для MySQL](../sample-scripts-azure-cli.md).
- Дополнительные сведения о параметрах сервера см. в статье [Как настроить параметры сервера в базе данных Azure для MySQL с помощью портала Azure](../howto-server-parameters.md).
