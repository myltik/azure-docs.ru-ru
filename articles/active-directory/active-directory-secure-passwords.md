---
title: "Защита паролей в Azure AD и сброс паролей, которые блокируются с помощью Smart Password Lockout | Документация Майкрософт"
description: "В этом разделе объясняется, что такое клиент Azure AD и как управлять Azure с помощью Azure Active Directory."
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 910ca62c331df0825e1403c19fe2f53672a7359d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/08/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Защита паролей в Azure AD и сброс паролей, которые блокируются с помощью Smart Password Lockout
В этой статье приведены рекомендации для пользователей и администраторов по обеспечению безопасности учетных записей службы Azure Active Directory (Azure AD) и службы учетных записей Майкрософт. 

 >[!NOTE]
 >Администраторы Azure AD могут сбрасывать пароли пользователей, щелкнув имя каталога. На [портале управления Azure](https://manage.windowsazure.com) откройте страницу пользователей, а затем выберите имя пользователя и щелкните "Сбросить пароль". 
 >

Для защиты паролей в Azure AD используются следующие распространенные методы:
 *    настройка требований к длине пароля;
 *    настройка требований к сложности пароля;
 *    регулярная и периодическая смена пароля. 

Сведения о возможностях управления паролями см. в статье [Управление паролями в Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Защита паролей Azure AD
Azure AD и система учетных записей Майкрософт используют проверенные методы, чтобы гарантировать защиту паролей пользователей и администраторов. 

В этом разделе описано, как Azure AD защищает пароли, используя следующие методы:
 *    динамическая блокировка паролей;
 *    Smart Password Lockout.

Сведения об управлении паролями на основе проведенных исследований см. в техническом документе [Password Guidance](http://aka.ms/passwordguidance) (Рекомендации по паролям). 

### <a name="dynamically-banned-passwords"></a>Динамическая блокировка паролей
Azure AD и система учетных записей Майкрософт обеспечивают защиту паролей, динамически блокируя все часто используемые пароли. Команда защиты идентификации Azure регулярно анализирует списки заблокированных паролей, чтобы запретить пользователям использовать стандартные пароли. Эта служба доступна для всех клиентов Azure AD и службы учетных записей Майкрософт. 

Администраторы должны следить за тем, чтобы пользователи создавали необычные пароли, которые содержат уникальное сочетание букв, цифр и символов. Это сведет вероятность взлома паролей пользователей до минимума. 

**Списки нарушений**

Azure AD усердно работает над тем, чтобы быть на шаг впереди от киберпреступников. Помимо прочего, мы запрещаем пользователям создавать пароли, которые могут подвергаться атакам злоумышленников.

Команда защиты идентификации Azure AD постоянно анализирует часто используемые пароли. Киберпреступники также применяют подобные методы (такие как создание [радужной таблицы](https://en.wikipedia.org/wiki/Rainbow_table) для взлома хэшей паролей) для подготовки своих атак. 

Корпорация Майкрософт постоянно анализирует [утечки данных](https://www.privacyrights.org/data-breaches), чтобы поддерживать динамически обновляемый список паролей, что позволяет блокировать уязвимые пароли, прежде чем они станут потенциальной угрозой для клиентов Azure AD. Дополнительные сведения о нашей текущей деятельности по обеспечению безопасности см. в этом [отчете корпорации Майкрософт](https://www.microsoft.com/security/sir/default.aspx). 

### <a name="smart-password-lockout"></a>Smart Password Lockout

Когда Azure AD обнаружит, что потенциальный киберпреступник пытается взломать пароль пользователя, учетная запись пользователя будет автоматически заблокирована с помощью Smart Password Lockout. Azure AD позволяет определить риск, связанный с конкретным сеансом входа. 

Используя самые актуальные данные безопасности, мы предотвращаем возможность возникновения киберугроз. Если киберпреступники попытаются взломать пароль пользователя, учетная запись пользователя не будет заблокирована.

Если пользователь заблокирован в Azure AD, его экран будет выглядеть следующим образом:

  ![Блокировка в Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
Для других учетных записей Майкрософт экран будет выглядеть следующим образом:

  ![Блокировка учетной записи Майкрософт](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Сведения об управлении паролями в Azure Active Directory см. в статье [Как работает управление паролями в Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works).

  >[!NOTE]
  >Если вы являетесь администратором Azure AD, возможно, вам понадобится использовать [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello), чтобы избежать создания традиционных паролей пользователями.
  >

## <a name="next-steps"></a>Дальнейшие действия
[Как изменить свой пароль](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Основы управления удостоверениями Azure](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Получение оперативной аналитики с помощью отчетов об управлении паролями](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



