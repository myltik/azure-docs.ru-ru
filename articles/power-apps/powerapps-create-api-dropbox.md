<properties
	pageTitle="Добавление API Dropbox в службу PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Dropbox в среде службы приложений организации"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Создание API Dropbox в PowerApps Enterprise

> [AZURE.SELECTOR]
- [Приложения логики](../articles/connectors/connectors-create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

Добавьте API Dropbox в среду службы приложений (клиента) организации.

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**:  
![][12]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре:  
![][13]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**:  
![Переход к зарегистрированным API][4]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API:  
![Добавление API][5]

6. Введите описательное **имя** для API.
	
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Dropbox**:  
![выбор API Dropbox][6]

8. Выберите **Настройки — Настроить обязательные параметры**:  
![настройка параметров API Dropbox][7]

9. Введите **Ключ приложения** и **Секрет приложения** своего приложения Dropbox. Если приложения нет, см. раздел "Регистрация приложения Dropbox для использования с PowerApps" в этой статье, где описывается создание нужных значений ключа и секрета.

	> [AZURE.IMPORTANT]Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.

10. Нажмите кнопку **ОК**, чтобы завершить эти действия.


После их выполнения в среду службы приложений организации будет добавлен новый API Dropbox.


## (Необязательно.) Регистрация приложения Dropbox для использования с PowerApps

Если у вас нет приложения Dropbox с ключом и секретом, выполните следующие действия, чтобы создать приложение и получить нужные значения.

1. Войдите в [Dropbox][1] с помощью своей учетной записи.

2. Перейдите на сайт разработчика Dropbox и выберите **Мои приложения**:  
![сайт разработчика Dropbox][8]

3. Щелкните **Создать приложение**:  
![создание приложения Dropbox][9]

4. В окне **Создание нового приложения на платформе Dropbox**:

	1. В поле **Choose API** (Выбор API) выберите пункт **Dropbox API**.  
	2. В поле **Choose the type of access you need** (Выберите необходимый тип доступа) выберите пункт **Full Dropbox…** (Весь Dropbox…).  
	3. Введите имя своего приложения.  

	![создание приложения Dropbox, страница 1][10]

5. На странице настройки приложения:

	1. В разделе **OAuth 2** в качестве **URL-адреса перенаправления** укажите URL-адрес перенаправления, полученный при добавлении нового API Dropbox на портале Azure (в этой статье). Выберите **Добавить**.  
	2. Щелкните ссылку **Show** (Показать), чтобы показать **секрет приложения**.  

	![создание приложения Dropbox, страница 2][11]

Будет создано новое приложение Dropbox. Вы можете использовать это приложение при настройке API Dropbox на портале Azure.

## См. раздел "Интерфейсы REST API".

Справочник по [REST API Dropbox](../connectors/connectors-create-api-dropbox.md).


## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Dropbox в службу PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения:

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!----HONumber=AcomDC_0330_2016-->