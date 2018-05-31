---
title: Справочник по API аудита Azure Active Directory | Документация Майкрософт
description: Как начать работу с API аудита Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e620a7f488e51a60bff6943135831eea0d12816d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158053"
---
# <a name="azure-active-directory-audit-api-reference"></a>Справочник по API аудита Azure Active Directory

> [!TIP] 
> Ознакомьтесь с новым интерфейсом API Microsoft Graph для [отчетности](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit), который в конечном итоге заменит этот API. 


Эта статья входит в серию статей об API отчетов Azure Active Directory. Инструмент создания отчетов Azure AD предоставляет API, с помощью которого можно получить доступ к данным аудита, используя код или связанные инструменты.
Цель этой статьи — предоставить справочные сведения об **API аудита**.

См.:

* Основные сведения см. в разделе [Журналы аудита](active-directory-reporting-azure-portal.md#activity-reports).

* Дополнительные сведения об API отчетов см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).


Сведения:

- Ответы на часто задаваемые вопросы читайте в разделе [вопросы и ответы](active-directory-reporting-faq.md) 

- При возникновении проблем [отправьте запрос в службу поддержки](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>Кто может получить доступ к данным?
* Пользователи с ролью администратора безопасности или читателя безопасности
* Глобальные администраторы
* Любое приложение, имеющее разрешение на доступ к API (авторизация приложения может быть настроена только на основании разрешения глобального администратора)

## <a name="prerequisites"></a>предварительным требованиям
Чтобы получить доступ к этому отчету через API, вам нужно:

* установить [Azure Active Directory Free или более поздней версии](active-directory-whatis.md)
* выполнить [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Получение доступа к API
Получить доступ к API можно с помощью [песочницы Graph](https://graphexplorer2.cloudapp.net) или программным путем, используя, например, PowerShell. Чтобы программа PowerShell правильно интерпретировала синтаксис фильтров OData, используемых в вызовах REST AAD Graph, необходимо использовать обратный апостроф и отделить знак $ escape-символами. Обратный апостроф выступает в качестве [escape-символа PowerShell](https://technet.microsoft.com/library/hh847755.aspx), позволяя PowerShell выполнить точную интерпретацию знака $ и не спутать его с именем переменной PowerShell (например, $filter).

Основное внимание в этой статье уделяется Graph Explorer. Пример PowerShell см. в этом [сценарии PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Конечная точка API

Доступ к этому API можно получить, используя следующий URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Нет ограничений на количество записей, возвращаемых API аудита Azure AD (с использованием разбиения на страницы OData). Пределы хранения данных отчетности см. в разделе [отчеты политики хранения](active-directory-reporting-retention.md).

Этот вызов возвращает данные в пакетах. В каждом пакете содержится не более 1000 записей. Чтобы получить следующий пакет записей, используйте ссылку **"Следующий"**. Получите информацию об игнорируемых токенах из первого набора возвращенных записей. Маркер пропуска можно найти в конце результирующего набора.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Поддерживаемые фильтры

Можно сократить число записей, возвращаемых после вызова API, с помощью фильтра.  
Для данных входа, связанных с API, поддерживаются следующие фильтры:

* **$top=\<число возвращаемых записей\>** позволяет ограничить количество возвращаемых записей. Это дорогостоящая операция. Этот фильтр не следует использовать, если нужно возвратить большое количество объектов.     
* **$filter=\<оператор фильтра\>** позволяет указать тип требуемых записей на основе полей фильтра.

## <a name="supported-filter-fields-and-operators"></a>Поддерживаемый поля и операторы фильтров
Чтобы указать тип требуемых записей, можно создать оператор фильтра, который может содержать одно или несколько из следующих полей:

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

Без учета регистра

- - -
### <a name="targetupn"></a>target/upn
**Поддерживаемые операторы**: eq, startsWith

**Пример**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Примечания**

* Без учета регистра
* Добавьте полное пространство имен при запросе Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

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

* Без учета регистра 
* Добавьте полное пространство имен при запросе Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Дальнейшие действия

- Хотите увидеть примеры отфильтрованных системных операций? См. [примеры API аудита Azure Active Directory](active-directory-reporting-api-audit-samples.md).

- Хотите узнать больше об API отчетов Azure AD? См. статью [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

