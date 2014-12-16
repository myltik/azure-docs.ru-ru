<properties pageTitle="Приступая к работе с мобильными службами для приложений из Магазина Windows | Центр разработчиков для мобильных устройств" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как добавить облачную серверную службу в универсальное приложение Windows с помощью мобильных служб Azure. 

В этом учебнике будет создана новая мобильная служба и простое приложение *To do list*, которое хранит свои данные в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера. Сведения о создании мобильной службы, которая позволит создавать в Visual Studio бизнес-логику на серверной стороне на языках, поддерживаемых платформой .NET, см. в разделе о серверной версии .NET.

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://www.windowsazure.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express для Windows] 

## Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Создание нового универсального приложения для Windows

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на портале управления, чтобы создать новое приложение или изменить существующее приложение и подключить его к вашей мобильной службе. 

В этом разделе будет создано новое приложение для магазина Windows, подключаемое к вашей мобильной службе.

1.  На портале управления щелкните **Мобильные службы**, затем щелкните только что созданную вами мобильную службу.

   
2. На вкладке быстрого запуска щелкните **Windows** в разделе **Выберите платформу** и разверните элемент **Создание нового приложения для Магазина Windows**.

   	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png)

   	Здесь показаны три простых шага по созданию приложения для магазина Windows, подключаемого к вашей мобильной службе.

  	![](./media/mobile-services-javascript-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png)

3. Если этого еще не сделано, загрузите и установите [Visual Studio 2013][Visual Studio 2013 Express для Windows] на своем локальном компьютере или виртуальной машине.

4. Щелкните **Создание таблицы TodoItem**, чтобы создать таблицу для хранения данных приложения.

5. В разделе **Загрузка и запуск приложения** выберите язык для своего приложения, а затем щелкните **Загрузить**. 

  	При этом будут загружены файлы проекта для примера по созданию приложения *To do list*, подключаемого к вашей мобильной службе. Сохраните упакованный файл проекта на своем локальном компьютере и зафиксируйте, в каком месте он был сохранен.

## Запустите приложение Windows

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

>[WACOM.NOTE]Можно просмотреть код доступа к мобильной службе для запроса и вставки данных, который находится в файле default.js.

## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с данными]
  <br/>Узнайте больше о хранении и передаче запросов к данным с использованием мобильных служб.

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

Дополнительные сведения об универсальных приложениях Windows см. в разделе [Поддержка нескольких платформ устройств из одной мобильной службы](/ru-ru/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Приступая к работе с мобильными службами]:#getting-started
[Создание мобильной службы]:#create-new-service
[Определение экземпляра мобильной службы]:#define-mobile-service-instance
[Дальнейшие действия]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
[Visual Studio 2013 Express для Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Портал управления]: https://manage.windowsazure.com/
