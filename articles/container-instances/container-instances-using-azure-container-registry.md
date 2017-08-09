---
title: "Развертывание в службе \"Экземпляры контейнеров Azure\" из реестра контейнеров Azure | Документация Azure"
description: "Развертывание в службе \"Экземпляры контейнеров Azure\" из реестра контейнеров Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: fcb21785584c3c5fb41f5ceb70346e3e05a30d93
ms.contentlocale: ru-ru
ms.lasthandoff: 07/26/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Развертывание в службе "Экземпляры контейнеров Azure" из реестра контейнеров Azure

Реестр контейнеров Azure является частным реестром на базе Azure для образов контейнеров Docker. В этой статье описывается, как развертывать в службе "Экземпляры контейнеров Azure" образы контейнеров, хранящиеся в реестре контейнеров Azure.

## <a name="using-the-azure-cli"></a>Использование Azure CLI

Azure CLI содержит команды для создания контейнеров в службе "Экземпляры контейнеров Azure" и управления ими. Если вы укажете в команде `create` частный образ, вместе с ним можно указать пароль реестра образов, который нужен для проверки подлинности в реестре контейнеров.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

Команда `create` поддерживает также параметры `image-registry-login-server` и `image-registry-username`. Важно знать, что для входа в реестр контейнеров Azure по умолчанию используется сервер *имя_реестра*.azurecr.io и имя пользователя *имя_реестра*. Эти значения будут выведены из имени образа, если вы не укажете их явным образом.

## <a name="using-an-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager

В шаблоне Azure Resource Manager можно задать свойства реестра контейнеров Azure. Для этого достаточно включить свойство `imageRegistryCredentials` в определение группы контейнеров:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Чтобы не сохранять в шаблоне пароль к реестру контейнеров, сохраните его как секрет в [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) и укажите в шаблоне ссылку на него, используя [интеграцию платформенной функциональности между Azure Resource Manager и Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как выполнить сборку контейнеров, отправить их в закрытый реестр контейнеров и развернуть их в службе "Экземпляры контейнеров Azure", [изучив это руководство](container-instances-tutorial-prepare-app.md).
