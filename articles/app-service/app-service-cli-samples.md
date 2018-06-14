---
title: Примеры Azure CLI. Служба приложений | Документация Майкрософт
description: Примеры Azure CLI. Служба приложений
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fdc5e03350783fb8c3e30b6c9a40af45a5925ba8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
ms.locfileid: "26738972"
---
# <a name="azure-cli-samples"></a>Примеры Azure CLI

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создание приложения**||
| [Создание веб-приложения и развертывание файлов с помощью протокола FTP](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Создание веб-приложения Azure и развертывание файла с использованием протокола FTP. |
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
|**Резервное копирование и восстановление приложения**||
| [Резервное копирование веб-приложения](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Создание веб-приложения Azure и однократное создание его резервной копии. |
| [Создание резервной копии веб-приложения по расписанию](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Создание веб-приложения Azure и запланированное создание его резервной копии. |
| [Восстановление веб-приложения из резервной копии](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Восстановление веб-приложения Azure из резервной копии. |
|**Мониторинг приложения**||
| [Мониторинг веб-приложения с помощью журналов веб-сервера](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Создает веб-приложение Azure, включает ведение журналов и скачивает их на локальный компьютер. |
| | |