<properties pageTitle="Refine Mobile Services queries with paging (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Уточнение запросов мобильных служб c разбиением по страницам

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title="Магазин Windows &mdash; C#" class="current">Магазин Windows &mdash; C#</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title="Магазин Windows &mdash; JavaScript">Магазин Windows &mdash; JavaScript</a><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a>
<!--<a href="/ru-ru/documentation/articles/mobile-services-ios-add-paging-data" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/mobile-services-android-add-paging-data" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Windows вашему приложению Магазина Windows. В этом учебнике методы запроса **Take** и **Skip** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

> [WACOM.NOTE]Чтобы предотвратить переполнение данных в клиентах мобильных устройств, в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию — 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными][Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными][Приступая к работе с данными].

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a> Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]

    Узнайте об аутентификации пользователей приложения с помощью учетной записи Windows.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]

    Сведения об отправке в приложение простейших push-уведомлений.

-   [Справочник принципов использования мобильных служб .NET][Справочник принципов использования мобильных служб .NET]

    Дополнительные сведения об использовании мобильных служб в .NET.



  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-windows-dotnet-paging]: ../includes/mobile-services-windows-dotnet-paging.md
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
