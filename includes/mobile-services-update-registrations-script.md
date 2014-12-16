

1. На портале управления щелкните вкладку **Данные**, а затем таблицу **Registration**. 

	![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. В таблице **Registration** откройте вкладку **Скрипт** и выберите команду **Вставить**.
   
	![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и щелкните **Сохранить**:

		function insert(item, user, request) {
			var registrationTable = tables.getTable('Registrations');
			registrationTable
				.where({ handle: item.handle })
				.read({ success: insertChannelIfNotFound });
	        function insertChannelIfNotFound(existingRegistrations) {
        	    if (existingRegistrations.length > 0) {
            	    request.respond(200, existingRegistrations[0]);
        	    } else {
            	    request.execute();
        	    }
    	    }
	    }

   Это регистрирует новый скрипт вставки, который хранит сведения о регистрации в новой таблице.

