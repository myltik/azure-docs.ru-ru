---
title: включение файла
description: включение файла
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cff737b8c79c44494cb0151d6a6281550401b26e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805072"
---
## <a name="scenario"></a>Сценарий

Чтобы проиллюстрировать процесс создания виртуальной сети и подсетей, в этом документе будет использоваться представленный ниже сценарий.

![Сценарий виртуальной сети](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

В этом сценарии вы создадите виртуальную сеть с именем **TestVNet** и зарезервированным блоком CIDR **192.168.0.0./16**. Виртуальная сеть содержит следующие подсети: 

* **FrontEnd** с блоком **192.168.1.0/24** в качестве блока CIDR.
* **BackEnd** с блоком **192.168.2.0/24** в качестве блока CIDR.

