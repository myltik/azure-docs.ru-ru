---
title: Шаблоны и сценарии использования Azure Service Fabric | Документация Майкрософт
description: Практические рекомендации по применению проверенных шаблонов (с возможностью повторного использования) для проектирования, разработки и эксплуатации микрослужб в Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 5c2cd38001a9645011bbab84cff3b9366aa64b1d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206146"
---
# <a name="service-fabric-patterns-and-scenarios"></a>Шаблоны и сценарии использования Service Fabric
Если вам требуется создать крупномасштабные микрослужбы с помощью Azure Service Fabric, воспользуйтесь опытом специалистов, которые разработали и создали эту платформу как услугу (PaaS). Начните с правильной архитектуры, а затем узнайте, как оптимизировать ресурсы для приложения. Курс [Service Fabric Patterns and Practices](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344) (Шаблоны Service Fabric и практические рекомендации) содержит ответы на самые распространенные вопросы, задаваемые реальными пользователями о сценариях использования и областях применения Service Fabric.
 
Узнайте, как проектировать, разрабатывать и эксплуатировать микрослужбы в Service Fabric, применяя рекомендации и проверенные шаблоны с возможностью повторного использования. Получите общие сведения о Service Fabric, а затем подробнее изучите разделы, посвященные оптимизации и безопасности кластера, переносу устаревших приложений, масштабированию в Интернете вещей, размещению игровых ядер и другим темам. Рассмотрите процесс непрерывной доставки для различных рабочих нагрузок, а также узнайте полезные сведения о контейнерах и поддержке Linux. 

## <a name="introduction"></a>Введение
Рекомендации и общие сведения о выборе между платформой как услугой (PaaS) и инфраструктурой как услугой (IaaS). Ознакомьтесь со следующими проверенными принципами проектирования приложений.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Общие сведения о Service Fabric</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>Планирование кластера и управление им
Получите сведения о планировании ресурсов, оптимизации и безопасности кластера в контексте Azure Service Fabric.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">Планирование кластера и управление им</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>Гипермасштабируемые веб-службы
Ознакомьтесь с основными понятиями гипермасштабируемых веб-служб, включая доступность и надежность, гипермасштабирование и управление состоянием.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">Гипермасштабируемые веб-службы</a></td></tr>
</table>

## <a name="iot"></a>Интернет вещей
Ознакомьтесь с возможностями Интернета вещей в контексте Azure Service Fabric, включая конвейер Интернета вещей Azure, мультитенантность и масштабирование в Интернете вещей.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Интернет вещей</a></td></tr>
</table>

## <a name="gaming"></a>Игры
Рассмотрите пошаговые и интерактивные игры, а также узнайте о размещении существующих игровых ядер.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">Игры</a></td></tr>
</table>

## <a name="continuous-delivery"></a>Непрерывная доставка
Ознакомьтесь с основными понятиями, такими как непрерывная интеграция и непрерывная доставка с помощью Visual Studio Team Services, рабочий процесс сборки/пакета/публикации, настройка в нескольких средах, а также пакет и общая папка службы.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">Непрерывная доставка</a></td></tr>
</table>

## <a name="migration"></a>Миграция
Сведения о переносе из облачной службы и о переносе устаревших приложений.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">Миграция</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>Контейнеры и поддержка Linux
Получите ответ на вопрос "Зачем нужны контейнеры?" Сведения о предварительных версиях функций контейнеров Windows, поддержки Linux и оркестрации контейнеров Linux. Узнайте, как выполнить перенос приложений .NET Core на Linux.

<table><tr><th>Видео</th><th>В формате PowerPoint</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">Контейнеры и поддержка Linux</a></td></tr>
</table>

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы изучили шаблоны и сценарии использования Service Fabric, узнайте больше о [создании кластеров и управлении ими](service-fabric-deploy-anywhere.md), [переносе приложений облачных служб в Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md), [настройке непрерывной доставки](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) и [развертывании контейнеров](service-fabric-containers-overview.md).
