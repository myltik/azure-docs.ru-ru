1. В файле insert.js для таблицы **каналов** найдите следующие строки кода, трансформируйте их в комментарий или удалите из файла, а затем сохраните изменения.

		sendNotifications(item.channelUri);

		function sendNotifications(uri) {
		    console.log("Uri: ", uri);
		    push.wns.sendToastText01(uri, {
		        text1: "Sample toast from sample insert"
		    }, {
		        success: function (pushResponse) {
		            console.log("Sent push:", pushResponse);
		        }
		    });
		}
		
	При сохранении изменений в файле insert.js, вашей мобильной службе передается новая версия скрипта.

2. В обозревателе сервера разверните таблицу TodoItem, откройте файл insert.js и замените текущую функцию вставки следующим кодом, а затем сохраните изменения: 

		function insert(item, user, request) {
			request.execute({
				success: function() {
					request.respond();
					sendNotifications();
				}
			});
		
			function sendNotifications() {
				var channelsTable = tables.getTable('channels');
				channelsTable.read({
					success: function(devices) {
						devices.forEach(function(device) {
							push.wns.sendToastText04(device.channelUri, {
								text1: item.text
							}, {
								success: function(pushResponse) {
									console.log("Sent push:", pushResponse);
								}
							});
						});
					}
				});
			}
		}
		
	Теперь при вставке новых элементов TodoItem на все зарегистрированные устройства будет передаваться push-уведомление.

<!--HONumber=42-->
