<properties pageTitle="Configure IIS Express for local testing of Mobile Service" metaKeywords="Azure Mobile Services, .NET Backend, IIS Express" description="Learn how to configure IIS Express to allow connections to a local mobile service project for testing." authors="glenga" title="Configure the local web server to allow connections to a local mobile service" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Настройка локального веб-сервера для подключения к локальной мобильной службе

Мобильные службы Azure позволяют вам создавать мобильные службы в Visual Studio с помощью одного из поддерживаемых языков .NET, а затем публиковать их в Azure. Одним из основных преимуществ использования серверной версии .NET для мобильной службы является возможность выполнения, тестирования и отладки мобильной службы на локальном компьютере или виртуальной машине перед публикацией ее в Azure.

Для локального тестирования мобильной службы с использованием клиентов, работающих в эмуляторе, на виртуальной машине или отдельной рабочей станции, необходимо настроить локальный веб-сервер и главный компьютер, чтобы разрешить подключения к IP-адресу и порту рабочей станции. В этом разделе показано, как настроить IIS Express, чтобы разрешить подключения к мобильной службе, размещенной локально.

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

  [mobile-services-how-to-configure-iis-express]: ../includes/mobile-services-how-to-configure-iis-express.md
