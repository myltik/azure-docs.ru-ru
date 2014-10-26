Затем нужно изменить время регистрации уведомлений, чтобы аутентификация пользователя проводилась до попытки регистрации.

1.  В обозревателе пакетов в Eclipse откройте файл ToDoActivity.java и найдите метод `onCreate`. Переместите следующий код из метода `onCreate` в начало метода `createTable`.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Метод `createTable` вызывается после завершения метода`authenticate`. Весь метод `createTable` должен выглядеть следующим образом.

        private void createTable() {

            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);

            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);

            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }   


