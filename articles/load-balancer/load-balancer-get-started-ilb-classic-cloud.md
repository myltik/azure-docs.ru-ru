<properties 
   pageTitle="Создание внутреннего балансировщика нагрузки для облачных служб в классической модели развертывания | Microsoft Azure"
   description="Узнайте, как создать внутренний балансировщик нагрузки в классической модели развертывания с помощью PowerShell."
   services="load-balancer-ilb"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Начало работы по созданию внутреннего балансировщика нагрузки (классическая версия) для облачных служб

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](load-balancer-get-started-ilb-arm-ps.md).


## Настройка внутреннего балансировщика нагрузки для облачных служб

Внутренний балансировщик нагрузки поддерживается как для виртуальных машин, так и для облачных служб. Доступ к конечной точке внутреннего балансировщика, созданной в облачной службе за пределами региональной виртуальной сети, можно будет получить только из этой облачной службы.

Конфигурацию внутреннего балансировщика нагрузки необходимо задавать во время создания первого развертывания в облачной службе, как показано в приведенном ниже примере.

>[AZURE.IMPORTANT] Для выполнения указанных ниже действий необходимо, чтобы виртуальная сеть была создана и готова к облачному развертыванию. Для создания внутренней балансировки нагрузки потребуется имя виртуальной сети и имя подсети.

### Шаг 1

Откройте файл конфигурации службы (с расширением CSCFG) для облачного развертывания в Visual Studio и добавьте под последним элементом `</Role>` в конфигурации сети приведенный ниже раздел, чтобы создать внутреннюю балансировку нагрузки.




	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="name of the load balancer">
	      <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>


Добавим значения в файл конфигурации сети, чтобы показать, как он будет выглядеть. Для примера предположим, что вы создали подсеть с именем test\_vnet и подсетью 10.0.0.0/24, имеющей имя test\_subnet и статический IP-адрес 10.0.0.4. Подсистема балансировки нагрузки будет называться testLB.

	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="testLB">
	      <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>

Дополнительные сведения о схеме балансировщика нагрузки см. в статье [Добавление подсистемы балансировки нагрузки](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### Шаг 2


Добавьте в CSDEF-файл конечные точки для подсистемы внутренней балансировки нагрузки. При создании экземпляра роли CSDEF-файл добавляет его в подсистему внутренней балансировки нагрузки.


	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
	  </Endpoints>
	</WorkerRole>

Добавим значения из приведенного выше примера в CSDEF-файл.

	<WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
	  </Endpoints>
	</WorkerRole>

Балансировка сетевого трафика будет осуществляться с использованием балансировщика нагрузки testLB. При этом порт 80 будет использоваться как для получения входящих запросов, так и для их отправки в экземпляры рабочих ролей.


## Дальнейшие действия

[Настройка режима распределения балансировщика нагрузки с помощью соответствия исходному IP-адресу](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->