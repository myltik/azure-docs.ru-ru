---
title: Репозитории Реестра контейнеров Azure на портале Azure
description: Узнайте, как просмотреть список репозиториев Реестра контейнеров Azure на портале Azure.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 171593483fc94c1c67013ab520b0085ca98f3a82
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33768334"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Просмотр списка репозиториев реестра контейнеров на портале Azure

Реестр контейнеров Azure позволяет хранить образы контейнеров Docker в репозиториях. Это позволяет хранить группы образов (версии образов) в изолированных средах. Эти репозитории можно указать при отправке образов в реестр и просмотреть их содержимое на портале Azure.

## <a name="prerequisites"></a>предварительным требованиям

* **Реестр контейнеров.** Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI.** Установите интерфейс командной строки [Docker][docker-install] на локальном компьютере.
* **Образ контейнера.** Отправьте образ в реестр контейнеров. Рекомендации о том, как это сделать, см. в статье [Отправка первого образа в частный реестр контейнеров Docker с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Просмотр списка репозиториев на портале Azure

На портале Azure можно просмотреть список репозиториев с образами, а также теги образов.

Если вы следовали инструкциям из статьи [Отправка первого образа в частный реестр контейнеров Docker с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md), в реестре контейнеров должен храниться образ Nginx (если вы не удалили его). При работе с этой статьей вы отметили образ тегом и указали пространство имен samples (`/samples/nginx`). В этой статье использовалась такая команда [docker push][docker-push]:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Реестр контейнеров Azure поддерживает такие многоуровневые пространства имен репозиториев. Благодаря этому вы можете задать область коллекциям образов, связанным с определенным приложением или коллекцией приложений, для разных рабочих групп и групп разработки. Дополнительные сведения о репозиториях в реестрах контейнеров см. в статье [Общие сведения о частных реестрах контейнеров Docker](container-registry-intro.md).

Чтобы просмотреть список репозиториев, сделайте следующее:

1. Войдите на [портал Azure][portal].
1. Выберите **Реестр контейнеров Azure**, в который вы отправили образ Nginx.
1. Выберите **Репозитории**, чтобы просмотреть список репозиториев с образами в реестре.
1. Выберите репозиторий, чтобы просмотреть теги сохраненных в нем образов.

Например, если вы следовали инструкциям из статьи [Отправка первого образа в частный реестр контейнеров Docker с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md), должен отобразиться примерно такой результат:

![Репозитории на портале](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы знаете, как просматривать список репозиториев на портале и работать с ними, попробуйте использовать Реестр контейнеров Azure с кластером [службы Azure Kubernetes (AKS)](../aks/tutorial-kubernetes-prepare-app.md).

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
