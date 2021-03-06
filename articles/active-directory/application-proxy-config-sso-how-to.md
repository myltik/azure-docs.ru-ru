---
title: Как настроить единый вход для приложения прокси приложения | Документы Майкрософт
description: В этой статье описывается, как быстро настроить единый вход для приложения прокси приложения.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c090a3fc36c300a108f10f02d0b663dc1e29f986
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156033"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Как настроить единый вход для приложения прокси приложения

Единый вход (SSO) позволяет пользователям обращаться к приложению несколько раз без повторной проверки подлинности. Он позволяет выполнить одну проверку подлинности для Azure Active Directory в облаке, после чего соединитель или служба будут выполнять дополнительные проверки подлинности для приложения от лица пользователя.

## <a name="how-to-configure-single-sign-on"></a>Как настроить единый вход
Чтобы настроить единый вход, убедитесь, что для приложения настроена предварительная проверка подлинности с использованием Azure Active Directory. Для этой конфигурации выберите **Azure Active Directory** -&gt; **Корпоративные приложения** -&gt; **Все приложения** -&gt; ваше приложение **-&gt; Прокси приложения**. На этой странице найдите параметр "Предварительная проверка подлинности" и убедитесь, что для него установлено значение "Azure Active Directory". 

Дополнительные сведения о методах предварительной проверки подлинности см. в шаге 4 в [документе, посвященном публикации приложения](manage-apps/application-proxy-publish-azure-portal.md).

   ![Метод предварительной проверки подлинности на портале Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Настройка режимов единого входа для приложения прокси приложения
Настройте конкретный тип единого входа. Методы входа подразделяются в зависимости от того, какой тип проверки подлинности используется серверным приложением. Приложения прокси поддерживают три типа входа:

-   **Вход на основе пароля**: может использоваться для любого приложения, в котором для входа используются поля с именем пользователя и паролем. Действия по настройке описаны в [документации по настройке единого входа на основе пароля](active-directory-enterprise-apps-whats-new-azure-portal.md#bring-your-own-password-sso-applications).

-   **Встроенная проверка подлинности Windows**: для приложений, использующих встроенную проверку подлинности Windows, единый вход выполняется с использованием ограниченного делегирования Kerberos. Этот метод дает соединителям прокси приложения в Active Directory возможность действовать от имени пользователей (в частности, отправлять и получать маркеры). Дополнительные сведения о настройке ограниченного делегирования Kerberos можно найти в [документации по настройке единого входа на основе ограниченного делегирования Kerberos](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

-   **Вход на основе заголовка**: обеспечивается с помощью партнерства и требует дополнительной настройки. Дополнительные сведения о партнерстве и пошаговые инструкции по настройке единого входа с проверкой подлинности на основе заголовка см. в [документации по PingAccess для Azure AD](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md).

Каждый из этих вариантов входа можно выбрать, открыв свое приложение в разделе "Корпоративные приложения" и выбрав страницу **Единый вход** в меню слева. Обратите внимание, что если приложение было создано в старой версии портала, могут быть доступны не все варианты входа.

На этой странице отображается еще один дополнительный вариант единого входа: связанный единый вход. Этот вариант также поддерживается прокси приложения. Однако при выборе этого варианта единый вход для приложения не настраивается. Предположим, что для приложения уже настроен единый вход с использованием другой службы, например служб федерации Active Directory. 

Этот вариант позволяет создать ссылку на приложение, которое будет открываться для пользователей первым при обращении к вашему приложению. Например, если есть приложение, которое выполняет проверку подлинности пользователей с помощью служб федерации Active Directory 2.0, администратор может создать ссылку на него на панели доступа, используя параметр "Связанный единый вход".

## <a name="next-steps"></a>Дополнительная информация
[Реализация единого входа в приложения с помощью прокси приложения](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
