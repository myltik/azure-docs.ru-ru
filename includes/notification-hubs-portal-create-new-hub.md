

1. Войдите на [портал Azure](https://portal.azure.com)и щелкните **+Создать** в левой верхней части экрана.
2. Щелкните **Создать** и **Интернет+мобильные устройства**. При необходимости прокрутите вниз и щелкните **Концентратор уведомлений**.
   
       ![Azure Portal - Create Notification Hubs](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
3. Обязательно укажите уникальное имя в поле **Центр уведомлений** . Выберите нужный **регион**, **подписку** и **группу ресурсов** (если она уже создана). 
   
    Если у вас уже есть пространство имен служебной шины, в котором вы хотите создать центр уведомлений, выберите его с помощью варианта **Выбрать существующее** в поле **Пространство имен**.  В противном случае можно использовать имя по умолчанию, которое будет создано на основе имени центра уведомлений, при условии, что имя пространства имен доступно. 
   
    Когда все будет готово, нажмите кнопку **Создать**.
   
       ![Azure Portal - Set notification hub properties](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. После создания пространства имен и центра уведомлений вы будете перенаправлены на соответствующую страницу портала. 
   
       ![Azure Portal - Notification hub portal page](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)
5. Щелкните **Параметры**, а затем — **Политики доступа**. Запишите две строки подключения, доступные для вас, — они понадобятся позже для обработки push-уведомлений.
   
       ![Azure Portal - Notification hub connection strings](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)



<!--HONumber=Nov16_HO2-->


