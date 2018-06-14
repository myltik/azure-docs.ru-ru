---
title: Справочник по синтаксису SQLFilter для служебной шины Azure | Документация Майкрософт
description: Сведения о грамматике SQLFilter.
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
ms.openlocfilehash: ec9d728eb31eb979e82bfb53cf619f823750e65c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29132173"
---
# <a name="sqlfilter-syntax"></a>Синтаксис SQLFilter

Объект *SqlFilter* — это экземпляр [класса SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), представляющий выражение фильтра на основе языка SQL, которое вычисляется по классу [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). SqlFilter поддерживает подмножество стандарта SQL-92.  
  
 В этом разделе приведены сведения о грамматике SqlFilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
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
  
## <a name="remarks"></a>Примечания

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

Логические константы представлены в виде ключевого слова **TRUE** или **FALSE**. Значения хранятся в виде `System.Boolean`.  
  
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
  
Рассмотрим следующую семантику [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter):  
  
-   В именах свойств не учитывается регистр.  
  
-   Во всех сценариях операторы следуют семантике неявного преобразования C#.  
  
-   Системные свойства являются общедоступными свойствами, используемыми в экземплярах [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).  
  
    Рассмотрим следующую семантику `IS [NOT] NULL`:  
  
    -   `property IS NULL` возвращает значение `true`, если свойство не существует или его значение равно `null`.  
  
### <a name="property-evaluation-semantics"></a>Семантика оценки свойств  
  
-   При попытке вычислить несуществующее системное свойство будет вызвано исключение [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception).  
  
-   Несуществующее свойство вычисляется внутри системы как **неизвестное**.  
  
 Вычисление неизвестного свойства в арифметических операторах:  
  
-   Если левая и/или правая часть операнда вычисляется как **неизвестная**, то результат **неизвестный** (для бинарных операторов).  
  
-   Если операнд вычисляется как **неизвестный**, то результат тоже **неизвестный** (для унарных операторов).  
  
 Неизвестный результат вычисления в двоичных операторах сравнения:  
  
-   Если левая и/или правая часть операнда вычисляется как **неизвестная**, то результат **неизвестный**.  
  
 Неизвестный результат вычисления в `[NOT] LIKE`:  
  
-   Если любой операнд вычисляется как **неизвестный**, то результат тоже **неизвестный**.  
  
 Неизвестный результат вычисления в `[NOT] IN`:  
  
-   Если левый операнд вычисляется как **неизвестный**, то результат тоже **неизвестный**.  
  
 Неизвестный результат вычисления в операторе **AND**:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Неизвестный результат вычисления в операторе **OR**:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Семантика привязки операторов
  
-   Операторы сравнения, такие как `>`, `>=`, `<`, `<=`, `!=`, и `=` следуют той же семантике, что и операторы C#: связываются повышения типов данных и неявные преобразования.  
  
-   Арифметические операторы, такие как `+`, `-`, `*`, `/` и `%` следуют той же семантике, что и операторы C#: связываются повышения типов данных и неявные преобразования.

## <a name="next-steps"></a>Дополнительная информация

- [Класс SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Класс SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.filters.sqlfilter)
- [SQLRuleAction class](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) (Класс SQLRuleAction)