<properties pageTitle="Начало работы с данными (магазин Windows JavaScript) | Центр разработчиков для мобильных устройств" description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении JavaScript Магазина Windows." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga"/>


# Добавление мобильных служб к существующему приложению
[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

В этом разделе показано, как применять мобильные службы Azure для эффективного использования данных в универсальном приложении Windows. Решения для универсальных приложений Windows включают проекты для приложений Магазинов Windows 8.1 и Windows Phone 8.1 и общий проект. Дополнительные сведения см. в разделе [Создание универсальных приложений Windows для Windows и Windows Phone](http://msdn.microsoft.com/ru-ru/library/windows/apps/xaml/dn609832.aspx).

В этом учебнике вам предстоит загрузить проект Visual Studio 2013 для универсального приложения Windows, которое хранит данные в памяти, создать мобильную службу, интегрировать мобильную службу с приложением, а затем выполнить вход на портал управления Azure для просмотра изменений, внесенных в данные в ходе выполнения приложения.

>[AZURE.NOTE]В этом разделе поясняется, как использовать средства Visual Studio Express 2013 для Windows с обновлением 3 для подключения новой мобильной службы к универсальному приложению Windows. Эти же инструкции можно использовать для подключения мобильной службы к приложению Магазина Windows или Магазина Windows Phone 8.1. Сведения о том, как подключить мобильную службу к приложению Windows Phone 8.0 или Windows Phone Silverlight 8.1, см. в разделе [Приступая к работе с данными для Windows Phone](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

В этом учебнике рассматриваются следующие основные действия:

1. [Загрузка проекта приложения для Магазина Windows][Получение приложения для Магазина Windows] 
2. [Создание мобильной службы]
3. [Добавление таблицы для хранения данных]
4. [Обновление приложения для использования мобильных служб]
5. [Тестирование работы приложения с мобильными службами]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/ru-ru/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-ru%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 для Windows</a> (с обновлением 2 или более поздней версии). 

##<a name="download-app"></a>Загрузка проекта GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Создание мобильной службы в Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8">
<li><p>В обозревателе решений перейдите ко вложенной папке <strong>services\mobileService\scripts</strong>, откройте файл скрипта service.js и обратите внимание на новую глобальную переменную, которая выглядит, как в следующем примере:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Этот код предоставляет доступ к новой мобильной службе в вашем приложении с помощью глобальной переменной. Клиент создается с указанием URI и ключа приложения новой мобильной службы. Так как ссылка на этот скрипт была добавлена в файл default.html, эта переменная доступна для всех файлов скриптов, на которые также указывает ссылка на этой странице.</p>
</li>
<li><p>Откройте файл проекта default.html, найдите ссылку на новый файл скрипта service.js и убедитесь в том, что путь ссылки выглядит следующим образом:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>В настоящее время в Visual Studio есть ошибка, из-за которой создается путь с неправильным именем папки.</p></li>
<li><p>Щелкните проект приложения Windows Phone правой кнопкой мыши, выберите пункт <strong>Добавить</strong>, щелкните <strong>Подключенная служба...</strong>, выберите только что созданную мобильную службу и нажмите кнопку <strong>ОК</strong>. </p>
<p>Тот же новый файл с кодом добавляется в проект приложения Магазина Windows Phone. Не забудьте также исправить путь ссылки, добавленный в файл default.html.</p></li>
</ol>

##<a name="add-table"></a>Добавление новой таблицы в мобильную службу

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Новые таблицы создаются со столбцами "Id", "__createdAt", "__updatedAt" и "__version". Если динамическая схема включена, мобильные службы автоматически создают новые столбцы на основе JSON-объекта в запросе вставки или обновления. Дополнительные сведения см. в разделе [Динамическая схема](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Обновление приложения для использования мобильной службы

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-azure-hosted"></a>Тестирование мобильной службы, размещенной в Azure

Теперь мы можем протестировать обе версии универсального приложения Windows с мобильной службой, размещенной в Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>На <a href="https://manage.windowsazure.com/" target="_blank">портале управления</a> щелкните <strong>Мобильные службы</strong>, затем щелкните свою мобильную службу.<p></li>
<li><p>Откройте вкладку <strong>Данные</strong>, а затем щелкните <strong>Обзор</strong>.</p>
<p>Обратите внимание, что таблица <strong>TodoItem</strong> теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
Это заключительный шаг учебника **Приступая к работе с данными**.

## <a name="next-steps"> </a>Дальнейшие действия

В этом учебнике содержатся базовые сведения о том, как включить в универсальном приложении Windows возможности работы с данными в мобильных службах. Далее рассмотрите выполнение одного из следующих учебников, которые основаны на приложении GetStartedWithData, созданном в этом учебнике:

* [Проверка и изменение данных с помощью скриптов]
  <br/>Дополнительные сведения об использовании серверных скриптов в мобильных службах для проверки и изменения данных, отправляемых из приложения.

* [Уточнение запросов c разбиением по страницам]
  <br/>Сведения об использовании разбиения по страницам в запросах для управления объемом данных, обрабатываемым в одном запросе.

После завершения серии учебников по работе с данными попробуйте один из следующих учебников:

* [Приступая к работе с проверкой подлинности]
  <br/>Сведения о выполнении аутентификации учетных данных пользователей приложения.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник принципов использования мобильных служб HTML/JavaScript]
  <br/>Дополнительные сведения об использовании мобильных служб с HTML и JavaScript.

<!-- Anchors. -->

[Получение приложения для Магазина Windows]: #download-app
[Создание мобильной службы]: #create-service
[Добавление таблицы для хранения данных]: #add-table
[Обновление приложения для использования мобильных служб]: #update-app
[Тестирование работы приложения с мобильными службами]: #test-app
[Дальнейшие действия]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Проверка и изменение данных с помощью скриптов]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Уточнение запросов c разбиением по страницам]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Приступая к работе с мобильными службами]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Приступая к работе с данными]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Портал управления Azure]: https://manage.windowsazure.com/
[Портал управления]: https://manage.windowsazure.com/
[Пакет SDK для мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=257545
[Веб-сайт с примерами кода для разработчиков]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Справочник принципов использования мобильных служб HTML/JavaScript]: /ru-ru/documentation/articles/mobile-services-html-how-to-use-client-library/


<!--HONumber=42-->
