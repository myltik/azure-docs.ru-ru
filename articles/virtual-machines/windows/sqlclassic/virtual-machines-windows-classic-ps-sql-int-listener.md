---
title: Настройка прослушивателя внутреннего балансировщика нагрузки для групп доступности AlwaysOn в Azure | Документация Майкрософт
description: В этом руководстве используются ресурсы, созданные в классической модели развертывания, а также создается прослушиватель группы доступности AlwaysOn в Azure, использующий внутренний балансировщик нагрузки.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0466265ad5a24e8ea6dc5079e2b4006d74e7dde0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240754"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Настройка прослушивателя внутреннего балансировщика нагрузки для групп доступности AlwaysOn в Azure
> [!div class="op_single_selector"]
> * [Внутренний прослушиватель](../classic/ps-sql-int-listener.md)
> * [Внешний прослушиватель](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>Обзор

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Azure Resource Manager и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний рекомендуется использовать модель Resource Manager.

Дополнительные сведения о настройке прослушивателя для группы доступности AlwaysOn в модели Resource Manager см. в [этой статье](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

В группе доступности могут быть реплики, доступные только локально или только в Azure. В гибридных конфигурациях возможны оба способа доступа одновременно. Реплики в Azure могут находиться в одном или нескольких регионах, использующих несколько виртуальных сетей. В процедурах, приведенных в этой статье, предполагается, что вы уже [настроили группу доступности](../classic/portal-sql-alwayson-availability-groups.md), но еще не настроили прослушиватель.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Рекомендации и ограничения для внутренних прослушивателей
Обратите внимание на следующие рекомендации относительно использования внутреннего балансировщика нагрузки с прослушивателем группы доступности в Azure.

* Прослушиватель группы доступности поддерживается в Windows Server 2008 R2, Windows Server 2012 и Windows Server 2012 R2.
* Для каждой облачной службы поддерживается только один внутренний прослушиватель группы доступности, так как он настраивается только на использование ILB, а в облачной службе только один ILB. Однако можно создать несколько внешних прослушивателей. Дополнительные сведения см. в статье [Настройка внешнего прослушивателя для групп доступности AlwaysOn в Azure](../classic/ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Определение доступности прослушивателя
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

В этой статье рассматривается создание прослушивателя, использующего внутренний балансировщик нагрузки. Если вам требуется общедоступный (внешний) прослушиватель, см. другую версию этой статьи, где приведены инструкции по настройке [внешнего прослушивателя](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Создание конечных точек балансировки нагрузки в ВМ со службой Direct Server Return
Сначала создайте внутренний балансировщик нагрузки, запустив скрипт (следуя указаниям далее в этом разделе).

Создайте конечную точку с балансировкой нагрузки для каждой виртуальной машины с репликой Azure. Все реплики одного региона должны быть в одной облачной службе и одной виртуальной сети Azure. Чтобы создать реплики группы доступности, охватывающие несколько регионов Azure, необходимо настроить несколько виртуальных сетей. Дополнительные сведения о настройке подключения между виртуальными сетями см. в [этой статье](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. Для просмотра сведений на портале Azure перейдите к каждой виртуальной машине, на которой размещена реплика.

2. Перейдите на вкладку **Конечные точки** для каждой виртуальной машины.

3. Убедитесь, что **имя** и **общий порт** необходимой конечной точки прослушивателя еще не используются. В примере, рассматриваемом в этом разделе, используется имя *MyEndpoint* и порт *1433*.

4. На локальном клиенте скачайте и установите [последнюю версию модуля PowerShell](https://azure.microsoft.com/downloads/).

5. Запустите Azure PowerShell.  
    Откроется новый сеанс PowerShell с загруженными административными модулями Azure.

6. Запустите `Get-AzurePublishSettingsFile`. Он перенаправит вас в браузер для скачивания файла параметров публикации в локальный каталог. Может потребоваться ввод учетных данных для входа в подписку Azure.

7. Выполните следующую команду `Import-AzurePublishSettingsFile`, указав путь к скачанному файлу параметров публикации:

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    После импорта файла параметров публикации вы сможете управлять подпиской Azure в сеансе PowerShell.

8. Для *балансировщика нагрузки* вам потребуется назначить статический IP-адрес. Изучите текущую конфигурацию виртуальной сети, выполнив следующую команду:

        (Get-AzureVNetConfig).XMLConfiguration
9. Обратите внимание на имя *Subnet* для подсети, содержащей виртуальные машины с репликами. Это имя присваивается параметру $SubnetName в скрипте.

10. Обратите внимание на имя *VirtualNetworkSite* и начальный префикс подсети *AddressPrefix*, содержащей виртуальные машины с репликами. Определите доступные IP-адреса. Для этого передайте оба значения в команду `Test-AzureStaticVNetIP` и изучите значение *AvailableAddresses*. Например, если виртуальная сеть называется *MyVNet*, а диапазон адресов подсети начинается с *172.16.0.128*, следующая команда выведет список доступных адресов:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. Выберите один из доступных адресов и используйте его в качестве значения параметра $ILBStaticIP в приведенном ниже скрипте.

12. Скопируйте приведенный ниже скрипт PowerShell в текстовый редактор и задайте подходящие для среды значения переменных. Для некоторых параметров указаны значения по умолчанию.  

    Вы не сможете добавить внутренний балансировщик нагрузки к имеющимся развернутым службам, использующим территориальные группы. Дополнительные сведения о требованиях внутреннего балансировщика нагрузки см. в статье [Обзор внутренней подсистемы балансировки нагрузки](../../../load-balancer/load-balancer-internal-overview.md).

    Если ваша группа доступности охватывает несколько регионов Azure, приведенный сценарий нужно будет запустить в каждом центре данных для всех облачных служб и узлов, которые находятся в этом центре.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. Присвоив значения переменным, скопируйте скрипт из текстового редактора в текущий сеанс PowerShell и выполните его. Если в командной строке отображается **>>**, нажмите клавишу ВВОД еще раз, чтобы начать выполнение скрипта.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Проверка наличия пакета KB2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Открытие портов брандмауэра в узлах групп доступности.
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Создание прослушивателя группы доступности

Создайте прослушиватель группы доступности в два этапа. Сначала создайте кластерный ресурс точки доступа клиента и настройте зависимости. Затем настройте кластерные ресурсы в PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Создание точки доступа клиента и настройка кластерных зависимостей
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Настройка кластерных ресурсов в PowerShell
1. Чтобы использовать внутренний балансировщик нагрузки, вам потребуется указать IP-адрес ранее созданного внутреннего балансировщика нагрузки. Этот IP-адрес можно получить в PowerShell, выполнив следующий скрипт:

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. Войдите на одну из виртуальных машин и скопируйте скрипт PowerShell для операционной системы в текстовый редактор. Затем присвойте переменным записанные ранее значения.

    Для Windows Server 2012 или более поздней версии используйте следующий скрипт:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Для Windows Server 2008 R2 используйте следующий скрипт:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. Присвоив значения переменным, откройте окно Windows PowerShell с повышенными правами. Вставьте скрипт из текстового редактора в текущий сеанс PowerShell и выполните скрипт. Если в командной строке отображается **>>**, нажмите клавишу ВВОД еще раз, чтобы начать выполнение скрипта.

4. Повторите предыдущие действия для каждой виртуальной машины.  
    Этот скрипт настраивает ресурс IP-адреса путем установки IP-адреса облачной службы и прочих параметров, таких как порт зонда. После подключения ресурс IP-адреса сможет отвечать на запросы, отправляемые на порт зонда из созданной ранее конечной точки балансировки нагрузки.

## <a name="bring-the-listener-online"></a>Подключите прослушиватель.
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Дальнейшие действия
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>Проверка прослушивателя группы доступности (в пределах одной виртуальной сети)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Дополнительная информация
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
