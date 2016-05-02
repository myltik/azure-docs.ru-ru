<properties
	pageTitle="Учебник. Создание пользовательского API с помощью Azure Resource Manager в PowerApps и логических потоках | Microsoft Azure"
	description="Учебник по созданию пользовательского API с помощью Azure Resource Manager в PowerApps и логических потоках"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>


# Учебник. Создание пользовательского API ARM с защитой AAD для PowerApps и логических потоков 

Этот учебник поможет выполнить действия, необходимые для регистрации файла Swagger с описанием [API ARM][6] и последующего подключения к пользовательскому API в PowerApps.

## Что необходимо для начала работы

- Подписка Azure
- Учетная запись PowerApps

## Включение проверки подлинности в Azure Active Directory

Сначала необходимо создать приложение Azure Active Directory (AAD), которое выполняет проверку подлинности при вызове конечной точки API ARM.

1. Чтобы создать приложение AAD, войдите в вашу [подписку Azure][7] и перейдите к **Active Directory**: ![](./media/powerapps-azure-resource-manager-tutorial/azureaad.png "Azure AAD")  

2. На этой странице выберите каталог, в котором вы хотите создать свое приложение AAD. Выберите каталог, перейдите на вкладку **Приложения** и выберите **Добавить**: ![](./media/powerapps-azure-resource-manager-tutorial/azureapplication.png "Приложение Azure")

3. Введите понятное имя для приложения, выберите **Веб-приложение и/или веб-API** и нажмите кнопку **Далее**: ![](./media/powerapps-azure-resource-manager-tutorial/newapplication.png "Новое приложение")

4. В поле **URL-адрес входа** введите: **http://login.windows.net*. В поле **URI идентификатора приложения** введите любой уникальный URI. Затем выберите **Готово**: ![](./media/powerapps-azure-resource-manager-tutorial/newapplication2.png "Второе новое приложение")

5. В созданном приложении AAD перейдите на вкладку **Настройка**. На этой вкладке настройте разрешения для приложения.

6. В разделе **Разрешения для других приложений** выберите **Добавить приложение** и введите следующие разрешения: ![](./media/powerapps-azure-resource-manager-tutorial/permissions.png "Разрешения")

	Убедитесь, что вы делегировали необходимые разрешения для вашего приложения: ![](./media/powerapps-azure-resource-manager-tutorial/permissions2.png "Делегирование разрешений")

7. В разделе **ключи** выберите любую длительность. **Скопируйте и сохраните ключ в безопасном месте**; он понадобится позже. Также запишите __идентификатор клиента__: ![](./media/powerapps-azure-resource-manager-tutorial/configurekeys.png "Настройка ключей")

8. В разделе **Единый вход** введите следующий URL-адрес в поле __URL-адрес ответа__: https://msmanaged-na.consent.azure-apim.net/redirect: ![](./media/powerapps-azure-resource-manager-tutorial/redirecturl.png "URL-адрес перенаправления")

9. **Сохраните** изменения. **Скопируйте и сохраните ключ в безопасном месте**.

## Добавление подключения в PowerApps или логические потоки

Теперь, когда приложение AAD настроено, добавим пользовательский API.

1. Откройте [веб-портал PowerApps][1], перейдите на вкладку **Подключения** и выберите __Добавить подключение__ в правом верхнем углу: ![](./media/powerapps-azure-resource-manager-tutorial/createnewconnection.png "Создание пользовательского интерфейса API")  

2. Выберите __Добавить пользовательский API__: ![](./media/powerapps-azure-resource-manager-tutorial/connecttocustomapi.png "Создание пользовательского интерфейса API")

3. Отправьте файл Swagger ARM, который доступен [для скачивания][8]\: ![](./media/powerapps-azure-resource-manager-tutorial/createcustom.png "Создание пользовательского интерфейса API")

4. На следующем экране будет указано, что в нашем файле Swagger используется проверка подлинности AAD. Введите идентификатор клиента AAD, секрет клиента (**ключ**, который вы сохранили в безопасном месте) и другие параметры: ![](./media/powerapps-azure-resource-manager-tutorial/oauthsettings.png "Параметры OAuth")

5. Если все настроено правильно, вы сможете воспользоваться интерфейсом API ARM, создав подключение и указав этот API при создании PowerApp или логического потока: ![](./media/powerapps-azure-resource-manager-tutorial/createdcustomapi.png "Пользовательский интерфейс API создан")

Аналогичным образом можно получить доступ к любым данным, которые предоставляются с помощью интерфейсов API RESTful и проверки подлинности с использованием AAD OAuth2.

Более подробное описание действий по созданию PowerApps и логических потоков см. в следующих разделах:

- [Подключение к Office 365, Twitter и Microsoft Translator][5]
- [Отображение данных из Office 365][4]
- [Создание приложения из данных][3]
- [Приступая к работе с логическими потоками][2]

Свои вопросы или комментарии о пользовательских интерфейсах API отправляйте по электронной почте [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).


<!--Reference links in article-->
[1]: https://web.powerapps.com
[2]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[3]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[4]: https://powerapps.microsoft.com/tutorials/show-office-data/
[5]: https://powerapps.microsoft.com/tutorials/powerapps-api-functions/
[6]: https://msdn.microsoft.com/library/azure/dn790568.aspx
[7]: https://manage.windowsazure.com
[8]: http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json

<!---HONumber=AcomDC_0420_2016-->