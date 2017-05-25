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
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5fe1e3595d9459e60b2fd54d6f17f6822731f453
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Выполнение заданий в пакетной службе Azure с помощью Azure CLI

Этот сценарий создает задание пакетной службы и добавляет в него ряд задач. Он также демонстрирует, как отслеживать задание и его задачи. Кроме того, он показывает, как эффективно выполнять запрос к пакетной службе на получение сведений о задачах задания.

## <a name="prerequisites"></a>Предварительные требования

- Установите Azure CLI с помощью инструкций, приведенных в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), если вы этого еще не сделали.
- Создайте учетную запись пакетной службы, если у вас ее еще нет. Пример скрипта создания учетной записи см. в статье [Создание учетной записи пакетной службы с помощью Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account).
- Настройте выполнение приложения из задачи запуска, если вы этого еще не сделали. Пример скрипта создания приложения и отправки пакета приложения в Azure см. в статье [Добавление приложений в пакетную службу Azure с помощью Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application).
- Настройте пул, в котором будет выполняться задание. Пример скрипта создания пула с использованием конфигурации облачной службы или конфигурации виртуальной машины см. в статье [Управление пулами пакетной службы Azure с помощью Azure CLI](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/batch/run-job/run-job.sh "Выполнение задания")]

## <a name="clean-up-job"></a>Очистка задания

После выполнения представленного выше примера сценария выполните следующую команду, чтобы удалить задание и все его задачи. Обратите внимание, что пул нужно удалить отдельно. Дополнительные сведения о создании и удалении пулов см. в статье [Управление пулами пакетной службы Azure с помощью Azure CLI](./batch-cli-sample-manage-pool.md).

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
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#list) | Выводит список задач, связанных с определенным заданием.  |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов для интерфейса командной строки пакетной службы см. в [документации по интерфейсу командной строки пакетной службы Azure](../batch-cli-samples.md).

