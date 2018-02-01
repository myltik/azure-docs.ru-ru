---
title: "Аутентификация с помощью реестра контейнеров Azure из службы \"Экземпляры контейнеров Azure\""
description: "Узнайте, как обеспечить доступ к образам в закрытом реестре контейнеров из службы \"Экземпляры контейнеров Azure\" с помощью субъекта-службы Azure Active Directory."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 00d9632a5d0c42eceee1b412f8963bbadbea651f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Аутентификация с помощью реестра контейнеров Azure из службы "Экземпляры контейнеров Azure"

Вы можете обеспечить доступ к закрытым реестрам контейнеров в реестре контейнеров Azure с помощью субъекта-службы Azure Active Directory (Azure AD).

Из этой статьи вы узнаете, как создать и настроить субъект-службу Azure AD с разрешениями на *извлечение данных* в реестре. Затем вы запустите контейнер в службе "Экземпляры контейнеров Azure" (ACI), которая извлекает образ контейнера из закрытого реестра, с помощью субъекта-службы для аутентификации.

## <a name="when-to-use-a-service-principal"></a>Когда следует использовать субъект-службу

Субъект-службу следует использовать для аутентификации из ACI в **сценариях автоматического входа**, например в приложениях или службах, которые создают экземпляры контейнеров автоматически или другим способом без участия пользователя.

Например, для автоматического скрипта, который выполняется ночью и создает [экземпляр контейнера на основе задач](../container-instances/container-instances-restart-policy.md), чтобы обрабатывать определенные данные, можно использовать субъект-службу с разрешениями только на извлечение (читателя) для аутентификации в реестре. Затем можно сменить учетные данные субъекта-службы или полностью отменить его доступ, что никак не отразится на других службах и приложениях.

Субъекты-службы также следует использовать, если учетная запись [администратора](container-registry-authentication.md#admin-account) реестра отключена.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Аутентификация с помощью субъекта-службы

Чтобы запустить контейнер в службе "Экземпляры контейнеров Azure", используя субъект-службу, укажите идентификатор субъекта-службы для параметра `--registry-username` и пароль для параметра `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="next-steps"></a>Дополнительная информация

В следующих статьях приводятся дополнительные сведения о работе с субъектами-службами и реестром контейнеров Azure.

* [Аутентификация в реестре контейнеров Azure с помощью субъектов-служб](container-registry-auth-service-principal.md)
* [Аутентификация с помощью реестра контейнеров Azure из Службы контейнеров Azure](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->
