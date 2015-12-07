<properties 
   pageTitle="Приступая к работе по созданию балансировщика нагрузки для Интернета по классической модели развертывания для облачных служб | Microsoft Azure"
   description="Сведения о создании балансировщика нагрузки для Интернета по классической модели развертывания для облачных служб"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/03/2015"
   ms.author="joaoma" />

# Приступая к работе по созданию балансировщика нагрузки для Интернета для облачных служб

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]В этой статье рассматривается классическая модель развертывания. Вы также можете [узнать, как создать балансировщик нагрузки для Интернета с помощью диспетчера ресурсов Azure](load-balancer-get-started-internet-arm-cli.md).

Облачные службы автоматически настраиваются с помощью подсистемы балансировки нагрузки. Их также можно настроить, используя модель службы.

Вы можете обновить облачную службу с помощью пакета Azure SDK для .NET 2.5. Параметры конечных точек для облачных служб создаются в файле [service definition](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef.

В следующем примере показано, как настроить файл servicedefinition.csdef для облачного развертывания.

В фрагменте CSDEF-файла, созданного путем облачного развертывания, можно увидеть внешнюю конечную точку, настроенную для использования портов HTTP через порты 10000, 10001 и 10002.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### Проверка состояния работоспособности подсистемы балансировки нагрузки для облачных служб


Пример зонда работоспособности:

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

Балансировщик нагрузки объединяет информацию о конечной точке и информацию о пробе для создания URL-адреса в виде http://{DIP of VM}:80/Probe.aspx, который можно использовать для запроса на проверку работоспособности службы.

Служба обнаруживает периодические зонды с одного и того же IP-адреса. Это запрос зонда работоспособности, поступающий от узла, на котором запущена виртуальная машина. Служба должна отправить ответ с кодом состояния HTTP 200, что подтверждает для подсистемы балансировки нагрузки, что служба работоспособна. Любой другой код состояния HTTP (например, 503) непосредственно выводит виртуальную машину из ротации.

С помощью определения зонда можно контролировать частоту отправки зондов. В описанном выше случае подсистема балансировки нагрузки зондирует конечную точку каждые 5 секунд. Если в течение 10 секунд не приходит положительный ответ (два интервала зонда), предполагается, что зонд завершился сбоем, и виртуальная машина перестает использоваться. Аналогично, если получен положительный ответ для службы, которая не используется, сразу же выполняется ее автоматический запуск. Если состояние службы постоянно меняется (с рабочего на нерабочее и наоборот), подсистема балансировки нагрузки может отложить повторный запуск службы до получения определенного количества ответов, подтверждающих ее работоспособность.

Чтобы получить дополнительную информацию о [зонде работоспособности](https://msdn.microsoft.com/library/azure/jj151530.aspx), см. схему определения службы.

## Дальнейшие действия

[Приступая к настройке внутренней подсистемы балансировки нагрузки](load-balancer-internal-getstarted.md)

[Настройка режима распределения подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)

[Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1125_2015-->