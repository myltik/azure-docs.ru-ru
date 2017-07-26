---
title: "Параметры портов API ретранслятора Azure | Документация Майкрософт"
description: "Сведения о значениях портов ретранслятора Azure."
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
ms.date: 07/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 5906495c565dad583e74a43b2e5eed57e0c68df1
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017


---

# <a name="azure-relay-port-settings"></a>Параметры порта ретрансляции Azure

В таблице ниже описаны необходимые значения параметров портов для ретранслятора Azure.

## <a name="hybrid-connections"></a>через гибридные подключения
Гибридные подключения используют протокол WebSockets в качестве базового транспортного механизма, который использует только протокол **HTTPS**. 

## <a name="wcf-relays"></a>Ретрансляторы WCF
  
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
Дополнительные сведения о ретрансляторе Azure доступны по следующим ссылкам:
* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Вопросы и ответы по ретранслятору](relay-faq.md)
