---
title: Подписки управления привилегированными пользователями (PIM) в Azure | Документация Майкрософт
description: Требования к подписке и лицензиям для управления Azure AD Privileged Identity Management и варианты использования этой службы в вашем клиенте
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9e49ddb8fca9ce193c92f27c307942d5f3d3d2fd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Требования к подписке для Azure Active Directory Privileged Identity Management

Технология Azure AD Privileged Identity Management доступна только в выпуске Azure Active Directory Premium P2. Сведения о других функциях P2 и отличиях этого выпуска от выпуска Premium P1 см. в статье [Выпуски Azure Active Directory](../active-directory-editions.md).

>[!NOTE]
Пока служба Azure Active Directory (Azure AD) Privileged Identity Management была доступна в режиме предварительной версии, клиенты могли использовать ее без проверки лицензий.  В декабре 2016 г. вышла общедоступная версия Azure AD Privileged Identity Management. Теперь ее использование возможно только при наличии в клиенте пробной или платной подписки.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>Подтверждение пробной или платной подписки

Если вы не уверены, есть ли у вашей организации пробная или платная подписка, вы можете проверить ее наличие в клиенте с помощью команд, включенных в модуль Azure Active Directory для Windows PowerShell V1. 
1. Откройте окно PowerShell.
2. Введите `Connect-MsolService`, чтобы выполнить проверку подлинности в клиенте в качестве пользователя.
3. Укажите `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`.

Эта команда извлекает список подписок в клиенте. Если результат не содержит строки, для использования Azure AD Privileged Identity Management следует получить пробную версию Azure AD Premium P2 или приобрести подписку Azure AD Premium P2 или EMS E5.  Сведения о том, как получить пробную версию и начать использовать Azure AD Privileged Identity Management, см. в статье [Приступая к работе с Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

Если команда возвращает строку, в которой SkuPartNumber имеет значение AAD_PREMIUM_P2 или EMSPREMIUM, а IsTrial — значение True, это значит, что бесплатная пробная версия Azure AD Premium P2 уже установлена в клиенте.  Если эта подписка имеет статус неактивной, и у вас нет приобретенной подписки Azure AD Premium P2 или EMS E5, чтобы и дальше использовать Azure AD Privileged Identity Management, вам необходимо приобрести подписку Azure AD Premium P2 или EMS E5.

Служба Azure AD Premium P2 предоставляется в рамках [соглашения Microsoft Enterprise](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [программы корпоративного лицензирования Open](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) или [программы поставщиков облачных решений](https://partner.microsoft.com/en-US/cloud-solution-provider). Подписчики Azure и Office 365 также могут приобрести выпуск Active Directory Premium P2 через Интернет.  Дополнительные сведения о ценах на Azure AD Premium и инструкции по заказу этого продукта см. на странице [Azure Active Directory: цены](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Служба Azure AD Privileged Identity Management недоступна в клиенте

Служба Azure AD Privileged Identity Management будет недоступна в клиенте в следующих случаях:
- если организация использовала Azure AD Privileged Identity Management в режиме предварительного просмотра без приобретения подписки на Azure AD Premium P2 или EMS E5;
- если организация использовала пробную версию служб Azure AD Premium P2 или EMS E5, у которых истек срок действия;
- если организация использовала платную подписку, у которой истек срок действия.

Когда истекает срок действия подписки на Azure AD Premium P2 или EMS E5 или если организация, использовавшая Azure AD Privileged Identity Management в режиме предварительного просмотра, не приобретает подписку на Azure AD Premium P2 или EMS E5, происходит следующее:

- назначенные постоянные роли Azure AD сохраняются без изменений;
- расширение Azure AD Privileged Identity Management на портале Azure, а также командлеты API Graph и интерфейсы PowerShell для Azure AD Privileged Identity Management больше не позволяют пользователям активировать привилегированные роли, управлять привилегированным доступом или выполнять проверки доступа для привилегированных ролей;
- удаляются назначения для ролей Azure AD, так как пользователи уже не могут активировать привилегированные роли;
- завершаются все действующие проверки доступа для ролей Azure AD и удаляются параметры конфигурации Azure AD Privileged Identity Management;
- Azure AD Privileged Identity Management прекращает отправку сообщений электронной почты об изменении назначений ролей.

## <a name="next-steps"></a>Дополнительная информация

- [Приступая к работе с управлением привилегированными пользователями Azure AD](../active-directory-privileged-identity-management-getting-started.md)
- [Роли в службе управления привилегированными пользователями Azure AD](../active-directory-privileged-identity-management-roles.md)
