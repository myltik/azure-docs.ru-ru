---
title: включение файла
description: включение файла
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c1e57ea28f597293d8bb52207bbbb76892b1d5f5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2018
---
## <a name="deployment-considerations"></a>Рекомендации по развертыванию

* Сведения о доступности виртуальных машин серии N по регионам см. на [этой странице](https://azure.microsoft.com/en-us/regions/services/).

* Виртуальные машины серии N поддерживают только модель развертывания с помощью Resource Manager.

* Виртуальные машины серии N отличаются типом хранилища Azure, которое поддерживается для их дисков. Виртуальные машины серий NC и NV поддерживают только диски на основе хранилища дисков уровня "Стандартный" (HDD). Виртуальные машины серий NCv2, ND и NCv3 поддерживают только диски на основе хранилища дисков уровня "Премиум" (SSD).

* Если вы хотите развернуть большое количество виртуальных машин серии N, мы рекомендуем подписку с оплатой по мере использования или другие варианты покупки. Если вы используете [бесплатную учетную запись Azure](https://azure.microsoft.com/free/), вам доступно ограниченное количество вычислительных ядер Azure.

* Возможно, вам потребуется увеличить квоту на использование ядер (для каждого региона) в подписке Azure и отдельную квоту для ядер серии NC, NCv2, NCv3, ND или NV. Чтобы увеличить квоту, [отправьте запрос в службу поддержки](../articles/azure-supportability/how-to-create-azure-support-request.md). Это бесплатная услуга. Ограничения по умолчанию отличаются в зависимости от категории подписки.




