---
title: "Динамическое заполнение групп на основе атрибутов объекта в Azure Active Directory | Документы Майкрософт"
description: "Узнайте, как создать расширенные правила для членства в группе, используя поддерживаемые операторы и параметры правил выражения."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: ae2a2e477137bc117111b147e1f088d528a55de5
ms.contentlocale: ru-ru
ms.lasthandoff: 08/30/2017

---

# <a name="populate-groups-dynamically-based-on-object-attributes"></a>Динамическое заполнение групп на основе атрибутов объекта
Классический портал Azure предоставляет возможность поддержки более сложного динамического членства в группах Azure Active Directory (Azure AD) на основе атрибутов.  

Когда изменяются любые атрибуты пользователя, система оценивает все правила динамических групп в каталоге, чтобы определить, приведет ли это изменение к добавлению или удалению в группах. Если пользователь или устройство отвечает правилу, установленному для группы, они добавляются в эту группу. Они удаляются из группы, когда перестают отвечать этому правилу.

> [!NOTE]
> - Вы можете настроить правило динамического членства для групп безопасности или групп Office 365.
>
> - Этот компонент требует наличия лицензии Azure AD Premium P1 для каждого члена, добавленного хотя бы в одну из динамических групп.
>
> - Вы можете создать динамическую группу как для устройств, так и для пользователей, но невозможно создать правило, которое включает объекты пользователей и устройств одновременно.

> - Сейчас невозможно создать группу устройств на основе атрибутов пользователей, которым принадлежат эти устройства. Правила членства для устройств могут ссылаться только на непосредственные атрибуты объектов устройств в каталоге.

## <a name="to-create-an-advanced-rule"></a>Создание расширенного правила
1. На [классическом портале Azure](https://manage.windowsazure.com)щелкните **Active Directory**и откройте каталог своей организации.
2. Перейдите на вкладку **Группы** , а затем откройте группу, которую нужно изменить.
3. Перейдите на вкладку **Настройка**, выберите параметр **Расширенное правило** и введите расширенное правило в текстовое поле.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Создание текста расширенного правила
Расширенное правило, которое можно создать для динамического членства в группах, является по сути бинарным выражением из трех частей, результат которого — значение true или false. Вот эти три части:

* параметр в левой части;
* бинарный оператор;
* константа в правой части.

Полное расширенное правило выглядит примерно так: (параметр_слева бинарный_оператор "константа_справа"), где все двоичное выражение обязательно помещается между открывающей и закрывающей скобками, константа в правой части обязательно заключается в двойные кавычки, а синтаксис параметра в левой части — «пользователь.свойство». Расширенное правило может состоять из нескольких двоичных выражений, разделенных логическими операторами -and, -or, и -not.
Ниже приведены примеры правильно составленных расширенных правил:

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Полный список поддерживаемых параметров и операторов выражений правил см. в приведенных ниже разделах.


Обратите внимание, что для свойства в качестве префикса должен быть указан правильный тип объекта: пользователь или устройство.
Следующее правило не пройдет проверку: mail –ne null.

Правильное правило будет выглядеть так:

user.mail –ne null.

Общая длина текста расширенного правила не должна превышать 2048 символов.

> [!NOTE]
> В операциях со строками и регулярными выражениями учитывается регистр.
> Строки, содержащие кавычки ("), следует экранировать с помощью знака '. Например: user.department -eq \`"Sales".
> Используйте кавычки (только английская раскладка) только для строковых типов значений.
>
>

## <a name="supported-expression-rule-operators"></a>Поддерживаемые операторы выражений правил
В следующей таблице перечислены все поддерживаемые операторы выражений правил и их синтаксис, используемый в тексте расширенного правила:

| Оператор | Синтаксис |
| --- | --- |
| Не равно |-ne |
| Равно |-eq |
| Не начинается с |-notStartsWith |
| Начинается с |-startsWith |
| Не содержит |-notContains |
| Содержит |-contains |
| Не соответствует |-notMatch |
| Соответствует |-match |
| В | -in |
| Не входит | -notIn |

## <a name="operator-precedence"></a>Приоритет операторов

Все операторы перечислены ниже в порядке повышения приоритета. Операторы в одной строке имеют равный приоритет: -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn.

Все операторы можно использовать с дефисом в качестве префикса и без него.

Обратите внимание, что скобки не всегда нужны. Добавляйте их, только если порядок приоритета не удовлетворяет требованиям. Например, правило

   user.department –eq "Marketing" –and user.country –eq "US"

эквивалентно правилу

   (user.department –eq "Marketing") –and (user.country –eq "US").

## <a name="using-the--in-and--notin-operators"></a>Использование операторов -In и -notIn

Чтобы сравнить значение атрибута пользователя с рядом различных значений, можно использовать оператор -In или -notIn. Ниже приведен пример использования оператора -In.

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

Обратите внимание на символы "[" и "]" в начале и конце списка значений. Это условие принимает значение True, если значение user.department равно одному из значений в списке.

## <a name="query-error-remediation"></a>Исправление ошибки запроса
В следующей таблице перечислены потенциальные ошибки и способы их исправления, если они встречаются

| Ошибка анализа запроса | Неправильное использование | Правильное использование |
| --- | --- | --- |
| Ошибка: атрибут не поддерживается. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>Свойство должно соответствовать одному из свойств [в списке поддерживаемых свойств](#supported-properties). |
| Ошибка: не поддерживается оператор для атрибута. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/>Свойство имеет логический тип. Используйте поддерживаемые операторы (-eq и - ne) для логического типа из списка выше. |
| Ошибка: ошибка компиляции запроса. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>Логический оператор должен соответствовать одному из свойств в приведенном выше списке поддерживаемых свойств. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$") — ошибка в регулярном выражении. |
| Ошибка: неправильный формат двоичного выражения. |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>Запрос содержит несколько ошибок. Скобки не в нужном месте. |
| Ошибка: неизвестная ошибка при настройке динамического членства. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>Запрос содержит несколько ошибок. Скобки не в нужном месте. |

## <a name="supported-properties"></a>Поддерживаемые свойства
Ниже приведены все свойства пользователя, которые можно использовать в расширенном правиле.

### <a name="properties-of-type-boolean"></a>Свойства логического типа
Допустимые операторы

* -eq
* -ne

| Свойства | Допустимые значения | Использование |
| --- | --- | --- |
| AccountEnabled |true, false |user.accountEnabled -eq true |
| dirSyncEnabled |true, false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Свойства строкового типа
Допустимые операторы

* -eq
* -ne
* -notStartsWith
* -StartsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| Свойства | Допустимые значения | Использование |
| --- | --- | --- |
| city |Любое строковое значение или $null |(user.city -eq "value") |
| country |Любое строковое значение или $null |(user.country -eq "value") |
| companyName | Любое строковое значение или $null | (user.companyName -eq "value") |
| department |Любое строковое значение или $null |(user.department -eq "value") |
| displayName |Любое строковое значение. |(user.displayName -eq "value") |
| facsimileTelephoneNumber |Любое строковое значение или $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Любое строковое значение или $null |(user.givenName -eq "value") |
| jobTitle |Любое строковое значение или $null |(user.jobTitle -eq "value") |
| mail |Любое строковое значение или $null (SMTP-адрес пользователя) |(user.mail -eq "value") |
| mailNickName |Любое строковое значение (псевдоним электронной почты пользователя) |(user.mailNickName -eq "value") |
| mobile |Любое строковое значение или $null |(user.mobile -eq "value") |
| objectId |GUID объекта пользователя. |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier; | Локальный идентификатор безопасности (SID) для пользователей, которые были синхронизированы из локальной среды в облако. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None, DisableStrongPassword, DisablePasswordExpiration, DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Любое строковое значение или $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Любое строковое значение или $null |(user.postalCode -eq "value") |
| preferredLanguage |Код ISO 639-1. |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Любое строковое значение или $null |(user.sipProxyAddress -eq "value") |
| state |Любое строковое значение или $null |(user.state -eq "value") |
| streetAddress |Любое строковое значение или $null |(user.streetAddress -eq "value") |
| surname |Любое строковое значение или $null |(user.surname -eq "value") |
| TelephoneNumber |Любое строковое значение или $null |(user.telephoneNumber -eq "value") |
| usageLocation |Двухбуквенный код страны. |(user.usageLocation -eq "US") |
| userPrincipalName |Любое строковое значение. |(user.userPrincipalName -eq "alias@domain") |
| userType |member, guest, $null |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Свойства типа коллекции строк
Допустимые операторы

* -contains
* -notContains

| Свойства | Допустимые значения | Использование |
| --- | --- | --- |
| otherMails |Любое строковое значение. |(user.otherMails -contains "alias@domain") |

| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Многозначные свойства
Допустимые операторы

* -any (выполняется, когда условию соответствует хотя бы один элемент в коллекции)
* -all (выполняется, когда условию соответствуют все элементы в коллекции)

| Свойства | Значения | Использование |
| --- | --- | --- |
| assignedPlans |Каждый объект в коллекции предоставляет следующие строковые параметры: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

Многозначные свойства являются коллекциями объектов того же типа. Вы можете использовать операторы -any и -all для применения условия к одному или ко всем элементам в коллекции соответственно. Например:

assignedPlans — это многозначное свойство, которое перечисляет все сервисные планы, назначенные пользователю. Представленное ниже выражение выбирает пользователей, которым назначен план обслуживания Exchange Online (План 2) и для которых этот план находится в состоянии Enabled:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Идентификатор Guid обозначает план обслуживания Exchange Online (План 2)).

> [!NOTE]
> Это полезно, если вы хотите найти всех пользователей, которым разрешено использовать Office 365 (или другую службу Microsoft Online), например, чтобы применить к ним некоторый набор политик.

Следующее выражение выбирает всех пользователей, которым назначен любой план обслуживания, связанный со службой Intune (это определяется по имени службы "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Использование значений Null

Чтобы указать значение Null в правиле, можно использовать null или $null. Пример:

   правило user.mail –ne null эквивалентно правилу user.mail –ne $null.

## <a name="extension-attributes-and-custom-attributes"></a>Атрибуты расширения и настраиваемые атрибуты
Атрибуты расширения и настраиваемые атрибуты поддерживаются в правилах динамического членства.

Атрибуты расширения синхронизируются из локального каталога Windows Server AD и принимают формат ExtensionAttributeX, где X равно 1–15.
Пример правила, которое использует атрибут расширения:

(user.extensionAttribute15 -eq "Marketing")

Настраиваемые атрибуты синхронизируются из локального каталога Windows Server AD или из подключенного приложения SaaS в формате user.extension_[GUID]\__[Attribute], где [GUID] — уникальный идентификатор в AAD для приложения, создавшего атрибут в AAD, а [Attribute] — имя атрибута, присвоенное при создании.
Пример правила, которое использует настраиваемый атрибут:

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Имя настраиваемого атрибута можно найти в каталоге, отправив запрос на атрибут пользователя с помощью обозревателя графов и выполнив поиск по имени атрибута. Использование нескольких значений атрибутов, синхронизированных из локального экземпляра Active Directory, сейчас не поддерживается. 

## <a name="direct-reports-rule"></a>Правило "Непосредственные подчиненные"
Вы можете создать группу, в которую войдут все непосредственные подчиненные определенного руководителя. Если в будущем состав подчиненных этого руководителя изменится, членство в группе будет скорректировано автоматически.

> [!NOTE]
> 1. Чтобы такое правило работало, следите за правильностью заполнения свойства **Manager ID** (Идентификатор руководителя) для всех пользователей в арендаторе. Текущее значение свойства можно проверить на **вкладке профиля** для каждого пользователя.
> 2. Это правило учитывает только **непосредственных** подчиненных. Сейчас невозможно создать группу с учетом вложенной иерархии, в которую будут входить не только непосредственные подчиненные, но и их подчиненные.

**Настройка группы**

1. Выполните шаги 1–5 из раздела [Создание расширенного правила](#to-create-the-advanced-rule) и для параметра **Тип членства** выберите значение **Динамический пользователь**.
2. В колонке **Dynamic membership rules** (Правила динамического членства) введите правило, используя приведенный ниже синтаксис.

    *Direct Reports for "{идентификатор_объекта_руководителя}"*

    Пример допустимого правила:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Когда вы сохраните это правило, в группу будут добавлены все пользователи с выбранным значением идентификатора руководителя.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Создание правил для объектов устройств с помощью атрибутов
Можно также создать правило, которое выбирает объекты устройств для членства в группе. Можно использовать следующие атрибуты устройства:

| Свойства              | Допустимые значения                  | Использование                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| AccountEnabled          | true, false                      | (device.accountEnabled -eq true)                            |
| displayName             | Любое строковое значение                | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | Любое строковое значение                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | Любое строковое значение                | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | Любое строковое значение.                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | Любое строковое значение.                | (device.deviceManufacturer -eq "Microsoft")                 |
| deviceModel             | Любое строковое значение.                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | Любое строковое значение.                | (device.deviceOwnership -eq "")                             |
| domainName              | Любое строковое значение.                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Любое строковое значение.                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true, false                      | (device.deviceOSType -eq true)                              |
| managementType          | Любое строковое значение.                | (device.managementType -eq "")                              |
| organizationalUnit      | Любое строковое значение.                | (device.organizationalUnit -eq "")                          |
| deviceId                | a valid deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | Допустимый идентификатор objectId в AAD            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> Эти правила устройств невозможно создать с помощью раскрывающегося списка "Простое правило" на классическом портале Azure.
>
>

## <a name="next-steps"></a>Дальнейшие действия
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Устранение неполадок, связанных с динамическим членством в группах](active-directory-accessmanagement-troubleshooting.md)
* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Azure Active Directory cmdlets for configuring group settings](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

