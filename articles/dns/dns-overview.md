---
title: "Обзор Azure DNS | Документация Майкрософт"
description: "Обзор служб размещения DNS в Microsoft Azure. Размещение домена в Microsoft Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 87122cb432f90573fdb0a35943ce14c8d62e84a5
ms.openlocfilehash: f8ccf5c0fab1e4aca85b22b99a1a5b48f35dfcbc

---

# <a name="azure-dns-overview"></a>Обзор Azure DNS

Служба доменных имен, или DNS, отвечает за преобразование (или разрешение) имени веб-сайта или службы в IP-адрес. Azure DNS является службой размещения для доменов DNS, предоставляющей разрешение имен с помощью инфраструктуры Microsoft Azure. Размещая домены в Azure, вы можете управлять своими записями DNS с помощью тех же учетных данных, API и инструментов и оплачивать использование, как и другие службы Azure.

Домены DNS в Azure DNS размещаются в глобальной сети DNS-серверов Azure. Мы используем адресацию любому устройству сети, чтобы на каждый запрос DNS отвечал ближайший доступный DNS-сервер. Это обеспечивает высокую производительность и высокий уровень доступности для вашего домена.

Служба Azure DNS работает на основе Azure Resource Manager. Таким образом она использует преимущества Resource Manager, такие как управление доступом на основе ролей, журналы аудита и блокировка ресурсов. Доменами и записями можно управлять с помощью портала Azure, командлетов Azure PowerShell и кроссплатформенного Azure CLI. Приложения, для которых необходимо автоматическое управление DNS, можно интегрировать со службой с помощью REST API и пакетов SDK.

Azure DNS в настоящее время не поддерживает приобретение доменных имен. Если вы хотите приобрести домены, вам потребуется использовать регистратор доменных имен стороннего поставщика. Регистратор обычно взимает небольшую годовую плату. Затем вы сможете разместить домены в Azure DNS, чтобы управлять записями DNS. Дополнительные сведения см. в статье [Делегирование домена в Azure DNS](dns-domain-delegation.md).

### <a name="next-steps"></a>Дальнейшие действия

[Создание зоны DNS](./dns-getstarted-create-dnszone-portal.md)




<!--HONumber=Feb17_HO2-->


