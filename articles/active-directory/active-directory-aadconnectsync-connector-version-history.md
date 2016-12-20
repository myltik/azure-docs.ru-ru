---
title: "История выпусков версий соединителей | Документация Майкрософт"
description: "В этой статье перечислены все соединители для Forefront Identity Manager (FIM) и Microsoft Identity Manager (MIM)."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 58c89833330d8dbb1147b42c086ca2c86be3e94d


---
# <a name="connector-version-release-history"></a>История выпусков версий соединителей
Соединители для Forefront Identity Manager (FIM) и Microsoft Identity Manager (MIM) часто обновляются.

> [!NOTE]
> Эта статья касается только FIM и MIM. Эти соединители не поддерживаются в Azure AD Connect.
> 
> 

В этой статье перечисляются все выпущенные соединители.

Связанные ссылки

* [Скачивание последних соединителей.](http://go.microsoft.com/fwlink/?LinkId=717495)
* [универсальному соединителю LDAP](active-directory-aadconnectsync-connector-genericldap.md) .
* [универсальному соединителю SQL](active-directory-aadconnectsync-connector-genericsql.md) .
* [соединителю веб-служб](http://go.microsoft.com/fwlink/?LinkID=226245) .
* [соединителю PowerShell](active-directory-aadconnectsync-connector-powershell.md) .
* [соединителю Lotus Domino](active-directory-aadconnectsync-connector-domino.md) .

## <a name="111170"></a>1.1.117.0
Дата выпуска: март 2016 г.

**Новый соединитель**  
Первоначальный выпуск [универсальному соединителю SQL](active-directory-aadconnectsync-connector-genericsql.md).

**Новые функции:**

* Универсальный соединитель LDAP:
  * Добавлена поддержка импорта изменений с помощью Isode.
* Соединитель веб-служб:
  * Обновлены действия csEntryChangeResult и setImportErrorCode, чтобы устранить ошибки уровня объекта, возвращаемые в модуль синхронизации.
  * Обновлены шаблоны SAP6 и SAP6User для использования новых функций обработки ошибок уровня объекта.
* Соединитель Lotus Domino:
  * Для экспорта требуется один издатель сертификатов на адресную книгу. Теперь можно использовать один и тот же пароль для всех издателей сертификатов, чтобы облегчить управление.

**Исправленные проблемы:**

* Универсальный соединитель LDAP:
  * IBM Tivoli DS: некоторые ссылочные атрибуты не обнаруживались должным образом.
  * Open LDAP: при импорте изменений усекались пробелы в начале и конце строки.
  * Novell и NetIQ: экспорт, при котором объект перемещался между подразделениями или контейнерами и в то же время переименовывался, приводил к сбою.
* Соединитель веб-служб:
  * Если у веб-службы было несколько конечных точек для одной привязки, то соединитель не мог правильно обнаружить эти конечные точки.
* Соединитель Lotus Domino:
  * Не работал экспорт атрибута fullName в базу данных обслуживания почты.
  * При экспорте с добавлением и удалением участника группы выполнялось только экспортирование добавленных участников.
  * Если документ Notes являлся недопустимым (атрибуту isValid задано значение false), происходил сбой соединителя.

## <a name="older-releases"></a>Более старые выпуски
До марта 2016 г. соединители выпускались в виде разделов службы поддержки.

**Универсальный LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) — 1.0.0597, сентябрь 2015 г.
* [KB3044896](https://support.microsoft.com/kb/3044896) — 1.0.0549, март 2015 г.
* [KB3031009](https://support.microsoft.com/kb/3031009) — 1.0.0534, январь 2015 г.
* [KB3008177](https://support.microsoft.com/kb/3008177) — 1.0.0419, сентябрь 2014 г.
* [KB2936070](https://support.microsoft.com/kb/2936070) — 4.3.1082, март 2014 г.

**Веб-службы**

* [KB3008178](https://support.microsoft.com/kb/3008178) — 1.0.0419, сентябрь 2014 г.

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) — 1.0.0419, сентябрь 2014 г.

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) — 1.0.0597, сентябрь 2015 г.
* [KB3044895](https://support.microsoft.com/kb/3044895) — 1.0.0549, март 2015 г.
* [KB2977286](https://support.microsoft.com/kb/2977286) — 5.3.0712, август 2014 г.
* [KB2932635](https://support.microsoft.com/kb/2932635) — 5.3.1003, февраль 2014 г.  
* [KB2899874](https://support.microsoft.com/kb/2899874) — 5.3.0721, октябрь 2013 г.
* [KB2875551](https://support.microsoft.com/kb/2875551) — 5.3.0534, август 2013 г.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).




<!--HONumber=Nov16_HO3-->


