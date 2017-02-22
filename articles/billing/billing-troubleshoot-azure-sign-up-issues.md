---
title: "Устранение неполадок при регистрации в Azure | Документация Майкрософт"
description: "Описание процесса устранения неполадок для некоторых распространенных проблем с регистрацией."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: f058194302949d1f5ba7d86e9a693ba229bdc9a2
ms.openlocfilehash: b913fd0d944568e422ffcf86791910a746367add


---
# <a name="i-cant-sign-up-for-azure"></a>Не удается зарегистрироваться для работы с Azure
Если не удается зарегистрироваться в Azure, можно проверить несколько моментов, чтобы устранить проблему.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>Индикатор выполнения зависает в разделе "Проверка личности с помощью карты"

Во время регистрации Azure вы можете увидеть раздел "Проверка личности с помощью карты". Ниже представлен снимок экрана, на котором отображается зависание индикатора выполнения:

![Снимок экрана, на котором показано, что индикатор выполнения завис в разделе "Проверка личности с помощью карты" при регистрации](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Эта проблема возникает, если сторонние файлы cookie заблокированы в вашем браузере.

### <a name="suggestion"></a>Предложение

1. Разрешите использование сторонних файлов cookie в параметрах браузера.
  * В Edge выберите "Параметры -> Просмотреть дополнительные параметры -> Файлы cookie" и щелкните "Не блокировать файлы «cookie»".
  * В Chrome выберите "Настройки -> Показать дополнительные параметры -> Конфиденциальность -> Параметры содержимого" и снимите флажок Block third-party cookies and site data (Блокировать сторонние файлы cookie и данные сайта).
2. Обновите страницу регистрации Azure и проверьте, устранена ли проблема.
3. Если это не помогло, закройте и перезапустите браузер, а затем повторите попытку.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Отсутствие текстовых сообщений или вызовов при проверке учетной записи во время регистрации
* Доставка кода текста может занимать до&4; минут.
* Убедитесь, что ваш телефон может получать текстовые сообщения (SMS).
* Проверьте введенный номер телефона, включая код страны в раскрывающемся меню.
* Если вы используете вариант подтверждения путем отправки текстового сообщения, убедитесь, что ваш телефон может получать текстовые сообщения (SMS). Если вы используете вариант вызова, убедитесь, что телефон может принимать звонки.
* Получив текстовое сообщение, введите код в текстовое поле и нажмите кнопку подтверждения.

### <a name="suggestions"></a>Предложения
* Если с доставкой текстовых сообщений (SMS) возникают проблемы, попробуйте альтернативный метод проверки с помощью звонка.
* Воспользуйтесь другим номером телефона при проблемах с двумя предложенными методами (SMS и звонок).
* Для проверки номера телефона нельзя использовать номер VoIP-телефона.

## <a name="credit-card-declined-or-not-accepted"></a>Кредитная карта отклонена или не принята
Убедитесь, что метод оплаты, который вы используете при регистрации, поддерживается. Вы также можете узнать, [почему банковская или кредитная карта отклонена при регистрации в Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="free-trial-is-not-available"></a>"Бесплатная пробная версия недоступна"
В прошлом вы уже использовали подписку Azure? В соответствии с условиями соглашения Azure любой новый пользователь Azure может выполнить только одну активацию бесплатной пробной версии. Если у вас уже была подписка Azure любого типа, вы не сможете активировать бесплатную пробную версию.

### <a name="suggestion"></a>Предложение
* Попробуйте зарегистрироваться, чтобы использовать [подписку с оплатой по мере использования](https://azure.microsoft.com/offers/ms-azr-0003p/).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Не удается активировать льготный план Azure, например MSDN, BizSpark, BizSparkPlus или MPN
Убедитесь, что вы используете правильные учетные данные для входа, и уточните по каналу своей программы преимуществ, имеете ли вы право на выбранный план.

* MSDN
  * Проверьте свой статус соответствия требованиям на своей [странице учетной записи MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Если вам не удается проверить свой статус, обратитесь в [центры обслуживания клиентов с подписками MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * Войдите на [портал MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) и проверьте свой статус соответствия требованиям. Дополнительные преимущества предоставляются при наличии соответствующих [компетенций облачной платформы](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx).
  * Если вам не удается проверить свой статус, обратитесь в [службу поддержки MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).
* BizSpark
  * войдите на [портал BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) и проверьте свой статус соответствия требованиям BizSpark и BizSpark Plus.
  * Если вам не удается проверить состояние, [обратитесь к группе BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team).

## <a name="cant-activate-new-azure-in-open-subscription"></a>Не удается активировать новую подписку Azure с открытой лицензией
Чтобы создать подписку Azure с открытой лицензией, вам потребуется действующий ключ OSA, с которым связан как минимум один соответствующий маркер Azure с открытой лицензией.

### <a name="suggestion"></a>Предложение
Если у вас нет ключа OSA, свяжитесь с любым из партнеров корпорации Майкрософт, которые перечислены на сайте [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.



<!--HONumber=Feb17_HO2-->


