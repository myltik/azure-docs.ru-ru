---
title: "Непрерывное развертывание для веб-приложения Azure на платформе Linux | Документы Майкрософт"
description: "Как настроить непрерывное развертывание в веб-приложении Azure на платформе Linux."
keywords: "служба приложений azure, linux, oss, acr"
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: f8f7d51003f8a55b7f51e8cc2cea838e8e5a6196
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>Непрерывное развертывание для веб-приложения Azure на платформе Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

В этом руководстве описывается настройка непрерывного развертывания для настраиваемого образа контейнера из управляемых репозиториев [реестра контейнеров Azure](https://azure.microsoft.com/en-us/services/container-registry/) или [Docker Hub](https://hub.docker.com).

## <a name="step-1---sign-in-to-azure"></a>Шаг 1. Вход в Azure

Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="step-2---enable-container-continuous-deployment-feature"></a>Шаг 2. Включение функции непрерывного развертывания контейнера

Включить функцию непрерывного развертывания можно с помощью [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) и выполнив следующую команду:

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

На **[портале Azure](https://portal.azure.com/)** щелкните **Служба приложений** в левой части страницы.

Щелкните имя приложения, для которого вы хотите настроить непрерывное развертывание Docker Hub.

В разделе **Параметры приложения** добавьте параметр приложения `DOCKER_ENABLE_CI` со значением `true`.

![Изображение добавления параметра приложения](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>Шаг 3 . Подготовка URL-адреса Webhook

Получить URL-адрес можно с помощью [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) и выполнив следующую команду:

```azurecli-interactive
az webapp deployment container -n sname1 -g rgname -e true --show-cd-url
``` 

Для URL-адреса webhook необходима следующая конечная точка: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Вы можете получить `publishingusername` и `publishingpwd`, скачав профиль публикации веб-приложения с помощью портала Azure.

![Изображение добавления webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>Шаг 4. Добавление webhook

### <a name="azure-container-registry"></a>Реестр контейнеров Azure

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

## <a name="next-steps"></a>Дальнейшие действия
* [Что такое веб-приложение Azure на платформе Linux?](./app-service-linux-intro.md)
* [Реестр контейнеров Azure](https://azure.microsoft.com/en-us/services/container-registry/)
* [Использование конфигурации PM2 для Node.js в веб-приложении Azure на платформе Linux](app-service-linux-using-nodejs-pm2.md)
* [Использование .NET Core в веб-приложении Azure на платформе Linux](app-service-linux-using-dotnetcore.md)
* [Использование Ruby в веб-приложении Azure на платформе Linux](app-service-linux-ruby-get-started.md)
* [Как применить пользовательский образ Docker для веб-приложения Azure на платформе Linux](./app-service-linux-using-custom-docker-image.md)
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](./app-service-linux-faq.md) 
* [Управление веб-приложением на платформе Linux с помощью Azure CLI](./app-service-linux-cli.md)




