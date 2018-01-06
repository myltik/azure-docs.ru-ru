---
title: "Azure репозиториев контейнер реестра на портале Azure"
description: "Порядок просмотра репозиториев реестра контейнера Azure на портале Azure."
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 593972e972207a27d1232fcb0c1bf220ac3a8def
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Просмотреть репозитории реестра контейнера на портале Azure

Azure реестра контейнера можно хранить Docker образы контейнеров в репозитории. Путем сохранения изображений в репозитории, можно хранить в изолированных средах групп изображения (или версий образов). Можно указать эти репозитории при принудительной отправки изображений в системный реестр и просматривать их содержимое на портале Azure.

## <a name="prerequisites"></a>Технические условия

* **Контейнер реестра**: создание реестра контейнер в вашей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI**: Установка [Docker] [ docker-install] на локальном компьютере, который предоставляет интерфейс командной строки Docker.
* **Образ контейнера**: Push изображения в системный реестр контейнера. Рекомендации о том, как изображения, push и pull см. в разделе [по запросу и принудительные изображения](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Представление репозиториев на портале Azure

Можно просмотреть список репозиториев, размещение изображений, а также тегов изображений, на портале Azure.

Если вы следовали инструкциям [по запросу и принудительные изображения](container-registry-get-started-docker-cli.md) и впоследствии не удалось удалить образ, должен содержать образ Nginx в реестре контейнера. В этой статье инструкциям указан тег изображения с пространством имен «samples», в `/samples/nginx`. Ее вспомним [отправки в docker] [ docker-push] была команда, указанная в этой статье:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Поскольку реестр контейнера Azure поддерживает такие многоуровневые репозитория пространства имен, вы можете задать область коллекциями изображений, связанных с определенного приложения или коллекцию приложений для разработки или рабочей группы. Дополнительные сведения о репозиториях в реестрах контейнеров см. в статье [Общие сведения о частных реестрах контейнеров Docker](container-registry-intro.md).

Чтобы просмотреть репозиторий:

1. Войдите в [портал Azure][portal]
1. Выберите **реестра контейнера Azure** на который отправлена Nginx изображения
1. Выберите **репозиториев** для просмотра списка репозиториев, содержащие изображения в реестре
1. Выберите репозиторий для просмотра тегов изображений в пределах этого репозитория

Например, если отправлена изображение Nginx, как указано в [по запросу и принудительные изображение](container-registry-get-started-docker-cli.md), вы увидите нечто похожее на:

![Репозитории на портале](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знаете основы Просмотр и работа с хранилищами на портале, попробуйте использовать реестр контейнера Azure с помощью [контейнера службы Azure (AKS)](../aks/tutorial-kubernetes-prepare-app.md) кластера.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
