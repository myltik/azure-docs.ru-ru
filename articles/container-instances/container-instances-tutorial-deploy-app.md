---
title: "Руководство по службе \"Экземпляры контейнеров Azure\". Развертывание приложения"
description: "Руководство по службе \"Экземпляры контейнеров Azure\". Развертывание приложения"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5222c43fe381c93527ec6c8b927d723f75dd58f9
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Развертывание контейнера в службе "Экземпляры контейнеров Azure"

Эта последняя часть руководства, состоящего из трех частей. В предыдущих частях мы [создали образ контейнера](container-instances-tutorial-prepare-app.md), который затем [передали в реестр контейнеров Azure](container-instances-tutorial-prepare-acr.md). Эта часть завершает руководство. Мы развернем контейнер в службе "Экземпляры контейнеров Azure". В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * Развертывание контейнера из реестра контейнеров Azure с помощью Azure CLI
> * Просмотр приложения в браузере
> * Просмотр журналов контейнера

## <a name="before-you-begin"></a>Перед началом работы

Для этого руководства требуется Azure CLI 2.0.21 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Для работы с этим руководством требуется среда разработки Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Таким образом, мы рекомендуем выполнить локальную установку среды разработки Azure CLI и Docker.

## <a name="deploy-the-container-using-the-azure-cli"></a>Развертывание контейнера с помощью Azure CLI

Azure CLI позволяет одной командой развернуть контейнер в службе "Экземпляры контейнеров Azure". Так как образ контейнера размещается в частном реестре контейнеров Azure, необходимо включить учетные данные, необходимые для доступа к нему. При необходимости их можно запросить, как показано ниже.

Сервер входа в реестр контейнеров (укажите используемое имя реестра):

```azurecli
az acr show --name <acrName> --query loginServer
```

Пароль для реестра контейнеров:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Чтобы развернуть образ контейнера из реестра контейнеров с запросом ресурсов (одно ядро ЦП и 1 ГБ памяти), выполните следующую команду. Замените `<acrLoginServer>` и `<acrPassword>` значениями, полученными посредством предыдущих двух команд.

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80 -g myResourceGroup
```

В течение нескольких секунд вы получите исходный ответ Azure Resource Manager. Чтобы просмотреть состояние развертывания, используйте команду [az container show](/cli/azure/container#az_container_show).

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query instanceView.state
```

Повторяйте выполнение команды `az container show`, пока состояние не изменится с *Ожидание* на *Выполняется*, что должно потребовать не более минуты. Если контейнер находится в состоянии *Выполняется*, перейдите к следующему шагу.

## <a name="view-the-application-and-container-logs"></a>Просмотр приложения и журналов контейнера

После успешного развертывания отобразите общедоступный IP-адрес контейнера командой [az container show](/cli/azure/container#az_container_show).

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

Пример выходных данных: `"13.88.176.27"`

Чтобы увидеть работающее приложение, перейдите по общедоступному IP-адресу в своем браузере.

![Приложение Hello World в браузере][aci-app-browser]

Также можно просмотреть выходные данные журнала контейнера:

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

Выходные данные:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ни один из ресурсов, созданных в данном цикле руководств, больше не требуется, можно выполнить команду [az group delete](/cli/azure/group#delete), чтобы удалить группу ресурсов и все ресурсы, которые она содержит. Эта команда удаляет созданный вами реестр контейнеров, а также запущенный контейнер и все связанные с ним ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы завершили процесс развертывания контейнеров в службе "Экземпляры контейнеров Azure". Были выполнены следующие действия:

> [!div class="checklist"]
> * Развертывание контейнера из реестра контейнеров Azure с помощью Azure CLI
> * Просмотр приложения в браузере
> * Просмотр журналов контейнера

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
