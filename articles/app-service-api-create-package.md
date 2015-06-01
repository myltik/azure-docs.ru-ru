<properties 
	pageTitle="Создание пакета приложения API" 
	description="Узнайте, как создать пакет приложения API." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="guayan"/>

# Создание пакета приложения API

## Обзор

В этой статье показано, как создать пакет приложения API для публикации в [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Сведения о создании приложения API см. в статье [Создание приложения API с помощью Visual Studio](app-service-dotnet-create-api-app.md).
- Чтобы узнать, как опубликовать пакет приложения API в Azure Marketplace, см. статью [Публикация пакета приложения API в Azure Marketplace](app-service-api-publish-package).

## Структура папок

Пакет Nuget (файл * .nupkg) для приложения API содержит следующие файлы и папки в папке *Содержимое*:

    apiapp.json
    Metadata
        apiDefinition.swagger.json
        icons
            <icon files>
        screenshots
            <screenshot files>
        deploymentTemplates
            <template files>
        UIDefinition.json
    <other artifacts like source code, binary, etc.>

Файл *.nupkg* создается с этой структуре папок при упаковке проекта Visual Studio, который имеет *apiapp.json* и *Metadata* в папке проекта, как показано на следующем рисунке:

![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-create-package/metadatainse.png)

В следующих разделах перечислены все файлы и папки в структуре папок приложения API перед описанием порядка упаковки и развертывания проекта.

## apiapp.JSON

Это файл манифеста для вашего приложения API.

|Имя (bold=required)|Тип|Формат|Описание|
|:---------------------|:-----|:-------|:------------|
|**id**|string|[a-zA-Z0-9_.]|Идентификатор этого пакета. Должен быть уникальным в пределах пространства имен и может содержать только буквы, цифры, "_" и ".". Должен начинаться с буквы или цифры.|
|**namespace**|string|доменное имя|Пространство имен, вместе со свойством **id** уникально идентифицирует приложение API. Должно быть доменным именем клиента AAD издателя.|
|**version**|string|[semver](http://docs.nuget.org/Create/Versioning)|Версия этого пакета. Если включено автоматическое обновление пользователями для этого пакета, оно будет применяться только к новой версии в рамках основной.|
|**gateway**|string|2015-01-14|Версия API шлюза, которую использует этот пакет. Шлюз — это специальное веб-приложение, через которое направляются все запросы к приложению API в группе ресурсов. Одна из его основных функций — проверка подлинности. Пока существует только одна версия шлюза: 2015-01-14. В дальнейшем при выпуске новых версий шлюза это свойство поможет избежать критических изменений и продолжить использование предыдущей версии API шлюза.| 
|**title**|string||Отображаемое имя приложения API.|
|**summary**|string|макс. 100 символов|Краткое описание приложения API.
|description|string|макс. 1500 символов|Полное описание приложения API. Может содержать HTML. Разрешенные элементы и атрибуты: "h1", "h2", "h3", "h4", "h5", "p", "ol", "ul", "li", "a[target|href]", "br", "strong", "em", "b", "i".|
|**author**|string|макс. 256 символов|Авторы приложения API.|
|homepage|string|URL-адрес|Домашняя страница приложения API.|
|endpoints|object||Список конечных точек, у которых платформа приложения API может запрашивать сведения о методах и состоянии приложения API.|
|endpoints.apiDefinition|string|URL-адрес|Относительный URL-адрес приложения API, предоставляемый приложением API, которое возвращает определение Swagger 2.0 API на запрос GET (например, "/swagger/docs/v1"). Если установлен, он будет использоваться статическим файлом apiDefinition.swagger.json в пакете (при наличии).|
|endpoints.status|string|URL-адрес|Относительный URL-адрес API, предоставляемый приложением API, которое возвращает сведения о состоянии выполнения приложения API на запрос GET.|
|categories|string[]|сообщество, общественный, предприятие, интеграция, протокол, app-datasvc, другие|Этот пакет приложения API будет отображаться в категории Azure Marketplace. По умолчанию приложение API всегда будет отображаться в категории сообщества. После утверждения приложения API оно будет отображаться в указанной категории.|
|license|object||Лицензия приложения API.|
|**license.type**|string||Идентификатор лицензий SPDX, например MIT.|
|license.url|string|URL-адрес|Абсолютный URL-адрес текста полной лицензии.|
|license.requireAcceptance|bool|true, false|Определяет, нужно ли принимать лицензию перед установкой. По умолчанию: false.|
|links|object[]||Массив ссылок для добавления на страницу Marketplace.|
|**links.text**|string||Текст ссылки.|
|**links.url**|string|URL-адрес|Абсолютный URL-адрес ссылки.|
|authentication|object[]||Массив, который указывает, какой тип проверки подлинности для исходящих вызовов API требует приложение API. Сейчас для каждого пакета приложения API поддерживается только один тип проверки подлинности.|
|**authentication.type**|string|Box, DropBox, Facebook, Google, Office365, OneDrive, Quickbooks, Salesforce, SharePointOnline, Twitter, Yammer|Проверка подлинности требуется приложением API. Сейчас поддерживается 11 типов проверки подлинности. В дальнейшем будет добавлено больше типов.| 
|authentication.scopes|string[]||Массив областей, соответствующих типу проверки подлинности.|
|copyright|string|Уведомление об авторском праве приложения API.
|brandColor|string|Любой формат, поддерживающий CSS.|Необязательный цвет торговой марки для улучшения взаимодействия с пользовательским интерфейсом. Например, конструктор Logic App использует это свойство для рисования цвета фона заголовка карточки.|

## metadata/apiDefinition.swagger.json

При необходимости здесь вы можете предоставить статический файл Swagger 2.0 JSON для предоставления определения API-вашего приложения API. Сначала платформа проверит, или свойство **endpoints.apiDefinition** настроено в **apiapp.json**. Если да, она получит определение API из URL-адреса, указанного в свойстве. Если нет, она попытается найти этот файл.

- Сведения о стандарте Swagger 2.0 см. в разделе [http://swagger.io/](http://swagger.io/). 
<!--todo provide URLs
- Сведения о настройке определения API для его оптимизации для логических приложений см. в разделе [title of doc]().
- Сведения о предоставлении динамического определения API см. в разделе [title of doc](). 
-->

## metadata/icons

При необходимости можно предоставить собственный набор значков для пакета приложения API. Если файлов с требуемыми значками нет, будет использоваться значок по умолчанию, как показано ниже.

![Крупный значок приложения API по умолчанию](./media/app-service-api-create-package/api-app-default-large-icon.png)

|Файл |width:|height:|Описание|
|:--------------------|:----|:-----|:----------|
|metadata/icons/small.png|40px|40px|Обязательно, если вы хотите использовать собственные значки.|
|metadata/icons/medium.png|90px|90px|Обязательно, если вы хотите использовать собственные значки.|
|metadata/icons/large.png|115px|115px|Обязательно, если вы хотите использовать собственные значки.|
|metadata/icons/wide.png|255px|115px|Обязательно, если вы хотите использовать собственные значки.|
|metadata/icons/hero.png|815px|290px|Необязательно, если вы хотите использовать собственные значки.|

## metadata/screenshots

Вы можете дополнительно загрузить до 5 снимков экрана для своего пакета приложения API.

|Файл|width:|height:|Описание|
|:--------------------|:----|:-----|:----------|
|metadata/screenshots/*.png|533px|324px|Снимки экрана вашего пакета приложения API.|

## metadata/deploymentTemplates

Иногда во время развертывания пакета приложения API требуется дополнительная настройка.  Например [Соединитель хранилища BLOB-объектов Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/) требует URI контейнера BLOB-объектов хранилища Azure. При необходимости можно также настроить ключ доступа.

Для поддержки этого сценария вы можете добавить список файлов шаблонов JSON диспетчера ресурсов Azure (ARM) в эту папку для настройки развертывания приложения API. Платформа приложения API объединит ваши пользовательские шаблоны ARM с нашим системным шаблоном для создания окончательного шаблона для развертывания. Все параметры, определенные в ваших пользовательских шаблонах ARM (ожидаемые для **$system**), также будут автоматически запрашиваться в колонке **создания** портала предварительной версии Azure, чтобы пользователи приложения API могли вводить значения.

Ниже приведен образец шаблона ARM, демонстрирующий, как запросить URI контейнера BLOB-объектов и ключ доступа во время развертывания приложения API.

    {
      "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "$system": {
          "type": "object"
        },
        "BlobConnector_ContainerUrl": {
          "type": "string"
        },
        "BlobConnector_AccessKey": {
          "type": "securestring"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-04-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('$system').siteName]",
          "location": "[parameters('$system').location]",
          "properties": {
            "siteConfig": {
              "appSettings": [
                {
                  "name": "BlobConnector_ContainerUrl",
                  "value": "[parameters('BlobConnector_ContainerUrl')]"
                },
                {
                  "name": "BlobConnector_AccessKey",
                  "value": "[parameters('BlobConnector_AccessKey')]"
                }
              ]
            }
          }
        }
      ]
    }

Соответствующая колонка создания на портале предварительной версии Azure показана на приведенном ниже снимке экрана. (На снимке экрана показано, как пакет приложения API использует UIDefinition.json для улучшения колонки создания. Подробнее см. [metadata/UIDefinition.json](#metadata-uidefinition-json).

![Пример колонки создания пользовательского шаблона ARM](./media/app-service-api-create-package/custom-arm-template-create-blade-example.png)

Дополнительные сведения: [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/dn835138).

## metadata/UIDefinition.json

Если вы используете настраиваемые шаблоны ARM для указания собственных настроек для развертывания приложения API, колонка **создания** портала предварительного просмотра Azure автоматически запрашивает параметры шаблона ARM, чтобы пользователи приложения API могли вводить значения. Вы можете использовать файл *UIDefinition.json*, чтобы улучшить пользовательский интерфейс колонки **создания**, добавив значения по умолчанию, подсказки, проверки и т. д.

Чтобы добавить *файл UIDefinition.json* действуйте по следующей схеме, а затем настройте параметры с помощью таблицы ниже.


    {
      "parameters": {
        "<your API app package id>": {
            "<name of the ARM template parameter>": { },
            "<name of the ARM template parameter>": { }
        }
      }
    }

|Имя |Тип|Описание|
|:---------------------|:-------|:------------|
|defaultValue|string|Значение по умолчанию элемента управления вводом колонки создания портала предварительной версии Azure.|
|displayName|string|Метка элемента управления вводом колонки создания портала предварительной версии Azure. Она должна быть короткой.|
|description|string|Описание элемента управления вводом.|
|tooltip|string|Подсказка элемента управления вводом колонки создания портала предварительной версии Azure. Она будет отображаться при нажатии пузырька сведений справа от метки. Подсказка может быть длинной и обширной.|
|constraints|Объект|Ограничения для проверки параметра.|
|constraints.required|bool|Является ли параметр обязательным или нет. Значение по умолчанию — false.|

Например, вот файл *UIDefinition.json* для [соединителя хранилища BLOB-объектов Azure](http://azure.microsoft.com/marketplace/partners/microsoft_com/azurestorageblobconnector/):

    {
      "parameters": {
        "AzureStorageBlobConnector": {
          "BlobConnector_ContainerUrl": {
            "defaultValue": "",
            "displayName": "Container/SAS URI",
            "description": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "tooltip": "Specify the URI of the Blob Container. The URI may also include the SAS token. Example http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah",
            "constraints": {
              "required": "true"
            }
          },
          "BlobConnector_AccessKey": {
            "defaultValue": "",
            "displayName": "Access Key",
            "description": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "tooltip": "Specify a valid primary/secondary storage account access key. Leave this field empty if you are using SAS token for authentication.",
            "constraints": {
              "required": "false"
            }
          }
        }
      }
    }

С его помощью колонка **создания** портала предварительной версии Azure настраивается на отображение следующей колонки **Параметры пакета**.

![Пример колонки создания UIDefinition](./media/app-service-api-create-package/uidefinition-create-blade-example.png)

<!--todo add when ready to document status URI
A URI to a web service Get method that returns a value that indicates the API app's current status. If you provide this URI, the portal will show the API app's current operational status along with other information about the API app, for example:  running, nearing quota, SSL certificate expiring, etc. The format of the JSON the portal expects to receive is shown below, following the end of this table. If you don't provide an endpoints.status URI, the portal shows the Azure platform status as the API app's status.
Here is an example that shows the expected format of the JSON response from the Get method that `endpoints.status` points to:
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
The `name` property is a short description of the status, `message` is a longer description, and `level` can be "Error", "Warning", or "Info" for normal status.
-->

## Создание пакета приложения API

Чтобы создать пакета приложения API

1. [Скачайте и установите кроссплатформенный интерфейс командной строки Azure](xplat-cli).
2. Переключитесь в режим диспетчера ресурсов Azure, выполнив следующую команду

        azure config mode arm

3. Создайте пакет nuget для своего приложения API с помощью следующей команды

        azure apiapp package create -p <package folder> -o <destination folder>

    Например:

        azure apiapp package create -p c:\ContactList\ContactList -o c:\ContactListPackage

Содержимое пакета будет проверено

- на соответствие описанному выше формату
- Metadata\\apiDefinition.swagger.JSON (при наличии) содержит допустимое определение API Swagger 2.0

При появлении любых проблем выводятся подробности, которые позволяют устранить проблему и создать правильный пакет приложения API.

## Дальнейшие действия

Теперь ваш пакет приложения API готов к публикации в Azure Marketplace. Подробнее см. в учебнике [Публикация пакета приложения API в Azure Marketplace](app-service-api-publish-package).

<!--HONumber=52-->
