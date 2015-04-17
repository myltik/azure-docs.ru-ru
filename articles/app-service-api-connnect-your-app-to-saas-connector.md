<properties 
	pageTitle="Подключение приложения к существующему соединителю SaaS" 
	description="В этой статье показано, как подключиться к существующему соединителю SaaS" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# Подключение приложения к существующему соединителю SaaS

## Обзор

В этом учебнике показано, как использовать соединитель SaaS в Azure Marketplace. 

Вы выполните следующие действия.
- Подготовка соединителя Dropbox.
- Настройка приложения API Dropbox.
- Настройка шлюза.
- Получение токена и его сохранение в хранилище токенов. 
- Вызов API Dropbox и проверка работоспособности доступа с проверкой подлинности.

## Подготовка соединителя Dropbox

1. Перейдите на домашнюю страницу портала предварительной версии и щелкните "Marketplace".

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. Найдите Dropbox в коллекции Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Щелкните значок Dropbox, чтобы подготовить соединитель Dropbox. Нажмите кнопку "Создать", чтобы подготовить соединитель Dropbox. Убедитесь, что указали имя и нужные значения во всех полях, прежде чем нажать кнопку "Создать". 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. Взгляните на колонку группы ресурсов на [портале предварительной версии](https://portal.azure.com/). Вы увидите соединитель Dropbox и шлюз. Приложение API было подготовлено как часть группы ресурсов. Когда портал создал группу ресурсов, он также создал шлюз. Шлюз - это специальное веб-приложение, которое обрабатывает все запросы, предназначенные для приложений API в группе ресурсов.

	Вы можете добавлять веб- и другие приложения API в одну группу ресурсов, и каждое приложение API в группе ресурсов может иметь один из трех параметров доступности:

	* Общедоступный (анонимный) - любой пользователь может вызвать приложение API за пределами группы ресурсов, не выполнив вход.
	* Общедоступный (с проверкой подлинности) - только пользователи, выполнившие проверку подлинности, могут вызвать приложение API за пределами группы ресурсов.
	* Внутренний - только другие приложения API или веб-приложения в той же группе ресурсов могут вызвать приложение API.

## Настройка приложения API Dropbox

Сначала настройте в Dropbox возможность приема только запросов, прошедших проверку подлинности.  Задайте уровень доступа **Общедоступный (с проверкой подлинности)** и настройте в шлюзе обязательную проверку подлинности от поставщика, например Azure Active Directory, Google или Facebook.

1.	На [портале предварительной версии](https://portal.azure.com/) Azure щелкните **Обзор > Приложения API**, а затем выберите имя приложения API, которое нужно защитить.

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	В колонке "Приложение API" щелкните "Параметры", а затем - "Параметры приложения".
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	В колонке **Параметры приложения** измените **Уровень доступа** на **Общедоступный (с проверкой подлинности)**. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	Теперь вы защитили соединитель Dropbox от несанкционированного доступа. Пора настроить проверку подлинности Dropbox. Укажите сведения *Идентификатор клиента* и "Секрет клиента* в полях ниже (вы можете подключиться к *идентификатору клиента* и "секрету клиента* из [учетной записи разработчика Dropbox](https://www.dropbox.com/developers/apps)).

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

Далее в шлюзе потребуется указать, какого поставщика проверки подлинности следует использовать.

## Настройка шлюза

1. Вернитесь к колонке приложения API Dropbox и щелкните ссылку на шлюз.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. Выберите поставщика удостоверений, которого следует использовать, и выполните шаги в соответствующей статье, чтобы настроить приложение API с помощью того же поставщика. Эти статьи были написаны для мобильных приложений, но действия не отличаются от действий для приложений API. В некоторых случаях требуется использовать как [портал управления](https://manage.windowsazure.com/), так и [портал предварительной версии](https://portal.azure.com/).
  
	- [Учетная запись Майкрософт](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Имя для входа Facebook](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Имя для входа Twitter](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Имя для входа Google](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	Ознакомьтесь со статьей "Защита приложения API: добавление проверки подлинности Azure Active Directory или поставщика социальных сетей" для получения инструкций по настройке. 

## Получение токена OAuth и его сохранение в хранилище токенов

1.	В браузере перейдите по URL-адресу входа. 

	Вы можете получить URL-адрес в колонке настройки Active Directory шлюза.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	URL-адрес будет иметь следующий формат...
	http://[имя_группы_ресурсов]gateway.azurewebsites.net/login/[имя_поставщика]

	Например, если вы назвали группу ресурсов "MyResource" и настроили в шлюзе проверку подлинности Azure Active Directory, URL-адрес будет следующим:

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	Убедитесь, что запустили средства разработчика перед тем, как ввести URL-адрес в браузер и нажать клавишу ВВОД. Мы должны получить сообщение о выполнении входа. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	Получите токен проверки подлинности Zumo и значение на вкладке "Сеть"(Chrome).
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Выведите расширение postman в Chrome. Введите URL-адрес согласия, токен проверки подлинности Zumo(заголовок) шлюза и значение, после чего отправьте запрос POST. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	Получите URL-адрес перенаправления, который вернул запрос POST, и убедитесь, что URL-адрес перенаправления работает. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	Если URL-адрес перенаправления работает, это означает, что токен допустим. Все вызовы соединителя SaaS будут использовать заданный и проверенный токен. Этот токен должен быть включен во все запросы из внешних источников, проходящих через шлюз. При получении доступа к API в браузере обычно браузер сохраняет токен в файл cookie и отправляет его вместе с последующими вызовами в API.

Тот же поток применяется ко всем соединителям SaaS, таким как Safesforce, Facebook и т. д. 

## Вызов API Dropbox и проверка работоспособности доступа с проверкой подлинности.

1. Вернитесь к списку приложений API и выберите соединитель Dropbox. 

1. Обратите внимание на URL-адрес вверху.

2. Щелкните соединитель Dropbox (как показано на картинке), чтобы увидеть все поддерживаемые API.

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	Поддерживаемые операции можно просмотреть в приложении API, в колонке определений API. Вы можете скачать Swagger и использовать в Visual Studio для создания строго типизированных клиентов. Кроме того, можно скачать Swaddle для использования в Sienna и PowerApp Studio. 

2. В окне браузера введите URL-адрес, скопированный с портала, и добавьте к нему любой из поддерживаемых API, чтобы получить доступ к файлам или другим подробным сведениям в учетной записи Dropbox. 

	Формат: `<URL-адрес>`/Операция

	Например:

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	Если установлена правильная проверка подлинности, вызов выполняется успешно, и в браузере отображаются подробные сведения о папке фотографий. 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--задача<Копировать изображение браузера>-->

## Дальнейшие действия

Вы узнали, как защитить приложение API и настроить шлюз для доступа к соединителю SaaS, используя хранилище токенов. Дополнительные сведения см. в разделе [Что такое приложения API?](app-service-api-apps-why-best-platform.md). 

<!--HONumber=49-->