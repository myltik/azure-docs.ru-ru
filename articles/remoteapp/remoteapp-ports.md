
<properties
    pageTitle="Список портов и URL-адресов для разрешения доступа к развернутым Azure RemoteApp в виртуальной сети клиента | Microsoft Azure"
    description="Узнайте, какие порты и URL-адреса необходимо настроить для связи через Azure RemoteApp."
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/11/2016"
    ms.author="elizapo" />



# Список портов и URL-адресов для обеспечения доступа к развернутым Azure RemoteApp в виртуальной сети клиента 

Следующее применимо к Azure RemoteApp, облачной или гибридной коллекции при развертывании в виртуальной сети (VNET). Дополнительные сведения о виртуальных сетях см. в статье [Обзор виртуальной сети](virtual-networks-overview.md). Если вы создали группу безопасности сети (NSG), ограничивающую трафик к ресурсам вашей виртуальной сети, которые вы выбрали для Azure RemoteApp, убедитесь, что следующее доступно и разрешено посредством политик безопасности в виртуальной сети. Дополнительные сведения о группах безопасности сети см. в разделе [Что такое группа безопасности сети? (NSG)](virtual-networks-nsg.md).

##  Подсети Azure RemoteApp требуется доступ к этим конечным точкам и URL-адресам: 
*	**.servicebus.windows.net
*	 **.servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 Исходящие: TCP: 443, TCP: 10101-10175 
*	 Необязательно — UDP: 10201-10275 
 
## Клиентам Azure RemoteApp требуется доступ к этим конечным точкам и URL-адресам: 

Под клиентами подразумеваются настольные системы, устройства и т. д., которые люди используют для подключения к приложениям, развернутым в коллекции Azure RemoteApp.

-  https://telemetry.remoteapp.windowsazure.com  
-  https://**.remoteapp.windowsazure.com (необязательные UDP-порты для этого адреса) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://**.remoteapp.windowsazure.com  
-  https://*.core.windows.net  
-  Исходящие: TCP: 443, TCP: 10101-10175 
-  Необязательно — UDP: 10201-10275

<!---HONumber=AcomDC_0114_2016-->