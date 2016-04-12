<properties
	pageTitle="Добавление API SharePoint Server в службу PowerApps Enterprise | Microsoft Azure"
	description="Создание или настройка нового API SharePoint Server в среде службы приложений организации"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Создание API SharePoint Server в PowerApps Enterprise

Добавьте API SharePoint Server в среду службы приложений (клиента) организации.

## Создание API на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com/) с помощью рабочей учетной записи. Например, это может быть адрес *ИмяПользователя*@*ВашаКомпания*.com. При этом выполняется автоматический вход в подписку вашей компании.
 
2. На панели задач выберите **Обзор**: ![][14]

3. Вы можете прокрутить список и найти PowerApps или набрать *powerapps* на клавиатуре: ![][15]

4. В разделе **PowerApps** выберите **Управление интерфейсами API**: ![Переход к зарегистрированным API][5]

5. В разделе **Управление интерфейсами API** выберите **Добавить**, чтобы добавить новый API: ![Добавление API][6]

6. Введите описательное **имя** для API.
7. В поле **Источник** укажите **Доступные API**, чтобы выбрать встроенные API, а затем выберите **SharePoint Server**. 
8. Выберите **Настройки — Настроить обязательные параметры**.
9. Укажите *Идентификатор клиента* и *Ключ приложения* для приложения Azure Active Directory (AAD) в SharePoint Server, а также *URL-адрес SharePoint* и *Идентификатор ресурса* прокси-приложения AAD. Выполните действия, описанные в следующем разделе, для настройки подключения к локальному серверу SharePoint Server.  

	> [AZURE.IMPORTANT] Сохраните **URL-адрес перенаправления**. Это значение может понадобиться позже.
	
10. Нажмите кнопку **ОК**, чтобы завершить эти действия.

После их выполнения в среду службы приложений вашей организации будет добавлен новый API SharePoint Server.


## Настройка подключения к локальному серверу SharePoint Server

Сервер SharePoint Server использует Active Directory для проверки подлинности пользователей. API в средах службы приложений проходят проверку подлинности с помощью Azure Active Directory (AAD). Для этого нужно получить маркер AAD пользователя и преобразовать его в маркер AD. Затем этот маркер AD можно использовать для подключения к локальной службе.

[Прокси приложение Azure (прокси AAD)](../active-directory/active-directory-application-proxy-publish.md) позволяет это реализовать. Это общедоступная служба Azure, которая защищает удаленный доступ и единый вход в локальные веб-приложения. Действия по включению прокси AAD детально описываются в библиотеке MSDN. В общем они состоят из следующих этапов.

1. [Включение служб прокси приложений](../active-directory/active-directory-application-proxy-enable.md):  

	- включение прокси приложения в Azure AD;
	- установка и регистрация соединителя прокси приложения Azure.

2. [Публикация приложений с помощью прокси приложения](../active-directory/active-directory-application-proxy-publish.md):

	- публикация прокси приложения с помощью мастера. Отметьте внешний URL-адрес сайта интрасети SharePoint после создания прокси приложения.
	- Назначение пользователей и группы для приложения.
	- Ввод дополнительных параметров, например имени участника-службы (SPN), используемого соединителем прокси приложения для получения локального маркера Kerberos.

После создания прокси-приложения нужно создать другое приложение AAD, которое делегирует права прокси-приложению. Это нужно для получения маркера доступа и обновления маркера, которые требуются для потока согласия с условиями использования. Вы можете создать новое приложение AAD, следуя [этим инструкциям](../active-directory/active-directory-integrating-applications.md).

## Сводка и дальнейшие действия
В этом разделе вы добавили API Office 365 Outlook в службу PowerApps Enterprise. Теперь необходимо предоставить пользователям доступ к этому API, чтобы они могли добавлять его в свои приложения:

[Добавление подключения и предоставление доступа пользователям](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0309_2016-->