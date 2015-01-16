

1.  В файле скрипта default.js непосредственно под строкой кода, которая определяет список todoItems, добавьте следующее определение функции:

        // Add a filter that adds a header to prevent caching. This makes sure that the 
        // latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

    Здесь определяется функция фильтрации, которая добавляет заголовок `If-Modified-Since`, чтобы запретить кэширование в клиенте.

2.  Далее раскомментируйте или добавьте следующую строку кода и замените `<yourClient>` переменной, добавленной в файл service.js при подключении проекта к мобильной службе:

        var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   Этот код создает прокси-объект (**todoTable**) для новой таблицы базы данных с использованием фильтра кэширования.

3.  Замените функцию **InsertTodoItem** следующим кодом:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    Этот код вставляет новый элемент в таблицу.

4. Замените функцию **RefreshTodoItems** следующим кодом:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   Это установит привязку к коллекции элементов в таблицу todoTable, которая содержит все объекты **TodoItem**, возвращенные из мобильной службы.

5.  Замените функцию **UpdateCheckedTodoItem** следующим кодом:

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   В результате в мобильную службу будет отправлено обновление элемента.

Теперь, когда приложение обновлено для хранения данных на сервере с использованием мобильных служб, настало время протестировать приложение на работу с мобильными службами.

