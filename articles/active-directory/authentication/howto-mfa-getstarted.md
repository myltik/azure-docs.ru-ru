---
title: Приступая к работе с Azure MFA в облаке | Документация Майкрософт
description: Эта страница посвящена службе "Многофакторная идентификация Microsoft Azure". Она содержит сведения по началу работы с Azure MFA в облаке.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 0a822d55e8d7bd0d503eb7d77f96dc9e60e1a4ba
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33882874"
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Приступая к работе с Многофакторной идентификацией Azure в облаке
В этой статье вы узнаете, как приступить к работе со службой Многофакторной идентификации Microsoft Azure в облаке.

> [!NOTE]
> Здесь рассматривается использование **портала Azure**. Сведения о настройке Многофакторной идентификации Azure для пользователей Office 365 см. в статье [Настройка Многофакторной идентификации для Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA в облаке](./media/howto-mfa-getstarted/mfa_in_cloud.png)

## <a name="prerequisite"></a>Предварительные требования
[Зарегистрируйтесь, чтобы оформить подписку Azure](https://azure.microsoft.com/pricing/free-trial/). Если у вас еще нет подписки Azure, вам необходимо зарегистрироваться для ее оформления. Если вы только начинаете работать со службой "Многофакторная идентификация Microsoft Azure" (MFA), используйте пробную подписку.

## <a name="enable-azure-multi-factor-authentication"></a>Включение Многофакторной идентификации Azure
При наличии у пользователей лицензий, которые предусматривают использование многофакторной проверки подлинности в Azure, вам не нужно включать Azure MFA. Для отдельных пользователей можно включить двухфакторную проверку подлинности. Лицензии, которые предусматривают использование Azure MFA:
- Многофакторная идентификация Azure
- Azure Active Directory Premium;
- Enterprise Mobility + Security.

У вас могут отсутствовать эти лицензии или может не хватать их числа, чтобы охватить всех пользователей. Ничего страшного. Вам просто нужно выполнить дополнительное действие, чтобы [создать поставщика Многофакторной идентификации](concept-mfa-authprovider.md) в своем каталоге.

## <a name="turn-on-two-step-verification-for-users"></a>Включение двухфакторной проверки подлинности для пользователей

Используйте одну из процедур, перечисленных в статье [Состояние пользователей в службе "Многофакторная идентификация Microsoft Azure"](howto-mfa-userstates.md), для начала работы с Azure MFA. Вы можете применять двухфакторную проверку подлинности для всех входов или создать политики условного доступа, чтобы требовать ее только там, где нужно.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда служба Многофакторной идентификации Azure в облаке настроена, можно настроить и выполнить развертывание. Дополнительные сведения см. в статье [Настройка службы Многофакторной идентификации Azure](howto-mfa-mfasettings.md).

