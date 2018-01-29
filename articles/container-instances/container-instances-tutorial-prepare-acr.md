---
title: "Руководство по службе \"Экземпляры контейнеров Azure\". Подготовка реестра контейнеров Azure"
description: "Вторая часть руководства по службе \"Экземпляры контейнеров Azure\". Подготовка реестра контейнеров Azure"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Развертывание реестра контейнеров Azure и его использование

Это вторая часть руководства, состоящего из трех частей. На [предыдущем шаге](container-instances-tutorial-prepare-app.md) мы создали образ контейнера для простого веб-приложения, написанного на [Node.js][nodejs]. В этом руководстве мы поместим образ в реестр контейнеров Azure. Если вы еще не создали образ контейнера, вернитесь к первой части этой серии — [руководству по созданию образа контейнера](container-instances-tutorial-prepare-app.md).

Реестр контейнеров Azure — это частный реестр на базе Azure для образов контейнеров Docker. В этом руководстве описывается развертывание экземпляра реестра контейнеров Azure и отправка в него образа контейнера.

В этой статье (второй части цикла) вы:

> [!div class="checklist"]
> * развернете экземпляр реестра контейнеров Azure;
> * добавите образ контейнера для помещения в реестр контейнеров Azure;
> * отправите образ в реестр.

В следующей статье (последнем руководстве цикла) вы развернете контейнер из частного реестра в службе "Экземпляры контейнеров Azure".

## <a name="before-you-begin"></a>Перед началом работы

Для этого руководства требуется Azure CLI 2.0.23 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

Для работы с этим руководством требуется среда разработки Docker, установленная локально. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе [Mac][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Для работы с этим руководством вам необходимо установить среду разработки Azure CLI и Docker на свой локальный компьютер.

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

При развертывании реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. В этом примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте реестр контейнеров Azure с помощью команды[az acr create][az-acr-create]. Имя реестра контейнеров должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых знаков. Замените `<acrName>` уникальным именем реестра.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Например, чтобы создать реестр контейнеров Azure *mycontainerregistry082*, выполните следующую команду.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

В этом руководстве будет использоваться имя `<acrName>` как заполнитель выбранного имени реестра контейнеров.

## <a name="container-registry-login"></a>Вход в реестр контейнеров

Войдите в экземпляр реестра контейнеров Azure, прежде чем передавать в него образы. Используйте команду [az acr login][az-acr-login], чтобы выполнить операцию. Укажите уникальное имя реестра контейнеров, заданное при его создании.

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает сообщение `Login Succeeded`.

## <a name="tag-container-image"></a>Добавление тега к образу контейнера

Чтобы развернуть образ контейнера из частного реестра, необходимо добавить к этому образу тег с именем `loginServer` реестра.

Чтобы просмотреть список сохраненных образов, используйте команду [docker images][docker-images].

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Чтобы получить имя loginServer, выполните команду [az acr show][az-acr-show]. Замените `<acrName>` именем реестра контейнеров.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Выходные данные примера:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Добавьте к образу *aci-tutorial-app* тег loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег указывает номер версии образа. Замените `<acrLoginServer>` результатом только что выполненной команды [az acr show][az-acr-show].

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

После пометки выполните команду `docker images` для проверки операции.

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Передача образа в реестр контейнеров Azure

Отправьте образ *aci-tutorial-app* в реестр командой [docker push][docker-push]. Замените `<acrLoginServer>` полным именем сервера входа, полученным на предыдущем шаге.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Операция `push` займет от нескольких секунд до нескольких минут, в зависимости от подключения к Интернету. Ее выходные данные будут иметь следующий вид.

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Получение списка образов в реестре контейнеров Azure

Чтобы получить список образов, отправленных в реестр контейнеров Azure, выполните команду [az acr repository list][az-acr-repository-list]. Обновите команду, используя имя реестра контейнеров.

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```azurecli
Result
----------------
aci-tutorial-app
```

Чтобы увидеть теги для конкретного образа, используйте команду [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Выходные данные:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы подготовили реестр контейнеров Azure к использованию со службой "Экземпляры контейнеров Azure", а также передали образ контейнера в реестр. Были выполнены следующие действия:

> [!div class="checklist"]
> * развертывание экземпляра реестра контейнеров Azure;
> * добавление тегов к образу контейнера для помещения в реестр контейнеров Azure;
> * отправка образа в реестр контейнеров Azure.

Перейдите к следующему руководству, чтобы узнать о развертывании контейнера в Azure с помощью службы "Экземпляры контейнеров Azure".

> [!div class="nextstepaction"]
> [Deploy a container to Azure Container Instances](./container-instances-tutorial-deploy-app.md) (Развертывание контейнера в службе "Экземпляры контейнеров Azure")

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
