---
title: Защита сети в центре безопасности Azure | Документация Майкрософт
description: В этом документе рассматриваются рекомендации из центра безопасности Azure, которые помогают защитить вашу сеть Azure и обеспечить соответствие политикам безопасности.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23040599"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Защита сети в центре безопасности Azure.
Центр безопасности Azure анализирует состояние безопасности ресурсов Azure. Когда центр безопасности выявляет потенциальные уязвимости в системе безопасности, он создает рекомендации по настройке необходимых элементов управления.  Рекомендации относятся к следующим типам ресурсов Azure: виртуальные машины (ВМ), сети, SQL и приложения.

В этой статье рассматриваются рекомендации, которые касаются сети.  Основное внимание в рекомендациях для сети уделено брандмауэрам следующего поколения, группам безопасности сети, настройке правил входящего трафика и другим аспектам.  В таблице ниже приведена справочная информация о доступных рекомендациях, включая действия, выполняемые при применении этих рекомендаций.

## <a name="available-network-recommendations"></a>Доступные рекомендации для сети
| Рекомендации | ОПИСАНИЕ |
| --- | --- |
| [Добавить брандмауэр следующего поколения](security-center-add-next-generation-firewall.md) |Рекомендует добавить брандмауэр следующего поколения (NGFW) от партнера корпорации Майкрософт для повышения безопасности. |
| [Route traffic through NGFW only (Маршрутизировать трафик только через NGFW)](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Рекомендует настроить правила группы безопасности сети, которые принудительно передают входящий трафик к виртуальной машине через ваш NGFW. |
| [Enable Network Security Groups on subnets or virtual machines (Включить группы безопасности сети для подсетей или виртуальных машин)](security-center-enable-network-security-groups.md) |Рекомендует включить группы безопасности сети для подсетей или виртуальных машин. |
| [Restrict access through Internet facing endpoint (Ограничить доступ через подключенную к Интернету конечную точку)](security-center-restrict-access-through-internet-facing-endpoints.md) |Рекомендует настроить правила входящего трафика для групп безопасности сети. |

## <a name="see-also"></a>См. также
Дополнительные сведения о рекомендациях, которые относятся к другим типам ресурсов Azure, см. в следующих статьях:

* [Защита виртуальных машин в центре безопасности Azure.](security-center-virtual-machine-recommendations.md)
* [Защита приложений в центре безопасности Azure.](security-center-application-recommendations.md)
* [Защита службы SQL Azure в центре безопасности Azure.](security-center-sql-service-recommendations.md)

Дополнительные сведения о Центре безопасности см. в следующих статьях:

* [Настройка политик безопасности в Центре безопасности Azure](security-center-policies.md) — узнайте, как настроить политики безопасности для подписок и групп ресурсов Azure.
* [Управление оповещениями безопасности в Центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md) — узнайте, как управлять оповещениями системы безопасности и реагировать на них.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
