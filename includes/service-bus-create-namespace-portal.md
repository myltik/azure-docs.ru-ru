1. Войдите на классический портал Azure.

2. На портале в области навигации слева щелкните элемент **Служебная шина**.

3. На портале на панели внизу нажмите кнопку **Создать**.

    ![Нажмите кнопку "Создать"][select-create]
   
4. В диалоговом окне**Добавление нового пространства имен** введите имя пространства имен. Система немедленно проверяет, доступно ли оно.

    ![Имя пространства имен][namespace-name]
  
5. Проверив доступность имени пространства имен, выберите страну или регион, где должно размещаться это пространство имен.

6. Оставьте в остальных полях диалогового окна значения по умолчанию (**Обмен сообщениями** и **Уровень Standard**), а затем щелкните значок галочки (кнопка «ОК»). Теперь система создает пространство имен и включает его. Вероятно, придется подождать несколько минут, пока система не выделит ресурсы для вашей учетной записи.
 
    ![Успешно создано][created-successfully]

###Получение учетных данных
1. В левой области навигации щелкните узел **Служебная шина**, чтобы отобразить список доступных пространств имен.
 
    ![Выберите служебную шину][select-service-bus]
  
2. Выберите пространство имен, которое вы только что создали из появившегося списка:
 
    ![Выберите пространство имен][select-namespace]
 
3. Нажмите кнопку **Сведения о подключении**

    ![Сведения о подключении][connection-information]
  
4. В области **Сведения о доступе к подключению** найдите строку подключения, которая содержит ключ SAS и имя ключа.

    ![Сведения о доступе к подключению][access-connection-information]
  
5. Запишите ключ или скопируйте его в буфер обмена.

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0615_2016-->