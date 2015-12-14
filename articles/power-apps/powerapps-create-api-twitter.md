<properties
	pageTitle="Добавление API Twitter в службу PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Twitter в среде службы приложений организации"
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
   ms.date="11/25/2015"
   ms.author="litran"/>

# Создание нового API Twitter в среде службы приложений организации

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании. 

2. На панели задач выберите **Обзор**: ![][14]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][15]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][1]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][2]

6. Введите описательное **имя** для API.
	
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Twitter**: ![выбор API Twitter][3]

8. Выберите **Настройки — Настроить обязательные параметры**: ![настройка параметров API Twitter][4]

9. Введите *Ключ потребителя* и *Секрет потребителя* приложения Twitter. Если приложения нет, см. раздел "Регистрация приложения Twitter для использования с PowerApps" в этой статье, где описывается создание нужных значений ключа и секрета.

	> [AZURE.IMPORTANT]Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.

10. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений организации будет добавлен новый API Twitter.


## (Необязательно.) Регистрация приложения Twitter для использования с PowerApps

Если у вас нет приложения Twitter с ключом и секретом, выполните следующие действия, чтобы создать приложение и получить нужные значения.

1. Перейдите на сайт [https://apps.twitter.com/](https://apps.twitter.com) и войдите с помощью учетной записи Twitter.

2. Щелкните **Создать новое приложение**: ![страница приложений Twitter][6]

3. На экране **Создание приложения**:
   
	а) укажите **Имя**; б) укажите **Описание**; в) укажите **Веб-сайт**; г) в качестве **URL-адреса обратного вызова** укажите URL-адрес перенаправления, полученный при добавлении нового API Twitter на портале Azure (в этом разделе); д) примите условия соглашения с разработчиком и выберите **Создать приложение Twitter**.

	![создание приложения Twitter][7]

4. После создания приложения вы будете перенаправлены на страницу приложения.

Создается новое приложение Twitter. Это приложение вы можете использовать при настройке API Twitter на портале Azure.

## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Twitter в службу PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения:

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->