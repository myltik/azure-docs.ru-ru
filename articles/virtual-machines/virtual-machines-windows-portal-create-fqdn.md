---
title: "Создание FQDN для виртуальной машины Windows на портале Azure | Документация Майкрософт"
description: "Узнайте, как создать полное доменное имя (FQDN) для виртуальной машины на основе Resource Manager на портале Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 102/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 746ed119239d5a0a71a0663e81d9c5b259354419
ms.openlocfilehash: e89e5769701f00c6f403bd54cb2b29bcce53bf15


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Создание полного доменного имени на портале Azure
При создании виртуальной машины на [портале Azure](https://portal.azure.com) с применением модели развертывания с помощью Resource Manager для нее автоматически создается ресурс общедоступного IP-адреса. Этот IP-адрес используется для удаленного доступа к данной виртуальной машине. Несмотря на то, что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(FQDN) по умолчанию, его можно создать после создания виртуальной машины. В этой статье показан процесс создания DNS-имени или полного доменного имени.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Теперь вы можете удаленно подключиться к виртуальной машине с помощью DNS-имени, например, используя протокол удаленного рабочего стола (RDP).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у виртуальной машины имеется общедоступный IP-адрес и DNS-имя, можно развернуть общие программные платформы или службы, такие как IIS, SQL или SharePoint.

Изучите дополнительные сведения об [использовании Resource Manager](../azure-resource-manager/resource-group-overview.md), чтобы получить советы по созданию развертываний Azure.




<!--HONumber=Jan17_HO4-->


