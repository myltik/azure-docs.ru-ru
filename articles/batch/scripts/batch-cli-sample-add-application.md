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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 342cc2c93304f7a8f651769139e28ebf2e30d412
ms.lasthandoff: 03/24/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Добавление приложений в пакетную службу Azure с помощью Azure CLI

Этот сценарий демонстрирует настройку приложения для использования в пуле или задаче пакетной службы Azure. Чтобы настроить приложение, упакуйте его исполняемый файл и зависимые компоненты в ZIP-файл. В этом примере ZIP-файл с исполняемым файлом называется my-приложения-exe.zip.
При выполнении этого сценария предполагается, что учетная запись пакетной службы уже настроена. Чтоб узнать больше, ознакомьтесь с [примером сценария для создания учетной записи пакетной службы](./batch-cli-sample-create-account.md).

При необходимости установите Azure CLI с помощью инструкций, приведенных в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), а затем выполните команду `az login` для входа в Azure.

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
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | Создает приложение.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | Обновляет свойства приложения.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | Добавляет пакет приложения в указанное приложение.  |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев интерфейса командной строки для пакетной службы доступны в [документации по интерфейсу командной строки пакетной службы Azure](../batch-cli-samples.md).

