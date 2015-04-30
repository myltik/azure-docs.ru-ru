Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите в расположение, где сохранены сжатые файлы проекта, и извлеките файлы на компьютере в каталог проектов Android Studio.

2. Откройте Android Studio. Если вы работаете с некоторым проектом и он отображается, закройте этот проект ("Файл" = > "Закрыть проект").

3. Выберите **Открыть существующий проект Android Studio**, перейдите к папке с проектом и нажмите кнопку **OK.** 

 	![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Убедитесь, что в левом окне **обозревателя проектов** открыта папка *Project*, а затем откройте **app**, **src**, **java** и дважды щелкните элемент **ToDoactivity**,

   ![](./media/mobile-services-android-get-started/Android-Studio-quickstart.png)


5. Если вы загрузили пакет SDK версии 2.0, необходимо обновить код URL-адресом и ключом вашей мобильной службы:
	- 	Найдите метод **OnCreate** в **TodoActivity.java** и найдите код, который создает экземпляр клиента мобильных служб. Код отображается на предыдущем изображении.
	- 	Замените "MobileServiceUrl" фактическим URL-адресом вашей мобильной службы.
	- 	Замените "AppKey" ключом вашей мобильной службы.
	- 	Для получения дополнительных сведений обратитесь к учебнику [Добавление мобильных служб к существующему приложению](../articles/mobile-services-android-get-started-data.md). 

6. Для запуска проекта в эмуляторе Android в меню **Запуск** щелкните **Выполнить**.

	> [AZURE.IMPORTANT] Чтобы запустить проект в эмуляторе Android, необходимо определить как минимум одно виртуальное устройство Android (AVD). Для создания этих устройств и управления ими используйте диспетчер AVD.

7. Введите содержательный текст в приложении, например _Завершить работу с учебником_, и нажмите кнопку **Добавить**.

   ![][10]

   	При этом в новую мобильную службу, размещенную в Azure, будет отправлен запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

	> [AZURE.NOTE] Код, который обращается к вашей мобильной службе для запроса и вставки данных, можно просмотреть в файле ToDoActivity.java.

8. На портале управления перейдите на вкладку **Данные**, а затем щелкните таблицу **TodoItems**.

   ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

   	Это позволяет просматривать данные, добавленные в таблицу приложением.

   ![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-android-get-started/Android-Studio-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/android-studio-import-project.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Добавление мобильных служб к существующему приложению]: ../articles/mobile-services-android-get-started-data.md
[Приступая к работе с аутентификацией]: ../articles/mobile-services-android-get-started-users.md
[Приступая к работе с push-уведомлениями]: ../articles/mobile-services-javascript-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Пакет Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Портал управления]: https://manage.windowsazure.com/

<!--HONumber=52-->