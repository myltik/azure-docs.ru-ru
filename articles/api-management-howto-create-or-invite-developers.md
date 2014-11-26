<properties pageTitle="How manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to create or invite developers in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Как управлять учетными записями разработчика в Azure API Management

В API Management (предварительная версия) разработчики являются пользователями интерфейсов API, которые вы предоставляете с помощью API Management. В этом руководстве показано, как создавать и приглашать разработчиков использовать интерфейсы API и продукты, которые вы делаете доступными для них с помощью своего экземпляра API Management.

## Содержание раздела

-   [Создание нового разработчика][Создание нового разработчика]
-   [Приглашение разработчика][Приглашение разработчика]
-   [Деактивация или повторная активация учетной записи разработчика][Деактивация или повторная активация учетной записи разработчика]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="create-developer"> </a>Создание нового разработчика

Для создания нового разработчика щелкните **Консоль управления** на портале Azure службы API Management. Открывается административный портал API Management.

> Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][Создание экземпляра службы API Management] в руководстве [Начинаем работу с API Management][Начинаем работу с API Management].

![Консоль API Management][Консоль API Management]

Щелкните **Разработчики** в расположенном слева меню **API Management**, а затем **Добавить пользователя**.

![Создание разработчика][Создание разработчика]

Введите **Адрес электронной почты**, **Пароль** и **Имя** для нового разработчика, а затем щелкните **Сохранить**.

![Создание разработчика][1]

По умолчанию недавно созданные учетные записи разработчика являются **активными** и связаны с группой **Разработчики**.

![Новый разработчик][Новый разработчик]

Учетные записи разработчика, которые находятся в **активном** состоянии, можно использовать для доступа ко всем интерфейсам API, на которые у них есть подписки. Процедуру связывания недавно созданного разработчика с дополнительными группами см. в разделе [Как связывать группы с разработчиками][Как связывать группы с разработчиками].

## <a name="invite-developer"> </a>Приглашение разработчика

Для приглашения разработчика щелкните **Разработчики** в расположенном слева меню **API Management**, а затем щелкните **Пригласить пользователя**.

![Приглашение разработчика][2]

Введите имя и адрес электронной почты разработчика, а затем щелкните **Пригласить**.

![Приглашение разработчика][3]

Появляется сообщение-подтверждение, но недавно приглашенный разработчик не появляется в списке до принятия приглашения.

![Подтверждение приглашения][Подтверждение приглашения]

> После приглашения разработчика ему отправляется сообщение электронной почты. Это сообщение создается с помощью шаблона и может настраиваться. Дополнительные сведения см. в разделе [Настройка почтовых шаблонов][Настройка почтовых шаблонов].

После принятия приглашения учетная запись становится активной.

## <a name="block-developer"> </a> Деактивация или повторная активация учетной записи разработчика

По умолчанию недавно созданные или приглашенные учетные записи разработчика являются **активными**. Для деактивации учетной записи разработчика щелкните **Блокировать**. Для повторной активации блокированной учетной записи разработчика щелкните **Активировать**. Блокированная учетная запись разработчика не может получать доступ к порталу разработчика или вызвать любые интерфейсы API.

![Блокировка разработчика][Новый разработчик]

## <a name="next-steps"> </a>Дальнейшие действия

После создания учетной записи разработчика ее можно связать с ролями и подписать ее на продукты и интерфейсы API. Дополнительные сведения см. в разделе [Как создать и использовать группы][Как создать и использовать группы].

  [Создание нового разработчика]: #create-developer
  [Приглашение разработчика]: #invite-developer
  [Деактивация или повторная активация учетной записи разработчика]: #block-developer
  [Дальнейшие действия]: #next-steps
  [Создание экземпляра службы API Management]: ../api-management-get-started/#create-service-instance
  [Начинаем работу с API Management]: ../api-management-get-started
  [Консоль API Management]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
  [Создание разработчика]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
  [1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
  [Новый разработчик]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
  [Как связывать группы с разработчиками]: ../api-management-howto-create-groups/#associate-group-developer
  [2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
  [3]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
  [Подтверждение приглашения]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
  [Настройка почтовых шаблонов]: ../api-management-howto-configure-notifications/#email-templates
  [Как создать и использовать группы]: ../api-management-howto-create-groups
