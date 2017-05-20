---
title: "Использование Ruby в веб-приложении службы приложений Azure на платформе Linux | Документация Майкрософт"
description: "Использование Ruby в веб-приложении службы приложений Azure на платформе Linux."
keywords: "Служба приложений Azure, веб-приложение, вопросы и ответы, Linux, OSS, Ruby"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5aeef6f31dacb1b27c605d39a35a81bd0211e06a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="using-ruby-in-web-app-on-linux"></a>Использование Ruby в веб-приложении на платформе Linux #

В последнем обновлении нашей серверной части мы добавили поддержку Ruby версии 2.3. Настроив конфигурацию веб-приложения Linux, вы можете изменить стек приложений.

## <a name="using-the-azure-portal"></a>Использование портала Azure ##

Из меню "Создать" на [портале Azure](https://portal.azure.com) вы можете создать веб-приложение на платформе Linux, выбрав "Интернет+мобильные устройства", как показано на следующем рисунке.

![Начало создания веб-приложения на портале Azure][1]

После этого откроется колонка **Создание**, как показано на следующем изображении.

![Колонка "Создание"][2]

1. Присвойте веб-приложению имя.
2. Выберите имеющуюся группу ресурсов или создайте новую. (Доступные регионы см. в [разделе с ограничениями](app-service-linux-intro.md).)
3. Выберите имеющийся план службы приложений Azure или создайте новый. (Заметки о плане службы приложений см. в [разделе с ограничениями](app-service-linux-intro.md).)
4. Выберите Ruby из списка встроенных стеков времени выполнения.

После создания веб-приложения Ruby его можно развернуть с помощью Git или FTP.

## <a name="next-steps"></a>Дальнейшие действия
* [Что такое веб-приложение на платформе Linux?](app-service-linux-intro.md)
* [Создание веб-приложений в веб-приложении на платформе Linux](app-service-linux-how-to-create-web-app.md)
* [Развертывание локального репозитория Git в службе приложений Azure](app-service-deploy-local-git.md)
* [Вопросы и ответы о веб-приложении службы приложений Azure на платформе Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
