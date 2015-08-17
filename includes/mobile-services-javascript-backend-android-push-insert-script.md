
1. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 
 
2. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.
   
   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	При этом регистрируется новый скрипт вставки, в котором [объект gcm](http://go.microsoft.com/fwlink/p/?LinkId=282645) используется для отправки push-уведомлений на все зарегистрированные устройства после успешной вставки данных.

<!---HONumber=August15_HO6-->