---
title: "Параметры порта ретрансляции WCF служебной шины Azure | Документация Майкрософт"
description: "Сведения о значениях конфигурации порта ретрансляции WCF служебной шины."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f222caa272b86ff42873df09561ca4f6c6e6aa26
ms.openlocfilehash: ba499a98960b0cf2f865e2d349003cd8cb65b526


---

# <a name="azure-relay-port-settings"></a>Параметры порта ретрансляции Azure

В следующей таблице приведены необходимые значения параметров порта для привязки ретранслятора WCF служебной шины.

## <a name="ports"></a>порты;
  
|Привязка|Безопасность доставки|Порт|  
|-------------|------------------------|----------|  
|[Класс BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (клиент)|Да|HTTPS| 
| |" |Нет|HTTP|  
|[Класс BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (клиент)|Да|9351/HTTPS|  
||" |Нет|9350/HTTP|  
|[Класс NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (служба или клиент)|Можно использовать|5671/9352/HTTP (9352/9353 при использовании гибридной среды)|  
|[Класс NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (клиент)|Да|9351/HTTPS|  
||" |Нет|9350/HTTP|  
|[Класс NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (клиент)|Да|HTTPS|  
||" |Нет|HTTP|  
|[Класс WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (служба)|Можно использовать|9351/HTTP|  
|[Класс WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (клиент)|Да|HTTPS|  
||" |Нет|HTTP|  
|[Класс WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (служба)|Можно использовать|9351/HTTP|

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию об обмене сообщениями через служебную шину см. в следующих разделах.

* [Базовая информация о Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Очереди, разделы и подписки служебной шины](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)
* [Как использовать очереди служебной шины](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)



<!--HONumber=Nov16_HO4-->


