---
title: "Пример скрипта Azure CLI для управления пулами в пакетной службе | Документация Майкрософт"
description: "Пример скрипта Azure CLI для управления пулами в пакетной службе"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7cec2a8c66868c601c38dc2dea61dda38d3cd3b2
ms.lasthandoff: 03/24/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>Управление пулами пакетной службы Azure с помощью Azure CLI

Этот скрипт демонстрирует некоторые из доступных средств Azure CLI для создания пулов вычислительных узлов в пакетной службе Azure и управления ими.

Выполнение этих скриптов предполагает, что учетная запись пакетной службы уже создана, а приложение уже настроено. Дополнительные сведения см. в [примерах скриптов](../batch-cli-samples.md), представленных для каждого из этих разделов.

> [!NOTE]
> В этом примере используются команды для создания виртуальных машин Azure. При использовании виртуальных машин с вашей учетной записи будет взиматься плата. Чтобы свести к минимуму эти расходы, удалите виртуальные машины, когда завершите использование примера. См. раздел [Очистка пулов](#clean-up-pools).

При необходимости установите Azure CLI с помощью инструкций, приведенных в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), а затем выполните команду `az login` для входа в Azure.

Пулы пакетной службы можно настроить двумя способами: с помощью настройки облачных служб (только для Windows) или настройки виртуальных машин (для Windows и Linux).

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Пример скрипта для пула с настройкой облачной службы

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Управление пулами облачных служб")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Пример скрипта для пула с настройкой виртуальной машины

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Управление пулами виртуальных машин")]

## <a name="clean-up-pools"></a>Очистка пулов

После выполнения представленного выше примера скрипта запустите следующую команду, чтобы удалить пулы.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>Описание скрипта

Для создания и обработки пулов пакетной службы этот скрипт использует указанные ниже команды.
Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Проверка подлинности учетной записи пакетной службы.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#list) | Список доступных приложений в учетной записи пакетной службы.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#create) | Создание пула виртуальных машин.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#set) | Обновление свойств пула.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#list) | Отображение списка доступных номеров SKU агента узла и сведений об образе.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#resize) | Изменение количества работающих виртуальных машин в указанном пуле.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#show) | Отображение свойств пула.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#delete) | Удаление указанного пула.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#enable) | Включение автоматического масштабирования в пуле и применение формулы.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#disable) | Отключение автоматического масштабирования в пуле.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#list) | Отображение списка всех вычислительных узлов в указанном пуле.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#reboot) | Перезагрузка указанного вычислительного узла.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#delete) | Удаление указанных узлов из указанного пула.  |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов для интерфейса командной строки пакетной службы см. в [документации по интерфейсу командной строки пакетной службы Azure](../batch-cli-samples.md).


