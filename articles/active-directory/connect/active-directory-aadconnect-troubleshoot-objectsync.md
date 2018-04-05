---
title: 'Azure AD Connect: устранение неполадок с синхронизацией объектов | Документация Майкрософт'
description: В этой статье приводятся пошаговые инструкции по устранению неполадок, связанных с синхронизацией объектов, с помощью задач устранения неполадок.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: 54ae18b9a802fe078d307f4d36400adf806b233f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Устранение неполадок синхронизации объектов с помощью службы синхронизации Azure AD Connect
В этом документе приводятся пошаговые инструкции по устранению неполадок, связанных с синхронизацией объектов, с помощью задач устранения неполадок.

## <a name="troubleshooting-task"></a>Задачи по устранению неполадок
Чтобы устранить неполадки с синхронизацией объектов в развертывании Azure Active Directory (AAD) Connect версии 1.1.749.0 и выше, используйте задачу устранения неполадок в мастере. Если вы используете более ранние версии, выполните устранение неполадок вручную, как описано [здесь](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Запуск задачи устранения неполадок в мастере
Чтобы запустить задачу устранения неполадок в мастере, выполните следующие действия:

1.  Откройте новый сеанс Windows PowerShell на сервере Azure AD Connect и запустите его от имени администратора.
2.  Запустите `Set-ExecutionPolicy RemoteSigned` или `Set-ExecutionPolicy Unrestricted`.
3.  Откройте мастер Azure AD Connect.
4.  Перейдите к странице "Дополнительные задачи", выберите "Устранение неполадок" и щелкните "Далее".
5.  На странице "Устранение неполадок" щелкните "Запуск", чтобы открыть меню устранения неполадок в PowerShell.
6.  В главном меню выберите Troubleshoot Object Synchronization (Устранение неполадок с синхронизацией объектов).

### <a name="troubleshooting-input-parameters"></a>Входные параметры для устранения неполадок
Следующие входные параметры необходимы для выполнения задачи по устранению неполадок.
1.  **Object Distinguished Name** (Различающееся имя объекта) — различающееся имя объекта, неполадки которого требуется устранить.
2.  **AD Connector Name** (Имя соединителя AD) — имя леса AD, где находится указанный выше объект.
3.  Учетные данные глобального администратора клиента Azure AD. ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Изучение результатов задачи устранения неполадок
Задача устранения неполадок выполняет следующие проверки.

1.  Выявляет несоответствия в имени участника-пользователя, если объект синхронизирован с Azure Active Directory.
2.  Проверяет, фильтруется ли объект при фильтрации домена.
3.  Проверяет, фильтруется ли объект при фильтрации подразделения.

В оставшейся части этого раздела описаны возможные результаты, возвращаемые задачей. В каждом случае задача предоставляет анализ и рекомендуемые действия для устранения проблемы.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Выявление несоответствия в имени участника-пользователя при синхронизации объекта с Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Суффикс имени участника-пользователя НЕ проверяется клиентом Azure AD
Если имя субъекта-пользователя (UserPrincipalName) и суффикс альтернативного имени пользователя не проверены клиентом Azure AD Tenant, тогда Azure Active Directory заменяет суффиксы имени участника-пользователя на стандартное доменное имя onmicrosoft.com.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Изменение суффикса имени участника-пользователя при переходе из одного федеративного домена в другой
Azure Active Directory не позволяет выполнить синхронизацию имени участника-пользователя (UserPrincipalName) или суффикса альтернативного имени пользователя, который изменился при переходе из одного федеративного домена в другой. Это относится к доменам, которые проверяются с помощью клиента Azure AD и имеют федеративный тип аутентификации.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Компонент DirSync клиента Azure AD SynchronizeUpnForManagedUsers отключен
Если компонент DirSync клиента Azure AD SynchronizeUpnForManagedUsers отключен, Azure Active Directory не позволяет выполнить обновление синхронизации атрибута UserPrincipalName и альтернативного имени пользователя для учетных записей лицензированных пользователей с помощью управляемой аутентификации.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Фильтрование объекта при фильтрации домена
### <a name="domain-is-not-configured-to-sync"></a>Домен не настроен для синхронизации
Объект находится за пределами области, так как домен не настроен. В указанном ниже примере объект находится за пределами области, так как домен, к которому он принадлежит, фильтруется после синхронизации.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Домен настроен для синхронизации, но отсутствуют профили и шаги выполнения
Объект находится за пределами области, так как в домене отсутствуют профили и шаги выполнения. В указанном ниже примере объект находится за пределами области, так как в домене, к которому он принадлежит, отсутствуют шаги выполнения для профиля выполнения полного импорта.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

### <a name="object-is-filtered-due-to-ou-filtering"></a>Фильтрование объекта при фильтрации подразделения
Объект находится за пределами области из-за настройки фильтрации подразделений. В следующем примере объект принадлежит к подразделению OU=NoSync,DC=bvtadwbackdc,DC=com.  Это подразделение не включено в область синхронизации.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="html-report"></a>Отчет HTML
Помимо выполнения анализа объекта задача устранения неполадок также создает отчет HTML со всеми сведениями об объекте. Этот отчет HTML можно использовать совместно с группой поддержки для дальнейшего устранения неполадок, если потребуется.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).
