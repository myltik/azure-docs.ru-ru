---
title: Описание вариантов поддержки Azure Service Fabric | Документация Майкрософт
description: Поддерживаемые версии кластера Service Fabric и ссылки для отправки запросов в службу поддержки.
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/14/2018
ms.author: pkc
ms.openlocfilehash: 7a00b72f0b8d52ae0ced5f44784c2a61d40c8ac2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208963"
---
# <a name="azure-service-fabric-support-options"></a>Варианты поддержки Azure Service Fabric

Чтобы обеспечить надлежащую поддержку кластеров Service Fabric, выполняющих рабочие нагрузки ваших приложений, мы подготовили для вас различные варианты. Вы можете выбрать соответствующие варианты в зависимости от нужного уровня поддержки и серьезности проблемы. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Сообщение о проблемах рабочей среды или запрос платной поддержки для Azure

Чтобы сообщить о проблемах в кластере Service Fabric, развернутом в Azure, откройте запрос в службу поддержки [на портале Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) или [портале службы поддержки Майкрософт](http://support.microsoft.com/oas/default.aspx?prid=16146).

См. также:
 
- [Поддержка корпорации Майкрософт для клиентов Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Поддержка Premier](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Сообщение о проблемах рабочей среды или запрос платной поддержки для изолированных кластеров Service Fabric

Чтобы сообщить о проблемах в кластере Service Fabric, развернутом в локальной среде или облаке стороннего производителя, откройте запрос в профессиональную службу поддержки на [портале службы поддержки Майкрософт](http://support.microsoft.com/oas/default.aspx?prid=16146).

См. также:

- [Professional Support Options](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0) (Варианты профессиональной поддержки).
- [Поддержка Premier](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Сообщение о проблемах Azure Service Fabric 
Мы настроили репозиторий GitHub для сообщения о проблемах Service Fabric.  Кроме того, мы активно наблюдаем за приведенными ниже форумами.

### <a name="github-repo"></a>Репозиторий GitHub 
Сообщайте о проблемах Azure Service Fabric посредством [репозитория GitHub Service-Fabric-issues](https://github.com/Azure/service-fabric-issues). Этот репозиторий предназначен для сообщения о проблемах в Azure Service Fabric и их отслеживания, а также для выполнения запросов небольших функций. **Не используйте его для сообщения о проблемах в работе активных сайтов**.

### <a name="stackoverflow-and-msdn-forums"></a>Сайт StackOverflow и форум MSDN
[Тег Service Fabric на сайте StackOverflow][stackoverflow] и [форум MSDN по Service Fabric][msdn-forum] очень удобно использовать, чтобы задавать вопросы о принципах работы платформы и о том, как выполнять различные задачи с ее помощью.

### <a name="azure-feedback-forum"></a>Форум отзывов и предложений по Azure
[Форум отзывов и предложений по Azure по Service Fabric][uservoice-forum] — лучший форум, на котором можно поделиться идеями о важных функциях для продукта, так как мы рассматриваем наиболее популярные запросы в процессе среднесрочного и долгосрочного планирования. Мы рекомендуем вам обращаться с предложениями в службу поддержки в этом сообществе.


## <a name="supported-service-fabric-versions"></a>Поддерживаемые версии Service Fabric

Обязательно следите за тем, чтобы кластер всегда работал под управлением поддерживаемой версии Service Fabric. Когда мы объявляем о выпуске новой версии Service Fabric, для предыдущей версии определяется срок завершения жизненного цикла. Этот срок составляет по меньшей мере 60 дней. О доступности новых выпусков сообщается в [блоге группы разработчиков Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/),

Сведения о том, как обеспечить управление кластером поддерживаемой версией Service Fabric, приведены в следующих документах.

- [Обновление кластера Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade your standalone Service Fabric cluster on Windows Server](service-fabric-cluster-upgrade-windows-server.md) (Обновление изолированного кластера Service Fabric на платформе Windows Server)
 
Ниже приведен список поддерживаемых версий Service Fabric и их даты окончания поддержки.

| **Среда выполнения Service Fabric в кластере** | **Обновление непосредственно версии кластера** |**Совместимые версии пакета SDK и NuGet** | **Дата окончания поддержки** |
| --- | --- |--- | --- |
| Все версии кластера до 5.3.121 | 5.1.158* |Не выше версии 2.3 |20 января 2017 г. |
| 5.3.* | 5.1.158.* |Не выше версии 2.3 |24 февраля 2017 г. |
| 5.4.* | 5.1.158.* |Не выше версии 2.4 |10 мая 2017 г.       |
| 5.5.* | 5.4.164.* |Не выше версии 2.5 |10 августа 2017 г.    |
| 5.6.* | 5.4.164.* |Не выше версии 2.6 |13 октября 2017 г.   |
| 5.7.* | 5.4.164.* |Не выше версии 2.7 |15 декабря 2017 г.  |
| 6.0.* | 5.6.205.* |Не выше версии 2.8 |30 марта 2018 г.     | 
| 6.1.* | 5.7.221.* |Не выше версии 3.0 |15 июля 2018 г.      |
| 6.2.* | 6.0.232.* |Не выше версии 3.1 |Текущая версия, дата окончания поддержки не определена. |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Предварительные версии Service Fabric не поддерживаются для использования в рабочей среде.
Время от времени мы выпускаем версии с важными функциями, доступными в виде предварительных версий, отзывы по которым мы хотим получить. Эти предварительные версии следует использовать только для целей тестирования. На производственном кластере всегда должна быть запущена поддерживаемая стабильная версия Service Fabric. Предварительная версия всегда начинается с номера основной и вспомогательной версии, равного 255. Например, если отображается версия Service Fabric 255.255.5703.949, она является предварительной и ее следует использовать в тестовых кластерах. О выходе этих предварительных выпусков также объявляется в [блоге группы Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric) и приводятся сведения о функциях, включенных в их состав.

Возможность платной технической поддержки для этих предварительных выпусков отсутствует. Чтобы задать вопросы или отправить отзыв, воспользуйтесь одной из возможностей в разделе [Сообщение о проблемах Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues).

## <a name="next-steps"></a>Дополнительная информация

- [Обновление кластера Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Upgrade your standalone Service Fabric cluster on Windows Server](service-fabric-cluster-upgrade-windows-server.md) (Обновление изолированного кластера Service Fabric на платформе Windows Server)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
