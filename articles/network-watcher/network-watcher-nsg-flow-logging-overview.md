---
title: Общие сведения о ведении журналов потоков для групп безопасности сети с помощью Наблюдателя за сетями | Документация Майкрософт
description: В этой статье объясняется, как использовать возможности журналов потоков NSG, доступные в Наблюдателе за сетями Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c6a24fbca37d6aa1d775a70c708a139dfb70b813
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182431"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Общие сведения о ведении журнала потоков для групп безопасности сети

Журналы потока групп безопасности сети (NSG) являются функцией Наблюдателя за сетями, которая позволяет просматривать сведения о входящем и исходящем IP-трафике через NSG. Журналы потоков записываются в формате json и содержат исходящие и входящие потоки на основе каждого правила, сетевой интерфейс (NIC), к которому применяется поток, 5-кортежную информацию о потоке (IP-адрес источника, получателя, порт источника, назначения и протокол) и состояние трафика (разрешен или отклонен).

![Обзор журналов потоков](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Хотя журналы потоков предназначены для NSG, они не отображаются так же, как и другие журналы. Журналы потоков хранятся только в учетной записи хранения и имеют путь ведения журнала, показанный в следующем примере:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Для журналов потока применяются те же политики хранения, что и для других журналов. Вы можете установить политику хранения журнала от 1 дня до 365 дней. Если политика хранения не задана, то журналы сохраняются неограниченно долго.

## <a name="log-file"></a>Файл журнала

Журналы потока включают в себя следующие свойства:

* **time** — время занесения события в журнал.
* **systemId** — идентификатор ресурса группы безопасности сети.
* **category** — категория события. Категория — всегда **NetworkSecurityGroupFlowEvent**.
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
> Значения в свойстве **flowTuples* имеют вид списка с разделителями-запятыми.
 
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

## <a name="next-steps"></a>Дополнительная информация

- Чтобы узнать, как включить журналы потока, см. статью [Руководство по регистрации потока сетевого трафика на виртуальную машину и с нее с помощью портала Azure](network-watcher-nsg-flow-logging-portal.md).
- Дополнительные сведения о ведении журналов NSG см. в статье [Аналитика журналов для групп безопасности сети](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Дополнительные сведения об определении трафика см. в статье [Краткое руководство. Диагностика проблемы с фильтром трафика на виртуальной машине с помощью портала Azure](diagnose-vm-network-traffic-filtering-problem.md)