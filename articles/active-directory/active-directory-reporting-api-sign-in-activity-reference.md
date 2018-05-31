---
title: Справочник по API отчета о событии входа в Azure Active Directory | Документация Майкрософт
description: Справочник по API отчета о действиях при входе Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3831146caad4fe922e482ce782d5d41fb70338f4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155802"
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Справочник по API отчета о действиях при входе Azure Active Directory

> [!TIP] 
> Ознакомьтесь с новым интерфейсом API Microsoft Graph для [отчетности](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit), который в конечном итоге заменит этот API. 

Эта статья входит в серию статей об API отчетов Azure Active Directory. Инструмент создания отчетов Azure AD предоставляет API, с помощью которого можно получить доступ к данным аудита, используя код или связанные инструменты.
Цель этой статьи — предоставить справочные сведения об **API аудита**.

См.:

* Основные сведения см. в разделе [Действия при входе](active-directory-reporting-azure-portal.md#activity-reports).
* Дополнительные сведения об API отчетов см. в статье [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).


## <a name="who-can-access-the-api-data"></a>Кто может получить доступ к данным API?
* Пользователи и субъекты-службы с ролью администратора безопасности или читателя безопасности
* Глобальные администраторы
* Любое приложение, имеющее разрешение на доступ к API (авторизация приложения может быть настроена только на основании разрешения глобального администратора)

Для настройки доступа к приложению и доступа к API безопасности, таким как события входа в систему, используйте следующий PowerShell, чтобы добавить субъект-службу приложений в роль читателя безопасности.

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>предварительным требованиям
Для доступа к этому отчету с помощью API отчетов нужно:

* установить [Azure Active Directory Premium P1 или P2](active-directory-whatis.md)
* выполнить [предварительные требования для доступа к API отчетов Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Получение доступа к API
Получить доступ к API можно с помощью [песочницы Graph](https://graphexplorer2.cloudapp.net) или программным путем, используя, например, PowerShell. Чтобы программа PowerShell правильно интерпретировала синтаксис фильтров OData, используемых в вызовах REST AAD Graph, необходимо использовать обратный апостроф и отделить знак $ escape-символами. Обратный апостроф выступает в качестве [escape-символа PowerShell](https://technet.microsoft.com/library/hh847755.aspx), позволяя PowerShell выполнить точную интерпретацию знака $ и не спутать его с именем переменной PowerShell (например, $filter).

В этой статье внимание уделяется Graph Explorer. Пример PowerShell см. в этом [сценарии PowerShell](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).

## <a name="api-endpoint"></a>Конечная точка API
Доступ к этому API можно получить, используя следующий базовый URI:  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Из-за объема данных этот API имеет ограничение в 1 000 000 возвращаемых записей. 

Этот вызов возвращает данные в пакетах. В каждом пакете содержится не более 1000 записей. Чтобы получить следующий пакет записей, используйте ссылку "Следующий". Получите сведения о [маркере пропуска](https://msdn.microsoft.com/library/dd942121.aspx) из первого набора полученных записей. Маркер пропуска можно найти в конце результирующего набора.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Поддерживаемые фильтры
Можно сократить число записей, возвращаемых после вызова API, в виде фильтра.  
Для данных входа, связанных с API, поддерживаются следующие фильтры:

* **$top=\<число возвращаемых записей\>** позволяет ограничить количество возвращаемых записей. Это дорогостоящая операция. Этот фильтр не следует использовать, если нужно возвратить большое количество объектов.  
* **$filter=\<оператор фильтра\>** позволяет указать тип требуемых записей на основе полей фильтра.

## <a name="supported-filter-fields-and-operators"></a>Поддерживаемый поля и операторы фильтров
Чтобы указать тип требуемых записей, можно создать оператор фильтра, который может содержать одно или несколько из следующих полей фильтра:

* [signinDateTime](#signindatetime) определяет дату или диапазон дат;
* [userId](#userid) определяет конкретного пользователя на основе идентификатора пользователя;
* [userPrincipalName](#userprincipalname) определяет конкретного пользователя на основе имени участника-пользователя (UPN);
* [appId](#appid) определяет конкретное приложение на основе идентификатора приложения;
* [appDisplayName](#appdisplayname) определяет конкретное приложение на основе отображаемого имени приложения;
* [loginStatus](#loginStatus) определяет состояние входа (успешный вход или ошибка входа).

> [!NOTE]
> При использовании Graph Explorer каждый знак в полях фильтра нужно указывать в требуемом регистре.
> 
> 

Для сужения области возвращенных данных можно создавать сочетания поддерживаемых фильтров и полей фильтров. Например, следующий оператор возвращает первые 10 записей с 1 по 6 июля 2016 года:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**Поддерживаемые операторы**: eq, ge, le, gt, lt

**Пример**:

Использование определенной даты

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



Использование диапазона дат    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Примечания**

Значение параметра даты и времени следует указывать в формате UTC. 

- - -
### <a name="userid"></a>userId
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Примечания**

Значение userId является строковым значением

- - -
### <a name="userprincipalname"></a>userPrincipalName
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Примечания**

Значение userPrincipalName является строковым значением

- - -
### <a name="appid"></a>appId
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Примечания**

Значение appId является строковым значением

- - -
### <a name="appdisplayname"></a>appDisplayName
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Примечания**

Значение appDisplayName является строковым значением

- - -
### <a name="loginstatus"></a>loginStatus
**Поддерживаемые операторы**: eq

**Пример**:

    $filter=loginStatus+eq+'1'  


**Примечания**

Существует два варианта для параметра loginStatus: 0 — успех, 1 — сбой

- - -
## <a name="next-steps"></a>Дополнительная информация
* Хотите увидеть примеры отфильтрованных действий при входе? Просмотрите [примеры для API отчета о действиях при входе Azure Active Directory](active-directory-reporting-api-sign-in-activity-samples.md).
* Хотите узнать больше об API отчетов Azure AD? См. статью [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

