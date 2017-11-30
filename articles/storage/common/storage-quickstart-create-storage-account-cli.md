---
title: "Краткое руководство Azure. Создание учетной записи хранения с помощью Azure CLI | Документация Майкрософт"
description: "Быстро научитесь создавать учетную запись хранения с помощью Azure CLI."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: 7186c5e2ce94d06b21d95a557e960b82e268cdce
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Создание учетной записи хранения с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. Это краткое руководство содержит сведения о создании учетной записи хранения Azure с помощью Azure CLI.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов Azure с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. В этом примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Если вы не уверены, какой регион необходимо задать для параметра `--location`, вы можете получить список поддерживаемых регионов для вашей подписки, выполнив команду [az account list-locations](/cli/azure/account#list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Создание стандартной учетной записи хранения общего назначения

Для разных сценариев использования подходят несколько типов учетных записей хранения, каждый из которых поддерживает одну или несколько служб хранилища (большие двоичные объекты, файлы, таблицы или очереди). В таблице ниже приведены типы доступных учетных записей хранения.

|**Тип учетной записи хранения**|**Общего назначения, класс "Стандартный"**|**Общего назначения, класс "Премиум"**|**Хранилище BLOB-объектов, "горячий" и "холодный" уровни доступа**|
|-----|-----|-----|-----|
|**Поддерживаемые службы**| Службы больших двоичных объектов, файлов, таблиц и очередей | Служба больших двоичных объектов | Служба больших двоичных объектов|
|**Типы поддерживаемых BLOB-объектов**|Блочные, страничные и добавочные BLOB-объекты | Blob-страницы | Блочные и добавочные BLOB-объекты|

Создайте стандартную учетную запись хранения общего назначения с помощью команды [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны какие-либо ресурсы в группе ресурсов, включая учетную запись хранения, созданную в рамках этого краткого руководства, удалите группу ресурсов с помощью команды [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали группу ресурсов и стандартную учетную запись хранения общего назначения. Сведения о передаче данных в учетную запись хранения и обратно см. в кратком руководстве по хранилищу BLOB-объектов.

> [!div class="nextstepaction"]
> [Отправка объектов в хранилище BLOB-объектов Azure и обратно с помощью Azure CLI](../blobs/storage-quickstart-blobs-cli.md)