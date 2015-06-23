<properties 
   pageTitle="Руководство по устранению неполадок Azure Mobile Engagement — API" 
   description="Руководства по устранению неполадок для Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>

# Поиск и устранение неполадок API

Ниже представлены проблемы, которые могут возникнуть в ходе взаимодействия администраторов с Azure Mobile Engagement через интерфейсы API.

## Проблемы, связанные с синтаксисом

### Проблема
- Синтаксические ошибки при использовании API (или неожиданное поведение).

### Причины

- Проблемы, связанные с синтаксисом
    - Проверьте синтаксис определенного API, который вы используете, чтобы подтвердить, что определенная возможность доступна.
    - Распространенная проблема с использованием API — неправильное использование Reach API и API push-уведомлений (большинство задач необходимо выполнять с помощью Reach API, а не API push-уведомлений). 
    - Еще одна распространенная проблема с интеграцией пакета SDK и использованием API — неправильное использование ключа пакета SDK и ключа API.
    - Скрипты, подключающиеся к интерфейсам API, должны отправлять данные по крайней мере каждые 10 минут, в противном случае время ожидания подключения истечет (эта проблема особенно распространена в скриптах Monitor API, прослушивающих данные). Чтобы предотвратить возникновение задержки, ваш скрипт должен отправлять команду ping XMPP каждые 10 минут, чтобы сеанс работы с сервером не завершался.

### Дополнительные материалы
 
- [Принципы — глоссарий][Link 6], [Документация по API][Link 4], [Сведения о протоколе XMPP](http://xmpp.org/extensions/xep-0199.html)
 
## Не удается использовать API для выполнения действия, доступного в пользовательском интерфейсе Azure Mobile Engagement.

### Проблема
- Действие, которое выполняется в пользовательском интерфейсе Azure Mobile Engagement, не выполняется в соответствующем API Azure Mobile Engagement.

### Причины

- Подтверждение того, что вы можете выполнять это действие в пользовательском интерфейсе Azure Mobile Engagement, свидетельствует о правильной интеграции этой функции Azure Mobile Engagement в пакет SDK.

### Дополнительные материалы
 
- [Документация по пользовательскому интерфейсу][Link 1]
 
## сообщения об ошибках

### Проблема
- Во время выполнения или по завершении в журналах отображаются коды ошибок, связанные с использованием API.

### Причины

- Ниже приведен общий список распространенных кодов состояния API для справки и предварительного устранения неполадок.

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504         The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### Дополнительные материалы

- [Документация по API — подробная информация об ошибках для каждого конкретного интерфейса API][Link 4]
 
## Автоматические сбои

### Проблема
- Действие с использованием API заканчивается сбоем, при этом во время выполнения или по завершении в журналах не отображается сообщение об ошибке.

### Причины

- При неправильной интеграции многие элементы будут отключены в пользовательском интерфейсе Azure Mobile Engagement и незаметно завершатся ошибкой в API, поэтому не забудьте проверить определенную функцию в пользовательском интерфейсе, чтобы убедиться, что она работает.
- Служба Azure Mobile Engagement и множество дополнительных функций этой службы, которые вы намереваетесь применять, перед использованием необходимо отдельно интегрировать в приложение с помощью пакета SDK.

### Дополнительные материалы

- [Руководство по устранению неполадок — пакет SDK][Link 25], [Документация к пакету SDK][Link 5]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

<!--HONumber=54--> 