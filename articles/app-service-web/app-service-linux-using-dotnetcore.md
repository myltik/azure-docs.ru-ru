---
title: "Использование .NET Core в веб-приложениях службы приложений Azure на платформе Linux | Документация Майкрософт"
description: "Сведения об использовании .NET Core в веб-приложениях службы приложений Azure на платформе Linux."
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
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 769de52a85d1d5078b2ba583e94cabd22b0fde65
ms.lasthandoff: 02/17/2017


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Использование .NET Core в веб-приложениях на платформе Linux #

В последнем обновлении нашей серверной части мы добавили поддержку .NET Core версии 1.0. Настроив конфигурацию веб-приложения Linux, вы можете изменить стек приложений.


## <a name="using-xplat-cli"></a>Использование кроссплатформенного интерфейса командной строки ##

Используя последнюю версию кроссплатформенного интерфейса командной строки Azure, вы можете выполнить команду **azure webapp config set**, которая позволяет изменить стек приложений. Пример:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Файл **aspnetcore.dll** — это библиотека DLL приложения. В своем приложении вы можете указать любое имя.

В результате выполнения этой команды будет загружен образ .Net Core и запущено веб-приложение. Настроенные параметры можно проверить с помощью команды **azure webapp config show**. Пример:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Дальнейшие действия
* [What is App Service on Linux?](app-service-linux-intro.md) (Общие сведения о службе приложений в Linux)
* [Creating Web Apps in App Service on Linux](./app-service-linux-how-to-create-a-web-app.md) (Создание веб-приложений в службе приложений на платформе Linux)
* [Azure Web App Cross Platform CLI](app-service-web-app-azure-resource-manager-xplat-cli.md) (Кроссплатформенный интерфейс командной строки для веб-приложений Azure)
* [Вопросы и ответы о веб-приложениях службы приложений Azure на платформе Linux](app-service-linux-faq.md)
