---
title: "Соединители в пользовательском интерфейсе Synchronization Service Manager Azure AD Connect | Документация Майкрософт"
description: "Получите общие сведения о вкладке &quot;Соединители&quot; в диспетчере службы синхронизации для Azure AD Connect."
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
ms.date: 03/02/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bd2240678fed44db748ae062bdf91e457159b4a2
ms.lasthandoff: 03/04/2017

---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Использование соединителей с Synchronization Service Manager Azure AD Connect

![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Вкладка "Соединители" используется для управления всеми системами, к которым подключен модуль синхронизации.

## <a name="connector-actions"></a>Действия соединителя
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

### <a name="delete"></a>Удалить
Действие удаления используется в двух разных целях.  
![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Параметр **Delete connector space only** (Удалить только пространство соединителя) позволяет удалить все данные, но оставить конфигурацию.

Параметр **Delete Connector and connector space** (Удалить соединитель и пространство соединителя) позволяет удалить данные и конфигурацию. Этот параметр используется, если подключение к лесу больше не требуется.

Оба параметра синхронизируют все объекты и обновляют объекты метавселенной. Это действие является длительной операцией.

### <a name="configure-run-profiles"></a>Настройка профилей выполнения
Этот параметр позволяет просматривать профили выполнения, настроенные для соединителя.

![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Пространство поиска соединителя
Действие поиска пространства соединителя полезно для поиска объектов и устранения проблем с данными.

![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Начните с выбора **пространства**. Можно выполнять поиск на основе данных (RDN DN, привязка, поддерево) или состояния объекта (все другие параметры).  
![Диспетчер службы синхронизации](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Если, к примеру, выполняется поиск в поддереве, вы получаете все объекты в одном подразделении.  
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
Здесь можно выбрать объект и **свойства**, а также [отслеживать объект](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) из исходного пространства соединителя через метавселенную до целевого пространства соединителя.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

