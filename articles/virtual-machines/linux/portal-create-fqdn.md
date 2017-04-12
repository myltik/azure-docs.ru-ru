---
title: "Создание FQDN для виртуальной машины Linux на портале Azure | Документация Майкрософт"
description: "Узнайте, как создать полное доменное имя (FQDN) для виртуальной машины на основе Resource Manager на портале Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: afba4dbf4b70ad25cd2c8f33c9f6389bdfc74d68
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Создание полного доменного имени на портале Azure для виртуальной машины Linux

При создании виртуальной машины на [портале Azure](https://portal.azure.com) с применением модели развертывания с помощью Resource Manager для нее автоматически создается ресурс общедоступного IP-адреса. Этот IP-адрес используется для удаленного доступа к данной виртуальной машине. Несмотря на то, что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)по умолчанию, его можно добавить после создания виртуальной машины. В этой статье показан процесс создания DNS-имени или полного доменного имени.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Теперь вы можете удаленно подключиться к виртуальной машине с помощью DNS-имени, например `ssh ops@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда у виртуальной машины имеется общедоступный IP-адрес и DNS-имя, можно развернуть общие программные платформы или службы, например nginx, MongoDB, Docker и т. д.

Изучите дополнительные сведения об [использовании Resource Manager](../../azure-resource-manager/resource-group-overview.md), чтобы получить советы по созданию развертываний Azure.


