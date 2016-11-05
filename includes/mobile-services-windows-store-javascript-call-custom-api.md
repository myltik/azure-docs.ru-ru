
## <a name="update-app"></a>Обновление приложения для вызова настраиваемого интерфейса API
1. В Visual Studio откройте файл default.html в своем проекте быстрого запуска, найдите элемент **button** с именем `buttonRefresh` и добавьте следующий новый элемент сразу после него: 
   
        <button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>
   
    Это приведет к добавлению на страницу новой кнопки.
2. Откройте файл кода default.js в папке проекта `js`, найдите функцию **refreshTodoItems** и убедитесь в том, что эта функция содержит следующий код:
   
        todoTable.where({ complete: false })
           .read()
           .done(function (results) {
               todoItems = new WinJS.Binding.List(results);
               listItems.winControl.itemDataSource = todoItems.dataSource;
           });            
   
    Это приводит к фильтрации элементов, так что завершенные элементы не возвращаются в результатах запроса.
3. После функции **refreshTodoItems** добавьте следующий код:
   
        var completeAllTodoItems = function () {
            var okCommand = new Windows.UI.Popups.UICommand("OK");
   
            // Asynchronously call the custom API using the POST method. 
            mobileService.invokeApi("completeall", {
                body: null,
                method: "post"
            }).done(function (results) {
                var message = results.result.count + " item(s) marked as complete.";
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done(function () {
                    refreshTodoItems();
                });
            }, function (error) {
                var dialog = new Windows.UI.Popups
                    .MessageDialog(error.message);
                dialog.commands.append(okCommand);
                dialog.showAsync().done();
            });
        };
   
        buttonCompleteAll.addEventListener("click", function () {
            completeAllTodoItems();
        });
   
    Этот метод обрабатывает событие **Click** для новой кнопки. Метод **InvokeApiAsync** вызывается на стороне клиента, что приводит к отправке запроса POST новому настраиваемому интерфейсу API. Результат, возвращаемый настраиваемым интерфейсом API, отображается в диалоговом окне сообщения, как и любые ошибки.

## <a name="test-app"></a>Тестирование приложения
1. В Visual Studio нажмите клавишу **F5**, чтобы повторить сборку проекта, после чего запустите приложение.
2. В приложении введите какой-либо текст в поле **Вставить в TodoItem**, а затем нажмите кнопку **Сохранить**
3. Повторяйте предыдущий шаг, пока в список не будут добавлены несколько элементов списка дел.
4. Нажмите кнопку **Выполнить все**.
   
      ![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)
   
    Отображается диалоговое окно сообщения, в котором указывается количество элементов, помеченных как завершенные; снова выполняется отфильтрованный запрос, что приводит к удалению из списка всех элементов.

<!---HONumber=Oct15_HO3-->