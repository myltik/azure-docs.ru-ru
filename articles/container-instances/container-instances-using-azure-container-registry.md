---
title: "Развертывание в службе \"Экземпляры контейнеров Azure\" из реестра контейнеров Azure"
description: "Развертывание в службе \"Экземпляры контейнеров Azure\" из реестра контейнеров Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 4205b47dc67920021812c1e573a98de64ad198ec
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Развертывание в службе "Экземпляры контейнеров Azure" из реестра контейнеров Azure

Реестр контейнеров Azure является частным реестром на базе Azure для образов контейнеров Docker. В этой статье описывается, как развертывать в службе "Экземпляры контейнеров Azure" образы контейнеров, хранящиеся в реестре контейнеров Azure.

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Azure CLI содержит команды для создания контейнеров в службе "Экземпляры контейнеров Azure" и управления ими. При указании частным образом в [создать контейнер az] [ az-container-create] команду, можно также указать пароль реестра изображения, необходимый для проверки подлинности в реестре контейнеров.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

[Создать контейнер az] [ az-container-create] команда поддерживает задание `--registry-login-server` и `--registry-username`. Важно знать, что для реестра контейнеров Azure используется сервер входа *имя_реестра*.azurecr.io и имя пользователя по умолчанию *имя_реестра*. Эти значения будут выведены из имени образа, если вы не укажете их явным образом.

## <a name="deploy-with-azure-resource-manager-template"></a>Развертывание с помощью шаблона диспетчера ресурсов Azure

Свойства реестра контейнеров Azure можно указать в шаблоне Azure Resource Manager, добавив свойство `imageRegistryCredentials` в определение группы контейнеров:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Чтобы не сохранять в шаблоне пароль к реестру контейнеров, сохраните его как секрет в [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) и укажите в шаблоне ссылку на него, используя [интеграцию платформенной функциональности между Azure Resource Manager и Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Развертывание с помощью портала Azure

Если образы контейнеров хранятся в реестре контейнеров Azure, можно легко создать контейнер в службе "Экземпляры контейнеров Azure" с помощью портала Azure.

1. На портале Azure перейдите в реестр контейнеров.

2. Выберите **репозиториев**, а затем выберите репозиторий, в который требуется развернуть, щелкните правой кнопкой мыши в теге образ контейнера, который требуется развернуть и выберите **запуска экземпляра**.

    ![«Запуск экземпляра» в реестре контейнеров Azure на портале Azure][acr-runinstance-contextmenu]

3. Введите имя контейнера и группы ресурсов. При необходимости можно изменить значения по умолчанию.

    ![Меню "Создание" для службы "Экземпляры контейнеров Azure"][acr-create-deeplink]

4. После завершения развертывания можно перейти к группе контейнеров в области уведомлений, чтобы найти ее IP-адрес и другие свойства.

    ![Представление сведений для группы контейнеров службы "Экземпляры контейнеров Azure"][aci-detailsview]

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как выполнить сборку контейнеров, отправить их в закрытый реестр контейнеров и развернуть их в службе "Экземпляры контейнеров Azure", [изучив это руководство](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create