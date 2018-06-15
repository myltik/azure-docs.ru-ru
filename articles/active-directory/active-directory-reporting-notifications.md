---
title: Уведомления отчетов Azure Active Directory
description: Использование уведомлений отчетов Azure Active Directory для определения подозрительных событий во время входа в систему.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 01/03/2018
ms.author: dhanyahk;rolyon
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: 8f4beece9d1d351c91f2bb055eedc0ae9851f532
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588194"
---
# <a name="azure-active-directory-reporting-notifications"></a>Уведомления отчетов Azure Active Directory
## <a name="what-reports-generate-email-notifications"></a>Какие отчеты создают уведомления по электронной почте
В настоящее время только отчеты «Нестандартные действия при входе» инициируют уведомления по электронной почте.

## <a name="what-is-an-irregular-sign-in"></a>Что такое «нестандартные попытки входа»?
Нестандартные входы — это входы, которые определяет алгоритм машинного обучения, исходя из невозможности путешествия, а также аномального расположения входа и устройства, используемого для входа. Это может означать, что с помощью этой учетной записи пытается войти злоумышленник.

## <a name="who-receives-the-email-notifications"></a>Кто получает уведомления по электронной почте?
Сообщение отправляется всем глобальным администраторам, которым назначена лицензия Active Directory Premium. Чтобы убедиться, что сообщение доставлено, мы также отправляем его на альтернативный адрес электронной почты администраторов. Администраторы должны включить адрес aad-alerts-noreply@mail.windowsazure.com в свой список надежных отправителей, чтобы не пропустить сообщение электронной почты.

## <a name="how-often-are-these-emails-sent"></a>Как часто отправляются эти сообщения?
Письмо отправляется, если за последние 30 дней или с момента отправки последнего письма (в зависимости от того, что меньше) происходит 10 новых нестандартных операций входа.

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>Как получить доступ к отчету, указанному в сообщении электронной почты
Если щелкнуть ссылку, вы перейдете на страницу отчета на портале Azure. Чтобы получить доступ к отчету, необходимо одновременно быть:

* администратором или соадминистратором подписки Azure;
* глобальным администратором в каталоге с назначенной лицензией Active Directory Premium. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-whatis.md).

## <a name="can-i-turn-off-these-emails"></a>Можно ли отключить эти сообщения?
Да, чтобы отключить уведомления об аномальных операциях входа на портале Azure, щелкните **Настройка** и выберите **Отключено** в разделе **Уведомления**.

## <a name="whats-next"></a>Что дальше?
* Интересуют доступные отчеты о безопасности, аудиту и действиях? См. раздел [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md).
* [Начало работы с Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Добавление фирменной символики компании на страницах входа и панели доступа](customize-branding.md)

