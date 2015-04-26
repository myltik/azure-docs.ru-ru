<properties 
	pageTitle="Использование серверных скриптов для проверки данных (Windows Phone) | Центр разработчиков мобильных приложений" 
	description="Узнайте, как проверять и изменять данные, отправленные с помощью серверных сценариев из приложения Windows Phone." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Проверка и изменение данных в мобильных службах с помощью серверных скриптов

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>В этом разделе показано, как использовать серверные скрипты в мобильных службах Azure. Серверные скрипты зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. В этом руководстве вы определите и зарегистрируете серверные скрипты, которые проверяют и изменяют данные. Так как поведение серверных сценариев часто влияет на клиент, вам также понадобится обновить приложение для Windows Phone 8, чтобы воспользоваться преимуществом новых возможностей поведения.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="label">Просмотр учебника</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-validate-modify-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="dev-onpage-video"><span class="icon">Воспроизведение видео</span></a> <span class="time">11:36</span></div>
</div>

В этом учебнике рассматриваются следующие основные действия:

1. [Добавление проверки длины строки]
2. [Обновление клиента для поддержки проверки]
3. [Добавление метки времени при вставке]
4. [Обновление клиента для отображения метки времени]

В основе этого учебника лежат инструкции и пример приложения из предыдущего учебника [Добавление мобильных служб в существующее приложение](/ru-ru/documentation/articles/mobile-services-windows-phone-get-started-data/). Прежде чем начать работу с этим учебником, необходимо закончить изучение данного учебника.  

## <a name="string-length-validation"></a>Добавление проверки

Рекомендуется всегда проверять длину данных, предоставляемых пользователями. Сначала необходимо зарегистрировать скрипт, который проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинны строки; в этом случае отклоняются строки длиной более 10 знаков.

1. Войдите на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение. 

	![][0]

2. На вкладке **Данные** щелкните таблицу **TodoItem**.

	![][1]

3. Выберите элемент **Скрипт**, а затем - операцию **Вставить**.

	![][2]

4. Замените существующий скрипт указанной ниже функцией и нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Этот скрипт проверяет длину свойства **TodoItem.text** и отправляет ответ с сообщением об ошибке, если длина превышает 10 символов. В противном случае вызывается метод **execute** для завершения вставки.

    > [AZURE.TIP] Можно удалить зарегистрированный скрипт на вкладке **Скрипт**, последовательно щелкнув элементы **Очистить** и **Сохранить**.	

## <a name="update-client-validation"></a>Обновление клиента

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, необходимо обновить приложение для обработки сообщений об ошибках, полученных при выполнении проверки.

1. В Visual Studio 2012 Express для Windows Phone откройте проект, измененный после изучения учебника [Приступая к работе с данными].

2. Нажмите клавишу **F5**, чтобы запустить приложение, затем введите в поле текст, длина которого превышает 10 символов, и нажмите кнопку **Сохранить**.

   	Обратите внимание, что приложение вызывает необработанное исключение **MobileServiceInvalidOperationException** в результате ответа 400 (Неверный запрос), возвращенного мобильной службой.

6. 	Откройте файл MainPage.xaml.cs, а затем замените существующий метод **InsertTodoItem** следующим:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. 
			// When the operation completes and Mobile Services has 
			// assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                ite
	ms.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
					MessageBoxButton.OK);
            }
        }

   	Эта версия метода включает обработку ошибок для исключения **MobileServiceInvalidOperationException** , которое отображает ответ на ошибку в окне сообщения.

## <a name="add-timestamp"></a>Добавление метки времени

В предыдущих задачах проверялась операция вставки, после чего она была принята или отклонена. Теперь можно обновить вставленные данные путем использования серверного скрипта, который добавляет свойство отметки времени в объект до его вставки.

> [AZURE.NOTE] Продемонстрированное здесь свойство метки времени **createdAt** теперь является избыточным. Мобильные службы автоматически создают системное свойство **__createdAt** для каждой таблицы. Чтобы использовать это свойство системы в своем приложении, просто добавьте следующий элемент в класс TodoItem.  
> 
`````
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
`````


1. На вкладке **Скрипты** [портала управления] замените текущий скрипт **вставки** на следующую функцию, а затем нажмите кнопку **Сохранить**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Эта функция улучшает предыдущий скрипт вставки, добавляя к объекту новое свойство - метку времени **createdAt** - до того, как он будет вставлен вызовом **request**.**execute**. 

    > [AZURE.IMPORTANT] При первом выполнении этого скрипта вставки должна быть включена динамическая схема. После включения динамической схемы мобильные службы автоматически добавляют столбец **createdAt** в таблицу **TodoItem** при первом выполнении. Для новой мобильной службы динамическая схема включена по умолчанию, и перед публикацией приложения в Магазине Windows Phone ее следует отключить.

2. В Visual Studio нажмите клавишу **F5**, чтобы запустить приложение, затем введите в поле текст (менее 10 символов) и нажмите кнопку **Сохранить**.

   	Обратите внимание, что новая отметка времени не отображается в пользовательском интерфейсе приложения.

3. На портале управления откройте вкладку **Обзор** в таблице **todoitem**.
   
   	Обратите внимание на появление столбца **createdAt** и на то, что новый вставленный элемент имеет значение отметки времени.
  
Далее необходимо обновить приложение Windows Phone для отображения этого нового столбца.

## <a name="update-client-timestamp"></a>Повторное обновление клиента

Клиент мобильной службы будет пропускать любые данные в ответе, которые не сможет сериализовать в свойства согласно заданному типу. Последним шагом является обновление клиента для отображения новых данных.

1. В Visual Studio откройте файл MainPage.xaml.cs, затем замените существующий класс **TodoItem** на следующее определение:

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
  
    > [AZURE.NOTE] Атрибут **DataMemberAttribute** указывает клиенту на необходимость сопоставления нового свойства **CreatedAt** в приложении со столбцом **createdAt**, определенным в таблице TodoItem, где используется другой регистр. С помощью этого атрибута ваше приложение может получить имена свойств для объектов, которые отличаются от имен столбцов в базе данных SQL. Без этого атрибута ошибка возникает из-за различия в буквенном регистре.

5. Добавьте следующий элемент XAML прямо под элементом **CheckBoxComplete** в файле MainPage.xaml:
	      
        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	Это приведет к отображению нового свойства **CreatedAt** в текстовом поле. 
	
6. Нажмите клавишу **F5**, чтобы запустить приложение. 

   	Обратите внимание, что отметка времени отображается только для вставленных элементов после обновления скрипта вставки.

7. Замените существующий метод **RefreshTodoItems** на следующий код:

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionAsync();

            ListIte
	ms.ItemsSource = items;
        }

   	Этот метод обновляет запрос для исключения тех элементов, которые не имеют значения отметки времени.
	
8. Нажмите клавишу **F5**, чтобы запустить приложение.

   	Обратите внимание, что все элементы, созданные без значения отметки времени, исключаются из пользовательского интерфейса.

Вы завершили работу с этим учебником по данным.

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, после прохождения этого учебника, рассмотрите возможность перехода к заключительному учебнику в этом ряду, касающемся данных: 

Серверный скрипт также используются при авторизации пользователей и для отправки push-уведомлений. Дополнительные сведения см. в следующих учебниках: [Уточнение запросов c разбиением по страницам]

* [Авторизация пользователей с помощью скриптов]
  <br/>Сведения о фильтрации данных на основе идентификатора пользователя, прошедшего проверку.

* [Приступая к работе с push-уведомлениями] 
  <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Справочник серверных скриптов мобильных служб]
  <br/>Дополнительная информация о регистрации и использовании серверных сценариев.

<!-- Anchors. -->
[Добавление проверки длины строки]: #string-length-validation
[Обновление клиента для поддержки проверки]: #update-client-validation
[Добавление метки времени при вставке]: #add-timestamp
[Обновление клиента для отображения метки времени]: #update-client-timestamp
[Дальнейшие действия]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/#create-new-service
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-wp8
[Уточнение запросов c разбиением по страницам]: /ru-ru/develop/mobile/tutorials/add-paging-to-data-wp8
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-wp8
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-wp8
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-wp8

[Портал управления]: https://manage.windowsazure.com/
[Портал управления Azure]: https://manage.windowsazure.com/



<!--HONumber=42-->
