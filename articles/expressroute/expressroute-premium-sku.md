<properties 
   pageTitle="Включение и отключение надстройки ExpressRoute Premium | Microsoft Azure"
   description="Включение и отключение надстройки ExpressRoute Premium для канала ExpressRoute ExpressRoute Premium позволяет добавить к каналу ExpressRoute до 10 000 маршрутов для общедоступных и частных сеансов и до 10 виртуальных сетей. Можно также связать виртуальную сеть в одном регионе с каналом ExpressRoute в другом."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# Настройка надстройки ExpressRoute Premium для канала ExpressRoute

ExpressRoute Premium — это набор функций, перечисленных ниже.

 - Увеличенное ограничение таблицы маршрутизации с 4000 маршрутов до 10 000 маршрутов для общедоступного пиринга и частного пиринга.
 - Увеличенное число виртуальных сетей (VNet), которые можно подключить к каналу ExpressRoute (по умолчанию — 10). 
 - Глобальные подключения через основную сеть Microsoft. Теперь можно связать виртуальную сеть в одном геополитическом регионе с каналом ExpressRoute в другом регионе. **Пример.** Вы можете связать виртуальную сеть, созданную в Западной Европе, с каналом ExpressRoute, созданным в Кремниевой долине.

Более подробные сведения о надстройке ExpressRoute Premium см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md). Стоимость см. на странице [Сведения о ценах](http://azure.microsoft.com/pricing/details/expressroute/).

Приведенные ниже инструкции помогут вам сделать следующее:

- создать канал ExpressRoute с включенной надстройкой ExpressRoute Premium;
- обновить имеющийся канал ExpressRoute для включения надстройки ExpressRoute Premium;
- отключить надстройку ExpressRoute Premium для канала.


## Создание канала ExpressRoute с включенной надстройкой ExpressRoute Premium

###  Перед началом работы

Перед началом настройки убедитесь, что выполнены следующие предварительные требования.

- Подписка Azure
- Последняя версия Azure PowerShell

###  1\. Импорт модуля PowerShell для ExpressRoute

Windows PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Дополнительные сведения см. в документации по PowerShell в [библиотеке MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

Используйте следующий командлет, чтобы импортировать модуль PowerShell для ExpressRoute.


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2\. Настройка нового канала ExpressRoute с включенной надстройкой ExpressRoute Premium

Можно создать новый канал ExpressRoute с надстройкой ExpressRoute Premium, включенной во время создания. Следуйте инструкциям по созданию канала ExpressRoute с помощью [NSP](expressroute-configuring-nsps.md) или [EXP](expressroute-configuring-exps.md). У нас есть новый необязательный параметр в командлете New-AzureDedicatedCircuit, который позволяет указать SKU. Значением SKU может быть Standard или Premium. Значение по умолчанию: Standard. Передача в качестве SKU значения Premium включит канал с надстройкой Premium.


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3\. Проверка включения надстройки ExpressRoute Premium
Можно проверить, включена ли надстройка ExpressRoute Premium для канала. В следующем примере надстройка ExpressRoute Premium для канала ExpressRoute не включена. Значение SKU будет отображаться как ***Premium***, если надстройка включена.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## Включение надстройки ExpressRoute Premium для имеющегося канала ExpressRoute
Можно включить надстройку ExpressRoute Premium для любого уже созданного канала ExpressRoute.


1. **Получение сведений о канале ExpressRoute**

	Вы можете получить сведения о канале ExpressRoute с помощью следующего командлета PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Эта команда возвращает список всех каналов, созданных в подписке. Чтобы получить подробные сведения о конкретных каналах ExpressRoute, можно использовать следующую команду, если у вас есть ключ службы

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Замените <skey> действующим ключом службы.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **Включение надстройки ExpressRoute Premium для канала**


	Вы можете включить надстройку ExpressRoute Premium для существующего канала с помощью следующего командлета PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	Теперь для вашего канала включена надстройка ExpressRoute Premium. Обратите внимание, что с момента успешного выполнения команды мы начнем тарифицировать надстройку Premium.


## Выключение надстройки ExpressRoute Premium для канала ExpressRoute

Можно отключить включенную надстройку ExpressRoute Premium для канала ExpressRoute.

**Примечание**. Перед отключением необходимо убедиться в том, что вы перестали использовать функции, перечисленные в списке компонентов надстройки Premium. Ваш запрос на отключение надстройки Premium не будет удовлетворен, если с вашим каналом связано больше 10 виртуальных сетей. Кроме того, ваши сеансы BGP будут сброшены при объявлении более 5000 маршрутов после отключения надстройки Premium.

1. **Получение сведений о канале ExpressRoute**

	Вы можете получить сведения о канале ExpressRoute с помощью следующего командлета PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Эта команда возвращает список всех каналов, созданных в подписке. Чтобы получить подробные сведения о конкретных каналах ExpressRoute, можно использовать следующую команду, если у вас есть ключ службы

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Замените <skey> действующим ключом службы.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **Выключение надстройки ExpressRoute Premium для канала**


	Вы можете выключить надстройку ExpressRoute Premium для существующего канала с помощью следующего командлета PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	Теперь надстройка ExpressRoute Premium для вашего канала выключена.


 

<!---HONumber=August15_HO6-->