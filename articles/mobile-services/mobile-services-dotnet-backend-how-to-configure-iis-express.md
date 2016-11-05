---
title: Настройка IIS Express для локального тестирования мобильной службы | Microsoft Docs
description: Узнайте, как настроить IIS Express для подключения к проекту локальной мобильной службы в целях проведения тестирования.
author: ggailey777
manager: dwrede
services: mobile-services
documentationcenter: ''
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Настройка локального веб-сервера для подключения к локальной мобильной службе
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

Мобильные службы Azure позволяют вам создавать мобильные службы в Visual Studio с помощью одного из поддерживаемых языков .NET, а затем публиковать их в Azure. Одним из основных преимуществ использования серверной версии .NET для мобильной службы является возможность выполнения, тестирования и отладки мобильной службы на локальном компьютере или виртуальной машине перед публикацией ее в Azure.

Для локального тестирования мобильной службы с использованием клиентов, работающих в эмуляторе, на виртуальной машине или отдельной рабочей станции, необходимо настроить локальный веб-сервер и главный компьютер, чтобы разрешить подключения к IP-адресу и порту рабочей станции. В этом разделе показано, как настроить IIS Express, чтобы разрешить подключения к мобильной службе, размещенной локально.

[!INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

<!---HONumber=AcomDC_0727_2016-->