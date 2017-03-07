---
title: "Отчет о событиях входа, представляющих риск, на портале Azure Active Directory (предварительная версия) | Документация Майкрософт"
description: "Описание отчета о событиях входа, представляющих риск, на портале предварительной версии Azure Active Directory."
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 69b2166dcbc3e4abd99084b47907c90e157791de
ms.lasthandoff: 02/22/2017


---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal---preview"></a>Отчет о событиях входа, представляющих риск, на портале Azure Active Directory (предварительная версия)

С помощью отчетов о безопасности в [предварительной версии](active-directory-preview-explainer.md) Azure Active Directory можно получить ценную информацию о наличии скомпрометированных учетных записей пользователей в вашей среде. 

Azure Active Directory обнаруживает подозрительные действия, связанные с учетными записями пользователей. Для каждого обнаруженного действия создается запись, которая называется *событием риска*. Дополнительные сведения см. в статье о [событиях риска в Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Обнаруженные события риска используются для вычисления следующих параметров.

- **Входы, представляющие риск**. Вход, представляющий риск, означает, что в систему пытался войти пользователь, который не является законным владельцем учетной записи. Дополнительные сведения см. в [этом разделе](active-directory-identityprotection.md#risky-sign-ins). 

- **Пользователи, находящиеся в группе риска**. Такая пометка означает, что конфиденциальность учетной записи пользователя, возможно, нарушена. Дополнительные сведения см. в разделе о [пользователях, находящихся в группе риска](active-directory-identityprotection.md#users-flagged-for-risk).  

На портале Azure отчеты о безопасности можно найти в колонке **Azure Active Directory** в разделе **Безопасность**. 

![События входа, представляющие риск](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="azure-active-directory-free-and-basic-edition"></a>Выпуски "Бесплатный" и "Базовый" Azure Active Directory

В выпусках "Бесплатный" и "Базовый" Azure Active Directory вы можете просмотреть информацию об обнаруженных входах, представляющих риск, которые связаны с вашими пользователями. Отчет о событиях риска содержит следующие сведения.

- **Пользователь** — имя пользователя, которое использовалось во время операции входа.
- **IP-адрес** — IP-адрес устройства, которое использовалось для подключения к Azure Active Directory.
- **Расположение** — расположение, которое использовалось для подключения к Azure Active Directory.
- **Время входа** — время выполнения входа.
- **Состояние** — состояние входа.

Данные из этого отчета можно скачать.

![События входа, представляющие риск](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Ознакомившись со сведениями о входе, представляющем риск, вы можете отправить отзыв в Azure Active Directory в виде одной из следующих команд.

- Разрешить
- Пометить как ложное срабатывание
- Игнорировать
- Повторно активировать

![События входа, представляющие риск](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Дополнительные сведения см. в разделе [Закрытие событий риска вручную](active-directory-identityprotection.md#closing-risk-events-manually).

## <a name="azure-active-directory-premium-editions"></a>Выпуски Azure Active Directory Premium

Отчет о событиях входа, представляющих риск, в выпусках Azure Active Directory Premium предоставляет следующее.

- Сводная информация о [типах обнаруженных событий риска](active-directory-identity-protection-risk-events.md).

- Возможность скачать отчет.


![События входа, представляющие риск](./media/active-directory-reporting-security-risky-sign-ins/456.png)


При выборе события риска отображается подробное представление отчета об этом событии, предоставляющее перечисленные ниже возможности.

- Настройка [политики устранения рисков пользователей](active-directory-identityprotection.md#user-risk-security-policy).  

- Просмотр временной шкалы обнаружения для события риска.  

- Просмотр списка пользователей, для которых было обнаружено это событие риска.

- [Ручное закрытие события риска](active-directory-identityprotection.md#closing-risk-events-manually) или повторная активация события риска, закрытого вручную. 


![События входа, представляющие риск](./media/active-directory-reporting-security-risky-sign-ins/457.png)

При выборе пользователя отображается подробное представление отчета об этом пользователе, предоставляющее перечисленные ниже возможности.

- Отображение представления всех событий входа.

- Сброс пароля пользователя

- Отклонение всех событий.

- Изучение обнаруженных событий риска для пользователя. 


![События входа, представляющие риск](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Чтобы изучить событие риска, выберите его из списка.  
Откроется колонка **Сведения** для этого события риска. В колонке **Сведения** вы можете либо [вручную закрыть событие риска](active-directory-identityprotection.md#closing-risk-events-manually), либо повторно активировать событие риска, закрытое вручную. 


![События входа, представляющие риск](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о защите идентификации Azure см. в статье [Защита идентификации Azure Active Directory](active-directory-identityprotection.md).


