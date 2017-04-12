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
ms.date: 03/14/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 3feec792f906d7d0c6b12d22e1b8baa3f79a5c77
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Создание полного доменного имени на портале Azure для виртуальной машины Windows

При создании виртуальной машины на [портале Azure](https://portal.azure.com) с применением модели развертывания с помощью Resource Manager для нее автоматически создается ресурс общедоступного IP-адреса. Этот IP-адрес используется для удаленного доступа к данной виртуальной машине. Несмотря на то, что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(FQDN) по умолчанию, его можно создать после создания виртуальной машины. В этой статье показан процесс создания DNS-имени или полного доменного имени.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Теперь вы можете удаленно подключиться к виртуальной машине с помощью DNS-имени, например, используя протокол удаленного рабочего стола (RDP).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у виртуальной машины имеется общедоступный IP-адрес и DNS-имя, можно развернуть общие программные платформы или службы, такие как IIS, SQL или SharePoint.

Изучите дополнительные сведения об [использовании Resource Manager](../../azure-resource-manager/resource-group-overview.md), чтобы получить советы по созданию развертываний Azure.


