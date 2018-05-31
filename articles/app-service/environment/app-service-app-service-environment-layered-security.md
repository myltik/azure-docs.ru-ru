---
title: Многоуровневая архитектура безопасности в средах службы приложений
description: Реализация многоуровневой архитектуры безопасности в средах службы приложений.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.openlocfilehash: 29c928c7d81eb3a2532f735be9132b49db5da373
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356211"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Реализация многоуровневой архитектуры безопасности со средами службы приложений
## <a name="overview"></a>Обзор
Среды службы приложений предоставляют изолированную среду выполнения, развернутую в виртуальной сети. Поэтому разработчики могут создавать многоуровневую архитектуру безопасности, предусматривающую разные уровни доступа к сети для каждого физического уровня приложений.

Основная цель заключается в том, чтобы сделать невозможным общий доступ к внутреннему приложению API из Интернета, разрешив вызывать API только вышестоящими веб-приложениями.  В подсетях, содержащих среды службы приложений, могут использоваться [группы безопасности сети][NetworkSecurityGroups] для ограничения общего доступа к приложениям API.

На приведенной ниже схеме показан пример архитектуры с приложением на основе веб-API, развернутым в среде службы приложений.  Три отдельных экземпляра веб-приложения, развернутые в трех отдельных средах службы приложений, выполняют внутренний вызов одного приложения веб-API.

![Концепция архитектуры][ConceptualArchitecture] 

Знак зеленого плюса указывает на то, что группа безопасности сети разрешает выполнять в подсети, содержащей apiase, входящие вызовы от вышестоящих веб-приложений, а также вызовы от самого приложения.  При этом та же группа безопасности сети явным образом запрещает доступ к общему входящему трафику из Интернета. 

В оставшейся части этой статьи описаны шаги по настройке группы безопасности сети в подсети, содержащей apiase.

## <a name="determining-the-network-behavior"></a>Определение поведения сети
Чтобы узнать требуемые правила сетевой безопасности, вам необходимо определить, какие клиенты сети смогут обращаться к среде службы приложений, содержащей приложение API, а какие — будут заблокированы.

[Группы безопасности сети][NetworkSecurityGroups] применяются к подсетям, в которых также развертываются среды службы приложений. Поэтому содержащиеся в группе безопасности сети правила применяются ко **всем** приложениям, выполняющимся в среде службы приложений.  Использование приведенного в этой статье примера архитектуры предполагает следующее. После того как группа безопасности сети будет применена к подсети, содержащей apiase, все приложения, выполняющиеся в среде службы приложений apiase, будут защищены этим же набором правил безопасности. 

* **Определение исходящего IP-адреса вышестоящих вызывающих объектов.** Какой IP-адрес или какие IP-адреса у вышестоящих вызывающих объектов?  Эти адреса нужны для явного разрешения доступа в NSG.  Вызовы между средами службы приложений считаются интернет-вызовами, поэтому исходящему IP-адресу, назначенному каждой из трех вышестоящих сред службы приложений, в NSG должен быть разрешен доступ для подсети apiase.   Дополнительные сведения об определении исходящего IP-адреса для приложений, выполняемых в среде службы приложений, см. в статье [Общие сведения об архитектуре сетевых сред службы приложений][NetworkArchitecture].
* **Необходимо ли внутреннему приложению API вызывать самого себя?**  Есть один интересный сценарий, который часто упускают из виду: внутреннему приложению необходимо вызвать самого себя.  Если внутреннему приложению API в среде службы приложений необходимо вызвать себя же, такой вызов также будет расценен как интернет-вызов.  В нашем примере архитектуры для этого также необходимо разрешить доступ от исходящего IP-адреса среды службы приложений apiase.

## <a name="setting-up-the-network-security-group"></a>Настройка группы безопасности сети
Когда набор исходящих IP-адресов станет известен, можно переходить к созданию группы безопасности сети.  Группы безопасности сети можно создавать и для виртуальных сетей с Resource Manager, и для классических виртуальных сетей.  В приведенных ниже примерах показано создание и настройка группы безопасности сети в классической виртуальной сети с помощью Powershell.

Так как в этом примере архитектуры среды расположены в Южно-Центральном регионе США, пустая группа NSG создается в этом регионе:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Сначала для инфраструктуры управления Azure добавляется одно явно разрешающее правило, как описано в статье, посвященной [управлению входящим трафиком][InboundTraffic] в среде службы приложений.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Затем добавляются два правила, разрешающие вызовы HTTP и HTTPS из первой вышестоящей среды службы приложений (fe1ase).

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

То же самое выполняется для второй и третьей вышестоящих сред службы приложений (fe2ase и fe3ase).

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Наконец, нужно предоставить доступ для исходящего IP-адреса среды службы приложений, в которую входит внутреннее приложение API, чтобы оно смогло вызывать само себя.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Другие правила безопасности сети не требуются, так как у каждой группы NSG есть набор правил по умолчанию, блокирующий входящий доступ из Интернета по умолчанию.

Полный список правил группы безопасности сети приведен ниже.  Обратите внимание, как последнее правило (выделено) блокирует входящий доступ от всех вызывающих объектов, отличных от вызывающих объектов, которым явно предоставлен доступ.

![Конфигурация NSG][NSGConfiguration] 

Последним шагом является применение группы NSG к подсети, которая содержит среду службы приложений apiase.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

После применения группы NSG к подсети вызывать среду apiase смогут только три вышестоящие среды службы приложений, а также среда службы приложений, содержащая серверную часть API.

## <a name="additional-links-and-information"></a>Дополнительные ссылки и сведения
Информация о [группах безопасности сети](../../virtual-network/security-overview.md).

Основные сведения об [исходящих IP-адресах][NetworkArchitecture] и средах службы приложений.

[Сетевые порты][InboundTraffic], используемые в средах службы приложений.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
