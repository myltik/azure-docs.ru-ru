<properties 
	pageTitle="Как использовать клиент HTML для мобильных служб Azure" 
	description="Узнайте, как использовать клиент HTML для мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="glenga"/>


# Использование клиента HTML/JavaScript для мобильных служб Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../includes/mobile-services-selector-client-library.md)]

##Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием клиентов HTML и JavaScript для мобильных служб Azure, в том числе приложений JavaScript Магазина Windows, а также PhoneGap и Cordova. В сценарии входят запрос данных, вставка, обновление и удаление данных, проверка подлинности пользователей и обработка ошибок. Если вы не знакомы с мобильными службами, сначала мы рекомендуем прочитать [краткое руководство по мобильным службам](mobile-services-html-get-started.md). Учебник quickstart поможет вам настроить учетную запись и создать свою первую мобильную службу.

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Практическое руководство. Создание клиента мобильных служб

Способ добавления ссылки на клиент мобильных служб определяется платформой приложения и включает следующие действия.

- Если используется веб-приложение, откройте HTML-файл и добавьте следующее выражение в ссылки на скрипты на странице:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

- Если используется приложение Магазина Windows, написанное на JavaScript или HTML, в проект необходимо добавить только пакет NuGet **WindowsAzure.MobileServices.WinJS**.

- Если используется приложение PhoneGap или Cordova, добавьте в проект [подключаемый модуль мобильных служб](https://github.com/Azure/azure-mobile-services-cordova). Этот подключаемый модуль поддерживает [push-уведомления](#push-notifications).

В редакторе откройте или создайте файл JavaScript, добавьте следующее определение переменной `MobileServiceClient`, а также укажите URL-адрес и ключ приложения мобильной службы в конструкторе `MobileServiceClient` в указанном порядке.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Замените заполнитель `AppUrl` на URL-адрес приложения мобильной службы, а заполнитель `AppKey` — на ключ приложения. Чтобы узнать, как получить URL-адрес приложения и ключ приложения для мобильной службы, обратитесь к учебнику [Добавление мобильных служб в существующее приложение](mobile-services-html-get-started-data.md).

>[AZURE.IMPORTANT]Ключ приложения предназначен для фильтрации случайных запросов в мобильной службе и распространяется вместе с приложением. Поскольку этот ключ не зашифрован, он не может считаться безопасным. Чтобы обеспечить надежную защиту данных мобильной службы, необходимо выполнять проверку подлинности пользователей перед предоставлением им доступа. Дополнительные сведения см. в разделе [Практическое руководство. Проверка подлинности пользователей](#caching).

##<a name="querying"></a>Практическое руководство. Запрос данных от мобильной службы

Весь код, который открывает или изменяет данные в таблице базы данных SQL, вызывает функции для объекта `MobileServiceTable`. Ссылку на таблицу можно получить, вызвав функцию `getTable()` для экземпляра `MobileServiceClient`.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

В следующем коде показано, как фильтровать данные, включив предложение `where` в запрос. Код возвращает все элементы таблицы `todoItemTable`, поле complete которых имеет значение `false`. `todoItemTable` — это ссылка на созданную ранее таблицу мобильной службы. Функция where применяет предикат фильтрации строк для запросов к таблице. Он принимает в качестве аргумента объект JSON, который определяет фильтр строк и возвращает запрос, который можно компоновать далее.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Если добавить вызов `where` объекта Query и передать объект в качестве параметра, мобильные службы вернут только строки, в которых столбец `complete` содержит значение `false`. Кроме того, посмотрите на приведенный ниже URI запроса и обратите внимание на изменение самой строки запроса:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Можно просмотреть URI запроса, отправленного в мобильную службу, с помощью программы проверки сообщений, включая средства разработчика браузера и Fiddler.

На стороне сервера такой запрос обычно должен преобразовываться примерно в такой запрос SQL:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Объект, который передается в метод `where`, может включать произвольное число параметров, и все они будут интерпретироваться в запросе как предложения AND. Например, следующая строка:

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Будет преобразована (для того же запроса, приведенного выше) примерно в

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

В результате выполнения приведенной выше инструкции `where` и запроса SQL будут найдены незавершенные элементы, назначенные элементу david средней сложности (medium).

Однако существует другой способ составления того же запроса. Вызов `.where` объекта Query добавит в предложение `WHERE` выражение `AND`. Вместо него можно было бы написать следующие три строки:

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

или с помощью гибкого API:

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

Эти два способа эквивалентны и могут быть взаимозаменяемыми. До настоящего момента во все вызовы `where` передавался объект с параметрами и выполнялась проверка равенства с данными из базы данных. Однако есть другая перегрузка для метода запроса, который принимает функцию вместо объекта. Затем в этой функции можно составить более сложные выражения, используя операторы неравенства и другие реляционные операции. В этих функциях ключевое слово `this` привязывается к объекту сервера.

Тело функции преобразуется в логическое выражение Open Data Protocol (OData), которое передается в параметр строки запроса. Можно передать функцию, которая не принимает параметров, например:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


При передаче функции с параметрами все аргументы после предложения `where` привязаны к параметрам функции в заданном порядке. Любые объекты, которые поступают из-за пределов области действия функции, ДОЛЖНЫ быть переданы в качестве параметров. Функция не может записывать любые внешние переменные. В двух следующих примерах аргумент david привязан к параметру `name`. Кроме того, в первом примере аргумент meidum также привязан к параметру `level`. Также заметим, что эта функция должна состоять из одной инструкции `return` с поддерживаемым выражением, например:

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

Поэтому пока мы следуем правилам, мы можем добавить в наши запросы баз данных более сложные фильтры, например:

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

Инструкцию `where` можно объединить с инструкциями `orderBy`, `take` и `skip`: подробности приведены в следующем разделе.

### <a name="sorting"></a>Практическое руководство. Сортировка возвращаемых данных

В следующем коде показано, как сортировать данные, включив в запрос функцию `orderBy` или `orderByDescending` Он возвращает элементы таблицы `todoItemTable`, упорядочивая их по возрастанию значений в поле `text`. По умолчанию сервер возвращает только первые 50 элементов.

> [AZURE.NOTE]Чтобы предотвратить возврат всех элементов, по умолчанию используется размер страницы, управляемый сервером. Это предотвращает негативное воздействие больших наборов данных на функционирование службы. Можно увеличить количество возвращаемых элементов путем вызова `take`, как описано в следующем разделе. `todoItemTable` — это ссылка на созданную ранее таблицу мобильной службы.

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>Практическое руководство. Возврат данных на страницах

По умолчанию мобильные службы возвращают только 50 строк в указанном запросе, если клиент не запросит явное получение дополнительных данных в ответе. В следующем коде показано, как реализовать подкачку в возвращаемых данных с помощью предложений `take` и `skip` в запросе. При выполнении следующего запроса будут возвращены три главных элемента в таблице.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Обратите внимание, что в универсальном коде ресурса (URI) запроса метод `take(3)` преобразован в параметр запроса `$top=3`.

Следующий измененный запрос пропускает первые три результата и после этого возвращает следующие три. Это фактически вторая "страница" данных, где размер страницы составляет три элемента.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

И снова вы можете просмотреть URI запроса, отправленного в мобильную службу. Обратите внимание, что в универсальном коде ресурса (URI) запроса метод `skip(3)` преобразован в параметр запроса `$skip=3`.

Это упрощенный сценарий передачи жестко запрограммированных значений подкачки в функции `take` и `skip`. В реальных приложениях можно использовать запросы, подобные вышеуказанным с постраничным навигатором или с другим совместимым пользовательским интерфейсом, позволяющим переходить на следующую страницу или возвращаться к предыдущей странице.

### <a name="selecting"></a>Практическое руководство. Выбор определенных столбцов

Добавив в запрос предложение `select`, можно задать набор свойств, который будет включен в возвращаемые результаты. Например, следующий код возвращает свойства `id`, `complete` и `text` из каждой строки таблицы `todoItemTable`:

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

Здесь параметры функции select являются именами столбцов таблицы, которые требуется вернуть.


Все описанные функции являются суммируемыми, поэтому каждая последующая вызываемая функция будет продолжать влиять на запрос. Еще один пример:

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>Практическое руководство. Поиск данных по идентификатору

Функция `lookup` принимает только значение `id` и возвращает объект из базы данных с этим идентификатором. Таблицы базы данных создаются со столбцом целочисленных или строковых значений `id`. По умолчанию используется столбец `id` со строковыми значениями.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Выполнение операции запроса OData

Для создания и выполнения запросов REST мобильные службы используют соглашения об универсальных кодах ресурса (URI) запросов OData. С помощью встроенных функций запросов невозможно создать некоторые запросы OData, особенно такие сложные операции фильтрации, как поиск подстроки в свойстве. Для таких сложных запросов можно передать строку параметра какого-либо действительного запроса OData в функцию `read`, как показано ниже.

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]При передаче строки с параметром необработанного запроса OData в функцию `read` в этом запросе невозможно использовать методы создания запросов. В этом случае необходимо создать весь запрос в виде строки запроса OData. Дополнительную информацию о параметрах системы запросов OData см. в разделе [справочнике по параметрам системных запросов OData].

##<a name="inserting"></a>Практическое руководство. Вставка данных в мобильную службу

В следующем коде показано, как вставить новые строки в таблицу. Клиент запрашивает вставку строки данных путем отправки запроса POST в мобильную службу. Текст запроса содержит данные, которые вставлены в качестве объекта JSON.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

Это приводит к вставке в таблицу данных из предоставленного объекта JSON. Можно также указать функцию обратного вызова, которая будет вызываться после завершения вставки:

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

###Работа со значениями идентификаторов

Мобильные службы поддерживают уникальные настраиваемые строковые значения для столбца **id** таблицы. Это позволяет приложениям использовать в качестве идентификатора настраиваемые значения, такие как адреса электронной почты или имена пользователей. Например, следующий код вставляет новый элемент в качестве объекта JSON, уникальный идентификатор которого представляет из себя адрес электронной почты:

	todoItemTable.insert({
	   id: "myemail@domain.com",
	   text: "New Item",
	   complete: false
	});

Строковые идентификаторы предоставляют следующие преимущества.

+ Идентификаторы создаются без обмена данными с базой данных.
+ Можно легко объединять записи из разных таблиц или баз данных.
+ Значения идентификаторов можно удобно интегрировать с логикой приложения.

Если для вставленной записи не задано строковое значение идентификатора, мобильные службы создают уникальное значение для идентификатора. Чтобы узнать, как создавать собственные значения идентификатора в клиенте или на сервере .NET, см. раздел [Практическое руководство. Создание уникальных значений идентификатора](mobile-services-how-to-use-server-scripts.md#generate-guids).

Также для таблиц можно использовать целочисленные идентификаторы. Для этого необходимо создать таблицу с помощью команды `mobile table create`, используя параметр `--integerId`. Эта команда используется в интерфейсе командной строки (CLI) Azure. Дополнительные сведения об использовании CLI см. в статье [Интерфейс командной строки для управления таблицами мобильных служб](virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Практическое руководство. Изменение данных в мобильной службе

В следующем коде показано, как обновить данные в таблице. Клиент запрашивает обновление строки данных путем отправки запроса PATCH в мобильную службу. Текст запроса содержит определенные поля, которые должны быть обновлены в качестве объекта JSON. Он обновляет существующий элемент в таблице `todoItemTable`.

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	})

Первый параметр указывает экземпляр для обновления в таблице, который указан в идентификаторе.

Можно также указать функцию обратного вызова, которая будет вызываться после завершения обновления:

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="deleting"></a>Практическое руководство. Удаление данных в мобильной службе

В следующем коде показано, как удалить данные из таблицы. Клиент запрашивает удаления строки данных путем отправки запроса POST DELETE в мобильную службу. Это приводит к удалению существующих элементов в таблице todoItemTable.

	todoItemTable.del({
	   id: idToDelete
	})

Первый параметр указывает экземпляр для удаления в таблице, который указан в идентификаторе.

Можно также указать функцию обратного вызова, которая будет вызываться после завершения удаления:

	todoItemTable.del({
	   id: idToDelete
	}).done(function () {
	   /* Do something */
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="binding"></a>Практическое руководство. Отображение данных в пользовательском интерфейсе

В этом разделе показано, как отображать возвращенные объекты данных с использованием элементов пользовательского интерфейса. Чтобы запросить элементы таблицы `todoItemTable` и отобразить результаты в виде простого списка, можно выполнить следующий пример кода. Выделенные элементы отсутствуют, выполняется фильтрация и сортировка любого рода.

	var query = todoItemTable;

	query.read().then(function (todoItems) {
	   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
	   var listOfItems = document.getElementById('placeToInsert');
	   for (var i = 0; i < todoItems.length; i++) {
	      var li = document.createElement('li');
	      var div = document.createElement('div');
	      div.innerText = todoItems[i].text;
	      li.appendChild(div);
	      listOfItems.appendChild(li);
	   }
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

В приложении Магазина Windows результаты запроса можно использовать для создания объекта [WinJS.Binding.List], который можно привязать в качестве источника данных к объекту [ListView]. Дополнительные сведения см. в разделе [Привязка данных (приложения Магазина Windows с использованием JavaScript и HTML)].

##<a name="#custom-api"></a>Практическое руководство. Вызов настраиваемого интерфейса API

Настраиваемый интерфейс API позволяет определить пользовательские конечные точки, которые предоставляют функциональные возможности сервера, не сопоставляемые с операциями вставки, обновления, удаления или чтения. При использовании настраиваемого интерфейса API вы получаете больше возможностей для управления сообщениями, в том числе для чтения и установки заголовков HTTP-сообщений, а также определения форматов текста сообщений, отличных от JSON. Полный пример, который включает в себя создание пользовательского API в мобильной службе, см. в разделе [Вызов из клиента настраиваемого интерфейса API].

Настраиваемый интерфейс API можно вызвать путем вызова метода [invokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337) в **MobileServiceClient**. Например, следующая строка кода отправляет запрос POST интерфейсу API **completeAll** в мобильной службе:

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

 
Практические примеры и дополнительную информацию о методе **invokeApi** см. на [странице, посвященной настраиваемому интерфейсу API в пакетах SDK для клиента мобильных служб Azure](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

##<a name="caching"></a>Практическое руководство. Проверка подлинности пользователей

Мобильные службы поддерживают проверку подлинности и авторизацию пользователей с помощью различных внешних поставщиков идентификаторов: Facebook, Google, учетная запись Майкрософт и Twitter. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Приступая к работе с проверкой подлинности].

>[AZURE.NOTE]Если в приложении PhoneGap или Cordova используется проверка подлинности, также необходимо добавить в проект следующие подключаемые модули:
>
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git


Поддерживается два потока проверки подлинности: _серверный_ и _клиентский_. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

###Серверный поток
Чтобы мобильная служба могла выполнять процесс проверки подлинности в вашем приложении для магазина Windows или для приложения HTML 5, необходимо зарегистрировать приложение у поставщика удостоверений. Затем в вашей мобильной службе необходимо настроить код приложения и секретный код, предоставленный поставщиком. Дополнительные сведения см. в учебнике [Добавление проверки подлинности в приложение](mobile-services-html-get-started-users.md).

После регистрации у поставщика удостоверений просто вызовите [метод LoginAsync] с указанием значения [MobileServiceAuthenticationProvider] вашего поставщика. Например, для входа в систему через Facebook используйте следующий код.

	client.login("facebook").done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

Если используется поставщик удостоверений, отличный от Facebook, измените значение, передаваемое в метод `login` выше, на одно из следующих: `microsoftaccount`, `facebook`, `twitter`, `google` или `windowsazureactivedirectory`.

В этом случае мобильная служба управляет потоком проверки подлинности OAuth 2.0, отображая страницу входа выбранного поставщика и генерируя маркер проверки подлинности мобильных служб после успешного соединения с поставщиком удостоверений. Функция [login] после завершения работы возвращает объект JSON (**user**), который содержит как идентификатор пользователя, так и маркер проверки подлинности мобильных служб в полях **userId** и **authenticationToken** соответственно. Этот маркер можно кэшировать и повторно использовать до истечения срока его действия. Дополнительные сведения см. в разделе [Кэширование маркера проверки подлинности].

> [AZURE.NOTE]**Приложение Магазина Windows.** Если вы используете поставщик входа в учетные записи Майкрософт для проверки подлинности пользователей в приложении Магазина Windows, этот пакет приложения также необходимо зарегистрировать в мобильных службах. При регистрации сведений пакета приложений магазина Windows с помощью мобильных служб клиент сможет повторно использовать учетные данные для входа в учетную запись Майкрософт для осуществления единого входа. Если этого не сделать, пользователям входа учетной записи Майкрософт придется осуществлять вход в систему при каждом вызове метода входа в систему. Чтобы узнать, как зарегистрировать пакет приложения Магазина Windows, изучите документ [Регистрация пакета приложения Магазина Windows для проверки подлинности Майкрософт](/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). После регистрации данных пакета в мобильных службах вызовите метод [login](http://go.microsoft.com/fwlink/p/?LinkId=322050%20target="_blank"), установив для параметра <em>useSingleSignOn</em> значение **true**, чтобы повторно использовать учетные данные.

###Клиентский поток
Приложение может также независимо связаться с поставщиком удостоверений и указать возвращаемый маркер в мобильные службы для проверки подлинности. Этот клиентский поток позволяет пользователям выполнять единый вход или получать дополнительные данные о пользователе от поставщика удостоверений.

В следующем примере используется пакет SDK Live, поддерживающий единый вход в приложения Магазина Windows с использованием учетной записи Майкрософт:

	WL.login({ scope: "wl.basic"}).then(function (result) {
	      client.login(
	            "microsoftaccount",
	            {"authenticationToken": result.session.authentication_token})
	      .done(function(results){
	            alert("You are now logged in as: " + results.userId);
	      },
	      function(error){
	            alert("Error: " + err);
	      });
	});

Этот упрощенный пример получает маркер из Live Connect, который предоставляется мобильным службам путем вызова функции [login]. Более полный пример использования учетной записи Майкрософт для обеспечения единого входа см. в разделе [Проверка подлинности приложения с помощью единого входа].

При использовании интерфейсов API Facebook или Google для проверки подлинности клиентов следует немного изменить пример.

	client.login(
	     "facebook",
	     {"access_token": token})
	.done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

В этом примере предполагается, что маркер, предоставленный соответствующим поставщиком SDK, сохраняется в переменной `token`. В настоящее время Twitter не может использоваться для проверки подлинности клиента.

###Кэширование маркера проверки подлинности
В некоторых случаях вызова способа входа в систему можно избежать первого выполнения проверки подлинности пользователя. Мы можем использовать [sessionStorage] или [localStorage] для кэширования удостоверения текущего пользователя при первом входе, а затем будем проверять, содержится ли удостоверение этого пользователя в кэше. Если кэш является пустым или вызовы завершаются со сбоем (то есть истекло время текущего сеанса входа), нам необходимо пройти процедуру входа.

    // After logging in
    sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

    // Log in
    if (sessionStorage.loggedInUser) {
       client.currentUser = JSON.parse(sessionStorage.loggedInUser);
    } else {
       // Regular login flow
   }

     // Log out
    client.logout();
    sessionStorage.loggedInUser = null;

##<a name="push-notifications"></a>Практическое руководство. Регистрация для получения push-уведомлений

Если приложение является приложением PhoneGap или Apache Cordova на основе HTML либо JavaScript, собственная мобильная платформа обеспечивает получение push-уведомлений на устройстве. [Подключаемый модуль Apache Cordova для мобильных служб Azure](https://github.com/Azure/azure-mobile-services-cordova) позволяет выполнить регистрацию для получения push-уведомлений с помощью концентраторов уведомлений Azure. Служба, используемая для получения уведомлений, определяется собственной платформой устройства, в которой выполняется код. Практический пример см. в [статье об использовании службы Microsoft Azure для отправки уведомлений в приложения Cordova](https://github.com/Azure/mobile-services-samples/tree/master/CordovaNotificationsArticle).

>[AZURE.NOTE]Этот подключаемый модуль в настоящее время поддерживает только устройства под управлением iOS и Android. Решение, также включающее устройства Windows, описано в [статье об отправке уведомлений в приложения PhoneGap путем интеграции концентраторов уведомлений](http://blogs.msdn.com/b/azuremobile/archive/2014/06/17/push-notifications-to-phonegap-apps-using-notification-hubs-integration.aspx).

##<a name="errors"></a>Практическое руководство. Обработка ошибок

Существует несколько способов выявления, проверки и устранения ошибок в мобильных службах.

Например, серверные сценарии зарегистрированы в мобильной службе и могут использоваться для выполнения различных операций со вставляемыми и обновляемыми данными, включая проверку и изменение данных. Процесс определения и регистрации серверного сценария для проверки и изменения данных можно представить следующим образом:

	function insert(item, user, request) {
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
	      request.execute();
	   }
	}

Серверный сценарий проверяет длину строки данных, отправленных в мобильную службу, и отклоняет слишком длинные строки; в этом случае отклоняются строки длиной более 10 знаков.

Теперь, когда мобильная служба выполняет проверку данных и отправляет сообщения об ошибках на стороне сервера, можно обновить приложение HTML для обработки сообщений об ошибках, полученных при выполнении проверки.

	todoItemTable.insert({
	   text: itemText,
	   complete: false
	})
	   .then(function (results) {
	   alert(JSON.stringify(results));
	}, function (error) {
	   alert(JSON.parse(error.request.responseText).error);
	});


Далее можно передать обработчик ошибок в качестве второго аргумента при каждом доступе к данным:

	function handleError(message) {
	   if (window.console && window.console.error) {
	      window.console.error(message);
	   }
	}

	client.getTable("tablename").read()
		.then(function (data) { /* do something */ }, handleError);

##<a name="promises"></a>Практическое руководство. Использование обещаний

Обещания предоставляют механизм планирования выполнения работы со значением, которое еще не было вычислено. Это абстракция для управления взаимодействиями с асинхронными интерфейсами API.

Обещание `done` выполняется, как только предоставленная ему функция завершается как успешно, так и с ошибкой. В отличие от обещания `then` гарантируется возникновение любой ошибки, не обработанной в функции, а после выполнения обработчиков эта функция завершается любой ошибкой, возвращенной в качестве обещания в состоянии ошибки. Дополнительные сведения см. в описании [done].

	promise.done(onComplete, onError);

Следующим образом:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Обещание `then` не отличается от обещания `done`, однако в отличие от обещания `then`, обещание `done` гарантирует вызов ошибки, которая не обрабатывается в функции. Если не предоставить обработчик ошибок для обещания `then`, а операция содержит ошибку, исключение не вызывается, но возвращает обещание в состоянии ошибки. Дополнительные сведения см. в описании [then].

	promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Следующим образом:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Обещания можно использовать различными способами. Можно выстраивать цепочки операций обещаний путем вызова `then` или `done` для обещания, возвращенного предыдущей функцией `then`. Используйте `then` для промежуточного этапа операции (например, `.then().then()`), а `done` — для окончательного этапа операции (например, `.then().then().done()`). Можно связать несколько функций `then`, так как `then` возвращает обещание. Невозможно связывать более одного метода `done`, так как возвращается неопределенное значение. [Дополнительные сведения о различиях между then и done].

	todoItemTable.insert({
	   text: "foo"
	}).then(function (inserted) {
	   inserted.newField = 123;
	   return todoItemTable.update(inserted);
	}).done(function (insertedAndUpdated) {
	   alert(JSON.stringify(insertedAndUpdated));
	})

##<a name="customizing"></a>Практическое руководство. Настройка заголовков запроса клиента

Пользовательские заголовки запросов можно отправлять с помощью функции `withFilter`, считывая и записывая необязательные свойства запроса, которые будут отправлены в рамках фильтра. Может понадобиться добавить подобный пользовательский заголовок HTTP, если он будет востребован сценарием на стороне сервера или сможет улучшить работу этого сценария.

	var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
	   .withFilter(function (request, next, callback) {
	   request.headers.MyCustomHttpHeader = "Some value";
	   next(request, callback);
	});

Фильтры используются гораздо шире, чем настройка заголовков запросов. Они могут использоваться для изучения или изменения запросов, изучения или изменения ответов, обхода сетевых вызовов, отправки нескольких вызовов и т. д.

##<a name="hostnames"></a>Практическое руководство. Общий доступ к ресурсам независимо от источника

Чтобы управлять тем, каким веб-сайтам разрешено взаимодействовать с мобильной службой и отправлять в нее запросы, необходимо добавить имя узла веб-сайта, на котором размещена мобильная служба, в список общего доступа к ресурсам независимо от источника (CORS). Если используется серверная мобильная служба JavaScript, список разрешений можно настроить на вкладке "Настройка" на [портале управления Azure](https://manage.windowsazure.com). При необходимости можно использовать подстановочные знаки. По умолчанию новые мобильные службы указывают браузерам разрешать доступ только с узла `localhost`, а общий доступ к ресурсам независимо от источника (CORS) позволяет коду JavaScript работать в браузере по внешнему имени узла для взаимодействия с вашей мобильной службой. Эта конфигурация не является необходимой для приложений WinJS.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Display data in the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #caching
[How to: Handle errors]: #errors
[How to: Use promises]: #promises
[How to: Customize request headers]: #customizing
[How to: Use cross-origin resource sharing]: #hostnames
[Next steps]: #nextsteps
[Execute an OData query operation]: #odata-query



<!-- URLs. -->
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Дополнительные сведения о различиях между then и done]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[how to handle errors in promises]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Привязка данных (приложения Магазина Windows с использованием JavaScript и HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[login]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L301
[Проверка подлинности приложения с помощью единого входа]: mobile-services-windows-store-javascript-single-sign-on.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[справочнике по параметрам системных запросов OData]: http://go.microsoft.com/fwlink/p/?LinkId=444502
[Вызов из клиента настраиваемого интерфейса API]: mobile-services-html-call-custom-api.md

<!--HONumber=54-->