---
title: "Общие сведения о ведении журналов потоков для групп безопасности сети с помощью Наблюдателя за сетями | Документация Майкрософт"
description: "На этой странице объясняется, как использовать возможности журналов потоков NSG, доступные в Наблюдателе за сетями Azure."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4eaffba08ccf601e440709d804891668340a376d
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Общие сведения о ведении журнала потоков для групп безопасности сети

Журналы потоков для групп безопасности сети — это компонент Наблюдателя за сетями, который позволяет просматривать сведения о входящем и исходящем IP-трафике через группу безопасности сети. Эти журналы потоков записываются в формате JSON. В них отображаются входящие и исходящие потоки по каждому правилу, сетевая карта, с которой связан поток, сведения о 5 кортежах потока (исходный и конечный IP-адреса, исходный и конечный порты, протокол), а также сведения о состоянии трафика (разрешен или запрещен).

![Обзор журналов потоков][1]

Хотя журналы потоков нацелены на группы безопасности сети, они не отображаются так же, как другие журналы. Журналы потоков хранятся только в пределах учетной записи хранения и используют путь для ведения журнала, как показано в следующем примере.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

К журналам потоков применяются те же политики хранения, что и к другим журналам. Для журналов действует политика хранения, в которой можно задать период от 1 до 365 дней. Если политика хранения не задана, то журналы сохраняются неограниченно долго.

## <a name="log-file"></a>Файл журнала

Журналы потоков имеют несколько свойств. Ниже приведен список свойств, которые возвращаются в журнале потоков NSG.

* **time** — время занесения события в журнал.
* **systemId** — идентификатор ресурса группы безопасности сети.
* **category** — категории события. Всегда будет иметь значение NetworkSecurityGroupFlowEvent.
* **resourceid** — идентификатор ресурса NSG.
* **operationName** — всегда имеет значение NetworkSecurityGroupFlowEvents.
* **properties** — набор свойств потока.
    * **Version** — номер версии схемы событий журнала потоков.
    * **flows** — набор потоков. Это свойство имеет несколько записей для различных ролей.
        * **rule** — правило, для которого отображены потоки.
            * **flows** — набор потоков.
                * **mac** — MAC-адрес сетевой карты виртуальной машины, в которой был получен поток.
                * **flowTuples** — строка с разделителями-запятыми, содержащая несколько свойств кортежа потока.
                    * **Time Stamp** — это значение представляет собой метку времени возникновения потока в формате UNIX EPOCH.
                    * **Source IP** — исходный IP-адрес.
                    * **Destination IP** — конечный IP-адрес.
                    * **Source Port** — исходный порт.
                    * **Destination Port** — конечный порт.
                    * **Protocol** — протокол потока. Допустимые значения: **T** для протокола TCP и **U** для протокола UDP.
                    * **Traffic Flow** — направление потока трафика. Допустимые значения: **I** для входящего трафика и **O** для исходящего трафика.
                    * **Traffic** — указывает, разрешен или запрещен трафик. Допустимые значения: **A**, если трафик разрешен, и **D**, если запрещен.


Ниже приведен пример журнала потоков. Как вы видите, он содержит несколько записей, соответствующих списку свойств, описанных в предыдущем разделе. 

> [!NOTE]
> Значения в свойстве flowTuples имеют вид списка с разделителями-запятыми.
 
```json
{
    "records": 
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как включить журналы потоков, ознакомившись с разделом [Включение журналов потоков](network-watcher-nsg-flow-logging-portal.md).

Дополнительные сведения о ведении журнала NSG см. в разделе [Аналитика журналов для групп безопасности сети](../virtual-network/virtual-network-nsg-manage-log.md).

Сведения о том, как узнать, разрешен или запрещен трафик на виртуальной машине, см. в разделе [Проверка состояния входящего и исходящего трафика виртуальной машины (разрешен или запрещен) путем проверки IP-потока (компонент Наблюдателя за сетями Azure)](network-watcher-check-ip-flow-verify-portal.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png

