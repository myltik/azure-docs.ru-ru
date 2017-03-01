---
title: "Рекомендации Azure Advisor по безопасности | Документация Майкрософт"
description: "Используйте Azure Advisor для повышения безопасности развернутых служб Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: dbdbae0f87d8d115407026b263c65c3b387fa178
ms.lasthandoff: 02/21/2017

---
# <a name="advisor-security-recommendations"></a>Рекомендации Azure Advisor по безопасности

Advisor предоставляет согласованное сводное представление с рекомендациями для всех ресурсов Azure. Advisor интегрируется с центром безопасности Azure, чтобы вы могли получить рекомендации по безопасности. Эти рекомендации можно получить на вкладке **Безопасность** панели мониторинга Advisor.

![Вкладка "Безопасность" в Advisor](./media/advisor-security-recommendations/advisor-security-tab.png)

Центр безопасности помогает вам выявлять и предотвращать угрозы, а также принимать ответные меры благодаря более полной информации о состоянии ресурсов Azure и контролю над их безопасностью. Он периодически анализирует состояние безопасности ресурсов Azure. Когда Центр безопасности выявляет потенциальные уязвимости системы безопасности, он создает рекомендации. Рекомендации помогают настраивать необходимые элементы управления. 

![Вкладка "Безопасность" в Advisor](./media/advisor-security-recommendations/advisor-security-recommendations.png)

Дополнительные сведения о рекомендациях по безопасности см. в разделе [Управление рекомендациями по безопасности в Центре безопасности Azure](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/).

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Как получить доступ к рекомендации по безопасности в Azure Advisor

1. Войдите на [портал Azure](https://portal.azure.com).
2. В области навигации слева щелкните **Больше служб**, прокрутите вниз область меню служб до раздела **Мониторинг и управление** и щелкните **Помощник по Azure**. Откроется панель мониторинга Advisor. 
3. На панели мониторинга Advisor щелкните вкладку **Безопасность** и выберите подписку, для которой вы хотите получать рекомендации.

> [!NOTE]
> Для доступа к рекомендациям Помощника необходимо сначала **зарегистрировать** в нем свою подписку. Подписка регистрируется, когда **владелец подписки** запускает панель мониторинга Помощника и нажимает кнопку **Get recommendations** (Получить рекомендации). Выполнить данную **операцию достаточно всего один раз**. После регистрации подписки рекомендации Помощника могут просматривать **владельцы**, **участники** или **читатели** подписки, группы ресурсов или конкретного ресурса.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о рекомендациях Advisor, ознакомьтесь с приведенными ниже материалами.
-  [Рекомендации Azure Advisor по высокой доступности](advisor-high-availability-recommendations.md)
-  [Рекомендации Azure Advisor по производительности](advisor-performance-recommendations.md)
-  [Рекомендации Azure Advisor по затратам](advisor-performance-recommendations.md)
 

