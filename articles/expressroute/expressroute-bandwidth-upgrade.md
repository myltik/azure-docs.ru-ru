<properties 
   pageTitle="Динамическое обновление пропускной способности ExpressRoute | Microsoft Azure"
   description="Динамическое увеличение пропускной способности канала ExpressRoute без простоя."
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# Обновите пропускную способность канала ExpressRoute динамически и без простоя

Можно увеличить размер канала ExpressRoute без простоя. Эти инструкции помогут вам обновить пропускную способность канала ExpressRoute с помощью PowerShell. Более подробные сведения об ограничениях см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md).

##  Предварительные требования для настройки

Перед началом настройки убедитесь, что выполнены следующие предварительные требования.

- Подписка Azure
- Последняя версия Azure PowerShell
- Активный канал ExpressRoute настроен и работает


##  Настройка параметров с помощью PowerShell

Windows PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Дополнительные сведения см. в документации по PowerShell в [библиотеке MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Импортируйте модуль PowerShell для ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Получение сведений о канале ExpressRoute**

	Вы можете получить сведения о канале ExpressRoute с помощью следующего командлета PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Эта команда возвращает список всех каналов, созданных в подписке. Чтобы получить подробные сведения о конкретных каналах ExpressRoute, можно использовать следующую команду, если у вас есть ключ службы:

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


3. **Увеличение пропускной способности канала ExpressRoute со стороны Майкрософт**
	
	Параметры пропускной способности, поддерживаемые для вашего поставщика, см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала. Решив, какой размер вам необходим, вы можете использовать следующую команду для изменения размера канала.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Размер вашего канала будет изменен со стороны Майкрософт. Обратите внимание, что с этого момента мы начнем тарифицировать обновленный параметр пропускной способности.

4. **Увеличение пропускной способности канала ExpressRoute со стороны поставщика услуг**

	Обратитесь к поставщику услуг подключения (NSP / EXP) и предоставьте ему сведения об обновлении пропускной способности. Для завершения задачи выполните процесс обновления заказа согласно указаниям поставщика услуг.

 

<!---HONumber=July15_HO4-->