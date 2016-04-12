<properties
	pageTitle="Добавление API Salesforce в службу PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Salesforce в среде службы приложений организации"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Создание API Salesforce в PowerApps Enterprise

> [AZURE.SELECTOR]
- [Приложения логики](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

Добавьте API Salesforce в среду службы приложений (клиента) организации.

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**: ![][14]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][15]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][1]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][2]

6. Введите описательное **имя** для API.
	
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Salesforce**: ![выбор API Salesforce][3]

8. Выберите **Настройки — Настроить обязательные параметры**: ![настройка параметров API Dropbox][7]

9. Введите *Ключ приложения* и *Секрет приложения* своего приложения Salesforce. Если приложения нет, см. раздел "Регистрация приложения Salesforce для использования с PowerApps" в этой статье, где описывается создание нужных значений ключа и секрета.

	> [AZURE.IMPORTANT] Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.

10. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений организации будет добавлен новый API Salesforce.


## (Необязательно.) Регистрация приложения Salesforce для использования с PowerApps

Если у вас нет приложения Salesforce с ключом и секретом, выполните следующие действия, чтобы создать приложение и получить нужные значения.

1. Откройте [домашнюю страницу разработчика Salesforce][5] и войдите со своей учетной записью Salesforce. 

2. На домашней странице выберите свой профиль и выберите **Настройка**: ![домашняя страница SalesForce][6]

3. Выберите **Создать** и **Приложения**. На экране **Приложения** нажмите кнопку **Создать** в разделе **Подключенные приложения**: ![создание приложения SalesForce][7]

4. На экране **Новое подключенное приложение**:

	1. Введите значение в поле **Connected App Name** (Имя подключенного приложения).  
	2. Введите значение в поле **API Name** (Имя API).  
	3. Введите значение в поле **Contact Email** (Контактный электронный адрес).  
	4. В разделе _API (Enable OAuth Settings)_ (API (включение параметров OAuth)) установите флажок **Enable OAuth Settings** (Включить параметры OAuth) и в поле **Callback URL** (URL-адрес обратного вызова) укажите URL-адрес перенаправления, полученный при добавлении нового API Salesforce на портале Azure (в этой статье).  

5. В разделе _Выбранные области OAuth_ добавьте следующие области в поле **Выбранные области OAuth**.

	- Доступ к данным Chatter и управление ими (chatter\_api)
	- Доступ к своим данным и управление ими (api)
	- Разрешить доступ к своему уникальному идентификатору (openid)
	- Выполнять запросы от вашего имени в любое время (refresh\_token, offline\_access)

6. **Сохраните** изменения: ![новое приложение SalesForce][8]

Создается новое приложение Salesforce. Вы можете использовать это приложение при настройке API Salesforce на портале Azure.

## См. раздел "Интерфейсы REST API".

Справочник по [REST API Salesforce](../connectors/connectors-create-api-salesforce.md).

## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Salesforce в службу PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения:

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG
[5]: https://developer.salesforce.com
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG
[14]: ./media/powerapps-create-api-salesforce/browseall.png
[15]: ./media/powerapps-create-api-salesforce/allresources.png

<!---HONumber=AcomDC_0330_2016-->