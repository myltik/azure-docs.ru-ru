---
title: "Переключение подписки Azure на другое предложение | Документация Майкрософт"
description: "Узнайте, как изменить подписку Azure и переключиться на другое предложение с помощью портала управления подпиской."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: c18614f1c096eb76c6a28a9038dab079e04cd4db
ms.openlocfilehash: 91ad4b1a661e110f7ce8f0ca4cfc7697d86f6c58


---
# <a name="switch-your-azure-subscription-to-another-offer"></a>Переключение подписки Azure на другое предложение
Если вы клиент подписки [с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/), вы можете переключить свою подписку Azure на другое предложение в [центре управления учетной записью](https://account.windowsazure.com/Subscriptions). Например, с помощью этой функции вы можете воспользоваться преимуществами [ежемесячных кредитов для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Если вы используете [бесплатную пробную версию](https://azure.microsoft.com/free/), узнайте, как [перейти на предложение с оплатой по мере использования](billing-upgrade-azure-subscription.md).

#### <a name="whats-supported"></a>Поддерживаемые функции
| Из | Кому |
| --- | --- |
| [Оплата по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Разработка и тестирование с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Оплата по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Оплата по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Оплата по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/) |[MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Оплата по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Оплата по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> По вопросам других вариантов смены предложения [обращайтесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="switch-subscription-offer"></a>Изменение предложения подписки
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
> 
> 

1. Войдите в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions).
2. Выберите подписку с оплатой по мере использования
3. Щелкните **Переключиться на другое предложение**. Эта кнопка доступна, только если вы оплачиваете услуги по мере использования, и ваш первый расчетный период закончился.
   
   ![Обратите внимание на кнопку "Переключить предложение" в правой части страницы.](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. **Выберите нужное предложение** в списке предложений, на которые можно переключить вашу подписку. Список доступных предложений будет зависеть от членства вашей учетной записи. Если список пуст, проверьте [список доступных предложений, на которые вы можете переключить подписку](#whats-supported), и убедитесь, что ваша учетная запись имеет правильное членство. 
   
   ![Выберите предложение, на которое нужно переключиться.](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. При переходе на некоторые предложения может появляться уведомление о последствиях перехода. Внимательно ознакомьтесь с этим списком и выполните указанные действия, прежде чем продолжить.
   
   ![Просмотрите примечания.](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. Вы можете переименовать подписку. По умолчанию ей присваивается имя нового предложения. Чтобы завершить процедуру, щелкните **Переключить предложение** .
   
   ![Нажмите зеленую кнопку.](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. Готово! Подписка переведена на новое предложение.

## <a name="why-cant-i-switch-offers"></a>Почему не удается переключать предложения?
Кнопка **Переключиться на другое предложение** может не отображаться в перечисленных ниже случаях.

* Вы не являетесь клиентом подписки с [оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/). Сейчас на другое предложение можно переключать только подписки с оплатой по мере использования.
  
  * Если вы используете [бесплатную пробную версию](https://azure.microsoft.com/free/), узнайте, как [перейти на предложение с оплатой по мере использования](billing-upgrade-azure-subscription.md).
  * Чтобы переключить предложение из другой подписки, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Еще не завершился ваш первый расчетный период. Дождитесь его завершения и тогда вы сможете изменить предложение.

Вы можете увидеть сообщение **На данный момент предложения для вашей страны или вашего региона отсутствуют**. Сообщение появляется по следующим причинам.

* У вас нет права на изменение предложения. См. список [доступных предложений, на которые можно переключиться](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Как переключение предложений Azure повлияет на мою службу и выставление счетов?
Далее объяснятся, что произойдет, если вы измените план Azure в центре управления учетной записью.

### <a name="access-to-services"></a>Доступ к службам
Для пользователей, связанных с подпиской, службы будут работать как обычно. Однако предложение, на которое вы переключаетесь, может включать ограничения, Например, некоторые предложения запрещают использование в рабочей среде, поэтому вам нужно будет переместить рабочие ресурсы в другую подписку.

### <a name="billing"></a>Выставление счетов
В день смены предложения выставляется счет за все непогашенные задолженности. После этого счет за подписку будет выставляться по тарифам нового предложения. Дата взыскания годовой абонентской платы изменится в соответствии с датой смены предложения. Данные по платежам и использованию ресурсов до смены предложения не сохраняются, поэтому рекомендуем скачать их копию перед сменой тарифа.

> [!NOTE]
> Из-за ограничений, связанных с выставлением счетов, предложение нельзя изменить, пока не завершится первый расчетный период после создания подписки.
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Могу ли я переключить свою подписку с тарифного плата с оплатой по мере использования на [поставщик облачных решений](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) или [Соглашение Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?
Переключение на тарифные планы CSP или EA сейчас не поддерживается в центре управления учетной записью. Для перевода имеющейся подписки на предложение "Соглашение Enterprise" попросите своего администратора регистрации добавить вашу учетную запись в соглашение Enterprise. После этого вы получите приглашение по электронной почте. Как только вы примете приглашение согласно инструкциям, ваши подписки будут автоматически перенесены в Соглашение Enterprise. Как перейти на CSP см. в статье [Azure Subscription Migration to CSP](https://blogs.technet.microsoft.com/hybridcloudbp/2016/08/26/azure-subscription-migration-to-csp/) (Перенос подписки Azure в CSP).

## <a name="can-i-migrate-data-and-services-for-my-azure-subscription-to-a-new-subscription"></a>Можно ли перенести данные и службы из подписки Azure в новую подписку?
Да. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](./azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если у вас есть дополнительные вопросы, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , которая поможет быстро устранить проблему.




<!--HONumber=Dec16_HO2-->


