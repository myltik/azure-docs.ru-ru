---
title: Динамическое маскирование данных базы данных SQL Azure | Документация Майкрософт
description: Динамическое маскирование данных базы данных SQL ограничивает возможность раскрытия конфиденциальных данных, маскируя их для обычных пользователей.
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: ronitr
ms.openlocfilehash: 4d948786d70ffc1350825b04dbddc128cf89466e
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="sql-database-dynamic-data-masking"></a>Динамическое маскирование данных базы данных SQL Azure

Динамическое маскирование данных базы данных SQL Azure ограничивает возможность раскрытия конфиденциальных данных, маскируя их для обычных пользователей. 

Динамическая маскировка данных помогает предотвратить несанкционированный доступ к конфиденциальным данным, позволяя заказчикам самим определять, сколько конфиденциальных данных следует отображать, при минимальном влиянии на уровень приложения. Эта функция работает на основе политики безопасности, скрывая конфиденциальные данные в результирующем наборе запроса по заданным полям базы данных, не меняя сами данные в базе данных.

Например, представитель отдела обслуживания клиентов в центре обработки вызовов может идентифицировать абонентов по нескольким цифрам номера кредитной карты, но эти элементы данных не должны предоставляться ему полностью. Правило маскирования может быть задано таким образом, чтобы в результатах выполнения запросов маскировалось все, кроме последних четырех цифр номера кредитной карты. Еще один пример — защита персональных данных с помощью соответствующей маски. Разработчик может отправлять запросы в рабочие среды для устранения неполадок, не нарушая при этом нормативные правила.

## <a name="sql-database-dynamic-data-masking-basics"></a>Основы динамического маскирования данных базы данных SQL
Настройка политики динамического маскирования данных на портале Azure осуществляется посредством выбора операции "Динамическое маскирование данных" в колонке конфигурации или параметров базы данных SQL.

### <a name="dynamic-data-masking-permissions"></a>Разрешения для динамической маскировки данных
Динамическую маскировку данных может настраивать администратор базы данных Azure, администратор сервера или сотрудник службы безопасности.

### <a name="dynamic-data-masking-policy"></a>Политика динамической маскировки данных
* **Пользователи SQL, исключенные из маскирования** — ряд пользователей SQL или удостоверений AAD, при использовании которых с данных, полученных в результатах SQL-запросов, снимается маска. Пользователи с привилегия администратора всегда исключаются из правил маскирования и видят исходные данные без какой-либо маски.
* **Правила маскирования** — набор правил, определяющих поля, подлежащие маскированию, и используемую функцию маскирования. Задать такие поля можно с помощью имени схемы базы данных, имени таблицы и имени столбца.
* **Функции маскирования** — набор методов, управляющих доступностью данных для различных сценариев.

| Функция маскирования | Логика маскирования |
| --- | --- |
| **По умолчанию** |**Полное маскирование в соответствии с типами данных предопределенных полей**<br/><br/>• Для строковых типов данных (nchar, ntext, nvarchar) используется XXXX или меньшее количество символов "X", если размер поля меньше 4 символов.<br/>• Для числовых типов данных (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real) используется нулевое значение.<br/>• Для типов данных даты и времени (date, datetime2, datetime, datetimeoffset, smalldatetime, time) используется формат 01-01-1900.<br/>• Для варианта SQL используется значение по умолчанию текущего типа.<br/>• Для XML-документа используется <masked/>.<br/>.• Используйте пустое значение для специальных типов данных (таблица меток времени, hierarchyid, GUID, binary, image, пространственные типы varbinary). |
| **Кредитная карта** |**Метод маскирования, который предоставляет последние четыре цифры назначенных полей** и добавляет строковую константу в качестве префикса в формате номера кредитной карты.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Электронная почта** |**Метод маскирования, который предоставляет первую букву и домен**, используя строковую константу в качестве префикса в формате электронного адреса.<br/><br/>aXX@XXXX.com |
| **Случайное число** |**Метод маскирования, который формирует случайное число** в соответствии с выбранными границами и типами фактических данных. Если установленные границы равны, то функция маскирования будет постоянным числом.<br/><br/>![Область навигации](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Пользовательский текст** |**Метод маскирования, при котором видны первая и последняя буквы** и добавляется пользовательская строка заполнения в середине. Если исходная строка короче открытых префикса и суффикса, то будет использоваться только строка заполнения. <br/>префикс[заполнение]суффикс<br/><br/>![Область навигации](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Рекомендуемые поля для маскирования
Система рекомендаций DDM помечает в базе данных поля, которые могут содержать конфиденциальные данные и являются хорошими кандидатами на применение маскирования. Список рекомендуемых для маскирования столбцов в вашей базе данных см. на портале в колонке «Динамическое маскирование данных». Вам нужно только щелкнуть **Добавить маску** для одного или нескольких столбцов, а затем щелкнуть **Сохранить**, чтобы применить маски к выбранным полям.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Настройка динамического маскирования данных для базы данных с помощью командлетов Powershell
См. статью с описанием [командлетов базы данных SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Настройка динамической маскировки данных для базы данных с помощью REST API
См. статью с описанием [операций для баз данных SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).

