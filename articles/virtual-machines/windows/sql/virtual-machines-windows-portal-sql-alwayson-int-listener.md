---
title: "Создание прослушивателя для групп доступности SQL Server в службе виртуальных машин Azure | Документация Майкрософт"
description: "Пошаговые инструкции по созданию прослушивателя для группы доступности AlwaysOn для SQL Server в службе виртуальных машин Azure."
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Настройка внутренней подсистемы балансировки нагрузки для группы доступности AlwaysOn в Azure
В этой статье описывается, как создать внутреннюю подсистему балансировки нагрузки для группы доступности AlwaysOn для SQL Server в службе виртуальных машин Azure, развернутой с помощью модели Resource Manager. Группе доступности нужен балансировщик нагрузки, если экземпляры SQL Server находятся на виртуальных машинах Azure. Балансировщик нагрузки хранит IP-адрес для прослушивателя группы доступности. Если группа доступности распространяется на несколько регионов, для каждого из них нужен отдельный балансировщик.

Для этого необходимо развернуть группу доступности SQL Server на виртуальных машинах Azure с использованием модели Resource Manager. Обе виртуальные машины SQL Server должны принадлежать к одной и той же группе доступности. Вы можете использовать [шаблон Майкрософт](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , чтобы автоматически создать группу доступности в Azure Resource Manager. Этот шаблон также автоматически создает внутренний балансировщик нагрузки. 

При необходимости [группу доступности можно настроить вручную](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Здесь предполагается, что группы доступности уже настроены.  

Связанные разделы включают:

* [Введение в группы доступности AlwaysOn SQL Server на виртуальных машинах Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Настройка подключения между виртуальными сетями с помощью Azure Resource Manager и PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Последовательно выполнив все действия в этом документе, вы создадите и настроите подсистему балансировки нагрузки на портале Azure. После этого вы настроите в кластере IP-адрес подсистемы балансировки для прослушивателя группы доступности.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Создание и настройка балансировщика нагрузки на портале Azure
В этом разделе мы сделаем на портале Azure следующее:

1. Создадим балансировщик нагрузки и настроим IP-адрес.
2. Настроим серверный пул.
3. Создадим пробу. 
4. Настроим правила балансировки нагрузки.

> [!NOTE]
> Если серверы SQL Server расположены в разных группах ресурсов и регионах, необходимо выполнить все эти действия в каждой группе ресурсов.
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Создание балансировщика нагрузки и настройка IP-адреса
Сначала необходимо создать балансировщик нагрузки. На портале Azure откройте группу ресурсов, в которой содержатся виртуальные машины SQL Server. В колонке группы ресурсов щелкните **Добавить**.

1. Найдите **балансировщик нагрузки**. В результатах поиска выберите **Балансировщик нагрузки**, опубликованный корпорацией **Майкрософт**.
1. В колонке **Балансировщик нагрузки** щелкните **Создать**.
1. В колонке **Create load balancer**(Создание балансировщика нагрузки) настройте балансировщик, задав следующие параметры:

   | Параметр | Значение |
   | --- | --- |
   | **Имя** |Текст, представляющий имя балансировщика нагрузки. Например, **sqlLB**. |
   | **Тип** |**Внутренний** |
   | **Виртуальная сеть** |Выберите виртуальную сеть, в которой расположены серверы SQL Server. |
   | **Подсеть** |Выберите подсеть, в которой расположены серверы SQL Server. |
   | **Назначение IP-адресов** |**Статическое** |
   | **Частный IP-адрес** |Укажите свободный IP-адрес из нужной подсети. Используйте этот IP-адрес при создании прослушивателя в кластере. Далее вам понадобится использовать этот адрес для переменной `$ILBIP` в сценарии PowerShell. |
   | **Подписка** |Это поле может появиться, если у вас несколько подписок. Выберите подписку, которую требуется связать с этим ресурсом. Обычно это подписка, с которой связаны все ресурсы группы доступности. |
   | **Группа ресурсов** |Выберите группу ресурсов, в которой расположены серверы SQL Server. |
   | **Расположение** |Выберите расположение Azure, в котором находятся серверы SQL Server. |

1. Щелкните **Создать**. 

Azure создаст подсистему балансировки нагрузки. Балансировщик нагрузки относится к конкретной сети, подсети, группе ресурсов и расположению. После создания балансировщика нагрузки проверьте его параметры в Azure. 

### <a name="2-configure-the-backend-pool"></a>2. Настройка серверного пула
Следующий шаг — создание серверного пула адресов. В Azure серверный пул адресов называется просто *серверным пулом*. В нашем случае — это адреса двух серверов SQL Server в группе доступности. 

1. В группе ресурсов щелкните созданную подсистему балансировки нагрузки. 
1. В колонке **Параметры** щелкните **Серверные пулы**.
1. На вкладке **Серверные пулы** щелкните **Добавить**, чтобы создать серверный пул адресов. 
1. В колонке **Добавить внутренний пул** under **Имя**введите имя серверного пула.
1. В разделе **Виртуальные машины** щелкните **+ Добавить виртуальную машину**. 
1. В разделе **Выбор виртуальных машин** щелкните **Выберите группу доступности** и укажите группу доступности, к которой принадлежат виртуальные машины SQL Server.
1. Выбрав группу доступности, щелкните **Выберите виртуальные машины**. Щелкните две виртуальные машины, на которых размещены экземпляры SQL Server в группе доступности. Нажмите кнопку **Выбрать**. 
1. Нажмите кнопку **ОК**, чтобы закрыть колонки **Выбор виртуальных машин** и **Добавить внутренний пул**. 

После этого Azure обновит параметры для серверного пула адресов. Теперь в вашей группе доступности есть пул из двух серверов SQL Server.

### <a name="3-create-a-probe"></a>3. Создание пробы
Теперь необходимо создать пробу. Проба определяет, как Azure будет проверять, какому из серверов SQL Server в данный момент принадлежит прослушиватель группы доступности. Azure проверяет службу по IP-адресу и порту, которые вы указываете при создании пробы.

1. В колонке **Параметры** для подсистемы балансировки нагрузки щелкните **Health probes** (Пробы работоспособности). 
1. В колонке **Health probes** (Пробы работоспособности) щелкните **Добавить**.
1. Настройте пробу в колонке **Добавление пробы** . Для настройки пробы используйте следующие значения:

   | Настройка | Значение |
   | --- | --- |
   | **Имя** |Текст, представляющий имя пробы. Например, **SQLAlwaysOnEndPointProbe**. |
   | **Протокол** |**TCP** |
   | **Порт** |Вы можете использовать любой доступный порт. Например, *59999*. |
   | **Интервал** |*5* |
   | **Пороговое значение сбоя** |*2* |

1.  Нажмите кнопку **ОК**. 

> [!NOTE]
> Указанный порт должен быть открыт в брандмауэре обоих серверов SQL Server. Для обоих серверов требуется правило для входящего трафика используемого TCP-порта. Дополнительные сведения см. в статье [Добавление и изменение правила брандмауэра](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure создаст пробу. С ее помощью Azure будет проверять, на каком сервере SQL Server есть прослушиватель для группы доступности.

### <a name="4-set-the-load-balancing-rules"></a>4. Настройка правил балансировки нагрузки
Настройте правила балансировки нагрузки. Правила балансировки нагрузки определяют способ направления трафика балансировщиком нагрузки на серверах SQL Server. Для этой подсистемы балансировки включите прямой ответ от сервера, так как одновременно только один из двух серверов SQL Server может содержать ресурс прослушивателя группы доступности.

1. В колонке **Параметры** для балансировщика нагрузки щелкните **Правила балансировки нагрузки**. 
1. В колонке **Правила балансировки нагрузки** щелкните **Добавить**.
1. Используйте колонку **Add load balancing rules** (Добавление правил балансировки нагрузки), чтобы настроить правило балансировки нагрузки. Используйте следующие параметры: 

   | Настройка | Значение |
   | --- | --- |
   | **Имя** |Текст, представляющий имя правила балансировки нагрузки. Например, **SQLAlwaysOnEndPointListener**. |
   | **Протокол** |**TCP** |
   | **Порт** |*1433* |
   | **Серверный порт** |*1433*. Это значение игнорируется, поскольку правило использует **плавающий IP-адрес (прямой ответ от сервера)**. |
   | **Проба** |Используйте имя пробы, созданной для этого балансировщика нагрузки. |
   | **Сохранение сеанса** |**None** |
   | **Время ожидания простоя (в минутах)** |*4* |
   | **Плавающий IP-адрес (прямой ответ от сервера)** |**Включено** |

   > [!NOTE]
   > Возможно, потребуется прокрутить колонку вниз, чтобы просмотреть все параметры.
   > 

1. Нажмите кнопку **ОК**. 
1. Azure настроит правило балансировки нагрузки. Теперь балансировщик нагрузки настроен для маршрутизации трафика на сервер SQL Server, на котором размещается прослушиватель для группы доступности. 

На этом этапе группа ресурсов содержит подсистему балансировки нагрузки, через которую происходит подключение к обеим виртуальным машинам SQL Server. Подсистема балансировки также содержит IP-адрес прослушивателя группы доступности AlwaysOn для SQL Server. Таким образом, любая виртуальная машина может отвечать на запросы для групп доступности.

> [!NOTE]
> Если серверы SQL Server находятся в разных регионах, повторите эти действия еще раз в другом регионе. Балансировщик нагрузки необходим для каждого региона. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Настройка использования IP-адреса балансировщика нагрузки для кластера
Теперь необходимо настроить прослушиватель в кластере и подключить его. Сделайте следующее: 

1. Создайте прослушиватель группы доступности в отказоустойчивом кластере. 
2. Подключите прослушиватель.

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. Создание прослушивателя группы доступности в отказоустойчивом кластере
На этом шаге вручную создается прослушиватель группы доступности в диспетчере отказоустойчивости кластеров и службе SQL Server Management Studio (SSMS).

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>Проверка конфигурации прослушивателя

Если ресурсы и зависимости кластера настроены правильно, вы увидите прослушиватель в SQL Server Management Studios. Чтобы задать порт прослушивателя, сделайте следующее:

1. Запустите SQL Server Management Studio и подключитесь к основной реплике.
2. Перейдите в раздел **Высокий уровень доступности AlwaysOn** | **Группы доступности** | **Прослушиватели группы доступности**. 
1. Теперь вы увидите имя прослушивателя, созданного в диспетчере отказоустойчивости кластеров. Щелкните правой кнопкой мыши имя прослушивателя и выберите **Свойства**.
1. В поле **Порт** укажите номер порта для прослушивателя группы доступности с помощью использованного ранее параметра $EndpointPort (по умолчанию использовалось значение&1433;) и нажмите кнопку **ОК**.

Теперь у вас есть группа доступности AlwaysOn для SQL Server в службе виртуальных машин Azure, развернутой с помощью модели Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Проверка подключения к прослушивателю
Чтобы проверить подключение:

1. Подключитесь по протоколу RDP к серверу SQL Server, который находится в той же виртуальной сети, но не содержит реплику. Это может быть другой сервер SQL Server в кластере.
2. Для проверки подключения используйте служебную программу **sqlcmd** . Например, в следующем сценарии **sqlcmd** подключается к основной реплике через прослушиватель с использованием аутентификации Windows.
   
        sqlcmd -S <listenerName> -E

sqlcmd автоматически подключается к любому экземпляру SQL Server, на котором размещена основная реплика. 

## <a name="guidelines-and-limitations"></a>Рекомендации и ограничения
Обратите внимание на следующие рекомендации относительно прослушивателя группы доступности в Azure, использующего внутренний балансировщик нагрузки Azure.

* У одной облачной службы может быть только один внутренний прослушиватель группы доступности, так как он присваивается подсистеме балансировки нагрузки, а в облачной службе может быть только одна внутренняя подсистема балансировки. Однако вы можете создать несколько внешних прослушивателей. 
* Если вы используете внутреннюю подсистему балансировки, доступ к прослушивателю можно получить только из той же виртуальной сети.


<!--HONumber=Jan17_HO2-->


