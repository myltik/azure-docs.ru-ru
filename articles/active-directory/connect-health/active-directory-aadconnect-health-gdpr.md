---
title: "Azure AD Connect Health и Общий регламент по защите данных | Документация Майкрософт"
description: "В этом документе рассматривается обеспечение соответствия требованиям GDPR в Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>Azure AD Connect Health и соответствие требованиям GDPR 

[Общий регламент по защите данных (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) является законом о защите и конфиденциальности данных Европейского союза (ЕС). GDPR устанавливает новые правила для компаний, правительственных учреждений, некоммерческих организаций и других организаций, которые предлагают товары и услуги жителям государств ЕС или собирают и анализируют связанные с ними данные. 

Сегодня доступны продукты и службы Майкрософт, позволяющие обеспечить удовлетворение требований GDPR. Дополнительные сведения о политике конфиденциальности корпорации Майкрософт см. в [центре управления безопасностью](https://www.microsoft.com/trustcenter).

Azure AD Connect Health выполняет мониторинг локальной инфраструктуры идентификации и службы синхронизации. Она также предоставляет аналитику и оповещения. Корпорация Майкрософт планирует обеспечить совместимость облачных служб с GDPR на момент вступления регламента в силу (в мае 2018 года) и будет предоставлять связанные с GDPR гарантии в своих контрактных обязательствах. 

>[!NOTE] 
> В этой статье рассматривается соответствие GDPR в Azure AD Connect Health. Сведения о соответствии GDPR в Azure AD Connect см. в разделе [о соответствии GDPR и Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="gdpr-classification"></a>Классификация GDPR
Azure AD Connect Health попадает в категорию **обработчика данных** в классификации GDPR. В качестве конвейера обработчика данных Azure AD Connect Health предоставляет службы обработки данных ключевым партнерам и пользователям. Эта служба не создает пользовательские данные и не может независимо управлять сбором каких-либо конфиденциальных данных и их использованием. Получение данных, агрегация, анализ и отчетность в Azure AD Connect Health основаны на имеющихся локальных данных. 

## <a name="data-retention-policy"></a>Политика хранения данных
Azure AD Connect Health создает отчеты, выполняет анализ или предоставляет подробные сведения за период не более 30 дней. Таким образом, в Azure AD Connect Health не хранятся и не обрабатываются данные за период более 30 дней. Такой подход является совместимым с нормами GDPR, требованиями соответствия нормативам корпорации Майкрософт о конфиденциальности и политике хранения данных Azure AD. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Отключение сбора данных и мониторинга в Azure AD Connect Health
Azure AD Connect Health позволяет остановить сбор данных для каждого отдельного отслеживаемого сервера или экземпляра отслеживаемой службы. Например, можно остановить сбор данных для отдельных серверов AD FS (службы федерации Active Directory), которые отслеживаются с помощью Azure AD Connect Health. Кроме того, можно остановить сбор данных для всего экземпляра AD FS, который отслеживается с помощью Azure AD Connect Health. В этом случае соответствующие серверы будут удалены с портала Azure AD Connect Health после остановки сбора данных. 

>[!IMPORTANT]
> Чтобы удалить отслеживаемые серверы из Azure AD Connect Health, вам необходимы разрешения глобального администратора Azure AD или роль участника в RBAC.
>
> Удаление сервера или экземпляра службы из Azure AD Connect Health — необратимое действие. 

### <a name="what-to-expect"></a>Основные принципы
Если необходимо остановить сбор данных и мониторинг для отдельного отслеживаемого сервера или экземпляра отслеживаемой службы, обратите внимание на следующее:

- При удалении экземпляра отслеживаемой службы он удаляется из списка мониторинга службы Azure AD Connect Health на портале. 
- При удалении отслеживаемого сервера или экземпляра отслеживаемой службы агент работоспособности не удаляется с серверов. Агент работоспособности настроен не отправлять данные в Azure AD Connect Health. Вам необходимо вручную удалить агент работоспособности на отслеживаемых ранее серверах.
- Если вы не удаляли агент работоспособности перед выполнением этого шага, на серверах могут отображаться события ошибок, связанные с агентом работоспособности.
- Все данные, принадлежащие экземпляру отслеживаемой службы, будут удалены в соответствии с политикой хранения данных Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Отключение сбора данных и мониторинга для отслеживаемого сервера
Дополнительные сведения см. в статье об [удалении сервера из Azure Active Directory Connect Health](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Отключение сбора данных и мониторинга для экземпляра отслеживаемой службы
Дополнительные сведения см. в [статье о том, как удалить экземпляр службы из Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Повторное включение сбора данных и мониторинга в Azure AD Connect Health
Чтобы снова включить мониторинг в Azure AD Connect Health для ранее удаленной отслеживаемой службы, необходимо удалить и [повторно установить агент работоспособности](active-directory-aadconnect-health-agent-install.md) на всех серверах.


## <a name="next-steps"></a>Дополнительная информация
* [Просмотр политики конфиденциальности корпорации Майкрософт в центре управления безопасностью](https://www.microsoft.com/trustcenter)
* [Сведения о соответствии GDPR в Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Операции Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
