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
   ms.date="03/29/2016"
   ms.author="SubramaR"/>

# Service Fabric в Azure

Сейчас доступна ограниченная предварительная версия Service Fabric для Linux, которая дает возможность создавать, развертывать высокодоступные приложения с высокой масштабируемостью и управлять ими в этой среде так же, как и в Windows. Кроме того, теперь высокоуровневые платформы Service Fabric (Reliable Services и Reliable Actors) могут создаваться на языке Java.

## Поддерживаемые операционные системы и языки программирования

Ограниченная предварительная версия поддерживает создание универсальных кластеров для разработки, а также многомашинных кластеров в Azure под управлением Ubuntu Server 15.10.

Можно создавать [гостевые исполняемые службы](service-fabric-deploy-existing-app.md) с помощью любого языка или платформы. Кроме того, можно использовать Java для создания служб на основе платформ Reliable Services и Reliable Actors.

>[AZURE.NOTE] Платформа Reliable Collections пока не поддерживается в Java.

## Участие в использовании предварительной версии

Если вы заинтересованы в участии в программе использования ограниченной предварительной версии, [заполните форму опроса](http://aka.ms/sflinuxsurvey), чтобы мы могли лучше понять ваш сценарий и требования. Период использования предварительной версии будет очень небольшим и со временем будет продлен.

Обратите внимание, что Service Fabric в Linux концептуально не отличается от того, что доступно в Windows (за исключением особенностей ОС и поддержки языков программирования), поэтому вам подойдет большая часть нашей [имеющейся документации](http://aka.ms/servicefabricdocs), которая поможет ознакомиться с этой технологией.

## Дальнейшие действия

Ознакомление с платформами программирования [Reliable Actors](service-fabric-reliable-actors-introduction.md) и [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0406_2016-->