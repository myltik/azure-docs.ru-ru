---
title: "Пример скрипта Azure CLI. Создание учетной записи пакетной службы | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание учетной записи пакетной службы"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 698978fd717091c49a1375e222f46f4325431223
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>Создание учетной записи пакетной службы с помощью Azure CLI

С помощью этого скрипта создается учетная запись пакетной службы Azure. Кроме того, в нем показано, как запрашивать и обновлять различные свойства учетной записи.

## <a name="prerequisites"></a>Предварительные требования

Установите Azure CLI с помощью инструкций, приведенных в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), если вы этого еще не сделали.

## <a name="batch-account-sample-script"></a>Пример скрипта учетной записи пакетной службы

Когда вы создаете учетную запись пакетной службы, по умолчанию ее вычислительные узлы назначаются внутри пакетной службы. К выделенным вычислительным узлам будет применяться отдельная квота на ядра, и учетная запись сможет проходить проверку подлинности с помощью учетных данных общего ключа или маркера Azure Active Directory.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "Создание учетной записи")]

## <a name="batch-account-using-user-subscription-sample-script"></a>Учетная запись пакетной службы с использованием примера скрипта подписки пользователя

Вы также можете разрешить пакетной службе создавать вычислительные узлы в вашей подписке Azure.
Учетные записи, которые выделяют вычислительные узлы в подписке, должны пройти проверку подлинности с помощью маркера Azure Active Directory. Тогда выделенные вычислительные узлы будут учитываться в квоте вашей подписки. Чтобы создать учетную запись в этом режиме, необходимо указать ссылку хранилище ключей при создании учетной записи.

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "Создание учетной записи с помощью подписки пользователя")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения одного из примеров скриптов, приведенных выше, выполните следующую команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы (включая учетные записи пакетной службы, учетные записи хранения Azure и хранилища ключей Azure).

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, учетной записи пакетной службы и всех связанных с ними ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#create) | Создает учетную запись пакетной службы.  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#set) | Обновляет свойства учетной записи пакетной службы.  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#show) | Получает сведения об указанной учетной записи пакетной службы.  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#list) | Получает ключи доступа указанной учетной записи пакетной службы.  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Выполняет проверку подлинности с помощью указанной учетной записи пакетной службы для дальнейшего взаимодействия с интерфейсом командной строки.  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Создание учетной записи хранения. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | Создает хранилище ключей. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | Обновляет политику безопасности указанного хранилища ключей. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Удаляет группу ресурсов со всеми вложенными ресурсами. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов для интерфейса командной строки пакетной службы см. в [документации по интерфейсу командной строки пакетной службы Azure](../batch-cli-samples.md).

