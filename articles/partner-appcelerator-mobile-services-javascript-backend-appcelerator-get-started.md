<properties pageTitle="Приступая к работе с мобильными службами Azure для Appcelerator Titanium" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Appcelerator development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender" />

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как использовать мобильные службы Azure в приложениях Appcelerator.

В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. Создаваемая вами мобильная служба использует JavaScript реализации бизнес-логики на сервере.

Для выполнения заданий этого учебника необходим [Appcelerator Titanium].

При сборке проекта для iOS вам также понадобятся Xcode 5.1 и iOS SDK версии 7.1 или выше. 

При создании приложений Android дополнительно понадобится Android 4.3 или пакет SDK более поздней версии.

## <a name="create-new-service"> </a>Создание мобильной службы

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Создание нового приложения Appcelerator

1. Выберите на портале мобильных служб вкладку **Данные** для вашего мобильного устройства.

2. Щелкните **Добавить таблицу** и создайте новую таблицу с именем **TodoItem**.

3. Загрузите новое приложение Appcelerator [Todolist] и разархивируйте проект.

4. Загрузите и установите [Appcelerator Titanium Studio][Appcelerator Titanium] и SDK версии 3.2.1 или выше, если не сделали этого раньше. Также для запуска проекта вам понадобятся Xcode (версии 5.0 или выше) и/или Android SDK (версии 4.3 или выше).

5. Откройте на портале мобильных служб **Панель мониторинга**, выберите **Управление ключами** и скопируйте **ключ приложения**.

5. Замените в файле index.js приложения строки `<---App Name---->` и `<------------APP KEY------------>` значениями из вашей мобильной службы.

## Запустите новое приложение Appcelerator ##

Последний раздел учебника - построение и выполнение нового приложения.

1. Откройте Titanium studio и перейдите в **Файл -> Импорт**, чтобы импортировать ранее загруженный вами проект.

    ![][0]

2.	В следующем окне выберите **Существующие проекты в рабочем пространстве** и щелкните **Далее**.

    ![][1]

3.	На экране выбора проекта используйте параметр просмотра и найдите проект Azure Titanium, который был загружен с портала управления Azure.

    ![][2]

4.	На экране отобразится проект, выбранный вами в разделе проектов. Можно также установить флажок &quot;Копировать проекты в рабочее пространство&quot;, при этом загруженный проект будет скопирован в ваше рабочее пространство. Нажмите кнопку "Завершить", чтобы открыть проект в Titanium Studio.

    ![][3]

5.	Выберите платформу (iOS/Android), на которой необходимо запустить проект.

    ![][4]

6.	Нажмите кнопку "Запуск" для построения проекта, после чего запустите приложение в эмуляторе iPhone или эмуляторе Android.

7.	Сделайте свой выбор на основе параметров портала управления Azure.

    ![][5]

8.	Щелкните на следующем экране значок "+", введите полный осмысленный текст (например, "Закончить этот учебник") и нажмите кнопку "Сохранить".<br/>

    ![][6]

    ![][7]

Новой мобильной службе, размещенной в Windows Azure, будет отправлен POST-запрос. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

![][8]


>[WACOM.NOTE] Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле TodoService.m.

4. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

   	![][9]

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

   	![][10]


## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с аутентификацией]
  <br/>Проверка подлинности пользователей приложения с помощью поставщика идентификации.

* [риступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
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

<!-- URLs. -->
[Приложение Todolist]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push
