---
title: Пример сценария Azure CLI. Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин | Документация Майкрософт
description: Пример сценария Azure CLI. Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 9c4032191738703b71319c05bce4f6bbbc61b44b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847205"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Получение ресурсов из управляемой группы ресурсов и изменение размера виртуальных машин с помощью Azure CLI

С помощью этого скрипта можно получить ресурсы из управляемой группы ресурсов и изменить размер виртуальных машин в этой группе.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для развертывания управляемого приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Перечисление управляемых приложений Azure. Укажите значения запроса для поиска результатов. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Перечисление ресурсов. Укажите группу ресурсов и значения запроса для поиска результатов. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Обновление размера виртуальной машины. |


## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
