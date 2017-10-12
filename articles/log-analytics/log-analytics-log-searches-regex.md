---
title: "Регулярные выражения при поиске по журналам Log Analytics OMS | Документация Майкрософт"
description: "Для фильтрации результатов в соответствии с регулярным выражением вы можете использовать ключевое слово RegEx при поиске по журналам Log Analytics.  В этой статье содержатся сведения о синтаксисе для этих выражений с несколькими примерами."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.openlocfilehash: 9746170f157ed5065adc953a31687ff18bd73708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Фильтрование поиска по журналам в Log Analytics с помощью регулярных выражений

[Поиск по журналам](log-analytics-log-searches.md) позволяет извлекать сведения из репозитория Log Analytics.  [Выражения фильтра](log-analytics-search-reference.md#filter-expressions) позволяют фильтровать результаты поиска в соответствии с определенными критериями.  Ключевое слово **RegEx** позволяет указать регулярное выражение для фильтра.  

Эта статья содержит сведения о синтаксисе регулярных выражений, используемых службой Log Analytics.

> [!NOTE]
> Регулярное выражение можно использовать только в полях с поддержкой поиска.  Дополнительные сведения о полях, поддерживающих поиск, см. в разделе **Типы полей** статьи [Поиск данных по журналам в Log Analytics](log-analytics-log-searches.md#use-additional-filters).


## <a name="regex-keyword"></a>Ключевое слово RegEx

Чтобы использовать ключевое слово **RegEx** при поиске по журналам, воспользуйтесь следующим синтаксисом.  Вам также могут понадобиться сведения из других разделов этой статьи, чтобы определить сам синтаксис регулярного выражения.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Например, для возврата записей оповещений с типом *Warning* или *Error* с применением регулярного выражения используется следующий запрос поиска по журналам.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Частичные совпадения
Обратите внимание, что регулярное выражение должно соответствовать всему тексту свойства.  При частичных совпадениях ни одна запись не будет возвращена.  Например, если вы пытались возвратить записи на компьютере с именем srv01.contoso.com, следующий поиск по журналам **не** возвратит никаких записей.

    Computer=RegEx("srv..")

Это происходит, потому что только первая часть имени соответствует регулярному выражению.  Следующие два поиска по журналам возвратят записи на этом компьютере, так как эта записи соответствуют полному имени.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Знаки
Укажите различные знаки.

| Character | Описание | Пример | Пример совпадений |
|:--|:--|:--|:--|
| a | Одно вхождение знака. | Computer=RegEx("srv01.contoso.com") | Srv01.contoso.com |
| . | Любой отдельный знак. | Computer=RegEx("srv...contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| a? | Одно вхождение знака или ни одного. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>Srv01.contoso.com |
| a* | Несколько вхождений знака или ни одного. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>Srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | Одно вхождение знака или несколько. | Computer=RegEx("srv01+.contoso.com") | Srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Соответствует любому отдельному знаку в квадратных скобках | Computer=RegEx("srv0[123].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | Соответствует одному знаку в диапазоне.  Может содержать несколько диапазонов. | Computer=RegEx("srv0[1-3].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Нет подходящих символов в квадратных скобках. | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | Нет подходящих знаков в диапазоне. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Соответствует диапазону числовых знаков. | Computer=RegEx("srv[01-03].contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |
| @ | Любая строка знаков. | Computer=RegEx("srv@.contoso.com") | Srv01.contoso.com<br>SRV02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Несколько вхождений знака
Укажите несколько вхождений определенных знаков.

| Character | Описание | Пример | Пример совпадений |
|:--|:--|:--|:--|
| a{n} |  *n* вхождений знака. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  *n* или больше вхождений знака. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  Вхождений знака от *n* до *m*. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Логические выражения
Выберите из нескольких значений.

| Character | Описание | Пример | Пример совпадений |
|:--|:--|:--|:--|
| &#124; | Логический оператор OR.  Возвращает результат, если есть совпадение в любом из выражений. | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | Предупреждение<br>Ошибка |
| & | Логический оператор AND.  Возвращает результат, если есть совпадение в обоих выражениях. | EventData=regex("(Security.\*&.\*success.\*)") | Аудит системы безопасности успешно выполнен |


## <a name="literals"></a>Литералы
Преобразуйте специальные знаки в литеральные.  Сюда входят знаки, предоставляющие возможности для регулярных выражений: ?, -, \*, ^, \[\], {}, \(\), +, \|, ., &.

| Character | Описание | Пример | Пример совпадений |
|:--|:--|:--|:--|
| \\ | Преобразовывает специальный знак в литерал. | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Error] Файл не найден.<br>Error. Файл не найден. |


## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с [поиском по журналу](log-analytics-log-searches.md), чтобы просматривать и анализировать данные в репозитории Log Analytics.
