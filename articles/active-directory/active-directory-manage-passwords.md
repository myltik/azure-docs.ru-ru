---
title: "Управление паролями в Azure Active Directory | Документация Майкрософт"
description: "Сведения об управлении паролями в Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a7679724-0ed5-4973-92e2-bd1285a6ef93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 794b1e5deca6c3bda078e3ea9006d334bc3a8052


---
# <a name="manage-passwords-in-azure-active-directory"></a>Управление паролями в Azure Active Directory
Если вы являетесь администратором, вы можете сбросить пароль пользователя в Azure Active Directory (Azure AD) на классическом портале Azure. Щелкните имя каталога, на странице «Пользователи» щелкните имя пользователя, а в нижней части портала щелкните **Сбросить пароль**.

В остальной части этой статьи рассматривается весь набор возможностей управления паролями, которые поддерживает Azure AD, в том числе перечисленные ниже.

* **Самостоятельное изменение пароля** позволяет конечным пользователям или администраторам менять свои пароли с истекшим или не истекшим сроком действия без поддержки администратора или службы технической поддержки.
* **Самостоятельный сброс пароля** позволяет конечным пользователям или администраторам автоматически сбрасывать свои пароли без вызова администратора или обращения в службу технической поддержки. Для самостоятельного сброса пароля требуется Azure AD Premium или Basic. Дополнительные сведения см. в разделе [Выпуски Azure Active Directory](active-directory-editions.md).
* **Сброс пароля администратором**. Администратор может сбросить пароль пользователя или другого администратора на классическом портале Azure.
* **Отчеты по операциям управления паролями** позволяют администраторам лучше понять ситуацию с регистрацией и сбросом паролей в организации.
* **Обратная запись паролей** позволяет управлять локальными паролями из облака, чтобы федеративный пользователь или пользователь с синхронизированным паролем смог выполнять все сценарии, указанные выше, или эти сценарии можно было выполнить от его лица. Для обратной записи паролей требуется Azure AD Premium. Дополнительные сведения см. в разделе [Приступая к работе с Azure Active Directory Premium](active-directory-get-started-premium.md).

> [!NOTE]
> Китайским клиентам служба Azure AD Premium доступна в качестве веб-экземпляра Azure AD, доступного по всему миру. Служба Microsoft Azure, обслуживаемая компанией 21Vianet в Китае, сейчас не поддерживает AD Premium. Чтобы получить дополнительную информацию, свяжитесь с нами на [форуме Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).
>
>

Используйте следующие ссылки, чтобы перейти к документации, в которой вы заинтересованы больше всего.

* [Общие сведения: управление паролями в Azure AD](active-directory-passwords-how-it-works.md)
* [Самостоятельный сброс пароля в Azure AD: порядок включения, настройки и тестирования самостоятельного сброса паролей](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
* [Самостоятельный сброс пароля в Azure AD: как настроить сброс пароля согласно своим потребностям](active-directory-passwords-customize.md)
* [Самостоятельный сброс пароля в Azure AD: рекомендации по развертыванию и управлению](active-directory-passwords-best-practices.md)
* [Отчеты об управлении паролями в Azure AD: как просматривать операции управления паролями в клиенте](active-directory-passwords-get-insights.md)
* [Обратная запись паролей: настройка Azure AD для управления локальными паролями](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
* [Устранение неполадок, связанных с управлением паролями Azure AD](active-directory-passwords-troubleshoot.md)
* [Часто задаваемые вопросы об управлении паролями Azure AD](active-directory-passwords-faq.md)

## <a name="whats-next"></a>Что дальше?
* [Администрирование Azure AD](active-directory-administer.md)
* [Создание и изменение пользователей в Azure AD](active-directory-create-users.md)
* [Управление группами в Azure AD](active-directory-manage-groups.md)



<!--HONumber=Dec16_HO4-->


