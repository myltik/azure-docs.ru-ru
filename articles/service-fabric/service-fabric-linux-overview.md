<properties
   pageTitle="Azure Service Fabric в Linux | Microsoft Azure"
   description="Кластеры Service Fabric поддерживает Linux и Java. Это значит, что вы сможете развернуть и разместить приложения Service Fabric, написанные на языке Java в Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="SubramaR"/>

# Service Fabric в Azure

Предварительная версия станет общедоступной 26 сентября, как было объявлено [в этой записи блога](https://azure.microsoft.com/blog/service-fabric-on-linux-support-available-this-month/). Предварительная версия Service Fabric для Linux дает возможность создавать, развертывать высокодоступные приложения с высокой масштабируемостью и управлять ими в Linux так же, как и в Windows. Кроме того, теперь высокоуровневые платформы Service Fabric (Reliable Services и Reliable Actors) доступны для программирования на языке Java в Linux.

> [AZURE.VIDEO service-fabric-linux-preview]

## Поддерживаемые операционные системы и языки программирования

Ограниченная предварительная версия поддерживает создание универсальных кластеров для разработки, а также многомашинных кластеров в Azure под управлением Ubuntu Server 16.04.

Можно создавать [гостевые исполняемые службы](service-fabric-deploy-existing-app.md) с помощью любого языка или платформы. Кроме того, в дополнение к координированию контейнеров Docker, можно использовать Java или C# для создания служб на основе платформ Reliable Services и Reliable Actors.

>[AZURE.NOTE] Платформа Reliable Collections пока не поддерживается в Linux.

Служба Service Fabric в Linux в принципе эквивалентна Service Fabric в Windows (за исключением особенностей ОС и поддержки языков программирования). Поэтому большая часть нашей [имеющейся документации](http://aka.ms/servicefabricdocs) пригодна для того, чтобы ознакомиться с этой технологией.

## Дальнейшие действия

Ознакомьтесь с платформами программирования [Reliable Actors](service-fabric-reliable-actors-introduction.md) и [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0921_2016-->