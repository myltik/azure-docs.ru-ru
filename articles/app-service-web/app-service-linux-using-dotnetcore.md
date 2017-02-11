---
title: "Использование .NET Core в веб-приложениях службы приложений Azure на платформе Linux | Документация Майкрософт"
description: "Сведения об использовании .NET Core в веб-приложениях службы приложений Azure на платформе Linux."
keywords: "служба приложений azure, веб-приложение, dotnet, core, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Использование .NET Core в веб-приложениях на платформе Linux #

В последнем обновлении нашей серверной части мы добавили поддержку .NET Core версии 1.0. Настроив конфигурацию веб-приложения Linux, вы можете изменить стек приложений.


## <a name="using-xplat-cli"></a>Использование кроссплатформенного интерфейса командной строки ##

Используя последнюю версию кроссплатформенного интерфейса командной строки Azure, вы можете выполнить команду **azure webapp config set**, которая позволяет изменить стек приложений. Ниже приведен пример такой команды.

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Обратите внимание, что файл **aspnetcore.dll** — это библиотека динамической компоновки приложения. Для своего приложения вы можете указать любое имя.

В результате выполнения этой команды будет загружен образ .Net Core и запущено веб-приложение. Настроенные параметры можно проверить с помощью команды **azure webapp config show**. Ниже приведен пример такой команды.

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Дальнейшие действия
* [Вводные сведения о службе приложений на платформе Linux](./app-service-linux-intro.md) 
* [What is App Service on Linux?](app-service-linux-intro.md) (Общие сведения о службе приложений в Linux)
* [Creating Web Apps in App Service on Linux](./app-service-linux-how-to-create-a-web-app.md) (Создание веб-приложений в службе приложений на платформе Linux)
* [Azure Web App Cross Platform CLI](app-service-web-app-azure-resource-manager-xplat-cli.md) (Кроссплатформенный интерфейс командной строки для веб-приложений Azure)



<!--HONumber=Nov16_HO3-->


