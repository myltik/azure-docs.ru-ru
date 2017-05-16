---

title: "Отчет системы безопасности о пользователях под угрозой на портале Azure Active Directory | Документация Майкрософт"
description: "Описание отчета системы безопасности о пользователях под угрозой на портале Azure Active Directory."
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 01ecb98c02b2a01007c7f76805d4db4b7aeee1f0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---
# <a name="users-at-risk-security-report-in-the-azure-active-directory-portal"></a>Отчет системы безопасности о пользователях под угрозой на портале Azure Active Directory

С помощью отчетов о безопасности в Azure Active Directory (Azure AD) можно получить ценную информацию о наличии скомпрометированных учетных записей пользователей в вашей среде. 

Azure Active Directory обнаруживает подозрительные действия, связанные с учетными записями пользователей. Для каждого обнаруженного действия создается запись, которая называется *событием риска*. Дополнительные сведения см. в статье о [событиях риска в Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Обнаруженные события риска используются для вычисления следующих параметров.

- **Входы, представляющие риск**. Вход, представляющий риск, означает, что в систему пытался войти пользователь, который не является законным владельцем учетной записи. Дополнительные сведения см. в [этом разделе](active-directory-identityprotection.md#risky-sign-ins). 

- **Пользователи, находящиеся в группе риска**. Такая пометка означает, что конфиденциальность учетной записи пользователя, возможно, нарушена. Дополнительные сведения см. в разделе о [пользователях, находящихся в группе риска](active-directory-identityprotection.md#users-flagged-for-risk).  

На портале Azure отчеты о безопасности можно найти в колонке **Azure Active Directory** в разделе **Безопасность**.  

![События входа, представляющие риск](./media/active-directory-reporting-security-user-at-risk/10.png)

## <a name="azure-active-directory-free-and-basic-edition"></a>Выпуски "Бесплатный" и "Базовый" Azure Active Directory

В выпусках "Бесплатный" и "Базовый" Azure Active Directory доступен список учетных записей пользователей, конфиденциальность которых могла быть нарушена. 


![События входа, представляющие риск](./media/active-directory-reporting-security-user-at-risk/03.png)

Если выбрать пользователя, откроется колонка его данных.
Вы можете просмотреть журнал входов пользователя под угрозой и сбросить пароль, если это необходимо.

![События входа, представляющие риск](./media/active-directory-reporting-security-user-at-risk/46.png)

## <a name="azure-active-directory-premium-editions"></a>Выпуски Azure Active Directory Premium

Отчет о пользователях под угрозой в выпусках Azure Active Directory Premium предоставляет следующее.

- [Список учетных записей пользователей](active-directory-identityprotection.md#users-flagged-for-risk), которые, возможно, были скомпрометированы. 

- Сводная информация о [типах обнаруженных событий риска](active-directory-identity-protection-risk-events.md).

- Возможность скачать отчет.

- Настройка [политики устранения рисков пользователей](active-directory-identityprotection.md#user-risk-security-policy).  


![События входа, представляющие риск](./media/active-directory-reporting-security-user-at-risk/71.png)

При выборе пользователя отображается подробное представление отчета об этом пользователе, предоставляющее перечисленные ниже возможности.

- Отображение представления всех событий входа.

- Сброс пароля пользователя

- Отклонение всех событий.

- Изучение обнаруженных событий риска для пользователя. 


![События входа, представляющие риск](./media/active-directory-reporting-security-user-at-risk/324.png)


Чтобы изучить событие риска, выберите его из списка.  
Откроется колонка **Сведения** для этого события риска. В колонке **Сведения** вы можете либо [вручную закрыть событие риска](active-directory-identityprotection.md#closing-risk-events-manually), либо повторно активировать событие риска, закрытое вручную. 


![События входа, представляющие риск](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о защите идентификации Azure см. в статье [Защита идентификации Azure Active Directory](active-directory-identityprotection.md).


