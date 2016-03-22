<properties
	pageTitle="Добавление API OneDrive в PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API OneDrive в среде службы приложений организации."
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Создание API OneDrive в PowerApps Enterprise

> [AZURE.SELECTOR]
- [Приложения логики](../articles/connectors/create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

Добавьте API OneDrive в среду службы приложений (клиента) организации.

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**: ![][14]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][15]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][1]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][2]

6. Введите описательное **имя** для API.
	
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **OneDrive**: ![Выбор API OneDrive][3]

8. Выберите **Настройки > Настроить обязательные параметры**: ![Настройка параметров API OneDrive][4]

9. Введите *ключ* и *секрет* своего приложения OneDrive. Если у вас нет приложения, см. раздел «Регистрация приложения OneDrive для использования с PowerApps» в этой статье, где описывается создание требуемых значений ключа и секрета.

	> [AZURE.IMPORTANT] Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.

10. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений организации будет добавлен новый API OneDrive.

## (Необязательно.) Регистрация приложения OneDrive для использования с PowerApps

Если у вас нет приложения OneDrive с ключом и секретом, выполните следующие действия, чтобы создать приложение и получить нужные значения.

1. Перейдите на [страницу создания приложения][5] в _Центре разработчиков для учетной записи Microsoft_ и войдите с помощью своей _учетной записи Майкрософт_.

2. Введите **имя приложения** и щелкните **Я принимаю**: ![Новое приложение OneDrive][6]

3. На странице «Параметры»:

	1. Щелкните **Параметры API**.  
	2. В качестве URL-адреса перенаправления укажите URL-адрес перенаправления, полученный при добавлении нового API OneDrive на портале Azure (в этой статье).  
	3. Щелкните **Сохранить**.  

	![Параметры API приложения OneDrive][7]

Будет создано новое приложение OneDrive. Это приложение можно использовать при настройке API OneDrive на портале Azure.

## См. раздел "Интерфейсы REST API".

Справочник по [REST API OneDrive](../connectors/create-api-onedrive.md).

## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API OneDrive в PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения.

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_0309_2016-->