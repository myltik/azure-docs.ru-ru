---
title: "Руководство по обеспечению устойчивости служб | Microsoft Azure"
description: "Ссылки на руководства по аварийному восстановлению и превентивным мерам повышения устойчивости и доступности служб Microsoft Azure."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 919a197d-ba47-4e49-a64c-118e27d5a7df
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: aafb03c86884d1af3e2cae1134c9e6d7ac42397b
ms.openlocfilehash: 4186211af944c0c0109799a94e4163cfceeee4c4


---
# <a name="microsoft-azure-service-resiliency-guidance"></a>Руководство обеспечению по устойчивости службы Microsoft Azure
Инфраструктура службы Microsoft Azure предоставляет пользователям необходимые ресурсы по мере необходимости. Согласно общим рекомендациями по разработке и методикам, вам следует знать, как планировать использование служб Azure для обеспечения высокой доступности, а также о возможностях защиты приложения от перебоев в работе службы. Этот документ содержит ссылки на руководства по аварийному восстановлению, а также руководства по проектированию для разных служб Azure.

## <a name="disaster-recovery-guidance"></a>Руководство по аварийному восстановлению
Приведенные ниже ссылки на рекомендации по аварийному восстановлению помогут узнать, как можно быстро вернуть приложение в рабочее состояние при перебоях в работе службы Azure. Эти ссылки помогут определиться с действиями при перебоях в работе служб Azure, которые влияют на приложение.

## <a name="design-guidance"></a>Руководство по проектированию
Приведенные ниже ссылки на рекомендации по проектированию и разработке архитектуры помогут вам понять, как лучше использовать отдельные службы Azure, чтобы максимально повысить работоспособность приложения. Эти ссылки помогут определиться с действиями при ошибках, сбоях оборудования, перебоях в работе службы или других сбоях, которые могут потенциально повлиять на общую доступность приложения. Если нет конкретных указаний для службы, которую вы в настоящее время ищете, статья [Обеспечение высокой доступности для приложений на платформе Microsoft Azure](resiliency-high-availability-azure-applications.md) может предоставить дополнительные сведения, которые помогут вам при проектировании.

## <a name="service-guidance"></a>Руководства по службам
| служба | Руководство по аварийному восстановлению | Руководство по проектированию |
|:--- |:---:|:---:|
| [Облачные службы](https://azure.microsoft.com/services/cloud-services/ "Azure Облачные службы") |[link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Руководство по аварийному восстановлению в облачных службах Azure") |Недоступно |
| [хранилище ключей;](https://azure.microsoft.com/services/key-vault/ "Azure хранилище ключей;") |[link](../key-vault/key-vault-disaster-recovery-guidance.md "Руководство по аварийному восстановлению в хранилище ключей Azure") |Недоступно |
| [Хранилище](https://azure.microsoft.com/services/storage/ "Azure Хранилище") |[link](../storage/storage-disaster-recovery-guidance.md "Руководство по аварийному восстановлению в службе хранилища Azure") |Недоступно |
| [Базы данных SQL](https://azure.microsoft.com/services/sql-database/ "Azure Базы данных SQL") |[link](../sql-database/sql-database-disaster-recovery.md "Руководство по аварийному восстановлению в службе баз данных SQL Azure") |[link](../sql-database/sql-database-performance-guidance.md "Руководство по проектированию баз данных SQL Azure") |
| [Виртуальные машины](https://azure.microsoft.com/services/virtual-machines/ "Azure Виртуальные машины") |[link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Руководство по аварийному восстановлению в службе виртуальных машин Azure") |Недоступно |
| [Виртуальная сеть](https://azure.microsoft.com/services/virtual-network/ "Azure Виртуальная сеть") |[link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Руководство по аварийному восстановлению виртуальной сети Azure") |Недоступно |

## <a name="next-steps"></a>Дальнейшие действия
Более развернутые рекомендации по системам и решениям см. в статье [Аварийное восстановление и высокая доступность для приложений на платформе Azure](https://aka.ms/drtechguide).



<!--HONumber=Nov16_HO3-->


