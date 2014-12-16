<properties pageTitle="Добавление в данные разбиения на страницы (JavaScript). Мобильные службы Azure" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />


# Уточнение запросов мобильных служб c разбиением по страницам

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


В этом разделе показано, как использовать разбиение на страницы для управления объемом данных, возвращаемых мобильными службами Windows вашему приложению из Магазина Windows. В этом учебнике будут использоваться методы запроса **Take** и **Skip** на клиенте для запроса конкретных "страниц" данных.

>[WACOM.NOTE]Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию - 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.

В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Начало работы с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными]. 

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>Дальнейшие действия

На этом завершается серия учебников, демонстрирующих основы работы с данными в мобильных службах. Рассмотрите возможность лучше ознакомиться со следующими темами, касающимися мобильных служб:

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью учетной записи Windows.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

<!-- Anchors. -->

[Дальнейшие действия]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-windows-store-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Портал управления]: https://manage.windowsazure.com/
