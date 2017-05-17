---
title: "Отчетность Azure Active Directory: начало работы | Документация Майкрософт"
description: "Перечислены различные отчеты, доступные в Azure Active Directory Reporting."
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.contentlocale: ru-ru
ms.lasthandoff: 02/24/2017


---
# <a name="getting-started-with-azure-active-directory-reporting"></a>Приступая к работе со средством создания отчетов Azure Active Directory
## <a name="what-it-is"></a>Что это
Azure Active Directory (Azure AD) формирует отчеты о безопасности, активности и аудите каталога. Ниже приведен список включенных отчетов.

### <a name="security-reports"></a>Отчеты о безопасности
* Попытки входа из неизвестных источников
* "Операции входа после нескольких неудачных попыток";
* "Операции входа из нескольких географических регионов".
* Попытки входа с IP-адресов с подозрительными действиями
* Нестандартные действия при входе
* Попытки входа с возможно инфицированных устройств
* Пользователи с аномальными событиями при входе

### <a name="activity-reports"></a>Отчеты об активности
* Использование приложения: сводка
* Использование приложения: подробности
* Панель мониторинга приложений
* Ошибки подготовки учетной записи
* Устройства отдельного пользователя
* Активность отдельного пользователя
* Отчет о действиях групп
* Отчет о событиях регистрации для сброса пароля
* Действие сброса пароля

### <a name="audit-reports"></a>Отчеты об аудите
* Отчет об аудите каталога

> [!TIP]
> Дополнительную документацию по Azure AD Reporting см. в статье [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="how-it-works"></a>Принцип работы
### <a name="reporting-pipeline"></a>Конвейер отчетов
Конвейер отчетов состоит из трех основных этапов. Каждый раз при входе пользователя в систему или при проверке подлинности, происходит следующее.

* Во-первых, пользователь проходит проверку подлинности (успешно или неуспешно) и результат сохраняется в базах данных службы Azure Active Directory.
* Все недавние попытки пользователей войти в систему регулярно обрабатываются с определенными интервалами. На этом этапе наши алгоритмы безопасности и поиска аномальных событий проверяют все недавние входы в систему на предмет подозрительных действий.
* После обработки отчеты записываются, сохраняются в кэше и становятся доступны на классическом портале Azure.

### <a name="report-generation-times"></a>Время создания отчета
Из-за большого объема проверок подлинности и входов, обрабатываемых платформой Azure AD, новые данные о входе пользователей в систему поступают в среднем с задержкой в один час. В редких случаях обработка последней попытки входа может занимать до 8 часов.

Изучив текст справки в верхней части каждого отчета, можно найти самый последний обработанный отчет о входе в систему.

![Текст справки в верхней части каждого отчета](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Дополнительную документацию по Azure AD Reporting см. в статье [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="getting-started"></a>Приступая к работе
### <a name="sign-into-the-azure-classic-portal"></a>Перейдите на классический портал Azure.
Сначала необходимо войти на [классический портал Azure](https://manage.windowsazure.com) с правами глобального администратора или администратора соответствия требованиям. Кроме того, вы должны иметь права администратора или соадминистратора службы подписки Azure или использовать подписку "Доступ к Azure AD".

### <a name="navigate-to-reports"></a>Переход к отчетам
Для просмотра отчетов перейдите на вкладку «Отчеты» в верхней части вашего каталога.

Если вы впервые просматриваете отчеты, необходимо принять условия в диалоговом окне, прежде чем начать просмотр. Этим вы подтверждаете, что администраторы вашей организации имеют право на просмотр данной информации, которая в некоторых странах может считаться конфиденциальной.

![Диалоговое окно](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Изучение каждого отчета
Просмотрите каждый отчет, чтобы понять механизм сбора данных и обработки сведений о входе в систему. Список всех отчетов вы можете найти [здесь](active-directory-reporting-guide.md).

![Все отчеты](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Загрузка отчетов в формате CSV
Каждый отчет можно загрузить как CSV-файл (с разделителями-запятыми). Вы можете использовать эти файлы в Excel, PowerBI или сторонних программах для дальнейшего анализа данных.

Чтобы загрузить любой отчет в виде CSV-файла, перейдите к отчету и нажмите кнопку «Загрузить» в нижней части страницы.

![Кнопка загрузки](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Дополнительную документацию по Azure AD Reporting см. в статье [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Настройка оповещений в случае аномальных действий при входе в систему
Перейдите на вкладку «Настройка» каталога.

Прокрутите страницу вниз до раздела «Уведомления».

Включите или отключите параметр "Уведомления по электронной почте при аномальных попытках входа".

![Раздел уведомлений](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Интеграция с API службы Azure AD Reporting
См. статью [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Включение службы Multi-Factor Authentication для пользователей
Выберите пользователя в отчете.

Нажмите кнопку «Включить MFA» в нижней части экрана.

![Кнопка Multi-Factor Authentication, расположенная в нижней части экрана](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Дополнительную документацию по Azure AD Reporting см. в статье [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="learn-more"></a>Подробнее
### <a name="audit-events"></a>Аудит событий
Узнайте о том, какие события аудита учитываются в каталоге [События аудита Azure Active Directory Reporting](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Интеграция API
См. статьи [Приступая к работе с API отчетов Azure Active Directory](active-directory-reporting-api-getting-started.md) и [Azure AD Graph API reference](https://msdn.microsoft.com/library/azure/mt126081.aspx) (Справочник по API Graph в Azure AD).

### <a name="get-in-touch"></a>Будьте на связи
Чтобы отправить отзыв, получить справку или задать вопросы, напишите электронное письмо по адресу [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) .

> [!TIP]
> Дополнительную документацию по Azure AD Reporting см. в статье [Просмотр отчетов о доступе и использовании](active-directory-view-access-usage-reports.md).
> 
> 


