---
title: "Ошибка \"Подписки не найдены\" при попытке входа на портал Azure или в Центр управления учетной записью Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как устранить ошибку \"Подписки не найдены\", которая возникает при входе на портал Azure или в Центр управления учетной записью Azure."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a4ce9b219c05f8469379c2aac5241fcfffd16033
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---

# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Ошибка "Подписки не найдены" на портале Azure или в Центре управления учетной записью Azure
При попытке входа на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) может появиться сообщение об ошибке "Подписки не найдены". В этой статье предлагается решение указанной проблемы.

## <a name="symptom"></a>Симптом

При попытке входа на [портал Azure](https://portal.azure.com/) или в [Центр управления учетной записью Azure](https://account.windowsazure.com/Subscriptions) отображается следующее сообщение об ошибке: "Подписки не найдены".

## <a name="cause"></a>Причина:

Эта проблема возникает, если учетная запись не предоставляет необходимых разрешений. 

## <a name="solution"></a>Решение

Убедитесь, что используете для входа надлежащую учетную запись администратора. У администратора учетной записи есть доступ только к Центру управления учетной записью Azure. А у администраторов служб и соадминистраторов есть разрешение только на доступ к порталу Azure и классическому порталу Azure.

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Сценарий 1. Сообщение об ошибке получено на [портале Azure](https://portal.azure.com)

Чтобы устранить эту проблему:

* Убедитесь, что выбран надлежащий каталог Azure, щелкнув учетную запись в правом верхнем углу.

  ![Выберите каталог в верхней правой части портала Azure](./media/billing-no-subscriptions-found/directory-switch.png)

* Если вы выбрали надлежащий каталог Azure, но по-прежнему получаете сообщение об ошибке, [добавьте учетную запись с правами владельца](billing-add-change-azure-subscription-administrator.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Сценарий 2. Сообщение об ошибке получено в [Центре управления учетной записью Azure](https://account.windowsazure.com/Subscriptions)

Убедитесь, что используете учетную запись администратора учетной записи. Чтобы проверить, кто является администратором учетной записи, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com).
2. В главном меню выберите **Подписка**.
3. Выберите подписку, которую требуется проверить, а затем щелкните **Параметры**.
4. Выберите **Свойства**. Администратор учетной записи подписки отобразится в поле **Администратор учетной записи** .

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.
Если вам все еще нужна помощь, [обратитесь в службу поддержки](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409), которая поможет быстро устранить проблему. 

