---
title: "Dropbox | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Подключитесь к Dropbox, чтобы управлять файлами. Вы можете выполнять различные действия, такие как отправка, обновление, получение и удаление файлов в Dropbox."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: app-service-logic
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0038c582d1a597dfcbb1fe8255b3a0fac1235e25


---
# <a name="get-started-with-the-dropbox-connector"></a>Начало работы с соединителем Dropbox
Подключитесь к Dropbox, чтобы управлять файлами. Вы можете выполнять различные действия, такие как отправка, обновление, получение и удаление файлов в Dropbox.

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-dropbox"></a>Подключение к Dropbox
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите соединение между приложением логики и другой службой. Например, чтобы подключиться к Dropbox, сначала необходимо создать *подключение* к Dropbox. Чтобы создать подключение, необходимо ввести учетные данные, которые обычно используются для доступа к определенной службе. То есть для создания подключения к Dropbox вам понадобятся учетные данные учетной записи Dropbox. Дополнительные сведения о подключениях см. [здесь]().

### <a name="create-a-connection-to-dropbox"></a>Создание подключения к Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Использование триггера Dropbox
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

В этом примере мы будем использовать триггер **When a file is created** (При создании файла). Когда этот триггер активируется, мы вызовем действие Dropbox **Get file content using path** (Получение содержимого файла с помощью пути). 

1. Введите запрос *dropbox* в поле поиска в конструкторе Logic Apps, а затем выберите триггер **Dropbox - When a file is created** (Dropbox — при создании файла).      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Выберите папку для отслеживания создания файла. Щелкните многоточие "…" (на снимке экрана выделено красной рамкой) и перейдите к папке, которую вы хотите выбрать в качестве входных данных триггера.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Использование действия Dropbox
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Теперь, когда триггер добавлен, выполните следующие действия, чтобы добавить действие, которое получит новое содержимое файла.

1. Нажмите кнопку **+ Новый шаг**, чтобы добавить действие, которое будет выполняться после создания нового файла.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Выберите **Добавить действие**. Откроется поле поиска. В этом поле вы можете выполнить поиск действия, которое нужно применить.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Введите запрос *dropbox*, чтобы найти действия, связанные с Dropbox.  
4. Выберите **Dropbox - Get file content using path** (Dropbox — получение содержимого файла с помощью пути) в качестве действия, которое будет выполняться после создания файла в выбранной папке Dropbox. Откроется блок управления действием. Вам будет предложено авторизовать приложение логики для доступа к учетной записи Dropbox, если вы еще не сделали это.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Щелкните многоточие "…" (находится в правой части элемента управления **Путь к файлу**) и перейдите к файлу, который вы хотите использовать. Или используйте маркер **file path**, чтобы ускорить создание приложения логики.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Сохраните изменения и создайте новый файл в Dropbox, чтобы активировать рабочий процесс.  

## <a name="technical-details"></a>Технические сведения
Ниже приведены сведения о триггерах, действиях и ответах, которые поддерживает это подключение.

## <a name="dropbox-triggers"></a>Триггеры Dropbox
Соединитель Dropbox предоставляет следующие триггеры.  

| Триггер | Description (Описание) |
| --- | --- |
| [При создании файла](connectors-create-api-dropbox.md#when-a-file-is-created) |Запускает поток при создании файла в папке. |
| [При изменении файла](connectors-create-api-dropbox.md#when-a-file-is-modified) |Запускает поток при изменении файла в папке. |

## <a name="dropbox-actions"></a>Действия Dropbox
Соединитель Dropbox предоставляет следующие действия.

| Действие | Description (Описание) |
| --- | --- |
| [Получение метаданных файла](connectors-create-api-dropbox.md#get-file-metadata) |Извлекает метаданные файла. |
| [Обновление файла](connectors-create-api-dropbox.md#update-file) |Обновляет файл. |
| [Удаление файла](connectors-create-api-dropbox.md#delete-file) |Удаляет файл. |
| [Получение метаданных файла с помощью пути](connectors-create-api-dropbox.md#get-file-metadata-using-path) |Извлекает метаданные файла с помощью пути. |
| [Получение содержимого файла с помощью пути](connectors-create-api-dropbox.md#get-file-content-using-path) |Извлекает содержимое файла с помощью пути. |
| [Получение содержимого файла](connectors-create-api-dropbox.md#get-file-content) |Извлекает содержимое файла. |
| [Создание файла](connectors-create-api-dropbox.md#create-file) |Создает файл. |
| [Копирование файла](connectors-create-api-dropbox.md#copy-file) |Копирует файл в Dropbox. |
| [Вывод списка файлов в папке](connectors-create-api-dropbox.md#list-files-in-folder) |Извлекает список файлов и вложенных папок в папке. |
| [Вывод списка файлов в корневой папке](connectors-create-api-dropbox.md#list-files-in-root-folder) |Извлекает список файлов и вложенных папок в корневой папке. |
| [Извлечение архива в папку](connectors-create-api-dropbox.md#extract-archive-to-folder) |Извлекает файл архива в папку (например, ZIP-файл). |

### <a name="action-details"></a>Сведения о действиях
Ниже приведены подробные сведения о действиях и триггерах этого соединителя, а также ответы на них.

### <a name="get-file-metadata"></a>Get file metadata (Получение метаданных файла)
Извлекает метаданные файла. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Файл |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="update-file"></a>Обновление файла
Обновляет файл. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Файл |Выбор файла |
| body* |содержимое файла; |Содержимое файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
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
| id* |Файл |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

### <a name="get-file-metadata-using-path"></a>Получение метаданных файла с помощью пути
Извлекает метаданные файла с помощью пути. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| path* |Путь к файлу |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |строка |

### <a name="get-file-content-using-path"></a>Получение содержимого файла с помощью пути
Извлекает содержимое файла с помощью пути. 

| Имя свойства | Отображаемое имя | Описание |
| --- | --- | --- |
| path* |Путь к файлу |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

### <a name="get-file-content"></a>Получение содержимого файла
Извлекает содержимое файла. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Файл |Выбор файла |

Звездочка (*) означает, что свойство является обязательным.

### <a name="create-file"></a>Создание файла
Создает файл. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| folderPath* |Путь к папке |Выбор папки |
| name* |Имя файла |Имя файла |
| body* |содержимое файла; |Содержимое файла |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="copy-file"></a>Копирование файла
Копирует файл в Dropbox. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| source* |URL-адрес исходного файла |URL-адрес исходного файла |
| destination* |Путь к конечному файлу |Путь к конечному файлу, включая имя конечного файла |
| overwrite |Перезаписать? |Перезаписывает конечный файл, если задано значение "true" |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |string |

### <a name="when-a-file-is-created"></a>При создании файла
Запускает поток при создании файла в папке. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| folderId* |Папка |Выбор папки |

Звездочка (*) означает, что свойство является обязательным.

### <a name="when-a-file-is-modified"></a>При изменении файла
Запускает поток при изменении файла в папке. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| folderId* |Папка |Выбор папки |

Звездочка (*) означает, что свойство является обязательным.

### <a name="list-files-in-folder"></a>List files in folder (Вывод списка файлов в папке)
Извлекает список файлов и вложенных папок в папке. 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| id* |Папка |Выбор папки |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |строка |

### <a name="list-files-in-root-folder"></a>List files in root folder (Вывод списка файлов в корневой папке)
Извлекает список файлов и вложенных папок в корневой папке. 

Для этого вызова параметры отсутствуют

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
| LastModified |string |
| Размер |целое число |
| MediaType |string |
| IsFolder |Логическое |
| ETag |string |
| FileLocator |строка |

### <a name="extract-archive-to-folder"></a>Извлечение архива в папку
Извлекает файл архива в папку (например, ZIP-файл). 

| Имя свойства | Отображаемое имя | Description (Описание) |
| --- | --- | --- |
| source* |Путь к исходному файлу архива |Путь к файлу архива |
| destination* |Путь к конечной папке |Путь для извлечения содержимого архива |
| overwrite |Перезаписать? |Перезаписывает конечные файлы, если задано значение "true" |

Звездочка (*) означает, что свойство является обязательным.

#### <a name="output-details"></a>Сведения о выходных данных
BlobMetadata

| Имя свойства | Тип данных |
| --- | --- |
| id |string |
| Имя |string |
| displayName |string |
| path |string |
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
[Создайте приложение логики](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


