---
title: "Azure Service Fabric в Linux | Документация Майкрософт"
description: "Кластеры Service Fabric поддерживают Linux и Java. Это значит, что вы сможете развернуть и разместить в Linux приложения Service Fabric, написанные на языках Java и C#."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 459afade-145d-4ee6-b72b-ddf380ccd1bf
ms.service: service-fabric
ms.devlang: Java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: SubramaR
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: dddc9f698d9776999d406117b46285a0f90d9620
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---
# <a name="service-fabric-on-linux"></a>Service Fabric в Azure
Предварительная версия Service Fabric для Linux дает возможность создавать, развертывать высокодоступные приложения с высокой масштабируемостью и управлять ими в Linux так же, как и в Windows. Платформы Service Fabric (Reliable Services и Reliable Actors) в Linux доступны для программирования на языках Java и C# (.NET Core).  Также можно создавать [гостевые исполняемые службы](service-fabric-deploy-existing-app.md) , используя любой язык или платформу. Кроме того, предварительная версия поддерживает оркестрацию контейнеров Docker. В контейнерах Docker могут выполняться гостевые исполняемые файлы или собственные службы Service Fabric, использующие платформы Service Fabric.

Служба Service Fabric в Linux в принципе эквивалентна Service Fabric в Windows (за исключением особенностей ОС и поддержки языков программирования). Поэтому большая часть нашей [имеющейся документации](http://aka.ms/servicefabricdocs) пригодна для того, чтобы ознакомиться с этой технологией.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Service-Fabric-Linux-Preview/player]
>
>

## <a name="supported-operating-systems-and-programming-languages"></a>Поддерживаемые операционные системы и языки программирования
Ограниченная предварительная версия поддерживает создание универсальных кластеров для разработки, а также многомашинных кластеров в Azure под управлением Ubuntu Server 16.04. Помимо гостевых исполняемых файлов и оркестрации контейнеров Docker предварительная версия поддерживает платформы Reliable Actors и Reliable Stateless Services на языках Java и C#.  

> [!NOTE]
> Платформа Reliable Collections пока не поддерживается в Linux. Автономные кластеры тоже не поддерживаются. В предварительной версии поддерживаются только универсальные кластеры, а также кластеры Azure из нескольких компьютеров под управлением Linux.
>
>


## <a name="supported-tooling"></a>Поддерживаемые средства
В предварительной версии взаимодействие с кластером осуществляется через интерфейс командной строки Service Fabric. Для разработчиков Java интеграция с Eclipse и Yeoman доступна при помощи среды Eclipse, поддерживаемой в Linux и OS X. В интеграции с OS X используется виртуальная машина под управлением Linux (через Vagrant). Разработчики C# могут использовать интеграцию с Yeoman для создания шаблонов приложений.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с платформами программирования [Reliable Actors](service-fabric-reliable-actors-introduction.md) и [Reliable Services](service-fabric-reliable-services-introduction.md).
* [Подготовка среды разработки в Linux](service-fabric-get-started-linux.md)
* [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md)
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-java.md)
* [Настройка непрерывных интеграции и развертывания с помощью Jenkins и GitHub](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
* [Различия между Service Fabric для Linux (предварительная версия) и Windows (общедоступная версия)](service-fabric-linux-windows-differences.md)

