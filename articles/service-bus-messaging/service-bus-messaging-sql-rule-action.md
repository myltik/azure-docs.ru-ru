---
title: Справочник по синтаксису SQLRuleAction в Azure | Документация Майкрософт
description: Сведения о грамматике SQLRuleAction.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: 9ac9a2968adfdd8e1fb229ad744bc99914cdcd08
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29125825"
---
# <a name="sqlruleaction-syntax"></a>Синтаксис SQLRuleAction

*SqlRuleAction* — это экземпляр класса [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction). Он представляет набор действий, написанных по правилам синтаксиса на основе языка SQL, выполняемых с [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
В этой статье приведены сведения о грамматике действия правила SQL.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Аргументы  
  
-   `<scope>` — необязательная строка, указывающая область `<property_name>`. Допустимые значения: `sys` и `user`. Значение `sys` указывает область системы, где `<property_name>` — имя общедоступного свойства [класса BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Значение `user` указывает область пользователя, где `<property_name>` — ключ словаря [класса BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Область `user` используется по умолчанию, если аргумент `<scope>` не указан.  
  
### <a name="remarks"></a>Примечания  

Попытка доступа к несуществующему системному свойству вызывает ошибку, а попытка доступа к несуществующему свойству пользователя — нет. Вместо этого несуществующее свойство пользователя вычисляется внутри системы как неизвестное значение. Неизвестное значение обрабатывается особым образом во время вычисления оператора.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Аргументы  
 `<regular_identifier>` — строка, представленная следующим регулярным выражением:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Это любая строка, которая начинается с буквы, после которой идет один или несколько символов подчеркивания, букв или цифр.  
  
 `[:IsLetter:]` — любой символ Юникода, который относится к категории букв Юникода. `System.Char.IsLetter(c)` возвращает значение `true`, если `c` является буквой Юникода.  
  
 `[:IsDigit:]` — любой символ Юникода, который относится к категории десятичных цифр. `System.Char.IsDigit(c)` возвращает значение `true`, если `c` является цифрой Юникода.  
  
 `<regular_identifier>` не может быть зарезервированным ключевым словом.  
  
 `<delimited_identifier>` — любая строка, заключенная в квадратные скобки ([]). Закрывающая квадратная скобка представляется в виде двух закрывающих квадратных скобок. Ниже приведены примеры `<delimited_identifier>`.  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` — любая строка, заключенная в двойные кавычки. Двойные кавычки в идентификаторе представляются в виде двух пар двойных кавычек. Мы не рекомендуем использовать заключенные в кавычки идентификаторы, так как их можно легко спутать со строковой константой. По возможности используйте идентификаторы с разделителем. Ниже приведен пример `<quoted_identifier>`.  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Примечания
  
 Свойство `<pattern>` должно быть выражением, которое будет вычисляться как строка. Оно используется в качестве шаблона для оператора LIKE.      Оно может содержать следующие подстановочные знаки:  
  
-   `%` — любая строка без символов или с несколькими символами.  
  
-   `_` — любой один символ.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Примечания
  
 Свойство `<escape_char>` должно быть выражением, которое будет вычисляться в качестве строки с 1 символом. Оно используется в качестве escape-символа для оператора LIKE.  
  
 Например, `property LIKE 'ABC\%' ESCAPE '\'` соответствует `ABC%`, а не строке, начинающейся с `ABC`.  
  
## <a name="constant"></a>константа  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Аргументы  
  
-   `<integer_constant>` — строка чисел без кавычек и десятичного разделителя. Значения хранятся в виде `System.Int64` внутри системы и попадают в тот же диапазон.  
  
     Ниже приведены примеры длинных констант.  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` — строка чисел без кавычек с десятичным разделителем. Значения хранятся в виде `System.Double` внутри системы и попадают в тот же диапазон и точность.  
  
     В следующих версиях это число может храниться в другом типе данных для обеспечения поддержки более точной числовой семантики. Поэтому не следует полагаться на тот факт, что базовый тип данных для `<decimal_constant>` — `System.Double`.  
  
     Ниже приведены примеры десятичных констант.  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` — число, записанное в экспоненциальном представлении чисел. Значения хранятся в виде `System.Double` внутри системы и попадают в тот же диапазон и точность. Ниже приведены примеры констант с приближенными числами.  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Примечания
  
Логические константы представлены в виде ключевого слова `TRUE` или `FALSE`. Значения хранятся в виде `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Примечания
  
Строковые константы заключаются в одинарные кавычки и включают любые допустимые символы Юникода. Одинарная кавычка, внедренная в строковую константу, представляется в виде двух одинарных кавычек.  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Примечания  

Функция `newid()` возвращает значение **System.Guid**, созданное методом `System.Guid.NewGuid()`.  
  
Функция `property(name)` возвращает значение свойства, на которое указывает `name`. В качестве значения `name` может использоваться любое допустимое выражение, возвращающее строковое значение.  
  
## <a name="considerations"></a>Рекомендации

- Оператор SET используется для создания свойства или обновления значения имеющегося свойства.
- Оператор REMOVE используется для удаления свойства.
- По возможности оператор SET выполняет неявное преобразование, если тип выражения и тип имеющегося свойства отличаются.
- Если ссылка указывает на несуществующие свойства системы, действие завершается сбоем.
- Если ссылка указывает на несуществующие свойства пользователя, действие не завершается сбоем.
- Несуществующее свойство пользователя вычисляется внутри системы как неизвестное значение с использованием той же семантики, что и [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) при вычислении операторов.

## <a name="next-steps"></a>Дополнительная информация

- [SQLRuleAction class](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) (Класс SQLRuleAction)
- [SQLFilter class](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) (Класс SQLFilter)
