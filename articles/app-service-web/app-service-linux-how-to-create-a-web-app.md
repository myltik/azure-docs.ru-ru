---
title: "Создание веб-приложения Azure на платформе Linux | Документация Майкрософт"
description: "Рабочий процесс создания веб-приложения для службы приложений в Linux."
keywords: "служба приложений azure, веб-приложение, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 19a42a8dc9a4026d93c10dd209e84d4e14b8f00a
ms.lasthandoff: 02/17/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Создание веб-приложения Azure на платформе Linux
## <a name="use-the-azure-portal-to-create-your-web-app"></a>Создание веб-приложения на портале Azure
Чтобы приступить к созданию собственного веб-приложения в Linux на [портале Azure](https://portal.azure.com), выполните действия, как показано на следующем изображении.

![Начало создания веб-приложения на портале Azure][1]

После этого откроется колонка **Создание**, как показано на следующем изображении.

![Колонка "Создание"][2]

1. Присвойте веб-приложению имя.
2. Выберите имеющуюся группу ресурсов или создайте новую. (Доступные регионы см. в [разделе с ограничениями](app-service-linux-intro.md).)
3. Выберите имеющийся план службы приложений Azure или создайте новый. (Заметки о плане службы приложений см. в [разделе с ограничениями](app-service-linux-intro.md).)
4. Выберите необходимый стек приложений. На выбор предоставляется несколько версий Node.js, PHP, .NET Core и Ruby.

После создания приложения стек можно изменить в настройках, как показано на следующем изображении.

![Параметры приложения][3]

## <a name="deploy-your-web-app"></a>Развертывание веб-приложения
Выберите один из доступных **вариантов развертывания** на портале управления: развертывание с использованием локального репозитория Git или развертывание с использованием репозитория GitHub. Остальная часть инструкций аналогична указаниям для веб-приложений не на платформе Linux. Чтобы развернуть приложение, вы можете следовать указаниям по [локальному развертыванию Git](app-service-deploy-local-git.md) или [непрерывному развертыванию](app-service-continuous-deployment.md).

Чтобы передать свое приложение на сайт, можно использовать протокол FTP. Конечную точку FTP для веб-приложения можно получить в разделе "Журналы диагностики", как показано на следующем изображении.

![Раздел "Журналы диагностики"][4]

## <a name="next-steps"></a>Дальнейшие действия
* [What is App Service on Linux?](app-service-linux-intro.md) (Общие сведения о службе приложений в Linux)
* [Использование конфигурации PM2 для Node.js в веб-приложениях на платформе Linux](app-service-linux-using-nodejs-pm2.md)
* [Использование Ruby в веб-приложениях службы приложений Azure на платформе Linux](app-service-linux-using-ruby.md)
* [Вопросы и ответы о веб-приложениях службы приложений Azure на платформе Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

