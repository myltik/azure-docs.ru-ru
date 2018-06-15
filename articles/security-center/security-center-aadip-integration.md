---
title: Подключение службы "Защита идентификации Azure Active Directory" к центру безопасности Azure | Документация Майкрософт
description: Сведения об интеграции центра безопасности Azure со службой "Защита идентификации Azure Active Directory".
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b15508acea92c165cba85e39ab907fa524088164
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774188"
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Подключение службы "Защита идентификации Azure Active Directory" к центру безопасности Azure
В этом документе объясняется, как настроить интеграцию между службой "Защита идентификации Azure Active Directory" (AD) и центром безопасности Azure.

## <a name="why-connect-azure-ad-identity-protection"></a>Что может дать подключение службы "Защита идентификации Azure AD"?
Служба [Защита идентификации Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) позволяет обнаруживать потенциальные уязвимости, которые могут повлиять на учетные записи организации. Подключив эту службу, вы сможете просматривать уведомления службы "Защита идентификации Azure AD" в центре безопасности Azure. Такая интеграция позволяет просматривать, сопоставлять и исследовать в центре безопасности Azure все предупреждения безопасности, связанные с гибридными облачными рабочими нагрузками. 

## <a name="how-do-i-configure-this-integration"></a>Как настроить эту интеграцию?
Если в вашей организации уже используется служба "Защита идентификации Azure AD", выполните следующие действия для настройки интеграции.

1. Откройте панель мониторинга **Центр безопасности**.
2. На панели слева щелкните **Решения безопасности**. Центр безопасности автоматически определяет, включена ли служба "Защита идентификации Azure AD" для вашей организации.

    ![Служба "Защита идентификации Azure AD"](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Нажмите кнопку **Подключить**.
4. Прокрутите вниз страницу **Интеграция защиты идентификации Azure AD** и выберите соответствующую рабочую область.

    ![Рабочая область](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Щелкните **Подключить**.

Когда вы завершите эту конфигурацию, на странице **Решения безопасности** в разделе **Подключенные решения** появится решение "Защита идентификации Azure AD". 

## <a name="next-steps"></a>Дополнительная информация
Из этого документа вы узнали, как подключить службу "Защита идентификации Azure AD" к центру безопасности. Дополнительные сведения о центре безопасности см. в следующих статьях:

* [Подключение Microsoft Advanced Threat Analytics к центру безопасности Azure](security-center-ata-integration.md)
* [Настройка политик безопасности в центре безопасности Azure](security-center-policies.md) — сведения о настройке политик безопасности для подписок и групп ресурсов Azure.
* [Управление рекомендациями по безопасности в центре безопасности Azure](security-center-recommendations.md) — сведения об использовании рекомендаций для защиты ресурсов Azure.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md) — узнайте, как отслеживать работоспособность ресурсов Azure.
* [Управление оповещениями безопасности в центре безопасности Azure и реагирование на них](security-center-managing-and-responding-alerts.md) — сведения об управлении оповещениями системы безопасности и реагировании на них.
* [Мониторинг решений партнеров с помощью центра безопасности Azure](security-center-partner-solutions.md) — узнайте, как отслеживать состояние работоспособности решений партнеров.
- [Защита данных в центре безопасности Azure](security-center-data-security.md) — сведения об управлении данными и их защите в центре безопасности.
* [Центр безопасности Azure: часто задаваемые вопросы](security-center-faq.md) — часто задаваемые вопросы об использовании этой службы.
* [Блог по безопасности Azure](http://blogs.msdn.com/b/azuresecurity/) — последние новости и сведения об обеспечении безопасности в Azure.


