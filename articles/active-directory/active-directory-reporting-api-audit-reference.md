<properties
    pageTitle="Справочник по API аудита Azure Active Directory | Microsoft Azure"
    description="Как начать работу с API аудита Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# Справочник по API аудита Azure Active Directory

Эта статья входит в серию статей об API отчетов Azure Active Directory. Инструмент создания отчетов Azure AD предоставляет API, с помощью которого можно получить доступ к данным аудита, используя код или связанные инструменты. Цель этой статьи — предоставить справочные сведения об **API аудита**.

См.:

- Основные сведения см. в разделе [Журналы аудита](active-directory-reporting-azure-portal.md#audit-logs).
- Дополнительные сведения об API отчетов см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

Чтобы задать вопросы, обговорить проблемы или предоставить отзыв, обратитесь в [службу поддержки по инструментам создания отчетов AAD](mailto:aadreportinghelp@microsoft.com).


## Кто может получить доступ к данным?

- Пользователи с ролью администратора безопасности или читателя безопасности

- Глобальные администраторы

- Любое приложение с разрешением на доступ к API (авторизацию приложения можно настроить только на основе разрешения глобального администратора)



## Предварительные требования

Для доступа к этому отчету с помощью API отчетов нужно:

- установить [Azure Active Directory Free или более поздней версии](active-directory-editions.md);

- выполнить [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites.md).
 

##Получение доступа к API

Получить доступ к API можно с помощью [Graph Explorer](https://graphexplorer2.cloudapp.net) или программным путем, используя, например, PowerShell. Чтобы программа PowerShell правильно интерпретировала синтаксис фильтров OData, используемых в вызовах REST AAD Graph, необходимо использовать обратный апостроф и отделить знак $ escape-символами. Обратный апостроф выступает в качестве [escape-символа PowerShell](https://technet.microsoft.com/library/hh847755.aspx), позволяя PowerShell выполнить точную интерпретацию знака $ и не спутать его с именем переменной PowerShell (т. е. $filter).

В этой статье внимание уделяется Graph Explorer. Пример PowerShell см. в [сценарии PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

 
 

## Конечная точка API


Доступ к этому API можно получить, используя следующий URI:

	https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Количество записей, возвращаемых API аудита Azure AD (с помощью разбиения на страницы OData), не ограничено. Сведения о периоде удержания данных отчетов приведены в статье [Политики периода удержания отчетов](active-directory-reporting-retention.md).

Этот вызов возвращает данные в пакетах. В каждом пакете содержится не более 1000 записей. Чтобы получить следующий пакет записей, используйте ссылку "Следующий". Получите сведения о маркере пропуска из первого набора полученных записей. Маркер пропуска можно найти в конце результирующего набора.

	https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## Поддерживаемые фильтры

Можно сократить число записей, возвращаемых после вызова API, в виде фильтра. Данные, связанные с API входа, поддерживают следующие фильтры:

- **$top=<число возвращаемых записей>** позволяет ограничить количество возвращаемых записей. Это дорогостоящая операция. Этот фильтр не следует использовать, если нужно возвратить большое количество объектов.
- **$filter=<оператор фильтра>** позволяет указать тип требуемых записей на основе полей фильтра.



## Поддерживаемый поля и операторы фильтров

Чтобы указать тип требуемых записей, можно создать оператор фильтра, который может содержать одно или несколько из следующих полей фильтра:

- [activityDate](#activitydate) определяет дату или диапазон дат;
- [activityType](#activitytype) определяет тип действия;
- [activity](#activity) определяет действие в виде строки;
- [actor/name](#actorname) определяет субъект в виде имени субъекта;
- [actor/objectid](#actorobjectid) определяет субъект в виде идентификатора субъекта;
- [actor/upn](#actorupn) определяет субъект в виде основного имени пользователя (UPN) субъекта;
- [target/name](#targetname) определяет целевой объект в виде имени субъекта;
- [target/objectid](#targetobjectid) определяет целевой объект в виде идентификатора целевого объекта;
- [target/upn](#targetupn) определяет целевой объект в виде основного имени пользователя (UPN) субъекта.




----------

### activityDate

**Поддерживаемые операторы**: eq, ge, le, gt, lt

**Пример**:

	$filter=activityDate ge 2016-01-01T00:00:00Z and activityDate le 2016-01-02T00:00:00Z	

**Примечания**

Время и дата указываются в формате UTC.

----------

### activityType

**Поддерживаемые операторы**: eq

**Пример**:

	$filter=activityType eq 'User'	

**Примечания**

Учитывает регистр.

----------

### действие

**Поддерживаемые операторы**: eq, contains, startsWith

**Пример**:

	$filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')	

**Примечания**

Учитывает регистр.

----------

### actor/name

**Поддерживаемые операторы**: eq, contains, startsWith

**Пример**:

	$filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')	

**Примечания**

Не учитывает регистр.

	

----------
### actor/objectId

**Поддерживаемые операторы**: eq

**Пример**:

	$filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'	

----------
### target/name

**Поддерживаемые операторы**: eq, contains, startsWith

**Пример**:

	$filter=target/name eq 'test' or contains(target/name, 'test') or startswith(target/name, 'test')	

**Примечания**

Без учета регистра

----------

### target/upn

**Поддерживаемые операторы**: eq, startsWith

**Пример**:

	$filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))	

**Примечания**

- Без учета регистра
- При запросе Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity необходимо добавить полное пространство имен.

----------

### target/objectId

**Поддерживаемые операторы**: eq

**Пример**:

	$filter=target/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'	

----------

### actor/upn

**Поддерживаемые операторы**: eq, startsWith

**Пример**:

	$filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')	

**Примечания**

- Без учета регистра
- При запросе Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity необходимо добавить полное пространство имен.

----------




## Дальнейшие действия

- Хотите увидеть примеры отфильтрованных системных операций? См. [примеры API аудита Azure Active Directory](active-directory-reporting-api-audit-samples.md).

- Хотите узнать больше об API отчетов Azure AD? См. статью [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

<!---HONumber=AcomDC_0928_2016-->