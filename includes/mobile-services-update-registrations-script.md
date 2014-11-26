1.  На портале управления щелкните вкладку **Данные** и затем щелкните таблицу **Регистрации**.

    ![][0]

2.  В таблице **Регистрации** щелкните вкладку **Скрипт** и выберите команду **Вставить**.

    ![][1]

    Это действие отображает функцию, которая вызывается при выполнении вставки в таблице **Регистрации**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

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

  [0]: ./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png
  [1]: ./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png
