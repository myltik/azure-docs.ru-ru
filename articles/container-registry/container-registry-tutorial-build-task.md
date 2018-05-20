---
title: Руководство. Автоматизация сборок образов контейнера с помощью службы "Сборка Реестра контейнеров Azure"
description: Из этого руководства вы узнаете, как настроить задачу сборки, чтобы автоматически активировать сборки образов контейнера в облаке при фиксации исходного кода в репозитории Git.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4accbcb61e57d58100b6a4c06142dd3dc633f7f4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Руководство. Автоматизация сборок образов контейнера с помощью службы "Сборка Реестра контейнеров Azure"

В дополнение к функции [Быстрая сборка](container-registry-tutorial-quick-build.md), решение "Сборка ACR" поддерживает автоматические сборки образов контейнера Docker с помощью *задачи сборки*. Из этого руководства вы узнаете, как использовать Azure CLI для создания задачи сборки, которая автоматически активирует сборки образов в облаке при фиксации исходного кода в репозитории Git.

В этом руководстве, второй части серии:

> [!div class="checklist"]
> * Создание задачи сборки
> * Тестирование задачи сборки.
> * Просмотр состояния сборки.
> * Активация задачи сборки с помощью фиксации кода

В этом руководстве предполагается, что вы уже выполнили шаги в [предыдущем руководстве](container-registry-tutorial-quick-build.md). Если вы еще этого не сделали, выполните действия, описанные в разделе [Предварительные требования](container-registry-tutorial-quick-build.md#prerequisites) в предыдущем руководстве, прежде чем перед продолжить.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы хотите использовать Azure CLI локально, установите Azure CLI версии **2.0.32** или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>предварительным требованиям

### <a name="get-sample-code"></a>Получение примера кода

В этом руководстве предполагается, что вы уже выполнили шаги в [предыдущем руководстве](container-registry-tutorial-quick-build.md), создали вилку и клонировали пример репозитория. Если вы еще этого не сделали, выполните действия, описанные в разделе [Предварительные требования](container-registry-tutorial-quick-build.md#prerequisites) в предыдущем руководстве, прежде чем перед продолжить.

### <a name="container-registry"></a>Реестр контейнеров

Для выполнения заданий данного руководства необходимо иметь реестр контейнеров Azure в подписке Azure. Если вам нужен реестр, см. сведения в [предыдущем руководстве](container-registry-tutorial-quick-build.md) или в статье [Краткое руководство. Создание реестра контейнеров с использованием Azure CLI](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Задача сборки

Задача сборки определяет свойства автоматической сборки, включая расположение исходного образа контейнера и событие, которое запускает сборку. Когда происходит событие, определенное в задаче сборки, например фиксация в репозитории Git, решение "Сборка ACR" инициирует сборку образа контейнера в облаке. Затем оно по умолчанию помещает успешно созданный образ в реестр контейнеров Azure, указанный в задаче.

Решение "Сборка ACR" в настоящее время поддерживает следующие триггеры задачи сборки:

* фиксация в репозитории Git;
* обновление базового образа.

## <a name="create-a-build-task"></a>Создание задачи сборки

В этом разделе вы сначала создаете личный маркер доступа GitHub (PAT) для использования с решением "Сборка ACR". Затем вы создаете задачу сборки, которая запускает сборку при фиксации кода в вилке репозитория.

### <a name="create-a-github-personal-access-token"></a>Создание личного маркера доступа GitHub

Чтобы инициировать сборку при фиксации кода в репозитории Git, решению "Сборка ACR" требуется личный маркер доступа для доступа к репозиторию. Выполните следующие действия для создания PAT в GitHub:

1. Перейдите на страницу создания PAT на GitHub по адресу https://github.com/settings/tokens/new.
1. Введите краткое **описание** маркера, например "ACR Build Task Demo".
1. Под флажком **repo** установите флажки **repo:status** и **public_repo**.

   ![Снимок экрана со страницей создания личного маркера доступа GitHub][build-task-01-new-token]

1. Нажмите кнопку **Generate token** (Создать маркер) (может появиться запрос на подтверждение пароля).
1. Скопируйте и сохраните созданный маркер в **защищенном расположении** (вы используете этот маркер при определении задачи сборки в следующем разделе).

   ![Снимок экрана созданного личного маркера доступа на GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Создание задачи сборки

Теперь, когда вы выполнили шаги, необходимые для включения в решении "Сборка ACR" чтения состояния фиксации и создания веб-перехватчиков в репозитории, вы можете создать задачу сборки, которая запускает сборку образа контейнера при фиксации кода в репозитории.

Сначала заполните эти переменные среды оболочки значениями, подходящими для вашей среды. Это требование не является обязательным, но оно упрощает выполнение многолинейных команд Azure CLI в этом руководстве. Если вы не заполните эти значения, нужно будет вручную заменять каждое значение, где бы оно не появлялось в примерах команд.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Теперь создайте задачу сборки, выполнив следующую команду [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

Эта задача сборки указывает, что каждый раз при фиксации кода в *главной* ветви репозитория, указанной в `--context`, решение "Сборка ACR" создает образ контейнера из кода в этой ветви. Аргумент `--image` указывает параметризованное значение `{{.Build.ID}}` для части с версией в теге образа, гарантируя, что образ коррелирует с конкретной сборкой и имеет уникальный тег.

Выходные данные успешно выполненной команды [az acr build-task create][az-acr-build-task-create] имеют следующий вид:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>Тестирование задачи сборки

Теперь у вас есть задача сборки, которая определяет сборку. Чтобы протестировать определение сборки, запустите сборку вручную, выполнив команду [​​az acr build-task run][az-acr-build-task-run]:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

По умолчанию при выполнении команда `az acr build-task run` передает выходные данные журнала в консоль. Здесь выходные данные показывают, что сборка **aa2** поставлена в очередь и создана.

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>Просмотр состояния сборки

Иногда может быть полезно просмотреть состояние текущей сборки, которую вы не запускали вручную. Например, при устранении неполадок в сборках, активируемых при фиксации исходного кода. В этом разделе вы запустите сборку вручную, но отключите поведение по умолчанию для потоковой передачи журнала сборки в консоль. Затем используете команду `az acr build-task logs` ​​для отслеживания текущей сборки.

Сначала запустите сборку вручную, как вы делали ранее, но укажите аргумент `--no-logs`, чтобы отключить передачу журнала в консоль:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

Затем используйте команду `az build-task logs`, чтобы просмотреть журнал запущенной сборки:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Журнал текущей запущенной сборки передается в консоль и должен выглядеть примерно так, как показано ниже (сокращенный фрагмент):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>Активация сборки с помощью фиксации

Теперь, когда вы протестировали задачу сборки, выполнив ее вручную, запустите ее автоматически, изменив исходный код.

Сначала перейдите в каталог, содержащий локальный клон вашего [репозитория][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Затем выполните следующие команды для создания, фиксации и передачи нового файла в вилку вашего репозитория GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Может потребоваться предоставить ваши учетные данные GitHub при выполнении команды `git push`. Введите свое имя пользователя GitHub и укажите созданный ранее личный маркер доступа в качестве пароля.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

После того, как вы передали фиксацию в ваш репозиторий, веб-перехватчик, созданный службой "Сборка ACR", запускается и выполняет сборку в Реестре контейнеров Azure. Отобразите журналы текущей сборки, чтобы проверить ее и отслеживать ход ее выполнения:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

Выходные данные выглядят следующим образом. Они показывают выполняемую (или последнюю выполненную) сборку:

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>Вывод списка сборок

Чтобы просмотреть список сборок, которые решение "Сборка ACR" выполнило в реестре, запустите команду [​​az acr build-task list-builds][az-acr-build-task-list-builds]:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Результат этой команды должен выглядеть примерно следующим образом: Отобразятся сборки, созданные решением "Сборка ACR", а также фраза "Git Commit" в столбце TRIGGER для самой последней сборки:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как использовать задачу сборки, чтобы автоматически активировать сборки образов контейнера в Azure при фиксации исходного кода в репозитории Git. Перейдите к следующему руководству, чтобы узнать, как создавать задачи сборки, которые активируют сборку при обновлении базового образа контейнера.

> [!div class="nextstepaction"]
> [Руководство по автоматизации сборки образа при обновлении базового образа с помощью сборки реестра контейнеров Azure](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
