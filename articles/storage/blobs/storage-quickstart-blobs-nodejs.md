---
title: Краткое руководство Azure. Отправка, скачивание и составление списка больших двоичных объектов в службе хранилища Azure с помощью Node.js | Документация Майкрософт
description: В рамках этого краткого руководства вы создадите учетную запись хранения и контейнер. Затем используете клиентскую библиотеку службы хранилища для Node.js, чтобы отправить большой двоичный объект в службу хранилища Azure, скачать его и составить список больших двоичных объектов в контейнере.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 8783b83a1a94caf4a49f9da7a2dd30c9cb52df22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Node.js

Из этого краткого руководства вы узнаете, как использовать Node.js для передачи, скачивания и перечисления блочных BLOB-объектов в контейнере с помощью хранилища BLOB-объектов Azure.

Для работы с этим кратким руководством вам потребуется [подписка Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Загрузка примера приложения

[Пример приложения](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git), используемый в этом кратком руководстве, является простым консольным приложением Node.js. Чтобы начать работу, клонируйте репозиторий на компьютер, используя следующую команду.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Чтобы открыть приложение, найдите папку *storage-blobs-node-quickstart* и откройте ее в избранной среде редактирования кода.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища

Перед запуском приложения укажите строку подключения для учетной записи хранения. Образец репозитория включает в себя файл с именем *.env.example*. Вы можете переименовать этот файл, удалив расширение *.example*, после чего в названии файла будет расширение *.env*. Внутри файла формата *ENV* добавьте значение строки подключения после ключа *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Установка необходимых пакетов

В каталоге приложения выполните команду *npm install*, чтобы установить необходимые пакеты для приложения.

```bash
npm install
```

## <a name="run-the-sample"></a>Запуск примера
После установки требуемых компонентов вы можете запустить пример, передав команды в скрипт. Например, чтобы создать контейнер больших двоичных объектов, выполните следующую команду.

```bash
node index.js --command createContainer
```

Доступны следующие команды.

| Get-Help | ОПИСАНИЕ |
|---------|---------|
|*createContainer* | Создает контейнер с именем *test-container* (завершается успешно, даже если этот контейнер уже имеется) |
|*upload*          | Передает файл *example.txt* в контейнер *test-container* |
|*download*        | Загружает содержимое *образца* большого двоичного объекта в файл *example.downloaded.txt* |
|*delete*          | Удаляет *образец* большого двоичного объекта |
|*list*            | Выводит содержимое контейнера *test-container* в консоль |


## <a name="understanding-the-sample-code"></a>Общие сведения о примере кода
Этот пример кода использует несколько модулей для взаимодействия с файловой системой и командной строкой. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

Ниже представлены сведения о предназначении каждого модуля. 

- *dotenv* загружает переменные среды, заданные в файле формата *ENV*, в текущий контекст выполнения.
- *path* требуется, чтобы определить абсолютный путь к файлу для передачи в хранилище BLOB-объектов.
- *yargs* предоставляет простой интерфейс для доступа к аргументам командной строки.
- *azure-storage* — модуль [SDK службы хранилища Azure](/nodejs/api/azure-storage) для Node.js.

Затем инициализируется ряд переменных.

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

Для переменных задаются следующие значения.

- *blobService* — присваивается новому экземпляру службы BLOB-объектов Azure.
- *containerName* — присваивается имени контейнера.
- *sourceFilePath* — присваивается абсолютному пути передаваемого файла.
- *blobName* — создается путем использования имени файла без его расширения.

В следующей реализации каждая из функций *blobService* упакована в *Promise*, что позволяет получить доступ к функции JavaScript *async* и оператору *await*, чтобы упростить характер обратного вызова [API службы хранилища Azure](/nodejs/api/azure-storage/blobservice). Если для каждой функции возвращается ответ об успешном выполнении, объект promise разрешает соответствующие данные вместе с сообщением, связанным с действием.

### <a name="create-a-blob-container"></a>Создание контейнера BLOB-объектов

Функция *CreateContainer* вызывает команду [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) и задает соответствующий уровень доступа для большого двоичного объекта.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Второй параметр (*options*) для **createContainerIfNotExists** принимает значение параметра [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). Значение *blob* для *publicAccessLevel* указывает, что данные конкретного большого двоичного объекта доступны из общей сети. Этот параметр отличается от уровня доступа *контейнера*, который предоставляет возможность составить список содержимого контейнера.

Применение команды **createContainerIfNotExists** позволяет приложению выполнить команду *createContainer* несколько раз, не возвращая ошибки, если контейнер уже существует. В рабочей среде команда **createContainerIfNotExists** часто вызывается только один раз, так как в приложении используется один и тот же контейнер. В таких случаях контейнер можно создать заранее на портале или с помощью Azure CLI.

### <a name="upload-a-blob-to-the-container"></a>Передача большого двоичного объекта в контейнер

Функция *upload* использует функцию [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) для передачи, записи или перезаписи файла из файловой системы в хранилище BLOB-объектов. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
В контексте примера приложения файл с именем *example.txt* передается в большой двоичный объект *example* внутри контейнера *test-container*. Другие методы, доступные для передачи содержимого в большие двоичные объекты, включают в себя работу с [текстом](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) и [потоками данных](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

Чтобы проверить, передан ли файл в хранилище BLOB-объектов, можно использовать [Обозреватель службы хранилища Azure](https://azure.microsoft.com/en-us/features/storage-explorer/) для просмотра данных в учетной записи.

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Функция *list* вызывает метод [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText), чтобы получить список метаданных большого двоичного объекта в контейнере. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Вызов *listBlobsSegmented* возвращает метаданные большого двоичного объекта в качестве массива экземпляров [BlobResult](/nodejs/api/azure-storage/blobresult). Результаты возвращаются в 5000 пакетов увеличения (сегментов). Если в контейнере имеется более чем 5000 больших двоичных объектов, то результаты включают в себя значение для маркера **continuationToken**. Чтобы вывести список последующих сегментов из контейнера больших двоичных объектов, можно передать маркер продолжения обратно в **listBlobSegmented** в качестве второго аргумента.

### <a name="download-a-blob-from-the-container"></a>Скачивание большого двоичного объекта из контейнера

Функция *download* использует команду [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile), чтобы скачать содержимое большого двоичного объекта для заданного абсолютного пути к файлу.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
Показанная здесь реализация изменяет путь к добавляемому исходному файлу, чтобы добавить к его имени часть *.downloaded.txt*. В реальном контексте при выборе пути назначения для скачивания можно изменить расположение, а также имя файла.

### <a name="delete-blobs-in-the-container"></a>Удаление больших двоичных объектов в контейнере

Функция *deleteBlock* (с псевдонимом консольной команды *delete*) вызывает функцию [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Как и предполагает ее имя, эта функция не возвращает ошибку, если большой двоичный объект уже удален.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Передача и отображение списка

Одним из преимуществ использования promise является возможность объединения команд. Функция **uploadAndList** показывает, как можно легко вывести список содержимого большого двоичного объекта непосредственно после передачи файла.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Код вызова

Для добавления реализованных функций в командную строку каждая из функций сопоставляется с объектным литералом.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

После добавления *_module* в командной строке доступна каждая из команд.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Если указанная команда отсутствует, то свойства *_module* отображаются в консоли как текст справки для пользователя. 

Функция *executeCommand* — это функция *async*, которая вызывает заданную команду с помощью оператора *await* и регистрирует все сообщения для данных в консоли сообщения.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Наконец, выполняющийся код сначала вызывает *commandExists*, чтобы проверить, передана ли указанная команда в скрипт. Если выбрана имеющаяся команда, она выполняется, после чего все ошибки записываются в консоль.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы не планируете использовать данные или учетные записи, созданные в рамках этой статьи, вы можете удалить их, чтобы не получать нежелательные платежи. Чтобы удалить большой двоичный объект и контейнеры, можно использовать методы [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) и [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_). Вы также можете удалить учетную запись хранения [через портал](../common/storage-create-storage-account.md).

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Ресурсы для разработки приложений Node.js с большими двоичными объектами

Ознакомьтесь со следующими дополнительными ресурсами для разработки Node.js с использованием хранилища BLOB-объектов.

### <a name="binaries-and-source-code"></a>Двоичные файлы и исходный код

- Просмотрите и установите [исходный код клиентской библиотеки Node.js](https://github.com/Azure/azure-storage-node) для службы хранилища Azure на сайте GitHub.

### <a name="client-library-reference-and-samples"></a>Справочник по клиентской библиотеке и примеры

- Подробные сведения о клиентской библиотеке Node.js см. в [справочнике по API-интерфейсу Node.js](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage).
- Изучите [примеры для хранилища BLOB-объектов](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob), написанные с использованием клиентской библиотеки Node.js.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве показано, как передавать файл между локальным диском и хранилищем BLOB-объектов Azure с помощью Node.js. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем практическом руководстве.

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](storage-nodejs-how-to-use-blob-storage.md)

Справочник по Node.js для хранилища Azure см. в пакете [azure-storage](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).