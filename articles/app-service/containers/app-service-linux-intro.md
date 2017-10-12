---
title: "Общие сведения о веб-приложении Azure для контейнеров | Документация Майкрософт"
description: "Сведения о веб-приложении Azure для контейнеров."
keywords: "служба приложений azure, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: bdfaf0cd7d4bb44b877a9f14891e540fd789b6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-web-app-for-containers"></a>Общие сведения о веб-приложении Azure для контейнеров

[Веб-приложение](../app-service-web-overview.md) — это полностью управляемая вычислительная платформа, оптимизированная для размещения веб-сайтов и веб-приложений. Клиенты могут использовать веб-приложение Azure для контейнеров, чтобы размещать веб-приложения из поддерживаемых стеков приложений изначально в Linux. В следующем разделе перечислены поддерживаемые в настоящее время стеки приложений.

## <a name="languages"></a>Языки

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>Развернутые приложения

* FTP
* локальный репозиторий Git;
* GitHub
* Bitbucket;

## <a name="devops"></a>Разработка и операции

* Промежуточные среды
* [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) и непрерывная интеграция и непрерывное развертывание DockerHub.

## <a name="console-publishing-and-debugging"></a>Консоль, публикация и отладка

* средами;
* Развернутые приложения
* базовая консоль;
* SSH

## <a name="scaling"></a>Масштабирование

* Клиенты могут масштабировать веб-приложения, изменяя уровень [плана службы приложений](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Расположения

Проверьте [панель мониторинга состояния Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Ограничения

На портале Azure отображаются только те функции, которые уже доступны в веб-приложении для контейнеров. Дополнительные функции будут отображаться на портале по мере добавления.

Некоторые функции, такие как интеграция виртуальной сети, аутентификация Azure Active Directory, сторонняя аутентификация или расширения сайтов Kudu, в настоящее время недоступны. После того, как эти функции станут доступны, мы обновим нашу документацию и блог, добавив информацию об изменениях.

Веб-приложение для контейнеров поддерживается только в рамках планов службы приложений уровня ["Базовый" и "Стандартный"](https://azure.microsoft.com/pricing/details/app-service/plans/). Уровни ["Бесплатный" и "Общий"](https://azure.microsoft.com/pricing/details/app-service/plans/) недоступны. Ниже приведены важные ограничения для использования веб-приложения для контейнеров:

* Вы не можете создавать веб-приложения для контейнеров в рамках плана службы приложений, в котором уже размещены веб-приложения под управлением платформ, отличных от Linux.
* При создании веб-приложений для контейнеров в группе ресурсов, содержащей веб-приложения под управлением платформ, отличных от Linux, необходимо создать план службы приложений в другом регионе, отличном от региона с существующим планом службы приложений.

## <a name="troubleshooting"></a>Устранение неполадок

Если не удается запустить приложение или необходимо проверить ведение журнала приложения, просмотрите журналы Docker в каталоге LogFiles. Доступ к этому каталогу можно получить либо на сайте SCM, либо через FTP.
Чтобы записать `stdout` и `stderr` из контейнера, необходимо включить **ведение журнала контейнера Docker** в разделе **Журналы диагностики**.

![Включение ведения журнала][2]

![Просмотр журналов Docker с помощью Kudu][1]

Получить доступ к сайту SCM можно, щелкнув **Дополнительные средства** в меню **Средства разработки**.

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите следующие материалы, чтобы приступить к работе со службой приложений в Linux. Если у вас возникли вопросы, опубликуйте их на [нашем форуме](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Использование пользовательского образа Docker Hub для веб-приложений Azure для контейнеров](quickstart-custom-docker-image.md)
* [Создание веб-приложения .NET Core в контейнере Linux в Azure](quickstart-dotnetcore.md)
* [Создание приложения Ruby с помощью веб-приложений на платформе Linux](quickstart-ruby.md)
* [Вопросы и ответы о платформе "Веб-приложения для контейнеров" в службе приложений Azure](app-service-linux-faq.md)
* [Поддержка SSH для веб-приложения Azure для контейнеров](app-service-linux-ssh-support.md)
* [Настройка промежуточных сред в службе приложений Azure](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Непрерывное развертывание с использованием платформы Azure "Веб-приложения для контейнеров"](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
