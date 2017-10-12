---
title: "Приступая к работе с Azure MFA в облаке | Документация Майкрософт"
description: "Эта страница посвящена службе \"Многофакторная идентификация Microsoft Azure\". Она содержит сведения по началу работы с Azure MFA в облаке."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: kgremban
ms.openlocfilehash: 19f3228b874fc4e37bf83388dae4341428226482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Приступая к работе со службой "Многофакторная идентификация Microsoft Azure" в облаке
В этой статье вы узнаете, как приступить к работе со службой "Многофакторная идентификация Microsoft Azure" в облаке.

> [!NOTE]
> Здесь рассматривается использование **классического портала Azure**. Сведения о настройке многофакторной проверки подлинности в Azure для пользователей Office 365 см. в статье [Настройка многофакторной проверки подлинности для Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA в облаке](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Предварительные требования
[Зарегистрируйтесь, чтобы оформить подписку Azure](https://azure.microsoft.com/pricing/free-trial/). Если у вас еще нет подписки Azure, вам необходимо зарегистрироваться для ее оформления. Если вы только начинаете работать со службой "Многофакторная идентификация Microsoft Azure" (MFA), используйте пробную подписку.

## <a name="enable-azure-multi-factor-authentication"></a>Включение многофакторной проверки подлинности в Azure
При наличии у пользователей лицензий, которые предусматривают использование многофакторной проверки подлинности в Azure, вам не нужно включать Azure MFA. Для отдельных пользователей можно включить двухфакторную проверку подлинности. Лицензии, которые предусматривают использование Azure MFA:
- Многофакторная идентификация Microsoft Azure;
- Azure Active Directory Premium;
- Enterprise Mobility + Security.

У вас могут отсутствовать эти лицензии или может не хватать их числа, чтобы охватить всех пользователей. Ничего страшного. Вам просто нужно выполнить дополнительное действие, чтобы [создать поставщика Многофакторной идентификации](multi-factor-authentication-get-started-auth-provider.md) в своем каталоге.

## <a name="turn-on-two-step-verification-for-users"></a>Включение двухфакторной проверки подлинности для пользователей

Используйте одну из процедур, перечисленных в статье [Состояние пользователей в службе "Многофакторная идентификация Microsoft Azure"](multi-factor-authentication-get-started-user-states.md), для начала работы с Azure MFA. Вы можете применять двухфакторную проверку подлинности для всех входов или создать политики условного доступа, чтобы требовать ее только там, где нужно.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда служба "Многофакторная идентификация Microsoft Azure" в облаке настроена, можно настроить и выполнить развертывание. Дополнительные сведения см. в статье [Настройка службы "Многофакторная идентификация Microsoft Azure"](multi-factor-authentication-whats-next.md).

