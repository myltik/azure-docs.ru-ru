---
title: "Руководство по службе \"Экземпляры контейнеров Azure\". Развертывание приложения | Документация Майкрософт"
description: "Руководство по службе \"Экземпляры контейнеров Azure\". Развертывание приложения"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: d8c2056734bc1fdea71543157debd089a9ca743d
ms.contentlocale: ru-ru
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-container-to-azure-container-instances"></a>Развертывание контейнера в службе "Экземпляры контейнеров Azure"

Эта последняя часть руководства, состоящего из трех частей. В предыдущих частях мы [создали образ контейнера](container-instances-tutorial-prepare-app.md), который затем [передали в реестр контейнеров Azure](container-instances-tutorial-prepare-acr.md). Эта часть завершает руководство. Мы развернем контейнер в службе "Экземпляры контейнеров Azure". В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * Определение группы контейнеров с использованием шаблона Azure Resource Manager
> * Развертывание группы контейнеров с помощью Azure CLI
> * Просмотр журналов контейнера

## <a name="deploy-the-container-using-the-azure-cli"></a>Развертывание контейнера с помощью Azure CLI

Azure CLI позволяет одной командой развернуть контейнер в службе "Экземпляры контейнеров Azure". Так как образ контейнера размещается в частном реестре контейнеров Azure, необходимо включить учетные данные, необходимые для доступа к нему. При необходимости их можно запросить, как показано ниже.

Сервер входа в реестр контейнеров (укажите используемое имя реестра):

```azurecli-interactive
az acr show --name <acrName> --query loginServer
```

Пароль для реестра контейнеров:

```azurecli-interactive
az acr credential show --name <acrName> --query "passwords[0].value"
```

Чтобы развернуть образ контейнера из реестра контейнеров с запросом ресурсов (одно ядро ЦП и 1 ГБ памяти), выполните следующую команду:

```azurecli-interactive
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

В течение нескольких секунд вы получите исходный ответ Azure Resource Manager. Чтобы просмотреть состояние развертывания, используйте следующую команду:

```azurecli-interactive
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

Мы можем и дальше выполнять эту команду, пока состояние не изменится с *Ожидание* на *Выполняется*. Затем можно продолжить.

## <a name="view-the-application-and-container-logs"></a>Просмотр приложения и журналов контейнера

После успешного развертывания откройте в браузере IP-адрес, показанный в выходных данных следующей команды:

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![Приложение Hello World в браузере][aci-app-browser]

Также можно просмотреть выходные данные журнала контейнера:

```azurecli-interactive
az container logs --name aci-tutorial-app -g myResourceGroup
```

Выходные данные:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
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

