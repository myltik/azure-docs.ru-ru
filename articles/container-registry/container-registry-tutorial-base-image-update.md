---
title: Руководство. Автоматизация сборок образов контейнера при обновлении базового образа с помощью службы "Сборка Реестра контейнеров Azure"
description: В этом руководстве вы узнаете, как настроить задачу сборки, чтобы автоматически активировать сборки образов контейнера в облаке при обновлении базового образа.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 531aeaacf0bd70521d70afb45d141fc3296ebb04
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Руководство. Автоматизация сборок образа при обновлении базового образа с помощью службы "Сборка Реестра контейнеров Azure"

Решение "Сборка ACR" поддерживает автоматическое выполнение сборки при обновлении базового образа контейнера, например, когда вы исправляете исполняющую среду или ОС в одном из базовых образов. Из этого руководства вы узнаете, как создать задачу сборки в решении сборки ACR, которая запускает сборку в облаке, если базовый образ контейнера был отправлен в ваш реестр.

В этом руководстве (последняя часть в серии) вы выполните такие задачи:

> [!div class="checklist"]
> * создание базового образа;
> * создание задачи сборки образа приложения;
> * обновление базового образа, чтобы запустить сборку образа приложения;
> * отображение активированной сборки;
> * проверка обновленного образа приложения.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы хотите использовать Azure CLI локально, установите Azure CLI версии **2.0.32** или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>предварительным требованиям

### <a name="complete-previous-tutorials"></a>Завершение работы с предыдущими руководствами

Приступая к работе с этим руководством, предполагается, что вы уже выполнили шаги, описанные в первых двух руководствах в серии, и выполнили действия ниже.

* Создание реестра контейнеров Azure
* Создание вилки примера репозитория
* Клонировали пример репозитория.
* Создали личный маркер доступа GitHub.

Если вы еще этого не сделали, завершите работу с первыми двумя руководствами, а затем вернитесь к этому руководству:

[Создание образов контейнера в облаке с помощью службы "Сборка реестра контейнеров Azure"](container-registry-tutorial-quick-build.md)

[Automate container image builds with Azure Container Registry Build](container-registry-tutorial-build-task.md) (Автоматизация сборок образов контейнера с помощью службы "Сборка Реестра контейнеров Azure")

### <a name="configure-environment"></a>Настройка среды

Заполните эти переменные среды оболочки значениями, подходящими для вашей среды. Это требование не является обязательным, но оно упрощает выполнение многолинейных команд Azure CLI в этом руководстве. Если вы не заполните эти значения, нужно будет вручную заменять каждое значение, где бы оно не появлялось в примерах команд.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Базовые образы

Файлы Docker, определяющие большинство образов контейнера, указывают родительский образ, на котором они основаны и который часто называют их *базовым образом*. Базовые образы обычно содержат операционную систему, например [Alpine Linux][base-alpine] или [Windows Nano Server][base-windows], к которой применяются остальные слои контейнера. Они также могут включать в себя платформы приложений, например [Node.js][base-node] или [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Обновления базового образа

Разработчик образов часто обновляет базовый образ, чтобы включить новые функции или улучшения в ОС или платформу в образе. Исправления безопасности — еще одна распространенная причина обновления базового образа.

При обновлении базового образа вам предлагается перестроить любые основанные на нем образы контейнера в реестре для добавления новых функций и исправлений. Решение "Сборка ACR" включает в себя возможность автоматически создавать образы при обновлении базового образа контейнера.

### <a name="base-image-update-scenario"></a>Сценарий обновления базового образа

В этом руководстве рассматривается сценарий обновления базового образа. [Пример кода][code-sample] включает два файла Docker: образ приложения и образ, который определяется как базовый. В следующих разделах вы создадите задачу сборки ACR, которая автоматически запустит сборку образа приложения, когда новая версия базового образа будет отправлена в реестр контейнеров.

[Dockerfile-app][dockerfile-app]: небольшое веб-приложение Node.js, которое преображает для просмотра статическую веб-страницу с версией Node.js, на которой она основана. Строка версии моделируется: она отображает содержимое переменной среды `NODE_VERSION`, которая определена в базовом образе.

[Dockerfile-base][dockerfile-base]: образ, который приложение `Dockerfile-app` определяет как свою базу. Сам он основан на образе [узла][base-node] и включает переменную среды `NODE_VERSION`.

В следующих разделах вы создадите задачу сборки, обновите значение `NODE_VERSION` в файле Docker базового образа, а затем используете Решение "Сборка ACR" для создания базового образа. Когда сборка ACR принудительно отправит новый базовый образ в реестр, он автоматически запустит сборку образа приложения. При желании можно запустить образ контейнера приложения локально, чтобы увидеть различные строки версии в созданных образах.

## <a name="build-base-image"></a>Сборка базового образа

Начните с создания базового образа с помощью функции *Быстрая сборка* решения "Сборка ACR". Как обсуждалось в [первом руководстве](container-registry-tutorial-quick-build.md) в серии, это позволит не только создать образ, но и отправить его в реестр контейнеров (при успешном выполнении сборки).

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Создание задачи сборки

Далее с помощью команды [az acr build-task create][az-acr-build-task-create] создайте задачу сборки:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

Эта задача сборки похожа на задачу, созданную в рамках работы с [предыдущим руководством](container-registry-tutorial-build-task.md). Она инструктирует решение "Сборка ACR" активировать сборку образа, когда фиксации отправляются в репозиторий, указанный с помощью `--context`.

Отличается поведение задачи, так как она также запускает сборку образа при обновлении *базового образа*. Файл Docker, указанный аргументом `--file` приложения [Dockerfile-app ][dockerfile-app], поддерживает указание образа из того же реестра, где находится базовый:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

При запуске задачи сборки решение "Сборка ACR" обнаруживает зависимости образа. Если базовый образ, указанный в инструкции `FROM`, находится в одном реестре, он добавляет перехватчик, чтобы гарантировать, что он будет перестроен в любое время при обновлении его базы.

> [!NOTE]
> На время действия предварительной версии служба "Сборка ACR" поддерживает запуск производной сборки образа, только если базовый образ и образ, ссылающийся на него, находятся в одном реестре контейнеров Azure.

## <a name="build-application-container"></a>Контейнер приложения сборки

Чтобы включить решение "Сборка ACR" для определения и отслеживания зависимостей образа контейнера, включая его базовый образ, **нужно** сначала запустить задачу сборки **хотя бы один раз**.

Выполните команду [az acr build-task run][az-acr-build-task-run], чтобы вручную запустить задачу сборки и создать образ приложения:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

По завершении сборки обратите внимание на **код сборки** (например, "aa6"), если нужно выполнить следующий необязательный шаг.

### <a name="optional-run-application-container-locally"></a>Необязательно. Локальный запуск контейнера приложения

Если вы работаете локально (а не в облачной среде Cloud Shell) и у вас установлен Docker, запустите контейнер, чтобы увидеть приложение, отображаемое в веб-браузере, а затем восстановите его базовый образ. Если вы используете Cloud Shell, пропустите этот раздел (Cloud Shell не поддерживает `az acr login` или `docker run`).

Сначала войдите в реестр контейнеров с помощью команды [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Затем локально запустите контейнер с помощью `docker run`. Замените код **\<build-id\>** на код сборки, полученный в выходных данных на предыдущем шаге (например, "aa6").

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Перейдите по адресу http://localhost:8080 в браузере, и вы увидите номер версии Node.js, преобразованный для просмотра на веб-странице, аналогичный приведенному ниже. На более позднем этапе вы активируете версию, добавив "a" в строку версии.

![Снимок экрана примера приложения, отображаемого в браузере][base-update-01]

## <a name="list-builds"></a>Вывод списка сборок

Далее вывести список сборок, которые решение "Сборка ACR" выполнило для реестра:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Если вы закончили работу с предыдущим руководством (и не удалили реестр), вы должны увидеть выходные данные, похожие на приведенные ниже. Обратите внимание на количество сборок и последний идентификатор сборки, чтобы сравнить результаты после обновления базового образа в следующем разделе.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa6         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual      2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="update-base-image"></a>Обновление базового образа

Здесь вы моделируете исправление платформы в базовом образе. Измените файл **Dockerfile-base**, добавив "a" после номера версии, определенного в `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Запустите функцию быстрой сборки в решение "Сборка ACR", чтобы создать измененный базовый образ. Обратите внимание на **идентификатор сборки** в выходных данных.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

После завершения сборки и отправки решением "Сборка ACR" нового базового образа в реестр оно запустит сборку образа приложения. Задаче сборки ACR, созданной ранее, может потребоваться несколько минут, чтобы запустить сборку образа приложения, так как она должна обнаружить недавно выполненный и переданный базовый образ.

## <a name="list-builds"></a>Вывод списка сборок

Теперь, когда вы обновили базовый образ, получите список своих сборок снова, чтобы сравнить их со списком, полученным ранее. Если сначала выходные данные не отличаются, периодически запускайте команду, пока новая сборка не появится в списке.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Результат аналогичен приведенному ниже. Триггером для последней выполненной сборки должно быть "обновление образа", указывающее, что задача сборки была запущена с помощью функции быстрой сборки базового образа.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
aa8         buildhelloworld  Linux       Succeeded  Image Update  2018-05-10T20:09:52Z  00:00:45
aa7                          Linux       Succeeded  Manual        2018-05-10T20:09:17Z  00:00:40
aa6         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual        2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit    2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual        2018-05-10T19:10:14Z  00:00:55
```

Если вы хотите выполнить следующий необязательный шаг (запуск только что созданного контейнера), чтобы отобразился обновленный номер версии, обратите внимание на значение **кода сборки** для сборки образа, активируемой при обновлении (в предыдущих выходных данных — "aa8").

### <a name="optional-run-newly-built-image"></a>Необязательно. Запуск только что созданного образа

Если вы работаете локально (не в облачной среде Cloud Shell) и у вас установлен Docker, запустите новый образ приложения после завершения его сборки. Замените код `<build-id>` на код сборки, полученный на предыдущем шаге. Если вы используете Cloud Shell, пропустите этот раздел (Cloud Shell не поддерживает `docker run`).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Перейдите по адресу http://localhost:8081 в браузере, и вы увидите обновленный номер версии Node.js (с добавленным значением "а") на веб-странице:

![Снимок экрана примера приложения, отображаемого в браузере][base-update-02]

Важно отметить, что вы обновили **базовый** образ с помощью нового номера версии, но новая версия отображается в последнем созданном **образе приложения**. Решение "Сборка ACR" обнаружило изменение базового образа и автоматически перестроило образ приложения.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные в этой серии руководств, включая реестр контейнеров, экземпляр контейнера, хранилище ключей и субъект-службу, выполните следующие команды:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как использовать задачу сборки, чтобы автоматически активировать сборки образа контейнера при обновлении базового образа. Теперь рассмотрим аутентификацию реестра контейнеров.

> [!div class="nextstepaction"]
> [Аутентификация с помощью частного реестра контейнеров Docker](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png