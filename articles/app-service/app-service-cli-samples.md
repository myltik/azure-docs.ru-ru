---
title: "Примеры Azure CLI. Служба приложений | Документация Майкрософт"
description: "Примеры Azure CLI. Служба приложений"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: f2c95ec5cd32632bb5b9683f5273721f465aebdf
ms.openlocfilehash: 2d7d2154a2910c0bd7ff24c404c4eb05203b1453
ms.lasthandoff: 03/01/2017


---
# <a name="azure-app-service-cli-samples"></a>Примеры интерфейса командной строки для службы приложений Azure

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создание приложения**||
| [Создание веб-приложения с непрерывным развертыванием из GitHub](./scripts/app-service-cli-deploy-github.md)| Создает веб-приложение Azure, получающее код из GitHub. |
| [Создание веб-приложения с непрерывным развертыванием из GitHub](./scripts/app-service-cli-continuous-deployment-github.md)| Создает веб-приложение Azure, непрерывно развертывающее код из GitHub. |
| [Создание веб-приложения с непрерывным развертыванием из Visual Studio Team Services](./scripts/app-service-cli-continuous-deployment-vsts.md)| Создает веб-приложение Azure, непрерывно развертывающее код из Visual Studio Team Services. |
| [Создание веб-приложения и развертывание кода из локального репозитория Git](./scripts/app-service-cli-deploy-local-git.md) | Создает веб-приложение Azure и настраивает отправку кода из локального репозитория Git. |
| [Создание веб-приложения и развертывание кода в промежуточной среде](./scripts/app-service-cli-deploy-staging-environment.md) | Создает веб-приложение Azure со слотом развертывания для изменений промежуточного кода. |
| [Создание веб-приложения ASP.NET Core в контейнере Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md)| Создает веб-приложение Azure в Linux и загружает образ Docker из Docker Hub. |
| [Создание веб-приложения ASP.NET Core в контейнере Docker из реестра контейнеров Azure](./scripts/app-service-cli-linux-acr-aspnetcore.md)| Создает веб-приложение Azure в Linux и загружает образ Docker из реестра контейнеров Azure. |
|**Настройка приложения**||
| [Сопоставление пользовательского домена с веб-приложением](./scripts/app-service-cli-configure-custom-domain.md)| Создает веб-приложение Azure и сопоставляет c ним имя пользовательского домена. |
|**Масштабирование приложения**||
| [Масштабирование веб-приложения вручную](./scripts/app-service-cli-scale-manual.md) | Создает веб-приложение Azure и масштабирует его по двум экземплярам. |
| [Глобальное масштабирование веб-приложения с помощью высокодоступной архитектуры](./scripts/app-service-cli-scale-high-availability.md) | Создает два веб-приложения Azure в двух разных географических регионах и делает их доступными через одну конечную точку с помощью диспетчера трафика Azure. |
|**Подключение приложения к ресурсам**||
| [Подключение веб-приложения к базе данных SQL](./scripts/app-service-cli-app-service-sql.md)| Создает веб-приложение Azure и базу данных SQL, а затем добавляет строку подключения базы данных к параметрам приложения. |
| [Подключение веб-приложения к учетной записи хранения](./scripts/app-service-cli-app-service-storage.md)| Создает веб-приложение Azure и учетную запись хранения, а затем добавляет строку подключения хранилища к параметрам приложения. |
|**Мониторинг приложения**||
| [Мониторинг веб-приложения с помощью журналов веб-сервера](./scripts/app-service-cli-monitor.md) | Создает веб-приложение Azure, включает ведение журналов и скачивает их на локальный компьютер. |
| | |

