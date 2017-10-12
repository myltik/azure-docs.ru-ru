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
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: antisch
ms.openlocfilehash: ae7eab97c1da1113b0248b74a9dd67de8ce49e36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-batch-pools-with-azure-cli"></a>Управление пулами пакетной службы Azure с помощью Azure CLI

Этот скрипт демонстрирует некоторые из доступных средств Azure CLI для создания пулов вычислительных узлов в пакетной службе Azure и управления ими.

> [!NOTE]
> В этом примере используются команды для создания виртуальных машин Azure. При использовании виртуальных машин с вашей учетной записи будет взиматься плата. Чтобы свести к минимуму эти расходы, удалите виртуальные машины, когда завершите использование примера. См. раздел [Очистка пулов](#clean-up-pools).

Пулы пакетной службы можно настроить двумя способами: с помощью настройки облачных служб (только для Windows) или настройки виртуальных машин (для Windows и Linux). В примерах скриптов ниже показано, как создать пулы с использованием этих двух конфигураций.

## <a name="prerequisites"></a>Предварительные требования

- Установите Azure CLI с помощью инструкций, приведенных в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), если вы этого еще не сделали.
- Создайте учетную запись пакетной службы, если у вас ее еще нет. Пример скрипта создания учетной записи см. в статье [Создание учетной записи пакетной службы с помощью Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account).
- Настройте выполнение приложения из задачи запуска, если вы этого еще не сделали. Пример скрипта создания приложения и отправки пакета приложения в Azure см. в статье [Добавление приложений в пакетную службу Azure с помощью Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application).

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Пример скрипта для пула с настройкой облачной службы

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Cloud Services Pools")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Пример скрипта для пула с настройкой виртуальной машины

[!code-azurecli[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Virtual Machine Pools")]

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
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) | Проверка подлинности учетной записи пакетной службы.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#az_batch_application_summary_list) | Список доступных приложений в учетной записи пакетной службы.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | Создание пула виртуальных машин.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_set) | Обновление свойств пула.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#az_batch_pool_node_agent_skus_list) | Отображение списка доступных номеров SKU агента узла и сведений об образе.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_resize) | Изменение количества работающих виртуальных машин в указанном пуле.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_show) | Отображение свойств пула.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_delete) | Удаление указанного пула.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Включение автоматического масштабирования в пуле и применение формулы.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Отключение автоматического масштабирования в пуле.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_list) | Отображение списка всех вычислительных узлов в указанном пуле.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_reboot) | Перезагрузка указанного вычислительного узла.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az_batch_node_delete) | Удаление указанных узлов из указанного пула.  |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов для интерфейса командной строки пакетной службы см. в [документации по интерфейсу командной строки пакетной службы Azure](../batch-cli-samples.md).

