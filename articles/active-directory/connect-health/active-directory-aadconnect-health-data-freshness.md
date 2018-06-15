---
title: Azure AD Connect Health. Оповещение "Данные службы работоспособности неактуальны" | Документация Майкрософт
description: В этом документе описана причина отправки оповещения "Данные службы работоспособности неактуальны" и действия по устранению этой неполадки.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29715017"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Оповещение "Данные службы работоспособности неактуальны"

## <a name="overview"></a>Обзор
<li>Расширение Azure AD Connect Health создает оповещение об актуальности данных, если не получает все точки данных от сервера в течение двух часов. Заголовок оповещения — **Данные службы работоспособности неактуальны**. </li>
<li>Оповещение с состоянием **Предупреждение** срабатывает, если служба Connect Health не получает отправленные с сервера частичные элементы данных в течение двух часов. Оповещение с состоянием "Предупреждение" не инициирует отправку уведомлений по электронной почте для администратора клиента. </li>
<li>Оповещение с состоянием **Ошибка** срабатывает, если служба Connect Health не получает никаких отправленных с сервера элементов данных в течение двух часов. Оповещение с состоянием "Ошибка" инициирует отправку уведомлений по электронной почте для администратора клиента. </li>

>[!IMPORTANT] 
> Для этого оповещения соблюдается [политика хранения данных](active-directory-aadconnect-health-gdpr.md#data-retention-policy) Connect Health

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок 
* Откройте [раздел требований](active-directory-aadconnect-health-agent-install.md#requirements) и убедитесь, что все они соблюдены.
* Используйте [средство тестирования подключения](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) для обнаружения возможных проблем с подключением.


## <a name="next-steps"></a>Дополнительная информация
* [Часто задаваемые вопросы об Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
