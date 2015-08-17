
1. В Visual Studio щелкните правой кнопкой мыши проект Магазина Windows, выберите пункт **Назначить запускаемым проектом** и нажмите клавишу F5, чтобы запустить приложение Магазина Windows.
	
	После запуска приложения выполняется регистрация устройства для получения push-уведомлений.

2. Остановите приложение Магазина Windows и повторите предыдущий шаг, чтобы запустить приложение Магазина Windows Phone.

	Теперь оба устройства зарегистрированы для получения push-уведомлений.

3. Запустите приложение Магазина Windows и введите текст в поле **Insert a TodoItem** (Вставить TodoItem), после чего нажмите кнопку **Save** (Сохранить).

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push1.png)

   	Обратите внимание: после завершения вставки как приложение Магазина Windows, так и приложение Windows Phone получают push-уведомление из WNS.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push2.png)

	Уведомление отображается на устройстве Windows Phone, даже если приложение не запущено.

   	![](./media/mobile-services-javascript-backend-windows-universal-test-push/mobile-quickstart-push5-wp8.png)

<!---HONumber=August15_HO6-->