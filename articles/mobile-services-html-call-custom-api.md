<properties linkid="mobile-services-html-call-custom-api" urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from an HTML client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an HTML app that uses Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  solutions="" writer="jparrel" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Вызов настраиваемого API из приложения HTM

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Магазин Windows C#">Магазин Windows C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" >iOS</a><a href="/en-us/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-call-custom-api" title="HTML" class="current">HTML</a></div>

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложения HTML. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON.

Настраиваемый API, созданный в данном разделе, дает возможность отправить один запрос POST, который устанавливает для флага завершения значение `true` для всех элементов задач в таблице. Без настраиваемого интерфейса API клиенту требуется отправлять отдельные просьбы, чтобы обновить флаг для каждого элемента списка дел в таблице.

Вы добавите эту функцию в приложение, созданное в учебнике [Приступая к работе с мобильными службами][] или [Приступая к работе с данными][]. Для этого требуются следующие действия:

1.  [Определение настраиваемого интерфейса API][]
2.  [Обновление приложения для вызова настраиваемого API][]
3.  [Тестирование приложения][]

Этот учебник создан на основе краткого руководства по мобильным службам. Перед работой с этим учебником необходимо сначала пройти учебник [Приступая к работе с мобильными службами][] или [Приступая к работе с данными][].

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[WACOM.INCLUDE [mobile-services-create-custom-api][]]

## <a name="update-app"></a><span class="short-header">Обновление приложения </span>Обновление приложения для вызова настраиваемого API

1.  С помощью текстового редактора откройте файл index.htm, найдите элемент **button** с именем `buttonRefresh` и добавьте сразу после него следующий новый элемент:

        <button id="buttonCompleteAll">Complete All</button> 

    Это приведет к добавлению на страницу новой кнопки.

2.  В файле page.js после функции **refreshTodoItems** добавьте следующий код:

        var completeAllTodoItems = function () {
            // Asynchronously call the custom API using the POST method.
            client.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                alert(message);
                refreshTodoItems();
            }, function(error) {
                alert(error.message);
            });
        };

        $('#buttonCompleteAll').click(function () {
            completeAllTodoItems();
        });

    Этот метод обрабатывает событие **Click** для новой кнопки. Метод **invokeApi** вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки.

## <a name="test-app"></a> Тестирование приложения

1.  Обновите браузер.

2.  В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**

3.  Повторяйте предыдущий шаг, пока в список не будут добавлены несколько элементов списка дел.

4.  Нажмите кнопку **Выполнить все**. Отображается диалоговое окно сообщения, в котором указывается количество элементов, помеченных как завершенные; снова выполняется отфильтрованный запрос, что приводит к удалению из списка всех элементов.

## Дальнейшие действия

Теперь, после создания настраиваемого API и вызова его из приложения HTML, изучите следующие статьи о мобильных службах:

-   [Справочник серверных скриптов мобильных служб][]
    Дополнительные сведения о создании настраиваемых API.

<!-- Anchors. -->
<!-- URLs. -->

  [Магазин Windows C\#]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Магазин Windows C#"
  [Магазин Windows JavaScript]: /en-us/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Магазин Windows JavaScript"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-ios-call-custom-api "iOS"
  [Android]: /en-us/documentation/articles/mobile-services-android-call-custom-api "Android"
  [HTML]: /en-us/documentation/articles/mobile-services-html-call-custom-api "HTML"
  [Приступая к работе с мобильными службами]: /en-us/documentation/articles/mobile-services-html-get-started
  [Приступая к работе с данными]: /en-us/documentation/articles/mobile-services-html-get-started-data
  [Определение настраиваемого интерфейса API]: #define-custom-api
  [Обновление приложения для вызова настраиваемого API]: #update-app
  [Тестирование приложения]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [Справочник серверных скриптов мобильных служб]: http://go.microsoft.com/fwlink/?LinkId=262293
