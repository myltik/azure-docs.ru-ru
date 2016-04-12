<properties
	pageTitle="Добавление API Google Drive в службу PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Google Drive в среде службы приложений организации"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
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

# Создание API Google Диска в PowerApps Enterprise

> [AZURE.SELECTOR]
- [Приложения логики](../articles/connectors/connectors-create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

Добавьте API Google Диска в среду службы приложений (клиента) организации.

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**: ![][15]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][16]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][1]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][2]

6. Введите описательное **имя** для API.
	
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Google Drive**: ![выбор API Google Drive][3]

8. Выберите **Настройки — Настроить обязательные параметры**: ![настройка параметров API Google Drive][4]

9. Введите *Ключ приложения* и *Секрет приложения* для приложения Google Drive. Если приложения нет, см. раздел "Регистрация приложения Google Drive для использования с PowerApps" в этой статье, где описывается создание нужных значений ключа и секрета.

	> [AZURE.IMPORTANT] Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.

10. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений организации будет добавлен новый API Google Drive.


## (Необязательно.) Регистрация приложения Google Drive для использования с PowerApps

Если у вас нет приложения Google Drive с ключом и секретом, выполните следующие действия, чтобы создать приложение и получить нужные значения.

1. Войдите в [консоль разработчиков Google][5]\: ![консоль разработчиков Google][6]

2. Выберите команду **Создать пустой проект**.

3. Введите имя приложения, примите условия и нажмите кнопку **Создать**: ![создание нового проекта Google Drive][7]

4. После успешного создания нового проекта выберите **Использовать API Google**: ![использование API Google][8]

5. На странице "Обзор" щелкните **API Drive**: ![обзор API Google Drive][9]

6. Нажмите **Включить API**: ![включение API Google Drive][10]

7. После включения API Drive перейдите на вкладку **Учетные данные** и выберите **Идентификатор клиента OAuth 2.0**: ![Добавление учетных данных][12]

8. Щелкните **Настройка экрана согласия с условиями использования**.

9. На вкладке **Экран согласия с условиями использования OAuth** введите **Имя продукта** и нажмите кнопку **Сохранить**: ![Настройка экрана условий использования][13]

10. На странице создания идентификатора клиента:

	1. Из списка **Application type** (Тип приложения) выберите пункт **Web application** (Веб-приложение).  
	2.  Введите имя клиента.  
	3. В качестве URL-адреса перенаправления укажите URL-адрес перенаправления, полученный при добавлении нового API Google Диска на портале Azure (в этой статье).  
	4. Нажмите кнопку **Создать**.  

	![Создание идентификатора клиента][14]

11. Будут показаны идентификатор клиента и секрет клиента для зарегистрированного приложения.

Создается новое приложение Google Drive. Вы можете использовать это приложение при настройке API Google Drive на портале Azure.

## См. раздел "Интерфейсы REST API".

Справочник по [REST API Google Диска](../connectors/connectors-create-api-googledrive.md).

## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Google Drive в службу PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения:

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_0330_2016-->