---
title: "OneDrive для бизнеса | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Подключитесь к OneDrive для бизнеса, чтобы управлять файлами. Вы можете выполнять различные действия, такие как отправка, обновление, получение и удаление файлов."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cf9484e9-7a20-4de0-93c8-0fa132221f2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 992b4b7bab8878355cbe936b75768e67b086286e


---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>Начало работы с соединителем OneDrive для бизнеса
Подключитесь к OneDrive для бизнеса, чтобы управлять файлами. Вы можете выполнять различные действия, такие как отправка, обновление, получение и удаление файлов.

> [!NOTE]
> Эта версия статьи предназначена для приложений логики со схемой версии 2015-08-01-preview.
> 
> 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Триггеры и действия
Соединитель OneDrive для бизнеса можно использовать как действие. Кроме того, он имеет триггеры. Все соединители поддерживают данные в форматах JSON и XML.

 Соединитель OneDrive для бизнеса предоставляет следующие триггеры и действия:

### <a name="onedrive-for-business-actions"></a>Действия OneDrive для бизнеса
Вы можете выполнять перечисленные ниже действия:

| Действие | Описание |
| --- | --- |
| [GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata) |Получает метаданные файла в OneDrive для бизнеса с помощью идентификатора |
| [UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile) |Обновляет файл в OneDrive для бизнеса |
| [DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile) |Удаляет файл в OneDrive для бизнеса |
| [GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath) |Получает метаданные файла в OneDrive для бизнеса с помощью пути |
| [GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath) |Получает содержимое файла в OneDrive для бизнеса с помощью пути |
| [GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent) |Получает содержимое файла в OneDrive для бизнеса с помощью идентификатора |
| [CreateFile](connectors-create-api-onedriveforbusiness.md#createfile) |Передает файл в OneDrive для бизнеса |
| [CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile) |Копирует файл в OneDrive для бизнеса |
| [ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder) |Перечисляет файлы в папке OneDrive для бизнеса |
| [ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder) |Перечисляет файлы в корневой папке OneDrive для бизнеса |
| [ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2) |Извлекает папку в OneDrive для бизнеса |

### <a name="onedrive-for-business-triggers"></a>Триггеры OneDrive для бизнеса
Можно прослушивать указанные ниже события:

| Триггер | Описание |
| --- | --- |
| При создании файла |Запускает поток при создании файла в папке OneDrive для бизнеса |
| При изменении файла |Запускает поток при изменении файла в папке OneDrive для бизнеса |

## <a name="create-a-connection-to-onedrive-for-business"></a>Создание подключения к OneDrive для бизнеса
Для создания приложений логики с помощью OneDrive для бизнеса необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств.

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Укажите учетные данные OneDrive для бизнеса |

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 
> [!TIP]
> Это подключение можно использовать в других приложениях логики.
> 
> 

## <a name="reference-for-onedrive-for-business"></a>Справочник по OneDrive для бизнеса
Относится к версии 1.0.

## <a name="getfilemetadata"></a>GetFileMetadata
Получение метаданных файла с помощью идентификатора: получает метаданные файла в OneDrive для бизнеса с помощью идентификатора

```GET: /datasets/default/files/{id}```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| id |string |Да |path |Нет |Укажите файл |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="updatefile"></a>UpdateFile
Изменение файла: изменяет файл в OneDrive для бизнеса

```PUT: /datasets/default/files/{id}```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| id |string |Да |path |Нет |Укажите изменяемый файл |
| текст | |Да |текст |Нет |Содержимое файла, изменяемого в OneDrive для бизнеса |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="deletefile"></a>DeleteFile
Удаление файла: удаляет файл из OneDrive для бизнеса

```DELETE: /datasets/default/files/{id}```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| id |string |Да |path |Нет |Укажите файл для удаления |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
Получение метаданных файла с помощью пути: получает метаданные файла в OneDrive для бизнеса с помощью пути

```GET: /datasets/default/GetFileByPath```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| path |string |Да |запрос |Нет |Уникальный путь к файлу в OneDrive для бизнеса |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="getfilecontentbypath"></a>GetFileContentByPath
Получение содержимого файла с помощью пути: получает содержимое файла в OneDrive для бизнеса с помощью пути

```GET: /datasets/default/GetFileContentByPath```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| path |string |Да |запрос |Нет |Уникальный путь к файлу в OneDrive для бизнеса |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="getfilecontent"></a>GetFileContent
Получение содержимого файла с помощью идентификатора: получает содержимое файла в OneDrive для бизнеса с помощью идентификатора

```GET: /datasets/default/files/{id}/content```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| id |string |Да |path |Нет |Укажите файл |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="createfile"></a>CreateFile
Создание файла: передает файл в OneDrive для бизнеса

```POST: /datasets/default/files```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |Да |запрос |Нет |Путь к папке для отправки файла в OneDrive для бизнеса |
| name |string |Да |запрос |Нет |Имя файла, создаваемого в OneDrive для бизнеса |
| текст | |Да |текст |Нет |Содержимое файла, отправляемого в OneDrive для бизнеса |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="copyfile"></a>CopyFile
Копирование файла: копирует файл в OneDrive для бизнеса

```POST: /datasets/default/copyFile```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| источник |string |Да |запрос |Нет |URL-адрес исходного файла |
| ресурс destination |string |Да |запрос |Нет |Путь к конечному файлу в OneDrive для бизнеса, включая имя конечного файла |
| overwrite |Логическое |Нет |запрос |нет |Перезаписывает конечный файл, если задано значение "true" |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="onnewfile"></a>OnNewFile
При создании файла: запускает поток при создании файла в папке OneDrive для бизнеса

```GET: /datasets/default/triggers/onnewfile```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Да |запрос |Нет |Укажите папку |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="onupdatedfile"></a>OnUpdatedFile
При изменении файла: запускает поток при изменении файла в папке OneDrive для бизнеса

```GET: /datasets/default/triggers/onupdatedfile```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Да |запрос |Нет |Укажите папку |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="listfolder"></a>ListFolder
Список файлов в папке: перечисляет файлы в папке OneDrive для бизнеса

```GET: /datasets/default/folders/{id}```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Описание |
| --- | --- | --- | --- | --- | --- |
| id |string |Да |path |Нет |Укажите папку |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="listrootfolder"></a>ListRootFolder
Список файлов в корневой папке: перечисляет файлы в корневой папке OneDrive для бизнеса

```GET: /datasets/default/folders```

Для этого вызова параметры отсутствуют

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="extractfolderv2"></a>ExtractFolderV2
Извлечение папки: извлекает папку в OneDrive для бизнеса

```POST: /datasets/default/extractFolderV2```

| Имя | Тип данных | Обязательно | Местонахождение | Значение по умолчанию | Description (Описание) |
| --- | --- | --- | --- | --- | --- |
| источник |string |Да |запрос |Нет |Путь к файлу архива |
| ресурс destination |string |Да |запрос |Нет |Путь в OneDrive для бизнеса для извлечения содержимого архива |
| overwrite |Логическое |Нет |запрос |нет |Перезаписывает конечные файлы, если задано значение "true" |

#### <a name="response"></a>Ответ
| Имя | Описание |
| --- | --- |
| 200 |ОК |
| по умолчанию |Операция завершилась ошибкой. |

## <a name="object-definitions"></a>Определения объектов
### <a name="datasetsmetadata"></a>DataSetsMetadata
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| tabular |не определено |Нет |
| blob-объект |не определено |Нет |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| источник |string |Нет |
| displayName |string |Нет |
| urlEncoding |string |Нет |
| tableDisplayName |string |Нет |
| tablePluralName |string |Нет |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| источник |string |Нет |
| displayName |string |Нет |
| urlEncoding |string |Нет |

### <a name="blobmetadata"></a>BlobMetadata
| Имя свойства | Тип данных | Обязательно |
| --- | --- | --- |
| Идентификатор |string |Нет |
| Имя |string |Нет |
| DisplayName |string |Нет |
| Путь |string |Нет |
| LastModified |string |Нет |
| Размер |целое число |Нет |
| MediaType |string |Нет |
| IsFolder |Логическое |Нет |
| ETag |string |Нет |
| FileLocator |string |Нет |

### <a name="object"></a>Объект
## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


