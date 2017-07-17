---
title: "Уведомления отчетов Azure Active Directory"
description: "Использование уведомлений отчетов Azure Active Directory для определения подозрительных событий во время входа в систему."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 90ba006f25c27e1dc24c85fdc4ef1d5e2a8d0e3d
ms.contentlocale: ru-ru
ms.lasthandoff: 12/28/2016

---
# Уведомления отчетов Azure Active Directory
<a id="azure-active-directory-reporting-notifications" class="xliff"></a>
## Какие отчеты создают уведомления по электронной почте
<a id="what-reports-generate-email-notifications" class="xliff"></a>
В настоящее время только отчеты «Нестандартные действия при входе» инициируют уведомления по электронной почте.

## Что такое «нестандартные попытки входа»?
<a id="what-is-an-irregular-sign-in" class="xliff"></a>
Нестандартные входы — это входы, которые определяет алгоритм машинного обучения, исходя из невозможности путешествия, а также аномального расположения входа и устройства, используемого для входа. Это может означать, что с помощью этой учетной записи пытается войти злоумышленник.

## Кто получает уведомления по электронной почте?
<a id="who-receives-the-email-notifications" class="xliff"></a>
Сообщение отправляется всем глобальным администраторам, которым назначена лицензия Active Directory Premium. Чтобы убедиться, что сообщение доставлено, мы также отправляем его на альтернативный адрес электронной почты администраторов. Администраторы должны включить адрес aad-alerts-noreply@mail.windowsazure.com в свой список надежных отправителей, чтобы не пропустить сообщение электронной почты.

## Как часто отправляются эти сообщения?
<a id="how-often-are-these-emails-sent" class="xliff"></a>
Письмо отправляется, если за последние 30 дней или с момента отправки последнего письма (в зависимости от того, что меньше) происходит 10 новых нестандартных операций входа.

## Как получить доступ к отчету, указанному в сообщении электронной почты
<a id="how-do-i-access-the-report-mentioned-in-the-email" class="xliff"></a>
Если щелкнуть ссылку, вы перейдете на страницу отчета на классическом портале Azure. Чтобы получить доступ к отчету, необходимо одновременно быть:

* администратором или соадминистратором подписки Azure;
* глобальным администратором в каталоге с назначенной лицензией Active Directory Premium. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-editions.md).

## Можно ли отключить эти сообщения?
<a id="can-i-turn-off-these-emails" class="xliff"></a>
Да, чтобы отключить уведомления об аномальных операциях входа на классическом портале Azure, щелкните **Настройка** и выберите **Отключено** в разделе **Уведомления**.

## Что дальше?
<a id="whats-next" class="xliff"></a>
* Интересуют доступные отчеты о безопасности, аудиту и действиях? См. раздел [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md).
* [Начало работы с Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Добавление фирменной символики компании на страницах  входа и панели доступа](active-directory-add-company-branding.md)


