---
title: Настройка параметров DNS в файле конфигурации службы | Документация Майкрософт
description: Настройка пользовательских параметров DNS с помощью файла конфигурации службы для виртуальной сети
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 76e2e4736ed6d69946aa5f42312554e1a346e076
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Настройка параметров DNS в файле конфигурации службы
## <a name="dns-elements"></a>Элементы DNS
Файл конфигурации службы может содержать элемент DnsServers со списком IPv4-адресов для серверов доменных имен (DNS-серверов), которые будет использовать служба. Параметры файла конфигурации службы имеют приоритет над параметрами файла конфигурации сети. Дополнительные сведения см. в статье, посвященной [схеме конфигурации службы Azure (файл CSCFG)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Элемент NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Атрибут **name** в элементе **DnsServer** используется только в качестве ссылочного имени. Он не представляет имя узла для DNS-сервера. Каждое значение атрибута **DnsServer** должно быть уникальным во всей подписке Microsoft Azure.
> 
> 

## <a name="see-also"></a>См. также
[Схема конфигурации службы Azure (CSCFG-файл)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Схема настройки виртуальной сети Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Настройка виртуальной сети с помощью файлов конфигурации сети](http://go.microsoft.com/fwlink/?LinkId=248094)

[Параметры виртуальной сети на портале управления](http://go.microsoft.com/fwlink/?LinkId=248092)

