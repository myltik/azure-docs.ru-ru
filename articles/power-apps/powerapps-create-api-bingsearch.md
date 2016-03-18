<properties
	pageTitle="Добавление API Поиска Bing в PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Поиска Bing в среде службы приложений организации."
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="gautamt"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Создание нового API Поиска Bing в среде службы приложений организации

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**: ![][4]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][5]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][2]

2. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][3]

3. Введите описательное **имя** для API.

4. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Поиск Bing**.

	а) Выберите **Настройки > Настроить обязательные параметры**.
	
	б) Введите *ключ учетной записи*. Если у вас нет ключа Поиска Bing, создайте бесплатное [предложение для Поиска Bing][1], чтобы получить ключ.

	в) Нажмите кнопку **ОК**.

5. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений организации будет добавлен новый API Поиска Bing.


## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Поиска Bing в PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения.

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_1203_2015-->