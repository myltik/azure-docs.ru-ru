---
title: Краткое руководство по созданию учетной записи Служб мультимедиа Azure с помощью CLI 2.0 | Документация Майкрософт
description: В этом кратком руководстве описано, как создать учетную запись Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: f7e5cb28f90466e9366c0a32e2a333e6823b9396
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779724"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Краткое руководство по созданию учетной записи Служб мультимедиа Azure

> [!NOTE]
> Последняя версия Служб мультимедиа Azure (2018-30-03) находится в предварительной версии. Эта версия также называется третьей (3). 

Как разработчикам, так и создателям содержимого мультимедиа для хранения, шифрования, кодирования, контроля и потоковой передачи данных мультимедиа в Azure нужно создать учетную запись Служб мультимедиа. При создании учетной записи Служб мультимедиа необходимо предоставить идентификатор ресурса учетной записи хранения Azure. Указанная учетная запись хранения присоединена к учетной записи Служб мультимедиа. Этот ресурс учетной записи хранения должен находиться в том же географическом регионе, что и учетная запись Служб мультимедиа.  

В этом кратком руководстве описаны действия по созданию учетной записи Служб мультимедиа Azure с помощью CLI 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](http://portal.azure.com) и запустите **CloudShell**, чтобы выполнить команды CLI, приведенные на следующих шагах.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Настройка подписки Azure

В приведенной ниже команде укажите идентификатор подписки Azure, который необходимо использовать в учетной записи Служб мультимедиа. Чтобы просмотреть список доступных подписок, перейдите на страницу [Подписки](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Создание группы ресурсов Azure

С помощью следующей команды можно создать группу ресурсов, в которой должны быть учетная запись службы хранилища и Служб мультимедиа. Замените заполнитель *myresourcegroup* именем, которое будет использоваться для группы ресурсов.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

При создании учетной записи Служб мультимедиа необходимо предоставить идентификатор ресурса учетной записи хранения Azure. Указанная учетная запись хранения присоединена к учетной записи Служб мультимедиа. 

Необходимо иметь **основную** учетную запись хранения. У вас может быть любое количество **вторичных** учетных записей хранения, связанных с учетной записью Служб мультимедиа. Службы мультимедиа поддерживают учетные записи **общего назначения версии 2** (GPv2) или **общего назначения версии 1** (GPv1). Учетные записи, предназначенные только для большого двоичного объекта, нельзя использовать в качестве **основных**. Дополнительные сведения об учетных записях хранения см. в статье [Варианты учетной записи хранения Azure](../../storage/common/storage-account-options.md). 

Следующая команда создает учетную запись хранения, которая будет связана с учетной записью Служб мультимедиа (первичной). В приведенном ниже сценарии замените заполнитель *storageaccountforams*. Заполнитель account_name должен состоять максимум из 24 знаков.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Создание учетной записи служб мультимедиа Azure

Далее представлены команды Azure CLI, с помощью которых можно создать учетную запись Служб мультимедиа. Вам необходимо только заменить следующие выделенные значения:

* *myamsaccountname*;
* *myresourcegroup*
* *storageaccountforams*.

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны ресурсы в группе ресурсов, включая учетную запись Служб мультимедиа, созданную в рамках этого краткого руководства, удалите группу ресурсов.

В **CloudShell** выполните следующую команду:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
