<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Приступая к работе с синхронизацией автономных данных в мобильных службах

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ru-ru/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

В этом учебнике рассматривается функция автономной синхронизации мобильных служб, позволяющая разработчикам создавать приложения, которые могут использоваться даже в том случае, когда у конечного пользователя нет никакого сетевого доступа.

Автономная синхронизация может применяться в следующих случаях:

-   Повышение способности быстро реагировать путем локального кэширования данных сервера в устройстве
-   Защита приложений от потери сетевой связности
-   Предоставление конечным пользователям возможности создания и изменения данных даже в том случае, когда отсутствует сетевой доступ, поддерживая сценарии с минимальной связностью или при отсутствии связности
-   Синхронизация данных между несколькими устройствами и обнаружение конфликтов, когда два устройства изменяют одну и ту же запись

В этом учебнике показано, как обновлять приложение, созданное в учебнике [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами] или [Приступая к работе с данными][Приступая к работе с данными], для поддержки автономных функций мобильных служб Azure. Затем вы добавите данные в автономном сценарии без подключения, синхронизируете эти элементы с оперативной базой данных и войдете на портал управления Azure для просмотра изменений, внесенных в данные после запуска приложения.

> [WACOM.NOTE] Чтобы выполнить работу с этим учебником, необходимо использовать учетную запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][Бесплатная пробная версия Azure].

## Получение примера приложения

**Полный учебник скоро появится. Пока см. пример [списка автономных дел iOS][списка автономных дел iOS] и [сопутствующее сообщение в блоге][сопутствующее сообщение в блоге].**

<!-- ## Next steps  * [How to use the Mobile Services client library for iOS]    --> 



  [Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-ios-get-started/
  [Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-ios-get-started-data/
  [Бесплатная пробная версия Azure]: http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=AE564AB28
  [списка автономных дел iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
  [сопутствующее сообщение в блоге]: http://aka.ms/iosoffline
