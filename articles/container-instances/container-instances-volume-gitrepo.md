---
title: "Подключение тома gitRepo в службе \"Экземпляры контейнеров Azure\""
description: "Сведения о подключении тома gitRepo для клонирования репозитория в экземпляры контейнеров"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
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

## <a name="mount-a-gitrepo-volume"></a>Подключение тома gitRepo

Чтобы подключить том *gitRepo* в экземпляре контейнера, необходимо выполнить развертывание с помощью [шаблона Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Сначала заполните массив `volumes` в разделе `properties` группы контейнеров шаблона. Затем для каждого контейнера в группе контейнеров, в которой нужно подключить том *gitRepo*, заполните массив `volumeMounts` в разделе `properties` определения контейнера.

Например, следующий шаблон Resource Manager создает группу контейнеров, состоящую из одного контейнера. Контейнер клонирует два репозитория GitHub, указанных блоками тома *gitRepo*. Второй том содержит дополнительные свойства, указывающие каталог для клонирования и зафиксированный хэш определенной версии.

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
