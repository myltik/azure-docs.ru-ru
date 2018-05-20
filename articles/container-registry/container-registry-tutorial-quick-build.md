---
title: Руководство. Создание образов контейнера в облаке с помощью службы "Сборка Реестра контейнеров Azure"
description: В этом руководстве описывается, как создать образ контейнеров Docker в Azure с помощью службы "Сборка Реестра контейнеров Azure" ("Сборка ACR"), а затем развернуть его в службу "Экземпляры контейнеров Azure".
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0259f861016716ce2fdd10b866008cf5fcc305b4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Руководство. Создание образов контейнера в облаке с помощью службы "Сборка Реестра контейнеров Azure"

**Сборка ACR** — это набор компонентов в Реестре контейнеров Azure, предоставляющий упрощенные и эффективные сборки образов контейнера Docker в Azure. В этой статье вы узнаете, как использовать функцию *быстрой сборки* решения "Сборка ACR". Функция "Быстрая сборка" расширяет внутренний цикл разработки в облако, предоставляя успешную проверку сборки и автоматическую отправку успешно созданных образов в реестр контейнеров. Образы изначально создаются в облаке, ближе к вашему реестру, что обеспечивает быстрое развертывание.

Все знания о файле Dockerfile можно использовать в решении "Сборка ACR". Вам не нужно менять файлы Dockerfile для выполнения сборки в облаке с помощью решения "Сборка ACR", а только выполняемые команды.

В этом руководстве, которое является первой частью серии, вы:

> [!div class="checklist"]
> * получите исходный код примера приложения;
> * создадите образ контейнера в Azure;
> * Развертывание контейнера в службе "Экземпляры контейнеров Azure"

В последующих руководствах вы узнаете, как использовать задачи сборки решения "Сборка ACR" для автоматической сборки образов контейнеров при фиксации кода и обновлении базового образа.

> [!IMPORTANT]
> Решение "Сборка ACR" в настоящее время доступно в режиме предварительной версии и поддерживается только реестрами контейнеров Azure в регионах **Восточная часть США** (eastus) и **Западная Европа** (westeurope). Предварительные версии предоставляются только в том случае, если вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы хотите использовать Azure CLI локально, установите Azure CLI версии **2.0.32** или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо установить или обновить CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0][azure-cli].

## <a name="prerequisites"></a>предварительным требованиям

### <a name="github-account"></a>Учетная запись GitHub

Создайте учетную запись на сайте https://github.com, если у вас ее еще нет. В этой серии руководств репозиторий GitHub используется для демонстрации автоматического создания образов в решении "Сборка ACR".

### <a name="fork-sample-repository"></a>Создание вилки примера репозитория

Затем с помощью пользовательского интерфейса GitHub создайте вилку примера хранилища в своей учетной записи GitHub. В этом руководстве создается образ контейнера из источника в репозитории, а в следующем руководстве выполняется отправка фиксации в вилку репозитория для запуска автоматической сборки.

Создайте вилку этого репозитория: https://github.com/Azure-Samples/acr-build-helloworld-node.

![Снимок экрана с выделенной кнопкой "Fork" (Создать вилку) в GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Клонирование вилки

После создания вилки репозитория клонируйте свою вилку и войдите в каталог, содержащий локальный клон.

Клонируйте репозиторий с помощью `git`, замените **\<your-github-username\>** своим именем пользователя GitHub:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Введите каталог, содержащий исходный код:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Оболочка Bash

Команды в данной серии руководств отформатированы для оболочки Bash. Если вы предпочитаете использовать PowerShell, командную строку или другую оболочку, может потребоваться скорректировать продолжение строки и формат переменной среды соответствующим образом.

## <a name="build-in-azure-with-acr-build"></a>Сборка в Azure с использованием решения "Сборка ACR"

Теперь, когда исходный код извлечен на компьютер, выполните следующие действия, чтобы создать реестр контейнеров и образ контейнера с помощью решения "Сборка ACR".

Чтобы упростить выполнение примеров команд, руководства из этой серии используют переменные среды оболочки. Выполните следующую команду, чтобы задать переменную `ACR_NAME`. Замените **\<registry-name\>** уникальным именем нового реестра контейнеров. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов. Другие ресурсы, создаваемые в руководстве, основаны на этом имени, поэтому вам требуется изменить только первую переменную.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Теперь, когда переменная среды реестра контейнеров заполнена, можно копировать оставшиеся команды и вставлять их в руководство без изменения каких-либо значений. Для создания группы ресурсов и реестра контейнеров выполните следующие команды:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Теперь, когда у вас есть реестр, используйте решение "Сборка ACR" для создания образа контейнера из примера кода. Выполните команду [az acr build][az-acr-build], чтобы запустить *быструю сборку*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

Результат выполнения команды [az acr build][az-acr-build] будет аналогичен следующему примеру. Вы можете просмотреть сведения об отправке исходного кода ("контекста") в Azure и дополнительные сведения об операции `docker build`, которую решение "Сборка ACR" выполняет в облаке. Так как решение "Сборка ACR" использует `docker build` для создания образов, чтобы начать немедленно использовать это решение, изменений в файлах Dockerfiles не требуется.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (41.042 KiB) to ACR
Queued a build with ID: eastus1
Sending build context to Docker daemon  191.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
605ce1bd3f31: Pulling fs layer
f10758dcda1f: Pulling fs layer
4cbe43d669e5: Pulling fs layer

[...]

Removing intermediate container 2dbac02fb0e6
 ---> 670bbc77128d
Step 4/5 : EXPOSE 80
 ---> Running in 1d09ee337a47
Removing intermediate container 1d09ee337a47
 ---> f0739d333913
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1f019c4e4b24
Removing intermediate container 1f019c4e4b24
fbd7c8b9c17e: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
7d7224b439b3: Pushed
9dfa40a0da3b: Pushed
fbd7c8b9c17e: Pushed
26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1 size: 1367
time="2018-04-18T18:28:29Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1"]"
time="2018-04-18T18:28:30Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: eastus1 was successful after 38.116951381s
```

В конце выходных данных решения "Сборка ACR" отображаются зависимости, обнаруженные для образа. Таким образом решение может автоматизировать создание образов при обновлении базовых образов, например, когда базовый образ обновляется в связи с установкой исправлений ОС или платформы. Вы узнаете о том, как решение "Сборка ACR" поддерживает обновления базовых образов, позже в этом цикле руководств.

## <a name="deploy-to-azure-container-instances"></a>Развертывание в службу "Экземпляры контейнеров Azure"

Решение "Сборка ACR" по умолчанию автоматически отправляет успешно созданные образы в реестр, позволяя их сразу развертывать из реестра.

В этом разделе создается Azure Key Vault и субъект-служба, после чего в службу "Экземпляры контейнеров Azure" развертывается контейнер с использованием учетных данных субъекта-службы.

### <a name="configure-registry-authentication"></a>Настройка проверки подлинности в реестре

Все производственные сценарии должны использовать [субъекты-службы][service-principal-auth] для получения доступа к реестру контейнеров Azure. Субъекты-службы позволяют вам предоставлять контроль доступа на основе ролей к образам вашего контейнера. Например, вы можете настроить субъект-службу с доступом только на извлечение к реестру.

#### <a name="create-key-vault"></a>Создание хранилища ключей

Если у вас еще нет хранилища в [Azure Key Vault](/azure/key-vault/), создайте его с помощью Azure CLI, используя следующие команды.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Создание учетной записи субъекта-службы и сохранение учетных данных

Теперь вам нужно создать субъект-службу и сохранить его учетные данные в хранилище ключей.

Используйте команду [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] для создания субъекта-службы и [az keyvault secret set][az-keyvault-secret-set] для сохранения **пароля** субъекта-службы в хранилище:

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

Аргумент `--role` в предыдущей команде настраивает субъект-службу с ролью *читатель*, которая предоставляет доступ только для извлечения к реестру. Чтобы предоставить доступ для отправки и извлечения данных, измените аргумент `--role` на *участник*.

Затем сохраните в хранилище *appId* субъекта-службы, который является **именем пользователя**, переданным в реестр контейнеров Azure для проверки подлинности:

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Вы создали хранилище ключей Azure и сохранили в нем два секрета:

* `$ACR_NAME-pull-usr`: идентификатор субъекта-службы для использования в качестве **имени пользователя** реестра контейнеров.
* `$ACR_NAME-pull-pwd`: пароль субъекта-службы для использования в качестве **пароля** реестра контейнеров.

Теперь вы можете ссылаться на эти секреты по имени, когда вы или ваши приложения и службы извлекают образы из реестра.

### <a name="deploy-container-with-azure-cli"></a>Развертывание контейнера с помощью Azure CLI

Теперь, когда учетные данные субъекта-службы хранятся в секретах Аzure Key Vault, ваши приложения и службы могут использовать их для доступа к вашему частному реестру.

Выполните следующую команду [az container create][az-container-create], чтобы развернуть экземпляр контейнера. Команда использует учетные данные субъекта-службы, хранящиеся в Azure Key Vault, для проверки подлинности в вашем реестре контейнеров.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

Значение `--dns-name-label` должно быть уникальным в пределах Azure, поэтому предыдущая команда добавляет имя реестра контейнеров к метке DNS-имени контейнера. В выходных данных команды содержится полное доменное имя (FQDN) контейнера, например:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Запишите имя FQDN контейнера. Оно будет использоваться в следующем разделе.

### <a name="verify-deployment"></a>Проверка развертывания

Для контроля процесса запуска контейнера используйте команду [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

Выходные данные `az container attach` сначала отображают состояние контейнера, когда он извлекает образ и запускается, а затем связывает STDOUT и STDERR вашей локальной консоли с аналогичным содержимым контейнера.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

После появления `Server running at http://localhost:80` перейдите к FQDN контейнера в браузере, чтобы просмотреть работающее приложение:

![Снимок экрана примера приложения, отображаемого в браузере][quick-build-02-browser]

Чтобы окончательно отсоединить консоль от контейнера, нажмите клавиши `Control+C`.

## <a name="clean-up-resources"></a>Очистка ресурсов

Остановите экземпляр контейнера с помощью команды [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Чтобы удалить *все* ресурсы, созданные в этом руководстве, включая реестр контейнеров, хранилище ключей и субъект-службу, выполните следующие команды. Однако эти ресурсы используются в [следующем руководстве](container-registry-tutorial-build-task.md) цикла, поэтому их можно оставить, если вы хотите перейти напрямую к следующему руководству.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы протестировали внутренний цикл с быстрой сборкой, настройте **задачу сборки** для активации сборок образов контейнера при фиксации исходного кода в репозитории Git:

> [!div class="nextstepaction"]
> [Руководство по автоматической сборке образов контейнера с помощью решения "Сборка реестра контейнеров Azure"](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
