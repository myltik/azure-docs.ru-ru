---
title: "Примеры Azure PowerShell. Служба приложений | Документация Майкрософт"
description: "Примеры Azure PowerShell для службы приложений."
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: c8167eaeff389a1bb77a8f13e522b1a2ce58aff8
ms.lasthandoff: 03/10/2017


---
# <a name="azure-powershell-samples"></a>Примеры сценариев Azure PowerShell.

В следующей таблице приведены ссылки на сценарии bash, созданные с помощью Azure PowerShell.

| | |
|-|-|
|**Создание приложения**||
| [Создание веб-приложения с непрерывным развертыванием из GitHub](./scripts/app-service-powershell-deploy-github.md)| Создает веб-приложение Azure, получающее код из GitHub. |
| [Создание веб-приложения с непрерывным развертыванием из GitHub](./scripts/app-service-powershell-continuous-deployment-github.md)| Создает веб-приложение Azure, непрерывно развертывающее код из GitHub. |
| [Создание веб-приложения и развертывание кода с помощью протокола FTP](./scripts/app-service-powershell-deploy-ftp.md) | Создает веб-приложение Azure и передает файлы из локального каталога с помощью протокола FTP. |
| [Создание веб-приложения и развертывание кода из локального репозитория Git](./scripts/app-service-powershell-deploy-local-git.md) | Создает веб-приложение Azure и настраивает отправку кода из локального репозитория Git. |
| [Создание веб-приложения и развертывание кода в промежуточной среде](./scripts/app-service-powershell-deploy-staging-environment.md) | Создает веб-приложение Azure со слотом развертывания для изменений промежуточного кода. |
|**Настройка приложения**||
| [Сопоставление пользовательского домена с веб-приложением](./scripts/app-service-powershell-configure-custom-domain.md)| Создает веб-приложение Azure и сопоставляет c ним имя пользовательского домена. |
| [Привязка пользовательского SSL-сертификата к веб-приложению](./scripts/app-service-powershell-configure-ssl-certificate.md)| Создает веб-приложение Azure и привязывает к нему SSL-сертификат имени личного домена. |
|**Масштабирование приложения**||
| [Масштабирование веб-приложения вручную](./scripts/app-service-powershell-scale-manual.md) | Создает веб-приложение Azure и масштабирует его по двум экземплярам. |
| [Глобальное масштабирование веб-приложения с помощью высокодоступной архитектуры](./scripts/app-service-powershell-scale-high-availability.md) | Создает два веб-приложения Azure в двух разных географических регионах и делает их доступными через одну конечную точку с помощью диспетчера трафика Azure. |
|**Подключение приложения к ресурсам**||
| [Подключение веб-приложения к базе данных SQL](./scripts/app-service-powershell-connect-to-sql.md)| Создает веб-приложение Azure и базу данных SQL, а затем добавляет строку подключения базы данных к параметрам приложения. |
| [Подключение веб-приложения к учетной записи хранения](./scripts/app-service-powershell-connect-to-storage.md)| Создает веб-приложение Azure и учетную запись хранения, а затем добавляет строку подключения хранилища к параметрам приложения. |
|**Мониторинг приложения**||
| [Мониторинг веб-приложения с помощью журналов веб-сервера](./scripts/app-service-powershell-monitor.md) | Создает веб-приложение Azure, включает ведение журналов и скачивает их на локальный компьютер. |
| | |
