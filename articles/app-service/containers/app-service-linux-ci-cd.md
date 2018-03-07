---
title: "Непрерывное развертывание из реестра контейнеров Docker при помощи платформы \"Веб-приложения для контейнеров\" (Azure) | Документация Майкрософт"
description: "Как настроить непрерывное развертывание из реестра контейнеров Docker на платформе \"Веб-приложения для контейнеров\"."
keywords: azure app service, linux, docker, acr,oss
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: e61c767ada31fc32e28bfd9a2a4e843e9ca88053
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"

В этом руководстве описывается настройка непрерывного развертывания для настраиваемого образа контейнера из управляемых репозиториев [реестра контейнеров Azure](https://azure.microsoft.com/services/container-registry/) или [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>Включение функции непрерывного развертывания контейнера

Включить функцию непрерывного развертывания можно с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и выполнив следующую команду:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

На **[портале Azure](https://portal.azure.com/)** щелкните **Служба приложений** в левой части страницы.

Щелкните имя приложения, для которого вы хотите настроить непрерывное развертывание Docker Hub.

Чтобы включить непрерывное развертывание в разделе **Контейнер Docker** выберите "ВКЛ.", а затем нажмите кнопку "Сохранить".

![Изображение добавления параметра приложения](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>Подготовка URL-адреса веб-перехватчика

Получить URL-адрес можно с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и выполнив следующую команду:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Для URL-адреса webhook необходима следующая конечная точка: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Вы можете получить `publishingusername` и `publishingpwd`, скачав профиль публикации веб-приложения с помощью портала Azure.

![Изображение добавления webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>Добавление веб-перехватчика

### <a name="azure-container-registry"></a>реестр контейнеров Azure;

В колонке реестра щелкните **Webhooks** (Объекты webhook), создайте webhook, нажав кнопку **Добавить**. В колонке **Создание webhook** задайте имя для webhook. В качестве URI webhook необходимо указать URL-адрес, полученный на **шаге 3**.

Убедитесь, что вы определили область в качестве репозитория, который содержит образ контейнера.

![изображение webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

При обновлении образа веб-приложение будет автоматически обновлено с использованием нового образа.

### <a name="docker-hub"></a>Docker Hub

На своей странице Docker Hub щелкните **Webhooks** (Объекты webhook), затем щелкните **CREATE A WEBHOOK** (Создать webhook).

![Изображение добавления webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

В качестве URL-адреса Webhook необходимо указать URL-адрес, полученный на **шаге 3**.

![Изображение добавления webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

При обновлении образа веб-приложение будет автоматически обновлено с использованием нового образа.

## <a name="next-steps"></a>Дополнительная информация

* [Общие сведения о службе приложений на платформе Linux](./app-service-linux-intro.md).
* [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/)
* [Использование .NET Core в службе приложений Azure на платформе Linux](quickstart-dotnetcore.md).
* [Использование Ruby в службе приложений Azure на платформе Linux](quickstart-ruby.md)
* [Использование пользовательского образа Docker для платформы "Веб-приложения для контейнеров"](quickstart-docker-go.md).
* [Вопросы и ответы о платформе "Веб-приложения для контейнеров" в службе приложений Azure](./app-service-linux-faq.md)
* [Управление веб-приложениями для контейнеров с помощью Azure CLI 2.0](./app-service-linux-cli.md)
