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
ms.date: 03/08/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5c995ca118676935f4f9b0c72c266b9d52c181cb
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cli-samples"></a>Примеры Azure CLI

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создание приложения**||
| [Создание веб-приложения и развертывание кода из GitHub](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Создает веб-приложение Azure и развертывает код из общедоступного репозитория GitHub. |
| [Создание веб-приложения с непрерывным развертыванием из GitHub](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Создает веб-приложение Azure с непрерывной публикацией из репозитория GitHub, владельцем которого вы являетесь. |
| [Создание веб-приложения и развертывание кода из локального репозитория Git](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Создает веб-приложение Azure и настраивает отправку кода из локального репозитория Git. |
| [Создание веб-приложения и развертывание кода в промежуточной среде](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Создает веб-приложение Azure со слотом развертывания для изменений промежуточного кода. |
| [Создание веб-приложения ASP.NET Core в контейнере Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Создает веб-приложение Azure в Linux и загружает образ Docker из Docker Hub. |
|**Настройка приложения**||
| [Сопоставление пользовательского домена с веб-приложением](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Создает веб-приложение Azure и сопоставляет c ним имя пользовательского домена. |
| [Привязка пользовательского SSL-сертификата к веб-приложению](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Создает веб-приложение Azure и привязывает к нему SSL-сертификат имени личного домена. |
|**Масштабирование приложения**||
| [Масштабирование веб-приложения вручную](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Создает веб-приложение Azure и масштабирует его по двум экземплярам. |
| [Глобальное масштабирование веб-приложения с помощью высокодоступной архитектуры](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Создает два веб-приложения Azure в двух разных географических регионах и делает их доступными через одну конечную точку с помощью диспетчера трафика Azure. |
|**Подключение приложения к ресурсам**||
| [Подключение веб-приложения к базе данных SQL](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Создает веб-приложение Azure и базу данных SQL, а затем добавляет строку подключения базы данных к параметрам приложения. |
| [Подключение веб-приложения к учетной записи хранения](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Создает веб-приложение Azure и учетную запись хранения, а затем добавляет строку подключения хранилища к параметрам приложения. |
| [Подключение веб-приложения к кэшу Redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Создает веб-приложение Azure и кэш Redis, а затем добавляет сведения о подключении кэша Redis в параметры приложения. |
| [Подключение веб-приложения к Cosmos DB](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Создает веб-приложение Azure и базу данных Cosmos DB, а затем добавляет сведения о подключении Cosmos DB в параметры приложения. |
|**Мониторинг приложения**||
| [Мониторинг веб-приложения с помощью журналов веб-сервера](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Создает веб-приложение Azure, включает ведение журналов и скачивает их на локальный компьютер. |
| | |
