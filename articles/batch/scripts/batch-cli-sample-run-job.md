---
title: "Пример сценария Azure CLI для выполнения задания с помощью пакетной службы | Документация Майкрософт"
description: "Пример сценария Azure CLI для выполнения задания в пакетной службе."
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
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Выполнение заданий в пакетной службе Azure с помощью Azure CLI

Этот сценарий создает задание пакетной службы и добавляет в него ряд задач. Он также демонстрирует, как отслеживать задание и его задачи.
При выполнении этого сценария предполагается, что учетная запись пакетной службы уже создана, а пул и приложение настроены. Чтобы узнать больше, изучите [примеры сценариев](../batch-cli-samples.md), представленные для каждого из этих разделов.

При необходимости установите Azure CLI с помощью инструкций, приведенных в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), а затем выполните команду `az login` для входа в Azure.

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Выполнение задания")]

## <a name="clean-up-job"></a>Очистка задания

После выполнения представленного выше примера сценария выполните следующую команду, чтобы удалить задание и все его задачи. Обратите внимание, что пул потребуется удалить отдельно. Ознакомьтесь с [руководством по управлению пулами](./batch-cli-sample-manage-pool.md).

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать задание и задачи пакетной службы, сценарий использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Выполняет аутентификацию учетной записи пакетной службы.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | Создает задание пакетной службы.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | Обновляет свойства задания пакетной службы.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | Получает сведения об указанном задании пакетной службы.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | Добавляет задачу в указанное задание пакетной службы.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | Получает сведения о задаче из указанного задания пакетной службы.  |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для пакетной службы доступны в [документации по интерфейсу командной строки пакетной службы Azure](../batch-cli-samples.md).

