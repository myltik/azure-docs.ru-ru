---
title: "Как использовать соединитель SharePoint Online в приложениях логики | Документация Майкрософт"
description: "Создание приложений логики с соединителем SharePoint Online для управления списками на сайте SharePoint."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 3ecf3e30fe2fcb9d6473d7eda450536cddfa97f4


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Приступая к работе с соединителем SharePoint Online
Соединитель SharePoint Online можно использовать для управления списками SharePoint.  

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sharepoint-online"></a>Подключение к SharePoint Online
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой.  

### <a name="create-a-connection-to-sharepoint-online"></a>Создание подключения к SharePoint Online
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## <a name="use-a-sharepoint-online-trigger"></a>Использование триггера SharePoint Online
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## <a name="use-a-sharepoint-online-action"></a>Использование действия SharePoint Online
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## <a name="technical-details"></a>Технические сведения
Ниже приведены сведения о триггерах, действиях и ответах, которые поддерживает это подключение.

## <a name="sharepoint-online-triggers"></a>Триггеры SharePoint Online
В SharePoint доступны следующие триггеры.  

| Триггер | Description (Описание) |
| --- | --- |
| [При создании файла](connectors-create-api-sharepointonline.md#when-a-file-is-created) |Активирует поток при создании файла в папке SharePoint. |
| [При изменении файла](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |Активирует поток при изменении файла в папке SharePoint. |
| [When a new item is created](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |Активирует поток при создании элемента в списке SharePoint. |
| [When an existing item is modified](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |Активирует поток при изменении имеющегося элемента в списке SharePoint. |

## <a name="sharepoint-online-actions"></a>Действия SharePoint Online
В SharePoint доступны следующие действия.

| Действие | Description (Описание) |
| --- | --- |
| [Получение метаданных файла](connectors-create-api-sharepointonline.md#get-file-metadata) |Извлекает метаданные файла с помощью идентификатора файла. |
| [Обновление файла](connectors-create-api-sharepointonline.md#update-file) |Обновляет содержимое файла. |
| [Удаление файла](connectors-create-api-sharepointonline.md#delete-file) |Удаляет файл. |
| [Получение метаданных файла с помощью пути](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |Извлекает метаданные файла с помощью пути к файлу. |
| [Получение содержимого файла с помощью пути](connectors-create-api-sharepointonline.md#get-file-content-using-path) |Извлекает содержимое файла с помощью пути к файлу. |
| [Получение содержимого файла](connectors-create-api-sharepointonline.md#get-file-content) |Получает содержимое файла, используя идентификатор. |
| [Создание файла](connectors-create-api-sharepointonline.md#create-file) |Отправляет файл на сайт SharePoint. |
| [Копирование файла](connectors-create-api-sharepointonline.md#copy-file) |Копирует файл на сайт SharePoint. |
| [List folder](connectors-create-api-sharepointonline.md#list-folder) |Получает файлы, содержащиеся в папке SharePoint. |
| [List root folder](connectors-create-api-sharepointonline.md#list-root-folder) |Получает файлы, содержащиеся в корневой папке SharePoint. |
| [Extract folder](connectors-create-api-sharepointonline.md#extract-folder) |Извлекает файл архива в папку SharePoint (например, ZIP-файл). |
| [Get items](connectors-create-api-sharepointonline.md#get-items) |Получает элементы из списка SharePoint. |
| [Create item](connectors-create-api-sharepointonline.md#create-item) |Создает элемент в списке SharePoint. |
| [Get item](connectors-create-api-sharepointonline.md#get-item) |Получает один элемент из списка SharePoint, используя идентификатор. |
| [Delete item](connectors-create-api-sharepointonline.md#delete-item) |Удаляет элемент из списка SharePoint. |
| [Update item](connectors-create-api-sharepointonline.md#update-item) |Обновляет элемент в списке SharePoint. |
| [Get entity values](connectors-create-api-sharepointonline.md#get-entity-values) |Получает возможные значения для сущности SharePoint. |
| [Get lists](connectors-create-api-sharepointonline.md#get-lists) |Получает списки SharePoint с сайта. |

### <a name="action-details"></a>Сведения о действиях
Ниже приведены подробные сведения о действиях и триггерах этого соединителя, а также ответы на них.

### <a name="get-file-metadata"></a>Get file metadata (Получение метаданных файла)
Извлекает метаданные файла с помощью идентификатора файла. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| id* |Идентификатор файла |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="update-file"></a>Обновление файла
Обновляет содержимое файла. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| id* |Идентификатор файла |Выбор файла |
| body* |Содержимое файла |Содержимое файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |строка |

### <a name="delete-file"></a>Удаление файла
Удаляет файл. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| id* |Идентификатор файла |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

### <a name="get-file-metadata-using-path"></a>Получение метаданных файла с помощью пути
Извлекает метаданные файла с помощью пути к файлу. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| path* |Путь к файлу |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |строка |

### <a name="get-file-content-using-path"></a>Получение содержимого файла с помощью пути
Извлекает содержимое файла с помощью пути к файлу. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| path* |Путь к файлу |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

### <a name="get-file-content"></a>Получение содержимого файла
Получает содержимое файла, используя идентификатор. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| id* |Идентификатор файла |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

### <a name="create-file"></a>Создание файла
Отправляет файл на сайт SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| folderPath* |Путь к папке |Выбор файла |
| name* |Имя файла |Имя файла |
| body* |Содержимое файла |Содержимое файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="copy-file"></a>Копирование файла
Копирует файл на сайт SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| source* |Путь к исходному файлу |Путь к исходному файлу |
| destination* |Путь к конечному файлу |Путь к конечному файлу |
| overwrite |Флажок перезаписи |Указывает, нужно ли перезаписывать конечный файл, если он существует |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="when-a-file-is-created"></a>При создании файла
Активирует поток при создании файла в папке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint |
| folderId* |Идентификатор папки |Выбор папки |

Звездочка (*) означает, что свойство является обязательным.

### <a name="when-a-file-is-modified"></a>При изменении файла
Активирует поток при изменении файла в папке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint |
| folderId* |Идентификатор папки |Выбор папки |

Звездочка (*) означает, что свойство является обязательным.

### <a name="list-folder"></a>List folder (Получение содержимого папки)
Получает файлы, содержащиеся в папке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| id* |Идентификатор файла |Уникальный идентификатор папки |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="list-root-folder"></a>List root folder (Получение содержимого корневой папки)
Получает файлы, содержащиеся в корневой папке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="extract-folder"></a>Извлечение папки
Извлекает файл архива в папку SharePoint (например, ZIP-файл). 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint, например http://contoso.sharepoint.com/sites/mysite |
| source* |Путь к исходному файлу |Путь к исходному файлу |
| destination* |Путь к конечной папке |Путь к конечной папке |
| overwrite |Флажок перезаписи |Указывает, нужно ли перезаписывать конечный файл, если он существует |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string |
| Name (Имя) |string |
| DisplayName |string |
| Путь |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="when-a-new-item-is-created"></a>When a new item is created (При создании элемента)
Активирует поток при создании элемента в списке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |
| table* |Имя списка |Имя списка SharePoint |
| $filter |Запрос фильтра |Запрос фильтра ODATA для ограничения возвращаемых записей |
| $orderby |Упорядочить по |Запрос orderBy ODATA для указания порядка записей |
| $skip |Число пропусков |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |Максимальное число записей |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
ItemsList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

### <a name="when-an-existing-item-is-modified"></a>When an existing item is modified (При изменении имеющегося элемента)
Активирует поток при изменении имеющегося элемента в списке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |
| table* |Имя списка |Имя списка SharePoint |
| $filter |Запрос фильтра |Запрос фильтра ODATA для ограничения возвращаемых записей |
| $orderby |Упорядочить по |Запрос orderBy ODATA для указания порядка записей |
| $skip |Число пропусков |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |Максимальное число записей |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
ItemsList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

### <a name="get-items"></a>Get items (Получение элементов)
Получает элементы из списка SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |
| table* |Имя списка |Имя списка SharePoint |
| $filter |Запрос фильтра |Запрос фильтра ODATA для ограничения возвращаемых записей |
| $orderby |Упорядочить по |Запрос orderBy ODATA для указания порядка записей |
| $skip |Число пропусков |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |Максимальное число записей |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
ItemsList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

### <a name="create-item"></a>Create item (Создание элемента)
Создает элемент в списке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |
| table* |Имя списка |Имя списка SharePoint |
| item* |Элемент |Создаваемый элемент |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
Элемент

| Имя свойства | Тип данных |
| --- | --- |
| ItemInternalId |string |

### <a name="get-item"></a>Get item (Получение элемента)
Получает один элемент из списка SharePoint, используя идентификатор. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |
| table* |Имя списка |Имя списка SharePoint |
| id* |Идентификатор |Уникальный идентификатор извлекаемого элемента |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
Элемент

| Имя свойства | Тип данных |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-item"></a>Удаление элемента
Удаляет элемент из списка SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |
| table* |Имя списка |Имя списка SharePoint |
| id* |Идентификатор |Уникальный идентификатор удаляемого элемента |

Звездочка (*) означает, что свойство является обязательным.

### <a name="update-item"></a>Обновление элемента
Обновляет элемент в списке SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |
| table* |Имя списка |Имя списка SharePoint |
| id* |Идентификатор |Уникальный идентификатор обновляемого элемента |
| item* |Элемент |Элемент с измененными свойствами |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
Элемент

| Имя свойства | Тип данных |
| --- | --- |
| ItemInternalId |string |

### <a name="get-entity-values"></a>Get entity values (Получение значений сущности)
Получает возможные значения для сущности SharePoint. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта SharePoint |URL-адрес сайта SharePoint |
| table* |Имя таблицы |Имя таблицы |
| id* |Идентификатор сущности |Идентификатор сущности |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
### <a name="get-lists"></a>Получение списков
Получает списки SharePoint с сайта. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| dataset* |URL-адрес сайта |URL-адрес сайта SharePoint (например: http://contoso.sharepoint.com/sites/mysite) |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
TablesList

| Имя свойства | Тип данных |
| --- | --- |
| value |array |

## <a name="http-responses"></a>Ответы HTTP
Описанные выше действия и триггеры могут возвращать один или несколько кодов состояния HTTP, которые приведены ниже. 

| Имя | Описание |
| --- | --- |
| 200 |ОК |
| 202 |Принято |
| 400 |Ошибка запроса |
| 401 |Не авторизовано |
| 403 |Запрещено |
| 404 |Не найдено |
| 500 |Внутренняя ошибка сервера. Произошла неизвестная ошибка. |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


