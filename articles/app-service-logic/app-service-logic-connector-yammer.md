<properties
   pageTitle="Приложение API соединителя Yammer"
   description="Использование соединителя Yammer"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/19/2015"
   ms.author="sameerch"/>


# Использование соединителя Yammer в приложении логики #

Приложения логики могут запускаться на основе разных источников данных и предлагать соединители для получения и обработки данных в рамках потока.

Соединитель Yammer позволяет подключаться к Yammer и выполнять действие отправки сообщения и триггер для получения нового сообщения.

## Создание соединителя Yammer для приложения логики ##
Чтобы использовать соединитель Yammer, сначала нужно создать экземпляр приложения API соединителя Yammer. Это можно сделать следующим образом.

1.	Откройте Azure Marketplace, нажав кнопку "+ СОЗДАТЬ" в нижнем левом углу портала Azure.
2.	Перейдите в раздел "Мобильные и веб-приложения > Приложения API" и выполните поиск элемента "Соединитель Yammer".
3.	Настройте соединитель Yammer следующим образом.

 ![][1]

	- **Location** - choose the geographic location where you would like the connector to be deployed
	- **Subscription** - choose a subscription you want this connector to be created in
	- **Resource group** - select or create a resource group where the connector should reside
	- **App Service plan** - select or create a web hosting plan
	- **Pricing tier** - choose a pricing tier for the connector
	- **Name** - give a name for your Yammer Connector

4.	Нажмите кнопку "Создать". Будет создан новый соединитель Yammer.
5.	После создания экземпляра приложения API можно создать логику приложения в той же группе ресурсов для использования соединителя Yammer.

## Использование соединителя Yammer в приложении логики ##
После создания приложения API можно использовать соединитель Yammer как триггер или действие для приложения логики. Для этого необходимо выполнить следующие действия.

1.	Создайте новое приложение логики и выберите ту же группу ресурсов, что и для соединителя Yammer.

![][2]

2.	Открыть раздел "Триггеры и действия", чтобы открыть конструктор приложения логики и настроить поток.

![][3]

3.	Соединитель Yammer отображается в разделе "Приложения API в этой группе ресурсов" в коллекции с правой стороны.

![][4]

4. Можно удалить приложение API соединителя Yammer в редакторе, щелкнув "Соединитель Yammer". Нажмите кнопку "Авторизовать". Укажите учетные данные Yammer. Нажмите кнопку "Разрешить".

![][5]

![][6]

![][7]

Теперь можно использовать соединитель Yammer в потоке.

## Использование соединителя Yammer в качестве триггера

1.	 Теперь вы можете использовать новое сообщение, извлеченное из триггера Yammer ("Новое сообщение"), в других действиях в потоке. Настройте входные свойства для триггера Yammer следующим образом.

	- **Идентификатор группы** — идентификатор группы, от которой должно быть получено новое сообщение. Если не указан идентификатор группы, сообщение будет извлечено из следующего канала. Идентификатор группы может быть извлечен из URL-адреса группы в Yammer. Например, идентификатор группы в следующем URL-адресе — 5453203 https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203

	![][8]

	![][9]

## Соединитель Yammer позволяет отправлять сообщения

6.	Также соединитель Yammer можно использовать в качестве действия в приложениях логики. Укажите триггер для приложения логики или см. функцию "выполнить логику вручную" (показано ниже). Добавьте соединитель Yammer, укажите данные авторизации и выберите действие "Отправить сообщение". Настройте входные свойства для действия "Отправить сообщение" следующим образом.

	- **Текст сообщения** — текстовое содержимое отправляемого сообщения.
	- **Идентификатор группы** — укажите идентификатор группы, в которую следует добавлять новые сообщения. Если не указан идентификатор группы, сообщение будет отправлено в канал всей компании. Идентификатор группы может быть извлечен из URL-адреса группы в Yammer. Например, идентификатор группы в следующем URL-адресе — 5453203 https://www.yammer.com/microsoft.com/#/threads/inGroup?type=in_group&feedId=5453203
	- 	**Отметить пользователей** — массив, содержащий сетевые имена сети, которые должны быть отмечены в сообщении.

	![][10]

	![][11]

## Дополнительные возможности соединителя
После создания соединителя его можно добавить в рабочий бизнес-процесс с помощью приложения логики. См. раздел [Что такое приложения логики?](app-service-logic-what-are-logic-apps.md).

Справку по API REST Swagger см. в статье [Справочные материалы по соединителям и приложениям API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Можно также просматривать статистику производительности и управлять безопасностью соединителя. См. статью [Управление встроенными приложениями API и соединителями, а также их мониторинг](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-yammer/img1.PNG
[2]: ./media/app-service-logic-connector-yammer/img2.PNG
[3]: ./media/app-service-logic-connector-yammer/img3.png
[4]: ./media/app-service-logic-connector-yammer/img4.png
[5]: ./media/app-service-logic-connector-yammer/img5.PNG
[6]: ./media/app-service-logic-connector-yammer/img6.PNG
[7]: ./media/app-service-logic-connector-yammer/img7.png
[8]: ./media/app-service-logic-connector-yammer/img8.PNG
[9]: ./media/app-service-logic-connector-yammer/img9.PNG
[10]: ./media/app-service-logic-connector-yammer/img10.PNG
[11]: ./media/app-service-logic-connector-yammer/img11.PNG

<!---HONumber=August15_HO8-->