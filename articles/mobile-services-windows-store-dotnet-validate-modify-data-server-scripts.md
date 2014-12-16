<properties urlDisplayName="Validate and Modify Data" pageTitle="Пользовательские серверные скрипты для проверки и изменения данных (Магазин Windows) | Центр мобильных разработок" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных скриптов часто влияет на клиента, также понадобится обновить приложение Магазина Windows, чтобы воспользоваться преимуществом этого нового поведения.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/validate-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">09:54:00</span></div>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление отметки времени при вставке]
4. [Обновление клиента для отображения отметки времени]

В основе этого учебника лежат инструкции и образец приложения из предыдущего учебника [Начало работы с данными]. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с данными].  

## <a name="string-length-validation"></a>Добавить проверку

Всегда рекомендуется проверять длину данных, отправляемых пользователям. Вначале регистрируется скрипт для проверки значений длины данных строк, отправляемых мобильной службе, и удаления слишком длинных строк, в данном случае - превышающих 10 символов.

1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение. 

   	![][0]

2. Откройте вкладку **Данные** , затем щелкните таблицу  **TodoItem**.

   	![][1]

3. Щелкните  **Скрипт**, выберите операцию **Вставить**.

   	![][2]

4. Замените имеющийся скрипт следующей функцией и нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Этот сценарий проверяет длину свойства **TodoItem.text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute**  для завершения вставки.

    <div class="dev-callout"> 
	<b>Примечание.</b> 
	<p>Можно удалить зарегистрированный скрипт на вкладке <strong>Скрипт</strong> , щелкнув <strong>Очистить</strong> и <strong>Сохранить</strong>.</p></div>

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. Откройте в среде Visual Studio 2012 Express для Windows 8 проект, измененный при выполнении учебника  [Приступая к работе с данными].

2. Нажмите клавишу **F5**, чтобы запустить приложение, затем введите в поле **Вставить элемент TodoItem** текст, длина которого превышает 10 символов, и нажмите кнопку **Сохранить**.

   	Обратите внимание, что приложение вызывает необработанное исключение **MobileServiceInvalidOperationException**, т. к. мобильная служба вернула ответ с кодом 400 (Bad Request).	

6. 	Откройте файл проекта MainPage.xaml.cs и добавьте следующую инструкцию **using** :

        using Windows.UI.Popups;

7. Замените существующий метод **InsertTodoItem** на следующий код:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database.
            // When the operation completes and Mobile Services has
            // assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageDialog errormsg = new MessageDialog(e.Message, 
                    string.Format("{0} (HTTP {1})",                     
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode));
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

   	Эта версия метода включает обработку ошибок для исключения **MobileServiceInvalidOperationException**, которое отображает ответ на ошибку в окне всплывающего сообщения.

## <a name="add-timestamp"></a>Добавление временной метки

В предыдущих задачах проверялась операция вставки, после чего была принята или отклонена. Теперь будет проводиться обновление вставленных данных с использованием серверного скрипта, который добавляет свойство временной метки к объекту перед его вставкой.


<div class="dev-callout"><b>Примечание.</b>
<p>Продемонстрированное здесь свойство отметки времени  <b>createdAt</b> теперь является избыточным. Мобильные службы автоматически создают системное свойство <b>__createdAt</b> для каждой таблицы. Можно использовать это свойство системы в своем приложении, путем добавления следующего члена в класс TodoItem</p>
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
</code></pre>
</div>


1. На вкладке **Скрипты** на [Портале управления] замените текущий скрипт **Вставка** следующей функцией, затем щелкните ** Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Эта функция улучшает предыдущий скрипт вставки, добавляя к объекту новое свойство - метку времени **createdAt**до того, как он будет вставлен вызовом **request**.**execute**. 

    <div class="dev-callout"><b>Примечание.</b>
	<p>При первом выполнении этого скрипта вставки должна быть включена динамическая схема. При включенной динамической схеме мобильные службы автоматически добавляют столбец <strong>createdAt</strong> в таблицу <strong>TodoItem</strong> при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows ее следует отключить.</p>
    </div>

2. В Visual Studio нажмите клавишу **F5**, чтобы запустить приложение, затем введите в поле **Вставить элемент TodoItem** текст (менее 10 символов) и нажмите кнопку **Сохранить**.

   	Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3. Возвратившись на портал управления, откройте вкладку **Обзор** в таблице **todoitem**.
   
   	Обратите внимание, что теперь появился столбец **createdAt**, а вновь вставленный элемент имеет значение временной метки.
  
Далее необходимо обновить приложение магазина Windows для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Снова обновите клиент

Клиент мобильной службы будет пропускать любые данные в ответе, которые не сможет сериализовать в свойства согласно заданному типу. Последним шагом является обновление клиента для отображения этих новых данных.

1. Откройте в Visual Studio файл MainPage.xaml.cs, затем замените существующий метод **TodoItem** следующим определением:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        
            [JsonProperty(PropertyName = "createdAt")]
            public DateTime? CreatedAt { get; set; }
        }
	
    Это новое определение класса включает новое свойство отметки времени в качестве типа даты и времени, который поддерживает значение NULL.
  
    <div class="dev-callout"><b>Примечание.</b>
	<p>Атрибут  <code>DataMemberAttribute</code> предлагает клиенту сопоставить новое свойство <code>CreatedAt</code> в приложении со столбцом <code>createdAt</code>, определенным в таблице TodoItem, в которой используется другой регистр. С помощью этого атрибута ваше приложение может получить имена свойств для объектов, которые отличаются от имен столбцов в базе данных SQL. Без этого атрибута ошибка возникает из-за различия в буквенном регистре.</p>
    </div>

5. Добавьте следующий элемент XAML прямо под элементом **CheckBoxComplete** в файле MainPage.xaml file:
	      
        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	Это приведет к отображению нового свойства **CreatedAt** в текстовом поле. 
	
6. Нажмите клавишу **F5**, чтобы запустить приложение. 

   	Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

7. Замените существующий метод **RefreshTodoItems** следующим кодом:

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;
        }

   	Этот метод обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.
	
8. Нажмите клавишу **F5**, чтобы запустить приложение.

   	Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с учебником данных.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: [Уточнение запросов с постраничным просмотром].

Серверный скрипт также используются при авторизации пользователей и для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках:

* [Авторизация пользователей с помощью скриптов]
  <br/>Фильтрация данных с учетом идентификатора пользователя, прошедшего проверку подлинности.

* [Приступая к работе с push-уведомлениями] 
  <br/>Отправка очень простого push-уведомления в приложение.

* [Справочник серверных скриптов мобильных служб]
  <br/>Регистрация и использование серверных скриптов.

* [Справочник принципов использования мобильных служб .NET]
  <br/>Использование мобильных служб с .NET.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Добавление отметки времени при вставке]: #add-timestamp
[Обновление клиента для отображения отметки времени]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Уточнение запросов посредством разбиения по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-dotnet
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-dotnet
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library
