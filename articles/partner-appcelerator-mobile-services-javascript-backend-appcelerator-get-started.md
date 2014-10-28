<properties pageTitle="Get Started with Azure Mobile Services for Appcelerator Titanium" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Appcelerator development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender"></tags>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

В этом учебнике показано, как использовать мобильные службы Azure в приложениях Appcelerator.

В этом учебнике вы создадите новую мобильную службу и простое приложение *To do list*, хранящее данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера.

Для изучения этого учебника необходим [Appcelerator Titanium][Appcelerator Titanium].

В случае создания приложения для iOS также потребуется Xcode 5.1 и iOS 7.1 SDK или более поздних версий.

При создании приложений Android дополнительно понадобится Android 4.3 или пакет SDK более поздней версии.

## <a name="create-new-service"> </a> Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service][mobile-services-create-new-service]]

## Создание нового приложения Appcelerator

1.  На портале мобильных служб перейдите на вкладку **Данные** своей мобильной службы.

2.  Щелкните элемент **Добавить таблицу** и создайте новую таблицу с именем **TodoItem**.

3.  Загрузите новое [приложение Todolist][приложение Todolist] Appcelerator и распакуйте проект.

4.  Если это еще не сделано, загрузите и установите [Appcelerator Titanium Studio][Appcelerator Titanium] и пакет SDK v3.2.1 или более поздней версии. Чтобы запустить проект, вам также понадобится Xcode (v 5.0 и выше) и (или) Android SDK (v 4.3 и выше).

5.  Вернитесь на портал мобильных служб, в разделе **Панель мониторинга**выберите **Управление ключами** и скопируйте **Ключ приложения**.

6.  В index.js приложения замените `<---App Name---->` и `<------------APP KEY------------>` на значения своей мобильной службы.

## Запустите новое приложение Appcelerator

Последний раздел учебника — построение и выполнение нового приложения.

1.  Откройте Titanium Studio и перейдите к элементам **Файл -\> Импорт**, чтобы импортировать проект, который был загружен ранее.

    ![][]

2.  На следующем экране выберите **Существующие проекты и рабочая область**, а затем щелкните **Далее**.

    ![][1]

3.  На экране выбора проекта используйте параметр просмотра и найдите проект Azure Titanium, который был загружен с портала управления Azure.

    ![][2]

4.  Наконец, отображается проект, который был выбран в разделе «Проекты». Кроме этого, можно установить флажок «Копировать проекты в рабочую область», в результате чего загруженный проект будет скопирован в рабочую область. И наконец, щелкните элемент «Готово», чтобы открыть проект в Titanium Studio.

    ![][3]

5.  Выберите платформу (iOS/Android), на которой необходимо запустить проект.

    ![][4]

6.  Нажмите кнопку «Запуск» для построения проекта, после чего запустите приложение в эмуляторе iPhone или эмуляторе Android.

7.  Сделайте свой выбор на основе параметров портала управления Azure.

    ![][5]

8.  На следующем экране щелкните значок «плюс» (+), введите значимый текст, например, «Выполнить этот учебник», и затем нажмите кнопку «Сохранить».

    ![][6]

    ![][7]

При этом в новую мобильную службу, размещенную в Windows Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

![][8]

> [WACOM.NOTE] Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле TodoService.m.

1.  На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

    ![][9]

    Это позволяет просматривать данные, добавленные в таблицу приложением.

    ![][10]

## <a name="next-steps"> </a> Дальнейшие действия

Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

-   [Приступая к работе с аутентификацией][Приступая к работе с аутентификацией]
    Дополнительные сведения об аутентификации учетных данных пользователей приложения с помощью поставщика удостоверений.

-   [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями]
    Сведения об отправке в приложение простейших push-уведомлений.

<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [приложение Todolist]: http://go.microsoft.com/fwlink/p/?LinkId=506859
  []: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
  [7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
  [8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
  [9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
  [10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png
  [Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push
