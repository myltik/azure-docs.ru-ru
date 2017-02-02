---
title: "Список портов и URL-адресов для разрешения доступа к развернутым Azure RemoteApp в виртуальной сети клиента | Документация Майкрософт"
description: "Узнайте, какие порты и URL-адреса необходимо настроить для связи через Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 79f6398b861597e3303231bc17f69be1bde947ae
ms.openlocfilehash: ef052d074b593e4bbecfdbfb3af76d1ba1249e33


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Список портов и URL-адресов для обеспечения доступа к развернутым Azure RemoteApp в виртуальной сети клиента
> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Если вы развертываете облачную или гибридную коллекцию Azure RemoteApp в виртуальной сети (VNET), просмотрите приведенную ниже информацию о портах. Дополнительные сведения о виртуальных сетях см. в статье [Обзор виртуальной сети](../virtual-network/virtual-networks-overview.md). Если вы создали группу безопасности сети (NSG), ограничивающую трафик к ресурсам вашей виртуальной сети в коллекции, убедитесь, что приведенные ниже порты доступны и открыты посредством политик безопасности виртуальной сети. Дополнительные сведения о группах безопасности сети см. в разделе [Группа безопасности сети (NSG)](../virtual-network/virtual-networks-nsg.md).

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Подсети Azure RemoteApp требуется доступ к этим конечным точкам и URL-адресам:
* *.servicebus.windows.net
* *.servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* Исходящие: TCP: 443, TCP: 10101-10175 
* Необязательно — UDP: 10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Клиентам Azure RemoteApp требуется доступ к этим конечным точкам и URL-адресам:
Под клиентами подразумеваются настольные системы, устройства и т. д., которые люди используют для подключения к приложениям, развернутым в коллекции Azure RemoteApp.

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com (необязательные UDP-порты для этого адреса) 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* Исходящие: TCP: 443  
* (Необязательно) UDP: 3391 




<!--HONumber=Dec16_HO3-->


