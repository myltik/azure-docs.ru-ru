<properties
	pageTitle="Добавление API Office 365 Users в службу PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API Office 365 Users в среде службы приложений организации"
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

# Создание нового API Office 365 Users в среде службы приложений организации

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**:  
![][14]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре:  
![][15]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**:

	![Переход к зарегистрированным API][1]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API:

	![Добавление API][2]

6. Введите описательное **имя** для API.
	
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **Office 365 Users**:

	![выбор API Office 365 Users][3]

8. Выберите **Настройки — Настроить обязательные параметры**:

	![настройка параметров API Office 365 Users][4]

9. Введите *Идентификатор клиента* и *Секрет клиента* для приложения Azure Active Directory (AAD) в Office 365. Если приложения нет, см. раздел "Регистрация приложения AAD для использования с PowerApps" в этой статье, где описывается создание нужных значений идентификатора и секрета.

	> [AZURE.IMPORTANT]Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.

10. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений организации будет добавлен новый API Office 365 Users.

## (Необязательно.) Регистрация приложения AAD для использования с API Office 365 в PowerApps

Если у вас нет приложения AAD с ключом и секретом, выполните следующие действия, чтобы создать приложение и получить нужные значения.

1. Откройте [портал Azure][5].

2. Выберите **Обзор**, а затем — **Active Directory**:

	> [AZURE.NOTE]Откроется раздел Active Directory на классическом портале Azure.

3. Выберите имя клиента вашей организации:

	![Запуск Azure Active Directory][6]

4. Перейдите на вкладку **Приложения** и выберите **Добавить**:

	![Клиентские приложения AAD][7]

5. На экране **Добавление приложения**:

	а) введите **Имя** приложения; б) оставьте тип приложения **Веб**; в) щелкните значок **Далее**.

	![Добавление приложения AAD: сведения о приложении][8]

6. На экране **Свойства приложения**:

	а) введите **URL-адрес входа** приложения. Поскольку для PowerApps будет использоваться проверка подлинности AAD, в качестве URL-адреса входа укажите \__https://login.windows.net_; б) введите допустимый **универсальный код ресурса URI кода приложения** для своего приложения; в) нажмите **ОК**.

	![Добавление приложения AAD: свойства приложения][9]

7. После завершения вы будете перенаправлены в новое приложение AAD. Перейдите на вкладку **Настройка**:

	![Приложение AAD Contoso][10]

8. В разделе _OAuth 2_ в качестве **URL-адреса ответа** укажите URL-адрес перенаправления, полученный при добавлении нового API Office 365 Users на портале Azure (в этой статье). Выберите **Добавить приложение**:

	![Настройка приложения AAD Contoso][11]

9. В окне **Разрешения для других приложений** выберите **Office 365 Unified API (предварительная версия)** и нажмите **ОК**.

10. Обратите внимание, что на странице настройки _Office 365 Unified API (предварительная версия)_ добавляется в список _Разрешений для других приложений_.

11. Выберите **Делегированные разрешения** для _Office 365 Unified API (предварительная версия)_ и выберите разрешение **Чтение базовых профилей всех пользователей**.

Будет создано новое приложение Azure Active Directory. Вы можете использовать это приложение при настройке API Office 365 Users на портале Azure.

## Сводка и дальнейшие действия
В этом разделе вы добавили API пользователей Office 365 в PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения.

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!----HONumber=AcomDC_1217_2015-->