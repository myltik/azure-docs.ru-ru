---
title: "Создание веб-приложения Azure на платформе Linux | Документация Майкрософт"
description: "Рабочий процесс создания веб-приложения для веб-приложения Azure на платформе Linux."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: f4bf4138ce3a9a2495a0a0fd46e149f6ac96d82d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Создание веб-приложения Azure на платформе Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


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
* [Что такое веб-приложение Azure на платформе Linux?](app-service-linux-intro.md)
* [Использование конфигурации PM2 для Node.js в веб-приложении Azure на платформе Linux](app-service-linux-using-nodejs-pm2.md)
* [Использование Ruby в веб-приложении службы приложений Azure на платформе Linux](app-service-linux-ruby-get-started.md)
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

