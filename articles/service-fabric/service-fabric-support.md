---
title: "Описание вариантов поддержки Azure Service Fabric | Документация Майкрософт"
description: "Поддерживаемые версии кластера Service Fabric и ссылки для отправки запросов в службу поддержки."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/10/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 44e95f78b5fe592713570e53f2469c88202a02aa
ms.lasthandoff: 03/11/2017


---
# <a name="azure-service-fabric-support-options"></a>Варианты поддержки Azure Service Fabric

Чтобы обеспечить надлежащую поддержку кластеров Service Fabric, выполняющих рабочие нагрузки ваших приложений, мы подготовили для вас различные варианты. Вы можете выбрать соответствующие варианты в зависимости от нужного уровня поддержки и серьезности проблемы. 


<a id="getlivesitesupportonazure"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-azure"></a>Сообщение о проблемах рабочей среды или активного сайта либо запрос платной поддержки для Azure

Чтобы сообщить о проблемах активного сайта в кластере Service Fabric, развернутом в Azure, откройте запрос в профессиональную службу поддержки [на портале Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) или [портале службы поддержки Майкрософт](http://support.microsoft.com/oas/default.aspx?prid=16146).

См. также:
 
- [Поддержка Azure для клиентов](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Поддержка Premier](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Сообщение о проблемах рабочей среды или активного сайта либо запрос платной поддержки для изолированных кластеров Service Fabric

Чтобы сообщить о проблемах активного сайта в кластере Service Fabric, развернутом в локальной среде или облаке стороннего производителя, откройте запрос в профессиональную службу поддержки [портале службы поддержки Майкрософт](http://support.microsoft.com/oas/default.aspx?prid=16146).

См. также:

- [Professional Support Options](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0) (Варианты профессиональной поддержки).
- [Поддержка Premier](https://support.microsoft.com/en-us/premier).


<a id="getsupportonissues"></a>
## <a name="report-azure-service-fabric-issues"></a>Сообщение о проблемах Azure Service Fabric 
Мы настроили репозиторий GitHub для сообщения о проблемах Service Fabric.  Кроме того, мы активно наблюдаем за приведенными ниже форумами.

### <a name="github-repo"></a>Репозиторий GitHub 
Сообщайте о проблемах Azure Service Fabric посредством [репозитория GitHub Service-Fabric-issues](https://github.com/Azure/service-fabric-issues). Этот репозиторий предназначен для сообщения о проблемах в Azure Service Fabric и их отслеживания, а также для выполнения запросов небольших функций. **Не используйте его для сообщения о проблемах в работе активных сайтов**.

### <a name="stackoverflow-and-msdn-forums"></a>Сайт StackOverflow и форум MSDN

[Тег Service Fabric на сайте StackOverflow][stackoverflow] и [форум MSDN по Service Fabric][msdn-forum] очень удобно использовать, чтобы задавать вопросы о принципах работы платформы и о том, как выполнять различные задачи с ее помощью.

### <a name="azure-feedback-forum"></a>Форум отзывов и предложений по Azure

[Форум отзывов и предложений по Azure по Service Fabric][uservoice-forum] — лучший форум, на котором можно поделиться идеями о важных функциях для продукта, так как мы рассматриваем наиболее популярные запросы в процессе среднесрочного и долгосрочного планирования. Мы рекомендуем вам обращаться с предложениями в службу поддержки в этом сообществе.


<a id="releasesuport"></a>
## <a name="supported-service-fabric-versions"></a>Поддерживаемые версии Service Fabric

Обязательно следите за тем, чтобы кластер всегда работал под управлением поддерживаемой версии Service Fabric. Когда мы объявляем о выпуске новой версии Service Fabric, для предыдущей версии определяется срок завершения жизненного цикла. Этот срок составляет по меньшей мере 60 дней. О доступности новых выпусков сообщается в [блоге группы разработчиков Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/),

Сведения о том, как обеспечить управление кластером поддерживаемой версией Service Fabric, приведены в следующих документах.

- [Обновление кластера Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade your standalone Service Fabric cluster on Windows Server](service-fabric-cluster-upgrade-windows-server.md) (Обновление изолированного кластера Service Fabric на платформе Windows Server)
 
Ниже приведен список поддерживаемых версий Service Fabric и их даты окончания поддержки.

| **Кластер среды выполнения Service Fabric** | **Дата окончания поддержки** |
| --- | --- |
| Все версии кластера до 5.3.121 |20 января 2017 г. |
| 5.3.* |24 февраля 2017 г. |
| 5.4.* |10 мая 2017 г.     |
| 5.5.* |Текущая версия, дата окончания поддержки не определена.


## <a name="next-steps"></a>Дальнейшие действия

- [Обновление кластера Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade your standalone Service Fabric cluster on Windows Server](service-fabric-cluster-upgrade-windows-server.md) (Обновление изолированного кластера Service Fabric на платформе Windows Server)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples

