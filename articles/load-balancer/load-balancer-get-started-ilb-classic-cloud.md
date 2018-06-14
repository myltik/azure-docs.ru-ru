---
title: Создание внутренней подсистемы балансировки нагрузки для облачных служб Azure | Документация Майкрософт
description: Узнайте, как создать внутренний балансировщик нагрузки в классической модели развертывания с помощью PowerShell.
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.assetid: 57966056-0f46-4f95-a295-483ca1ad135d
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 96a7c5b21d78dcc84a32fbfc8835542d64a5d7af
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067764"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Начало работы по созданию внутреннего балансировщика нагрузки (классическая версия) для облачных служб

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Облачные службы](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Узнайте, как [выполнить эти действия с помощью модели Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Настройка внутреннего балансировщика нагрузки для облачных служб

Внутренний балансировщик нагрузки поддерживается как для виртуальных машин, так и для облачных служб. Доступ к конечной точке внутреннего балансировщика, созданной в облачной службе за пределами региональной виртуальной сети, можно будет получить только из этой облачной службы.

Конфигурацию внутреннего балансировщика нагрузки необходимо задавать во время создания первого развертывания в облачной службе, как показано в приведенном ниже примере.

> [!IMPORTANT]
> Для выполнения указанных ниже действий необходимо, чтобы виртуальная сеть была создана и готова к облачному развертыванию. Для создания внутренней балансировки нагрузки потребуется имя виртуальной сети и имя подсети.

### <a name="step-1"></a>Шаг 1

Откройте файл конфигурации службы (с расширением CSCFG) для облачного развертывания в Visual Studio и добавьте под последним элементом`</Role>`в конфигурации сети приведенный ниже раздел, чтобы создать внутреннюю балансировку нагрузки.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Добавим значения в файл конфигурации сети, чтобы показать, как он будет выглядеть. Предположим, вы создали виртуальную сеть с именем test_vnet и подсетью 10.0.0.0/24 с именем test_subnet и статическим IP-адресом 10.0.0.4. Подсистема балансировки нагрузки будет называться testLB.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Дополнительные сведения о схеме балансировщика нагрузки см. в статье [Добавление подсистемы балансировки нагрузки](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Шаг 2

Добавьте в CSDEF-файл конечные точки для подсистемы внутренней балансировки нагрузки. При создании экземпляра роли CSDEF-файл добавляет его в подсистему внутренней балансировки нагрузки.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

Добавим значения из приведенного выше примера в CSDEF-файл.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

Балансировка сетевого трафика будет осуществляться с использованием балансировщика нагрузки testLB. При этом порт 80 будет использоваться как для получения входящих запросов, так и для их отправки в экземпляры рабочих ролей.

## <a name="next-steps"></a>Дополнительная информация

[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

