<properties 
	pageTitle="Приступая к работе с мобильными службами Azure для приложений Appcelerator Titanium" 
	description="Этот учебник поможет приступить к использованию мобильных служб Azure для разработки приложений Appcelerator." 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-appcelerator" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="mahender"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

В этом учебнике показано, как использовать мобильные службы Azure в приложениях Appcelerator.

В этом учебнике будет создана новая мобильная служба и простое приложение _Список заданий_, которое хранит данные приложения в новой мобильной службе. Мобильная служба, которая будет создана, использует JavaScript для бизнес-логики на стороне сервера.

Для изучения этого учебника необходим [Appcelerator Titanium].

В случае создания приложения для iOS также потребуется Xcode 5.1 и iOS 7.1 SDK или более поздних версий. 

При создании приложений Android дополнительно понадобится Android 4.3 или пакет SDK более поздней версии.

## <a name="create-new-service"> </a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Создание нового приложения Appcelerator

1. На портале мобильных служб перейдите на вкладку **Данные** своей мобильной службы.

2. Щелкните элемент **Добавить таблицу** и создайте новую таблицу с именем **TodoItem**.

3. Скачайте новое приложение [Todolist Appcelerator] и распакуйте проект.

4. Если это еще не сделано, скачайте и установите [Appcelerator Titanium Studio][Appcelerator Titanium] и пакет SDK версии 3.2.1 или более поздней. Чтобы запустить проект, вам также понадобится Xcode (v 5.0 и выше) и (или) Android SDK (v 4.3 и выше).

5. Вернитесь на портал мобильных служб, в разделе **Панель мониторинга** выберите **Управление ключами** и скопируйте **Ключ приложения**.

5. В index.js приложения замените `<---App Name---->` и `<------------APP KEY------------>` на значения своей мобильной службы.

## Запустите новое приложение Appcelerator

Последний раздел учебника - построение и выполнение нового приложения.

1. Откройте Titanium Studio и перейдите к элементам **Файл -> Импорт**, чтобы импортировать проект, который был скачан ранее.

    ![][0]

2.	На следующем экране выберите **Существующие проекты и рабочая область**, а затем щелкните **Далее**.

    ![][1]

3.	На экране выбора проекта используйте параметр просмотра и найдите проект Azure Titanium, который был загружен с портала управления Azure.

    ![][2]

4.	Наконец, отображается проект, который был выбран в разделе "Проекты". Кроме этого, можно установить флажок &quot;Копировать проекты в рабочую область&quot;, в результате чего скачанный проект будет скопирован в рабочую область. И наконец, щелкните элемент "Готово", чтобы открыть проект в Titanium Studio.

    ![][3]

5.	Выберите платформу (iOS/Android), на которой необходимо запустить проект.

    ![][4]

6.	Нажмите кнопку "Запуск" для построения проекта, после чего запустите приложение в эмуляторе iPhone или эмуляторе Android.

7.	Сделайте свой выбор на основе параметров портала управления Azure.

    ![][5]

8.	На следующем экране щелкните значок "плюс" (+), введите значимый текст, например &quot;Выполнить этот учебник&quot;, а затем нажмите кнопку "Сохранить".<br />

    ![][6]

    ![][7]

При этом в новую мобильную службу, размещенную в Microsoft Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

![][8]


>[AZURE.NOTE] Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле TodoService.m.

4. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

   	![][9]

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

   	![][10]


## <a name="next-steps"> </a>Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах: 

* [Приступая к работе с проверкой подлинности]
  <br/>Дополнительные сведения о проверке подлинности пользователей приложения с использованием поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.



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
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push



<!--HONumber=42-->
