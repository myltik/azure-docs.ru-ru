---
title: "Настройка параметров DNS в файле конфигурации службы | Документация Майкрософт"
description: "Настройка пользовательских параметров DNS с помощью файла конфигурации службы для виртуальной сети"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0fba2ea06827aff29a7a092933edb8120d668b29
ms.contentlocale: ru-ru
ms.lasthandoff: 11/17/2016

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


