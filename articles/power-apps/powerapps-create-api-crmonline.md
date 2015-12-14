<properties
	pageTitle="Добавление API Dynamics CRM Online в PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Dynamics CRM Online в среде службы приложений организации."
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/25/2015"
   ms.author="sameerch"/>

# Создание нового API Dynamics CRM Online в среде службы приложений организации

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.

2. На панели задач выберите **Обзор**: ![][1]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][2]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][3]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][4]

6. Введите описательное **имя** для API.

7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Dynamics CRM Online**: ![Выбор API Microsoft Dynamics CRM Online][5]

8. Выберите **Настройки > Настроить обязательные параметры**: ![Настройка параметров API Dynamics CRM Online][6]

9. Введите **Идентификатор клиента** и **Ключ приложения** для приложения Azure Active Directory (AAD) в Dynamics CRM Online. Если приложения нет, см. раздел «Регистрация приложения AAD для использования с PowerApps» в этой статье, где описывается создание нужных значений идентификатора и секрета.

	> [AZURE.IMPORTANT]Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.

10. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений организации будет добавлен новый API Dynamics CRM Online.

## (Необязательно.) Регистрация приложения AAD для использования с API Dynamics CRM Online в PowerApps

1. Откройте [портал Azure](https://portal.azure.com).

2. Выберите **Обзор**, а затем — **Active Directory**:

	> [AZURE.NOTE]Откроется раздел Active Directory на классическом портале Azure.

3. Выберите имя клиента своей организации: ![Запуск Azure Active Directory][7]

4. Перейдите на вкладку **Приложения** и выберите **Добавить**: ![Клиентские приложения AAD][8]

5. На экране **Добавление приложения**:

	а) Введите **имя** приложения. б) Оставьте тип приложения **Интернет**. в) Щелкните **Далее**.

	![Добавление приложения AAD — сведения о приложении][9]

6. На экране **Свойства приложения**:

	а) Введите **URL-адрес входа** приложения. Так как для PowerApps будет использоваться аутентификация AAD, в качестве URL-адреса входа укажите \__https://login.windows.net_. б) Введите допустимый **универсальный код ресурса (URI) идентификатора приложения** для своего приложения. в) Нажмите кнопку **ОК**.

	![Добавление приложения AAD — свойства приложения][10]

7. После завершения вы будете перенаправлены в новое приложение AAD. Перейдите на вкладку **Настройка**: ![Приложение AAD Contoso][11]

8. В разделе _OAuth 2_ в качестве **URL-адреса ответа** укажите URL-адрес перенаправления, полученный при добавлении нового API Dynamics CRM Online на портале Azure (в этой статье). ![Настройка приложения AAD Contoso][12]

9. Щелкните **Сохранить**.

Будет создано новое приложение Azure Active Directory. Это приложение можно использовать при настройке API Dynamics CRM Online на портале Azure.

## Сводка и дальнейшие действия
С помощью инструкций в этом разделе вы добавили API Dynamics CRM Online в PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения.

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_1203_2015-->