---
title: "Непрерывное развертывание Docker Hub для веб-приложения Azure на платформе Linux | Документация Майкрософт"
description: "Как настроить непрерывное развертывание в веб-приложении Azure на платформе Linux."
keywords: "служба приложений azure, linux, oss"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>Непрерывное развертывание Docker Hub для веб-приложения Azure на платформе Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

В этом руководстве описывается настройка непрерывного развертывания для настраиваемого образа контейнера из [Docker Hub](https://hub.docker.com).

## <a name="step-1---log-in-to-azure"></a>Шаг 1. Вход в Azure

Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>Шаг 2. Включение непрерывного развертывания Docker Hub

На **[портале Azure](https://portal.azure.com/)** щелкните **Служба приложений** в левой части страницы.

Щелкните имя приложения, для которого вы хотите настроить непрерывное развертывание Docker Hub.

В разделе **Параметры приложения** добавьте параметр приложения `DOCKER_ENABLE_CI` со значением `true`.

![Изображение добавления параметра приложения](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>Шаг 3. Добавление webhook для Docker Hub

На своей странице Docker Hub щелкните **Webhooks** (Объекты webhook), затем щелкните **CREATE A WEBHOOK** (Создать webhook).

![Изображение добавления webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Для URL-адреса webhook необходима следующая конечная точка: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

![Изображение добавления webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Вы можете получить `publishingusername` и `publishingpwd`, скачав профиль публикации веб-приложения с помощью портала Azure.

![Изображение добавления webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

При обновлении образа веб-приложение будет автоматически обновлено с использованием нового образа.

## <a name="next-steps"></a>Дальнейшие действия
* [Что такое веб-приложение Azure на платформе Linux?](./app-service-linux-intro.md)
* [Создание приложений в веб-приложении Azure на платформе Linux](./app-service-linux-how-to-create-web-app.md)
* [Использование конфигурации PM2 для Node.js в веб-приложении Azure на платформе Linux](app-service-linux-using-nodejs-pm2.md)
* [Использование .NET Core в веб-приложении Azure на платформе Linux](app-service-linux-using-dotnetcore.md)
* [Использование Ruby в веб-приложении Azure на платформе Linux](app-service-linux-ruby-get-started.md)
* [Как применить пользовательский образ Docker для веб-приложения Azure на платформе Linux](./app-service-linux-using-custom-docker-image.md)
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](./app-service-linux-faq.md) 




