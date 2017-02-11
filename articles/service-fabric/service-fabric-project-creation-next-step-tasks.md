---
title: "Дальнейшие действия по созданию проекта Service Fabric | Документация Майкрософт"
description: "Эта статья содержит ссылки на набор основных задач разработки для Service Fabric"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4b2424e5efe3392b08e58ceb05ec63f15c7ad32


---
# <a name="your-service-fabric-application-and-next-steps"></a>Ваше приложение Service Fabric и дальнейшие действия
Ваше приложение Azure Service Fabric создано. В этой статье описываются состав проекта и некоторые возможные дальнейшие действия.

## <a name="your-application"></a>Ваше приложение
Каждое новое приложение включает проект приложения. В зависимости от типа выбранной службы также могут быть один или два дополнительных проекта.

### <a name="the-application-project"></a>Проект приложения
Проект приложения включает:

* Набор ссылок на службы, входящие в состав приложения.
* Три профиля публикации (локальный 1-узловой, локальный 5-узловой и облачный), которые можно использовать для хранения настроек для работы в различных средах, например параметров конечной точки кластера и параметра, определяющего, следует ли разворачивать обновления по умолчанию.
* Три файла с параметрами приложения (те же, что и выше), которые можно использовать для хранения конфигураций приложения для разных сред, включая число разделов, которые создаются для службы.
* Сценарий развертывания, который можно использовать для развертывания приложения из командной строки или в составе автоматизированного конвейера непрерывной интеграции и развертывания.
* Манифест приложения, описывающий приложение. Манифест можно найти в папке ApplicationPackageRoot.

### <a name="stateless-service"></a>Служба без отслеживания состояния
При добавлении новой службы без отслеживания состояния Visual Studio добавляет в решение проект службы, который содержит тип, происходящий из `StatelessService`. Служба увеличивает значение локальной переменной в счетчике.

### <a name="stateful-service"></a>Служба с отслеживанием состояния
При добавлении новой службы с отслеживанием состояния Visual Studio добавляет в решение проект службы, который содержит тип, происходящий из `StatefulService`. Служба увеличивает значение счетчика в своем методе `RunAsync` и сохраняет результат в `ReliableDictionary`.

### <a name="actor-service"></a>Служба субъекта
При добавлении нового надежного субъекта Visual Studio добавляет в решение два проекта: проект субъекта и проект интерфейса.

Проект субъекта предоставляет методы для настройки и получения значения счетчика, надежно сохраняемого в состоянии субъекта. Проект интерфейса предоставляет интерфейс, который используется другими службами для вызова субъекта.

### <a name="stateless-web-api"></a>Веб-API без отслеживания состояния
Проект веб-API без отслеживания состояния предоставляет простую веб-службу, которую можно использовать для открытия приложения во внешних клиентах. Дополнительные сведения о структуре проекта см. в разделе [Приступая к работе со службами веб-API Service Fabric с саморазмещением OWIN](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>ASP.NET core
Пакет SDK для Service Fabric предоставляет набор тех же шаблонов ASP.NET Core, которые доступны для автономных проектов ASP.NET Core. Набор включает пустой шаблон, а также шаблоны [веб-API][aspnet-webapi] и [веб-приложения][aspnet-webapp].

## <a name="next-steps"></a>Дальнейшие действия
### <a name="create-an-azure-cluster"></a>Создание кластера Azure
Пакет SDK Service Fabric предоставляет локальный кластер для разработки и тестирования. Сведения о создании кластера в Azure см. в статье [Создание кластера Service Fabric в Azure с помощью портала Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Бесплатное развертывание в Azure с использованием сторонних кластеров
Для пробного развертывания приложений и управления ими в Azure без настройки собственных кластеров можно использовать бесплатную [стороннюю службу кластеров](http://aka.ms/tryservicefabric).

### <a name="publish-your-application-to-azure"></a>Публикация изменений в Azure
Приложение в кластер Azure можно опубликовать напрямую из Visual Studio. Чтобы узнать, как это сделать, обратитесь к статье [Публикация приложения на удаленный кластер с помощью Visual Studio][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Визуализация кластера с помощью обозревателя Service Fabric
Обозреватель Service Fabric предоставляет простой способ визуализации кластера, включая развернутые приложения и физическую структуру. Дополнительные сведения см. в статье [Визуализация кластера с помощью Service Fabric Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Версии и обновление служб
Service Fabric обеспечивает независимое управление версиями и обновление независимых служб в приложении. Дополнительные сведения см. в статье [Руководство по обновлению приложений Service Fabric с помощью Visual Studio][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Настройка непрерывной интеграции с Visual Studio Team Services
Чтобы узнать, как настроить процесс непрерывной интеграции для приложения Service Fabric, см. статью [Настройка непрерывной интеграции для приложения Service Fabric с использованием Visual Studio Team Services][ci-with-vso].

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html



<!--HONumber=Nov16_HO3-->


