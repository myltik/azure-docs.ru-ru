---
title: "Использование соединителя SharePoint Online в приложениях логики | Документация Майкрософт"
description: "Как приступить к использованию соединителя SharePoint Online в приложениях логики."
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: c462c98e2ecef1afe62ef921265b8842b6ae717d


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>Приступая к работе с соединителем SharePoint Online
Подключение SharePoint позволяет работать со списками в SharePoint.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.
> 
> 

Сведения о добавлении операции в приложения логики см. в статье [Создание нового приложения логики, подключающего службы SaaS](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Поговорим о триггерах и действиях
Соединитель SharePoint можно использовать как действие. Кроме того, он имеет триггеры. Все соединители поддерживают данные в форматах JSON и XML. 

Соединитель SharePoint предоставляет следующие действия и (или) триггеры.

### <a name="sharepoint-actions"></a>Действия SharePoint
Вы можете выполнять перечисленные ниже действия:

| Действие | Description (Описание) |
| --- | --- |
| GetFileMetadata |Используется для получения метаданных файла в библиотеке документов |
| UpdateFile |Используется для обновления файла в библиотеке документов |
| DeleteFile |Используется для удаления файла в библиотеке документов |
| GetFileMetadataByPath |Используется для получения метаданных файла в библиотеке документов |
| GetFileContentByPath |Используется для получения файла в библиотеке документов |
| GetFileContent |Используется для получения файла в библиотеке документов |
| CreateFile |Используется для отправки файла в библиотеке документов |
| CopyFile |Используется для копирования файла в библиотеке документов |
| ExtractFolderV2 |Используется для извлечения папки в библиотеке документов |
| PostItem |Создает элемент в списке SharePoint |
| GetItem |Извлекает один элемент из списка SharePoint |
| DeleteItem |Удаляет элемент из списка SharePoint |
| PatchItem |Обновляет элемент в списке SharePoint |

### <a name="sharepoint-triggers"></a>Триггеры SharePoint
Можно прослушивать указанные ниже события:

| Триггер | Описание |
| --- | --- |
| OnNewFile |Запускает поток при создании файла в папке SharePoint |
| OnUpdatedFile |Запускает поток при изменении файла в папке SharePoint |
| GetOnNewItems |При создании элемента в списке SharePoint |
| GetOnUpdatedItems |При изменении существующего элемента в списке SharePoint |

## <a name="create-a-connection-to-sharepoint"></a>Создание подключения к SharePoint
Чтобы использовать соединитель SharePoint, сначала нужно создать **подключение** , а затем указать данные для следующих свойств: 

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Указание учетных данных SharePoint |

Для подключения к **SharePoint Online** необходимо предоставить свое удостоверение (имя пользователя и пароль, учетные данные смарт-карты и т. п.). После прохождения аутентификации вы сможете использовать соединитель SharePoint Online в приложении логики. 

В конструкторе приложения логики выполните следующие действия, чтобы войти в SharePoint для создания подключения **connection** , которое будет использоваться в приложении логики.

1. В поле поиска введите SharePoint и дождитесь возвращения всех записей с текстом SharePoint в имени.   
   ![Настройка SharePoint][1]  
2. Выберите **SharePoint Online — при создании файла**   
3. Выберите **Sign in to SharePoint Online** (Вход в SharePoint Online).   
   ![Настройка SharePoint][2]    
4. Укажите учетные данные SharePoint, чтобы войти и пройти аутентификацию в SharePoint.   
   ![Настройка SharePoint][3]     
5. После успешной аутентификации вы перейдете к своему приложению логики, чтобы завершить его, настроив параметры в диалоговом окне **При создании файла** SharePoint.          
   ![Настройка SharePoint][4]  
6. Затем можно будет добавить другие триггеры и действия, необходимые для завершения приложения логики.   
7. Сохраните результаты работы, выбрав **Сохранить** в строке меню вверху.  

## <a name="sharepoint-rest-api-reference"></a>Справочник по API REST SharePoint
#### <a name="this-documentation-is-for-version-10"></a>Эта документация предназначена для версии 1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Используется для получения метаданных файла в библиотеке документов
**```GET: /datasets/{dataset}/files/{id}```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| id |string |Да |path |Нет |Уникальный идентификатор файла |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>Используется для обновления файла в библиотеке документов
**```PUT: /datasets/{dataset}/files/{id}```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| id |string |Да |path |Нет |Уникальный идентификатор файла |
| текст | |Да |текст |Нет |Содержимое файла |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>Используется для удаления файла в библиотеке документов
**```DELETE: /datasets/{dataset}/files/{id}```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| id |string |Да |path |Нет |Уникальный идентификатор файла |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>Используется для получения метаданных файла в библиотеке документов
**```GET: /datasets/{dataset}/GetFileByPath```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| path |string |Да |запрос |Нет |Путь к файлу |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Используется для получения файла в библиотеке документов
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| path |string |Да |запрос |Нет |Путь к файлу |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>Используется для получения файла в библиотеке документов
**```GET: /datasets/{dataset}/files/{id}/content```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| id |string |Да |path |Нет |Уникальный идентификатор файла |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>Используется для отправки файла в библиотеке документов
**```POST: /datasets/{dataset}/files```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| folderPath |string |Да |запрос |Нет |Путь к папке |
| name |string |Да |запрос |Нет |Имя файла |
| текст | |Да |текст |Нет |Содержимое файла |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>Используется для копирования файла в библиотеке документов
**```POST: /datasets/{dataset}/copyFile```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| источник |string |Да |запрос |Нет |Путь к исходному файлу |
| ресурс destination |string |Да |запрос |Нет |Путь к конечному файлу |
| overwrite |Логическое |Нет |запрос |нет |Следует ли перезаписывать существующий файл |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>Запускает поток при создании файла в папке SharePoint
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint |
| folderId |string |Да |запрос |Нет |Уникальный идентификатор папки в SharePoint |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>Запускает поток при изменении файла в папке SharePoint
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint |
| folderId |string |Да |запрос |Нет |Уникальный идентификатор папки в SharePoint |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>Используется для извлечения папки в библиотеке документов
**```POST: /datasets/{dataset}/extractFolderV2```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint. Например:  http://contoso.sharepoint.com/sites/mysite |
| источник |string |Да |запрос |Нет |Путь к исходному файлу |
| ресурс destination |string |Да |запрос |Нет |Путь к конечной папке |
| overwrite |Логическое |Нет |запрос |нет |Следует ли перезаписывать существующий файл |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>При создании элемента в списке SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint (например, http://contoso.sharepoint.com/sites/mysite). |
| таблица |string |Да |path |Нет |Имя списка SharePoint |
| $skip |целое число |Нет |запрос |Нет |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |целое число |Нет |запрос |Нет |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |
| $filter |string |Нет |запрос |Нет |Запрос фильтра ODATA для ограничения количества записей |
| $orderby |string |Нет |запрос |Нет |Запрос orderBy ODATA для указания порядка записей |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>При изменении существующего элемента в списке SharePoint
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint (например, http://contoso.sharepoint.com/sites/mysite). |
| таблица |string |Да |path |Нет |Имя списка SharePoint |
| $skip |целое число |Нет |запрос |Нет |Количество пропускаемых записей (значение по умолчанию — 0) |
| $top |целое число |Нет |запрос |Нет |Максимальное количество извлекаемых записей (значение по умолчанию — 256) |
| $filter |string |Нет |запрос |Нет |Запрос фильтра ODATA для ограничения количества записей |
| $orderby |string |Нет |запрос |Нет |Запрос orderBy ODATA для указания порядка записей |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>Создает элемент в списке SharePoint
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint (например, http://contoso.sharepoint.com/sites/mysite). |
| таблица |string |Да |path |Нет |Имя списка SharePoint |
| item | |Да |текст |Нет |Создаваемый элемент |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>Извлекает один элемент из списка SharePoint
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint (например, http://contoso.sharepoint.com/sites/mysite). |
| таблица |string |Да |path |Нет |Имя списка SharePoint |
| id |целое число |Да |path |Нет |Уникальный идентификатор извлекаемого элемента |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>Удаляет элемент из списка SharePoint
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint (например, http://contoso.sharepoint.com/sites/mysite). |
| таблица |string |Да |path |Нет |Имя списка SharePoint |
| id |целое число |Да |path |Нет |Уникальный идентификатор удаляемого элемента |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>Обновляет элемент в списке SharePoint
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Да |path |Нет |URL-адрес сайта SharePoint (например, http://contoso.sharepoint.com/sites/mysite). |
| таблица |string |Да |path |Нет |Имя списка SharePoint |
| id |целое число |Да |path |Нет |Уникальный идентификатор обновляемого элемента |
| item | |Да |текст |Нет |Элемент с измененными свойствами |

### <a name="here-are-the-possible-responses"></a>Ниже перечислены возможные ответы:
| Name (Имя) | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

- - -
## <a name="object-definitions"></a>Определения объектов:
 **DataSetsMetadata**:

Обязательные свойства для DataSetsMetadata:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| tabular |не определено |
| blob-объект |не определено |

 **TabularDataSetsMetadata**:

Обязательные свойства для TabularDataSetsMetadata:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| источник |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**:

Обязательные свойства для BlobDataSetsMetadata:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| источник |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**:

Обязательные свойства для BlobMetadata:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
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

 **Object**:

Обязательные свойства для Object:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
|  | |

 **TableMetadata**:

Обязательные свойства для TableMetadata:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| name |string |
| title |string |
| x-ms-permission |string |
| schema |не определено |

 **DataSetsList**:

Обязательные свойства для DataSetsList:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| value |array |

 **DataSet**:

Обязательные свойства для DataSet:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| Имя |string |
| DisplayName |string |

 **Таблица**.

Обязательные свойства для Table:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| Имя |string |
| DisplayName |string |

 **Item**:

Обязательные свойства для Item:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| ItemInternalId |string |

 **ItemsList**:

Обязательные свойства для ItemsList:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| value |array |

 **TablesList**:

Обязательные свойства для TablesList:

обязательные свойства отсутствуют. 

**Все свойства**: 

| Имя | Тип данных |
| --- | --- |
| value |array |

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png



<!--HONumber=Jan17_HO3-->


