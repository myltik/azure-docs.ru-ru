<properties pageTitle="Добавление разбиения по страницам (JavaScript) - мобильные службы Azure" description="Сведения об использовании разбиения по страницам для управления объемом дata returned to your Windows Store JavaScript app from Mobile Services." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"/>


# Уточнение запросов мобильных служб c разбиением по страницам

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

В этом разделе показано, как использовать разбиение по страницам для управления объемом данных, возвращаемых мобильными службами Windows вашему приложению Магазина Windows. В этом учебнике методы запроса **Take** и **Skip** на стороне клиента будут использоваться для запроса определенных "страниц" данных.

>[AZURE.NOTE]Для предотвращения переполнения данных в клиентах мобильных устройств в мобильных службах реализовано автоматическое ограничение для страницы, значение по умолчанию - 50 элементов в ответе. Указав размер страницы, можно явно запросить до 1000 элементов в ответе.

Этот учебник основан на инструкциях и примере приложения предыдущего учебника, [Приступая к работе с данными]. Перед началом этого учебника необходимо изучить хотя бы первый учебник из серии по работе с данными, [Приступая к работе с данными]. 

[AZURE.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>Дальнейшие действия

Это заключительный раздел учебников, в которых демонстрируются основные принципы работы с данными в мобильных службах. Просмотрите следующие разделы, посвященные мобильным службам:

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о выполнении аутентификации учетных данных пользователей приложения с помощью учетной записи Windows.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

<!-- Anchors. -->

[Дальнейшие действия]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/

[Портал управления]: https://manage.windowsazure.com/


<!--HONumber=42-->
