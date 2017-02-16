---
title: "Создание расширенных правил членства в группе с помощью атрибутов в предварительной версии Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как создать расширенные правила для динамического членства в группе, используя поддерживаемые операторы и параметры выражений правила."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: da120b0ea1bfa7a0afcb6eed864c4eadbd2bbec0


---
# <a name="using-attributes-to-create-advanced-rules-for-group-membership-in-azure-active-directory-preview"></a>Создание расширенных правил членства в группе с помощью атрибутов в предварительной версии Azure Active Directory
Портал Azure предоставляет возможность создания расширенных правил для поддержки более сложного динамического членства в группах предварительной версии Azure Active Directory (Azure AD) на основе атрибутов. [Что есть в предварительной версии?](active-directory-preview-explainer.md)  В этой статье подробно описываются атрибуты правил и синтаксис для создания этих расширенных правил.

## <a name="to-create-the-advanced-rule"></a>Создание расширенного правила
1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2. Выберите **Больше служб**, введите **Пользователи и группы** в текстовое поле, а затем нажмите клавишу **ВВОД**.

   ![Открытие страницы "Управление пользователями"](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)
3. В колонке **Пользователи и группы** выберите **Все группы**.

   ![Открытие колонки группы](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)
4. В колонке **Пользователи и группы — Все группы** щелкните **Добавить**.

   ![Добавление новой группы](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)
5. В колонке **Группа** введите имя и описание новой группы. Для параметра **Тип членства** выберите значение **Динамический пользователь** или **Динамическое устройство** в зависимости от того, требуется создать правило для пользователей либо устройств, а затем щелкните **Добавить динамический запрос**. Атрибуты, используемые для правил устройств, описаны в разделе [Создание правил для объектов устройств с помощью атрибутов](#using-attributes-to-create-rules-for-device-objects).

   ![Добавление правила динамического членства](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)
6. В колонке **Правила динамического членства** введите правило в поле **Добавить расширенное правило динамического членства** и нажмите клавишу ВВОД. Затем щелкните **Создать** в нижней части колонки.
7. Щелкните **Создать** в колонке **Группа**, чтобы создать группу.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Создание текста расширенного правила
Расширенное правило, которое можно создать для динамического членства в группах, является по сути бинарным выражением из трех частей, результат которого — значение true или false. Вот эти три части:

* параметр в левой части;
* бинарный оператор;
* константа в правой части.

Полное расширенное правило выглядит примерно так: (параметр_слева бинарный_оператор "константа_справа"), где все двоичное выражение обязательно помещается между открывающей и закрывающей скобками, константа в правой части обязательно заключается в двойные кавычки, а синтаксис параметра в левой части — «пользователь.свойство». Расширенное правило может состоять из нескольких двоичных выражений, разделенных логическими операторами -and, -or, и -not.

Ниже приведены примеры правильно составленных расширенных правил:

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Полный список поддерживаемых параметров и операторов выражений правил см. в приведенных ниже разделах. Атрибуты, используемые для правил устройств, описаны в разделе [Создание правил для объектов устройств с помощью атрибутов](#using-attributes-to-create-rules-for-device-objects).

Общая длина текста расширенного правила не должна превышать 2048 символов.

> [!NOTE]
> В операциях со строками и регулярными выражениями учитывается регистр. Можно также выполнить проверку наличия значений Null, используя $null как константу, например: user.department - eq $null.
> Строки, содержащие кавычки ("), следует экранировать с помощью знака '. Например: user.department -eq \`"Sales".
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

## <a name="query-error-remediation"></a>Исправление ошибки запроса
В следующей таблице перечислены потенциальные ошибки и способы их исправления, если они встречаются

| Ошибка анализа запроса | Неправильное использование | Правильное использование |
| --- | --- | --- |
| Ошибка: атрибут не поддерживается. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>Свойство должно соответствовать одному из свойств [в списке поддерживаемых свойств](#supported-properties). |
| Ошибка: не поддерживается оператор для атрибута. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/>Свойство имеет логический тип. Используйте поддерживаемые операторы (-eq и - ne) для логического типа из списка выше. |
| Ошибка: ошибка компиляции запроса. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>Логический оператор должен соответствовать одному из свойств в приведенном выше списке поддерживаемых свойств. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error в регулярном выражении. |
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
| AccountEnabled |true, false |(user.accountEnabled -eq true) |
| dirSyncEnabled |true, false, null |(user.dirSyncEnabled -eq true) |

### <a name="properties-of-type-string"></a>Свойства строкового типа
Допустимые операторы

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch

| Свойства | Допустимые значения | Использование |
| --- | --- | --- |
| city |Любое строковое значение или $null |(user.city -eq "value") |
| country |Любое строковое значение или $null |(user.country -eq "value") |
| department |Любое строковое значение или $null |(user.department -eq "value") |
| displayName |Любое строковое значение. |(user.displayName -eq "value") |
| facsimileTelephoneNumber |Любое строковое значение или $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Любое строковое значение или $null |(user.givenName -eq "value") |
| jobTitle |Любое строковое значение или $null |(user.jobTitle -eq "value") |
| mail |Любое строковое значение или $null (SMTP-адрес пользователя) |(user.mail -eq "value") |
| mailNickName |Любое строковое значение (псевдоним электронной почты пользователя) |(user.mailNickName -eq "value") |
| mobile |Любое строковое значение или $null |(user.mobile -eq "value") |
| objectId |GUID объекта пользователя. |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
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

## <a name="extension-attributes-and-custom-attributes"></a>Атрибуты расширения и настраиваемые атрибуты
Атрибуты расширения и настраиваемые атрибуты поддерживаются в правилах динамического членства.

Атрибуты расширения синхронизируются из локального каталога Windows Server AD и принимают формат ExtensionAttributeX, где X равно 1–15.
Пример правила, которое использует атрибут расширения:

(user.extensionAttribute15 -eq "Marketing")

Настраиваемые атрибуты синхронизируются из локального каталога Windows Server AD или из подключенного приложения SaaS в формате user.extension_[GUID]\__[Attribute], где [GUID] — уникальный идентификатор в AAD для приложения, создавшего атрибут в AAD, а [Attribute] — имя атрибута, присвоенное при создании.
Пример правила, которое использует настраиваемый атрибут:

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Имя настраиваемого атрибута можно найти в каталоге, отправив запрос на атрибут пользователя с помощью обозревателя графов и выполнив поиск по имени атрибута.

## <a name="direct-reports-rule"></a>Правило Direct Reports
Теперь вы можете включать членов в группу на основе атрибута руководителя пользователя.

**Настройка группы в качестве группы «Руководитель»**

1. Выполните шаги 1–5 из раздела [Создание расширенного правила](#to-create-the-advanced-rule) и для параметра **Тип членства** выберите значение **Динамический пользователь**.
2. В колонке **Dynamic membership rules** (Правила динамического членства) введите правило, используя приведенный ниже синтаксис.

    Direct Reports for *Direct Reports for {obectID_of_manager}*. Пример допустимого правила для Direct Reports:

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    где "62e19b97-8b3d-4d4a-a106-4ce66896a863" — идентификатор объекта руководителя. Идентификатор объекта можно найти в Azure AD на **вкладке профиля** пользователя, который является руководителем.
3. После сохранения этого правила все пользователи, которые отвечают правилу, будут присоединены как члены группы. Процесс первоначального заполнения группы может занять несколько минут.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Создание правил для объектов устройств с помощью атрибутов
Можно также создать правило, которое выбирает объекты устройств для членства в группе. Можно использовать следующие атрибуты устройства:

| Свойства | Допустимые значения | Использование |
| --- | --- | --- |
| displayName |Любое строковое значение. |(device.displayName -eq "Rob Iphone”) |
| deviceOSType |Любое строковое значение. |(device.deviceOSType -eq "IOS") |
| deviceOSVersion |Любое строковое значение. |(device.OSVersion -eq "9.1") |
| isDirSynced |true, false, null |(device.isDirSynced -eq "true") |
| isManaged |true, false, null |(device.isManaged -eq "false") |
| isCompliant |true, false, null |(device.isCompliant -eq "true") |

## <a name="additional-information"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения о группах в Azure Active Directory.

* [Просмотр существующих групп](active-directory-groups-view-azure-portal.md)
* [Создание группы и добавление участников](active-directory-groups-create-azure-portal.md)
* [Управление параметрами группы](active-directory-groups-settings-azure-portal.md)
* [Управление членством в группе](active-directory-groups-membership-azure-portal.md)
* [Управление динамическими правилами для пользователей в группе](active-directory-groups-dynamic-membership-azure-portal.md)



<!--HONumber=Dec16_HO4-->


