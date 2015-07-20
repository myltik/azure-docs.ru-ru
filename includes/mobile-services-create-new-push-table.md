
1. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите свое приложение.

	![](./media/mobile-services-create-new-push-table/mobile-services-selection.png)

2. Щелкните вкладку **Данные**, а затем нажмите **Создать**.

	![](./media/mobile-services-create-new-push-table/mobile-create-table.png)

	Откроется диалоговое окно **Создание новой таблицы**.

3. Сохранив параметр по умолчанию **Все с ключом приложения** для всех разрешений, введите _Регистрации_ в поле **Имя таблицы** и нажмите кнопку "Проверить".

	![](./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png)

  При этом создается таблица **Регистрации**, где хранятся идентификаторы URI, используемые для отправки push-уведомлений.

Далее следует изменить приложение, чтобы включить push-уведомления.

<!-- URLs -->
[портал управления Azure]: https://manage.windowsazure.com/

<!---HONumber=July15_HO2-->