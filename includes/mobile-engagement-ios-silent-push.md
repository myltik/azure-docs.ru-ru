> [AZURE.IMPORTANT]Чтобы получать push-уведомления от Mobile Engagement, необходимо включить в приложении `Silent Remote Notifications`. Для этого требуется добавить значение remote-notification в массив UIBackgroundModes в файле info.plist.

1. Откройте файл `info.plist` в проекте.
2. Щелкните правой кнопкой мыши верхний элемент в списке (`Information Property List`) и добавьте новую строку.

	![][1]

3. В новой строке введите: `Required background modes`.

	![][2]

4. Щелкните стрелку влево, чтобы развернуть строку.
5. Добавьте следующее значение в элемент 0 `App downloads content in response to push notifications`.

	![][3]

После внесения изменений XML-файл info.plist должен содержать следующий ключ и значение:

    <key>UIBackgroundModes</key>
        <array>
            <string>remote-notification</string>
        </array>
    ...
    
6. Если вы используете Xcode 7 и iOS 9, вам необходимо выполнить следующие дополнительные шаги: • Установите для параметра **Включить Bitcode** значение **Нет** (в разделе "Конечные объекты" > "Параметры построения" > установите параметру "Включить Bitcode" значение "Да" или "Нет". Убедитесь, что в верхней строке выбраны ВСЕ элементы.) • Включите **Push-уведомления** в разделе "Конечные объекты" > "Имя вашего целевого объекта" > "Возможности".

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png
[2]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png
[3]: ./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png

<!---HONumber=Sept15_HO3-->