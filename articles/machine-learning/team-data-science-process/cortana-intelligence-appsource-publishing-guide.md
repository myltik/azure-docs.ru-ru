---
title: Руководство по публикации Cortana Intelligence в AppSource | Документация Майкрософт
description: Ниже описаны все шаги, которые партнеру корпорации Майкрософт необходимо выполнить для публикации решений Cortana Intelligence в AppSource.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams
ms.openlocfilehash: 9f4e88be7b9b8e3ed7f6a2bbd299fd1e92f9c7af
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836625"
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Руководство по публикации Cortana Intelligence в AppSource

## <a name="overview"></a>Обзор
AppSource — это единственная площадка, где лица, принимающие коммерческие решения, могут находить и легко испытывать бизнес-решения и приложения, которые созданы партнерами и оценены корпорацией Майкрософт. Просмотрите [этот видеоролик](https://youtu.be/hpq_Y9LuIB8), чтобы узнать, как работает AppSource. 

Как партнер Майкрософт вы можете получить преимущество от публикации на AppSource, если:
- вы создали интеллектуальное решение или приложение, используя [Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/?cdn=disable);
- ваше решение или приложение предназначено для решения определенной бизнес-проблемы;
- вы создали модули или интеллектуальную собственность, которую клиенты могут повторно использовать предсказуемым образом в сравнительно краткие строки.

Взгляните на [решения Cortana Intelligence](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1), опубликованные в AppSource. 

В этой статье приводится пошаговое руководство по публикации партнерского решения Cortana Intelligence в AppSource.

## <a name="getting-started"></a>Приступая к работе
1. В [руководстве для сообщества партнеров](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf) (PDF) перейдите на страницу 9, чтобы стать партнером по углубленной аналитике.
1. Заполните форму [Отправить приложение](https://appsource.microsoft.com/en-us/partners/list-an-app).

    При отображении вопроса *Choose the products that your app is built for* (Выберите продукты, для которых предназначено ваше приложение) установите флажок **Другое** и укажите "Cortana Intelligence" в элементе управления "Поле ввода".
1. Прежде чем приложение Cortana Intelligence будет подключено к AppSource, его должна сертифицировать технологическая команда по решениям партнеров Cortana Intelligence. Чтобы начать процесс, поделитесь сведениями о своем приложении, заполнив форму опроса на странице [Cortana Intelligence solution evaluation for AppSource publishing](https://aka.ms/cisappsrceval) (Оценка решения Cortana Intelligence для публикации в AppSource). Этот веб-сайт защищен паролем и содержит инструкции о том, как его получить.

## <a name="solution-evaluation-criteria"></a>Критерии оценки решения
Ниже приведен список критериев, который должно соответствовать приложение.
1. Приложение должно повторяемым образом решать определенную проблему в рамках того или иного варианта использования в заданной функциональной области. Кроме того, оно должно иметь минимальную конфигурацию для предопределенных значений и реализоваться за короткий промежуток времени.
1. Решение должно использовать по меньшей мере один из следующих компонентов:

    - HDInsight
    - Машинное обучение
    - Data Lake Analytics
    - Stream Analytics
    - Cognitive Services
    - Bot Framework
    - Analysis Services
    - Автономный Microsoft R Server
    - Службы R в SQL 2016 или HDInsight уровня "Премиум"
1. Решение должно приносить не менее 1000 долл. США в месяц на одного клиента, использующего программы зарегистрированных партнеров по цифровым технологиям (DPOR) или поставщиков облачных решений (CSP).
1. Решение должно использовать федеративный единый вход Azure Active Directory с разрешением на проверку подлинности пользователей и управление доступом к ресурсам. Вам нужно будет показать группе по оценке, что ваше решение использует федеративный единый вход Azure Active Directory, прежде чем ваше приложение будет добавлено в AppSource.

     Чтобы увидеть, что такое включенный федеративный единый вход Azure Active Directory, перейдите на время 02:35 в видеоролике [AppSource trial experience walk through](https://aka.ms/trialexperienceforwebapps) (Руководство по пробной версии AppSource). Если в вашем приложении еще не включен федеративный единый вход Azure Active Directory, вот некоторые сведения о нем:
   1. [Вход одним щелчком](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/)
   1. [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)
     
1. Использование Power BI в решении: необязательно, но настоятельно рекомендуется, так как это привлекает множество потенциальных клиентов.

## <a name="devcenter-account-setup"></a>Настройка учетной записи центра разработчиков
Это процесс регистрации вашей компании, чтобы стать издателем через корпорацию Майкрософт. Если вы уже зарегистрированный издатель с имеющейся учетной записью центра разработчиков, предоставьте связанный с ней идентификатор адреса электронной почты. Когда приложение будет утверждено для публикации, вы получите доступ к полной документации по [управлению профилем издателя облачного портала](https://cloudpartner.azure.com/#documentation/manage-publisher-profile).

Если у вас нет учетной записи, ниже приведены основные шаги для настройки учетной записи центра разработчиков.
1. Создайте учетную запись Майкрософт [здесь](https://signup.live.com/signup.aspx).
1. Перейдите на [страницу регистрации](http://go.microsoft.com/fwlink/?LinkId=615100) центра разработчиков Майкрософт и щелкните "Зарегистрироваться".
1. При появлении запроса на оплату используйте код, который мы предоставили вам. Если у вас нет кода, отправьте сообщение по адресу [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup).
1. Выберите [страну и регион](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees) где вы проживаете или расположен ваш бизнес. **Вы не сможете изменить это позже.**
1. Выберите [тип учетной записи разработчика](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees). Для AppSource выберите **Организация**. Для учетной записи организации обязательно ознакомьтесь с этими [рекомендациями](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account).
1. Введите контактные данные, которые будут использоваться для учетной записи разработчика.
Подробные пошаговые инструкции о способах настройки учетной записи центра разработчиков см. в инструкциях [здесь](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration).

## <a name="provide-info-for-microsoft-sellers"></a>Указание сведений для продавцов Майкрософт
Одна из ключевых возможностей AppSource для партнеров — совместная работа с продавцами Майкрософт при позиционировании приложений партнеров перед потенциальными клиентами.

Заполните форму [Partner Solution Info for Microsoft Sellers](https://aka.ms/aapartnerappinfo) (Информация о решении партнера для продавцов Майкрософт) и отправьте ее по адресу [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs). Это необходимый шаг для приложения Cortana Intelligence, чтобы получить утверждение для публикации.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>Создание привлекательного клиентского руководства в AppSource
Во-первых, рассмотрим решение [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome) на AppSource. Каждая запись о приложении в AppSource имеет заголовок, сводку (максимум 100 знаков), описание (максимум 1300 знаков), изображения, видео (необязательно), документы PDF, помимо ссылки для использования пробной версии. Партнеры должны использовать все это, чтобы привлечь клиентов.

Партнерам следует думать о содержимом, которое они добавляют в AppSource, как об оркестрации всех стадий продажи. Сначала клиенты читают название и описание, чтобы понять предлагаемые преимущества, а затем просматривают изображения и видео, чтобы понять, для чего предназначено это решение. Примеры использования помогают потенциальным клиентам увидеть, каким образом клиенты получают преимущества. 

Все это должно заинтересовать клиента и вызвать у него желание узнать больше. Думайте об этом как о презентации в слайдах, которую представляет хороший специалист по сбыту для новых клиентов. Что касается описания, рекомендуется разбить текст на подразделы в зависимости от предлагаемых преимуществ и выделить каждый из них подзаголовком. 

Клиенты обычно кратко просматривают поле сводки приложения и подзаголовки, чтобы получить представление о его предназначении и решить, стоит ли им на него взглянуть. Таким образом, важно привлечь внимание пользователя и дать ему причину продолжить чтение для получения подробных сведений.

Узнайте, что сделали эти партнеры.
- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure Retail Personalization](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [AvePoint Citizen Services](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

Заключительный этап продаж — продемонстрировать, каким образом приложение или решение предоставляет предлагаемое преимущество. Это именно то, для чего предназначена возможность использования пробной версии в AppSource. В хорошей демонстрации:
- Вкратце повторно показаны предлагаемые преимущества приложения и содержится список всех пользователей компании клиента, которые будут взаимодействовать с решением.
- Рассказывается история о клиенте, где он разбирается с определенными проблемами в конкретном контексте.
- Объясняется, как ситуация могла бы развиваться, и приводится сравнение положения при использовании решения и без него. Это можно отобразить с помощью панелей мониторинга PowerBI и т. д.
- Подводится итог того, как решение предоставляет преимущества с помощью любого из определенных алгоритмов машинного обучения и т. д.

Содержимое, добавляемое в AppSource, должно быть высокого качества и скомпоновано достаточно хорошо, чтобы:
- Специалисты по техническому сопровождению партнера могли использовать его для организации своих продаж. Если ваши специалисты по продажам используют его, значит, специалисты по продажам Майкрософт также смогут делать это. Таким образом, контактное лицо клиента сможет согласованно передавать одну и ту же историю своим товарищам по команде и руководителям, чтобы получить средства из бюджета и одобрение до того, как сделка по покупке произойдет.
- Клиент, посетивший веб-сайт, может самостоятельно ознакомиться с содержимым и захотеть связаться с партнером для обсуждения дальнейших шагов.

Поэтому партнерам следует думать о содержимом, которое они добавляют в AppSource как об оркестрации всех стадий продаж. На основе истории и функций, показанных в пробной версии, можно определить тип предложения.

## <a name="publish-your-app-on-the-publishing-portal"></a>Публикация приложения на портале публикации
Когда мы оценим все шаги выше для вашего приложения, вы получите доступ к порталу публикации и сможете ознакомиться с подробными инструкциями в разделе [How to publish a Cortana Intelligence offer via Cloud Partner Portal](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app) (Как публиковать предложения Cortana Intelligence через облачный портал для партнеров).

Чтобы получить сведения о любом из полей, отправьте сообщение по адресу <appsourcecissupport@microsoft.com>.
## <a name="my-app-is-published-on-appsource---now-what"></a>Что делать, когда приложение опубликовано на AppSource
Во-первых, поздравляем с публикацией приложения.
Отдача, которую вы получите от публикации своего приложения в AppSource, сильно зависит от вашего влияния на целевую аудиторию. Дополнительные сведения о том, как добиться максимальной отдачи, см. в документе [Growth-Hacking your Cortana Intelligence app on AppSource](http://aka.ms/aagrowthhackguide) (Увеличение спроса на приложение Cortana Intelligence в AppSource).

Если у вас есть вопросы или предложения, отправьте сообщение по адресу <appsourcecissupport@microsoft.com>.

