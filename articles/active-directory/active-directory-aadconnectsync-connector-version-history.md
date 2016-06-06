<properties
   pageTitle="Синхронизация Azure AD Connect: история выпусков версий соединителя | Microsoft Azure"
   description="В этом разделе перечислены все соединители для Forefront Identity Manager (FIM) и Microsoft Identity Manager (MIM)."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/24/2016"
   ms.author="andkjell"/>

# Синхронизация Azure AD Connect: история выпусков версий соединителя
Соединители для Forefront Identity Manager (FIM) и Microsoft Identity Manager (MIM) часто обновляются.

Эта статья поможет вам отследить выпущенные версии и понять, требуется ли обновление до последней версии или нет.

Связанные ссылки

- [Скачивание последних соединителей](http://go.microsoft.com/fwlink/?LinkId=717495).
- Справочная документация по [универсальному соединителю LDAP](active-directory-aadconnectsync-connector-genericldap.md).
- Справочная документация по [универсальному соединителю SQL](active-directory-aadconnectsync-connector-genericsql.md).
- Справочная документация по [соединителю веб-служб](http://go.microsoft.com/fwlink/?LinkID=226245).
- Справочная документация по [соединителю PowerShell](active-directory-aadconnectsync-connector-powershell.md).
- Справочная документация по [соединителю Lotus Domino](active-directory-aadconnectsync-connector-domino.md).

## 1\.1.117.0
Дата выпуска: март 2016 г.

**Новый соединитель** Первоначальный выпуск [универсального соединителя SQL](active-directory-aadconnectsync-connector-genericsql.md).

**Новые функции:**

- Универсальный соединитель LDAP:
    - Добавлена поддержка импорта изменений с помощью Isode.
- Соединитель веб-служб:
    - Обновлены действия csEntryChangeResult и setImportErrorCode, чтобы устранить ошибки уровня объекта, возвращаемые в модуль синхронизации.
    - Обновлены шаблоны SAP6 и SAP6User для использования новых функций обработки ошибок уровня объекта.
- Соединитель Lotus Domino:
    - При экспорте требуется один издатель сертификатов на адресную книгу. Теперь можно использовать один и тот же пароль для всех издателей сертификатов, чтобы облегчить управление.

**Исправленные проблемы:**

- Универсальный соединитель LDAP:
    - IBM Tivoli DS: некоторые ссылочные атрибуты не обнаруживались должным образом.
    - Open LDAP: при импорте изменений усекались пробелы в начале и конце строки.
    - Novell и NetIQ: экспорт, при котором объект перемещается между подразделениями или контейнерами и в то же время переименовывался, приводил к сбою.
- Соединитель веб-служб:
    - Если у веб-службы было несколько конечных точек для одной привязки, то соединитель не мог правильно обнаружить эти конечные точки.
- Соединитель Lotus Domino:
    - Не работал экспорт атрибута fullName в базу данных обслуживания почты.
    - При экспорте с добавлением и удалением участника группы выполнялось только экспортирование добавленных участников.
    - Если документ Notes являлся недопустимым (атрибуту isValid задано значение false), происходил сбой соединителя.

## Более старые выпуски
До марта 2016 года соединители выпускались в виде разделов службы поддержки.

**Универсальный LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) — 1.0.0597, сентябрь 2015 г.
- [KB3044896](https://support.microsoft.com/kb/3044896) — 1.0.0549, март 2015 г.
- [KB3031009](https://support.microsoft.com/kb/3031009) — 1.0.0534, январь 2015 г.
- [KB3008177](https://support.microsoft.com/kb/3008177) — 1.0.0419, сентябрь 2014 г.
- [KB2936070](https://support.microsoft.com/kb/2936070) — 4.3.1082, март 2014 г.

**Веб-службы**

- [KB3008178](https://support.microsoft.com/kb/3008178) — 1.0.0419, сентябрь 2014 г.

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) — 1.0.0419, сентябрь 2014 г.

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) — 1.0.0597, сентябрь 2015 г.
- [KB3044895](https://support.microsoft.com/kb/3044895) — 1.0.0549, март 2015 г.
- [KB2977286](https://support.microsoft.com/kb/2977286) — 5.3.0712, август 2014 г.
- [KB2932635](https://support.microsoft.com/kb/2932635) — 5.3.1003, февраль 2014 г.  
- [KB2899874](https://support.microsoft.com/kb/2899874) — 5.3.0721, октябрь 2013 г.
- [KB2875551](https://support.microsoft.com/kb/2875551) — 5.3.0534, август 2013 г.

## Дальнейшие действия
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0525_2016-->