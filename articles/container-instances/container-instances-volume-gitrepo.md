---
title: Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"
description: Сведения о подключении тома gitRepo для клонирования репозитория в экземпляры контейнеров
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166785"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"

Узнайте, как подключить том *gitRepo*, чтобы клонировать репозиторий в экземпляры контейнеров.

> [!NOTE]
> Подключение тома *gitRepo* сейчас поддерживается только для контейнеров Linux. Мы работаем над тем, чтобы обеспечить все функции для контейнеров Windows, но для текущей платформы есть отличия в [квотах и доступности регионов для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>Том gitRepo

Том *gitRepo* подключает каталог и клонирует в него указанный репозиторий Git при запуске контейнера. Используя том *gitRepo* в экземплярах контейнера, можно избежать добавления кода для выполнения этой задачи в приложениях.

После подключения тома *gitRepo* задайте три свойства, чтобы настроить том.

| Свойство | Обязательно | ОПИСАНИЕ |
| -------- | -------- | ----------- |
| `repository` | Yes | Полный URL-адрес (включая `http://` или `https://`) репозитория Git, который будет клонирован.|
| `directory` | Нет  | Каталог, в который необходимо клонировать репозиторий. Значение пути не должно содержать знак `..` или начинаться с него.  Если указан знак `.`, репозиторий клонируется в каталог тома. В противном случае репозиторий Git клонируется в подкаталог с указанным именем в каталоге томов. |
| `revision` | Нет  | Зафиксированный хэш версии, которая будет клонирована. Если не указан, клонируется версия `HEAD`. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Подключение тома gitRepo: Azure CLI

Чтобы подключить том gitRepo при развертывании экземпляров контейнера с помощью [Azure CLI](/cli/azure), укажите параметры `--gitrepo-url` и `--gitrepo-mount-path` в команде [az container create][az-container-create]. При необходимости можно указать каталог тома, в который будет осуществляться клонирование (`--gitrepo-dir`) и хэш фиксации редакции, которая будет клонирована (`--gitrepo-revision`).

В этом примере команда клонирует пример приложения [aci-helloworld] [ aci-helloworld] в расположение `/mnt/aci-helloworld` в экземпляре контейнера.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Чтобы убедиться, что том gitRepo подключен, запустите оболочку в контейнере с помощью команды [az container exec][az-container-exec] и выведите список каталогов.

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Подключение тома gitRepo: Resource Manager

Чтобы подключить том gitRepo при развертывании экземпляров контейнера с помощью [шаблона Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), в шаблоне сначала нужно заполнить массив `volumes` в разделе `properties` группы контейнеров. Затем для каждого контейнера в группе контейнеров, в которой нужно подключить том *gitRepo*, нужно заполнить массив `volumeMounts` в разделе `properties` определения контейнера.

Например, следующий шаблон Resource Manager создает группу контейнеров, состоящую из одного контейнера. Контейнер клонирует два репозитория GitHub, указанных блоками тома *gitRepo*. Второй том содержит дополнительные свойства, указывающие каталог для клонирования и зафиксированный хэш определенной версии.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

В результате структура каталогов двух клонированных репозиториев, определенных в предыдущем шаблоне, выглядит следующим образом.

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Пример развертывания экземпляра контейнера с помощью шаблона Azure Resource Manager см. в статье [Развертывание группы контейнеров](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Дополнительная информация

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Подключение общего файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure"](container-instances-volume-azure-files.md)
* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Подключение тома emptyDir в службе "Экземпляры контейнеров Azure")
* [Подключение тома secret в службе "Экземпляры контейнеров Azure"](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec