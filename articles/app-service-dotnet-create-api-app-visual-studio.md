<properties 
	pageTitle="Преобразование существующего API в приложение API" 
	description="Настройка проекта веб-API Visual Studio для развертывания в качестве приложения API в службе приложений Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Преобразование существующего API в приложение API

## Обзор

В этом учебнике вы создадите проект веб-API ASP.NET в Visual Studio, а затем добавите пакеты NuGet и метаданные проекта, позволяющие публиковать и развертывать проект как приложение API в службе приложений Azure. В учебнике также объясняется, как настроить метаданные приложения API.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Создание проекта веб-API

1.  Откройте диалоговое окно **Новый проект** в Visual Studio.

2.  Выберите узел **Облако** на панели **Установленные шаблоны**, а затем щелкните шаблон **Веб-приложение ASP.NET**.

3.  Присвойте проекту имя *ContactsList*, а затем щелкните **ОК**.

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. В диалоговом окне **Новый проект ASP.NET** выберите шаблон **Пустой проект**, установите флажок **Веб-API**, снимите флажок **Разместить в облаке** и нажмите кнопку **ОК**.

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio создаст файлы для пустого проекта веб-API.

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. В **обозревателе решений**, щелкните правой кнопкой мыши папку **Модели**, а затем в контекстном меню последовательно выберите пункты **Добавить > Класс**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. Назовите новый файл *Contact.cs* и щелкните **Добавить**.

5. Замените содержимое нового файла следующим кодом. 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. Щелкните правой кнопкой мыши папку **Контроллеры**, а затем в контекстном меню последовательно выберите пункты **Добавить > Контроллер**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. В диалоговом окне **Добавление функции формирования шаблонов** выберите параметр **Контроллер веб-API 2 - пустой** и щелкните **Добавить**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. Назовите контроллер **ContactsController** и щелкните **Добавить**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. Замените код в новом файле контроллера следующим кодом. 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


Теперь у вас есть рабочий проект веб-API. Самый простой способ убедиться, что он работает, - вызвать метод GET из браузера.

6. Нажмите клавиши CTRL+F5, чтобы запустить проект.

	Браузер отобразит ошибку HTTP 403, так как он открывается без полного URL-адреса, указывающего на метод GET.

7. В адресной строке браузера добавьте "api/contacts/get/" в URL-адрес и нажмите клавишу ВВОД.  Конечный URL-адрес будет выглядеть как в следующем примере:

		http://localhost:25735/api/contacts/get/

	Разные браузеры отвечают на вызов API по-разному; если вы используете Internet Explorer, то увидите сообщение о скачивании в нижней части окна браузера:

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## Настройка приложения для развертывания в качестве приложения API

1. В **обозревателе решений** щелкните правой кнопкой мыши проект (не решение) и последовательно выберите пункты **Добавить > Пакет SDK для приложения API Azure**.

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. В диалоговом окне **Выбор источника метаданных приложений API** щелкните **Автоматическое создание метаданных**.

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	Этот параметр включает динамический пользовательский интерфейс Swagger, который будет представлен в учебнике позже. В качестве альтернативы вы можете предоставить статический файл определения API Swagger. Для этого нужно установить флажок **Указать статический файл JSON, содержащий метаданные Swagger**, и Visual Studio предложит отправить файл. Отправленный файл будет сохранен как *apiDefinition.Swagger.json* в папке *Metadata*.

3. Нажмите кнопку **ОК**.

	Visual Studio добавит файл *apiapp.json* и папку *Metadata*.

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	Visual Studio также добавит пакеты NuGet Swashbuckle, и в последующих шагах вы попробуете динамический пользовательский интерфейс определения API, предоставляемый Swagger. 

6. Нажмите клавиши CTRL+F5, чтобы запустить проект.

	Как и раньше, браузер отобразит ошибку HTTP 403.

7. В адресной строке браузера добавьте "swagger/" в URL-адрес и нажмите клавишу ВВОД.  Конечный URL-адрес будет выглядеть как в следующем примере:

		http://localhost:25735/swagger/

8. На странице Swagger щелкните **Контакты**, чтобы увидеть доступные методы.
 
	Отобразится только `Get`, так как это единственный метод, который вы создали в контроллере "Контакты". На странице отобразится пример ответа JSON.

9. Щелкните метод GET, чтобы увидеть пример ответа JSON и кнопку **Try it out** ("Испытать").

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. Щелкните **Try it out**.

	Вернется закодированный в ContactsController ответ.

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

Теперь проект веб-API готов к развертыванию в качестве приложения API в службе приложений Azure. В следующих разделах этого учебника предоставляются сведения о метаданных, которые можно изменить для настройки приложения API. 

## Просмотр apiapp.json

Параметры в файле *apiapp.json* определяют способ идентификации и представления приложения API в Azure Marketplace. В этом предварительном выпуске Visual Studio не предоставляет возможность публикации приложений API в Marketplace, поэтому многие из этих параметров не работают.

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

Первоначальное содержимое файла, созданного при выборе пункта меню **Пакет SDK для приложения API Azure** выглядят как в следующем примере:

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

В следующей таблице описывается формат и использование полей, включенных в файл, а также дополнительные поля, которые можно добавить. 

**Примечание.** Как было упомянуто ранее, в этом предварительном выпуске многие поля не действуют, так как они определяют способ представления приложения API в Azure Marketplace, но Visual Studio еще не предоставляет возможность публикации приложений API в Marketplace.

| Имя (bold=required) | Тип | Комментарии |
|:-----------|:------------|:------------|
|**id**           |string|Идентификатор пакета. Должен быть уникальным в пространстве имен и не должен содержать точки, символы "/" или "@". При развертывании проекта Visual Studio проверяет уникальность идентификатора и позволяет изменить его. 
|**namespace**    |string|Пространство имен, которое вместе со свойством **id** составляет уникальный идентификатор приложения API.  Свойство является обязательным, но значение может быть пустой строкой. <br/><br/>Свойство позволяет указать домен, например contoso.com.  Если идентификатор пакета, который вы хотите использовать, уже занят, можно добавить домен. Например, если "ContactsList" уже существует в коллекции приложений API без пространства имен, можно добавить пакет ContactsList с пространством имен contoso.com. <br/><br/>Другая причина, по которой следует указать домен, - добавление пакета в коллекцию приложений API, доступ к которой могут получить только сотрудники организации. <br/><br/>После преобразования точек в дефисы, а дефисов - в два дефиса (--) пространство имен будет использоваться в Marketplace в качестве имени издателя.<br/><br/>Для приложений API, предоставленных Майкрософт, пространство имен - "microsoft.com".  
|**version**      |string|Формат [Semver](http://docs.nuget.org/Create/Versioning), например 1.0.1, 1.1.0-alpha.
|**gateway**      |string|Версия шлюза в виде даты, например: 2015-01-14.  *gateway* - это специальное веб-приложение, через которое перенаправляются все запросы в приложения API в группе ресурсов. Одна из его основных функций - обработка проверки подлинности. Сейчас единственная версия шлюза - "2015-01-14". В будущем, после выпуска новых версий это свойство позволит избежать критических изменений и продолжить использовать предыдущий API шлюза. 
|**title**        |string|Отображаемое имя приложения API.
|**summary**      |string|Краткая сводка приложения API, максимум 100 символов.
|description      |string|Полное описание приложения API. Может содержать HTML, максимум 1500 символов.
|**author**       |string|Авторы приложения API, максимум 256 символов.
|homepage         |URI|Домашняя страница приложения API.
|endpoints        |object[]|Массив из одного элемента, который может содержать конечную точку определения API. 
|>>endpoints.apiDefinition|string|Относительный URI динамического пользовательского интерфейса определения API Swagger (например, "/swagger/docs/v1"), или статического файла определения API Swagger. Для веб-API ASP.NET динамически созданный пользовательский интерфейс Swagger - обычно самый оптимальный выбор, но, если он не подходит для API или вы не используете веб-API ASP.NET, можно предоставить статический файл определения. Чтобы предоставить статический файл определения, можно задать относительный URI, который указывает на него, или оставить свойство пустым и включить статический файл определения API в папку "Metadata" в виде [apiDefinition.swagger.json](#apidef). 
|>>endpoints.status|URI|Зарезервировано для будущего использования.
|categories       |string[]|Определяет место отображения пакета в Azure Marketplace. Допустимые значения: social, enterprise, integration, protocol, app-datasvc, other. Значение по умолчанию: other.
|license          |object|Лицензия приложения API.
|>>**license.type**|string|Идентификатор лицензии SPDX, например MIT.
|>>license.url    |url|Абсолютный URL-адрес, указывающий на полный текст лицензии.
|>>license.requireAcceptance|bool|Значение, указывающее, требуется ли утверждение лицензии перед установкой. Значение по умолчанию: false.
|links            |object[]|Массив ссылок, добавляемый на страницу Marketplace.
|>>**links.text** |string|Текст ссылки.
|>>**links.url**  |url|URL-адрес ссылки.
|authentication|object[]|Массив, указывающий тип проверки подлинности, необходимый приложению API для выполнения исходящих вызовов API.  Например, соединителю DropBox требуется выполнить проверку подлинности в DropBox, а соединителю Salesforce - в Salesforce.
|>>authentication.type|string|Поддерживаемые значения (без учета регистра): Box, DropBox, Facebook, GitHub, Google, Instagram, Marketo, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, SugarCRM, Twitter, Yammer. По этому значению портал определяет, какие значения конфигурации являются обязательными, например идентификатор клиента и секрет клиента. 
|>>authentication.scopes|string[]|Массив областей видимости конкретного типа проверки подлинности.
|copyright        |string|Уведомление об авторских правах приложения API.
|brandColor       |string|Дополнительный параметр настройки фирменного цвета, расширяющий возможности пользовательского интерфейса, в любом формате, совместимом с CSS, например, #abc, red.
|tags             |string[]|Список тегов, связанных с пакетом.

<!--задача: добавить при готовности к записи зависимостей
|dependencies    |object[]|Массив зависимостей пакета.
|>>dependencies.id|string|Идентификатор пакета зависимостей.
|>>dependencies.domain|string|Домен пакета зависимостей.
|>>dependencies.version|string|Версия пакета зависимостей.
-->

<!--задача: добавить при готовности к записи URI состояния
URI, указывающий на метод GET веб-службы, который возвращает значение текущего состояния приложения API. При предоставлении этого URI на портале отобразится текущее рабочее состояние и другие сведения о приложении API, например:  "выполняется", "приближается к достижению квоты", "срок действия SSL-сертификата истекает" и т. д. Формат JSON, который портал ожидает получить, показан ниже, под таблицей. Если вы не предоставите URI endpoints.status, состояние платформы Azure отобразится на портале как состояние приложения API.
Далее показан пример ожидаемого формата ответа JSON из метода GET, на который указывает `endpoints.status`:
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
Свойство `name` - это краткое описание состояния, `message` - более длинное описание, а `level` для нормального состояния может быть "Ошибка", "Предупреждение" или "Сведения".
-->

## Просмотр папки "Metadata"

Папка "Metadata" может содержать значки и снимки экрана для коллекции приложений API, файл Swagger, в который записываются API, и конфигурацию пользовательского интерфейса для портала Azure. Вся эта информация является необязательной.

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**Примечание.** Как было упомянуто ранее, для *apiapps.json* метаданные, которые относятся к представлению приложения API в Azure Marketplace, не влияют на этот предварительный выпуск, так как Visual Studio не предоставляет возможность публикации приложений API в Marketplace.

### Папка "Metadata/icons"

Вы можете предоставить значки для отображения в коллекции. 
В отсутствие пользовательских значков будут использоваться универсальные значки приложения API. Файлы значков должны быть в формате PNG и соответствовать следующим соглашениям об именовании и размерах:

<!--todo: also used in the workflow designer--> 

|Имя файла|Размер
|:-----|:-----:
|small.png|40X40
|medium.png|90X90
|large.png|115X115
|hero.png|815X290
|wide.png|255X115

### Папка "Metadata/screenshots"

Вы можете отправить до 5 снимков экрана для отображения в коллекции. Файлы изображений должны быть в формате PNG, размером 533x324.

### <a id="apidef"></a>Файл "Metadata/apiDefinition.swagger.json"

Для описания определения API приложения API можно использовать файл формата [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md). Это позволит предоставить определения API статически, в пакете. 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- задача: найти, можно ли это чем-нибудь заменить - возможно, вложенной папкой deploymentTemplates.
### Файл resourceTemplate.delta.json
Вы можете указать пользовательский шаблон диспетчера ресурсов Azure, который выполняется во время развертывания приложения API. Ресурсы, указанные в разностном файле, добавляются в ресурсы, которые создаются по умолчанию для приложения API. Например, если приложению API требуется экземпляр базы данных SQL, вы можете использовать этот файл для автоматической подготовки базы данных  и установки строки подключения в параметрах конфигурации при развертывании приложения API.
-->  

### Файл "Метаданные/UIDefinition.json"

Вы можете предоставить сведения о пользовательском интерфейсе, например указания и проверку в [формате Azure Marketplace](https://auxdocs.azurewebsites.net/ru-ru/documentation/articles/gallery-items).

### Папка "Метаданные/deploymentTemplates"

Visual Studio создает эту папку при выборе пункта меню **Пакет SDK для приложения API Azure**, но в этом предварительном выпуске она не используется.

## Дальнейшие действия

Проект веб-API теперь можно развернуть как приложение API, и вы можете перейти к учебнику [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).

Дополнительные сведения см. в разделе [Что такое приложения API?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->