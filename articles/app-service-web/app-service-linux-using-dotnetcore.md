---
title: "Использование .NET Core в веб-приложении службы приложений Azure на платформе Linux | Документация Майкрософт"
description: "Использование .NET Core в веб-приложении службы приложений Azure на платформе Linux."
keywords: "служба приложений azure, веб-приложение, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 2d979b954912b8f344aa681980514821058ce5da
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>Использование .NET Core в веб-приложении Azure на платформе Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


В последнем обновлении нашей серверной части мы добавили поддержку .NET Core версии 1.0. Настроив конфигурацию веб-приложения Linux, вы можете изменить стек приложений.


## <a name="using-the-azure-cli"></a>Использование Azure CLI ##

Используя [последнюю версию интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), вы можете выполнить команду **azure webapp config set**, которая позволяет изменить стек приложений. Пример:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Файл **aspnetcore.dll** — это библиотека DLL приложения. В своем приложении вы можете указать любое имя.

В результате выполнения этой команды будет загружен образ .Net Core и запущено веб-приложение. Настроенные параметры можно проверить с помощью команды **azure webapp config show**. Пример:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Дальнейшие действия
* [Что такое веб-приложение Azure на платформе Linux?](app-service-linux-intro.md)
* [Создание веб-приложений в веб-приложении Azure на платформе Linux](./app-service-linux-how-to-create-web-app.md)
* [Azure Web App Cross Platform CLI](app-service-web-app-azure-resource-manager-xplat-cli.md) (Кроссплатформенный интерфейс командной строки для веб-приложений Azure)
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](app-service-linux-faq.md)
