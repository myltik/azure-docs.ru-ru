---
title: "Дальнейшие действия по созданию проекта Service Fabric | Документация Майкрософт"
description: "Дополнительные сведения о проекте приложения, созданного в Visual Studio.  Сведения о создании службы с помощью учебников и Дополнительные сведения о разработке службы для службы структуры."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 17eb1e7c2184fe9cae19685a47ea80716292b754
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>Ваше приложение Service Fabric и дальнейшие действия
Ваше приложение Azure Service Fabric создано. В этой статье описаны некоторые учебники на пробное использование, план проекта, некоторые дополнительные сведения, которые могут быть интересны и возможные дальнейшие действия.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Приступая к работе с учебники, руководства и образцы
Готовы начать работу?  

Проработать учебник приложений .NET. Узнайте, как [построение приложения](service-fabric-tutorial-create-dotnet-app.md) с ASP.NET Core переднего плана и с отслеживанием состояния серверной части, [развертывание приложения](service-fabric-tutorial-deploy-app-to-party-cluster.md) в кластер [настройки элемента конфигурации/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), и [Настройка мониторинг и диагностика](service-fabric-tutorial-monitoring-aspnet.md).

Или, попробуйте один из следующих экземпляров и создайте первое...
- [C# надежного на службы Windows](service-fabric-reliable-services-quick-start.md) 
- [C# службы Reliable Actor службы в Windows](service-fabric-reliable-actors-get-started.md) 
- [Служба гостевого исполняемого файла в Windows](quickstart-guest-app.md) 
- [Приложение-контейнер Windows](service-fabric-get-started-containers.md) 

Кроме того, может заинтересовать ознакомление нашей [образцы приложений](http://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Есть вопросы или отзывы?  Необходимо сообщить о проблеме?
Прочтите [часто задаваемые вопросы](service-fabric-common-questions.md) и найти ответы на Service Fabric, которые можно сделать и как его использовать.

[Варианты поддержки](service-fabric-support.md) перечислены форумы на StackOverflow и MSDN по запросам вопросы, а также параметры для сообщить о возникших проблемах, получение поддержки и отправки отзыва о продукте.

## <a name="the-application-project"></a>Проект приложения
Каждое новое приложение включает проект приложения. В зависимости от типа выбранной службы также могут быть один или два дополнительных проекта.

Проект приложения включает:

* Набор ссылок на службы, входящие в состав приложения.
* Три профиля публикации (локальный 1-узловой, локальный 5-узловой и облачный), которые можно использовать для хранения настроек для работы в различных средах, например параметров конечной точки кластера и параметра, определяющего, следует ли разворачивать обновления по умолчанию.
* Три файла с параметрами приложения (те же, что и выше), которые можно использовать для хранения конфигураций приложения для разных сред, включая число разделов, которые создаются для службы. Узнайте, как [настройки приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).
* Сценарий развертывания, который можно использовать для развертывания приложения из командной строки или в составе автоматизированного конвейера непрерывной интеграции и развертывания. Дополнительные сведения о [развертывание приложений с помощью PowerShell](service-fabric-deploy-remove-applications.md).
* Манифест приложения, описывающий приложение. Манифест можно найти в папке ApplicationPackageRoot. Дополнительные сведения о [манифестов приложения и службы](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Дополнительные сведения о модели программирования
Service Fabric предлагает несколько способов записи и управления службами.  Ниже приведен обзор и концептуальные сведения [надежных служб без отслеживания состояния и с отслеживанием состояния](service-fabric-reliable-services-introduction.md), [службы Reliable Actor](service-fabric-reliable-actors-introduction.md), [контейнеры](service-fabric-containers-overview.md), [гостевой исполняемые файлы ](service-fabric-deploy-existing-app.md), и [без сохранения состояния и с отслеживанием состояния службы ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Дополнительные сведения о взаимодействии службы
Приложение Service Fabric состоит из различных служб, где каждая служба выполняет специализированной задачи. Эти службы могут взаимодействовать друг с другом и могут быть клиентских приложений вне кластера, подключения и взаимодействия со службами. Узнайте, как [Настройка обмена данными с, а также между служб](service-fabric-connect-and-communicate-with-services.md) в Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Дополнительные сведения о настройке безопасности приложения
Можно защитить приложения, запущенные в кластере с разными учетными записями. Кроме того, платформа помогает защищать ресурсы, используемые приложениями при развертывании с помощью учетных записей, например файлы, каталоги и сертификаты. Это позволяет изолировать друг от друга выполняемые приложения, даже если они запущены в общей среде.  Узнайте, как [Настройка политик безопасности для приложения](service-fabric-application-runas-security.md).

Приложения могут содержать конфиденциальные сведения, например строки подключения хранилища, пароли и другие значения, которые не должны обрабатываться в виде обычного текста. Узнайте, как [управление секретами в приложении](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Дополнительные сведения о жизненном цикле приложения
Как с другими платформами приложения Service Fabric обычно проходит через следующие этапы: проектирования, разработки, тестирования, развертывания, обновления, обслуживания и удаления. [В этой статье](service-fabric-application-lifecycle.md) общие интерфейсы API и как они используются в различных ролях этапах жизненного цикла приложения Service Fabric.

## <a name="next-steps"></a>Дальнейшие действия
- [Создать в Azure кластер Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Визуализация кластера, включая развернутых приложений и физическую структуру с [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Версии и улучшать свои службы](service-fabric-application-upgrade-tutorial.md)


