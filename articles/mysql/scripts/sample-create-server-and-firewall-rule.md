---
title: "Скрипт Azure CLI. Создание базы данных Azure для MySQL | Документация Майкрософт"
description: "Этот пример скрипта CLI создает сервер базы данных Azure для MySQL и настраивает правило брандмауэра на уровне сервера."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 11/02/2017
ms.openlocfilehash: 50893a2265fcb9a610564748ba316a70c5205b4a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Создание сервера MySQL и настройка правила брандмауэра с помощью Azure CLI
Этот пример скрипта CLI создает сервер базы данных Azure для MySQL и настраивает правило брандмауэра на уровне сервера. После успешного выполнения скрипта доступ к серверу MySQL можно получить из всех служб Azure, а также по настроенному IP-адресу.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта
В этом примере скрипта измените выделенные строки, чтобы настроить имя и пароль администратора.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/create-mysql-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for MySQL, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>Очистка развертывания
После выполнения примера сценария можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/create-mysql-server-and-firewall-rule/delete-mysql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Описание скрипта
Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| **Команда** | **Примечания** |
|---|---|
| [az group create](/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az mysql server create](/cli/azure/mysql/server#create) | Создает сервер MySQL, на котором размещены базы данных. |
| [az mysql server firewall create](/cli/azure/mysql/server/firewall-rule#create) | Создает правило брандмауэра, чтобы разрешить доступ к серверу и размещенным на нем базам данных по введенному диапазону IP-адресов. |
| [az group delete](/cli/azure/group#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure/overview).
- Попробуйте использовать другие скрипты на основе [примеров Azure CLI для базы данных Azure для MySQL](../sample-scripts-azure-cli.md).
