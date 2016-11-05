---
title: Создание полного доменного имени для виртуальной машины на портале Azure | Microsoft Docs
description: Узнайте, как создать полное доменное имя (FQDN) для виртуальной машины на основе диспетчера ресурсов на портале Azure.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/23/2016
ms.author: iainfou

---
# Создание полного доменного имени на портале Azure
При создании виртуальной машины на [портале Azure](https://portal.azure.com) с применением модели развертывания с помощью Resource Manager для нее автоматически создается ресурс общедоступного IP-адреса. Этот IP-адрес используется для удаленного доступа к данной виртуальной машине. Несмотря на то, что портал не создает [полное доменное имя](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) по умолчанию, его можно добавить после создания виртуальной машины. В этой статье показан процесс создания DNS-имени или полного доменного имени.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Теперь вы можете удаленно подключиться к виртуальной машине с помощью DNS-имени, например `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## Дальнейшие действия
Теперь, когда у виртуальной машины имеется общедоступный IP-адрес и DNS-имя, можно развернуть общие программные платформы или службы, например nginx, MongoDB, Docker и т. д.

Вы можете также прочитать дополнительные сведения об [использовании Resource Manager](../resource-group-overview.md), чтобы получить советы по созданию развернутых служб Azure.

<!---HONumber=AcomDC_0824_2016-->