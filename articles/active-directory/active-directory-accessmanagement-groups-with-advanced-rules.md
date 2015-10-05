
<properties
	pageTitle="Использование атрибутов для создания расширенных правил | Microsoft Azure"
	description="Указания по созданию дополнительных правил для группы и использованием поддерживаемых операторов и параметров выражений правила."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="femila"/>


# Использование атрибутов для создания расширенных правил
Портал управления Azure предоставляет гибкие возможности настройки более сложных правил для включения динамического членства в группах.

**Чтобы создать дополнительное правило,** на портале управления на вкладке **Настройка** группы выберите переключатель **Расширенное правило**, а затем введите расширенное правило в текстовом поле. Можно создать расширенное правило с помощью следующей информации.

## Создание текста расширенного правила
Расширенное правило, которое можно создать для динамического членства в группах, является по сути бинарным выражением из трех частей, результат которого — значение true или false. Вот эти три части:

- параметр в левой части;
- бинарный оператор;
- константа в правой части. 

Полное расширенное правила выглядит примерно так: (параметр\_слева бинарный\_оператор "константа\_справа"), где все двоичное выражение обязательно помещается между открывающей и закрывающей скобками, константа в правой части обязательно заключается в двойные кавычки, а синтаксис параметра в левой части — «пользователь.свойство». Расширенное правило может состоять из нескольких двоичных выражений, разделенных логическими операторами -and, -or, и -not. Ниже приведены примеры правильно составленных расширенных правил:

- (user.department -eq "Sales") -or (user.department -eq "Marketing") 
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE") 

Полный список поддерживаемых параметров и операторов выражений правил см. в приведенных ниже разделах.

Общая длина текста расширенного правила не может превышать 255 знаков.
> [AZURE.NOTE]В операциях со строками и регулярными выражениями учитывается регистр. Можно также выполнить проверку наличия значений Null, используя $null как константу, например: user.department - eq $null. Строки, содержащие кавычки ", следует экранировать с помощью знака '. Пример: user.department -eq "Sa`"les".

##Поддерживаемые операторы выражений правил
В следующей таблице перечислены все поддерживаемые операторы выражений правил и их синтаксис, используемый в тексте расширенного правила:

| Оператор | Синтаксис |
|-----------------|----------------|
| Не равно | -ne |
| Равно | -eq |
| Не начинается с | -notStartsWith |
| Начинается с | -startsWith |
| Не содержит | -notContains |
| Содержит | -contains |
| Не соответствует | -notMatch |
| Соответствует | -match |


| Ошибка анализа запроса | Неправильное использование | Правильное использование |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ошибка: атрибут не поддерживается. | (user.invalidProperty -eq "Value") | (user.department -eq "value") — свойство должно соответствовать одному из свойств в приведенном выше списке поддерживаемых свойств. |
| Ошибка: не поддерживается оператор для атрибута. | (user.accountEnabled -contains true) | (user.accountEnabled - eq true) — свойство имеет логический тип. Используйте поддерживаемые операторы (-eq и - ne) для логического типа из списка выше. |
| Ошибка: ошибка компиляции запроса. | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing") — логический оператор должен соответствовать одному из приведенного выше списка поддерживаемых свойств. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$") — ошибка в регулярном выражении. |
| Ошибка: неправильный формат двоичного выражения. | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") — запрос содержит несколько ошибок. Скобки не в нужном месте. |
| Ошибка: неизвестная ошибка при настройке динамического членства. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain") — запрос содержит несколько ошибок. Скобки не в нужном месте. |

##Поддерживаемые параметры
Ниже приведены все свойства пользователя, которые можно использовать в расширенном правиле.

**Свойства логического типа**

Допустимые операторы

* -eq


* -ne


| Свойства | Допустимые значения | Использование |
|----------------|-----------------|--------------------------------|
| AccountEnabled | true, false | (user.accountEnabled -eq true) |
| dirSyncEnabled | true, false, null | (user.dirSyncEnabled -eq true) |

**Свойства строкового типа**

Допустимые операторы

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Свойства | Допустимые значения | Использование |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Любое строковое значение или $null. | (user.city -eq "value") |
| country | Любое строковое значение или $null. | (user.country -eq "value") |
| department | Любое строковое значение или $null. | (user.department -eq "value") |
| displayName | Любое строковое значение. | (user.displayName -eq "value") |
| facsimileTelephoneNumber | Любое строковое значение или $null. | (user.facsimileTelephoneNumber -eq "value") |
| givenName | Любое строковое значение или $null. | (user.givenName -eq "value") |
| jobTitle | Любое строковое значение или $null. | (user.jobTitle -eq "value") |
| mail | Любое строковое значение или $null. SMTP-адрес пользователя. | (user.mail -eq "value") |
| mailNickName | Любое строковое значение. Псевдоним электронной почты пользователя. | (user.mailNickName -eq "value") |
| mobile | Любое строковое значение или $null. | (user.mobile -eq "value") |
| objectId | GUID объекта пользователя. | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | None, DisableStrongPassword, DisablePasswordExpiration, DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Любое строковое значение или $null. | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | Любое строковое значение или $null. | (user.postalCode -eq "value") |
| preferredLanguage | Код ISO 639-1. | (user.preferredLanguage -eq "ru-ru") |
| sipProxyAddress | Любое строковое значение или $null. | (user.sipProxyAddress -eq "value") |
| state | Любое строковое значение или $null. | (user.state -eq "value") |
| streetAddress | Любое строковое значение или $null. | (user.streetAddress -eq "value") |
| surname | Любое строковое значение или $null. | (user.surname -eq "value") |
| TelephoneNumber | Любое строковое значение или $null. | (user.telephoneNumber -eq "value") |
| usageLocation | Двухбуквенный код страны. | (user.usageLocation -eq "US") |
| userPrincipalName | Любое строковое значение. | (user.userPrincipalName -eq "alias@domain") |
| userType | member, guest, $null | (user.userType -eq "Member") |

**Свойства типа коллекции строк**

Допустимые операторы

* -contains


* -notContains

| Свойства | Допустимые значения | Использование |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Любое строковое значение. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain, smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Правило Direct Reports
Теперь вы можете включать членов в группу на основе атрибута руководителя пользователя.
Настройка группы в качестве группы «Руководитель»
--------------------------------------------------------------------------------
1. На портале администратора перейдите на вкладку **Настройка**, а затем выберите **РАСШИРЕННОЕ ПРАВИЛО**. 
2. Введите правило, используя следующий синтаксис: *Direct Reports for {идентификатор\_руководителя}*.
3. После сохранения этого правила все пользователи, которые отвечают правилу, будут присоединены как члены группы. Обратите внимание, процесс первоначального заполнения группы может занять несколько минут. 


## Дополнительная информация
Ниже приведены некоторые разделы, содержащие дополнительные сведения в отношении Azure Active Directory.

* [Устранение неполадок, связанных с динамическим членством в группах](active-directory-accessmanagement-troubleshooting.md)

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)

* [Что такое Microsoft Azure Active Directory](active-directory-whatis.md)

* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Sept15_HO4-->