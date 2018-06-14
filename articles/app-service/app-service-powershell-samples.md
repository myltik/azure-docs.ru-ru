---
title: Примеры Azure PowerShell. Служба приложений | Документация Майкрософт
description: Примеры Azure PowerShell для службы приложений.
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: ba2bd2b185c395e54f2f085317a424a2aa1b4421
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2018
ms.locfileid: "27713422"
---
# <a name="azure-powershell-samples"></a>Примеры сценариев Azure PowerShell.

В следующей таблице приведены ссылки на сценарии PowerShell, созданные с помощью Azure PowerShell.

| | |
|-|-|
|**Создание приложения**||
| [Создание веб-приложения с непрерывным развертыванием из GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание веб-приложения Azure, которое вытягивает код из GitHub. |
| [Создание веб-приложения с непрерывным развертыванием из GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создание веб-приложения Azure, которое непрерывно развертывает код из GitHub. |
| [Создание веб-приложения и развертывание кода с помощью протокола FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает веб-приложение Azure и передает файлы из локального каталога с помощью протокола FTP. |
| [Создание веб-приложения и развертывание кода из локального репозитория Git](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает веб-приложение Azure и настраивает отправку кода из локального репозитория Git. |
| [Создание веб-приложения и развертывание кода в промежуточной среде](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает веб-приложение Azure со слотом развертывания для изменений промежуточного кода. |
|**Настройка приложения**||
| [Сопоставление пользовательского домена с веб-приложением](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает веб-приложение Azure и сопоставляет c ним имя пользовательского домена. |
| [Привязка пользовательского SSL-сертификата к веб-приложению](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает веб-приложение Azure и привязывает к нему SSL-сертификат имени личного домена. |
|**Масштабирование приложения**||
| [Масштабирование веб-приложения вручную](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает веб-приложение Azure и масштабирует его по двум экземплярам. |
| [Глобальное масштабирование веб-приложения с помощью высокодоступной архитектуры](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает два веб-приложения Azure в двух разных географических регионах и делает их доступными через одну конечную точку с помощью диспетчера трафика Azure. |
|**Подключение приложения к ресурсам**||
| [Подключение веб-приложения к базе данных SQL](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает веб-приложение Azure и базу данных SQL, а затем добавляет строку подключения базы данных к параметрам приложения. |
| [Подключение веб-приложения к учетной записи хранения](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает веб-приложение Azure и учетную запись хранения, а затем добавляет строку подключения хранилища к параметрам приложения. |
|**Резервное копирование и восстановление приложения**||
| [Резервное копирование веб-приложения](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создание веб-приложения Azure и однократное создание его резервной копии. |
| [Создание резервной копии веб-приложения по расписанию](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создание веб-приложения Azure и запланированное создание его резервной копии. |
| [Удаление резервной копии веб-приложения](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Удаление существующей резервной копии веб-приложения. |
|**Мониторинг приложения**||
| [Мониторинг веб-приложения с помощью журналов веб-сервера](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает веб-приложение Azure, включает ведение журналов и скачивает их на локальный компьютер. |
| | |
