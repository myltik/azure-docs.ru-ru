---
title: "Как использовать соединитель SFTP в приложениях логики | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Подключитесь к API SFTP, чтобы отправлять и получать файлы. Вы можете выполнять различные операции, например создавать, обновлять, получать или удалять файлы."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 2977404fb408ea5301c88caa7ce6767a906ca9c7


---
# <a name="get-started-with-the-sftp-connector"></a>Начало работы с соединителем SFTP
Используйте соединитель SFTP, чтобы получить доступ к учетной записи SFTP для отправки и получения файлов. Вы можете выполнять различные операции, например создавать, обновлять, получать или удалять файлы.  

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-sftp"></a>Подключение к SFTP
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой.  

### <a name="create-a-connection-to-sftp"></a>Создание подключения к SFTP
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>Использование триггера SFTP
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Из этого примера вы узнаете, как использовать триггер **SFTP - When a file is added or modified** (SFTP — при добавлении или изменении файла) для запуска рабочего процесса приложения логики при добавлении или изменении файла на SFTP-сервере. Из этого примера вы узнаете также, как добавить условие, которое проверяет содержимое нового или измененного файла, и принимает решение извлечь файл, если содержимое файла указывает, что он должен быть извлечен перед использованием содержимого. Наконец, вы узнаете, как добавить действие, которое извлекает содержимое файла и помещает извлеченное содержимое в папку на SFTP-сервере. 

В контексте предприятия можно использовать этот триггер, чтобы отслеживать в папке SFTP новые файлы, представляющие заказы от клиентов.  Также вы можете использовать действие соединителя SFTP **Get file content** (Получение содержимого файла), чтобы получить содержимое заказа для дальнейшей обработки и хранения в базе данных заказов.

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Добавить условие
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>Использование действия SFTP
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="technical-details"></a>Технические сведения
Ниже приведены сведения о триггерах, действиях и ответах, которые поддерживает это подключение.

## <a name="sftp-triggers"></a>Триггеры SFTP
В SFTP доступны следующие триггеры.  

| Триггер | Описание |
| --- | --- |
| [When a file is added or modified](connectors-create-api-sftp.md#when-a-file-is-added-or-modified) |Активирует поток при добавлении или изменении файла в папке. |

## <a name="sftp-actions"></a>Действия SFTP
Соединитель SFTP предоставляет следующие действия.

| Действие | Description (Описание) |
| --- | --- |
| [Получение метаданных файла](connectors-create-api-sftp.md#get-file-metadata) |Извлекает метаданные файла с помощью идентификатора файла. |
| [Обновление файла](connectors-create-api-sftp.md#update-file) |Обновляет содержимое файла. |
| [Удаление файла](connectors-create-api-sftp.md#delete-file) |Удаляет файл. |
| [Получение метаданных файла с помощью пути](connectors-create-api-sftp.md#get-file-metadata-using-path) |Извлекает метаданные файла с помощью пути к файлу. |
| [Получение содержимого файла с помощью пути](connectors-create-api-sftp.md#get-file-content-using-path) |Извлекает содержимое файла с помощью пути к файлу. |
| [Получение содержимого файла](connectors-create-api-sftp.md#get-file-content) |Извлекает содержимое файла с помощью идентификатора файла. |
| [Создание файла](connectors-create-api-sftp.md#create-file) |Загружает файл на SFTP-сервер. |
| [Копирование файла](connectors-create-api-sftp.md#copy-file) |Загружает файл на SFTP-сервер. |
| [Вывод списка файлов в папке](connectors-create-api-sftp.md#list-files-in-folder) |Возвращает файлы, содержащиеся в папке. |
| [Вывод списка файлов в корневой папке](connectors-create-api-sftp.md#list-files-in-root-folder) |Возвращает файлы в корневой папке. |
| [Extract folder](connectors-create-api-sftp.md#extract-folder) |Извлекает файл архива в папку (например, ZIP-файл). |

### <a name="action-details"></a>Сведения о действиях
Ниже приведены подробные сведения о действиях и триггерах этого соединителя, а также ответы на них.

### <a name="get-file-metadata"></a>Get file metadata (Получение метаданных файла)
Извлекает метаданные файла с помощью идентификатора файла. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Файл |Укажите файл |

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

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Файл |Укажите файл |
| body* |содержимое файла; |Содержимое обновляемого файла |

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

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Файл |Укажите файл |

Звездочка (*) означает, что свойство является обязательным.

### <a name="get-file-metadata-using-path"></a>Получение метаданных файла с помощью пути
Извлекает метаданные файла с помощью пути к файлу. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| path* |Путь к файлу |Уникальный путь к файлу |

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
| path* |Путь к файлу |Уникальный путь к файлу |

Звездочка (*) означает, что свойство является обязательным.

### <a name="get-file-content"></a>Получение содержимого файла
Извлекает содержимое файла с помощью идентификатора файла. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Файл |Укажите файл |

Звездочка (*) означает, что свойство является обязательным.

### <a name="create-file"></a>Создание файла
Загружает файл на SFTP-сервер. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| folderPath* |Путь к папке |Уникальный путь к папке |
| name* |Имя файла |Имя файла |
| body* |содержимое файла; |Содержимое создаваемого файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

|  | Имя свойства | Тип данных |
| --- | --- | --- |
| Идентификатор |string | |
| Name (Имя) |string | |
| DisplayName |string | |
| Путь |string | |
| LastModified |string | |
| Размер |целое число | |
| MediaType |string | |
| IsFolder |Логическое | |
| ETag |string | |
| FileLocator |string | |

### <a name="copy-file"></a>Копирование файла
Загружает файл на SFTP-сервер. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| source* |Путь к исходному файлу |Путь к исходному файлу |
| destination* |Путь к конечному файлу |Путь к конечному файлу, включая имя файла |
| overwrite |Перезаписать? |Перезаписывает конечный файл, если задано значение "true" |

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

### <a name="when-a-file-is-added-or-modified"></a>When a file is added or modified (При добавлении или изменении файла)
Активирует поток при добавлении или изменении файла в папке. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| folderId* |Папка |Укажите папку |

Звездочка (*) означает, что свойство является обязательным.

### <a name="list-files-in-folder"></a>List files in folder (Вывод списка файлов в папке)
Возвращает файлы, содержащиеся в папке. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Папка |Укажите папку |

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

### <a name="list-files-in-root-folder"></a>List files in root folder (Вывод списка файлов в корневой папке)
Возвращает файлы в корневой папке. 

Для этого вызова параметры отсутствуют

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
Извлекает файл архива в папку (например, ZIP-файл). 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| source* |Путь к исходному файлу архива |Путь к файлу архива |
| destination* |Путь к конечной папке |Путь к конечной папке |
| overwrite |Перезаписать? |Перезаписывает конечные файлы, если задано значение "true" |

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


