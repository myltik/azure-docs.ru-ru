---
title: "Развертывание в службе \"Экземпляры контейнеров Azure\" из реестра контейнеров Azure"
description: "Развертывание в службе \"Экземпляры контейнеров Azure\" из реестра контейнеров Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 9667a5b840d6c1fab5087cfcf3ede34a732fbe01
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Развертывание в службе "Экземпляры контейнеров Azure" из реестра контейнеров Azure

Реестр контейнеров Azure является частным реестром на базе Azure для образов контейнеров Docker. В этой статье описывается, как развертывать в службе "Экземпляры контейнеров Azure" образы контейнеров, хранящиеся в реестре контейнеров Azure.

## <a name="using-the-azure-cli"></a>Использование Azure CLI

Azure CLI содержит команды для создания контейнеров в службе "Экземпляры контейнеров Azure" и управления ими. Если вы укажете в команде `create` частный образ, вместе с ним можно указать пароль реестра образов, который нужен для проверки подлинности в реестре контейнеров.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

Команда `create` поддерживает также параметры `registry-login-server` и `registry-username`. Важно знать, что для реестра контейнеров Azure используется сервер входа *имя_реестра*.azurecr.io и имя пользователя по умолчанию *имя_реестра*. Эти значения будут выведены из имени образа, если вы не укажете их явным образом.

## <a name="using-an-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager

Свойства реестра контейнеров Azure можно указать в шаблоне Azure Resource Manager, добавив свойство `imageRegistryCredentials` в определение группы контейнеров:

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

## <a name="using-the-azure-portal"></a>Использование портала Azure

Если образы контейнеров хранятся в реестре контейнеров Azure, можно легко создать контейнер в службе "Экземпляры контейнеров Azure" с помощью портала Azure.

1. На портале Azure перейдите в реестр контейнеров.

2. Выберите "Репозитории".

    ![Меню реестра контейнеров Azure на портале Azure][acr-menu]

3. Выберите репозиторий, из которого требуется выполнить развертывание.

4. Щелкните правой кнопкой мыши тег для образа контейнера, который нужно развернуть.

    ![Контекстное меню запуска контейнера с помощью службы "Экземпляры контейнеров Azure"][acr-runinstance-contextmenu]

5. Введите имя контейнера и группы ресурсов. При необходимости можно изменить значения по умолчанию.

    ![Меню "Создание" для службы "Экземпляры контейнеров Azure"][acr-create-deeplink]

6. После завершения развертывания можно перейти к группе контейнеров в области уведомлений, чтобы найти ее IP-адрес и другие свойства.

    ![Представление сведений для группы контейнеров службы "Экземпляры контейнеров Azure"][aci-detailsview]

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как выполнить сборку контейнеров, отправить их в закрытый реестр контейнеров и развернуть их в службе "Экземпляры контейнеров Azure", [изучив это руководство](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
