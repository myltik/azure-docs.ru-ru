---
title: "Соединители в пользовательском интерфейсе Synchronization Service Manager Azure AD Connect | Документация Майкрософт"
description: "Получите общие сведения о вкладке \"Соединители\" в диспетчере службы синхронизации для Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6d893efd775ff6b55524ba3a621d8248adbdd432
ms.contentlocale: ru-ru
ms.lasthandoff: 04/03/2017

---
# Использование соединителей с Synchronization Service Manager Azure AD Connect
<a id="using-connectors-with-the-azure-ad-connect-sync-service-manager" class="xliff"></a>

![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Вкладка "Соединители" используется для управления всеми системами, к которым подключен модуль синхронизации.

## Действия соединителя
<a id="connector-actions" class="xliff"></a>
| Действие | Комментарий |
| --- | --- |
| Создание |Не используйте. Для подключения к дополнительным лесам AD используйте мастер установки. |
| Свойства |Используется для фильтрации доменов и подразделений. |
| [Удалить](#delete) |Используется для удаления данных в пространстве соединителя или удаления подключения к лесу. |
| [Настройка профилей выполнения](#configure-run-profiles) |Ничего настраивать не требуется, кроме фильтрации домена. Это действие можно использовать для просмотра уже настроенных профилей выполнения. |
| Выполнить |Используется для одноразового запуска профиля. |
| Остановить |Останавливает соединитель, на котором в настоящее время запущен профиль. |
| Экспортировать соединитель |Не используйте. |
| Импортировать соединитель |Не используйте. |
| Обновить соединитель |Не используйте. |
| Обновить схему |Обновляет кэшированную схему. Этот параметр рекомендуется использовать в мастере установки, так как он также обновляет правила синхронизации. |
| [Пространство поиска соединителя](#search-connector-space) |Применяется для поиска объектов и [отслеживания объекта и его данных в системе](#follow-an-object-and-its-data-through-the-system). |

### Удалить
<a id="delete" class="xliff"></a>
Действие удаления используется в двух разных целях.  
![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Параметр **Delete connector space only** (Удалить только пространство соединителя) позволяет удалить все данные, но оставить конфигурацию.

Параметр **Delete Connector and connector space** (Удалить соединитель и пространство соединителя) позволяет удалить данные и конфигурацию. Этот параметр используется, если подключение к лесу больше не требуется.

Оба параметра синхронизируют все объекты и обновляют объекты метавселенной. Это действие является длительной операцией.

### Настройка профилей выполнения
<a id="configure-run-profiles" class="xliff"></a>
Этот параметр позволяет просматривать профили выполнения, настроенные для соединителя.

![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### Пространство поиска соединителя
<a id="search-connector-space" class="xliff"></a>
Действие поиска пространства соединителя полезно для поиска объектов и устранения проблем с данными.

![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Начните с выбора **пространства**. Можно выполнять поиск на основе данных (RDN DN, привязка, поддерево) или состояния объекта (все другие параметры).  
![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Если, к примеру, выполняется поиск в поддереве, вы получаете все объекты в одном подразделении.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Здесь можно выбрать объект и **свойства**, а также [отслеживать объект](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) из исходного пространства соединителя через метавселенную до целевого пространства соединителя.

### Изменение пароля учетной записи AD DS
<a id="changing-the-ad-ds-account-password" class="xliff"></a>
В случае изменения пароля учетной записи служба синхронизации больше не сможет импортировать или экспортировать изменения в локальную среду AD.   Может отображаться следующее:

- Шаг импорта или экспорта для соединителя AD завершается ошибкой "no-start-credentials".
- В средстве просмотра событий Windows журнал событий приложения содержит ошибку с идентификатором события 6000 и сообщением об ошибке "Не удалось выполнить управляющий агент "contoso.com", так как учетные данные были недопустимыми".

Чтобы устранить это проблему, обновите учетную запись пользователя AD DS, как показано ниже.


1. Запустите Synchronization Service Manager ("Запуск" → "Служба синхронизации").
</br>![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. Перейдите на вкладку **Соединители**.
3. Выберите соединитель AD, который настроен на использование учетной записи AD DS.
4. В разделе "Действия" выберите **Свойства**.
5. Во всплывающем диалоговом окне выберите "Connect to Active Directory Forest" (Подключиться к лесу Active Directory).
6. Имя леса указывает соответствующую локальную службу AD.
7. Имя пользователя указывает учетную запись AD DS, используемую для синхронизации.
8. В текстовом поле "Пароль" введите новый пароль учетной записи AD DS. ![Служебная программа для ключа шифрования в службе синхронизации Azure AD Connect](media/active-directory-aadconnectsync-encryption-key/key6.png).
9. Нажмите кнопку "ОК" для сохранения нового пароля и перезапустите службу синхронизации, чтобы удалить из кэша памяти старый пароль.



## Дальнейшие действия
<a id="next-steps" class="xliff"></a>
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

