---
title: Краткое руководство. Создание первого контейнера в службе "Экземпляры контейнеров Azure"
description: В этом кратком руководстве вы развернете контейнер в службе "Экземпляры контейнеров Azure" при помощи Azure CLI.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: b68468cd8174d658d04d8e67433a8f18884493bd
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Краткое руководство. Создание первого контейнера в службе "Экземпляры контейнеров Azure"

Служба "Экземпляры контейнеров Azure" упрощает создание контейнеров Docker и управление ими в Azure, избавляя от необходимости подготавливать виртуальные машины или применять службу более высокого уровня. В этом кратком руководстве вы создадите контейнер в Azure и выставите его в Интернет с полным доменным именем (FQDN). Эта операция выполняется при помощи одной команды. Через несколько секунд в браузере отобразится следующее.

![Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][aci-app-browser]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][azure-account], прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для выполнения инструкций этого краткого руководства можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.27 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Служба "Экземпляры контейнеров Azure", как и все ресурсы Azure, должна быть помещена в группу ресурсов — логическую коллекцию, в которой выполняется развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Создание контейнера

Чтобы создать контейнер, нужно указать его имя, образ Docker и группу ресурсов Azure в команде [az container create][az-container-create]. Либо же можно предоставить контейнер в Интернете, указав метку DNS-имени. В ходе работы с этим руководством вы развернете контейнер, содержащий небольшое веб-приложение, написанное на языке [Node.js][node-js].

Выполните следующую команду, чтобы запустить экземпляр контейнера. Значение `--dns-name-label` должно быть уникальным в пределах региона Azure, в котором создан экземпляр. Возможно, потребуется изменить это значение, чтобы гарантировать уникальность.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Через несколько секунд вы должны получить ответ на запрос. Изначально контейнер находится в состоянии **Создание**, но через несколько секунд он запустится. Вы можете проверить состояние, выполнив команду [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

При выполнении команды отображается полное доменное имя (FQDN) и состояние подготовки контейнера:

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Когда контейнер перейдет в состояние **успешного запуска**, перейдите к нему в браузере, указав полное доменное имя:

![Снимок экрана браузера: приложение, выполняющееся в экземпляре контейнера Azure][aci-app-browser]

## <a name="pull-the-container-logs"></a>Извлечение журналов контейнера

Просмотр журналов для экземпляра контейнера помогает устранять неполадки с этим контейнером или запущенными в нем приложениями.

Извлеките журналы контейнера с помощью команды [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

В выходных данных будут содержаться журналы для контейнера, а также должны отобразиться запросы HTTP GET, созданные при просмотре приложения в браузере.

```console
$ az container logs --resource-group myResourceGroup -n mycontainer
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="attach-output-streams"></a>Присоединение потоков вывода

Помимо создания заключительных фрагментов журналов, можно присоединять локальные стандартные потоки вывода и стандартные потоки для вывода сообщений об ошибках контейнера.

Сначала выполните команду [az container attach][az-container-attach], чтобы присоединить локальную консоль к потокам вывода контейнера:

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

После присоединения несколько раз обновите страницу в браузере, чтобы создать некоторые дополнительные выходные данные. Наконец, отсоедините консоль с помощью `Control+C`. Вы должны увидеть результат, аналогичный приведенному ниже:

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с контейнером удалите его с помощью команды [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Чтобы проверить, удален ли контейнер, выполните команду [az container list](/cli/azure/container#az_container_list).

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Контейнер **mycontainer** не должен отображаться в выходных данных этой команды. Если у вас нет других контейнеров в группе ресурсов, выходные данные будут отсутствовать.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали экземпляр контейнера Azure из образа, размещенного в общедоступном реестре Docker Hub. Если вы хотите самостоятельно скомпилировать образ контейнера и развернуть его в службе "Экземпляры контейнеров Azure" через частный реестр контейнеров Azure, переходите к руководству по использованию службы "Экземпляры контейнеров Azure".

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](./container-instances-tutorial-prepare-app.md)

Чтобы проверить параметры запуска контейнеров в системе оркестрации Azure, см. краткие руководства по [Service Fabric][service-fabric] или [службе Kubernetes Azure (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-container-list]: /cli/azure/container#az_container_list
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
