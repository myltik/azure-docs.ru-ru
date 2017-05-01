---
title: "Справочник по API аудита Azure Active Directory | Документация Майкрософт"
description: "Как начать работу с API аудита Azure Active Directory"
services: active-directory
documentationcenter: 
author: markusvi
manager: femila
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 87c7990834eaf2aa6c4aff0c341150ba9bd9eed4
ms.lasthandoff: 04/10/2017


---
# <a name="azure-active-directory-audit-api-reference"></a>Справочник по API аудита Azure Active Directory
Эта статья входит в серию статей об API отчетов Azure Active Directory.  
Инструмент создания отчетов Azure AD предоставляет API, с помощью которого можно получить доступ к данным аудита, используя код или связанные инструменты.
Цель этой статьи — предоставить справочные сведения об **API аудита**.

См.:

* Основные сведения см. в разделе [Журналы аудита](active-directory-reporting-azure-portal.md#audit-logs).
* Дополнительные сведения об API отчетов см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

Чтобы задать вопросы, обговорить проблемы или предоставить отзыв, обратитесь в [службу поддержки по инструментам создания отчетов AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="who-can-access-the-data"></a>Кто может получить доступ к данным?
* Пользователи с ролью администратора безопасности или читателя безопасности
* Глобальные администраторы
* Любое приложение с разрешением на доступ к API (авторизацию приложения можно настроить только на основе разрешения глобального администратора)

## <a name="prerequisites"></a>Предварительные требования
Для доступа к этому отчету с помощью API отчетов нужно:

* установить [Azure Active Directory Free или более поздней версии](active-directory-editions.md)
* выполнить [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Получение доступа к API
Получить доступ к API можно с помощью [песочницы Graph](https://graphexplorer2.cloudapp.net) или программным путем, используя, например, PowerShell. Чтобы программа PowerShell правильно интерпретировала синтаксис фильтров OData, используемых в вызовах REST AAD Graph, необходимо использовать обратный апостроф и отделить знак $ escape-символами. Обратный апостроф выступает в качестве [escape-символа PowerShell](https://technet.microsoft.com/library/hh847755.aspx), позволяя PowerShell выполнить точную интерпретацию знака $ и не спутать его с именем переменной PowerShell (т. е. $filter).

В этой статье внимание уделяется Graph Explorer. Пример PowerShell см. в этом [сценарии PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Конечная точка API
Доступ к этому API можно получить, используя следующий URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Количество записей, возвращаемых API аудита Azure AD (с помощью разбиения на страницы OData), не ограничено.
Сведения о периоде удержания данных отчетов приведены в статье [Политики периода удержания отчетов](active-directory-reporting-retention.md).

Этот вызов возвращает данные в пакетах. В каждом пакете содержится не более 1000 записей.  
Чтобы получить следующий пакет записей, используйте ссылку "Следующий". Получите сведения о маркере пропуска из первого набора полученных записей. Маркер пропуска можно найти в конце результирующего набора.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Поддерживаемые фильтры
Можно сократить число записей, возвращаемых после вызова API, в виде фильтра.  
Данные, связанные с API входа, поддерживают следующие фильтры:

* **$top=\<число возвращаемых записей\>** позволяет ограничить количество возвращаемых записей. Это дорогостоящая операция. Этот фильтр не следует использовать, если нужно возвратить большое количество объектов.     
* **$filter=\<оператор фильтра\>** позволяет указать тип требуемых записей на основе полей фильтра.

## <a name="supported-filter-fields-and-operators"></a>Поддерживаемый поля и операторы фильтров
Чтобы указать тип требуемых записей, можно создать оператор фильтра, который может содержать одно или несколько из следующих полей фильтра:

* [activityDate](#activitydate) определяет дату или диапазон дат;
* [category](#category) определяет категорию, по которой необходимо отфильтровать;
* [activityStatus](#activitystatus) определяет состояние действия;
* [activityType](#activitytype) определяет тип действия;
* [activity](#activity) определяет действие в виде строки;  
* [actor/name](#actorname) определяет субъект в виде имени субъекта;
* [actor/objectid](#actorobjectid) определяет субъект в виде идентификатора субъекта;   
* [actor/upn](#actorupn) определяет субъект в виде имени участника-пользователя (UPN); 
* [target/name](#targetname) определяет целевой объект в виде имени субъекта;
* [target/objectid](#targetobjectid) определяет целевой объект в виде идентификатора целевого объекта;  
* [target/upn](#targetupn) определяет целевой объект в виде основного имени пользователя (UPN) субъекта.   

- - -
### <a name="activitydate"></a>activityDate
**Поддерживаемые операторы**: eq, ge, le, gt, lt

**Пример**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Примечания**

Время и дата указываются в формате UTC.

- - -
### <a name="category"></a>category

**Поддерживаемые значения**:

| Категория                         | Значение     |
| :--                              | ---       |
| "Core Directory" (Основной каталог);                   | Каталог |
| "Self-service Password Management" (Самостоятельное управление паролями); | SSPR      |
| "Self-service Group Management" (Самостоятельное управление группами);    | SSGM      |
| "Account Provisioning" (Подготовка учетных записей).             | Sync      |
| "Automated Password Rollover" (Автоматическая смена пароля)      | "Automated Password Rollover" (Автоматическая смена пароля) |
| Защита идентификации              | IdentityProtection |
| Invited Users (Приглашаемые пользователи)                    | Invited Users (Приглашаемые пользователи) |
| MIM Service (Служба MIM)                      | MIM Service (Служба MIM) |



**Поддерживаемые операторы**: eq

**Пример**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>activityStatus

**Поддерживаемые значения**:

| Состояние действия | Значение |
| :--             | ---   |
| Успешно         | 0     |
| Сбой         | - 1   |

**Поддерживаемые операторы**: eq

**Пример**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>activityType
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=activityType eq 'User'    

**Примечания**

Учитывает регистр.

- - -
### <a name="activity"></a>activity
**Поддерживаемые операторы**: eq, contains, startsWith

**Пример**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Примечания**

Учитывает регистр.

- - -
### <a name="actorname"></a>actor/name
**Поддерживаемые операторы**: eq, contains, startsWith

**Пример**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Примечания**

Не учитывает регистр.

- - -
### <a name="actorobjectid"></a>actor/objectid
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>target/name
**Поддерживаемые операторы**: eq, contains, startsWith

**Пример**:

    $filter=targets/any(t: t/name eq 'some name')    

**Примечания**

Не учитывает регистр.

- - -
### <a name="targetupn"></a>target/upn
**Поддерживаемые операторы**: eq, startsWith

**Пример**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Примечания**

* Без учета регистра
* При запросе Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity необходимо добавить полное пространство имен.

- - -
### <a name="targetobjectid"></a>target/objectid
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>actor/upn
**Поддерживаемые операторы**: eq, startsWith

**Пример**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Примечания**

* Не учитывает регистр. 
* При запросе Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity необходимо добавить полное пространство имен.

- - -
## <a name="next-steps"></a>Дальнейшие действия
* Хотите увидеть примеры отфильтрованных системных операций? См. [примеры API аудита Azure Active Directory](active-directory-reporting-api-audit-samples.md).
* Хотите узнать больше об API отчетов Azure AD? См. статью [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).


