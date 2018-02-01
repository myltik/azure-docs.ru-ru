---
title: "Развертывание в службе \"Экземпляры контейнеров Azure\" из реестра контейнеров Azure"
description: "Сведения о том, как развертывать контейнеры в службе \"Экземпляры контейнеров Azure\" с помощью образов контейнеров, хранящихся в реестре контейнеров Azure."
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/24/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c69b95f66bf2eaf4975961da5b25f5ac6172798c
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Развертывание в службе "Экземпляры контейнеров Azure" из реестра контейнеров Azure

Реестр контейнеров Azure — это частный реестр на базе Azure для образов контейнеров Docker. В этой статье описывается, как развертывать в службе "Экземпляры контейнеров Azure" образы контейнеров, хранящиеся в реестре контейнеров Azure.

## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Azure CLI содержит команды для создания контейнеров в службе "Экземпляры контейнеров Azure" и управления ими. Если вы укажете в команде [az container create][az-container-create] частный образ, вместе с ним можно указать пароль реестра образов, который нужен для проверки подлинности в реестре контейнеров.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

Команда [az container create][az-container-create] поддерживает также параметры `--registry-login-server` и `--registry-username`. Важно знать, что для реестра контейнеров Azure используется сервер входа *имя_реестра*.azurecr.io и имя пользователя по умолчанию *имя_реестра*. Эти значения будут выведены из имени образа, если вы не укажете их явным образом.

## <a name="deploy-with-azure-resource-manager-template"></a>Развертывание с помощью шаблона Azure Resource Manager

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

1. Щелкните **Репозитории**, а затем выберите репозиторий, из которого необходимо выполнить развертывание. Щелкните правой кнопкой мыши тег для образа контейнера, который нужно развернуть, и выберите пункт **Запустить экземпляр**.

    ![Пункт "Запустить экземпляр" в реестре контейнеров Azure на портале Azure][acr-runinstance-contextmenu]

1. Введите имя контейнера и группы ресурсов. При необходимости можно изменить значения по умолчанию.

    ![Меню "Создание" для службы "Экземпляры контейнеров Azure"][acr-create-deeplink]

1. После завершения развертывания можно перейти к группе контейнеров в области уведомлений, чтобы найти ее IP-адрес и другие свойства.

    ![Представление сведений для группы контейнеров службы "Экземпляры контейнеров Azure"][aci-detailsview]

## <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы

Если учетная запись администратора для реестра контейнеров Azure отключена, можно использовать [субъект-службу](../container-registry/container-registry-auth-service-principal.md) Azure Active Directory для проверки подлинности в реестре при создании экземпляра контейнера. Использовать субъект-службу для проверки подлинности также рекомендуется в сценариях автоматического входа, например для скриптов или приложений, которые автоматически создают экземпляры контейнеров.

Дополнительные сведения см. в статье [Authenticate with Azure Container Registry from Azure Container Instances](../container-registry/container-registry-auth-aci.md) (Аутентификация в реестре контейнеров Azure из службы "Экземпляры контейнеров Azure").

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как выполнить сборку контейнеров, отправить их в закрытый реестр контейнеров и развернуть их в службе "Экземпляры контейнеров Azure", [изучив это руководство](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create