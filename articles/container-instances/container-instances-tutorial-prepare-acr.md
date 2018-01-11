---
title: "Руководство по службе \"Экземпляры контейнеров Azure\". Подготовка реестра контейнеров Azure"
description: "Azure экземпляры контейнером учебника часть 2 из 3 - Подготовка реестра контейнера Azure"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c0aad1f9bbaac9a456b34f75633faba92f57f498
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Развертывание реестра контейнеров Azure и его использование

Это вторая часть руководства, состоящего из трех частей. В [ранее](container-instances-tutorial-prepare-app.md), был создан образ контейнера простого веб-приложения, написанные на [Node.js][nodejs]. В этом руководстве мы поместим образ в реестр контейнеров Azure. Если вы еще не создали образ контейнера, вернитесь к первой части этой серии — [руководству по созданию образа контейнера](container-instances-tutorial-prepare-app.md).

Реестр контейнера Azure является Azure, частного реестра образов контейнеров Docker. В этом учебнике рассматриваются по развертыванию экземпляра реестра контейнера Azure и принудительной отправки образа контейнера.

В этой статье часть двух рядов, то:

> [!div class="checklist"]
> * Разверните экземпляр реестра контейнера Azure
> * Тег образ контейнера для реестра контейнера Azure
> * Загрузить образ в системный реестр

В следующей статье последний учебник в ряду развертывании контейнера из системного реестра закрытый экземпляров контейнера Azure.

## <a name="before-you-begin"></a>Перед началом работы

Учебником, что вы используете Azure CLI версии 2.0.23 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить см. в разделе [установить CLI Azure 2.0][azure-cli-install].

Для работы с этим учебником требуется Docker среды разработки, установленные локально. Docker содержит пакеты, которые легко настроить Docker для какого-либо [Mac][docker-mac], [Windows][docker-windows], или [Linux] [ docker-linux] системы.

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Среда разработки Azure CLI и Docker необходимо установить на локальном компьютере для выполнения заданий данного учебника.

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

При развертывании реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. В этом примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создать контейнер Azure реестр с помощью [создать az acr] [ az-acr-create] команды. Имя реестра контейнеров **должно быть уникальным** в пределах Azure и содержать от 5 до 50 буквенно-цифровых знаков. Замените `<acrName>` уникальным именем реестра.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Например, чтобы создать реестр контейнеров Azure *mycontainerregistry082*, выполните следующую команду.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

В этом руководстве будет использоваться имя `<acrName>` как заполнитель выбранного имени реестра контейнеров.

## <a name="container-registry-login"></a>Вход в реестр контейнеров

Войдите на свой экземпляр реестра контейнера Azure перед принудительной отправки изображений. Используйте [входа acr az] [ az-acr-login] команду, чтобы завершить операцию. Необходимо указать уникальное имя, заданное для контейнера реестра при его создании.

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает сообщение `Login Succeeded`.

## <a name="tag-container-image"></a>Добавление тега к образу контейнера

Чтобы развернуть образ контейнера из частного реестра, должна быть помечена изображение с `loginServer` имя реестра.

Чтобы просмотреть список текущего изображения, используйте [образов docker] [ docker-images] команды.

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Чтобы получить имя loginServer, запустите [Показать acr az] [ az-acr-show] команды. Замените `<acrName>` именем реестра контейнеров.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Выходные данные примера:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Добавьте к образу *aci-tutorial-app* тег loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег указывает номер версии образа. Замените `<acrLoginServer>` с результатом [Показать acr az] [ az-acr-show] команды только что выполненной.

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

Принудительной *aci учебника приложения* изображения в реестре с [отправки в docker] [ docker-push] команды. Замените `<acrLoginServer>` полным именем сервера входа, полученным на предыдущем шаге.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push` Операции займет несколько секунд, чтобы несколько минут в зависимости от подключения к Интернету, и вывод команды будет примерно следующим:

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

Чтобы вернуть список образов, которые передаются в системный реестр контейнера Azure, используйте [списка репозитория acr az] [ az-acr-repository-list] команды. Обновите команду, используя имя реестра контейнеров.

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```azurecli
Result
----------------
aci-tutorial-app
```

И затем, чтобы увидеть теги для конкретных образа, использовать [acr репозитория az show теги] [ az-acr-repository-show-tags] команды.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Выходные данные:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике подготовлен в реестре контейнера Azure для использования с экземплярами Azure контейнер и передается образ контейнера в реестр. Были выполнены следующие действия:

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
