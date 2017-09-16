---
title: "Пример сценария Azure CLI для добавления приложения в пакетную службу | Документация Майкрософт"
description: "Пример сценария Azure CLI для добавления приложения в пакетную службу."
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
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: cbfe8ab565ecf7f298a9a6c0f0c8298c675f178c
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Добавление приложений в пакетную службу Azure с помощью Azure CLI

Этот сценарий демонстрирует настройку приложения для использования в пуле или задаче пакетной службы Azure. Чтобы настроить приложение, упакуйте его исполняемый файл и зависимые компоненты в ZIP-файл. В этом примере ZIP-файл с исполняемым файлом называется my-приложения-exe.zip.

## <a name="prerequisites"></a>Предварительные требования

- Установите Azure CLI с помощью инструкций, приведенных в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), если вы этого еще не сделали.
- Создайте учетную запись пакетной службы, если у вас ее еще нет. Пример скрипта создания учетной записи см. в статье [Создание учетной записи пакетной службы с помощью Azure CLI](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/batch/add-application/add-application.sh "Добавление приложения")]

## <a name="clean-up-application"></a>Очистка приложения

После запуска приведенного выше примера сценария выполните следующие команды, чтобы удалить приложение и все его переданные пакеты приложения.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует приведенные ниже команды для создания приложения и передачи пакета приложения.
Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_create) | Создает приложение.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#az_batch_application_set) | Обновляет свойства приложения.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#az_batch_application_package_create) | Добавляет пакет приложения в указанное приложение.  |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов для интерфейса командной строки пакетной службы см. в [документации по интерфейсу командной строки пакетной службы Azure](../batch-cli-samples.md).

