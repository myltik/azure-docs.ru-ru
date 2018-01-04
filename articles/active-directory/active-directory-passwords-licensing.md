---
title: "Лицензирование: Azure AD SSPR | Документация Майкрософт"
description: "Требования к лицензированию самостоятельного сброса пароля в Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 45cecbf1334d4d708f1578d1512627bc0ead3a04
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Требования к лицензированию самостоятельного сброса пароля в Azure AD

Для работы функции сброса пароля Azure Active Directory (Azure AD) *в вашей организации должна быть назначена по крайней мере одна лицензия*. Лицензирование каждого пользователя для сброса пароля необязательно. Для соблюдения лицензионного соглашения Майкрософт вам необходимо назначить лицензии для всех пользователей, которые используют функции уровня "Премиум".

* **Полностью облачные пользователи**. Любой платный номер SKU Office 365 или Azure AD Basic.
* **Облачные** или **локальные пользователи**. Azure AD Premium P1 или P2, Enterprise Mobility + Security (EMS) или Secure Productive Enterprise (SPE).

## <a name="licenses-required-for-password-writeback"></a>Лицензии, необходимые для компонента обратной записи паролей

Чтобы использовать компонент обратной записи паролей, клиенту должна быть назначена одна из приведенных ниже лицензий:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 (план E3);
* Microsoft 365 (план E5).

> [!WARNING]
> Автономные планы лицензирования Office 365 *не поддерживают компонент обратной записи паролей*. Для работы этой функции необходим один из указанных выше планов.

Дополнительные сведения о лицензировании, включая расходы, можно найти на следующих страницах:

* [Сайт с ценами на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* [Функции и возможности Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security).
* [Microsoft 365 корпоративный](https://www.microsoft.com/microsoft-365/enterprise).

## <a name="enable-group-or-user-based-licensing"></a>Включение группового и пользовательского лицензирования

Azure AD теперь поддерживает групповое лицензирование. Это позволяет администраторам назначать лицензии сразу группе пользователей, вместо назначения их по одной. Дополнительные сведения см. в разделе [Назначение лицензий группе пользователей в Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Некоторые службы Майкрософт недоступны во всех расположениях. Прежде чем назначать лицензию, администратор должен указать для пользователя свойство **Место использования**. Назначить лицензию можно на портале Azure в разделе **Пользователь** > **Профиль** > **Параметры**. *Если лицензии назначаются группам, все пользователи, для которых не указано расположение, наследуют расположение каталога.*

## <a name="next-steps"></a>Дальнейшие действия

* [Как развернуть самостоятельный сброс пароля?](active-directory-passwords-best-practices.md)
* [Сброс или изменение пароля](active-directory-passwords-update-your-own-password.md)
* [Регистрация для самостоятельного сброса пароля](active-directory-passwords-reset-register.md)
* [Какие данные используются для SSPR и какие сведения нужно указывать для пользователей](active-directory-passwords-data.md)
* [Доступные пользователям методы проверки подлинности](active-directory-passwords-how-it-works.md#authentication-methods)
* [Параметры политики для SSPR](active-directory-passwords-policy.md)
* [Что такое обратная запись паролей и каково ее назначение](active-directory-passwords-writeback.md)
* [Как сообщать о действиях в SSPR](active-directory-passwords-reporting.md)
* [Обзор всех параметров SSPR и их значение](active-directory-passwords-how-it-works.md)
* [Как устранить неполадки самостоятельного сброса пароля](active-directory-passwords-troubleshoot.md)
* [Вопросы, не вошедшие в другие статьи](active-directory-passwords-faq.md)

