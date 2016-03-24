##<a name="create-client"></a>Создание клиента

Создайте подключение клиента, создав объект `WindowsAzure.MobileServicesClient`. Замените `appUrl` URL-адресом в своем мобильном приложении.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Практическое руководство. Создание ссылки на таблицу

Для доступа к данным или их обновления создайте ссылку на таблицу серверной части. Замените `tableName` именем таблицы.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Практическое руководство. Запрос ссылки на таблицу

Ссылку на таблицу можно использовать для запроса данных на сервере. Запросы осуществляются на языке типа LINQ. Чтобы вернуть все данные из таблицы, используйте следующий код:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Выполненная функция вызывается с результатами. Не используйте в этой функции свойство `for (var i in results)` — оно вызывает перебор данных, включаемых в результаты применения других функций запросов (таких как `.includeTotalCount()`).

Дополнительные сведения о синтаксисе запроса см. в [документации по объектам запросов].

### Фильтрация данных на сервере

Используйте предложение `where` в ссылке на таблицу.

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Также можно использовать функцию фильтрации объектов. В этом случае переменной `this` присваивается текущий объект фильтрации. Представленный ниже код функционально эквивалентен предыдущему примеру:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Разбиение данных по страницам

Используйте методы take() и skip(). Например, если вы хотите разбить на строки таблицу, содержащую 100 записей:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Метод `.includeTotalCount()` добавляет в объект результатов поле totalCount. В этом поле указывается общее количество записей, которое будет возвращено, если данные не разбиты на страницы.

После этого можно настроить список страниц, используя переменную pages и определенные кнопки пользовательского интерфейса, и загрузить новые данные на каждой странице с помощью метода loadPage(). Чтобы ускорить доступ к уже загруженным записям, следует добавить кэширование.


###<a name="sorting-data"></a>Практическое руководство. Возврат отсортированных данных

Используйте методы запросов .orderBy() или .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Дополнительные сведения об объекте запроса см. в [документации по объектам запросов].

##<a name="inserting"></a>Практическое руководство. Вставка данных

Создайте объект JavaScript с соответствующей датой и асинхронно вызовите метод table.insert():

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Вставленный элемент возвращается с дополнительными полями, необходимыми для операций синхронизации. Обновите свой кэш, используя эти данные для последующих обновлений.

Обратите внимание на то, что серверный пакет SDK мобильных приложений Azure для Node.js поддерживает динамическую схему для целей разработки. При использовании динамической схемы схема таблицы обновляется на ходу, что позволяет добавлять в нее столбцы, просто указав их в операции вставки или обновления данных. Перед переносом приложения в рабочую среду динамическую схему рекомендуется отключить.

##<a name="modifying"></a>Практическое руководство. Изменение данных

Как и при использовании метода .insert(), в данном случае следует создать объект обновления и вызвать метод .update(). Объект обновления должен содержать идентификатор обновляемой записи обновления, получаемый при чтении записи или при вызове метода .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Практическое руководство. Удаление данных

Для удаления записи вызовите метод .del(). В ссылке на объект укажите идентификатор:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

<!-----HONumber=AcomDC_0309_2016-->
