---
title: Подключение Microsoft Advanced Threat Analytics к центру безопасности Azure | Документация Майкрософт
description: Узнайте, как интегрировать центр безопасности Azure с Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: a3444b9d42ffdd5f81568f0e9e09557096b4415f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32775927"
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Подключение Microsoft Advanced Threat Analytics к центру безопасности Azure
Этот документ поможет вам настроить интеграцию между Microsoft Advanced Threat Analytics и центром безопасности Azure.

## <a name="why-add-advanced-threat-analytics-data"></a>Чем полезны данные Microsoft Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) — это локальная платформа, которая помогает обнаруживать подозрительное поведение пользователей. Подключив ATA, вы сможете просматривать в центре безопасности обнаруженные подозрительные действия. Такая интеграция позволяет просматривать, сопоставлять и исследовать в центре безопасности Azure все предупреждения безопасности, связанные с гибридными облачными рабочими нагрузками. 

## <a name="how-do-i-configure-this-integration"></a>Как настроить эту интеграцию?
Если система ATA уже установлена и успешно работает в вашей локальной среде, для интеграции следует сделать следующее:

1. Войдите в центр ATA и откройте портал ATA.
2. Щелкните **Сервер системного журнала** на панели слева.

    ![Сервер системного журнала](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. В поле **Конечная точка сервера системного журнала** введите адрес 127.0.0.7 (обязательно именно этот) и укажите порт 5114 (рекомендуется). Номер порта может быть произвольным, подходит любой незанятый порт. Не изменяйте значения остальных параметров и нажмите **Сохранить**.
4. Щелкните **Уведомления** на панели слева и включите все уведомления системного журнала (рекомендуется), как показано на следующем рисунке.

    ![Уведомления](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Выберите команду **Сохранить**.
6. Откройте панель мониторинга **Центр безопасности**.
7. На панели слева щелкните **Решения безопасности**.
8. В разделе **Advanced Threat Analytics** щелкните действие **Добавить**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Переходите к последнему шагу и щелкните **Скачивание агента**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. На странице **Добавление компьютера, который не относится к Azure** выберите рабочую область.

    ![Узел, который не относится к Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. На странице **Прямой агент** скачайте соответствующий агент Windows и сохраните его **идентификатор рабочей области** и **первичный ключ**.

    ![Direct Agent](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Установите этот агент в центре ATA. Во время установки обязательно выберите **Connect the agent to Azure Log Analytics** (Подключить агент к Azure Log Analytics) и по запросу предоставьте сохраненные *идентификатор рабочей области* и *первичный ключ*.


Эта установка завершает процесс интеграции. Теперь вы сможете увидеть новые предупреждения, отправляемые из ATA в центр безопасности, в результатах **поиска**. Новое решение появится в списке **Решения безопасности** в разделе **Подключенные решения**. 

## <a name="next-steps"></a>Дополнительная информация
Из этого документа вы узнали, как подключить Microsoft ATA к центру безопасности Azure. Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Подключение службы "Защита идентификации Azure Active Directory" к центру безопасности Azure](security-center-aadip-integration.md)
* [Настройка политик безопасности в центре безопасности Azure](security-center-policies.md) — сведения о настройке политик безопасности для подписок и групп ресурсов Azure.
* [Управление рекомендациями по безопасности в центре безопасности Azure](security-center-recommendations.md) — сведения об использовании рекомендаций для защиты ресурсов Azure.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md) — узнайте, как отслеживать работоспособность ресурсов Azure.
* [Управление оповещениями безопасности в центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md) — сведения об управлении оповещениями системы безопасности и реагировании на них.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md) — узнайте, как отслеживать состояние работоспособности решений партнеров.
- [Защита данных в центре безопасности Azure](security-center-data-security.md) — сведения об управлении данными и их защите в центре безопасности.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — последние новости и сведения об обеспечении безопасности в Azure.


